
InPreD sample ID nomenclature
=============================

(v.4, March 17 2025)

InPreD sample IDs should be recorded with 19 characters, in format *PPPyyyy-Ann-Sxz-Mll*, where:

- **PPP** is a three-letter Project code:

  - *IPA*: InPreD AHUS;
  - *IPD*: InPreD OUS;
  - *IPH*: InPreD HUS;
  - *IPO*: InPreD St. Olav;

- **yyyy** is a four-digit patient code (all samples of a given patient should have the same patient code within a specific project);
- **A** is a single-letter nucleic acid input type code:

  - *C*: Cell-free samples;
  - *D*: DNA;
  - *R*: RNA;

- **nn** is a two-digit assay type code:

  - *01*: TSO500 DNA;
  - *02*: `Archer FusionPlex Lung assay <https://archerdx.com/research-products/solid-tumor-research/fusionplex-lung/>`_;
  - *03*: TSO500 RNA;
  - *04*: EPIC DNA methylation;
  - *05*: whole-genome DNA sequencing (WGS);
  - *06*: TSO500 HRD (TSO500 solid + HRD);
  - *07*: whole-transcriptome RNA sequencing (WTS);
  - *50*: [external] Twist Human Core Exome Plus (DNA);
  - *51*: [external] TruSeq Stranded mRNA;

- **S** is a single-letter sample type code:

  - *A*: post Allotransplantation;
  - *C*: Cell-line;
  - *D*: Distal metastasis, naive;
  - *d*: distal metastasis, post-treatment;
  - *E*: naive;
  - *e*: post treatment;
  - *L*: Liquid;
  - *M*: Metastasis;
  - *N*: Normal/control;
  - *P*: Primary tumor, naive;
  - *p*: primary tumor, post-treatment;
  - *R*: Regional metastasis, naive;
  - *r*: regional metastasis, post-treatment;
  - *T*: Primary tumor;
  - *X*: unknown;

- **x** is the *(n-1)th* library preparation attempt:

  -	*0*: first try;
  - *1*: second try (e.g., after cleaning, new extraction);
  - *2*: third try;
  - ...
  - *9*: validation/verification test;

- **z** is the *nth* biological replicate (e.g., using a different block or stock):

  -	*1*
  - *2*
  - ...

- **M** is a single-letter code for sample material:

  - *A*: Archived (FFPE);
  - *B*: Blood;
  - *C*: Cytology;
  - *E*: Extramedullary;
  - *F*: fresh Frozen;
  - *M*: fresh bone Marrow;
  - *S*: buccal Swab;
  - *X*: unknown.

- **ll** is a two-digit code for tumor site (these codes are adopted from `PCGR <https://github.com/sigven/pcgr>`_):

  - *00*: Cancer origo incerta;
  - *01*: Adrenal Gland;
  - *02*: Ampulla of Vater;
  - *03*: Biliary Tract;
  - *04*: Bladder/Urinary Tract;
  - *05*: Bone;
  - *06*: Breast;
  - *07*: Cervix;
  - *08*: CNS/Brain;
  - *09*: Colon/Rectum;
  - *10*: Esophagus/Stomach;
  - *11*: Eye;
  - *12*: Head and Neck;
  - *13*: Kidney;
  - *14*: Liver;
  - *15*: Lung;
  - *16*: Lymphoid;
  - *17*: Myeloid;
  - *18*: Ovary/Fallopian Tube;
  - *19*: Pancreas;
  - *20*: Peripheral Nervous System;
  - *21*: Peritoneum;
  - *22*: Pleura;
  - *23*: Prostate;
  - *24*: Skin;
  - *25*: Soft Tissue;
  - *26*: Testis;
  - *27*: Thymus;
  - *28*: Thyroid;
  - *29*: Uterus;
  - *30*: Vulva/Vagina;
  - *XX*: unknown.

(last updated: 2025-03-17)
