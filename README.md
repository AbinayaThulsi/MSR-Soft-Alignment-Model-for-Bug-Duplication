# MSR-Soft-Alignment-Model-for-Bug-Duplication
This is a reproduction project of paper - "A Soft Alignment Model for Bug Deduplication". The paper aims at the state-of-art development of a novel soft alignment method which helps to identify the distinct bug reports for the same bug registered by multiple users. A novel deep learning model named SABD is implemented to achieve this. Further, the results are evaluated based on the rank-based metric.

##  Metadata 

A reproduction of a following Mining Software Repository(MSR) research paper as part of the MSR course 2020/21 at UniKo, CS department, SoftLang Team:  

"A Soft Alignment Model for Bug Deduplication"[1] by Irving Muller Rodrigues, Daniel Aloise, Eraldo Rezende Fernandes and Michel Dagenais.

The Code[4] & Dataset[3] were licensed by research paper authors.

This project is licensed under MIT.

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
    - [Python 3.x](https://www.python.org/downloads/)
    - Gzip & wget(recommended) / curl
        - For Windows OS use [Git For Windows](https://git-scm.com/download/win)
    - [PostgreSQL 13](https://www.postgresql.org/download/) - [Direct Download](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)


## Process

- Steps:
    - Please Check [INSTALLATION.md](doc/INSTALLATION.md)

## Data

- Input data:
    - Software Heritage Graph Dataset - **[Popular 4k](data/popular-4k)**
    - NIST NVD
- Temporary data:
    - CVE related problems
- Output data:
    - CVE (Common Vulnerabilities and Exposures)
    - CWE (Common Weakness Enumeration)
- Schema:
    - [Relational schema](https://docs.softwareheritage.org/devel/swh-dataset/graph/schema.html)


## Delta

- Process delta:

- Data delta:

## References

1. [DBLP](https://dblp.org/rec/conf/msr/RodriguesAFD20.html?view=bibtex)
2. https://2020.msrconf.org/details/msr-2020-mining-challenge/3/Exploring-the-Security-Awareness-of-the-Python-and-JavaScript-Open-Source-Communities
3. [Dataset](https://zenodo.org/record/3922012#.YBUloehKhnI)
4. [Code](https://github.com/irving-muller/soft_alignment_model_bug_deduplication)
