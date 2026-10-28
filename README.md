<p align="center">
  <img src="assets/profile-banner.svg" alt="Bingyao Cai · VegaBowl — From clinical questions to learning systems" width="100%" />
</p>

<p align="center">
   <a href="https://github.com/VegaBowl">GitHub</a> · <a href="mailto:2310306233@stu.pku.edu.cn">Email</a>
</p>

# Hi, I'm Bingyao Cai 👋

**Undergraduate medical student at Peking University Health Science Center · Five-year Clinical Medicine program, entering class of 2023 · Growing into medical AI research**

🫀 **Physiological signals** · 🦴 **Medical imaging** · 🧬 **Single-cell biology and multi-omics**

My research began with clinical questions. In the foot and ankle group at the Department of Sports Medicine, Peking University Third Hospital, I worked on clinical outcomes and single-cell data analysis in osteochondral lesions of the talus, connecting cell states and molecular interactions with clinical outcomes. I also participated in gastric cancer research combining proteomics and single-cell transcriptomics.

I now conduct exercise ECG research with **Shenda Hong's health data science team at Peking University**, carrying out the full workflow: clinical questions and labels, model design, training experiments, statistical analysis, and interpretation. I want to bring this clinical and molecular research experience into AI, studying how medical data can be represented, supervised, and integrated.

> 🧭 I want to turn clinical questions into testable computational problems, then interpret the results in their medical context.

## 🔬 On my bench

### Exercise ECG × cardiovascular–kidney–metabolic health

**e2eckm · My first systematic medical AI research project · Ongoing research**

Exercise provides a dynamic physiological challenge. I study whether the **full ECG response before, during, and after exercise** can help characterize current health, rank future clinical risk, and identify disease burden that warrants further assessment.

The project uses exercise ECG and linked clinical follow-up from UK Biobank. A technically eligible cohort of **58,658 participants** provides the starting population, with task-specific analysis subsets.

| Clinical question | Modeling task |
| :--- | :--- |
| What is the current cardiovascular–kidney–metabolic (CKM) state? | Baseline CKM stage classification |
| Who may experience a clinical cardiovascular event earlier? | Survival analysis and future-event risk ranking |
| Which health domains deserve closer assessment? | Diabetes, hypertension, kidney-risk, and lipid-burden identification |

**My responsibility: the full research workflow**, including:

- **Translating clinical concepts into training labels.** Integrating diagnoses, measurements, medications, and follow-up; retaining admissible stage intervals when the available evidence does not establish an exact stage.
- **Learning representations of the full exercise response.** Adapting ECGFounder window encoding and combining a temporal Transformer, phase summaries, and attention pooling; designing and training self-supervised objectives for VICReg consistency, future-window prediction, and signed cross-phase changes, followed by downstream fine-tuning.
- **Connecting representations to clinical tasks.** Comparing linear and MLP readouts; combining ECG representations with age, sex, and BMI for classification, survival, and disease-burden analyses.
- **Interpreting model behavior in medical terms.** Examining heart-rate–time and heart-rate–workload curves by risk group; comparing pooling weights with reference-window replacement sensitivity to understand why they emphasize different phases.

```text
Pre-exercise → Exercise → Recovery
                    ↓
Window-level ECG encoding + temporal modeling → 1024-D representation
                    ↓
CKM staging · Future-event risk ranking · Multi-domain disease burden
```

<details>
<summary><b>Methods and interim results</b></summary>

Single-lead input covers 42 ten-second windows. Participants are split into training, validation, and test sets; preprocessing statistics are fitted on training data only. The current working models connect the fine-tuned ECG representation to Ridge-Softmax, Ridge Cox, and Ridge Logistic readouts.

I compared weighted interval negative log-likelihood (WINLL) with an ordinal-threshold objective (WOT), used class weighting for imbalance, and compared downstream readout capacity. Temporal interpretation examines both the pooling pathway and the sensitivity of the complete representation to window replacement, without treating attention weights as direct risk contributions.

The following internal test-set results are from the project report updated through **October 26, 2026**. They are interim research findings.

