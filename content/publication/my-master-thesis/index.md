---
title: "Image Reconstruction from Hierarchical Representations of Human Visual Cortex Activities"

# Authors
# If you created a profile for a user (e.g. the default `admin` user), write the username (folder name) here 
# and it will be replaced with their full name and linked to their profile.
authors:
- admin


# Author notes (optional)
# author_notes:
# - "Equal contribution"


date: "2021-08-19T00:00:00Z"
# doi: ""

# Schedule page publish date (NOT publication's date).
publishDate: "2021-08-19T00:00:00Z"

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
# publication_types: ["7"]

# Publication name and optional abbreviated publication name.
publication: 
publication_short: 

# abstract: 

# Summary. An optional shortened abstract.
summary: My master research focuses on image reconstruction from fMRI signals. Perceptive contents being hierarchically encoded in human brain has been the key to many research breakthroughs in the field. This concept provides a new window into the internal mechanisms of brain system for scientists.

tags: []

# Display this page in the Featured widget?
featured: true

# Custom links (uncomment lines below)
# links:
# - name: Custom Link
#   url: http://example.org

url_pdf: ''
url_code: ''
url_dataset: ''
url_poster: ''
url_project: ''
url_slides: ''
url_source: ''
url_video: ''

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
image:
  caption: ''
  focal_point: ""
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects:
- example

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: thesis
---

<!-- {{% callout note %}}
Click the *Cite* button above to demo the feature to enable visitors to import publication metadata into their reference management software.
{{% /callout %}} -->

<!-- {{% callout note %}}
Create your slides in Markdown - click the *Slides* button to check out the example.
{{% /callout %}} -->
# Abstract

Reconstructing stimuli images from brain activity has been a rising topic in the field of computational neuroscience due to the advent of deep learning algorithms and the advancement in functional Magnetic Resonance Imaging (fMRI) techniques. The application of deep neural networks on larger fMRI-image-paired dataset has demonstrated significant improvement in brain decoding tasks recently. Moreover, perceptive contents being hierarchically encoded in human brain has been the key to many research breakthroughs in the field. This concept provides a new window into the internal mechanisms of brain system for scientists.In this work, we follow the current state-of-the-art method “divide-and-conquer” proposed by Fang (2020). We are able to reproduce reconstruction results within 10% of the state-of-the-art performance. </br> 

Based on the reproduced work, we further enhance our performance by introducing K-means image clustering method that captures more accurate semantic features for reconstructed images. Our experiments demonstrate the feasibility and flexibility of “divide-and-conquer” method in image reconstruction task. Most importantly, incorporating fMRI signals from different regions of visual cortex with different decoders improves the image reconstruction performance. Such implementation results map to the concept of hierarchical structure of visual cortex, giving us a medium to ex-plore more about how human brain handles visual stimuli.

## Results

![image info](results.png)

 Refer to my github repository for [code](https://github.com/andwct/Image-Reconstruction-from-Hierarchical-Representations-of-Human-Visual-Cortex). 
