---
layout: post
title: "Continuous Deployment of two applications to the same Azure WebSite (using kudu)"
date: 2015-05-11 09:04:40 +1000
comments: true
categories: [Azure , WebSites , Deployment , Kudu]
---

<strong>Problem </strong> : I have two web projects in the same solution and they need to be deployed in the same AzureWeb site
in two different virtual directories.
(<a href='http://blogs.msdn.com/b/tomholl/archive/2014/09/22/deploying-multiple-virtual-directories-to-a-single-azure-website.aspx'>and yes it is possible in case you haven't noticed </a>)
<a href='http://blog.amitapple.com/post/38419111245/azurewebsitecustomdeploymentpart3'>This post</a> explains well, how you
could achieve this manually. The concern specifically addressed here is deploying both applications continuously.

Below screen is a summary of my expected deployment structure.

{% img left /images/using-kudu-to-deploy-two-applications-on-the-same-azure-website/1.jpg %}

Taking a step back, there are basically two built-in ways do continuous deployments deployments on Azure Websites.

    1. Use VSO Hosted Build Agent
    2. Use Kudu deployments

<a href='https://github.com/projectkudu/kudu/wiki/VSO-vs-Kudu-deployments'>Dabid Ebbo explains the difference between the two here </a>

The biggest and the most important difference is , <u>Kudu can only be used to do deployments from your local box</u> while
- to state the obvious - VSO build is coupled with your source repo in VSO.

But why am I even bothered about kudu while VSO hosted build is free ( well, 60 mins per month ) and is completely isolated from
the local dev box and have all the bells and whistles built in ( everyone loves XAML , right )?

Well the limitation of VSO build comes when you want to automate the deployments to two virtual directories in the same
website.

At the moment ( May 2015 ) deploying two applications in virtual directories is not supported in in VSO builds.
(<a href='source/_posts/2015-05-11-using-kudu-to-deploy-two-applications-on-the-same-azure-website.markdown:26'>you have to have two solutions for both applications instead</a> )

Fortunately, there is <a href='https://github.com/projectkudu/kudu'>kudu</a>. The awesome engine behind git/hg deployment in Azure websites.

Not only you can creation of deployment scripts is fairly straight-forward, they can be modified and tested  with ease
( ha! , take that VSO build )

I'm listing the steps that were followed in achieving the first stated objective using kudu deployments.


NOTE : You'd first want to have <a href='http://azure.microsoft.com/en-in/documentation/articles/xplat-cli/'>Azure CLI tools</a> installed to run the following script.


> Generate the kudu deployment script for Web Project

{% codeblock %}
azure site deploymentscript --aspWAP VeryComplexApplication\VeryComplexApplication.Web\VeryComplexApplication.Web.csproj -s VeryComplexApplication.sln
{% endcodeblock %}

> Rename the generated deployment script

{% codeblock %}
move deploy.cmd VeryComplexApplication.Web.deploy.cmd
{% endcodeblock %}

Then find the block with DEPLOYMENT_TARGET and change it as follows ( note the if condition is commented,
because we want to override what is set by kudu by default)

{% codeblock %}
:: IF NOT DEFINED DEPLOYMENT_TARGET (
  SET DEPLOYMENT_TARGET=%ARTIFACTS%\website
:: )
{% endcodeblock %}

> Generate the kudu deployment script for API Project

{% codeblock %}
azure site deploymentscript --aspWAP VeryComplexApplication\VeryComplexApplication.API\VeryComplexApplication.API.csproj -s VeryComplexApplication.sln
{% endcodeblock %}

Same as above, change the DEPLOYMENT_TARGET section as follows,

{% codeblock %}
:: IF NOT DEFINED DEPLOYMENT_TARGET (
  SET DEPLOYMENT_TARGET=%ARTIFACTS%\api
:: )
{% endcodeblock %}

> Rename the generated deployment script

{% codeblock %}
move deploy.cmd VeryComplexApplication.API.deploy.cmd
{% endcodeblock %}

> Create a new deploy.cmd file and add the following code block
 ( which will execute deployment scripts for web site and api separately )

{% codeblock %}

    @echo off

    echo deploying the web site....

    call VeryComplexApplication.Web.deploy.cmd

    echo deploying the API ...

    call VeryComplexApplication.API.deploy.cmd

{% endcodeblock %}

> Configure Virtual applications in Azure

Log into Azure portal and on the respective website , go to configure tab and configure the virtual directories as
seen in the above image.

> Push the code to the Azure web site by using Local Git Repository option.

Commit the newly added *.deploy.cmd files at the root and push the code to Azure website using 'Local Git Repo' option.

{% img left /images/using-kudu-to-deploy-two-applications-on-the-same-azure-website/2.png %}


Happy deployments !



