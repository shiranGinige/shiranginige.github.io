---
layout: post
title: "Continous Deployment Azure Website using TeamCity in few easy steps"
date: 2015-06-19 07:56:39 +1000
comments: true
categories: [ALM , TeamCity ,Azure]
---

If you are already using TeamCity and wanna do CD to Azure websites without breaking a sweat. Well, here is a way.

<ol>
<li>
Download and install <a href='http://www.microsoft.com/en-au/download/details.aspx?id=39277' >msdeploy</a>
on your build box
</li>

<li> Use chcolate to install <a href='https://chocolatey.org/packages/WAWSDeploy'>WAWSDeploy</a>
 This is a nice wrapper around msdeploy to enable deployments using publish profiles.
 </li>

<li>Create a azure website </li>

<li>Download the publishing profile </li>


{% img left /images/2015-06-19-continous-deployment-azure-website-using-teamcity-in-few-easy-steps/azure-publishprofile.png %}


<li> use msbuild to package artifacts</li>

<li> Use WAWSDeploy to do the deployment using artificts + publish profile </li>


</ol>
Add the last two steps into a powershell script and use a TeamCity powershell step to execute it.

{% img left /images/2015-06-19-continous-deployment-azure-website-using-teamcity-in-few-easy-steps/teamcity-powershell.png %}

{% codeblock %}


 msbuild  PeopleStreme.Web/PeopleStreme.Web.csproj /T:Package /P:Configuration=Release /P:OutputPath=..\Deployment\Artifacts\PackageWeb

 WAWSDeploy "Deployment\Artifacts\PackageWeb\_PublishedWebsites\PeopleStreme.Web" "Deployment/PublishProfiles/Dev/PeopleStreme.Web.PublishSettings" /v

{% endcodeblock %}


Andddd.... you're done!


