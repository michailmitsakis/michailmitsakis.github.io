---
layout: about
title: about
permalink: /
subtitle: Materials Scientist · electrochemistry, materials informatics, industrial decarbonization

profile:
  align: right
  image: prof_pic.jpg # replace assets/img/prof_pic.jpg with your own photo (same filename)
  image_circular: false # crops the image to make it circular
  more_info: >
    <p>MSc Engineering - Physics & Nanotechnology</p>
    <p>BSc Physics - Materials Science</p>

selected_papers: true # turn on once _bibliography/papers.bib has entries marked selected={true}
social: true # includes social icons at the bottom of the page

announcements:
  enabled: false # includes a list of news items
  scrollable: true # adds a vertical scroll bar if there are more than 3 news items
  limit: 5 # leave blank to include all the news in the `_news` folder

latest_posts:
  enabled: false
  scrollable: true # adds a vertical scroll bar if there are more than 3 new posts items
  limit: 3 # leave blank to include all the blog posts
---

Hi! I'm a mission-driven materials scientist, passionate about industrial decarbonization, sustainable energy solutions and accelerating R&D for low-carbon tech through electrochemistry and AI/ML for materials design.

I have a multidisciplinary background. I'm specialized in electrochemical synthesis for industrial decarbonization and AI-driven materials discovery, with hands-on experience in Bayesian optimization and building agentic tools for R&D workflows. Consulting on EU energy projects has shown me how scientific innovation translates into actionable insights and real-world impact. 

At DTU, I gained valuable experience in cleanroom and wetlab experimentation and manufacturing, as well as DFT modelling, chemical descriptors, materials screening and optimization. In particular, during my MSc thesis I optimized electrodeposition parameters for **hydrogen evolution reaction (HER) catalysts**. Specifically, I utilized multi-objective Bayesian Optimization (BO) to maximize the activity and stability of electrodeposited Ni-W (nickel-tungsten) alloys under various precursor concentrations, current densities, deposition times and pHs by minimizing their overpotential and overpotential difference (obtained before and after stress-testing the catalysts). After finishing my graduate studies, I continued that work out of deep interest in the field, and developed the same BO method through a more simplified, modular and easier-to-use platform for scientific experimentation, Ax.

Today, I work at a business and engineering consultancy on **deep-tech and EU-funded programmes**: CCS & hydrogen technologies, manufacturing data spaces, and shipyard digitalisation. That means proposal development, technoeconomic scoping, project management, and working directly with industrial and research partners.

Alongside that, in my free time I have been diving deeper into the latest research in **AI applications for materials discovery and optimization**, such as self-driving labs (SDLs), machine-learning interatomic potentials (MLIPs), advanced BO & LLM use, and more, while participating in hackathons and prototyping certain apps to further advance my understanding of the field. For example:

- **[catalyst-kg-agent](/projects/catalyst-kg-agent/)** — a knowledge-graph-grounded, cost-aware multi-agent system that decides when a cheap database lookup is enough and when an expensive simulation is justified, within a simulated SDL-like automated workflow; built to understand the complexities involved in automated discovery
- **[notion-second-brain](/projects/notion-second-brain/)** — a fully local RAG agent with hybrid retrieval, reranking, and an evaluation harness; for talking to my Notion notes offline
- **[baybe-corrosion-inhibitors](/projects/baybe-corrosion-inhibitors/)** — Bayesian optimisation with BayBE to screen small-molecule corrosion inhibitors for aluminium alloys, comparing molecular encodings against random search and testing transfer learning between alloys; a hackathon project exploring how far BO can cut the number of real experiments
- **[camel-rag](/projects/camel-rag/)** — a retrieval-augmented LLM that answers natural-language questions about catalyst adsorption energies, grounded in Open Catalyst DFT data and citing the records it draws on; a hackathon project on making large catalysis datasets queryable without losing traceability

One lesson I have found that holds along all these projects: it's important to do the upfront work on uncertainty awareness and reproducibility first, before optimizing and making the experiments more complicated. Electrochemical experiments especially are notoriously difficult to replicate due to the field's heavy reliance on customized apparatus, inconsistent reporting parameters, and high sensitivity to minor variations in experimental conditions. For example, during my thesis I dropped temperature from the search space, after finding large systematic errors between measurements that would only confuse the optimizer. I have tried to bring the same habit to my AI tools by building my projects around simplicity, validation and evaluation as the main throughline.

At the moment, I'm actively looking for **an industrial PhD or an applied research role in AI for materials science or manufacturing** in Europe.

You can find takeaways from my readings, research and [projects](/projects/) in [writing](/blog/).

Some more stuff I'm interested in: 
Calisthenics · Reading (fantasy, sci-fi, philosophy, science, history) · Writing · Philosophy of Mind · Ontologies of Quantum Mechanics · Music (rock, punk, psychedelic, funk, jazz, blues, indie)

I love hearing from people, so please reach out!
