Frequently asked questions
===========================

#### [How can I do...? Some exemplary protocols](#HowTo)
* [I have downloaded/received a BAM file - how do I generate a file I can look at in a Genome Browser?](#FASTQ2IGV)
* [How can I assess the reproducibility of my sequencing replicates?](#repCorr)
* [How do I know whether my sample is GC biased? And if yes, how do I correct for it?](#GC)
* [How do I get an input-normalized ChIP-seq coverage file?](#InputNorm)
* [How can I compare the ChIP strength for different ChIP experiments?](#fprint)
* [How do I get a (clustered) heatmap of sequencing-depth-normalized read coverages around the transcription start site of all genes?](#HM)
* [How can I compare the average signal for X- and autosomal genes for 2 or more different sequencing experiments](#profiler)

#### [Galaxy-specific questions](#GalSpecific)
* [I've reached my quota - what can I do to save some space?](#quota)
* [Copying from one history to another doesn't work for me - the data set simply doesn't show up in the target history!](#refresh)
* [How can I use a published workflow within deepTools Galaxy?](#workflow)
* [I would like to use one of your workflows - not in the deepTools Galaxy, but in the local Galaxy instance provided by my institute. Is that possible?](#workflow2)
* [How can I have a look at the continuous read coverages from bigWig files? Which Genome Browser do you recommend?](#browser)
* [What is the best way to integrate the deepTools results with other downstream analyses (outside of Galaxy)?](#integrate)
* [How can I determine basic parameters of a BAM file?](#BAMparams)

#### [General deepTools-related questions](#general)
* [How does deepTools handle data from paired-end sequencing?](#PE)
* [I just want to try out a tool, how can I optimize the computation time?](#compTime)
* [Does it speed up the computation if I limit bamCorrelate to one chromosome, but keep the same numbers and sizes of sampling bins?](#bamCorrelateLimit)
* [When should I exclude regions from computeGCbias?](#excludeGC)

###### Heatmapper
* [How can I increase the resolution of the heatmap?](#hmresolution)
* [How can I change the automatic labels of the clusters in a kmeans clustered heatmap?](#hmlabels)

###### External data
* [How do I calculate the effective genome size for an organism that's not in your list?](#effGenomeSize)
* [Where can I download the 2bit genome files required for computeGCbias?](#2bit)

###### [Go to deepTools Galaxy](https://deeptools.ie-freiburg.mpg.de)

###### [Go to the general Galaxy help page](https://github.com/fidelram/deepTools/blob/master/manual/GalaxyHelp.md)

###### [Go to installation information](https://github.com/fidelram/deepTools/edit/master/README.md#installation)
----------------------------------------------------------------------------------------------------


How can I do...? <a name="HowTo"></a>
--------------------------------------------------
This section is meant to give you quick guidance to specific tasks you may want to perform. We're using screenshots from Galaxy here, if you're using the command-line version, you can easily follow the given examples by typing the program name and the help option (e.g. /deepTools/bin/bamCoverage --help) which will show you all the parameters and options, most of them named very similarly to those in Galaxy.

For each "recipe" here, you will find the screenshot of the tool and the input parameters on the left hand side (we marked non-default, _user-specified entries_) and screenshots of the output on the right hand side. Do let us know if you spot things that are missing, should be explained better or are plain confusing!

There are many more ways in which you can use [deepTools Galaxy][] than those described here, so be creative once you're comfortable with using them. For detailed explanations of what the tools do, follow the links.

__All recipes assume that you have uploaded your files into a Galaxy instance with a deepTools installation, e.g. [deepTools Galaxy][].__

_If you would like to try out the protocols with sample data, go to [deepTools Galaxy][] --> "Shared Data" --> "Data Libraries" --> "Sample Data". Use one file from each folder. E.g., import 1 BAM file from the folder "mapped reads", 1 bigwig file from the folder "normalized read coverages", and the .bed file "Human RefSeq genes" from the folder "annotation data" into your current Galaxy history. For testing our protocols via the command line, you can download the sample files to your computer by clicking on the triangle right next to the file name._ 

* [I have downloaded/received a BAM file - how do I generate a file I can look at in a Genome Browser?](#FASTQ2IGV)
* [How can I assess the reproducibility of my sequencing replicates?](#repCorr)
* [How do I know whether my sample is GC biased? And if yes, how do I correct for it?](#GC)
* [How do I get an input-normalized ChIP-seq coverage file?](#InputNorm)
* [How can I compare the ChIP strength for different ChIP experiments?](#fprint)
* [How do I get a (clustered) heatmap of sequencing-depth-normalized read coverages around the transcription start site of all genes?](#HM)
* [How can I compare the average signal for X- and autosomal genes for 2 or more different sequencing experiments](#profiler)

------------------------------------------------------------------------------------------------------

#### I have downloaded/received a [BAM][] file - how do I generate a file I can look at in a Genome Browser?<a name="FASTQ2IGV"></a>
* tool: [bamCoverage][]
* input: your BAM file

Note: BAM files can also be viewed in Genome Browsers, however, they're large and tend to freeze the applications. Generating bigWig files of read coverages will help you a lot in this regard. In addition, if you have more than one sample you'd like to look at, it is helpful to normalize all of them to 1x sequencing depth.

![GalHow_bamCoverage](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_bamCoverage.png "deepTools Galaxy screenshot of bamCoverage usage and output")
--------------------------------------------------

#### How can I assess the reproducibility of my sequencing replicates?<a name="repCorr"></a>
* tool: [bamCorrelate][]
* input: BAM files
    * you can compare as many samples as you want - the more you put at the same time, the longer the computation takes
* output: heatmap of correlations - the closer two samples are to each other, the more similar their read coverages

![GalHow_bamCorrelate](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_bamCorrelate.png "deepTools Galaxy screenshot of bamCorrelate usage and output")
--------------------------------------------------

#### How do I know whether my sample is GC biased? And if yes, how do I correct for it?<a name="GC"></a>
* you need a BAM file of your sample in question
* use the tool [computeGCbias][] on that BAM file (default settings, just make sure your reference genome and genome size are matching)

![GalHow_computeGC](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_computeGCbias.png "deepTools Galaxy screenshot of computeGCbias")

* have a look at the image that is produced and compare it to the examples [here](https://github.com/fidelram/deepTools/blob/master/manual/QC.md#computeGCbias)
* if your sample shows an almost linear increase in exp/obs coverage (on the log scale of the lower plot), then you should consider correcting the GC bias - _if_ you think that the biological interpretation of this data would otherwise be compromised (e.g. by comparing it to another sample that does not have an inherent GC bias)
    
    + the GC bias can be corrected with the tool [correctGCbias][] using the second output of the computeGCbias tool that you had to run anyway
    + CAUTION!! correctGCbias will add reads to otherwise depleted regions (typically GC-poor regions), that means that you should __not__ remove duplicates in any downstream analyses based on the GC-corrected BAM file (we therefore recommend to remove duplicates before doing the correction so that only those duplicate reads are kept that were produced by the GC correction procedure)

![GalHow_correctGC](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_correctGCbias.png "deepTools Galaxy screenshot of correctGCbias usage and output")
--------------------------------------------------

#### How do I get an input-normalized ChIP-seq coverage file?<a name="InputNorm"></a>

* input: you need two BAM files, one for the input, one for the ChIP-seq experiment
* tool: [bamCompare][] with ChIP = treatment, input = control sample

![GalHow_bamCompare](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_bamCompare.png "deepTools Galaxy screenshot of bamCompare usage and output")
--------------------------------------------------

#### How can I compare the ChIP strength for different ChIP experiments?<a name="fprint"></a>
* tool: [bamFingerprint][]
* input: as many BAM files as you'd like to compare. Make sure you get all the labels right!

![GalHow_Fingerprint](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_bamFingerprint.png "deepTools Galaxy screenshot of bamFingerprint")
--------------------------------------------------

#### How do I get a (clustered) heatmap of sequencing-depth-normalized read coverages around the transcription start site of all genes?<a name="HM"></a>

* tools: [computeMatrix][], then [heatmapper][]
* inputs:
    * 1 bigWig file of normalized read coverages (e.g. the result of bamCoverage or bamCompare)
    * 1 BED or INTERVAL file of genes, e.g. obtained through Galaxy via "Get Data" &rarr; "UCSC main table browser" &rarr; group: "Genes and Gene Predictions" &rarr; (e.g.) "RefSeqGenes" &rarr; send to Galaxy (see screenshots below)

![GalHow_clustHM01](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_clustHM01.png "deepTools Galaxy screenshot of how to get a list of genes from UCSC")

* use [computeMatrix][] with the bigWig file and the BED file
* indicate "reference-point"  (and whatever other option you would like to tune, see screenshot below)

![GalHow_clustHM02](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_clustHM02.png "deepTools Galaxy screenshot of computeMatrix for profiles in reference-point mode with output")

* use the output from computeMatrix with [heatmapper][]
    * if you would like to cluster the signals, choose "kmeans clustering" (last option of "advanced options") with a reasonable number of clusters (usually between 2 to 7)

![GalHow_clustHM03](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_clustHM03.png "deepTools Galaxy screenshot of heatmapper usage and output")
--------------------------------------------------

#### How can I compare the average signal for X- and autosomal genes for 2 or more different sequencing experiments?<a name="profiler"></a>

Make sure you're familiar with computeMatrix and profiler before using this protocol.
* tools:
    * Filter data on any column using simple expressions
    * computeMatrix
    * profiler
    * (plotting the summary plots for multiple samples)
* inputs:
    * several bigWig files (one for each sequencing experiment you would like to compare)
    * two BED files, one with X-chromosomal and one with autosomal genes

##### How do obtain a BED file for X chromosomal and autosomal genes each

1. download a full list of genes via "Get Data" &rarr; "UCSC main table browser" &rarr; group:"Genes and Gene Predictions" &rarr; tracks: (e.g.) "RefSeqGenes" &rarr; send to Galaxy
2. filter the list twice using the tool __"Filter data on any column using simple expressions"__ 

    - first use the expression: c1=="chrX" to filter the list of all genes &rarr; this will generate a list of X-linked genes
    - then re-run the filtering, now with c1!="chrX" which will generate a list of genes that do not belong to chromosome X (!= indicates "not matching")

##### Compute the average values for X and autosomal genes 
* use [computeMatrix][] for __each__ signal file (bigWig) (you only need to specify all the parameters once, then use the re-run button underneath the first data set and just replace the signal file with the next one)
    * supply both filtered BED files (click on "Add new regions to plot" once) and label them
    * indicate the corresponding signal file
    * make sure to __re-name__ every data set in the history once computeMatrix is done so that you can easily keep track of which matrix was based on which bigWig file (you can always find these information by clicking on the i-button in the respective data set)
* now use [profiler][] for every file you generated with computeMatrix
    * important: display the "advanced output options" and select "save the data underlying the average profile" &rarr; this will generate a table in addition to the summary plot images
* now you have at least 2 separate images of profiles - one for each bigWig file - you can either leave it like this or use another script that will plot all the summary plots in one image at once
    * this tool is called "Plotting the summary plots for multiple signals"
    * it uses the tables generated by profiler
    * for each group of genes (in this case, X and autosomal genes = 2 groups), you can assign a color

The result could look like this:

![GalHow_profilesXA02](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_profiles_XvsA02.png "combined profiles for different histone marks") 

As you have noticed, this task requires several steps that are repeated. Here is a screenshot of how the Galaxy workflow would look like (you can find it under "Shared Data" &rarr; "Published Workflows" &rarr; "Summary plots for X and autosomal genes" where we have constructed it with the example histone marks from the Data Library. Be aware that running this workflow will take up quite some computation timing, but it won't require much input from your part - so start if before you go off for lunch ;) )

If you're not sure how to use the published workflow, please read [this entry](#workflow) or go to the central [Galaxy learning page full of tutorials]( https://wiki.galaxyproject.org/Learn/Screencasts#Tutorials "Galaxy Tutorials").

![GalHow_profilesXA](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_profiles_XvsA.png "Screenshot of the workflow designed for the above described task")

--------------------------------------------------



Galaxy-specific questions <a name="GalSpecific"></a>
--------------------------------------------------

#### I've reached my quota - what can I do to save some space? <a name="quota"></a>
1. make sure that all the data sets you deleted are __permanently__ eliminated from our disks: go to the history option button and select "Purge deleted data sets", then hit the "refresh" button on top of your history panel
2. download all data sets for which you've completed the analysis, then remove the data sets (click on the "x" and then make sure they're purged (see above)

#### Copying from one history to another doesn't work for me - the data set simply doesn't show up in the target history!<a name="refresh"></a>
Once you've copied a data set from one history to another, check two things:
* do you see the destination history in your history panel, i.e. does the title of the current history panel match the name of the destination history you selected in the main frame?
* hit the refresh button

![GalHow_clustHM03](https://raw.github.com/fidelram/deepTools/master/examples/Gal_historyReload.png "Galaxy history refresh button")


#### How can I use a published workflow?<a name="workflow"></a>
You __must register__ if you want to use the workflows within [deepTools Galaxy][]. ("User" &rarr; "Register" - all you have to supply is an email address)

You can find workflows that are public or specifically shared with you by another user via "Shared Data" &rarr; "Published Workflows". Click on the triangle next to the workflow you're interested in and select "import".

![GalHow_wf01](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_wf01.png "Finding published workflows")

A green box should appear, there you select "start using this workflow" which should lead you to your own workflow menu (that you can always access via the top menu "Workflow"). Here, you should now see a workflow labeled "imported: ....". If you want to use the workflow right away, click on the triangle and select "Run". The workflow should now be available within the Galaxy main data frame and should be waiting for your input.

![GalHow_wf02](https://raw.github.com/fidelram/deepTools/master/examples/GalHow_wf02.png "Finding published workflows")

#### I would like to use one of your workflows - not in the deepTools Galaxy, but in the local Galaxy instance provided by my institute. Is that possible? <a name="workflow2"></a>

Yes, it is possible. The only requirement is that your local Galaxy has a recent installation of deepTools.

Go to the workflows, click on the ones you're interested in and go to "Download". This will save the workflows into .ga files on your computer. Now go to your local Galaxy installation and login. Go to the workflow menu and select "import workflow" (top right hand corner of the page). Click on "Browse" and select the saved workflow. If you have the same tool versions installed in your local Galaxy, these workflows should work right away.

#### How can I have a look at the continuous read coverages from bigWig files? Which Genome Browser do you recommend? <a name="browser"></a>
There are 2 popular Genome Browsers to visualize continuous data: [UCSC](http://genome.ucsc.edu/cgi-bin/hgGateway?redirect=manual&source=genome-euro.ucsc.edu "UCSC Genome Browser") and [IGV](http://www.broadinstitute.org/igv/ "IGV Genome Browser").

##### IGV (recommended)

We recommend to download the [IGV Genome Browser](http://www.broadinstitute.org/igv/ "IGV Genome Browser"). Any user working for academia will be granted access after an informal registration. IGV itself needs an up-to-date Java installation and considerable amounts of RAM. It's usage is rather intuitive and the display can be easily customized. In addition, you can download genome-wide annotation data that can be displayed together with your own data.

To display data in IGV, do the following steps:

1. go to http://www.broadinstitute.org/igv/, register and download IGV
2. go to deepTools Galaxy and download the file(s) you would like to visualize (can be bigWig, bedGraph, bed, BAM)
3. open IGV, then choose the genome version of the file(s) you would like to visualize (e.g. mm9) THIS IS THE MOST IMPORTANT STEP! (IGV will not detect the genome version automatically, i.e. if you select mm9 but your file is based on human data, it will still be displayed without an error message (but with the wrong positions, obviously!))
4. go to "File" --> "Load from file" --> select the file(s) you just downloaded from Galaxy

Here's a screenshot of a typical bigWig file display:
![GalFAQ_IGV](https://raw.github.com/fidelram/deepTools/master/examples/Gal_FAQ_IGV.png "Screenshot of IGV browser display of bigWig files")

For more information, check out the [IGV documentation](http://www.broadinstitute.org/software/igv/UserGuide "IGV User Guide").

##### UCSC
There is a direct link from within deepTools Galaxy to stream a data set to UCSC. You can find it in the data set tiles: "display at UCSC", like here:

![GalFAQ_IGV](https://raw.github.com/fidelram/deepTools/master/examples/Gal_FAQ_UCSC_dataset.png "Screenshot of IGV browser display of bigWig files")
Click on "main" and the UCSC browser should open within a new window, displaying the data set that you chose.
The default setting for bigWig files is the "dense" display that looks like a heatmap.

![Gal_FAQ_UCSC01](https://raw.github.com/fidelram/deepTools/master/examples/Gal_FAQ_UCSC01.png "Screenshot of UCSC Genome Browser display of bigWig files")
If you would like to display the continuous profile in a "valley-mountain" fashion like the one shown in the IGV screenshot, go to the drop-down menu underneath your custom track and choose "full".

UCSC has large amounts of public data that you can display which you can find by scrolling down the page, beyond your custom track entry. For more information on how to use the UCSC Genome Browser, go [here](https://genome.ucsc.edu/goldenPath/help/hgTracksHelp.html "UCSC user guide").

###### Known issues with UCSC
* **chromosome naming**: UCSC expects chromosome names to be indicated in the format "chr"Number, e.g. chr1. If you mapped your reads to a non-UCSC-standard genome, chances are that chromosomes are labeled just with their number. bigWig files generated from these BAM files will not be recognized by UCSC, i.e. you will see the data set name, but no signal.
* **no upload of bigWig files from your hard drive**: to minimize the computational strains, UCSC relies on streaming bigWig files (i.e. there's no need to load the entire file at once, the browser will always just load the data for the specific region a user is looking at).

#### What's the best way to integrate the deepTools results with other downstream analyses (outside of Galaxy) <a name="integrate"></a>
* you can save all the data tables underlying every image produced by deepTools, i.e. if you would like to plot the average profiles in a different way, you could download the corresponding data (after ticking the profiler option at "advanced output options") and import them into R, Excel, GraphPadPrism etc.


#### How can I determine basic parameters of a BAM file, such as the number of reads, read length, duplication rate and average DNA fragment length? <a name="BAMparams"></a>

Eventhough [MACS][] is meant to do peak calling for you, it also outputs a number of useful information such as those listed above.
Simply run MACS on the BAM file that you would like to gain the information for and check the .xls file from the MACS output. It will list:

* tag length = read length
* duplication rate
* number of tags = number of reads
* d = distance = average DNA fragment size



General deepTools-related questions <a name="general"></a>
--------------------------------------------------------------

#### How does deepTools handle data from paired-end sequencing?<a name="PE"></a>
Generally, all the modules working with BAM files (_bamCorrelate, bamCoverage, bamCompare, bamFingerprint, computeGCbias_)
recognize paired-end sequencing data. You can enforce to ignore the fragment length based on the mate pairs using the option __doNotExtendPairedEnds_

#### How can I test a tool with little computation time? <a name="compTime"></a>
* when you're playing around with the tools to see what kinds of results they will produce: limit the operation to one chromosome only to __save computation time__! ("advanced output options" &rarr; "Region of the genome to limit the operation to")

#### Does it speed up the computation if I limit bamCorrelate to one chromosome, but keep the same numbers and sizes of sampling bins?<a name="bamCorrelateLimit"></a>
Yes. However, the way bamCorrelate (and all the other deepTools handle the option "limit the computation to a specific region" is as follows: first, the _entire_ genome represented in the BAM file will be regarded and sampled, _then_ all the regions or sampled bins that do not overlap with the region indicated by the user will be discarded. This means that if you wanted 10,000 bins to be sampled and you focus on, let's say, chromosome 2, the final computation will not be performed on the whole set of 10,000 bins, but only on those bins that overlap with chromosome 2.

#### When should I exclude regions from computeGCbias? <a name="excludeGC"></a>
In general, we recommend that you should only correct for GC bias (using computeGCbias followed by correctGCbias) if you observe that the majority of the genome (the region between 30-60%) is continuously GC-biased __and__ you want to compare this sample with another sample that is not GC-biased.

Sometimes, a certain GC bias is expected, for example for ChIP samples of H3K4me3 in mammalian samples where GC-rich promoters are expected to be enriched. To not confound the GC bias caused by the library preparation with the inherent, expected GC bias, we incorporated the possibility to supply a file of regions to computeGCbias that will be excluded from the GC bias calculation. This file should typically contain those regions that one expects to be significantly enriched per se. This way, the computeGCbias will focus on background regions.


#### The heatmap I generated looks very "coarse", I would like a much more fine-grained image. <a name="hmresolution"></a>
* decrease the __bin size__ when generating the matrix using computeMatrix
  * go to "advanced options" &rarr; "Length, in base pairs, of the non-overlapping bin for averaging the score over the regions length" &rarr; define a smaller value, e.g. 50 or 25 bp
* make sure, however, that you used a sufficiently small bin size when calculating the bigWig file, though (if generated with deepTools, you can check the option "bin size")


#### How can I change the automatic labels of the clusters in a kmeans clustered heatmap?<a name="hmlabels"></a>
Each cluster will get its own box, exactly the same way as different groups of regions. Therefore, you can use the same option to define the labels of the final heatmap: Heatmapper &rarr; "Advanced output options" &rarr; "Labels for the regions plotted in the heatmap".

If you indicated 3 clusters for kmeans clustering, enter here: C1, C2, C3 &rarr; instead of the full default label ("cluster 1"), the heatmap will be labeled with the abbreviations.

#### How do I calculate the effective genome size for an organism that's not in your list?<a name="effGenomeSize"></a>
This is something you will have to find a solution outside of deepTools at the moment. We suggest to run faCount from UCSC tools. If you used multi-read alignment (e.g. with bowtie2), then you can use that tool to report the total number of bases as well as the number of unmapped bp, indicated by 'N'. The effective genome size is the total number of reads minus the number of 'N'.


#### Where can I download the 2bit genome files required for _computeGCbias_?<a name="2bit"></a>
The 2bit files of most genomes can be found [here](http://hgdownload.cse.ucsc.edu/gbdb/).
Search for the .2bit ending. Otherwise, __fasta files can be converted to 2bit__ using the UCSC programm
faToTwoBit (available for different plattforms from [here](http://hgdownload.cse.ucsc.edu/admin/exe/)

----------------------------------------------------------------

[Download PDF](https://github.com/fidelram/deepTools/raw/master/manual/PDFs/GalaxyFAQs.pdf)

----------------------------------------------------------------
[Galaxy]: http://galaxyproject.org/ "General Galaxy platform from Penn State"
[GEO]: http://www.ncbi.nlm.nih.gov/geo/ "GEO database"
[Roadmap project]: http://www.roadmapepigenomics.org/data "Roadmap web site"
[UCSC]: http://genome.ucsc.edu/ "UCSC Genome web site"
[BioMart]: http://www.biomart.org/ "Biomart web site"
[deepTools Galaxy]: http://deeptools.ie-freiburg.mpg.de/ "deepTools Galaxy at the Max-Planck-Institute of Immunobiology and Epigenetics"

[bamCorrelate]: https://github.com/fidelram/deepTools/blob/master/manual/QC.md
[bamFingerprint]: https://github.com/fidelram/deepTools/blob/master/manual/QC.md
[computeGCBias]: https://github.com/fidelram/deepTools/blob/master/manual/QC.md
[bamCoverage]: https://github.com/fidelram/deepTools/blob/master/manual/normalizations.md
[bamCompare]: https://github.com/fidelram/deepTools/blob/master/manual/normalizations.md
[correctGCbias]: https://github.com/fidelram/deepTools/blob/master/manual/normalizations.md
[computeMatrix]: https://github.com/fidelram/deepTools/blob/master/manual/visualizations.md
[heatmapper]: https://github.com/fidelram/deepTools/blob/master/manual/visualizations.md
[profiler]: https://github.com/fidelram/deepTools/blob/master/manual/visualizations.md
[MACS]: http://www.ncbi.nlm.nih.gov/pubmed/22936215 "How to use MACS, Nature Protocols"
[CCAT]: http://www.ncbi.nlm.nih.gov/pubmed/20371496 "CCAT original publication"
[SICER]: http://bioinformatics.oxfordjournals.org/content/25/15/1952.full "SICER original publication"

[BAM]: https://docs.google.com/document/d/1Iv9QnuRYWCtV_UCi4xoXxEfmSZYQNyYJPNsFHnvv9C0/edit?usp=sharing "binary version of a SAM file; contains all information about aligned reads"
[SAM]: https://docs.google.com/document/d/1Iv9QnuRYWCtV_UCi4xoXxEfmSZYQNyYJPNsFHnvv9C0/edit?usp=sharing "text file containing all information about aligned reads"
[bigWig]: https://docs.google.com/document/d/1Iv9QnuRYWCtV_UCi4xoXxEfmSZYQNyYJPNsFHnvv9C0/edit?usp=sharing "binary version of a bedGraph file; contains genomic intervals and corresponding scores, e.g. average read numbers per 50 bp"
[bedGraph]: https://docs.google.com/document/d/1Iv9QnuRYWCtV_UCi4xoXxEfmSZYQNyYJPNsFHnvv9C0/edit?usp=sharing "text file that contains genomic intervals and corresponding scores, e.g. average read numbers per 50 bp"
[FASTQ]: https://docs.google.com/document/d/1Iv9QnuRYWCtV_UCi4xoXxEfmSZYQNyYJPNsFHnvv9C0/edit?usp=sharing "text file of raw reads (almost straight out of the sequencer)"

 
