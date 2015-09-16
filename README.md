# TargetSpecificGATKSequencingPipeline
This package takes FASTQ files and produces a filtered VCF file

# Setup 
* This has been tested on Ubuntu 14.04.3
* Since this is a pipeline that utilizes many other programs there are many dependencies. 
* I have tried to outline the steps to installing all the necessary dependencies from a fresh default Ubuntu 14.04.04 installation.

# Install dependencies

### git
```
sudo apt-get install git
```

### java
```
sudo apt-get install openjdk-6-jre
sudo apt-get install openjdk-6-jdk
```

### maven if you want to build source
```
sudo apt-get install maven
```

### If you are interested in read trimming
* cutadapt https://cutadapt.readthedocs.org/en/stable/installation.html
* install pip and cutadapt
 
 ```
sudo apt-get install build-essential
sudo apt-get install python-dev
sudo apt-get install python-dev
sudo apt-get install python-pip
pip install --user --upgrade cutadapt
or
sudo pip install --upgrade cutadapt
```

### If you are interested in aligning and variant calling

* GATK 
* https://www.broadinstitute.org/gatk/download/
* You will need an account to download it
```
# the name sequencing_programs does not matter
mkdir ~/sequencing_programs
cd ~/sequencing_programs
cp ~/Downloads/GenomeAnalysisTK-3.4-46.tar.bz2 .
tar -xvf GenomeAnalysisTK-3.4-46.tar.bz2
```

* htslib and samtools
```
sudo apt-get install libncurses5-dev
sudo apt-get install zlib1g-dev
cd ~/sequencing_programs
git clone https://github.com/samtools/htslib.git
cd htslib
make
cd ~/sequencing_programs
git clone git://github.com/samtools/samtools.git  
cd samtools
```

* bwa
```
cd ~/sequencing_programs
git clone https://github.com/lh3/bwa.git
make
```

* picard tools
```
cd ~/sequencing_programs
wget https://github.com/broadinstitute/picard/releases/download/1.139/picard-tools-1.139.zip
unzip picard-tools-1.139.zip
```

* qplot
```
cd ~/sequencing_programs
git clone https://github.com/statgen/libStatGen
cd libStatGen
make
cd ~/sequencing_programs
wget http://www.sph.umich.edu/csg/zhanxw/software/qplot/qplot-source.20130627.tar.gz
tar -xvf qplot-source.20130627.tar.gz
cd qplot
make
```

### If you want to use the svm filter
You will need to install R. Unfortunately, Ubuntu 14.04 has an old version of R so we have to install it a non-standard way
```
cd ~/
sudo apt-get install liblapack3
sudo apt-get install libgfortran3
sudo apt-get install libblas3
sudo apt-get install gfortran
wget http://cran.es.r-project.org/bin/linux/ubuntu/trusty/r-base-core_3.2.2-1trusty0_amd64.deb
sudo dpkg -i r-base-core_3.2.2-1trusty0_amd64.deb
sudp Rscript -e 'install.packages("ggplot2",repos="http://cran.wustl.edu/")' 
sudo Rscript -e 'install.packages("gridExtra",repos="http://cran.wustl.edu/")' 
sudo Rscript -e 'install.packages("e1071",repos="http://cran.wustl.edu/")' 
sudo Rscript -e 'source("https://bioconductor.org/biocLite.R"); biocLite("impute")' 
```

# Download Reference files
* note that there are about 20gb in reference files
```
mkdir ~/sequencing_reference_files
wget -c -r --no-parent --reject "index.html*" \
"http://glom.sph.umich.edu/TargetedSequencingPipelineReferences/" -nH --cut-dirs=1
```

# If you want to use Release version of TargetSpecificGATKSequencingPipeline then follow the steps below
```
cd ~/sequencing_programs
wget https://github.com/christopher-gillies/TargetSpecificGATKSequencingPipeline/raw/master/release/TargetSpecificGATKSequencingPipeline-0.1.jar
wget https://raw.githubusercontent.com/christopher-gillies/TargetSpecificGATKSequencingPipeline/master/example.ubuntu.application.properties
```

* Change the username from cgillies to YOUR_USERNAME in the example.ubuntu.application.properties file

```
cat ~/sequencing_programs/example.ubuntu.application.properties | \
perl -lane '$_ =~ s/cgillies/YOUR_USERNAME/; print $_' > \
~/sequencing_programs/ubuntu.application.properties
```

* Make sure the paths are still valid for the files in the ubuntu.application.properties. They should be hopefully be if you have followed this tutorial exactly.

* Test that the pipeline runs
```
export PIPELINE=~/sequencing_programs/TargetSpecificGATKSequencingPipeline-0.1.jar
export CONF=~/sequencing_programs/ubuntu.application.properties
java -jar $PIPELINE --conf $CONF --help
```
* The program successfully started up if the help menu is displayed


# If you wan to use source version of TargetSpecificGATKSequencingPipeline then follow the steps below
* Clone repository
```
export JAVA_HOME=/usr/lib/jvm/java-6-openjdk-amd64/
cd ~/sequencing_programs/
git clone https://github.com/christopher-gillies/TargetSpecificGATKSequencingPipeline.git
```
* Install maven dependencies in lib folder
```
mvn install:install-file -Dfile=./lib/picard-1.107.jar -DgroupId=net.sf.picard -DartifactId=picard -Dversion=1.107 -Dpackaging=jar

mvn install:install-file -Dfile=./lib/sam-1.107.jar -DgroupId=net.sf.samtools -DartifactId=sam -Dversion=1.107 -Dpackaging=jar

mvn install:install-file -Dfile=./lib/tribble-1.107.jar -DgroupId=org.broad.tribble \
    -DartifactId=tribble -Dversion=1.107 -Dpackaging=jar

mvn install:install-file -Dfile=./lib/VCFAnalysisTools-1.03.jar -DgroupId=org.kidneyomics \
    -DartifactId=VCFAnalysisTools -Dversion=1.03 -Dpackaging=jar
```
* Build the package
```
cd ~/sequencing_programs/TargetSpecificGATKSequencingPipeline/
mvn package
```
* Setup configureation
```
cat ~/sequencing_programs/TargetSpecificGATKSequencingPipeline/example.ubuntu.application.properties | \
perl -lane '$_ =~ s/cgillies/YOUR_USERNAME/; print $_' > \
~/sequencing_programs/TargetSpecificGATKSequencingPipeline/ubuntu.application.properties
```

* Test that the pipeline runs
```
export PIPELINE=~/sequencing_programs/TargetSpecificGATKSequencingPipeline/target/TargetSpecificGATKSequencingPipeline-0.1.jar
export CONF=~/sequencing_programs/TargetSpecificGATKSequencingPipeline/ubuntu.application.properties
java -jar $PIPELINE --conf $CONF --help
```
* The program successfully started up if the help menu is displayed