| Task | Current result | Interpretation |
| :--- | :--- | :--- |
| Five-class CKM staging | Macro AUROC **0.8021**; macro F1 **0.4017** | Evaluated in the exact-label subset; minority-stage recognition remains limited |
| Future clinical cardiovascular events | Ten-year-truncated Harrell C **0.7201**; competing-risk-aware IPCW AUROC **0.7351** | Evaluated in the defined CKM-background risk set; primarily supports risk ranking |
| Four-domain disease burden | AUROC: diabetes **0.7734**, hypertension **0.8032**, kidney risk **0.6668**, lipids **0.7635** | Labels combine baseline disease with evidence within ten years; these are not incident-disease risk estimates |

The current model combination was selected after multiple exploratory analyses and with existing test results visible. Confirmatory validation after locking the analysis, stronger clinical benchmarks, and further calibration work remain necessary. These results do not establish validated individual ten-year absolute event probabilities.

</details>

### Knee MRI × weak supervision

**RSNA Knee Abnormality Detection · My first hands-on medical AI project · Exploratory work**

This was my first experience taking a medical imaging model through data preparation, training, evaluation, and competition submission. With learning and research as my goals, I explored **how to obtain more reliable supervision from radiology reports and use fine-grained information about abnormalities**.

- **Imaging:** DICOM geometric ordering, normalization, multi-plane and multi-sequence inputs; DINOv2-based experiments with slice aggregation, class-query cross-attention, and 2.5D inputs.
- **Labels and text:** Starting with public report-derived pseudo-labels, processing multilingual reports, and preserving location, anatomy, changes, assertion/negation/uncertainty, and severity; building structured findings through vocabulary analysis, rules, and LLM extraction.
- **Supervision and alignment:** Distinguishing positive, negative, and unmentioned findings; experimenting with confidence and three-state weighting, BioClinicalBERT-based image–report InfoNCE, and class-query/label-semantic alignment. Text supplies training-time signals; inference uses images only.
- **Medical reasoning:** Reviewing approximately 50 cases and using 58 expert-labeled cases to examine synovitis versus effusion, diagnostic criteria, missing report information, and proxy signals, followed by targeted fine-tuning.

**What I took away:** Text alignment did not show a consistent improvement over the image baseline in these experiments. A small labeled evaluation set and configuration differences also limited attribution. Experiments without performance gains still helped me narrow the question and revise hypotheses.

<details>
<summary><b>What the label-supervision experiments taught me</b></summary>

- **An unmentioned finding is not necessarily negative.** Synovitis experiments led me to examine reporting behavior, pseudo-label generation, and the underlying imaging phenotype; effusion is not a direct substitute label for synovitis.
- **More explicit text supervision can reinforce proxy signals.** Upweighting report-positive cases did not produce the expected improvement, prompting closer examination of sample selection and image features.
- **Alignment and task performance require separate evaluation.** A lower InfoNCE objective does not establish better classification performance.
- **Small evaluation sets limit conclusions.** The 58 expert-labeled cases were used repeatedly; these experiments generate hypotheses rather than independent confirmation. Changes in unfreezing, learning rates, and training strategy also affect comparisons.

</details>

### Single-cell biology and multi-omics × disease mechanisms

**My biomedical computational foundation, and a direction I want to connect with deep learning.**

In osteochondral lesion research, my single-cell analysis focused on matrix remodeling and the inflammatory microenvironment. The related manuscript describes **lesion and paired control tissues from five patients, with 123,944 cells retained after quality control**. Cell counts and participant sample size represent different levels of evidence.

- **Cell types and states:** annotation, subclustering, differential expression, GO enrichment, and GSVA to characterize fibroblast and macrophage states.
- **Dynamics and regulation:** Slingshot / Monocle 3 pseudotime, hdWGCNA co-expression modules, and DoRothEA transcription-factor activity to examine remodeling-associated transcriptional programs.
- **Cellular interactions:** CellChat / NicheNet communication and ligand–target inference, connecting stromal–immune hypotheses with experimental and clinical findings.

