# Static pipeline testing on Jenkins server running in AWS EC2

## Outline of this Project

The project showcases a creation and running an instance on AWS, configuring of Jenkins server, and create a pipeline to deploy a static website on S3.

### Steps to be performed autimatically using the CloudFormation stack

If desired, the same steps can be performed manually.

- Set up EC2 instance with Ubuntu AMI
- Set up SecurityGroups
- Install Jenkins On Ubuntu
- Set Up Jenkins
- Set up S3 Bucket
- Set up Bucket Policy
- Set up pipeline for AWS
- Add another stage in pipeline

Manual steps required:
- Install required plugins for Jenkins
- Connecting to a GitHub repo
- Set up AWS credentials in Jenkins

### AWS Steps
1) Log in to the AWS management console. Find and select the IAM (Identify and Access Management).

2) Create a new group, select "Group" from the left sidebar, name it "jenkins", and attach the following policies:

- AmazonEC2FullAccess
- AmazonVPCFullAccess
- AmazonS3FullAccess.
Hit Next Step, review, and then "Create Group."

3) Create a new user, select "Users" from the left sidebar, then "Add user," and use "jenkins" as the user name. Click on both "programmatic access" and "AWS management console access." The defaults for auto-generated password and "users must create a new password at next sign-in" are OK and should be kept. Hit "Next", and add the "jenkins" user to the "jenkins" group. Hit "next," no need to add "Tags." Review, and accept. Capture the Access Key, Secret Access Key, and the password so that you can log in as that user (easy to just download the csv file).

To sign in as this new IAM user, sign out of the AWS console, then use the following URL, where your_aws_account_id is your AWS account number without the hyphens (for example, if your AWS account number is 1234-5678-9012, your AWS account ID is 123456789012):

https://your_aws_account_id.signin.aws.amazon.com/console/

This is available in the 'Users' section of the sidebar, under the 'jenkins' user link, in the Security credentials tab, as a "Console sign-in link."

Upon signing in, go through the "renew your password" steps.

4) Create a new key pair for access to your instance(s).

5) Launch EC2 instance.

6) Create corresponding Security Group. Select "Security Groups." Under name, use: 'jenkins', description: "basic Jenkins security group," VPC should have the default one used. Click Add Rule: Custom TCP Rule, Protocol: TCP, Port Range 8080, Source 0.0.0.0/0 Then add the SSH rule: Protocol: SSH, Port range: 22, From source, use the dropdown and select "My IP."

7) Go back to instances, and right click the running instance, select Networking and change the security groups. Select the Jenkins security group that was created previously.

8) To connect to your instance using your key pair.

## Setting up your repository

Minimal setup of your repository should include:
 - static *.html file
 - Jenkinsfile
 
An example Jenkinsfile can look like the following:
```
pipeline {
  agent any 
  stages {
    stage(‘Build’) {
      steps {
        sh ‘echo “Hello World”’
        sh ‘“
                  echo “Multiline shell script works too”
                  ls -lah
               “‘
      }
    }
  }
}
```

## Setting up AWS resources

0. Create IAM user with permissions to create EC2 instances, S3 buckets and full access to VPC.

1. Deploy necessary resources in AWS with help of CloudFormation.

2. Setup Jenkins
  - Visit Jenkins on its default port, 8080, with your server IP address or domain name included like this: http://your_server_ip_or_domain:8080.
  Note! Jenkins server will be accessible only from your IP, and will not be public.
  - Next you will see the "Unlock Jenkins" screen, displaying the location of the initial password. In the terminal, use cat to show the password:
  
  `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
  
  - The next screen gives you the choice of installing recommended plugins, or selecting specific plugins - choose the Install suggested plugins option, which quickly begins the installation process.

 - When installation is complete, you are prompted to set up the first admin user. Create the admin user and make note of both the user and password to use in the future.

- You next see an Instance Configuration page, asking you to confirm the preferred URL for your Jenkins instance. Confirm the address, click save and finish.

3. Install necessary plugins: 
  - Blue Ocean 
  - AWS Steps

4. Click **Create Pipeline**

5. Link your Github repository using Developer token created from your Github account.

6. Setup AWS credentials in Jenkins server.
    - Clieck Credentials tab and select (global).
    - Choose "AWS Credentials" from the dropdown, add "aws-static" on ID, add a description like "Static HTML publisher in AWS," and fill in the AWS Key and Secret Access Key generated when the IAM role was created.
    
7. Use an example Jenkinsfile provided in this repo to push and deploy static file into S3 bucket.

8. Commit your changes and run the Jenkins pipeline.

8. The final successful Jenkins pipeline should look similar to this:

![Successfull Pipeline](/img/pipeline.png)
