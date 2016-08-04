# Operations Management Suite Log Analytics with Linux

[Original URL](http://blogs.technet.com/b/systemcenter/archive/2016/01/28/operations-management-suite-log-analytics-with-linux.aspx)

> Last week we kicked off our series guiding you through the rich feature set of Operations Management Suite (OMS), starting with Log Analytics. One of the goals for Operations Management Suite is...

[Last week](http://blogs.technet.com/b/systemcenter/archive/2016/01/21/a-tour-of-operations-management-suite-what-is-log-analytics.aspx) we kicked off our series guiding you through the rich feature set of Operations Management Suite (OMS), starting with Log Analytics. One of the goals for Operations Management Suite is extending this rapidly evolving cloud-based management service to multiple clouds, and multiple platforms. This week we reinforce that statement by diving deeper into Log Analytics by talking about Linux specific capabilities, and scenarios within both your datacenter, and your Linux machines in the cloud.

Last November we released the [OMS Agent for Linux](http://blogs.technet.com/b/momteam/archive/2015/11/04/oms-agent-for-linux-now-available.aspx) as our first step for Linux management with OMS. The OMS agent for Linux brings your Linux data into the service world and gives the same instant insight into events with quick search capabilities to assist with troubleshooting, or take additional actions Today, Log Analytics with Linux includes syslog data, performance metrics, and Nagios/Zabbix alerts for the following Linux distributions:

- Red Hat Enterprise Linux Server 5,6 and 7 (x86/x64)
- Ubuntu 12.04 LTS, 14.04 LTS, 15.04, and15.10 (x86/x64)
- SUSE Linux Enterprise Server 11, and 12 (x86/x64)
- CentOS Linux 5,6, and 7 (x86/x64)
- Oracle Linux 5,6, and 7 (x86/x64)
- Debian 6, 7, and 8 (x86/x64)
- Amazon Linux 2012.09 –> 2015.09 (x86/x64)

In addition to the syslog data collection feature set, an important aspect of the OMS agent for Linux has been the agent itself, utilizing [FluentD](https://github.com/fluent/fluentd), a log aggregation tool, which together with the agent itself is available under an open source license. Users can check out the source code from the [OMS agent for Linux GitHub repository](https://github.com/Microsoft/OMS-Agent-for-Linux), which contains the latest stable release code, along with the current developer versions.

## Onboarding Linux Server to OMS

The onboarding experience for Linux has been a key priority, and our goal has been to get you from zero to sending data to OMS within minutes.

The following steps show how to enable your Linux system for OMS management:

<span>wget <a href="https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh">https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh</a></span>

<span>sudo sh ./omsagent-1.1.0-28.universal.x64.sh –upgrade -w &lt;YOUR OMS WORKSPACE ID&gt; -s &lt;YOUR OMS WORKSPACE PRIMARY KEY&gt;</span>

You can also check out a complete walkthrough [video walkthrough](https://www.youtube.com/watch?v=7b4KxL7E5fw) of the process, or send us an email at [scdata@microsoft.com](mailto:scdata@microsoft.com) with further comments.

## Syslog forwarding scenarios

[![ ](http://blogs.technet.com/resized-image.ashx/__size/550x0/__key/communityserver-blogs-components-weblogfiles/00-00-00-59-30/OMS-Syslog-Diagram.png)](http://blogs.technet.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-00-59-30/OMS-Syslog-Diagram.png)

The Linux syslog daemon captures a wide variety of information, ranging from system events and kernel messages to user audit information. One of the great features of syslog as a central logging facility is the number of applications or Unix based services that can write to it using the syslog protocol. The OMS agent for Linux is able to forward all the syslog data from a server to enable various scenarios.

**Application Data:** Application logs are critical when troubleshooting, but also extremely useful when developing. This makes OMS a perfect hybrid for developers and IT operations who want a single pane of glass view across development, QA/Test, and production environments. Using pre-existing plugins, you can route Jenkins, a popular continuous integration and autoation tool, status information to OMS. Another example is configuring your Java application server logs to send directly to the syslog daemon, by adding options in logback or log4j java logging platforms.

**Network Devices:** Network devices collect various points of data, and some have capabilities to send this data to syslog. Using normal instructions for forwarding this data to the syslog daemon, you can route this data to the OMS Agent for Linux and bring visibility across all your network devices. Applying features such as custom fields, and alerting on top can give you even more granular visibility across your network device environment.

## Roadmap

Linux support is a top priority for the OMS team. We've already introduced a lot of powerful features, and we have no intentions of slowing down. Over the next three to six months you will see us light up a range of new and existing solutions with support for Linux, as well as custom logs.

**Change Tracking:** Get a view into all software and service activity on your machines across your entire environment. Track which packages have been installed, removed and updated, and also which daemons and services are running or stopped on your machines.

**Security and Audit:** Track failed logins, sudo activity, ssh activity and more for all of your Linux machines in one convenient dashboard. We will also do more complex searching of your data to alert you of notable issues you may have otherwise missed.

[![ ](http://blogs.technet.com/resized-image.ashx/__size/550x0/__key/communityserver-blogs-components-weblogfiles/00-00-00-59-30/Security-and-Audit.jpg)](http://blogs.technet.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-00-59-30/Security-and-Audit.jpg)_**_

**Alert Management:** View all your alerts from all your tools in one place. When an incident occurs you no longer will have to switch between seven different monitoring tools. Simply use Operations Management Suite and get context into how all of your alerts relate to each other. We already can collect alerts from both Nagios and Zabbix and we will be adding alert collection from more monitoring tools as part of this effort.

**Custom Logs:** Collect any log on your machine and index it to utilize the full power of OMS Search. Simply provide the file location and we will capture any new events and upload them to OMS.

Linux is truly a first class citizen in OMS, and we aim to continue building out our capabilities across Log Analytics. As we continue the OMS agent for Linux development, we want to build a rich community around it, and leverage existing community contributions. We invite and encourage your participation as we build this resource.