+++
date = '2025-06-21T22:50:51Z'
draft = false
title = 'Moving from AWS to Cloudflare'
author = 'Moriel Mauni'
tags = ["AWS", "CloudFlare"]
categories = ["AWS", "CloudFlare"] 
description = "How did I moved my domain from AWS to CloudFlare"
+++


### Intro 
My protfolio website is hosted on AWS with S3 bucket to store the files, AWS Route 53 to manage the domain, and AWS Amplify to host the app (as they said, serverless is the best). But, it's getting a little bit pricey for static website, so I want to move it to be managed by CloudFlare.

### TL;DR  
1. In Route 53 checking that the "Transfer Lock" is set to off 
2. Getting the code from "Transfer out" -> "Transfer to another registrar"
3. Registrar the AWS domain in CloudFlare 
4. Adding the CloudFlare nameservers in AWS Route 53 
5. Delete the old name servers
6. Waiting to CloudFlare to active the domain 
7. Deleting all AWS services except Route 53 


### AWS 

It's really easy to be honest.
Need to check that "**Transfer Lock**" is set to **Off** and changes the Name Servers to CloudFlare's ones and deleting the AWS ones.
The CloudFlare name servers:
```
andy.ns.cloudflare.com
karina.ns.cloudflare.com
```

Under "**Transfer out**" --> "**Transfer to another registrar**" get the code and save it.


### CloudFlare
Under **Domain Registration** there is an option to **Transfer Domains**
**NOTE**: it's cost around 10$ to move it because they want you to buy another year to the domain.

![Domain Transfer](/images/domain-1.png)

Now, this is the step when we need the code: 

![Domain Transfer-2](/images/domain-2.png)

The next 2 images just shows that everything is done and it's re-registrar the domain 

![Domain Transfer-3](/images/domain-3.png)

I'm for now waiting to the transfer to happen. I will update :-) 

![Domain Transfer-4](/images/domain-4.png)



