---
layout: post
title: "Alternative way to test emails in Dev and Test"
date: 2016-03-23 19:58:31 +1100
comments: true
categories: [slack, email testing, dev]
---

I'm working in a project which has about 40 different types of emails sent to various users in the system. The type of email would vary depending on the user role. For example an employee has a manager. When a certain action is performed, an email needs to be sent to both employee and manager. Sometimes there's an optional third party these emails could be copied to. To state is mildly, testing something like this is painful! So far the testers have been doing it the hard way : i.e create separate mail accounts and check them individually.

But what are the alternatives?


 <ul>
 <li>

  Configure a <a href='https://support.microsoft.com/en-us/kb/230235'>SMTP relay</a>
  </li>

 <li>
  If you want a quick and easy hosted solution, user something like <a href='https://mailtrap.io' >Mail Trap</a>
  </li>

<li>
 Or, use Slack! Yes, you heard it right. Here is how...
   </li>



 * Create a slack channel and <a href='https://api.slack.com/incoming-webhooks'>configure a incoming web hook</a>.

 * Post the message to the web hook as per the documentation.

 I created this NuGet package to make it easy for you.

 ```PowerShell

 > Install-Package Klabs.RouteEmailsToSlack

 ```

 Modify your SendEmail method slightly to look for the test domains and re-route those emails to slack.

{% codeblock %}
SendEmail(string receiverName ,string receiverEmail, string subject , string body )
{
    if(receiverEmail.EndsWith("test.com"))
    {
        var slackEmailRouter = new SlackEmailRouter("http://your.webhoook.url" , "#your-channel-name");
        slackEmailRouter.Send(receiverName,receiverEmail,subject,body);
     }
    else
    {
        //Your real SMTP logic here
    }
}

{% endcodeblock %}

You should start seeing emails on your channel now

{% img left /images/2016-03-23-alternative-way-to-test-emails-in-dev-and-test/slack-email-channel.png %}

Other benefits

1. You don't have to make sure to configure your test users with a specific valid email address. As long as it is on the domains valid for testing ( in this case test.com) it would always be sent to the Slack channel.

2. Save email credit. If you are using a hosted service like SendGrid or MailChimp, every email sent cost something. In SendGrid it is .025 cents. If you use this approach, it won't cost you anything. Nothing. None. Nada!

3. Easy to use. You only have to check the channel. If you are already using slack, you know what I'm talking about. No in-boxes, no send-receive and all your application related emails for all test users will be in one place.




