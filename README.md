
This workshop will cover amplicon-based microbiome analysis using [QIIME2](https://qiime2.org). 
This four-day workshop will consist of hands on training to analyze from raw dataset through publication-quality statistics and visualizations.

There are more detailed descriptions and tutorials on the [QIIME2 website](https://docs.qiime2.org/2019.1/tutorials/).

## Get Started
### Natively installing QIIME2 on your own machine


>**Note:** Make sure that your machine is powerful enough for the data set you wish to run. What we are doing today is a very, very small data set, which should not pose any issues on a resonably new standard laptop. However, if you were to run a full data set on a low-end personal computer, you would likely run out of memory and crash it. So make sure that whatever machine you use is up to the task before you start.


The latest version of Qiime2 can be found [here](https://docs.qiime2.org/2023.9/install/#qiime-2-2023-9-distributions)).   

If you have a Mac or Linux system -  You can install using Miniconda.   

Other options for everyother OS including windows OS is   
[WSL](https://docs.qiime2.org/2023.2/install/virtual/wsl/),  
[Docker](https://docs.qiime2.org/2023.2/install/virtual/docker/) and  
[Amazon Web Services](https://docs.qiime2.org/2019.1/install/virtual/aws/) 

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
/home/BIOTECH/student01
```

If at any point you get lost within the labyrinth of directories, simply type `cd` to come back.

If you would like to back up by a single directory, type `cd ..`

To know where your terminal is “looking” at any moment, you can invoke the print working directory command: `pwd`
