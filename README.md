# splunk-ingest-action
The purpose of this lab is to use Splunk Ingest Action on Heavy Forwarder to forward logs to both Splunk Enterprise and AWS S3. Note that ingest action can be configured either in heavy forwarder or indexer but preferably in heavy forwarder in order not to affect the performance of the indexer. This is tested on Splunk Enterprise version 9.0.5.

## Architecture

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/5b9ac2cb-41b5-408b-b56b-75a17d138cde)

In this lab, heavy forwarder and indexer have been setup up omitting the universal forwarder. The heavy forwarder has been configured to more monitor a log file stored locally. Heavy forwarder will then forward the data to both the Splunk Enterprise and AWS S3 bucket.

## Pre-requisites
1. Both heavy forwarder and indexer has already been setup
2. Heavy forwarder has been configured to monitor a log file locally

## Part 1 - Setup your AWS S3 bucket
Step 1: Navigate to AWS S3 Service and create a new bucket. Provide the bucket name and the rest of the field can be left as default.

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/0fd72bc9-4c5f-4128-b03f-217bfecc95c7)

Step 2: Click on the new bucket created. Create a folder within the bucket to store your incoming data

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/95771a85-cb9e-4acc-a6ed-08fd024e174c)

## Part 2 - Configure ingest action in heavy forwarder

Step 1: Navigate to the ingest action page

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/e140a227-f999-40f6-88bb-9371fe5ee385)

