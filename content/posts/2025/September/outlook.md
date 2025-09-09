+++
date = '2025-09-09T19:25:26Z'
draft = false
title = 'PowerShell script to auto genetare signatures- Part 1'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### The Mission

So I got a task from my team leader to make a new and better script to "Auto create signatures for Oultlook". 
I had to know some things before I start doing things, like understanding what I need to do to make it work.

### Old script

The old script is really a mess. The only comments are lines of code the writer didn't want.

#### The steps:
- Checking that there is an Outlook profile.
- Setting up the variables from Active Directory.
- Checking there is a directory '/APPDATA/Microsoft/Signatures'
- Checking the path to the template- .docx file.

#### Activation
- Open the template.docx file.
- Replace the variables in the template with the values from the Active Directory.
- Save it in the path 'APPDATAT/Microsoft/Signatures'

#### Conclusions

I didn't only read the script, I tried to mess up with Outlook a little bit.
I noticed that when I create a signature there are 3 files created: 
- .html 
- .rtf 
- .txt 

Also, to really use the signature you need to move it from 'signatures on this device' to the tab with your mail. And when you do move this, the files getting a name change plus another directory is created. 


