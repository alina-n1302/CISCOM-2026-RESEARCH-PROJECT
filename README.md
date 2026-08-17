# ABE Guide RNA Ranking for the BCL11A Enhancer

This project presents a computer-based workflow for ranking guide RNAs for adenine base editing (ABE). The workflow focuses on the +58 DNase I hypersensitive site of the BCL11A erythroid enhancer and is intended to support future research in hematopoietic stem and progenitor cells (HSPCs). The project does not introduce a new base editor or a new machine-learning model. Its main contribution is the integration of guide design, editing predictions, bystander analysis, off-target-related metrics, and composite ranking in one BCL11A-focused pipeline.

All results in this project are computational predictions. They are not laboratory measurements and do not prove that any guide is safe or effective in primary human HSPCs. The ranked guides should therefore be treated as candidates for future laboratory testing.

# Background

Hematopoietic stem and progenitor cells are blood-forming stem cells that give rise to different types of blood cells. Correcting these cells could support healthy blood production over a long period. This makes HSPCs important for research on inherited blood diseases such as sickle cell disease and β-thalassemia.

Traditional CRISPR-Cas9 editing cuts both strands of DNA. These cuts can sometimes cause unwanted insertions, deletions, or rearrangements. Base editing aims to change one DNA letter without making a traditional double-strand break. This project focuses on adenine base editing, which changes adenine (A) to guanine (G).

Guide RNA selection is difficult because guide performance can depend on editing efficiency, bystander activity, specificity, sequence context, genomic location, chromatin, epigenetic marks, and cell type. AI and machine-learning models can help estimate some of these features before laboratory testing.

# Project goal

The goal of this project is to create a clear and reproducible way to identify candidate ABE guide RNAs for the BCL11A enhancer, remove guides with weaker quality or safety-related metrics, predict editing efficiency and bystander activity, compare specificity and off-target-related metrics, rank guides using several features together, and create an intergenic-only list for additional prioritization.

# Workflow

The workflow begins by retrieving the BCL11A target sequence from NCBI using Biopython. It then reads a CRISPOR-like guide table, applies guide-quality filters, builds 50-nucleotide sequence contexts, runs BE-Hive-derived predictions, normalizes and combines the guide metrics, and exports ranked guide lists.

The target region is located on the hg19 human genome assembly at chr2:60,721,212-60,722,958. The retrieved sequence is saved as bcl11a_sequence.fa.

The guide table contains guide IDs, target sequences, specificity scores, off-target counts, guide-design scores, genomic locations, and safety annotations. The workflow filters this table using the selected quality and safety rules and prepares the remaining guides for BE-Hive-derived prediction.

For every filtered guide, the workflow creates a 50-nucleotide sequence context. It checks the guide in the correct orientation and removes guides when a valid sequence context cannot be created.

The workflow uses BE-Hive-derived models to predict editing efficiency and bystander activity. The model is run with an adenine base-editor setting and uses CELL_TYPE = 'mES' to keep the computational procedure reproducible. This cell-type setting is not a direct measurement of editing in primary HSPCs.

The workflow combines predicted editing efficiency, guide specificity, predicted off-target burden, predicted bystander activity, guide-design scores, genomic-location information, and safety annotations. These values are normalized and combined into a weighted score called final_score. The guides are then sorted from the highest score to the lowest score. The weights are practical choices and were not fitted to experimental HSPC data or clinical outcomes.

# Main results

The workflow began with 240 guide RNA candidates. After filtering, 124 guides remained and 116 guides were removed. The retention rate was 51.7 percent.

BE-Hive efficiency predictions were available for 123 guides. The mean predicted efficiency was 0.442, the median was 0.481, and the maximum was 0.904. These are predicted values and should not be described as measured editing rates.

The results show why guide ranking should not depend on one metric alone. High specificity does not always mean high predicted editing efficiency, and bystander activity or safety annotations can change the final priority of a guide.

The workflow proposes guides such as 1091rev, 101forw, and 1090rev for further prioritization. These guides are computational candidates for future laboratory testing, not confirmed therapeutic guides.

The displayed top five candidates mapped to the expected intergenic region between BCL11A and AC009970.1. This supports the consistency of the guide annotations with the intended target region, but it does not prove biological specificity or safety.

# Output files

The workflow produces several output files. The file bcl11a_sequence.fa contains the retrieved target sequence. The file behiveinput.tsv contains guide data prepared for prediction. The file behiveoutput.tsv contains predicted editing and bystander results. The file final_ranked_guides.tsv contains the complete ranked guide list, while final_ranked_guides_intergenic.tsv contains the ranked intergenic guide list.

# Reproducibility

The workflow was developed in Google Colab. It uses Biopython, pandas, scikit-learn, and separate conda environments to support the different toolchains. BE-Hive-derived predictions were run in the oldenv environment.

To repeat the analysis, the same genome assembly, guide table, model settings, filters, normalization method, and score weights should be used. Changing these inputs may change the final ranking.

# Limitations

The current project does not include laboratory testing in primary CD34+ HSPCs, direct comparison with published BCL11A enhancer editing results, comparison between predicted scores and measured editing outcomes, or sensitivity analysis of the composite-score weights.

Many existing prediction tools were trained using cell lines or other datasets rather than patient-derived HSPCs. As a result, their predictions may not fully represent editing in therapeutic HSPC settings.

# Future work

Future work should test the highest-ranked guides in primary CD34+ HSPCs and measure editing efficiency, bystander effects, off-target activity, cell outcomes, and persistence. The predictions should then be compared with experimental results and with published BCL11A enhancer guide data when the experimental conditions are similar.

Future analysis should also test alternative score weights and measure ranking stability. HSPC-specific chromatin, epigenetic, and training data should be added when such data become available. These steps will help determine whether the current guide priorities are stable and whether the predictions transfer to primary HSPCs.

# Responsible use

This workflow is a first-stage screening tool. The ranked guides should be used to help plan laboratory experiments, not to make therapeutic or clinical decisions.
