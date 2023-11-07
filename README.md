
This workshop will cover amplicon-based microbiome analysis using [QIIME2](https://qiime2.org). 
This four-day workshop will consist of hands on training to analyze from raw dataset through publication-quality statistics and visualizations.

There are more detailed descriptions and tutorials on the [QIIME2 website](https://docs.qiime2.org/2019.1/tutorials/).

## Get Started
## $ prompt

The $ in the `Terminal` is simply a way for the computer to notify you that it is ready to receive typed commands.    

**Note: The $ is not typed when issuing commands. It is already there.**

For example, `student01` would see the following `Terminal` prompt:
```
[student01@macbook ~]$
```

## Home directory
Once you have connected to the sumo computer, you “**land**” within your “**Home**” directory. For `student01`, this would be:

```
/home/student01
```

If at any point you get lost within the labyrinth of directories, simply type `cd` to come back.

If you would like to back up by a single directory, type `cd ..`

To know where your terminal is “looking” at any moment, you can invoke the print working directory command: `pwd`

```
$ pwd    
$ /home/student01 
```
   
Then, we create the directory with mkdir

Create a project folder

`$ mkdir 111323_qiime2_tutorial`   
We can verify that this directory exists by checking the content of our “Home” directory with the list command ls

```
$ ls     
111323_qiime2_tutorial   
```
  
Next, move into your  111323_qiime2_tutorial folder. This will be done with the change directory command cd into  111323_qiime2_tutorial:

Change directory

`$ cd 111323_qiime2_tutorial`    
We can check that this worked with the command print working directory pwd. For student01, this would look like:

```
$ pwd    
$ /home/BIOTECH/student01/111323_qiime2_tutorial    
```


### Natively installing QIIME2 on your own machine


>**Note:** Make sure that your machine is powerful enough for the data set you wish to run. What we are doing today is a very, very small data set, which should not pose any issues on a resonably new standard laptop. However, if you were to run a full data set on a low-end personal computer, you would likely run out of memory and crash it. So make sure that whatever machine you use is up to the task before you start.


The latest version of Qiime2 can be found [here](https://docs.qiime2.org/2023.9/install/#qiime-2-2023-9-distributions)).   

If you have a Mac or Linux system -  You can install using Miniconda.   

Other options for everyother OS including windows OS is   
[WSL](https://docs.qiime2.org/2023.9/install/virtual/wsl/),  
[Docker](https://docs.qiime2.org/2023.9/install/virtual/docker/) and  
[Amazon Web Services](https://docs.qiime2.org/2019.1/install/virtual/aws/) 

## Data description

In this tutorial you’ll use QIIME 2 to perform an analysis of soil samples from the Atacama Desert in northern Chile. The Atacama Desert is one of the most arid locations on Earth, with some areas receiving less than a millimeter of rain per decade. Despite this extreme aridity, there are microbes living in the soil. The soil microbiomes profiled in this study follow two east-west transects, Baquedano and Yungay, across which average soil relative humidity is positively correlated with elevation (higher elevations are less arid and thus have higher average soil relative humidity). Along these transects, pits were dug at each site and soil samples were collected from three depths in each pit.
  

>The data used in this tutorial is presented in: Significant Impacts of Increasing Aridity on the Arid Soil Microbiome. Julia W. Neilson, Katy Califf, Cesar Cardona, Audrey Copeland, Will van Treuren, Karen L. Josephson, Rob Knight, Jack A. Gilbert, Jay Quade, J. Gregory Caporaso, and Raina M. Maier. mSystems May 2017, 2 (3) e00195-16; DOI: 10.1128/mSystems.00195-16.
  
### Downloading data

`mkdir raw_data`

**Forward reads**   
```
wget -O "raw_data/forward.fastq.gz" "https://data.qiime2.org/2019.1/tutorials/atacama-soils/10p/forward.fastq.gz"   
```
**Reverse reads**      
```
wget -O "raw_data/reverse.fastq.gz" "https://data.qiime2.org/2019.1/tutorials/atacama-soils/10p/reverse.fastq.gz"     
```
**Barcodes**     
```
wget -O "raw_data/barcodes.fastq.gz" "https://data.qiime2.org/2019.1/tutorials/atacama-soils/10p/barcodes.fastq.gz" 

**Demultiplexed reads**
```
mkdir raw_data/demux
cp /mnt/software/workshop/data/qiime2/qiime2_tutorial/demux/data/*.fastq.gz raw_data/demux/.
```

## 1. First steps

### 1.1 Format metadata file

You can format the metadata file in a spreadsheet and validate the format using a google spreadsheet plugin 'Keemei' as described on the QIIME2 website. The only required column is the sample id column, which should be first. All other columns should correspond to sample metadata. Below, we use the default filename of metadata.txt.

```
wget -O "sample-metadata.tsv" "https://data.qiime2.org/2019.1/tutorials/atacama-soils/sample_metadata.tsv"
```
  
### 1.2 Inspect read quality
A combination of [FASTQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/) and [MultiQC](https://multiqc.info/) is used to check if the reads are of reasonable quality and to  how your reads should be trimmed in downstream steps. The report "***multiqc_report.html***" is found in the fastqc output directory. you can view this report in the web browser. 

```
mkdir fastqc

fastqc -t 4 raw_data/*.fastq.gz -o fastqc

cd fastqc
multiqc .
cd ..
```

### 1.3 Activate conda environment

You should run this workflow in a conda environment, which makes sure the correct version of the Python packages required by QIIME2 are being used. You can activate this conda environment with this command:

```
source activate qiime2-2020.8
```

## 2 Paired-end read analysis commands
To analyze these data, the sequences that you just downloaded must first be imported into an qiime artifact of type EMPPairedEndSequences.

```
qiime tools import --type EMPPairedEndSequences --input-path raw_data --output-path raw_data/paired-end-sequences.qza
```

You next can demultiplex the sequence reads. This requires the sample metadata file, and you must indicate which column in that file contains the per-sample barcodes. In this case, that column name is BarcodeSequence. In this data set, the barcode reads are the reverse complement of those included in the sample metadata file, so we additionally include the --p-rev-comp-mapping-barcodes parameter. After demultiplexing, we can generate and view a summary of how many sequences were obtained per sample.

```
qiime demux emp-paired --m-barcodes-file sample-metadata.tsv --m-barcodes-column BarcodeSequence --i-seqs raw_data/paired-end-sequences.qza --o-per-sample-sequences demux.qza --p-rev-comp-mapping-barcodes

qiime demux summarize --i-data demux.qza --o-visualization demux.qzv
```

### 2.1 Importing already demultiplexed data
   
It's assumed that the input is raw paired-end MiSeq data in demultiplexed FASTQ format located within a folder called raw_data. The filenames are expected to look like this: 105CHE6WT_S325_L001_R1_001.fastq.gz, where each field separated by an _ character corresponds to: (1) the sample name, (2) the sample# on the run, (3) the lane number, (4) the read number (R1 = forward, R2 = reverse), and (5) the set number. You may need to re-name your files to match this format; however, QIIME2 accepts many different input formats so the format and naming scheme you're using may also be supported.

```
qiime tools import --type 'SampleData[PairedEndSequencesWithQuality]' --input-path raw_data --input-format CasavaOneEightSingleLanePerSampleDirFmt --output-path raw_data/demux-paired-end.qza    

qiime demux summarize --i-data raw_data/demux-paired-end.qza --o-visualization raw_data/demux-paired-end.qzv
```
### 2.2 transferring qzv files between server and your system

* open a new terminal in your laptop and write the following scp command to transfer files between server and laptop only for linux and Mac

change the xxx to match your id's

```
scp -r studentxx@sumo.biotech.wisc.edu:/home/BIOTECH/xxxxx/qiime2_tutorial/demux/demux-paired-end.qzv /xxx/xxxx/
```
Those on Windows using MobaXterm (listed above) will have a built-in GUI for transfers.

## Trim Primers
Cutadapt is used to remove reads that do not begin with the primers seqeunce and remove the primer sequence from the reads

```
qiime cutadapt trim-paired --i-demultiplexed-sequences raw_data/demux-paired-end.qza --p-cores 4 --p-front-f GTGYCAGCMGCCGCGGTAA --p-front-r GGACTACNVGGGTWTCTAAT --p-discard-untrimmed --p-no-indels --o-trimmed-sequences raw_data/trimmed_paired-end.qza
 
qiime demux summarize --i-data raw_data/trimmed_paired-end.qza --o-visualization raw_data/trimmed_paired-end.qzv
```

