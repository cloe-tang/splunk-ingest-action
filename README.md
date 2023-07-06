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

## Part 2 - Configure ingest action in heavy forwarder forwarding to S3

Step 1: Navigate to the ingest action page

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/e140a227-f999-40f6-88bb-9371fe5ee385)

Step 2: Click on "Destinations" to configure the S3 enpoint. Click on "New Destination" and provide the required information.

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/bcfb9915-0388-467e-aefd-82ba0135a694)

Step 3: Choose your authentication method. There are 2 options: "Access key and secret key" and "IAM Role". In this case, I'm using Access key and secret key. If the access key and secret key provided is correct, you should see "Successfully connected to the server" upon clicking on "Test Connection". If you are using IAM role, please refer to the Additional Information section for the permission required.

<img width="516" alt="image" src="https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/e655301d-2622-4402-9bb7-6a01ec7ff153">

Step 4: Under "Advanced Options", make sure to configure according to your S3 bucket setting. 

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/6ff2946a-7915-44d0-b912-d7aca77f95ef)

Step 5: Ingest Action sends data in batches based on either the size of the data or the time whichever that comes first. The default setting for batch timeout is 30 seconds and batch size threshold is 2MB. This setting can changed under the "Advanced Options".

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/b40bf137-9108-4acd-85a7-e9b66e6deced)

Step 6: Once configuration is completed, click on "Save". 

Step 7: Click on "Rulesets" then "New Ruleset".

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/cf855140-e093-4eca-8484-1f2b54ff79bb)

Step 8: Give the Ruleset a name. Under Event Stream, select the sourcetype which you want to send to S3.

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/3421d452-0702-4b4b-be5c-fa51a9311ba0)

Step 9: Click on "+ Add Rule". Select "Route to Destination"

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/c839ebb4-4375-4f11-8b64-aa963e12b895)

Step 10: By default, you will see "Default Destination" added. That is the splunk indexer. If you remove it, the data will not be forwarded to splunk indexer. Hence for any sourcetype you intercept in ingest action, you will need to include "Default Destination" if you want to forward to splunk indexer as well. 

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/00180a56-714c-41b0-9a39-dd8e10cd9cdb)

Step 11: Add the S3 destination in. Once done, click on "Save"

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/35f97da3-bab9-4f58-af00-ed923574f83a)

Step 12: You will see the rule set added. 

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/700da7c0-ad4f-4bed-b065-4ac9c8a8efb7)

## Verify result

Step 1: Navigate to your S3 bucket. You should see a json file created in the following directory. Note that in version 9.1, you can choose to have sourcetype name as part of the directory. 

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/8a788877-2a30-4a1a-8e6f-7965c8523378)

In version 9.0, the following is how the S3 partition looks like

<img width="536" alt="image" src="https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/3ed2af73-0623-4c3a-836a-a4560cdb85cb">

In version 9.1, you have the option to partition with the sourcetype

<img width="794" alt="image" src="https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/b5b078e7-36b8-41f4-9985-cb0ffaf48d73">

The filename for both version will be as follows:

```
events_{LT}_{ET}_{file_create_epoch}_{32b_seq_num}_{peer_guid}.{format_ext}{.compression_ext}
```

Step 2: Download and open the json file from S3. The Json file should look something like the following for version 9.0

<img width="1036" alt="image" src="https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/dd9f0794-1699-43de-b7fe-e5397f102f81">

In version 9.1, you have the option to save it in raw, JSON or New line delimited json

<img width="982" alt="image" src="https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/954a91d8-183d-40d9-9484-b801f57c4160">

Step 3: Verify that the data is also going into Splunk Indexer. 

## Masking IP for data going to S3. Data going to Splunk Enterprise unmask

Step 1: Edit the rule_cyberark ruleset. Remove the AWS S3 under Route to Destination

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/1297cfd9-a70c-4ed5-93b6-a4b500613912)

Step 2: Click on "+ Add Rule". Select "Mask with regular expression"

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/cd87b166-3ee6-4bc2-aad3-7e1a3e7b5604)

Step 3: Enter the regex for IP address and the replace expression

```
([01]?\d\d?|2[0-4]\d|25[0-5])(?:\.(?:[01]?\d\d?|2[0-4]\d|25[0-5])){3}(?:/[0-2]\d|/3[0-2])?
```
![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/0b7dc447-ebf5-4e60-aa86-318320ed7756)

Step 4: Click on "+ Add Rule" and select "Route to Destination". In this rule, remove "Default destination" and add "AWS S3"

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/ec73be57-8e40-40bd-9b03-74fe6525a827)

Step 5: There should be 4 rules in total. Click on "Save"

** Note that you need to have 2 "Route to Destination" rule. One to Splunk Enterprise with IP unmasked and the other to S3 with IP masked. If you do not have the first "Route to Destination" set to Default Destination, data will not be going to Splunk Enterpise. 

## Verify Result

Step 1: Pump some new data with IP address into the logs you are monitoring

Step 2: Verify in the indexer. The IP address should not be masked

<img width="949" alt="image" src="https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/938c8a3b-c846-48ea-9abe-487b908dbac1">

Step 3: Verify in the S3. The IP address should be masked

![image](https://github.com/cloe-tang/splunk-ingest-action/assets/58005106/190c7b0f-0c42-4c06-8c99-9952b6030a92)

## Additional Information

1. If you are using IAM role as your authentication method for S3, make sure that you have the following permission
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::BucketNameHere",
                "arn:aws:s3:::BucketNameHere/*"
            ]
        }
    ]
}
```
2. If you have transformation configured in the heavy forwarder, the ingest action happens after the transform.
3. Once sourcetype is intercepted, you have to specify all destination you would like the route the data to including the Default Destination. 
