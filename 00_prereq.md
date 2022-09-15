# 00_prereq

In order to test the Airlines use\-case demo flow, you must collect the setup the below and/or collect details from Cloudera:

1. Set workload password on CDP One management console. Follow the documentation 
2. Collect the name of the region your demo instance has been deployed to
3. Collect the name of your CDP One, private & public S3 buckets. They should be of the form:
    1. `s3a://*private*`
    2. `s3a://*public*`
4. Create home directory on S3
    1. Go to the CDP One console
    2. Under the "Command Line" section, click on "SSH for Gateway"
    3. Copy\-paste the 
    4. You can run the below command to create your home directory

```
hdfs dfs -mkdir /user/<enter-your-cdp-username>
```