I was also the **third author of a study combining proteomics and single-cell transcriptomics in Helicobacter pylori–related gastric carcinogenesis**, published in [Cancer Biology & Medicine (2025)](https://doi.org/10.20892/j.issn.2095-3941.2025.0077).

🧩 **Connections I want to explore:** representation learning for cell states and gene expression, cross-batch and cross-omic integration, and fusion of molecular information with imaging and clinical phenotypes. These are future research interests grounded in my existing bioinformatics experience.

## 🧰 My toolkit

| Area | Practical experience |
| :--- | :--- |
| Clinical research | Retrospective cohorts, follow-up, imaging and laboratory data integration, clinical endpoints and research-label definitions |
| Medical AI | Medical image and time-series processing, pretrained-model adaptation, weak supervision, self-supervised learning, training, evaluation, and error analysis |
| Programming and statistics | R; Python / PyTorch project experience; classification evaluation and survival analysis |
| Single-cell and multi-omics background | Annotation, GSVA, pseudotime, co-expression networks, regulatory activity, cell communication, and proteomics/transcriptomics-related research experience |
| AI-assisted research | Agentic AI for implementation, debugging, and experiment organization, with medical review of data definitions and interpretation |

I am continuing to strengthen my foundations in deep learning, experimental design, and reproducible research code, and to translate clinical reasoning into better modeling and evaluation.

## 🩺 Where I come from

**September 2024–September 2026 · Sports Medicine, Peking University Third Hospital · Dong Jiang's group**  
Research on clinical outcomes and injury mechanisms in osteochondral lesions of the talus: cohort follow-up, data analysis, single-cell data mining, manuscript contributions, and participation in developing a surgical-outcome prediction system.

Earlier projects included childhood/adolescent obesity and hypertension, and research related to *Helicobacter pylori* and gastric cancer. These experiences introduced me to data at population, clinical, imaging, and molecular scales.

<details>
<summary><b>Selected publications, projects, and academic record</b></summary>

**Selected publications**

- **Co-first author** · *Bone tracer uptake in SPECT/CT is a potential indicator for treatment decision-making in cystic osteochondral lesions of the talus.* **Bone & Joint Research**, 2026;15(7):784–794. [DOI](https://doi.org/10.1302/2046-3758.157.BJR-2025-0635.R1)
- **Co-first author** · MRI“跳跃斑点征”可用于预测囊性距骨骨软骨损伤微骨折手术的长期预后. **生物医学转化**, 2026;7(1):88–93,108. [Chinese-language publication]
- **Co-first author** · 生活方式对大学生超重肥胖与血压偏高及其共病的影响. **中国儿童保健杂志**, 2024;32(11):1179–1185. [Chinese-language publication]
- **Third author** · *Proteomic profiling and scRNA sequencing identify signatures associated with Helicobacter pylori infection and risk of developing gastric cancer.* **Cancer Biology & Medicine**, 2025;22(8):946–963. [DOI](https://doi.org/10.20892/j.issn.2095-3941.2025.0077)

**Projects and presentations**

- **Medical Grand Challenge global finalist:** an AI system using paper ECGs to predict long-term cardiovascular–kidney–metabolic syndrome risk.
- **PKU Health Science Center “优星计划” funding:** single-cell investigation of persistent osteochondral lesions of the talus (pkustar2026015).
- First prizes in the Peking University Third Hospital undergraduate innovation project (2024–2025) and summer research program (2025).
- Poster presentation at the 2026 Chinese sports medicine annual meeting.

**Academics and languages**

- GPA **3.83 / 4.0**, ranked **1st** in my year; overall average **93.8**, ranked **2nd**.
- **National Scholarship**, 2023–2024; Peking University 三好学生标兵 award.
- TOEFL **109**; CET-6 **653**.

</details>

## 🌱 Next

I plan to continue pursuing AI-related research, whether through an AI research team or an AI role within a clinical research group. I want to grow into a researcher who can **formulate clinical questions, organize data, design experiments, and interpret model results**.

I am especially interested in:

- **Physiological time series and health management:** what can dynamic responses tell us?
- **Medical images and text:** how can clinical reports become reliable training signals?
- **Single-cell biology and multi-omics:** how can representation learning connect cell states, molecular changes, and clinical phenotypes?
- **Uncertainty and clinical evaluation:** how can we draw credible conclusions from missing data, noisy labels, and incomplete follow-up?

Happy to connect about medical AI, exercise ECGs, medical imaging, and learning AI from a clinical background.

📬 [2310306233@stu.pku.edu.cn](mailto:2310306233@stu.pku.edu.cn)

---

<p align="center"><i>Clinical curiosity. Learning by building. Keeping the evidence in view.</i></p>
