---
layout: post
title: "Customising VSO build template to execute an arbitrary exe"
date: 2015-05-09 10:37:41 +1000
comments: true
categories: ALM , VSO , BUILD , Continuous Integration
---

I had to work with VSO and Azure WebSites more than usual during the last few days. A bulk of it involved moving
on prem code base to VSO and setting up CI/CD process with VSO build.

One particular scenario I had to tackle was to carryout database patching using
<a href='http://dbup.github.io' >DbUp</a> after the build/deployment is complete. The DBUp upgrade happens in the
form of running an executable generated during the build process.
The default templates does not have any built-in option to run an arbitrary exe as part of the build process.

The VSO build templates, however, can be customized to achieve pretty much anything you would expect to happen in a typical
deployment process - contrary to the common belief that hosted builds are limited to only the default provided options.

Here is a list of items I had do to get the above achieved :

<h3>Step 1 : Download the default build process template.</h3>

Navigate to Team Explorer -> Builds -> Edit Build Definition
In the Process tab, download the default selected build process template.

{% img left /images/customising-vso-build-template-to-execute-an-exe/1.png %}

Rename the template and add to the source structure ( e.g in my case I added it under /Builds/CustomTemplates/CustomTemplate1.xaml)
As you can see build process template is a .xaml file so you can open it in Visual Studio to modify.

<h3>Step 2 : Add a InvokeProcess task to the template</h3>

Select the InvokeProcess task and add in the appropriate location in the template ( in this case I have decided to update the
database as the last step of the process - but it could well be before tests are executed)

{% img left /images/customising-vso-build-template-to-execute-an-exe/2.png %}

Add a debug message ( WriteBuildMessage) in the task, so that you can see in the log that the process is triggered.
<h3>Step 3 : Add an input argument</h3>

At the bottom of the xaml designer view, you could find the 'Arguments' tab. The parameter that you expect to be received form the
'build definition' should be specified here.

{% img left /images/customising-vso-build-template-to-execute-an-exe/2-1.png %}

In this case I'm specifying a argument of type 'BuildParameter' which allows me to specify a collection of items ( name of the exe, input argumets )
But it could also be a simple string or int.

<h3>Step 4 : Specify the process to invoke and the place to look for arguments</h3>
Back in the 'InvokeProcess' task, select 'Properties' to specify the process to execute and where to look for input arguments.

{% img left /images/customising-vso-build-template-to-execute-an-exe/3.png %}

In the above case I'm asking the task to look at the Input Arguments specified in step 3.

Since my executable is part of the build process itself, I need to specify the location MSBUILD has created it in ( which is $TF_BUILD_BINARIESDIRECTORY)
You can find the complete list of build environment variables in <a href='https://msdn.microsoft.com/en-au/library/hh850448.aspx'>this MSDN article</a>

<h3>Step 5 : Change the build configuration to use the newly created template</h3>

Check in the modified template and in the build definition select New to specify the location for the new template.

{% img left /images/customising-vso-build-template-to-execute-an-exe/4.png %}

Give the relative path of your template file under 'path in repo'

<h3>Step 6 : Update the build configuration with the required parameters</h3>

When the new template is selected in the build definition, you should see the newly specified 'Arguments' in the 'Misc' section
at the bottom.
{% img left /images/customising-vso-build-template-to-execute-an-exe/5.png %}

Update the exe name and specify the input arguments and queue a new build. Queue a new build check the log to see if the
debug message from the InvokeProcess is present. Happy coding!



