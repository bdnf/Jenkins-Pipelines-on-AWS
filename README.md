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

## How to start

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
