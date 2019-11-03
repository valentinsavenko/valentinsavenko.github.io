---
layout: post
last-modified: '2019-11-05T01:00:00-04:00'

title: "Automate website basics with AWS & Terraform"
subtitle: "certificates, reverse proxy and e-mails for free, with AWS"
image:
    feature: aws-cloudfront-email/cloud-143152_1920.jpg
    credit: lmaresz
    creditlink: https://pixabay.com/users/lmaresz-23602/
---


## The idea: certificates, reverse proxy and e-mails for free, with AWS

> The general idea is completely based on David Gurevich [blog post](https://medium.com/@davidgurevich_11928/cloudfront-as-a-reverse-proxy-fb6a8dcbed96)

The point here is to get a proper blog site for free just for the fun of it!
We'll also learn to use a lot of very distinct AWS services in the process and show a good piece of engineering work which correlates nicely with our (mission statement)[lol-what-replace-me].
And while we're on it, we also like to use the domain we bought to get very posh email-addresses like `ceo@codingdevops.com`, because we can.
Although AWS has no IMAP/POP3 and we have to use Lambdas to redirect mail to another mailbox. Still, it's free and easy (enough)!

On AWS _free_ is relative, for a private website with not that much traffic you'll probably never run against the free-usage threshold of the services we use. You should still try and set up limits or alerts. If some bot decides to DDoS your website, or spam your new shiny e-mail you could be paying for extra usage of Cloudfront, e-mail service or Lambda executions!

<!--more-->
## 1. Setting up SSL-certificates with the AWS Certificate Manager (ACM)
 
The _only correct region for us here is `us-east-1`_, because in later steps Cloudfront is only able to use the Certificates that are in this region. All others can't be selected!
```
```

## 2. Caching and reverse proxy with AWS Cloudfront

## 3. Domain-specific e-mails with AWS Lambda, Simple Notification Service (SNS) and Simple Email Service (SES)