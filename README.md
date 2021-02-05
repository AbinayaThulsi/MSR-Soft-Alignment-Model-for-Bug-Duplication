# MSR-Soft-Alignment-Model-for-Bug-Duplication
This is a reproduction project of paper - "A Soft Alignment Model for Bug Deduplication". The paper aims at the state-of-art development of a novel soft alignment method which helps to identify the distinct bug reports for the same bug registered by multiple users. A novel deep learning model named SABD is implemented to achieve this. Further, the results are evaluated based on the rank-based metric.

#  Metadata 

A reproduction as part of the MSR course at MSR couse 2020/21 at UniKo, CS department, SoftLang Team.

**Paper Title**: "A Soft Alignment Model for Bug Deduplication" by the authors Irving Muller Rodrigues, Daniel Aloise, Eraldo Rezende Fernandes and Michel Dagenais.

**DBLP Link:** https://dblp.org/rec/conf/msr/RodriguesAFD20.html?view=bibtex (https://2020.msrconf.org/details/msr-2020-papers/31/A-Soft-Alignment-Model-for-Bug-Deduplication)

The [Code](https://github.com/irving-muller/soft_alignment_model_bug_deduplication) & [Dataset](https://zenodo.org/record/3922012#.YBUloehKhnI) of the paper can be found here.

# Requirements 

- Hardware
    - Minimum Requirements:  
        - **OS**: Windows / Linux
        - **Processor**: Intel Core i5-3570K 3.4GHz
        - **Memory**: 4 GB RAM
        - **Storage**: 70 GB Hard drive space

    - **Recommended Requirements**:  
        - **OS**: Windows / Linux
        - **Processor**:  Intel Core i7-4790 4-Core 3.6GHz
        - **Memory**: 8/16 GB RAM
        - **Storage**: 70 GB **SSD** space
        - **GPU** : NVIDIA GPUs G8x version onwards

- Software
    - Python, Pytorch, nltk, scipy, ujson, pytorch ignite 0.1.2, sacred, MongoDB


# Process

## Step 1: Package Installation
Install the following packages:

```bash
#CPU
conda install pytorch torchvision cpuonly -c pytorch 
#GPU
conda install pytorch torchvision cudatoolkit=10.1 -c pytorch

conda install -c anaconda nltk
conda install -c anaconda scipy
conda install -c anaconda ujson
conda install -c pytorch ignite=0.4.2
pip install sacred 
conda install ignite -c pytorch
```
Otherwise, run the below command to install all the Packages.
```bash
pip install -r requirements.txt
```

## Step 2: Data Extraction
The complete data can be found [here](https://zenodo.org/record/3922012#.YBUloehKhnI) .

The data can also be fetched by the below process:

The data used in the paper can be found [here](https://zenodo.org/record/3922012). One folder contain the word embeddings and four remaining ones 
contain the dataset of the open-sources projects (Eclipse, Netbeans, Open Office and Firefox).
This data came from the [Lazar's work](https://dl.acm.org/doi/abs/10.1145/2597073.2597128  ).
The original dataset is available [here](http://alazar.people.ysu.edu/msr14data/ ).

```bash
 wget http://alazar.people.ysu.edu/msr14data/datasets/eclipse.tar.gz
 tar zxvf eclipse.tar.gz
 mongorestore
 mongo eclipse
 show collections
 db.initial.count()
 
 wget http://alazar.people.ysu.edu/msr14data/datasets/mozilla.tar.gz
 tar zxvf mozilla.tar.gz
 mongorestore
 mongo mozilla
 show collections
 
 
 wget http://alazar.people.ysu.edu/msr14data/datasets/openOffice.tar.gz
 tar zxvf openOffice.tar.gz
 mongorestore
 mongo openOffice
 show collections
 
 wget http://alazar.people.ysu.edu/msr14data/datasets/netBeans.tar.gz
 tar zxvf netBeans.tar.gz
 mongorestore
 mongo netBeans
 show collections
``` 

*The commands below generates the dataset of each open-sources projects:*
> Note: Lazar's dataset has to be dumped into mongo before running these commands


## Step 3: Data Preprocessing

> Note: The code has been completely automated for **Ecliple**. After running the command under eclipse, we can directly run the SABD code under **Usage**.

```bash    
# Eclipse
python3 data/create_dataset_our_methodology.py --database eclipse --collection initial --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --training  DATASET_DIR/eclipse_2001-2007_2008/training_split_eclipse.txt --validation  DATASET_DIR/eclipse_2001-2007_2008/validation_eclipse.txt --test DATASET_DIR/eclipse_2001-2007_2008/test_eclipse.txt --date="2008/01/01" --date_threshold="2008/12/31" --no_tree --dev_perc=0.05

python3 data/clean_data.py --bug_dataset DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --output DATASET_DIR/eclipse_2001-2007_2008/eclipse_soft_clean_rm_punc_sent_tok.txt.json --fields short_desc description --type soft --rm_punc --sent_tok --lower_case

# example to generate pairs and triplets
python data/generate_pairs_triplets.py --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --dataset DATASET_DIR/eclipse_2001-2007_2008/validation_eclipse.txt --n 1 --type random

python data/generate_pairs_triplets.py --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --dataset DATASET_DIR/eclipse_2001-2007_2008/test_eclipse.txt --n 1 --type random

python data/generate_pairs_triplets.py --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --dataset DATASET_DIR/eclipse_2001-2007_2008/training_split_eclipse.txt --n 1 --type random

# example to create categorical_lexicons
python data/generate_categorical_lexicon.py --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json -o DATASET_DIR/dataset/sun_2011/eclipse_2001-2007_2008/categorical_lexicons.json


# Mozilla
python3 data/create_dataset_our_methodology.py --database mozilla --collection initial --bug_data DATASET_DIR/mozilla_2001-2009_2010/mozilla_initial.json --training DATASET_DIR/mozilla_2001-2009_2010/training_split_mozilla.txt --validation  DATASET_DIR/mozilla_2001-2009_2010/validation_mozilla.txt --test DATASET_DIR/mozilla_2001-2009_2010/test_mozilla.txt --date="2008/01/01" --date_threshold="2008/12/31" --no_tree --dev_perc=0.05

python3 data/clean_data.py --bug_dataset DATASET_DIR/mozilla_2001-2009_2010/mozilla_initial.json --output DATASET_DIR/mozilla_2001-2009_2010/mozilla_soft_clean_rm_punc_sent_tok.txt.json --fields short_desc description --type soft --rm_punc --sent_tok --lower_case

# Netbeans
python3 data/create_dataset_our_methodology.py --database netBeans --collection netall --bug_data DATASET_DIR/netbeans_2001-2007_2008/netbeans_initial.json --training  DATASET_DIR/netbeans_2001-2007_2008/training_split_netbeans.txt --validation  DATASET_DIR/netbeans_2001-2007_2008/validation_netbeans.txt --test DATASET_DIR/netbeans_2001-2007_2008/test_netbeans.txt --date="2008/01/01" --date_threshold="2008/12/31" --no_tree --dev_perc=0.05

python3 data/clean_data.py --bug_dataset DATASET_DIR/netbeans_2001-2007_2008/netbeans_initial.json --output DATASET_DIR/netbeans_2001-2007_2008/netbeans_soft_clean_rm_punc_sent_tok.txt.json --fields short_desc description --type soft --rm_punc --sent_tok --lower_case


# OpenOffice
python3 data/create_dataset_our_methodology.py --database openOffice --collection ooall --bug_data DATASET_DIR/open_office_2001-2008_2010/open_office_initial.json --training  DATASET_DIR/open_office_2001-2008_2010/training_split_open_office.txt --validation  DATASET_DIR/open_office_2001-2008_2010/validation_open_office.txt --test DATASET_DIR/open_office_2001-2008_2010/test_open_office.txt --date="2008/01/01" --date_threshold="2010/12/31" --no_tree --dev_perc=0.05

python3 data/clean_data.py --bug_dataset DATASET_DIR/open_office_2001-2008_2010/open_office_initial.json --output DATASET_DIR/open_office_2001-2008_2010/open_office_soft_clean_rm_punc_sent_tok.txt.json --fields short_desc description --type soft --rm_punc --sent_tok --lower_case
```
   
# Usage

In order to train SABD, a json have to be created with the argument values of SABD. 

Run python script datas/sabd.py to perform the experiments.
> Note: The embedding data thats is available [here](https://zenodo.org/record/3922012) should be pasted in HOME_DIR folder.

```bash
# Examples
python3 data/sabd.py -F HOME_DIR/experiments with HOME_DIR/duplicate-bug-report/json_parameters/sabd_eclipse_test.json "recall_rate.window=365"
python3 data/sabd.py -F HOME_DIR/experiments with HOME_DIR/duplicate-bug-report/json_parameters/sabd_open_office_test.json "recall_rate.window=365"
python3 data/sabd.py -F HOME_DIR/experiments with HOME_DIR/duplicate-bug-report/json_parameters/sabd_netbeans_test.json "recall_rate.window=365"
python3 data/sabd.py -F HOME_DIR/experiments with HOME_DIR/duplicate-bug-report/json_parameters/sabd_mozilla_test.json "recall_rate.window=365"
```
# Data

## Input Data
- The Bug report is collected from four open source projects(Eclipse, Mozilla, NetBeans and OpenOffice). 
- Soft Alignment Model for Bug Deduplication (SABD) model receives the bug reports as the input which is composed of the categorical fields, a summary and a description. 
- We get the input data from the data extraction phrase. The sample data is present [here](https://github.com/AbinayaThulsi/MSR-Soft-Alignment-Model-for-Bug-Duplication/tree/main/data/HOME_DIR/dataset/sun_2011/eclipse_2001-2007_2008).

## Output Data
- Soft Alignment Model for Bug Deduplication (SABD) model outputs the probability of the bug report being a duplicate and indicates whether the given reports are duplicate. 
- The output is evaluated using ranking-based metrics and recall rate (RR@k). This is been calculated for each open source at k= 1 to 20, where k is the list of bug reports recommended by the triager. Therefore, with the help of Time Window for a period of 1 to 3 years the data is being compared.
- The output of our sample data can be found [here](https://github.com/AbinayaThulsi/MSR-Soft-Alignment-Model-for-Bug-Duplication/tree/main/process/Ouput_Sample_data).

# Results

The attached screenshot is thefinal results which we have achieved.
Since we had to use a small data of about 600 records the recall rate becomes 1 from k=3 onwards. So, we ran only for the Eclipse as it would be the same case for other open sources because of data constraint.

![](Image/recall_rate.PNG)

# Delta

## Process Delta 
- We were able to fetch the data using the commands provided with few minor changes in the code(e.g.Changing the path). The data was fetched to mongodb and then to local system.
- Data preprocessing also worked fine (cleaning data, creating pairs etc). 
- The method was tested with a time window of one year and three years to measure how its size affects performance.
- During analysis sabd.py program had to be modified to run for the CPU instead of GPU. As we did not have the version of GPU needed to run the code for the dataset provided.

## Data Delta
- When we ran the code with CPU instead of GPU, the code ran for about 20 hours without any outputs as the size of dataset was huge. So we manually took the sample of data for one of the open source (eclipse) and preprocessed data using clean_data, generate_pairs_triplets, generate_categorical_lexicon. 
- We ran the sabd.py code for 1 year and 3 years window and we got the final output. i.e, ranking rate for k 1 to 20.
- Since we could only run for sample data we could not compare the results with other open sources or with other methodologies. 
- The code article_results.ipynb gives the comparison for all open sources and other methodologies. These ranking rates were fetched by the author of the paper. All the ranking rates were fetched as a list in article_results.ipynb code. As mentioned in 3rd point we were not able to compare because of the GPU constraint.

# Problems Faced
- During data fetch phrase we did not face much issues, MongoDB was required and a few minor changes had to be done. Data preprocessing went smoothly.
- The major issue was while running sabd.py which took us a few days to figure out the exact issue which was related to compatibility and version. We were getting GPU issue (Error: No HIP GPU available) which we resolved by downloading drivers. But, the versions were not matching so NVIDIA failed to communicate with the driver (Error : No Nvidia driver in your system).
- To overcome GPU issue, we tried to run the code with only CPU. But the code was running for epoch:1 for about 20 hours. 
- We tried running for 10000 records but after 1 hour the code was still at epoch 1.
- So we decided to run the code by taking around 500-600 records and were able to receive recall-rate within 10 mins. The sample data we have provided for eclipse has around 600 records. 
