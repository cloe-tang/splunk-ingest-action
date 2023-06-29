# splunk-ingest-action
The purpose of this lab is to use Splunk Ingest Action on Heavy Forwarder to forward logs to both Splunk Enterprise and AWS S3. Note that ingest action can be configured either in heavy forwarder or indexer but preferably in heavy forwarder in order not to affect the performance of the indexer. 

## Architecture

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/5b9ac2cb-41b5-408b-b56b-75a17d138cde)

In this lab, heavy forwarder and indexer have been setup up omitting the universal forwarder. The heavy forwarder has been configured to more monitor a log file stored locally. 
