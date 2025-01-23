---
layout: page
title: Image Geolocation
description: Predicting geographic coordinates from images using hierarchical clustering and retrieval augmented generation.
img: assets/img/geoclip_architecture.png
importance: 2
category: work
giscus_comments: false
---

[[Project Paper]](assets/pdf/CV_project_image_geolocation_final.pdf) [[Presentation]](assets/pdf/Image-Geolocation.pdf) [[Code]](https://github.com/satyachillale/g3-gg)

### Introduction


As computer vision systems scale to a global level, one particularly exciting challenge emerges: automatically determining precise geographic coordinates from a single image. Not only can such geolocation models be beneficial for navigation or environmental monitoring, but they can also tackle problems like crime tracking and disaster response. The main hurdle is reconciling the sheer diversity of landscapes worldwide with robust machine learning workflows that can handle large-scale datasets spanning millions of images.

### CLIP Meets Geolocation


A key step forward comes from CLIP-like architectures that align images and text in a shared embedding space. Recent research proposes expanding CLIP by adding a specialized GPS encoder to represent latitude-longitude pairs in high-dimensional embeddings. This approach, often referred to as GeoCLIP, allows continuous geographic coordinates (instead of discrete classes) to co-exist with images and textual information. By training the model to retrieve the matching GPS encoding for a given test image, GeoCLIP demonstrates accurate results for images taken almost anywhere on Earth.
Yet, straightforward retrieval from a large, flat collection of GPS points can quickly become infeasible. Searching every coordinate in a gallery of 100K or more points is computationally expensive. This has led researchers to explore hierarchical structures to narrow down candidate regions, simulating how a human might first guess a continent or country and then progressively “zoom in” on a more precise area.

### Hierarchical Feature Clustering


To address the computational bottleneck, one promising solution is hierarchical feature clustering at multiple scales. Instead of lumping all GPS coordinates into a single pool, the approach organizes them into clusters at different geographic levels—continent, country, or city. Each cluster corresponds to a centroid representing coordinates that are geographically and visually similar. During inference, the query image embedding is compared only against top-level centroids. Once the model identifies the most similar cluster, it moves to the sub-clusters in that region to further refine the search.
By mimicking coarse-to-fine localization, this technique can cut down the number of comparisons by 100x while still preserving a high level of accuracy. It also allows flexibly adding more layers to the tree structure for very large datasets or for extremely fine-grained local predictions like neighborhoods or streets.

### Retrieval-Augmented Generation (RAG)


While pure retrieval-based methods are great for speed, they sometimes struggle with ambiguous images—think of uniform landscapes like deserts or unremarkable city blocks. Retrieval-Augmented Generation (RAG) tackles this challenge by leveraging large language models (LLMs). First, the system retrieves the top candidate locations from its hierarchical gallery. Then, an LLM such as GPT-4o or Mistral reasons about these candidates and suggests refined or alternate coordinates. This can be especially useful when there are text-based cues (like signs or landmarks) that can help the model disambiguate visually similar areas.
When tested on geolocation benchmarks like IM2GPS3K, combining hierarchical retrieval with LLM-based generation produced more robust predictions, particularly for regions with visually similar terrains. The system can also include specific metadata like neighborhood names or local landmarks to further guide the LLM and improve street-level accuracy.

### Results


Experiments show that:
Hierarchical clustering can reduce inference costs significantly while maintaining competitive accuracy compared to flat retrieval methods. 
For instance, clustering the gallery into around 800 groups is enough to capture broad geographical distinctions with minimal loss in precision.
RAG inference with a capable LLM often performs better than purely retrieval-based approaches, particularly for ambiguous or underrepresented cases, boosting accuracy at narrower distance thresholds.
Fine-grained text embeddings, such as neighborhood-focused prompts, enhance the model’s ability to differentiate locations that look visually similar from a distance but are in different parts of a city or region.
When tested on large datasets like MP16-Pro, the combination of a powerful image encoder (e.g., a ViT-based CLIP) and a well-tuned GPS encoder achieves strong performance across multiple geographic levels. The model also benefits substantially from an efficient data pipeline, such as precomputing embeddings and streaming data on the fly during training.

### Samples

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/geolocation_samples.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="caption">
    Two samples from the IM2GPS3K dataset. For each image, we provide the actual location, the final
predicted location, and the RAG-based inference predictions using different numbers of candidate references supplied to the large multimodal models (LMMs).

</div>

### Future Directions


Looking ahead, there are several avenues for continued improvement:
- Deeper Hierarchies: Instead of just two clustering levels, one could extend this to three or four levels to handle extremely large galleries and zoom down to very specific coordinates with minimal overhead.

- Fine-Grained Language Cues: Incorporating textual clues (think city landmarks or neighborhoods) into the embedded representation can help in densely populated and architecturally diverse areas.

- Scaling Up: For truly global applications, we might consider galleries of over a million coordinates. The hierarchical approach is well suited to this scenario, though additional techniques (such as beam search) might be required for further efficiency.

- Generation-Based Refinements: Using techniques from advanced generative models or diffusion models can augment training data for poorly represented locations, and large language models can act as “validators” to refine final predictions.

By uniting large-scale visual encoders with hierarchical clustering and retrieval-augmented generation, we can significantly improve image geolocation systems and bring them closer to solving real-world applications—from next-level gaming to humanitarian goals.