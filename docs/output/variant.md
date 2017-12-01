# Variant Pipeline Results Description (2016-12-30)

Version 3.1 (2016-12-30)

## Directory structure

The main results directory will be of the form:

	.../<LAB>/<INVESTIGATOR>/<PROJNUM>/<REVISION>/

LAB, INVESTIGATOR and the mskcc ids for the lab head and investigator.
PROJNUM is the iGO project number and REVISION is the version of this
result. All previous runs of the pipeline will be stored and any re-run
will go into a new REVISION folder.

Under this main folder you will see:

```bash
alignments
metrics
post
variants
```

which are the various output directories of the WES pipeline. The
contents are as follows

* alignments --- BAM files

* metrics --- various metrics (most from PICARD) on the output

* post --- filtered, post-processed output.

* variants/copyNumber/facets --- output of Venkat and Ronglai's new copy number algorithm

### post folder

This folder contains the primary MAF file. It will be called:

```
post/Proj_<NUM>___SOMATIC.vep.filtered.facets.V3.maf
```

It has been post process and annotated with the VEP annotator. Additionally gene level information from the FACETS output has been joined in, including: local purity, ploidy, cancer cell fraction, and confidence intervals.

Additionally, the file is now marked with a number of filtering flags to help mark a number of potential artifacts that lead to false positives.

The filters currently being used are:

* filter_cohort_normals
* filter_low_conf
* filter_blacklist_regions
* filter_normal_panel
* filter_ffpe_pool
* filter_ffpe

__N.B.__, the events have only been _flagged_ not removed. But it is strongly suggested that some of all of these be redacted before moving forward with any further downstream analysis.

The filtering code is online at:
```
https://github.com/mskcc/wes-filters
```
there you can find more documentation and the source code.

And there is also

```Proj_<NUM>___FILLOUT.V3.maf```

which is the _filled_ outmaf which contains DP and AD information for every sample for each event called in the SOMATIC MAF.


The code for post-processing is available here:

```bash
https://github.com/soccin/Variant-PostProcess/tree/feature/Version3
```

on on LUNA at:

```bash
/home/socci/Code/Pipelines/CBE/Variant/PostProcessV3
```

I will write up more the postprocessing steps are time goes on.

### facets folder

This folder has the output from the FACETS algorithm. At the top level there is a SEG file which is an IGV formatted copy number segmenation file of all samples in the hisensitivity settings. Then for every sample (tumor/normal) pair there is a separate folder for the facets output for each pair.

For each sample FACETS is run twice once in a low sensitivity seeting (Cval==300) to get an estimate for the diploid level and sample level purity. Then the algorithm is rerun in a high sensitivity pass (Cval==100) to get more fine grain segmenation.

For each pass the following files are output

* `Proj_#####__<TUMOR-ID>__<NORMAL-ID>_hisens.BiSeg.png`
* `Proj_#####__<TUMOR-ID>__<NORMAL-ID>_hisens.CNCF.png`

Plots of the BiSegmention and the Cancer Cell Fractions

* `Proj_#####__<TUMOR-ID>__<NORMAL-ID>_hisens.cncf.txt`

Facets segmention which has for each segment: coordinates, metrics, cell fraction (cf), total and lessor copy number (tcn, lcn) for both estimators, cncf and em.

* `Proj_#####__<TUMOR-ID>__<NORMAL-ID>_hisens.out`

Run metrics/settings and global (sample level) Purity, Ploidy, dipLogR

* `Proj_#####__<TUMOR-ID>__<NORMAL-ID>_hisens.seg`

IGV segmentation file for this sample

* `Proj_#####__<TUMOR-ID>__<NORMAL-ID>_hisens.Rdata`

FACETS input file; SNP counts for both tumor and normal.
