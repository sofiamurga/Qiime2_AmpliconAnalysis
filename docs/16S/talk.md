<!-- .slide: data-background="assets/isb/data-midnight.jpg" class="dark" -->

# Amplicon Sequencing Data Analysis with QIIME 2

### Alyssa Easton, Gibbons Lab

<img src="assets/isb/logo.png" width="40%">

from the *ISB Microbiome Course 2024*

<br>
<div class="footer">
<a href="https://creativecommons.org/licenses/by-nc/4.0/"><i class="fa fa-bullhorn"></i>CC-BY-NC</a>
<a href="https://gibbons.isbscience.org/"><i class="fa fa-globe"></i>gibbons.isbscience.org</a>
<a href="https://github.com/gibbons-lab"><i class="fa fa-github"></i>gibbons-lab</a>
<a href="https://www.linkedin.com/in/alyssa-n-easton-7692b4175"><i class="fa fa-linkedin-square"></i>Alyssa N Easton</a>
</div>

---

## Let's Start Learning :books:

First, we'll need the slides, full of _digestible_ information

*https://gibbons-lab.github.io/isb_course_2024/16S*

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

# Organization of the course

<img src="assets/materials.png" width="80%">


---

## Guts, Camera, Action :movie_camera:


📕 Let's switch to the notebook and get started. Step 1: save a copy of this notebook in your Google Drive.

<br>

<a href="https://colab.research.google.com/github/gibbons-lab/isb_course_2024/blob/main/16S_2024.ipynb"
   target="_blank">Click me to open the notebook!</a>

