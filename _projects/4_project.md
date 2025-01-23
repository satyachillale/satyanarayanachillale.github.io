---
layout: page
title: SENTIMENT & CAPITAL, THE DATA BEHIND FINANCIALMOVEMENTS
description: Analyzes financial data and sentiment, revealing interconnections across stocks, Bitcoin, loans.
img: assets/img/stock-market-image.jpeg
importance: 2
category: work
giscus_comments: false
---

[[Project Paper]](https://satyachillale.github.io/assets/pdf/BDAD_Project_report.pdf) [[Presentation]](https://satyachillale.github.io/assets/pdf/BDAD_presentation.pdf) [[Code]](https://github.com/satyachillale/stockmarket-analysis)

### Introduction

Financial data analysis often goes beyond simple price tracking. In modern markets, the interplay between traditional financial metrics and social media sentiment can be pivotal. This project explores how stock prices, Bitcoin data, personal loan data, and Twitter sentiments relate to each other. By leveraging distributed tools like Apache Spark, Scala, and Zeppelin notebooks, it showcases how large-scale data analysis can uncover potential correlations and even attempt to model “shock propagation” across different market sectors. Computer science engineers will appreciate how this project flexibly combines big data frameworks, sentiment analysis, and graph-based models for real-world financial insights.

### Key Data Sources

- Stock Data – A dataset of ~700 MB containing daily records for numerous U.S. companies (1962–2017), enriched with sector and industry fields.

- Tweets – An 800 MB dataset of ~3.7 million tweets referencing publicly traded companies, including engagement metrics like retweets, likes, and comments.

- Bitcoin Data – A 350 MB dataset providing BTC-USD price at 1-minute intervals from 2012 to the present, later aggregated to daily values for consistency with the other datasets.

- Personal Loan Data – ~648 MB of Lending Club loan applications, split into accepted (~2.2 million rows) and rejected (~27.6 million rows) applications, offering a broad view of credit trends.

### Data Ingestion and Cleaning

#### Stocks
Extracted stock names from filenames and removed rows with missing data.
Filtered out anomalies by removing rows with extreme values (more than two standard deviations away from the mean) and eliminated stocks with sparse historical data.

#### Tweets
Removed hyperlinks and special characters while retaining the “$” symbol for symbol mentions.
Tokenized tweets and removed stopwords.
Ensured consistent time zones by converting post dates from Unix timestamps to EST.

#### Bitcoin
Data arrived at per-minute intervals, aggregated to daily open, close, high, low, and summed volume.
Verified continuity by removing a lone row with NaN values and ensured no duplicates based on timestamps.

#### Personal Loans
Cleaned high-level columns (e.g., removing “<” or “+” from employment-length fields).
Applied imputation using medians for numeric fields and modes for categorical fields.
Aligned acceptance and rejection rates with certain time windows to compare against financial market movements.

### Data Enhancement

#### Tweet Sentiment
Used a lexicon-based approach (AFINN) to score each tokenized tweet.
Summed word-level sentiment scores to classify each tweet as positive, negative, or neutral.
Broadcasted the lexicon to all Spark executors to optimize join operations at scale.

#### Stocks
Merged with Nasdaq screener data to map each stock to a sector and industry.
Computed daily percentage changes and “net sentiment” (positive minus negative counts on a given date).
Created features such as log returns to smooth daily price fluctuations.

#### Bitcoin
Converted timestamps to dates in EST.
Derived moving averages (5-day and 28-day SMA) and added forward-looking returns (1-, 3-, 7-, 14-day) to study if current sentiments could predict future price changes.

### Insights

#### Twitter and Stock Prices
Correlation analysis between sentiment score and stock daily change showed minimal direct impact on price movements. However, higher sentiment activity tended to correlate with increased trading volume. This suggests that while social media buzz may not reliably push prices up or down, it can attract more traders on a given day.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/2016-dip.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/2016-gain.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/img0.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    The top stock that dipped the most in each sector for the year 2016 and 2017.

</div>

#### Bitcoin and Social Media

Bitcoin price showed little correlation with tweet sentiment. For instance, correlations between average price and positive, neutral, or negative tweet counts (within the same day) hovered around moderate to low values. This may reflect the diversified international nature of Bitcoin investors, who do not necessarily respond to the same sentiment cues as equity traders.

#### Shock Propagation with Graphs

Built a multi-sector relationship model using GraphX in Spark. Each node was a sector, and edges stored regression coefficients indicating how a sector’s return might be influenced by the prior returns of another sector. When artificially amplifying the Finance sector’s returns by a factor of 10, strong ripple effects appeared in Real Estate (massive gains) and a shift from losses to gains in Technology. Other sectors, such as Healthcare, showed slight negative effects. This highlights how a shock in one sector can cascade and alter the returns in others.

#### Leading vs. Lagging Indicators

Comparisons between stock returns (leading) and personal loan rejections (lagging) suggested that deteriorations in the equity market often predicted future rises in loan rejection rates. This aligns with a common economic theory: as financial markets soften, credit availability narrows, causing higher loan rejection rates down the line.

#### Personal Loans and External Factors

A spike in Bitcoin returns overlapped with a sudden surge in loan rejections, possibly reflecting investors shifting their capital from supporting loans into cryptocurrency investments. This correlation hints at intriguing capital flow patterns, though more granular analysis (like investor-level data) would be needed to confirm causality.
Challenges
Spark’s default machine learning support doesn’t include advanced time-series algorithms like ARIMA or vector autoregression (VAR). Many correlation and causal inference methods (e.g., Granger causality) are not natively available.
Zeppelin notebooks can be cumbersome for generating multiple visualization types, necessitating creative Python or Scala workarounds.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/volume.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    Correlation of stock volume traded with twitter engagement. 
</div>

### Future Directions

- Integrate PySpark to leverage more robust time-series libraries and incorporate advanced analytics.

- Fill gaps in the stock data and improve continuity with additional external data sources.

- Conduct deeper analyses of rejected loans, correlating multiple factors (e.g., geographical clusters, DTI, sector performance) to isolate the exact drivers behind loan rejections.

Computer science engineers can learn from this project how to design data-pipelines that scale, manage multiple heterogeneous datasets, and derive meaningful features for advanced analytics. While well-known linear methods provided valuable correlations and insights, extending the solution to more specialized time-series and causal models could yield even richer findings for financial technology applications.

This project illustrates how a combination of structured (stocks, loans) and unstructured (tweets) data, combined with robust distributed computing tools, can produce actionable intelligence and drive deeper understanding of market movements