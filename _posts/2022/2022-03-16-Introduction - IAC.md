---
layout: single
title: "Introduction -  IAC"
date: 2022-03-16
tags: 
  - Automation
  - PowerShell
  - IAC
  - Terraform
  - Packer
  - Vagrant
  - Vault
categories:
  - Iac
published: true
comments: true
author_profile: true
header:
  teaserlogo:
  teaser: ''
  image: img/headers/terraform-1500x500.jpg
  caption:
gallery:

  - image_path: ''
    url: ''
    title: ''
toc: true
toc_sticky: true
toc_label: "Table of content"
---

# 1.Introduction

Infrastructure as code (IaC) is a set of mechanisms to manage a virtual (computer) infrastructure through descriptor files or scripts (computer code).

In other words, this means that each element of the infrastructure (virtual machine, network interface, application server, database instance, etc.) can be described in a language and provisioned by a tool capable of interpreting its description.

IaC was built on the historical flaws of **SysOps**: the difficulty of knowing the history and the existing, tedious operations, the risks of human error and a poor ability to capitalize on what has already been done.

This brings several **advantages**:

* Automatic documentation: if the infrastructure is described by code, then this code is an exhaustive representation of the existing infrastructure, both quantitatively (e.g. number of disks attached to a VM) and qualitatively (e.g. size of disks).
Source version management: the other major advantage of using code is to be able to capitalize on all the years of good software practices in this field. Thus the code will be versioned, each change will be logged and the same verification processes as for application code can exist (code review before deployment, automated tests and code analysis by robots). In the event of a problem, rollback will be facilitated by the fact that a state of the infrastructure corresponds to a specific version of the code.
Automated and secure deployment: the creation of resources is faster and automated. Reduced human intervention reduces risk, resulting in a more consistent infrastructure.

* Code reusability: duplication of resources for the implementation of a different environment (e.g. going into production) can be done very quickly and securely since it involves copying code that has been validated beforehand. Moreover, this opens the door to easy scalability of resources.
Today, the "IaC" ecosystem is already large and includes well-known tools from the DevOps world such as Ansible, Vagrant, Puppet and therefore ... Terraform.

![IAC](/img/Iac.png){: .align-center}

# 2.Communication models and methods for IaC tools

There are mainly two models of communication between the IaC solution and its management targets:

* **Agentless** versus Agent-Based models
* **Push** versus **Pull model**

The first Agentless/Agent-Based criterion concerns the constraint of prior provisioning of targets with the installation of specific software (the agent). Often, as a consequence of the underlying protocols and mechanisms such as DNS, NTP and TLS are also prerequisites that can be difficult to control with respect to the management solution. One can then consider that the IaC will always depend on another infrastructure that is not necessarily managed in such an "agile" way.

The second Push/Pull criterion concerns the direction of communications between the IaC solution and its targets. A Push model "pushes" information to the targets, while a Pull model asks the targets of the management solution to fetch their parameters

# 3.Final Word

IaC solutions are therefore not competing solutions because they solve different phases of infrastructure deployment. It is therefore important to choose the tool that best meets our needs, even if it means combining several tools. The following table summarizes some of the elements of comparison between IaC tools


|Tool  |Type  |Working  |Agent  |
|---------|---------|---------|---------|
|Ansible     |  Push       |  Functional/ Procedural       |   Agentless      |
|Chef     |   Pull      |    Procedural     |    Agent-Based     |
|Puppet     |    Pull     |     Functional    |   Agent-Based      |
|Terraform     |     Push    |     Functional    |  Agentless       |
|SALTSTACK     |  Pull/Push       |    Functional/ Procedural     |  Agentless       |
|CFEngine     |   Pull      |    Functional     | Agent-Based        |

On the next part I will speak about Packer and how to use it.

Thank's for reading.🤗

Written by Jérôme Bezet-Torres @JM2K69.
