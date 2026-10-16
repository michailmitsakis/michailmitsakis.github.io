---
layout: post
title: The ECSA Iceberg
date: 2026-10-15 10:00:00+0300
description: What I measured as ‘electrochemically active surface area’ in my MSc thesis, and why the area you divide by can change the story a catalyst tells.
tags: electrochemistry electrocatalysis hydrogen EIS bayesian-optimization
categories: technical
giscus_comments: false
related_posts: false
toc:
  sidebar: left
og_image: https://michailmitsakis.github.io/assets/img/posts/ecsa/eis-nyquist-primer.png
thumbnail: assets/img/posts/ecsa/eis-nyquist-primer.png
---

Electrochemistry is complicated. It gets more complicated the deeper you get into it. The more you learn, the more it feels like there is a massive iceberg of details hidden from view. As with all disciplines, you hopefully reach a point where you come closer to knowing how much you don't know. This post is about one tip of that iceberg: the **electrochemically active surface area (ECSA)**. In my MSc thesis at DTU I measured it, divided by it, and built my main conclusion on it. Three years on, I understand much better what that single number represents. Below: what I did, how a new study used the same idea, what a recent perspective paper says is hiding underneath, and what I'd do differently now.

> **TL;DR**
>
> - Every activity metric in electrocatalysis is a current divided by an area. Pick a different area and you can reach a different conclusion if you're not careful.
> - The textbook shortcut for the ECSA gives an *apparent* area, resting on several assumptions.
> - That doesn't make it useless, but one does need to measure it carefully, and read it as a diagnostic rather than a ranking between catalysts.
{: .block-tip }

## Why the denominator matters

Catalytic activity is usually reported as the **overpotential** needed to reach a target current density, or the current density at a fixed overpotential. The smaller the overpotential (in absolute terms) for a given current density, the better the catalyst is at its job. Either way, the core quantity is $$j = I/A$$, and everything downstream depends on what goes into this $$A$$. 

The naive choice is the **geometric area**: the area you can measure with a ruler. But real electrodes are rough and porous. Push a material toward the nanoscale and a growing fraction of its atoms sits at the surface, so the same apparent area can hide many times more surface, and many more active catalytic sites. Normalizing by geometric area blends two different things: *how good each site is* and *how many sites are packed inside it*. This can still be useful for measuring the performance of a device, since for example an electrolyzer stack cares about amps per cm² of electrode. However, it is the wrong metric if you want to know whether a new alloy is intrinsically better or just... fluffier. That's the job of the ECSA: the part of the catalyst surface that actually participates in the catalytic reaction. Normalize by it and you get closer to the **intrinsic activity** of the catalyst: essentially how good the average active site is. The ratio of the two areas is the normalized, dimensionless metric called the roughness factor, $$ R_f = \frac{ECSA}{A_{\text{geo}}} $$: representing how many cm² of active surface hide behind each cm² of footprint.

## What I did: ECSA from the double layer

**Context.** My MSc thesis (DTU Energy, 2023) [[1]](#ref-1) used Bayesian optimization, via the [Dragonfly](https://github.com/dragonfly/dragonfly) library, to search electrodeposition recipes for Ni–W hydrogen evolution catalysts on nickel foam, from a citrate–ammonia bath held at pH ≈ 9. The search covered the following parameters: deposition current density (5–125 mA cm⁻²), deposition time (60–600 s), tungstate (Na₂WO₄) concentration (0.05–0.2 M) and temperature (25–60 °C). 

**The double layer.** Put an electrode in an electrolyte and charge builds up on both sides of the interface: in the metal, and in a compact layer of adsorbed, oriented species plus a diffuse cloud of ions on the solution side. Electrically, this behaves like a capacitor. In a potential window where no charge-transfer (Faradaic) reactions occur, the only current flowing is due to this capacitor charging and discharging. The water dipole orientation and concentration of the ions at the double-layer varies with potential, causing this charging/discharging behavior. The interfacial potential drop sits across this nanometre-thin layer, and that’s what drives electron transfer. Specifically, in the case of a metal-solution interface, this double layer consists of the inner and outer Helmholtz planes [IHP and OHP respectively]. The inner Helmholtz plane runs through oriented water molecules and any ions adsorbed on the surface, while the outer Helmholtz plane runs through the nearest solvated ions, with the diffuse layer beyond. 

The overpotential is the extra potential beyond equilibrium needed to run a reaction at a given rate. It lowers the activation barrier, which is why current rises exponentially with it. *(according to the Butler–Volmer equation)*

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/posts/ecsa/thesis-cv-nonfaradaic.png" class="img-fluid rounded z-depth-1" zoomable=true alt="Cyclic voltammogram with the non-Faradaic region highlighted" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/posts/ecsa/thesis-double-layer.png" class="img-fluid rounded z-depth-1" zoomable=true alt="Schematic of the electrochemical double layer with inner and outer Helmholtz planes" %}
  </div>
