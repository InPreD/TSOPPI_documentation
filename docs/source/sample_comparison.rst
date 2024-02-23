**Sample comparison** tool
==========================

About
-----
A single patient might have multiple biopsies/samples sequenced with assays from the TSO500 family (TSO500/TSO500 HT/TSO500 ctDNA).
In these cases it is usually of interest to track the variant support between the samples.
The purpose of the Sample comparison tool is to make this tracking work easier.

Functionality overview
----------------------
The tool is meant to compare small variant sets reported for two input samples
(further referred to as "sample_A" and "sample_B") sequenced with a TSO500 assay.
Any combination of TSO500, TSO500 HT and TSO500 ctDNA samples is supported, but prior analysis with
Illumina's LocalApp v.2.2 (or Dragen ctDNA pipeline v.2.1) and post-processing
with TSOPPI's :doc:`sample data post-processing </sample_data_postprocessing>` tool are required.

A single plain-text output file with the following contents is generated:

- section [A]: input sample and tool version meta-information;
- section [B]: an overview of small variant counts for all possible classification combinations (i.e., the number of variants classified as 'SOM'
  in both samples, the number of variants classfied as 'SOM' in one sample and as 'GL_DB' in the other sample, etc.);
- sections [C1] and [C2]: the concordance/mutual recall of likely germline variants (i.e., variants classified as "GL_DB") reported in the compared samples;
- section [D]: details about selected 'noteworthy' variants reported in the compared samples (please see the 'Additional notes' section below regarding the variant selection process); the details include
  per-sample information (read depth, VAF, classification and potential inclusion in a given sample's TMB calculation by the respective Illumina pipeline),
  as well as generally applicable annotation (selected information from both PCGR and Nirvana output, the latter being limited to HIGH/MODERATE
  impact transcript changes according to `Ensembl's calculated variant consequences <http://mart.ensembl.org/info/genome/variation/prediction/predicted_data.html>`_).

Input files
-----------
- LocalApp-/Dragen-generated TMB trace TSV files, one for each input sample
  (e.g., "[LocalApp_output_directory]/Logs_Intermediates/Tmb/[sample_A_ID]/[sample_A_ID].tmb.trace.tsv"
  - please note that the file names differ based on the utilized Illumina pipeline);
- LocalApp-/Dragen-generated Nirvana annotation JSON files, one for each input sample
  (e.g., "[LocalApp_output_directory]/Logs_Intermediates/Annotation/[sample_A_ID]/[sample_A_ID]_SmallVariants_Annotated.json.gz");
- TSOPPI-generated small variant TSV files with the "Class_judgement" column, one for each input sample whose selection mode is set to "reportability"
  (e.g., "[TSOPPI_output_directory_for_sample_A]/[sample_A_ID]_small_variant_table.tsv");
- TSOPPI-generated PCGR small variant annotation TSV files, one for each input sample
  (e.g., "[TSOPPI_output_directory_for_sample_A]/[sample_A_ID]_PCGR_1.0.0_all/[sample_A_ID].pcgr_acmg.grch37.snvs_indels.tiers.tsv").

Output files
------------
- a plain-text file with the content as described above (sample ID values provided via the "-\-sample_A_ID"
  and "-\-sample_B_ID" parameters will be utilized inside the file).

Additional notes
----------------

- The concordance analysis (as reported on in sections [C1] and [C2]) consists of two steps:

  - identifying validation-eligible germline variants in one of the samples (the 'source sample');
  - checking the presence of the identified germline variants in the other sample (the 'recall sample').
  
  The whole process is performed twice, with each sample serving once in each role.
- In order to avoid noise during the concordance analysis, only variants of "sufficient quality" are considered
  by default. The following variant properties are required in the 'source sample':
  
   - SNV type (INDELs and MNVs are omitted);
   - "GL_DB" classification;
   - at least 50 high-quality reads at the variant site;
   - VAF >= 0.15.

  (The only variant requirement for the 'recall sample' is inclusion in the corresponding PCGR-generated file
   '\*.pcgr_acmg.grch37.snvs_indels.tiers.tsv' that is provided as analysis input.)
  As a consequence, the concordance comparisons aren't affected by low-level contamination
  (e.g., at 10 % contamination, most contaminant variants should have VAFs considerably below 0.15).
- An individual has typically between 1100 and 1200 high quality GL_DB variants covered by the TSO500 assay(s).
  Significantly higher variant counts (1500 - 1600) are usually indicative of substantial contamination.
- Parameters "-\-sample_A_variant_selection_mode" and "-\-sample_B_variant_selection_mode" determine which variants
  will be included in the detailed variant table at the bottom of the output file. Two modes are available
  (a different mode can be selected for each of the compared samples):

  - "reportability": selected variants are required to either have "include" 'Class_judgement' status in TSOPPI's small
    variant table and/or to have a non-empty 'Mutation_hotspot' value reported by PCGR; this mode is by default enabled for both input samples;
  - "consequence": selected variants are required to belong to one of the classes specified by the "-\-variant_class" parameter
    and to have a MODERATE/HIGH impact consequence in at least one transcript according to the Nirvana annotation
    (please see the Functionality overview section regarding the consequence assessment); this mode is currently recommended for ctDNA samples.

- When comparing a solid sample with a ctDNA sample, the current InPreD practice (as of November 2023)
  is to label the solid sample as "S" and the ctDNA sample as "C". The variant selection mode should be set to "consequence"
  for the ctDNA sample, while the solid sample should use the default ("reportability") mode.
- The detailed table in output section [D] is sorted based on variant consequence and classification in the input samples.
  First, variants with "PCGR_consequence" values other than "x:noncoding_variant" and "synonymous_variant" are listed,
  those are followed by the noncoding and synonymous variants. Within each of those two groups, the ordering follows
  the sample_A-sample-B classification order as listed in output section [B].

Running the tool
----------------
Command line options:

.. code-block::

  usage: compare_variant_lists.py [-h] [--version] --sample_A_ID SAMPLE_A_ID --sample_B_ID SAMPLE_B_ID --sample_A_variant_selection_mode {reportability,consequence} --sample_B_variant_selection_mode
                                  {reportability,consequence} --sample_A_pcgr_tsv SAMPLE_A_PCGR_TSV --sample_B_pcgr_tsv SAMPLE_B_PCGR_TSV --sample_A_trace_tsv SAMPLE_A_TRACE_TSV --sample_B_trace_tsv
                                  SAMPLE_B_TRACE_TSV --sample_A_nirvana_json SAMPLE_A_NIRVANA_JSON --sample_B_nirvana_json SAMPLE_B_NIRVANA_JSON --output_file OUTPUT_FILE --host_system_mounting_directory
                                  HOST_SYSTEM_MOUNTING_DIRECTORY [--sample_A_label SAMPLE_A_LABEL] [--sample_B_label SAMPLE_B_LABEL] [--sample_A_tsoppi_svt_tsv SAMPLE_A_TSOPPI_SVT_TSV]
                                  [--sample_B_tsoppi_svt_tsv SAMPLE_B_TSOPPI_SVT_TSV] [--variant_class {SOM,SOM_PCH,GL_P,GL_DB,BL}] [--minimum_read_depth MINIMUM_READ_DEPTH] [--minimum_VAF MINIMUM_VAF]
                                  [--container_mounting_directory CONTAINER_MOUNTING_DIRECTORY]

  Compare two samples based on their small variant lists. Assess germline variant correlation, compare somatic variant calls.

    --sample_A_ID SAMPLE_A_ID
                          ID string for sample A (the value will be used in the output).
    --sample_B_ID SAMPLE_B_ID
                          ID string for sample B (the value will be used in the output).
    --sample_A_pcgr_tsv SAMPLE_A_PCGR_TSV
                          Absolute path to TSOPPI-generated *.pcgr_acmg.grch37.snvs_indels.tiers.tsv file for sample A.
    --sample_B_pcgr_tsv SAMPLE_B_PCGR_TSV
                          Absolute path to TSOPPI-generated *.pcgr_acmg.grch37.snvs_indels.tiers.tsv file for sample B.
    --sample_A_trace_tsv SAMPLE_A_TRACE_TSV
                          Absolute path to a TMB trace tsv file generated by an Illumina pipeline for sample A.
    --sample_B_trace_tsv SAMPLE_B_TRACE_TSV
                          Absolute path to a TMB trace tsv file generated by an Illumina pipeline for sample B.
    --sample_A_nirvana_json SAMPLE_A_NIRVANA_JSON
                          Absolute path to Nirvana-generated json.gz file for sample A.
    --sample_B_nirvana_json SAMPLE_B_NIRVANA_JSON
                          Absolute path to Nirvana-generated json.gz file for sample B.
    --output_file OUTPUT_FILE
                          Asolute path to the output file.
    --host_system_mounting_directory HOST_SYSTEM_MOUNTING_DIRECTORY
                          Absolute path to the host system mounting directory. The specified directory should include all input and output file paths in its directory tree.
  optional arguments:
    -h, --help            show this help message and exit
    --version             show program`s version number and exit
    --sample_A_variant_selection_mode {reportability,consequence}
                          Method of selecting sample A`s somatic variants for detailed comparison. (default value: "reportability")
    --sample_B_variant_selection_mode {reportability,consequence}
                          Method of selecting sample B`s somatic variants for detailed comparison. (default value: "reportability")
    --sample_A_label SAMPLE_A_LABEL
                          Label string for sample A (the value will be used in the output). (default value: "A")
    --sample_B_label SAMPLE_B_LABEL
                          Label string for sample B (the value will be used in the output). (default value: "B")
    --sample_A_tsoppi_svt_tsv SAMPLE_A_TSOPPI_SVT_TSV
                          Absolute path to TSOPPI-generated small variant table file for sample A. Required if the "--sample_A_variant_selection_mode" is set to "reportability".
    --sample_B_tsoppi_svt_tsv SAMPLE_B_TSOPPI_SVT_TSV
                          Absolute path to TSOPPI-generated small variant table file for sample B. Required if the "--sample_B_variant_selection_mode" is set to "reportability".
    --variant_class {SOM,SOM_PCH,GL_P,GL_DB,BL}
                          In case of samples for which the "consequence" variant selection mode was chosen, only variants of the specified classes will be considered for reporting in the output detailed table.
                          Multiple values can be specified. (default value: ["SOM", "SOM_PCH", "GL_P"])
    --minimum_read_depth MINIMUM_READ_DEPTH
                          Minimum required read depth for variants included in the concordance/recall comparisons. Expecting integer values >= 20. (default value: 50)
    --minimum_VAF MINIMUM_VAF
                          Minimum required VAF for variants included in the concordance/recall comparisons. Expecting float values in range (0; 1]. (default value: 0.15)
    --container_mounting_directory CONTAINER_MOUNTING_DIRECTORY
                          Container`s inner mounting point. The host system mounting directory path/prefix will be replaced by the container mounting directory path in all input and output file paths (this
                          parameter shouldn't be changed during regular use). (default value: "/inpred/data")


Example invocation using the Docker image:

.. code-block::

  $ [sudo] docker run \
      --rm \
      -it \
      -v /hs_prefix_path:/inpred/data \
      inpred/icnva_main:0.2.0 \
        python /inpred/compare_variant_lists.py \
          --sample_A_ID tumor_DNA_A \
          --sample_B_ID tumor_DNA_B \
          --sample_A_label "S" \
          --sample_B_label "C" \
          --sample_A_variant_selection_mode "reportability" \
          --sample_B_variant_selection_mode "consequence" \
          --sample_A_tsoppi_svt_tsv /hs_prefix_path/postprocessing/run_A/<tumor_DNA_A>/<tumor_DNA_A>_small_variant_table.tsv \
          --sample_A_nirvana_json /hs_prefix_path/primary/run_A/Logs_Intermediates/Annotation/<tumor_DNA_A>/<tumor_DNA_A>_SmallVariants_Annotated.json.gz \
          --sample_B_nirvana_json /hs_prefix_path/primary/run_B/Logs_Intermediates/Annotation/<tumor_DNA_B>/<tumor_DNA_B>_SmallVariants_Annotated.json.gz \
          --sample_A_trace_tsv /hs_prefix_path/primary/run_A/Logs_Intermediates/Tmb/<tumor_DNA_A>/<tumor_DNA_A>_TMB_Trace.tsv \
          --sample_B_trace_tsv /hs_prefix_path/primary/run_B/Logs_Intermediates/Tmb/<tumor_DNA_B>/<tumor_DNA_B>.tmb.trace.tsv \
          --sample_A_pcgr_tsv /hs_prefix_path/postprocessing/run_A/<tumor_DNA_A>/<tumor_DNA_A>_PCGR_1.0.0_all/<tumor_DNA_A>.pcgr_acmg.grch37.snvs_indels.tiers.tsv \
          --sample_B_pcgr_tsv /hs_prefix_path/postprocessing/run_B/<tumor_DNA_B>/<tumor_DNA_B>_PCGR_1.0.0_all/<tumor_DNA_B>.pcgr_acmg.grch37.snvs_indels.tiers.tsv \
          --output_file /hs_prefix_path/postprocessing/sample_comparison/<tumor_DNA_A>_<tumor_DNA_B>_variant_set_overlaps.txt \
          --host_system_mounting_directory "/data"


(last updated: 2024-02-23)