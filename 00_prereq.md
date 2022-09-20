# 00_prereq

Note: These steps are in the process of being automated. TBC on when to remove these sections

In order to test the Airlines use\-case demo flow, you must collect the setup the below and/or collect details from Cloudera:

1. Set workload password on CDP One management console. Follow the documentation here: https://docs.cloudera.com/cdp-one/saas/cdp-one-accessing-clusters/topics/cdp-one-set-workload-password.html
2. Create home directory on the private S3 bucket
    1. Go to the CDP One console
    2. Under the "Command Line" section, click on "SSH for Gateway"
    3. Copy\-paste the command to a terminal and enter your workload password that you setup above.
    4. Run the below command to create your home directory

```
hdfs dfs -mkdir s3a://<cdp-private-s3-bucket>/user/<enter-your-cdp-username>
```
