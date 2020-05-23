# Big Data: A Survey

## Abstract

background and related techs,ie.,cloud computing, loT, data centers, hadoop

4 phases of value chain of big data: data generation, data acquisition, data storage and data anasysis.

representative applications of big data: enterprise management, loT, online social network, medial applications, collective intelligence, smart grid.

## background

1. Dawn of big data era

    In consideration of the heterogeneity, scalability, realtime, complexity, and privacy of big data, we shall effectively “mine” the datasets at different levels during the analysis, modeling, visualization, and forecasting, so as to reveal its intrinsic property and improve the decision making.

2. Definition and features of big data

    .......announced Big Data as the next frontier for innovation, competition, and productivity. B

    a 3Vs model, i.e., the increase of Volume, Velocity, and Variety, 

3. value of big data

    “big data technologies describe a new generation of technologies and architectures, designed to economically extract value from very large volumes of a wide variety of data, by enabling the high-velocity capture, discovery, and/or analysis

    Google found that during the spreading of influenza, entries frequently sought at its search engines would be different from those at ordinary times, and the use frequencies of the entries were correlated to the influenza spreading in both time and location.

4. development

    database->parallel database system->Google MapReduce

5. challenges

    For solutions of permanent storage and management of large-scale disordered datasets, distributed file systems and NoSQL databases are good choices.

    some challenges:
    data representation
    redundancy
    ......

## related techs

1. relationship between cloud computing and big data

    The main objective of cloud computing is to use huge computing and storage resources under concentrated management, so as to provide big data applications with fine-grained computing capacity. 

    the concepts are different to a certain extent. Cloud computing transforms the IT architecture while big data influences business decision-making. However, big data depends on cloud computing as the fundamental infrastructure for smooth operation.

2. relationship between Hadoop and bigdata

    According to the study, the authors concluded that the loose coupling will be increasingly applied to research on electron cloud, and the parallel programming technology (MapReduce) framework may provide the user an interface with more convenient services and reduce unnecessary costs.

## big data generation and aquisition

through the exploitation of accumulated big data, useful information such as habits and hobbies of users can be identified.

big data acquisition includes data collection, data transmission, and data pre-processing.

data transportaion讲的是网络知识

Nowadays, the internal connection networks of most data centers are fat-tree, two-layer or three-layer structures based on multi-commodity network flows

 Some relational data pre-processing techniques are discussed as follows:
integration
cleaning
redundancy elimination

## bigdata storage

On one hand, the storage infrastructure needs to provide information storage service with reliable storage space; on the other hand, it must provide a powerful access interface for query and analysis of a large amount of data.

1. storage system for massive data

    Existing massive storage technologies can be classified as Direct Attached Storage (DAS) and network storage,

2. distributed storage system

    factors considered:
    consistency
    availability
    partition tolerance

    a distributed system could not simultaneously meet the requirements on consistency, availability, and partition tolerance.

3. storage mechanism for big data

    Existing storage mechanisms of big data may be classified into three bottom-up levels: (i) file systems, (ii) databases, and (iii) programming models.

    distributed file systems have been relatively mature after years of development and business operation.

    **database**

    NoSQL databases (i.e., non traditional relational databases) are becoming more popular for big data storage.

    other key-value storage systems include Redis

    The column-oriented databases are mainly inspired by Google’s BigTable. HBase is column-oriented database

    three important representatives of document storage systems, i.e., MongoDB, SimpleDB, and CouchDB.

    some proposed parallel programming models effectively improve the performance of NoSQL and reduce the performance gap to relational databases. Therefore, these models have become the cornerstone for the analysis of massive data: MapReduce

4. Traditional data analysis

    Data analysis is the final and the most important phase in the value chain of big data, with the purpose of extracting useful values, providing suggestions or decisions.

    Data analysis plays a huge guidance role in making development plans for a country, understanding customer demands for commerce, and predicting market trend for enterprises.

     many traditional data analysis methods may still be utilized for big data analysis:
    Cluster
    Factor Analysis
    Correlation Analysis
    Regression Analysis
    Data Mining Algorithms(ie.SVM, k-means, Naive Bayes.....)
    ......

5. Big data Analytic methods

    the main processing methods of big data:
    Parallel Computing
    ......

    Although the parallel computing systems or tools, such as MapReduce or Dryad, are useful for big data analysis, they are low levels tools that are hard to learn and use. Therefore, some high-level parallel programming tools or languages are being developed based on these systems. 

6. Architecture for big data analysis

    **real-time analysis and offline analysis**

    Real-time analysis: is mainly used in E-commerce and finance. 

    Offline analysis: is usually used for applications without high requirements on response time, e.g., machine learning, statistical analysis, and recommendation algorithms. 

    offline analysis architecture based on Hadoop:  Facebook’s open source tool Scribe, LinkedIn’s open source tool Kafka.

    **Analysis at different levels**

    Memory-level analysis： Memory-level analysis is extremely suitable for real-time analysis. MongoDB is a representative memory-level analytical architecture.

    Massive analysis: most massive analysis utilize HDFS of Hadoop to store data and use MapReduce for data analysis.

## Big data applications

1. structured data analysis

    emerging field:  statistical machine learning based on exact mathematical models and powerful algorithms have been applied to anomaly detection; time and space mining; , privacy protection data mining; , process mining.

2. Text data analysis

    text analysis is deemed to feature more business-based potential than structured data.

    Most text mining systems are based on text expressions and natural language processing (NLP), with more emphasis on the latter. NLP allows computers to analyze, interpret, and even generate text.

3. Web data analysis (related to NLP)

4. Multimedia data  analysis

5. Network data analysis



