---
layout: post
title: "Using KUDU to deploy two applications on the same Azure WebSite"
date: 2015-05-11 09:04:40 +1000
comments: true
categories: [Azure , WebSites , Deployment , Kudu]
---

<strong>Problem </strong> : I have two web projects in the same solution and they need to be deployed in the same AzureWeb site
in two different virtual directories.
(<a href='http://blogs.msdn.com/b/tomholl/archive/2014/09/22/deploying-multiple-virtual-directories-to-a-single-azure-website.aspx'>and yes it is possible in case you haven't noticed </a>)

Below screen is a summary of my expected deployment structure.

{% img left /images/using-kudu-to-deploy-two-applications-on-the-same-azure-website/1.jpg %}

Takign a step back, there are basically two built-in ways do continuous deployments deployments on Azure Websites.

    1. Use VSO Hosted Build Agent
    2. Use Kudu deployments

Dabid Ebbo explains the difference between the two <a href='https://github.com/projectkudu/kudu/wiki/VSO-vs-Kudu-deployments'>here</a>

The biggest and the most important difference is , Kudu can only be used to do deployments from your local box while
- to state the obvious - VSO build is coupled with your source repo in VSO.

But why am I even bothered about kudu while VSO hosted build is free ( well, 60 mins per month ) and is completely isolated from
the local dev box and have all the bells and whistles built in ( everyone loves XAML , don't they) ?

Well the limitation of VSO build is when it comes to deploy two applications on the same Azure Website ,
in two virtual directories. (  )

At the moment ( May 2015 ) deploying two applications in virtual directories is not supported in in VSO.
( <a href='source/_posts/2015-05-11-using-kudu-to-deploy-two-applications-on-the-same-azure-website.markdown:26'> you have to have two solutions for both applications instead </a> )

Fortunately, there is <a href='https://github.com/projectkudu/kudu'>kudu</a>. The awesome engine behind git/hg deployment in Azure websites.

Not only you can creating deployment scripts fairly straight-forward, they can be modified and tested locally easily ( ha! , take that VSO build )

I'm listing the steps that were followed in achieving the first stated objective using kudu deployments.


NOTE : You'd first want to have <a href='http://azure.microsoft.com/en-in/documentation/articles/xplat-cli/'>Azure CLI tools</a> installed to run the following script.


1. Generate the kudu deployment script for Web Project

{% codeblock %}
azure site deploymentscript --aspWAP VeryComplexApplication\VeryComplexApplication.Web\VeryComplexApplication.Web.csproj -s VeryComplexApplication.sln
{% endcodeblock %}

2. Rename the generated deployment script

{% codeblock %}
move deploy.cmd VeryComplexApplication.Web.deploy.cmd
{% endcodeblock %}

3. Generate the kudu deployment script for API Project

{% codeblock %}
azure site deploymentscript --aspWAP VeryComplexApplication\VeryComplexApplication.API\VeryComplexApplication.API.csproj -s VeryComplexApplication.sln
{% endcodeblock %}

4. Rename the generated deployment script

{% codeblock %}
move deploy.cmd VeryComplexApplication.API.deploy.cmd
{% endcodeblock %}

5. Create a new deploy.cmd file and add the following code block
 ( which will execute deployment scripts for web site and api separately )

{% codeblock %}

    @echo off

    echo deploying the web site....

    call VeryComplexApplication.Web.deploy.cmd

    echo deploying the API ...

    call VeryComplexApplication.API.deploy.cmd

{% endcodeblock %}

6. Log into Azure portal and on the respective website , go to configure tab





