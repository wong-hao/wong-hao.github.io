---
title: "ReLoRaWAN: Reliable data delivery in LoRaWAN networks with multiple gateways"
authors:
- Wenjia Wu
- admin
- Zisheng Cheng
# author_notes:
# - "Equal contribution"
# - "Equal contribution"
date: "2023-05-15T00:00:00Z"
doi: "https://doi.org/10.1016/j.adhoc.2023.103203"

# Schedule page publish date (NOT publication's date).
# publishDate: "2023-08-01T00:00:00Z"

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ["2"]

# Publication name and optional abbreviated publication name.
publication: "Ad Hoc Networks"
publication_short: ""

abstract: LoRa has emerged as a promising technology to provide low-power and long-range communication for IoT devices. LoRaWAN networks build on top of the LoRa physical layer and adopt a centralized network architecture that supports one or more gateways connected with a large number of end devices. Although LoRa communication is resistant to channel noises, it still frequently suffers data delivery failures in LoRaWAN networks due to packet collisions of the concurrent transmissions. The delivery failures trigger the retransmission procedure in LoRaWAN protocol, which causes additional power consumption on end devices and further exacerbates packet collisions. To solve this issue, we investigate the reliable data delivery mechanism that utilizes with the packet reception of multiple gateways to recover the distorted payload. Firstly, we present a ReLoRaWAN framework, where the central server aggregates distorted packet payload from multi-gateway reception of the same packet and executes the corresponding data recovery operations. Then, we design a Tri-operation Integrated Data Recovery (TIDR) algorithm for recovering the distorted packet payload, which involves exclusive-OR based bitwise inversion operation, majority voting based bitwise inversion operation, and weighted bitwise decision operation. Finally, we implement a ReLoRaWAN testbed and conduct real-world experiments to evaluate the performance of our solution. Compared with existing works, the ReLoRaWAN greatly optimizes the quality of service and power consumption in the network. It improves the packet delivery ratio by 35% and reduces the average power consumption of the end device by 30%.

# Summary. An optional shortened abstract.
summary: Lorem ipsum dolor sit amet, consectetur adipiscing elit. Duis posuere tellus ac convallis placerat. Proin tincidunt magna sed ex sollicitudin condimentum.

tags:
- Source Themes
featured: false

# links:
# - name: ""
#   url: ""
url_pdf: '/publication/journal-article/ReLoRaWAN.pdf'
url_code: 'https://github.com/wowchemy/wowchemy-hugo-themes'
url_dataset: ''
url_poster: ''
url_project: ''
url_slides: ''
url_source: ''
url_video: ''

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/jdD8gXaTZsc)'
  focal_point: ""
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects: []

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: example
---

{{% callout note %}}
Click the *Cite* button above to demo the feature to enable visitors to import publication metadata into their reference management software.
{{% /callout %}}

{{% callout note %}}
Create your slides in Markdown - click the *Slides* button to check out the example.
{{% /callout %}}

Supplementary notes can be added here, including [code, math, and images](https://wowchemy.com/docs/writing-markdown-latex/).
