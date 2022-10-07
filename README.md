# Athena-DBVisualizer
This README will demonstrate how to setup an AWS Athena Database in DBVisualizer.

Prerequisites:
- AWS Account with an Athena DB setup on it (Athena database should have a S3 bucket setup for query results)
- DBVisualizer (https://www.dbvis.com/)

---
## Download the AWS Athena JDBC
You need to download the AWS Athena JDBC42 jar file from https://docs.aws.amazon.com/athena/latest/ug/connect-with-jdbc.html
Download the JDBC42 driver **with** AWS SDK.
If you download the JDBC driver without AWS SDK, you'll encounter errors like: `java.lang.NoClassDefFoundError: com/amazonaws/auth/AWSCredentials`.

Once downloaded, you should move the jar file into your dbvis/jdbc folder. On Mac, this folder is located at `/Users/<Your name>/.dbvis/jdbc/`

## Setting up the JDBC driver in DBVisualizer
- Open DBVis
- On the Menu Bar, open the Tools dropdown, click "Driver Manager"
- The driver manager panel should open.
- Click the '+' sign to create a new driver
- Double click 'Custom'
- Fill the fields as follows:
![image](https://user-images.githubusercontent.com/32295800/194339159-2199e18b-ce0d-489a-8789-22c123b1c1c3.png)
- On the right hand side, click the '+' sign and click 'Add Files...'
- Navigate to the jdbc folder mentioned earlier and select your Athena JDBC .jar file & click Open
- Close the driver manager panel

## Creating the connection
- Click the 'Create Connection' button and select the Athena connector that was just created

![image](https://user-images.githubusercontent.com/32295800/194368996-428f318a-693f-455b-945e-4758de8643a2.png)
- If the 'Database URL' row doesn't appear under the Database section, then switch the Database Type to something else and back to Athena.

### IAM Credentials

If you're wanting to just use your AWS IAM Credentials and only connect to athena dbs from a single account:

- In the Database Userid field, put your IAM Access Key
- In the Database Password field, put your IAM Secret Key
- In the Database URL field, put `jdbc:awsathena://AwsRegion=us-east-1;` switching `us-east-1` with your region.
- Switch to the Properties tab
- In the value cell of the 'S3OutputBucket' row, you want to put the URI of the query result s3 bucket mentioned earlier.
- In the value cell of the 'AwsCredentialsProviderClass', you want to put `com.simba.athena.amazonaws.auth.DefaultAWSCredentialsProviderChain`
- You should now be able to connect to the database.

### Profile based
If you have multiple AWS profiles under one account E.g. 'prod' & 'dev':
- In the Database URL field, put `jdbc:awsathena://AwsRegion=us-east-1;Profile=prod` switching `us-east-1` with your region and switching `prod` with your profile found in the .aws/credentials file.
- Switch to the Properties tab
- In the Value cell of the 'S3OutputBucket' row, you want to put the URI of the query result s3 bucket mentioned earlier.
- You should now be able to connect to the database.

---

## Common issues
#### ATHENA_CLIENT. Can't find bundle for base name com.simba.athena.athena.jdbc42.messages, locale ...
This error is a bit of red herring. The error occurs because the locales bundle was missed when the Athena jars were built.
However this error only occurs because it can't display the other error you're encountering. 
This could be a misconfiguration or a misspelt SQL statement. To properly debug the issue you'll have to setup additional logging in your Database URL.
E.g.
`jdbc:awsathena://AwsRegion=us-east-1;LogLevel=3;LogPath=/Users/<Your name>/dbvislogs;`
This database url change will output log files into the dbvislogs directory which will allow you to figure out what's really going on.

---  
## Helpful links
[AWS Support Forums: Output Bucket Error](https://aws.amazon.com/premiumsupport/knowledge-center/athena-output-bucket-error/)
  
[Simba Athena JDBC Docs](https://s3.amazonaws.com/athena-downloads/drivers/JDBC/SimbaAthenaJDBC-2.0.32.1000/docs/Simba+Amazon+Athena+JDBC+Connector+Install+and+Configuration+Guide.pdf)
