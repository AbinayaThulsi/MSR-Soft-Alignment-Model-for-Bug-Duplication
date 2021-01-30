# MSR-Soft-Alignment-Model-for-Bug-Duplication
This is a reproduction project of paper - "A Soft Alignment Model for Bug Deduplication". The paper aims at the state-of-art development of a novel soft alignment method which helps to identify the distinct bug reports for the same bug registered by multiple users. A novel deep learning model named SABD is implemented to achieve this. Further, the results are evaluated based on the rank-based metric.

##  Metadata 

A reproduction of a following Mining Software Repository(MSR) research paper as part of the MSR course 2020/21 at UniKo, CS department, SoftLang Team: "A Soft Alignment Model for Bug Deduplication"[1][2] by Irving Muller Rodrigues, Daniel Aloise, Eraldo Rezende Fernandes and Michel Dagenais. The Code[4] & Dataset[3] were licensed by research paper authors.This project is licensed under MIT.

## Requirements 

- Hardware
    - Minimum Requirements:  
        - **OS**: Windows / Linux
        - **Processor**: Intel Core i5-3570K 3.4GHz / AMD FX-8310
        - **Memory**: 4 GB RAM
        - **Storage**: 70 GB Hard drive space

    - **Recommended Requirements**:  
        - **OS**: Windows / Linux
        - **Processor**:  Intel Core i7-4790 4-Core 3.6GHz / AMD Ryzen 3 3200G
        - **Memory**: 8/16 GB RAM
        - **Storage**: 70 GB **SSD** space

- Software
    - Python, Pytorch, nltk, scipy, ujson, pytorch ignite 0.1.2, sacred, MongoDB



## DATA

## Install
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


## Data fetch
The sample of the data is present in data/HOME_DIR folder. The complete data can be found [here](https://zenodo.org/record/3922012#.YBUloehKhnI) .

But the data can also be fetched by the below process:

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


## Data Preprocessing
```bash    
# Eclipse
python3 data/create_dataset_our_methodology.py --database eclipse --collection initial --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --training  DATASET_DIR/eclipse_2001-2007_2008/training_split_eclipse.txt --validation  DATASET_DIR/eclipse_2001-2007_2008/validation_eclipse.txt --test DATASET_DIR/eclipse_2001-2007_2008/test_eclipse.txt --date="2008/01/01" --date_threshold="2008/12/31" --no_tree --dev_perc=0.05

python3 data/clean_data.py --bug_dataset DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --output DATASET_DIR/eclipse_2001-2007_2008/eclipse_soft_clean_rm_punc_sent_tok.txt.json --fields short_desc description --type soft --rm_punc --sent_tok --lower_case

# example to generate pairs and triplets
python data/generate_pairs_triplets.py --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --dataset DATASET_DIR/eclipse_2001-2007_2008/validation_eclipse.txt --n 1 --type random

python data/generate_pairs_triplets.py --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --dataset DATASET_DIR/eclipse_2001-2007_2008/test_eclipse.txt --n 1 --type random

python data/generate_pairs_triplets.py --bug_data DATASET_DIR/eclipse_2001-2007_2008/eclipse_initial.json --dataset DATASET_DIR/eclipse_2001-2007_2008/training_eclipse.txt --n 1 --type random

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


    
## Usage

In order to train SABD, a json have to be created with the argument values of SABD. 

Run python script experiments/sabd.py to perform the experiments.
```bash
# Examples
python3 experiments/sabd.py -F HOME_DIR/experiments with HOME_DIR/duplicate-bug-report/json_parameters/sabd_eclipse_test.json "recall_rate.window=365"
python3 experiments/sabd.py -F HOME_DIR/experiments with HOME_DIR/duplicate-bug-report/json_parameters/sabd_open_office_test.json "recall_rate.window=365"
python3 experiments/sabd.py -F HOME_DIR/experiments with HOME_DIR/duplicate-bug-report/json_parameters/sabd_netbeans_test.json "recall_rate.window=365"
python3 experiments/sabd.py -F HOME_DIR/experiments with HOME_DIR/duplicate-bug-report/json_parameters/sabd_mozilla_test.json "recall_rate.window=365"
```


## Delta

# Process Delta: 
```bash
1. We were able to fetch the data using the commands provided with few minor changes in the code(e.g.Changing the path). 
2. Data preprocessing also worked fine (cleaning data, creating pairs etc). 
3. During analysis sabd.py program had to be modified to run for the CPU instead of GPU. As we did not 
have the version of GPU needed to run the code for the dataset provided.
```
# Data Delta:
```bash
1. When we ran the code with CPU instead if GPU, the code ran for about 20 hours without any others as the 
size of dataset was huge. So we manually took the sample of data for one of the open source (eclipse) and 
preprocessed data using clean_data, generate_pairs_triplets, generate_categorical_lexicon. 
2. We ran the sabd.py code for 1 year and 3 years window and we got the final output. i.e, ranking rate 
for k 1 to 20.
3. Since we could only run for sample data we could not compare the results with other open sources or with 
other methodologies. 
4. The code article_results.ipynb gives the comparison for all open sources and other methodologies. These 
ranking rates were fetched by the author of the paper. All the ranking rates were fetched as a list in 
article_results.ipynb code. As mentioned in 3rd point we were not able to compare because of the GPU constraint.
```
## Problems Faced
```bash
1. During data fetch phrase we did not face much issues, MongoDB was required and a few minor changes had 
to be done. Data preprocessing went smoothly.
2. The major issue was while running sabd.py which took us a few days to figure out the exact issue which 
was related to compatibility and version. We were getting GPU issue (Error: No HIP GPU available) which we 
resolved by downloading drivers. But, the versions were not matching so NVIDIA failed to communicate with 
the driver (Error : No Nvidia driver in your system).
3. To overcome GPU issue, we tried to run the code with only CPU. But the code was running for epoch:1 for 
about 20 hours. 
4. We tried running for 10000 records but after 1 hour the code was still at epoch 1.
5. So we decided to run the code by taking around 500-600 records and were able to receive recall-rate 
within 10 mins. The sample data we have provided for eclipse has around 600 records. 
```

## References

1. [DBLP](https://dblp.org/rec/conf/msr/RodriguesAFD20.html?view=bibtex)
2. https://2020.msrconf.org/details/msr-2020-papers/31/A-Soft-Alignment-Model-for-Bug-Deduplication
3. [Dataset](https://zenodo.org/record/3922012#.YBUloehKhnI)
4. [Code](https://github.com/irving-muller/soft_alignment_model_bug_deduplication)