</div>
<div class="caption">
  Fig 1. Left: a schematic CV (cyclic voltammogram) with the non-Faradaic window marked; the only current there is double-layer charging. Right: inner and outer Helmholtz planes and the diffuse layer. Redrawn after corresponding figures of my thesis <a href="#ref-1">[1]</a>, which were adapted from <a href="#ref-13">[13]</a> and <a href="#ref-14">[14]</a>.
</div>

**The measurement.** So how do you measure the ECSA? More real surface means more capacitance. So, divide the measured double-layer capacitance by the capacitance of an ideally flat surface of the same material, or the *specific capacitance* $$C_s$$, and you get: 

$$ \text{ECSA} = \frac{C_{\text{DL}}}{C_s} $$

The real $$C_s$$ is typically unknown, so common practice is to borrow a literature value [[6]](#ref-6). For flat metal electrodes, ~20 µF cm⁻² is a typical choice, and that's what I used.

**Measuring $$C_{\text{DL}}$$.** There are two standard routes. One is to run CVs at several scan rates inside the non-Faradaic window and take the slope of charging current against scan rate, $$i_c = C_{\text{DL}}\,\nu$$ [[7]](#ref-7). The other is electrochemical impedance spectroscopy (EIS). I went with EIS, mainly for speed, which matters when every sample sits inside an optimization loop.

<details markdown="1">
<summary><strong>EIS explained, briefly</strong> (click to expand)</summary>

A CV sweeps the potential and watches the current. EIS instead holds the electrode at a fixed DC operating point and adds a small wiggle on top: a sinusoidal voltage of typically 5–10 mV (potentiostatic) or a small AC current (galvanostatic). The response is recorded one frequency at a time, usually from about 100 kHz down into the mHz range. The ratio of the two sine waves (their amplitude ratio and phase shift) is the impedance, $$Z(\omega) = Z' + jZ''$$, where $$j$$ is the imaginary unit, not a current density.

The payoff is separation. Every process at the interface has its own time constant, $$\tau = RC$$. Fast processes, like ions moving through the electrolyte, respond at high frequencies; slower ones, like charge transfer, adsorption and diffusion, respond at low frequencies. One frequency sweep pulls apart what a CV smears together. The fine print: this only works if the system responds linearly (hence the small amplitude), stays stable for the whole sweep, and responds only to the perturbation. A Kramers–Kronig test checks this before you fit anything. For any linear, stable, causal system, the real and imaginary parts of the impedance are mathematically linked, so each can be calculated from the other; if the measured parts disagree, something went wrong during the measurement. In practice, software fits the spectrum with a long chain of RC elements, which obey these relations by construction, and checks that the leftover errors are small and random [[11]](#ref-11).

In my thesis I also ran potentiostatic EIS in the non-Faradaic window, but only as a qualitative check; every number in this post comes from the galvanostatic measurements (gEIS) shown below.

Another caveat: an equivalent circuit is a model, not a measurement. Different circuits can produce identical spectra, so every element in a fit needs a physical reason to be there. For a much fuller (and very readable) treatment, see the open-access tutorial by Lazanas and Prodromidis [[11]](#ref-11).

</details>

The usual way to look at an impedance spectrum is a Nyquist plot: $$-Z''$$ against $$Z'$$, one point per frequency, high frequencies on the left. The simplest model with charge transfer is the R–RC circuit (Fig. 2, left): a solution resistance $$R_s$$ in series with a charge-transfer resistance $$R_{ct}$$ that sits in parallel with the double layer:

$$
Z(\omega) = R_s + \frac{R_{ct}}{1 + j\omega R_{ct}\, C_{\text{DL}}}
$$

On a Nyquist plot, this traces a semicircle from $$R_s$$ to $$R_s + R_{ct}$$, with its apex at $$\omega = 1/(R_{ct}\,C_{\text{DL}})$$ (Fig. 2, right).

<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/posts/ecsa/eis-nyquist-primer.png" class="img-fluid rounded z-depth-1" zoomable=true alt="The R-RC equivalent circuit and its semicircular Nyquist plot, for an ideal capacitor and a constant phase element" %}
  </div>
</div>
<div class="caption">
  Fig 2. Left: the R–RC equivalent circuit. Right: its Nyquist plot, computed from the circuit equation (illustrative values; dots mark individual frequencies). Real electrodes behave more like a constant phase element (CPE, more on it below), which flattens the semicircle. After Lazanas & Prodromidis <a href="#ref-11">[11]</a>.
</div>

**My measurement.** The gEIS applied a small AC current perturbation on top of a DC current, i.e. with hydrogen evolution running. Its spectrum closely traces the R–RC semicircle (Fig. 3): charge transfer occurs here, and $$R_{ct}$$ reflects the HER kinetics at that current. The same fit gave me both numbers I needed: $$R_s$$ for the ohmic (solution) resistance correction *(the so-called iR correction)*, and $$C_{\text{DL}}$$ for the ECSA. That's one advantage of EIS: the semicircle separates the double layer from charge transfer even with the reaction running, so no non-Faradaic window is needed.

<div class="row justify-content-sm-center mt-3">
  <div class="col-sm-7 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/posts/ecsa/thesis-geis-nyquist.png" class="img-fluid rounded z-depth-1" zoomable=true alt="gEIS Nyquist plot showing a semicircle and its R-RC fit" %}
  </div>
</div>
<div class="caption">
  Fig 3. gEIS on one of my samples, closely tracing the typical semicircle, with its R–RC fit. Replotted from my MSc thesis <a href="#ref-1">[1]</a>, with points digitized from the printed figure.
</div>

**What I concluded.** After calculating the ECSAs, I found that most, though not all, of my Ni–W coatings had *lower* ECSA-normalized activity than bare nickel foam. In other words, the deposits performed better per geometric cm² mainly because they added surface area, not because the Ni–W sites were intrinsically better. The best NiW electrodes that **did** outperform pure Ni foam all came from 0.1 M tungstate, and tended to activate rather than degrade during cycling.

## Three years later: CatBot asks the same question

A 2026 DTU study [[2]](#ref-2) (led by the group that supervised my thesis) reports results from **CatBot** [[4]](#ref-4), a roll-to-roll robot that electrodeposits NiMo coatings on nickel wire and tests them in 30 wt% KOH at 80 °C, closer to industrial alkaline electrolysis conditions. A Gaussian-process Bayesian optimizer (based on BoTorch) chose each next recipe across six deposition parameters, a space of roughly 3.5 × 10¹⁴ combinations. Here, the objective merges activity and stability into one objective (so single objective vs multi-objective optimization): the average HER overpotential at −10 mA cm⁻² over 100 stress-test CV cycles. The best coatings reached about 63 mV within 30–40 experiments, and the top recipe, scaled up to nickel foam, needed about 173 mV to reach 1 A cm⁻².

It is essentially a much improved system of what I originally used: a similar nickel–group-6 alloy (Mo instead of W), electrodeposited from a citrate bath, with Bayesian optimization choosing the recipes. And underneath it all, the same question: **is the improvement coming from an intrinsically better catalyst, or from more active surface?**

How they used ECSA:
- **As a diagnostic.** Comparing their top coatings with randomly chosen baselines, higher performance correlated strongly with larger ECSA.
- **Cross-checked with structure and composition, which I didn't have time to do.** SEM showed the best coatings were noticeably rougher, with dense small globular clusters; XPS showed more Mo in the samples. Deposition temperature correlated with roughening, while sulfuric acid addition tracked Mo incorporation.
- **As a contributing factor.** They conclude there are two contributions: more Mo, and a rougher surface that *may* provide a larger ECSA. I originally attributed the activity gain mostly to the ECSA; they find that in this case it is likely both.

Three details stood out to me:

- **Temperature.** Their SHAP analysis of a fitted XGBoost model ranks deposition temperature as the most influential input, by a wide margin. Different but similar alloy to my NiW, and a different parameter range, so not a 1-1 comparison; still, it's ironically the parameter I found I couldn't count on due to large systematic experimental errors with my more primitive experimental setup.
- **The roughness factor influence.** To transfer recipes from wire to foam, they scaled the deposition current using a roughness factor of 16.5 for the foam. 
- **The surface moves.** After on/off cycling in a flow cell, the coating cracked and partly delaminated, and its Mo content halved (3.9 → 1.9 at.%). Again, we can see how complex such systems can be and how quickly their morphology can change, especially during dynamic operation, present in industrial conditions.

## What that number was assuming

A 2026 perspective in *ACS Energy Letters* by Mygind, Rost and Escudero-Escribano [[3]](#ref-3) is entirely about this problem. Their core move is to separate four quantities that often get blurred together:

| Quantity | What it is | Typically measured with | Good for |
|---|---|---|---|
| Geometric area | Naive apparent area | A "ruler" | Device-level performance |
| Real surface area | Full 3-D area, roughness included | Microscopy, gas adsorption, double-layer capacitance | Determining effects of architecture and porosity |
| ECSA | The part of the surface active for a *specific* reaction under *specific* conditions | Adsorption-limited Faradaic probes (H-UPD, metal UPD, CO stripping, oxide reduction) | Intrinsic activity, if the probe sees the right sites |
| Active-site count | A number of sites, not an area | Probe charge ÷ (elementary charge × electrons per site) | Turnover frequencies |

Through this framing, ECSA isn't a material property at all, but depends on the reaction, the measurement method and the conditions. Three apparent consequences relating to my results:

### 1. The double layer measures, at best, the "real" area

Double-layer capacitance responds to the charged interface wherever it exists, not only at the active sites partaking in the HER. So $$C_{\text{DL}}/C_s$$ is seemingly closer to a real-surface-area estimate than a true ECSA. When the reference is a single smooth surface, they recommend reporting the result as an **apparent** real surface area, so essentially the "ECSA" in my thesis.

### 2. $$C_s$$ is not a constant

Their compilation of reported specific capacitances spans from a few to over 200 µF cm⁻², depending on electrode material, electrolyte and measurement protocol. The same metal in two electrolyte concentrations, or the same RuO₂ facet (ordered versus disordered), can differ by close to a factor of two. One proposed workaround is to measure in an electrolyte chosen to suppress ion transfer across the interface, so that only genuine double-layer charging gets counted [[8]](#ref-8). The one aqueous nickel value in Fig. 4 sits around 30 µF cm⁻² (though measured in NaClO₄, not KOH as in my case). Even the "standard" shortcuts disagree: 20 µF cm⁻² is common for flat metals, while an influential OER benchmarking study used 40 µF cm⁻² in 1 M NaOH [[10]](#ref-10). That's a factor of two in ECSA from the choice of citation.

<div class="row justify-content-sm-center mt-3">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/posts/ecsa/mygind2026-fig6-capacitance.png" class="img-fluid rounded z-depth-1" zoomable=true alt="Bar chart of reported specific capacitance values across electrode-electrolyte systems" %}
  </div>
</div>
<div class="caption">
  Fig 4. Reported specific capacitances vary widely across materials, electrolytes and methods. Solid bars: cyclic voltammetry; hatched: EIS. From Mygind, Rost & Escudero-Escribano <a href="#ref-3">[3]</a>, ACS Energy Lett. 2026, Fig. 6. © 2026 The Authors, CC BY 4.0.
</div>

Since ECSA scales as $$1/C_s$$, any error in $$C_s$$ passes straight into the area, and inversely into "intrinsic" activity. In my results, comparing Ni–W against bare nickel foam with one $$C_s$$ essentially assumed that adding W doesn't change the capacitance per unit of real area. I think the qualitative conclusion holds up, and CatBot points the same way on roughness. But it still remains an assumption that quantitatively would lead to different numbers if I had measured the actual $$C_s$$ instead.

### 3. How you fit matters

Real interfaces rarely behave like one ideal capacitor. The usual explanation is that rough and porous surfaces are patchworks of regions with different local time constants, which together look like a constant phase element (CPE). The EIS tutorial [[11]](#ref-11) is more cautious: the evidence for roughness as the cause is mixed, and slow adsorption of ions or impurities is another candidate. Either way, the CPE takes the capacitor's place:

$$
Z_{\text{CPE}}(\omega) = \frac{1}{(j\omega)^{\alpha}\, Y_0}
$$

With $$\alpha = 1$$ you recover an ideal capacitor; the further $$\alpha$$ drops below 1, the flatter the semicircle gets (the purple curve in Fig. 2). The catch for ECSA: $$Y_0$$ is not a capacitance (its units are F s<sup>α−1</sup>), so it can't go straight into $$C_{\text{DL}}/C_s$$. Brug's formula [[12]](#ref-12) converts it into an effective capacitance first. With charge transfer present, as in an R–RC fit under HER, it reads:

$$
C_{\text{eff}} = \left[ Y_0 \left( \frac{1}{R_s} + \frac{1}{R_{ct}} \right)^{\alpha - 1} \right]^{1/\alpha}
$$

Even then, as $$\alpha$$ drops, the result is more of an effective number than a physical capacitance. Porosity adds a further wrinkle: deep pores need enough time (lower frequencies) and enough ionic strength to charge, so the chosen frequency window decides how much of a porous electrode, like a nickel foam, you actually "see". 

### The Faradaic route?

The alternative to the double layer is to count the charge of a surface reaction that stops once a single layer has formed: [hydrogen underpotential deposition (H-UPD)](https://www.sciencedirect.com/topics/chemistry/underpotential-deposition) on Pt, metal UPD such as Pb on Cu, [CO stripping](https://en.wikipedia.org/wiki/CO_stripping), or [surface-oxide reduction](https://www.sciencedirect.com/science/article/pii/S0040603120306468). Because the reaction is self-limiting, its charge scales with the number of surface sites. The measured charge is first corrected for double-layer charging and any coadsorbed species, $$Q_{\text{corr}} = Q_{\text{meas}} - Q_{\text{DL}} - Q_{\text{co}}$$, then divided by a reference charge per cm² of ideal surface. That reference charge is where the trouble starts, because most values in use are historical or idealized rather than measured on the surface at hand. The famous 210 µC cm⁻² for H-UPD on platinum traces back to unpublished krypton-adsorption measurements from the 1960s; decades later, Trasatti and Petrii [[5]](#ref-5) wrote that "the absolute significance of the accepted $$Q_{H,s}$$ is questionable." Similarly, the 516 µC cm⁻² often used for nickel-oxide reduction assumes a full monolayer on an idealized (100) terrace, with two electrons per surface atom.

Real surfaces rarely look like that, and the probe cycles themselves can restructure the very surface being measured, as well established by say, SEM/XPS measurements before and after. For well-characterized electrodes, Mygind et al. show a more rigorous option: build the reference from facet-specific values, weighted by the fraction of each facet present (from diffraction or microscopy). It's more accurate, but it needs structural data that is often more difficult to find.

### Or skip the area entirely

Their proposed alternative for intrinsic activity is to count sites directly from a probe reaction and report a turnover frequency:

$$
N_{\text{active}} = \frac{Q_{\text{probe}}}{e\, n_{\text{probe}}},
\qquad
\text{TOF} = \frac{I_{\text{target}}}{N_{\text{active}}\, n_{\text{target}}\, e}
$$

The catch: the probe has to sample the same sites that drive the reaction. Strongly binding sites tend to dominate probe charge, whereas the best catalytic sites, per the [Sabatier principle](https://www.frontiersin.org/journals/energy-research/articles/10.3389/fenrg.2021.654460/full), bind intermediates neither too strongly nor too weakly. Same electrode, potentially different site populations.

<div class="row justify-content-sm-center mt-3">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/posts/ecsa/mygind2026-fig7-flowchart.png" class="img-fluid rounded z-depth-1" zoomable=true alt="Flowchart for choosing a normalization strategy" %}
  </div>
</div>
<div class="caption">
  Fig 5. Picking a normalization: real surface area for architecture effects; site counting for intrinsic activity, if a probe genuinely reflects the active sites; otherwise real surface area with its assumptions stated. From Mygind, Rost & Escudero-Escribano <a href="#ref-3">[3]</a>, ACS Energy Lett. 2026, Fig. 7. © 2026 The Authors, CC BY 4.0.
</div>

## What I'd do now, for more robust results

- **Choose the area.** Given my specific assumptions, be more clear about which 'area' I would normalize over. Geometric, apparent real area or ECSA. I would not have used the active sites directly due to the complexity/budget of the setup needed.
- **Ideally measure $$C_{\text{DL}}$$ two ways**, both by CV scan rate [[7]](#ref-7) and by EIS.
- **Fit a CPE, not an ideal capacitor.** Under HER that means $$R_s$$ in series with $$R_{ct}$$ in parallel with a CPE, rather than the ideal R–RC I used. Report α, and convert $$Y_0$$ to an effective capacitance (Brug) before dividing by $$C_s$$.
- **State $$C_s$$ and its source.** Or better still, measure it on a smooth reference of the same material in the same electrolyte. 
- **Measure before and after stability testing.** Determining morphological changes to the area is critical for explainable results, as shown in the CatBot experiments.
- **Treat ECSA as even more of a diagnostic**, cross-checked against evidence from SEM and XPS measurements, rather than a metric to rank catalysts by.

## Conclusions & What I still don't know

Back to the iceberg. Here are some more questions I can now ask, but not yet answer:

- For Ni-based alloys in higher temperature and more concentrated KOH, which probe (if any) reflects the HER-active sites the way H-UPD does on Pt? A comparison of ECSA methods on nickel nanostructures [[9]](#ref-9) is a good starting point.
- How much of a porous foam's surface does a given EIS frequency window actually sample?
- When composition shifts during operation (such as Mo or W leaching), how far does $$C_s$$ move with it, if at all?
- What were the last few low-frequency gEIS points doing below the real axis in Fig. 3? The EIS tutorial [[11]](#ref-11) notes that low-frequency inductive loops are usually blamed on adsorbed reaction intermediates (and the HER has one: adsorbed hydrogen). Drift during a long, low-frequency measurement (bubbles forming under the HER current, for one) can produce artefacts too, and a Kramers–Kronig test would have told me which.

So, after all this I'm a bit closer to knowing how much I don't know. My main takeaway from this whole exercise is that **reproducibility of catalytic (or really any electrochemical) experiments is critical**, and reporting results in an agreed upon, normalized fashion would go a long way towards achieving it. At the same time, incorporating classic systematic and statistical **uncertainty propagation methods** relevant to one's experimental setup and parameters is another area that I find is often overlooked and that I like to focus on. Beyond typical error estimation through the Bayesian optimization loop itself, I think it can often help a researcher pinpoint hidden weaknesses in a setup, before running a full autonomous discovery loop.

*If you're curious about the optimization side, I rebuilt the thesis Bayesian-optimization workflow in Ax, built on top of BoTorch (check out the related project page [here](https://michailmitsakis.github.io/projects/msc-thesis/) and the repo [Ax_bayes_opt_NiW](https://github.com/michailmitsakis/Ax_bayes_opt_NiW))* 

## References

1. <a id="ref-1"></a>M. Mitsakis, "Machine learning-driven optimization of electrodeposited Ni–W electrocatalysts for H₂ evolution in alkaline electrolysis cells," MSc thesis, DTU Energy (2023).
2. <a id="ref-2"></a>P. V. Freiesleben de Blasio, J. Elsborg, E. Coşkun, A. Burger, P. M. Leuaa, T. P. A. Phan, J. H. Chang, C. Chatzichristodoulou, "Optimizing NiMo Electrocatalyst Coatings for Hydrogen Evolution in Alkaline Electrolysis With The Machine Learning-Guided Autonomous Platform CatBot," *Adv. Funct. Mater.* (2026), e78438. [doi:10.1002/adfm.78438](https://doi.org/10.1002/adfm.78438)
3. <a id="ref-3"></a>J. B. V. Mygind, M. J. Rost, M. Escudero-Escribano, "The Hidden Complexities of Electrochemically Active Surface Area Measurements," *ACS Energy Lett.* 11 (2026) 2508–2520. [doi:10.1021/acsenergylett.5c04204](https://doi.org/10.1021/acsenergylett.5c04204)
4. <a id="ref-4"></a>P. V. Freiesleben de Blasio, R. Kruger, N. Fisker-Bødker, J. H. Chang, C. Chatzichristodoulou, "CatBot – A High-Throughput Catalyst Synthesis and Testing System With Roll to Roll Transfer," *Digital Discovery* 4 (2025) 3810–3817. [doi:10.1039/D5DD00403A](https://doi.org/10.1039/D5DD00403A)
5. <a id="ref-5"></a>S. Trasatti, O. A. Petrii, "Real Surface Area Measurements in Electrochemistry," *Pure Appl. Chem.* 63 (1991) 711–734.
6. <a id="ref-6"></a>C. Wei, S. Sun, D. Mandler, X. Wang, S. Z. Qiao, Z. J. Xu, "Approaches for measuring the surface areas of metal oxide electrocatalysts for determining their intrinsic electrocatalytic activity," *Chem. Soc. Rev.* 48 (2019) 2518–2534.
7. <a id="ref-7"></a>D. M. Morales, M. Risch, "Seven steps to reliable cyclic voltammetry measurements for the determination of double layer capacitance," *J. Phys. Energy* 3 (2021) 034013.
8. <a id="ref-8"></a>Y. Yoon, B. Yan, Y. Surendranath, "Suppressing Ion Transfer Enables Versatile Measurements of Electrochemical Surface Area for Intrinsic Activity Comparisons," *J. Am. Chem. Soc.* 140 (2018) 2397–2400.
9. <a id="ref-9"></a>E. Cossar, M. S. E. Houache, Z. Zhang, E. A. Baranova, "Comparison of electrochemical active surface area methods for various nickel nanostructures," *J. Electroanal. Chem.* 870 (2020) 114246.
10. <a id="ref-10"></a>C. C. L. McCrory, S. Jung, J. C. Peters, T. F. Jaramillo, "Benchmarking Heterogeneous Electrocatalysts for the Oxygen Evolution Reaction," *J. Am. Chem. Soc.* 135 (2013) 16977–16987. {% comment %}CHECK: McCrory is believed to use C_s = 0.040 mF cm⁻² (40 µF cm⁻²) in 1 M NaOH and 0.035 in 1 M H2SO4; confirm in their ECSA/experimental section, then delete this note.{% endcomment %}
11. <a id="ref-11"></a>A. Ch. Lazanas, M. I. Prodromidis, "Electrochemical Impedance Spectroscopy—A Tutorial," *ACS Meas. Sci. Au* 3 (2023) 162–193. [doi:10.1021/acsmeasuresciau.2c00070](https://doi.org/10.1021/acsmeasuresciau.2c00070)
12. <a id="ref-12"></a>G. J. Brug, A. L. G. van den Eeden, M. Sluyters-Rehbach, J. H. Sluyters, "The analysis of electrode impedances complicated by the presence of a constant phase element," *J. Electroanal. Chem.* 176 (1984) 275–295.
13. <a id="ref-13"></a>M. M. Mench, *Fuel Cell Engines*, John Wiley & Sons (2008).
14. <a id="ref-14"></a>"Electric Double Layer," Chem 435 lab notes, New Mexico State University. [web.nmsu.edu/~snsm/classes/chem435/Lab14/double_layer.html](https://web.nmsu.edu/~snsm/classes/chem435/Lab14/double_layer.html)
