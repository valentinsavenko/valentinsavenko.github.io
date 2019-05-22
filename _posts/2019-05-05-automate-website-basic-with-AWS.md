---
layout: post
last-modified: '2019-05-05T01:00:00-04:00'

title: "Automate website basics with AWS Cloudformation"
subtitle: "certificates, reverse proxy and e-mails for free, with AWS"
image:
    feature: aws-cloudfront-email/cloud-143152_1920.jpg
    credit: lmaresz
    creditlink: https://pixabay.com/users/lmaresz-23602/
---


## The idea: certificates, reverse proxy and e-mails for free, with AWS
The point here is to get a proper blog site for free, not because we're cheap but just for the fun of it!
We'll also learn to use a lot of very distinct AWS services in the process and show a good piece of engineering work which correlates nicely with our (mission statement)[lol-what-replace-me].
And while we're on it, we also like to use the domain we bought to get very posh email-addresses like `ceo@codingdevops.com`, because we can.
Although AWS has no IMAP/POP3 and we have to use Lambdas to redirect mail to another mailbox. Still, it's free and easy (enough)!

On AWS _free_ is relative, for a private website with not that much traffic you'll probably never run against the free-usage threshold of the services we use. You should still try and set up limits or alerts. If some bot decides to DDoS your website, or spam your new shiny e-mail you could be paying for extra usage of Cloudfront, e-mail service or Lambda executions!

<!--more-->
## 1. Setting up SSL-certificates with the AWS Certificate Manager (ACM)

> The automated-certification part is completely based on Mark van Holsteijns [blog post](https://binx.io/blog/2018/10/05/automated-provisioning-of-acm-certificates-using-route53-in-cloudformation/) and [repo](https://github.com/binxio/cfn-certificate-provider)

Unfortunately Cloudformation Templates (CFTs) don't support completely automated SSL Certificates and I had to find some customised code to enable us to provision everything in one step. [_Binx.io_](https://binx.io/) has a repo and [blog post](https://binx.io/blog/2018/10/05/automated-provisioning-of-acm-certificates-using-route53-in-cloudformation/) solving that problem in a very elaborate way. 
They basically implement Custom Resources for Cloudformation and use those inside of their CFTs.

They have 3 parts for this:
1. Python code that actually executes the steps and is used by a Lambda function to check for the DNS-Entries that need to be setup for [ACME challenge](https://en.wikipedia.org/wiki/Automated_Certificate_Management_Environment) of the SSL Certificate.
2. A Template that creates those Lambda functions and establishes them as Custom Resources to be called by Cloudformation.
3. The Cloudformation Template that is using those Custom Resources, to create and verify the SSL Certs for your domain.

Step 1 could potentially be skipped, because [_Binx.io_](https://binx.io/) are providing their [public S3 Bucket](https://s3.console.aws.amazon.com/s3/buckets/binxio-public-us-east-1/lambdas) with already uploaded Python zips. But I don't like using binaries that weren't compiled by me and we only need to use this for 1 Region.

After making sure your [AWS CLI is setup](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) we can pull the code, switch to the `cfn-certificate-provider` directory, create our creatively named S3 Bucket in the correct region and push the build zip-file up there. 
The _only correct region for us here is `us-east-1`_, because in later steps Cloudfront is only able to use the Certificates that are in this region. All others can't be selected!
```sh
git clone git@github.com:binxio/cfn-certificate-provider.git
cd cfn-certificate-provider

aws s3 mb s3://own-binxio-bucket-us-east-1 --region us-east-1

make S3_BUCKET_PREFIX=own-binxio-bucket AWS_REGION=us-east-1 deploy
```
Find your [newly created bucket here](https://s3.console.aws.amazon.com/s3/buckets/own-binxio-bucket-us-east-1/lambdas/) when everything worked fine.

```sh
aws cloudformation create-stack \
    --region us-east-1 \
    --capabilities CAPABILITY_IAM \
    --stack-name cfn-certificate-provider \
    --parameters ParameterKey=CFNCustomProviderZipFileName,ParameterValue=lambdas/cfn-certificate-provider-latest.zip \
        ParameterKey=S3BucketPrefix,ParameterValue=own-binxio-bucket \
    --template-body file://cloudformation/cfn-resource-provider.yaml

aws cloudformation wait stack-create-complete --region us-east-1 --stack-name cfn-certificate-provider
```

```sh
# DOMAIN_NAME='example.com'
DOMAIN_NAME='codingdevops.com'

# HOSTED_ZONE_ID='<no-idea-hash-value>' # check on route53 for this value to your domain
HOSTED_ZONE_ID='Z2ZPCJHPDV0FI'

aws cloudformation create-stack --stack-name cfn-certificate-provider-demo \
    --region us-east-1 \
    --template-body file://cloudformation/demo-stack.yaml \
    --parameters ParameterKey=DomainName,ParameterValue=$DOMAIN_NAME \
        ParameterKey=HostedZoneId,ParameterValue=$HOSTED_ZONE_ID
aws cloudformation wait stack-create-complete --region us-east-1 --stack-name cfn-certificate-provider-demo
```


## 2. Caching and reverse proxy with AWS Cloudfront

## 3. Domain-specific e-mails with AWS Lambda, Simple Notification Service (SNS) and Simple Email Service (SES)