In case you get lost, *all* output we generate can be found on [Github](https://github.com/Gibbons-Lab/isb_course_2024/tree/main/treasure_chest), or in `materials/treasure_chest` in Colab.


---

<div style="display: flex; justify-content: space-between; align-items: center">

<div>

## The Gut Microbiome 🦠

- 30-40 trillion bacterial cells
- Heterogenous between individuals
- Helps digest food and produces metabolites
- Affects our entire body, including the brain 🧠

</div>

 <img src="assets/microbes.png" width="35%">

</div>

---

## The Gut-Brain Axis

<img src="assets/gut_brain_axis.png" height="700vh">

<div class="footnote">
created with *BioRender.com*
</div>

---

## Why Parkinson's Disease?

- Parkinson's Disease (PD) is characterized by aggregation of *alpha-synuclein* protein and degeneration of of *dopaminergic* neurons, leading to widespread neuroinflammation and progressive motor impairment.
- Motor symptoms are often *preceded* by gastrointestinal symptoms like *constipation*, increased gut permeability and inflammation.

Meta-analysis of the Parkinson's Disease Microbiome suggests alterations linked to intestinal inflammation ([Romano et. al. 2021](https://www.nature.com/articles/s41531-021-00156-z))

---

## Analyzing gut microbial composition in Parkinson's Disease (PD)

In a 2017 [study](https://movementdisorders.onlinelibrary.wiley.com/doi/10.1002/mds.26942), *Hill-Burns et. al.* performed 16S rRNA sequencing of DNA extracted from the stool of 197 PD cases and 130 controls. Using tools like QIIME, they identified small but significant effects of Parkinson's Disease and common PD medications on the gut microbiome. 

[Free full-text manuscript available at Europe PMC](https://europepmc.org/article/PMC/5469442)

This data was later included in a 2021 [meta-analysis](https://www.nature.com/articles/s41531-021-00156-z) of PD-microbiome data

Today, we'll look at a *small subset* of the original data: 5 Parkinson's Disease patients and 5 healthy controls.

---

## Measuring Your Microbes

How do we see what is in the microbiome?

- Hundreds to thousands of taxa in each person
- Difficult to culture outside the resident environment
- We can *sequence* their DNA instead

---

## What will we do today?

<img src="assets/steps.png" width="100%">

</div>

---

# QIIME 2: Quantitative Insights into Microbial Ecology

Pronounced like *chime* 🔔

Created ~2010 during the Human Microbiome Project (2007 - 2016) under the leadership
of Greg Caporaso and Rob Knight.

> *QIIME 2* is a powerful, extensible, and decentralized microbiome
analysis package with a focus on data processing and analysis transparency.

QIIME 2 comes with a lot of help, including a wide range of [tutorials](https://docs.qiime2.org/2024.5/tutorials/),
[general documentation](https://docs.qiime2.org/2024.5/) and a [user forum](https://forum.qiime2.org/) where you can ask questions.

---

## But what is QIIME2, really?

Essentially, QIIME 2 is a set of *commands* to transform microbiome *data* into
*intermediate outputs* and *visualizations*.

<img src="assets/barplot.gif" width="100%">

It's commonly used via the *command line*. We'll use it within the Colab Notebook.

---

## QIIME2 Workflow

When we run a QIIME2 command, we specify the inputs and *action* to perform, and QIIME2 will output *artifacts* (.qza) and/or *visualizations* (.qzv).

<img src="assets/key.png" width="50%"><img src="assets/overview.png" width="50%">

<div class="footnote">

https://docs.qiime2.org/2024.5/concepts/
https://docs.qiime2.org/2024.5/tutorials/overview/

</div>

---

## Let's make an artifact

To start, we'll import our raw data into QIIME as an *artifact*.

:computer: Let's switch to the notebook and get started

</div>

---

## What is amplicon sequencing?

<img src="assets/16S.png" width="100%">

---

## Why the 16S gene?

![](assets/16S_gene.png)


The 16S gene is *universal* and contains interspersed conserved regions perfect for *PCR* priming and hypervariable regions with *phylogenetic heterogeneity*. Our data used the V4 region. 

The V4-specific primers used in this study were F515/R806. How long is the amplified fragment, and how long are the reads?

---

## Errors during PCR and sequencing generate *noise*

<img src="assets/pcr_seq_errors.png" width="80%">

---

## DADA2 to the rescue!

We just ran the DADA2 plugin for QIIME, which is doing 4 things:

1. *filter and trim* the reads<br>
    a. trim low quality regions<br>
    b. remove reads with low average quality<br>
    c. remove reads with ambiguous bases (Ns)<br>
    d. remove PhiX (added to sequencing)
2. find the most likely *original sequences* in the sample (*ASVs*)
3. remove chimeras
4. count ASV abundances

</div>

---

## Identifying Amplicon Sequence Variants (ASVs)

<img src="assets/dada2.png" width="80%">

Expectation-Maximization (EM) algorithm simultaneously assigns ASVs and models error ([Callahan, 2016](https://www.nature.com/articles/nmeth.3869#MOESM269)).

---

We now have a table containing the counts for each ASV in each sample.
We also have a list of ASVs.

<br>

:thinking_face: Do you have an idea for what we could do with those two data sets? What quantities
might we be interested in?

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Diversity metrics

In microbial community analysis we are usually interested in two different families of diversity metrics:<br>
- *alpha diversity* (ecological diversity within a sample)
- *beta diversity* (ecological differences between samples)

---

## Alpha diversity

How diverse is a single sample?

<img src="assets/alpha_diversity.png" width="50%">

- *richness:* how many taxa do we observe (richness)?<br>
  → #observed taxa
- *evenness*: how evenly are abundances distributed across taxa?<br>
  → Evenness index
- *mixtures*: metrics that combine both richness and evenness<br>
  → Shannon Index, Simpson's Index

Each sample has **1** Shannon Index.

---

## Beta diversity

How different are two or more samples/donors/sites from one another other?

<img src="assets/beta_diversity.png" width="50%">

- *unweighted:* how many taxa are *shared* between samples?<br>
  → Jaccard index, unweighted UniFrac
- *weighted:* do shared taxa have *similar abundances*?<br>
  → Bray-Curtis distance, weighted UniFrac

Each sample has **n** Bray-Curtis distances, where n = number of samples.

---

### UniFrac beta diversity

Do samples share *genetically similar* taxa?
UniFrac distance = branch length

<img src="assets/unifrac.png" width="70%">

Weighted UniFrac **scales branches by abundance**, so the presence of one distant member does not skew diversity.

---

## How to build a phylogenetic tree?

One of the basic things we might want to look at is how the sequences across
all samples are related to one another. That is, we are often interested in their *phylogeny*.

Phylogenetic trees are built from *multiple sequence alignments* and sequences are
arranged by *sequence similarity* (branch length).

Let's make one!

---

## Statistical tests for alpha diversity

Alpha diversity can be treated as any other sample measurement and is suitable for *classic univariate tests*.
    → Nonparametric: Mann-Whitney U test
    → Parametric: t-test

---


## Visualizing beta diversity with PCoA

<img src="assets/pcoa.png" width="100%">


---

## Statistical tests for beta diversity

More complicated. Usually not normal and very heterogeneous. PERMANOVA can deal with that.

<img src="assets/permanova.png" width="80%">

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Run the diversity analyses

:computer: Let's switch to the notebook and calculate the diversity metrics

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## But what organisms are there in our sample?

We are still just working with sequences and have no idea what *organisms*
they correspond to.

<br>

:thinking_face: What would you do to go from a sequence to an organism's name?

---

## Taxonomic ranks

<img src="assets/ranks.png" width="40%">

---

Even though directly aligning our sequences to a *database of known genes*
seems most intuitive, this does not always work well in practice. Why?

---

## Multinomial Naive Bayes

<img src="assets/naive_bayes.png" width="75%">

Instead, use *subsequences (k-mers)* and their counts to *predict* the
lineage/taxonomy with *machine learning* methods. For 16S amplicon fragments this
often provides better *generalization* and faster results.

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Let's assign taxonomy to the sequences

:computer: Let's switch to the notebook and assign taxonomy to our ASVs

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

# Differential abundance

How can we compare abundance between groups of samples (like case-control)?

---

## PSA: Sequencing is a *random sample* of an ecosystem

*Sampling fraction* varies between samples.

<img src="assets/sampling_fraction.png" width="75%">

Even if we knew the concentration of bacteria in a sample, we don't know how much bacterial biomass is in each person.
What we do know is the **proportions** of the bacteria **within a sample** (maybe).

<div class="footnote">

Figures from [Vandeputte, 2017](https://www.nature.com/articles/nature24460)

---

## What taxonomic level should we use?

16S rRNA amplicon sequencing generally has good resolution at the *genus* level.

---

## What statistical tests can we use?

Before we do any tests, we need to LOOK at the data 🔍<br>
How is it distributed? What is the variance?

---

## Relative abundance data structure

<div style="display: flex; justify-content: space-around; align-items: center; height: 100%">

<div style="width: 40%; padding: 5px;">

Microbiome relative abundance data is:
- compositional
- not normally distributed
- zero-inflated: contains both true and sampling zeros
- more variable than expected by a Poisson model (overdispersed)
- heteroscedastic

</div>

<div style="width: 40%; text-align:center; padding: 5px;">

<img src="assets/rel_abund_histogram.png">

</div>
</div>

These features violate the assumptions of parametric statistical tests.

---

## So what can we do?

We have a few options:
- nonparametric, rank-based tests (underpowered)

- *transform* the data, then parametric tests (may require us to discard data)

- more complex statistical models (each with their own caveats + assumptions)

---

## Wilcoxon Rank-Sum Test (a.k.a. Mann-Whitney U test)

- Nonparametric test for difference in a continuous variable between two groups
- Uses *ranks*, rather than counts
- Underpowered, because we are not assuming an ideal distribution shape

<img src="assets/RA_split_dist.png" width="75%">

---

## Normalization + Parametric tests

The center-log ratio transform (Aitchison, 1982), is a transformation for compositional data that normalizes by the *sample geometric mean*, which is less sensitive to outliers and gives more weight to smaller values.

<img src="assets/clr.png" width="75%">

The caveat: zeros are still a problem. We can either impute them somehow, or discard them. Check out [this blog post](https://cduvallet.github.io/posts/2018/06/fuzzy-zeros) for more information on imputation with zero-inflated compositional data.

---

## After hypothesis testing, we need to *correct* our p-values

Multiple comparisons inflate the likelihood of false positives!

Check your p-value distribution.

---

## Limitations

Analysis of synthetic communities suggests that *bacterial taxa* have different *sequencing efficiencies*<br>

If each taxon has a sequencing efficiency *B*, between-sample differences in actual abundance (A) get *distorted*

<img src="assets/taxa_bias.png" width="50%">

Only one method currently takes this possible bias into account; radEmu (developed by David Claussen and Amy Willis at UW) is currently in review, but the R package is available on [Github]()

<div class="footnote">

Figure from [McLaren, 2019](https://elifesciences.org/articles/46923)<br>
Expanded manuscript [McLaren, 2022](https://mikemc.github.io/differential-abundance-theory/v/7412a36ddb8cad3c1c0f3ff2055f5402c4a74360/index.html)

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

## Let's try it!

:computer: We will now switch to the notebook to do some normalization and statistical testing.

---

## In conclusion, always look at your data!

---

## Your turn!

Which taxa are associated with the disease state?

<img src="assets/coding.gif" width="50%">

---

<!-- .slide: data-background="var(--primary)" class="dark" -->

### And we are done :clap:

<div style="display: flex; justify-content: space-around; align-items: center;">

<div>

Alex Carr <br>
Jacob Cavon <br>
Christian Diener <br>
Alyssa Easton <br>
Karl Gaisser <br>
Sean Gibbons <br>
Crystal Perez <br>
Nick Quinn-Bohmann <br>
Noa Rappaport <br>

</div>

<div>

Shanna Braga<br>
Greg Caporaso <br>
Audri Hubbard <br>
Connor Kelly<br>
Allison Kudla <br>
Dominic Lewis <br>
Joe Myxter <br>
Thea Swanson <br>
Victoria Uhl<br>
ISB Facilities Team

</div></div>

# Thanks! :heart:

