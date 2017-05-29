# trec-dd-jig

##### This is the readme file of  the user simulator (jig) package for the TREC 2016-2017 Dynamic Domain (DD) Track.
##### The package is for research use only.

##### If you have any technical question, please send your request to [google group](https://groups.google.com/forum/#!forum/trec-dd).

**************************************************************************
### **_Changes_ in TREC2017 DD Jig**

* **_Python 3.5 environment_**
* **_New metrics_**
* **_Additional supporting file for evaluation_**

**************************************************************************
### What's inside the package:

* the jig (`jig/jig.py`)
* evaluation metric scripts
* sample files

**************************************************************************

### Requirements

#### 1. System requirements
- Operation Systems: Mac OS, Windows, Linux.
- Python 3.5 environment.


#### 2. Download the TREC DD Topics:

- Download the Topics (with ground truth) from [NIST](http://trec.nist.gov/act_part/tracks17.html).

#### 3. Obtain TREC DD Datasets:

- Obtain the TREC DD datasets following the instructions [here](http://trec-dd.org/dataset.html).
- You will need to obtain the [New York Times dataset](https://catalog.ldc.upenn.edu/ldc2008t19)
 from [LDC](https://www.ldc.upenn.edu/).


### Install the Jig

#### 1. Download the jig package:

  ``` shell
  > git clone https://github.com/trec-dd/trec-dd-jig
  ```

#### 2. Quick install Python packages needed:

  ``` shell
  > cd trec-dd-jig
  > pip3 install -r requirements.txt
  ```
        

#### 3. Setup the jig
- Create your own directory to hold your trec-dd search system.


  ``` shell
  > mkdir your_dd_directory
  ```

- Put the jig package under your_dd_directory, that is,


  ``` shell
  > mv trec-dd-jig your_dd_directory/.
  ```

- Unzip and put the topic (with ground truth) file that you downloaded from NIST, under `~/your_dd_directory/trec-dd-jig/jig/topics/`
 
  
  ``` shell
  > gunzip dd_topic_file.xml.gz
  > mv dd_topic_file.xml your_dd_directory/trec-dd-jig/topics/
  ```

- Uncompress and preprocess the datasets. In this jig package, we release sample files from both the Ebola and the NYT datasets. The following codes are for the NYT dataset. 
They can be run on both the sample files and the actual dataset. Remember to replace the input .tgz file with the actual dataset. The resulting corpus will be in TRECTEXT format. 


  ```shell
  > cd trec-dd-jig
  > ./config/process_nyt.sh sample_doc/nyt_sample.tgz sample_doc/nyt_sample
  ```
  The first parameter is the `.tgz` file that you obtained from LDC, the second parameter is the output directory. 
  
  The output directory will contain the following subdirectories:
      
      - nyt_corpus: a directory that contains the uncompressed New York Times dataset 
      - nyt_trectext: a directoty that contains the processed text in TRECTEXT format
  
  
 
- Setup database, compute document length


  ``` shell
  > python3 config/setup.py --topics sample_run/topic.xml --trecdirec sample_doc/ebola_sample sample_doc/nyt_sample/nyt_trectext --doc-len sample_doc/mini_doc_len
  ```
    + where:
        - topics: the topic xml file you download from NIST
        - trecdirec: the directory that holds the Ebola and New York Times dataset in TRECTEXT format
        - doc-len: the parameter file containing each document's length, which will be used in evaluation 
  
  
  This script will set up a sqlite database at `./trec-dd-jig/jig/truth.db`. It will also generate a parameter file 
  storing the document lengths which will be used later in the metric calculation.  The parameter file is located at `sample_doc/mini_doc_len`
  
  Remeber to replace the file paths with the actual datasets. 
  
  
  Congratulations to a successful installation of the jig!

 

**************************************************************************

### Run the Jig
- Your systems should call `python3 jig/jig.py` to get feedback for each search iteration. The jig outputs a json dumped string. It provides feedback to your returned documents. Use the following command to call the Jig:


  ``` shell
    > python3 jig/jig.py -runid my_runid -topic topic_id -docs docno1:rankingscore docno2:rankingscore docno3:rankingscore docno4:rankingscore docno5:rankingscore
  ```

    + where:

        - my_runid: An identifier used to declare the run
        - topic_id: the id of the topic you are working on
        - docno1, docno2 ...: the five document ids that your system returned. It needs to be the document ids in TREC DD datasets.
        - ranking score: the ranking score of the documents generated by your system

-  Suppose your run id is 'testrun'. Call the jig with the topic id, the run id, the ids of the 5 documents that your system retrieved together with their ranking scores:


    ``` shell
        > python3 jig/jig.py -runid testrun -topic DD16-1 -docs ebola-45b78e7ce50b94276a8d46cfe23e0abbcbed606a2841d1ac6e44e263eaf94a93:833.00 ebola-012d04f7dc6af9d1d712df833abc67cd1395c8fe53f3fcfa7082ac4e5614eac6:500.00 ebola-20d0e40dbc6f60c35f581f4025076a4400bd46fa01fa0bc8164de05b286e04f8:123.00 ebola-da1e8bbbb0c543df9be4d59b37558927ed5ee4238c861ce1205ed7b41140ad3a:34.00 ebola-9e501dddd03039fff5c2465896d39fd6913fd8476f23416373a88bc0f32e793c:5.00
    ```

- The jig will print the feedback on the screen. Each feedback is a json dumped string.

  
  ``` shell
    [
     {
         "topic_id": "DD15-1"
         "ranking_score": "833",
         "on_topic": "1",
         "doc_id": "1322120460-d6783cba6ad386f4444dcc2679637e0b",
         "subtopics": [
             {
                 "passage_text": "Federal judge Redden taking himself off the salmon case",
                 "rating": 3,
                 "subtopic_id": "DD15-1.1",
             },

             { ... }
         ],
     }
     { ... }
    ]
  ```
    + where:
        - doc_id: the id of a document
        - subtopic_id: the id of a relevant subtopic that the document covers
        - passage_text: the content of a relevant passage that the document covers
        - rating: the graded relevance judgments provided by NIST assessors. less than 2: marginally relevant, 2: relevant, 3: highly relevant, 4: key results. The relevance grades refer to the relevance level of the passage to a subtopic.
        - ranking score: the ranking score of a document provided by your system

- A run file will be automatically generated at the current directory with the runid as its name. This will be the run file that you submit to NIST later.

  
  ``` shell
    > cat ./testrun.txt

    DD15-1 1322120460-d6783cba6ad386f4444dcc2679637e0b 833.00 1 DD15-1.1:3|DD15-1.4:2|DD15-1.4:2|DD15-1.4:2|DD15-1.4:2|DD15-1.4:2|DD15-1.2:2|DD15-1.2:2

    DD15-1 1322509200-f67659162ce908cc510881a6b6eabc8b 500.00 1 DD15-1.1:3

    DD15-1 1321860780-f9c69177db43b0f810ce03c822576c5c 123.00 1 DD15-1.1:3

    DD15-1 1327908780-d9ad76f0947e2acd79cba3acd5f449f7 34.00 1 DD15-1.3:2|DD15-1.1:2

    DD15-1 1321379940-4227a3d1f425b32f9f8595739ef2b8c3 5.00 0
  ``` 


    + where:
        - topic id: the id of the topic you are working on
        - docid: the id of a document
        - ranking score: the ranking score of a document provided by your system
        - on topic: 0 means the document is off topic, 1 means on topic
        - subtopic_id: the id of a relevant subtopic that your document covers
        - rating: the relevance grade provided by NIST assessors. -1/0/1: marginally relevant (Note that: ratings -1 or 0 or 1 all mean marginally relevant), 2: relevant, 3: highly relevant, 4: key results. The relevance grades refer to the relevance level of your document to the whole topic.

- Note that subtopic_ids are global ids, i.e., a certain topic might contain subtopic with id 12, 45, 101, 103...
- Everytime when you run `jig/jig.py` for the same topic id, the run file will be automatically appended and the number of interaction iterations will increase by one. 


**************************************************************************


### Metrics 
- We support a few metrics. The scripts for these metrics can be found at the `./scorer` directory.
- In 2017, the Track mainly uses three metrics.  They are Session DCG (sDCG),
Cube Test and Expected Utility. 
- We will provide both raw scores and normalized scores for your run. 
- You will need the actual topic xml file from NIST and the parameter file generated during installation to evaluate your runs. 
- Here we demonstrate how to use the scorers using a sample topic xml file, a sample pickle file and a sample run file. All the files can be found at the `./sample_run/` directory.
    + runfile: a sample run file
    + topic.xml: a sample topic xml file
    + doc_len: a sample file that holds the information of document length 
- In the real evaluation, please use files of **_REAL_** datasets instead of sample files we provided in `sample_run` or `sample_doc`
- How to run the scorers
    
    + The following arguments need to be specified to run the scoring scripts
        
        - `runfile`: the file generated by jig that records your run
        - `topics`: the ground truth file that downloaded from NIST
        - `doc-len`: the parameter file generated in installation
        - `cutoff`: the number of iterations taken into evaluation, eg. `--cutoff 5` means only the first 5 iterations of every topic 
        are taken into evaluation.
        
    + Session DCG (sDCG)
    
      Syntax
      ``` shell
        >$ python3 scorer/sDCG.py --runfile your_runfile --topics your_topic.xml --doc-len your_doc_len --cutoff your_cut_off_value
      ```
       
      To run the example
      ```shell
        >$ python3 scorer/sDCG.py --runfile sample_run/runfile --topics sample_run/topic.xml --doc-len sample_run/doc_len --cutoff 5
      ``` 
     
    + Cube Test
    
      Syntax
      ``` shell
        >$ python3 scorer/cubetest.py --runfile your_runfile --topics your_topic.xml --doc-len your_doc_len --cutoff your_cut_off_value
      ```
      To run the example
      ```shell
        >$ python3 scorer/cubetest.py --runfile sample_run/runfile --topics sample_run/topic.xml --doc-len sample_run/doc_len --cutoff 5
      ```
      
    + Expected Utility
      
      Syntax
      ``` shell
        >$ python3 scorer/expected_utility.py --runfile your_runfile --topics your_topic.xml --doc-len your_doc_len --cutoff your_cut_off_value
      ```
      To run the example
      ```shell
        >$ python3 scorer/expected_utility.py --runfile sample_run/runfile --topics sample_run/topic.xml --doc-len sample_run/doc_len --cutoff 5
      ```

    
    