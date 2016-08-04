# OpenDaylight Application Developer's tutorial

[Original URL](http://sdnhub.org/tutorials/opendaylight/)

> This tutorial is intended for developers new to SDN application development with OpenDaylight. We have come a long way from the earlier version of this tutorial to focus on MD-SAL, and Karaf in this...

This tutorial is intended for developers new to SDN application development with OpenDaylight. We have come a long way from the [earlier version](http://sdnhub.org/tutorials/opendaylight-helium) of this tutorial to focus on MD-SAL, and Karaf in this tutorial. While OpenDaylight is not simply an OpenFlow controller, OpenFlow continues to be a popular south-bound and we use this in this tutorial to introduce the platform.

An introductory presentation is available at **[slideshare](http://www.slideshare.net/sdnhub/opendaylight-app-development-tutorial)**.

## <span id="1_Setup">1\. Setup</span>

To get started, download and set up the [**SDN Hub Tutorial VM**](http://sdnhub.org/tutorials/sdn-tutorial-vm/) in Virtualbox or VMware. This VM has a sample OpenDaylight project that will pull relevant dependent libraries from [nexus.opendaylight.org](http://nexus.opendaylight.org) without having to actually clone the full OpenDaylight source code.

The tutorial application that we will work with is located in 

<span id="crayon-56bcd202b0ab2541477637" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-o">/</span>
  <span class="crayon-v">home</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">SDNHub_OpenDaylight_tutorial</span>
</span>
</span>

 directory. Open a Terminal tab (Ctrl-Shift-T) and go into the SDNHub_Opendaylight_Tutorial folder where we have included two sample applications that this tutorial will focus on: 1) a Hub / L2 learning switch, 2) a network traffic monitoring tap.

Before we start, we recommend you run the following commands to update the tutorial code, which is available at <https://github.com/sdnhub/SDNHub_Opendaylight_Tutorial>:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-r">cd</span>
  <span class="crayon-e">SDNHub_Opendaylight_Tutorial</span>
</p>
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-e">git </span>
  <span class="crayon-v">pull</span>
  <span class="crayon-o">–</span>
  <span class="crayon-v">rebase</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## <span id="2_Fundamentals_for_OpenDaylight_programming">2\. Fundamentals for OpenDaylight programming</span>

OpenDayLight uses the following software tools/paradigms. It is important to become familiar with them. The Service Abstraction Layer (SAL) is your friend for most development aspects.

- **Java interfaces**: Java Interfaces are used for event listening, specifications and forming patterns. This is the main way in which specific bundles implement call-back functions for events and also to indicate awareness of specific state. Many of the interfaces are auto-generated using YANG tools.
- **Maven**: OpenDayLight uses Maven for easier build automation. Maven uses pom.xml (Project Object Model for this bundle) to script the dependencies between bundles and also to describe what bundles to load on start.
- **OSGi**: This framework in the backend of OpenDayLight allows dynamically loading bundles and packaged Jar files, and binding bundles together for information exchange.
- **Karaf**: Karaf is a small OSGi based runtime which provides a lightweight container for loading different modules.

### <span id="21_Maven_and_project_building">2.1 Maven and project building</span>

Some basic understanding of maven is essential for working with OpenDaylight. Anytime you create a new project or module, or expand functionality of existing modules, you will have to appropriately upgrade the various pom.xml and feature.xml files.

Let's start with building the the tutorial project in the VM using the following step.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">SDNHub_Opendaylight_Tutorial</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-e">mvn </span>
  <span class="crayon-v">install</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">nsu</span>
</p>
  <p>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">lots </span>
  <span class="crayon-e">of </span>
  <span class="crayon-i">text</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">Reactor </span>
  <span class="crayon-v">Summary</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">Tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-e">common </span>
  <span class="crayon-i">properties</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">1.090</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-e">common </span>
  <span class="crayon-i">utils</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">4.276</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-v">learning</span>
  <span class="crayon-o">-</span>
  <span class="crayon-st">switch</span>
  <span class="crayon-o">-</span>
  <span class="crayon-r">parent</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">0.027</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-e">learning </span>
  <span class="crayon-st">switch</span>
  <span class="crayon-i">Impl</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">9.013</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-e">Learning </span>
  <span class="crayon-st">Switch</span>
  <span class="crayon-i">Config</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">0.601</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-e">Tap </span>
  <span class="crayon-e">application </span>
  <span class="crayon-i">Model</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">3.564</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-v">tapapp</span>
  <span class="crayon-o">-</span>
  <span class="crayon-r">parent</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">0.023</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-e">Tap </span>
  <span class="crayon-e">application </span>
  <span class="crayon-i">Impl</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">6.049</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">Project </span>
  <span class="crayon-e">Tap </span>
  <span class="crayon-e">application </span>
  <span class="crayon-i">Config</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">0.052</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-i">features</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">1.736</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-v">distribution</span>
  <span class="crayon-o">-</span>
  <span class="crayon-r">parent</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">0.028</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-e">Karaf </span>
  <span class="crayon-i">branding</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">0.359</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">SDN </span>
  <span class="crayon-e">Hub </span>
  <span class="crayon-e">tutorial </span>
  <span class="crayon-e">project </span>
  <span class="crayon-e">distribution </span>
  <span class="crayon-i">packaging</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">44.436</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-i">main</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-i">SUCCESS</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">0.025</span>
  <span class="crayon-v">s</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">BUILD </span>
  <span class="crayon-i">SUCCESS</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">Total </span>
  <span class="crayon-r">time</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">32</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">39</span>
  <span class="crayon-i">min</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">Finished </span>
  <span class="crayon-r">at</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">2015</span>
  <span class="crayon-o">-</span>
  <span class="crayon-cn">06</span>
  <span class="crayon-o">-</span>
  <span class="crayon-cn">07T22</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">50</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">37</span>
  <span class="crayon-o">-</span>
  <span class="crayon-cn">07</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">00</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-e">Final </span>
  <span class="crayon-v">Memory</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">100M</span>
  <span class="crayon-o">/</span>
  <span class="crayon-cn">631M</span>
</p>
  <p>
  <span class="crayon-sy">[</span>
  <span class="crayon-v">INFO</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
  <span class="crayon-o">–</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

This indicates that the build went successfully. In the event something failed, the build will stop at the module where it failed. All the above modules are important to make the full project to work. Here are some important notes on the build above:

- "mvn" command uses [Apache Maven](http://maven.apache.org) to build the tutorial code. It compiles code based on the pom.xml file in that directory. "install" is essential for compilation. It also accepts an optional argument "clean" if you wish to clean the temporary build files.
- The way Maven works is by resolving dependencies between packages. For instance, our example applications (the learning-switch and tapapp code we will edit) depends on the Opendaylight controller package and on the OpenFlowPlugin package. This triggers Maven to download the pre-compiled jar files from [nexus.opendaylight.org](http://nexus.opendaylight.org), resolve dependencies, and so on.

  - Have a look at the [snapshot repository of OpenDaylight](http://nexus.opendaylight.org/content/repositories/opendaylight.snapshot/org/opendaylight/) to get an idea of what's out there.
  - Also look at 

    <span id="crayon-56bcd202b0ae4549320643" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-o">~</span>
      <span class="crayon-o">/</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">m2</span>
      <span class="crayon-o">/</span>
      <span class="crayon-v">repository</span>
    </span>
    </span>

     directory in your local machine. This is where maven places all compiled or downloaded Jar files. If you have build issues or karaf runtime import issues, you should look in this directory.

- Creating a pom.xml with dependencies being downloaded is much, much faster than compiling the tutorial project within the full source of the Opendaylight controller. This can be compared with compiling an application instead of compiling the full operating system.
- Maven has a module name and Java code has a package name. They do not need to match. The module learning-switch has following attributes. The Maven group-id and artifact-id are used for the name of the Jar file generated, while package name is only recognized within the source code.

  - pom.xml group-id: 

    <span id="crayon-56bcd202b0aee589614314" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-v">org</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">sdnhub</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">odl</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">tutorial</span>
    </span>
    </span>

  - pom.xml artifact-id: 

    <span id="crayon-56bcd202b0af6159215972" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-v">learning</span>
      <span class="crayon-o">-</span>
      <span class="crayon-st">switch</span>
    </span>
    </span>

  - Source code package name: 

    <span id="crayon-56bcd202b0aff658935424" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-v">org</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">sdnhub</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">odl</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">tutorial</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">learning</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-st">switch</span>
    </span>
    </span>

- **Note:** to speed up subsequent compilations, you can run "mvn install -DskipTests -DskipIT -nsu". The "nsu" is short for no-snapshot-updates. It ensures that the compilation does not download definitions from nexus.opendaylight.org
- The main modules built are listed in the root pom.xml (located in ~/SDNHub_Opendaylight_Tutorial/pom.xml). Each child module and its respective child modules have their own pom.xml. All common properties (that includes versions, and common dependencies) are listed in common/parent/pom.xml. This pom.xml serves as the base for all other module pom.xml.
- All these poms are individually built and finally combined by the distribution/pom.xml to prepare a running directory. The sample project's distribution/opendaylight-karaf/pom.xml is specially crafted to include our sample project and specify one of them to be autoloaded when controller is started.

### <span id="22_Karaf_and_feature_creation">2.2 Karaf and feature creation</span>

Now that we compiled our sample project, let's run the controller itself; preferably in a different terminal

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">SDNHub<em>Opendaylight<em>Tutorial</em></em></span>
  <em><em><span class="crayon-sy">$</span><span class="crayon-e">cd </span><span class="crayon-v">distribution</span><span class="crayon-o">/</span><span class="crayon-v">opendaylight</span><span class="crayon-o">-</span><span class="crayon-v">karaf</span><span class="crayon-o">/</span><span class="crayon-v">target</span><span class="crayon-o">/</span><span class="crayon-e">assembly</span></em></em>
</p>
  <em>
  <em><p><span class="crayon-v">ubuntu</span><span class="crayon-sy">@</span><span class="crayon-v">sdnhubvm</span><span class="crayon-o">:</span><span class="crayon-o">~</span><span class="crayon-o">/</span><span class="crayon-v">SDNHub<em>Opendaylight<em>Tutorial</em></em></span><em><em><span class="crayon-o">/</span><span class="crayon-v">distribution</span><span class="crayon-o">/</span><span class="crayon-v">opendaylight</span><span class="crayon-o">-</span><span class="crayon-v">karaf</span><span class="crayon-o">/</span><span class="crayon-v">target</span><span class="crayon-o">/</span><span class="crayon-v">assembly</span><span class="crayon-sy">$</span><span class="crayon-sy">.</span><span class="crayon-o">/</span><span class="crayon-v">bin</span><span class="crayon-o">/</span><span class="crayon-e">karaf </span></em></em></p><em><em><p><span class="crayon-v">karaf</span><span class="crayon-o">:</span><span class="crayon-e">Enabling </span><span class="crayon-e">Java </span><span class="crayon-e">debug </span><span class="crayon-v">options</span><span class="crayon-o">:</span><span class="crayon-o">-</span><span class="crayon-v">Xdebug</span><span class="crayon-o">-</span><span class="crayon-v">Xnoagent</span><span class="crayon-o">-</span><span class="crayon-v">Djava</span><span class="crayon-sy">.</span><span class="crayon-v">compiler</span><span class="crayon-o">=</span><span class="crayon-v">NONE</span><span class="crayon-o">-</span><span class="crayon-v">Xrunjdwp</span><span class="crayon-o">:</span><span class="crayon-v">transport</span><span class="crayon-o">=</span><span class="crayon-v">dt<em>socket</em></span><em><span class="crayon-sy">,</span><span class="crayon-v">server</span><span class="crayon-o">=</span><span class="crayon-v">y</span><span class="crayon-sy">,</span><span class="crayon-v">suspend</span><span class="crayon-o">=</span><span class="crayon-v">n</span><span class="crayon-sy">,</span><span class="crayon-v">address</span><span class="crayon-o">=</span><span class="crayon-cn">5005</span></em></p><em><p><span class="crayon-e">Java </span><span class="crayon-e">HotSpot</span><span class="crayon-sy">(</span><span class="crayon-v">TM</span><span class="crayon-sy">)</span><span class="crayon-cn">64</span><span class="crayon-o">-</span><span class="crayon-e">Bit </span><span class="crayon-e">Server </span><span class="crayon-e">VM </span><span class="crayon-v">warning</span><span class="crayon-o">:</span><span class="crayon-e">ignoring </span><span class="crayon-e">option </span><span class="crayon-v">MaxPermSize</span><span class="crayon-o">=</span><span class="crayon-cn">512m</span><span class="crayon-sy">;</span><span class="crayon-e">support </span><span class="crayon-e">was </span><span class="crayon-e">removed </span><span class="crayon-st">in</span><span class="crayon-cn">8.0</span></p><p><span class="crayon-e">Listening </span><span class="crayon-st">for</span><span class="crayon-e">transport </span><span class="crayon-e">dt_socket </span><span class="crayon-e">at </span><span class="crayon-v">address</span><span class="crayon-o">:</span><span class="crayon-cn">5005</span></p></em></em></em><p><em><em><em><span class="crayon-e">
</span></em></em></em> <span class="crayon-v">
  <strong>
  <strong></strong>
</strong>
</span><strong><strong><span class="crayon-sy">
  <em>
</em>
</span><em><span class="crayon-i">
</span></em><span class="crayon-i">
  <em>
</em>
</span><em><span class="crayon-i">
</span></em><span class="crayon-i">
  <em>
</em>
</span><em><span class="crayon-v">
</span></em></strong></strong></p><strong><strong></strong></strong><p><strong><strong><span class="crayon-o">/</span><span class="crayon-v">
</span></strong></strong><span class="crayon-o">|</span><span class="crayon-v">
  <em>
</em>
</span><em><span class="crayon-sy">
</span></em><span class="crayon-sy">\</span><span class="crayon-o">|</span><span class="crayon-sy">\</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span></p><p><span class="crayon-o">|</span><span class="crayon-sy">(</span><span class="crayon-v">
  <strong>_</strong>
</span><strong><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-sy">\</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-v">
</span></strong><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-i">
  <em>
</em>
</span><em><span class="crayon-v">
</span></em><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-v">
  <em>
</em>
</span><em><span class="crayon-sy">
</span></em></p><p><span class="crayon-sy">\</span><span class="crayon-v">
  <strong>
</strong>
</span><strong><span class="crayon-sy">_</span><span class="crayon-sy">\</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-sy">.</span><span class="crayon-sy">`</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-v">
</span></strong><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-s">’<em> \ </em></span></p><em></em><p><em><span class="crayon-s"><strong>__) | |</strong>| | |\  | | |  | | |</span></em>| | |<em>) |</em></p><em></em><p><em><span class="crayon-s">    |<em>__</em></span></em>/|<strong>_</strong>/|<em>| \</em>| |<em>|  |</em>|_<em>,</em>|<em>._</em>/ </p><p><span class="crayon-s">Hit ’</span><span class="crayon-s">’ for a list of available commands</span></p><p><span class="crayon-s">and ’</span><span class="crayon-sy">[</span><span class="crayon-v">cmd</span><span class="crayon-sy">]</span><span class="crayon-o">–</span><span class="crayon-i">help</span><span class="crayon-s">’ for help on a specific command.</span></p><p><span class="crayon-s">Hit ’</span><span class="crayon-s">‘ or type ’</span><span class="crayon-v">system</span><span class="crayon-o">:</span><span class="crayon-i">shutdown</span><span class="crayon-s">‘ or ’</span><span class="crayon-i">logout</span>’<span class="crayon-st">to</span><span class="crayon-e">shutdown </span><span class="crayon-v">OpenDaylight</span><span class="crayon-sy">.</span></p><p><span class="crayon-v">opendaylight</span><span class="crayon-o">-</span><span class="crayon-v">user</span><span class="crayon-sy">@</span><span class="crayon-v">root</span><span class="crayon-o">&gt;</span><span class="crayon-v">feature</span><span class="crayon-o">:</span><span class="crayon-v">list</span><span class="crayon-o">|</span><span class="crayon-e">grep </span><span class="crayon-e">sdnhub</span></p><p><span class="crayon-v">sdnhub</span><span class="crayon-o">-</span><span class="crayon-v">tutorial</span><span class="crayon-o">-</span><span class="crayon-v">tapapp</span><span class="crayon-o">|</span><span class="crayon-cn">1.0.0</span><span class="crayon-o">-</span><span class="crayon-v">SNAPSHOT</span><span class="crayon-o">|</span><span class="crayon-v">x</span><span class="crayon-o">|</span><span class="crayon-v">tutorial</span><span class="crayon-o">-</span><span class="crayon-v">features</span><span class="crayon-o">-</span><span class="crayon-cn">1.0.0</span><span class="crayon-o">-</span><span class="crayon-v">SNAPSHOT</span><span class="crayon-o">|</span><span class="crayon-e">SDN </span><span class="crayon-e">Hub </span><span class="crayon-v">Tutorial</span><span class="crayon-o">::</span><span class="crayon-v">OpenDaylight</span><span class="crayon-o">::</span><span class="crayon-e">Tap </span><span class="crayon-e">application</span></p><p><span class="crayon-v">sdnhub</span><span class="crayon-o">-</span><span class="crayon-v">tutorial</span><span class="crayon-o">-</span><span class="crayon-v">learning</span><span class="crayon-o">-</span><span class="crayon-st">switch</span><span class="crayon-o">|</span><span class="crayon-cn">1.0.0</span><span class="crayon-o">-</span><span class="crayon-v">SNAPSHOT</span><span class="crayon-o">|</span><span class="crayon-o">|</span><span class="crayon-v">tutorial</span><span class="crayon-o">-</span><span class="crayon-v">features</span><span class="crayon-o">-</span><span class="crayon-cn">1.0.0</span><span class="crayon-o">-</span><span class="crayon-v">SNAPSHOT</span><span class="crayon-o">|</span><span class="crayon-e">SDN </span><span class="crayon-e">Hub </span><span class="crayon-v">Tutorial</span><span class="crayon-o">::</span><span class="crayon-v">OpenDaylight</span><span class="crayon-o">::</span><span class="crayon-e">Learning </span><span class="crayon-st">switch</span></p><p><span class="crayon-v">opendaylight</span><span class="crayon-o">-</span><span class="crayon-v">user</span><span class="crayon-sy">@</span><span class="crayon-v">root</span><span class="crayon-o">&gt;</span><span class="crayon-v">bundle</span><span class="crayon-o">:</span><span class="crayon-v">list</span><span class="crayon-o">-</span><span class="crayon-v">s</span><span class="crayon-o">|</span><span class="crayon-e">grep </span><span class="crayon-i">sdnhub</span></p><p><span class="crayon-cn">178</span><span class="crayon-o">|</span><span class="crayon-v">Active</span><span class="crayon-o">|</span><span class="crayon-cn">80</span><span class="crayon-o">|</span><span class="crayon-cn">1.0.0.SNAPSHOT</span><span class="crayon-o">|</span><span class="crayon-v">org</span><span class="crayon-sy">.</span><span class="crayon-v">sdnhub</span><span class="crayon-sy">.</span><span class="crayon-v">odl</span><span class="crayon-sy">.</span><span class="crayon-v">tutorial</span><span class="crayon-sy">.</span><span class="crayon-v">tapapp</span><span class="crayon-sy">.</span><span class="crayon-i">impl</span></p><p><span class="crayon-cn">179</span><span class="crayon-o">|</span><span class="crayon-v">Active</span><span class="crayon-o">|</span><span class="crayon-cn">80</span><span class="crayon-o">|</span><span class="crayon-cn">1.0.0.SNAPSHOT</span><span class="crayon-o">|</span><span class="crayon-v">org</span><span class="crayon-sy">.</span><span class="crayon-v">sdnhub</span><span class="crayon-sy">.</span><span class="crayon-v">odl</span><span class="crayon-sy">.</span><span class="crayon-v">tutorial</span><span class="crayon-sy">.</span><span class="crayon-v">tapapp</span><span class="crayon-sy">.</span><span class="crayon-e">model        </span></p><p><span class="crayon-v">opendaylight</span><span class="crayon-o">-</span><span class="crayon-v">user</span><span class="crayon-sy">@</span><span class="crayon-v">root</span><span class="crayon-o">&gt;</span><span class="crayon-v">log</span><span class="crayon-o">:</span><span class="crayon-e">set </span><span class="crayon-e">DEBUG </span><span class="crayon-v">org</span><span class="crayon-sy">.</span><span class="crayon-v">sdnhub</span><span class="crayon-sy">.</span><span class="crayon-v">odl</span><span class="crayon-sy">.</span><span class="crayon-e">tutorial</span></p><p><span class="crayon-v">opendaylight</span><span class="crayon-o">-</span><span class="crayon-v">user</span><span class="crayon-sy">@</span><span class="crayon-v">root</span><span class="crayon-o">&gt;</span><span class="crayon-v">log</span><span class="crayon-o">:</span><span class="crayon-i">tail</span></p><p><span class="crayon-sy">.</span><span class="crayon-sy">.</span><span class="crayon-sy">.</span><span class="crayon-e">lots </span><span class="crayon-e">of </span><span class="crayon-e">debug </span><span class="crayon-i">logs</span><span class="crayon-sy">.</span><span class="crayon-sy">.</span><span class="crayon-sy">.</span></p></em>
</em>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Here are some notes on the above process of starting the controller:

- Running karaf starts all the Java bundles installed as jar files in the OSGi environment. Once all bundles are started, the 

  <span id="crayon-56bcd202b0b14097577396" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-e">createInstance</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-sy">)</span>
  </span>
  </span>

   method in each implementation class will be be called, and the controller moves to an event-driven operation state.
- The karaf shell is your main portal for managing all applications and the Java bundles. Press "Tab" to learn about all the CLI commands possible here
- "feature:list" and "bundle:list -s" are commands that will help with look at active features and bundles in the Karaf runtime environment. 'x' means the feature is loaded and active. Work "Active" means the module/bundle is running, while "Resolved" would mean that it has been stopped, and "Installed" means it is blocked on some missing dependency.
- There are also commands to install a feature (feature:install) or a specific bundle (bundle:install). By default karaf loads all the features listed in the distribution/opendaylight-karaf/target/assembly/etc/org.apache.karaf.features.cfg file. In our example, we autoload sdnhub-tutorial-tapapp feature.
- Karaf and OSGi do not provide a way to specify which modules get precedence over other modules. So we use the config subsystem of OpenDaylight to enforce the ordering. More on that in the next subsection.

To drive karaf, every application built must have a feature description for the karaf shell to load it. The feature.xml file is where we define it. For instance, the sdnhub-tutorial-tapapp feature that is autoloaded, has the following corresponding feature description in the feature.xml file:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">SDNHub_Opendaylight_Tutorial</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-r">cat</span>
  <span class="crayon-v">features</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">src</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">main</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">resources</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">feature</span>
  <span class="crayon-e">.xml</span>
</p>
  <p>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">feature </span>
  <span class="crayon-v">name</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“sdnhub-tutorial-tapapp”</span>
  <span class="crayon-v">description</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“SDN Hub Tutorial :: OpenDaylight :: Tap application”</span>
  <span class="crayon-v">version</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">‘${project.version}’</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">feature </span>
  <span class="crayon-v">version</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“${openflowplugin.version}”</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">odl</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">openflowplugin</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">southbound</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">feature</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">feature </span>
  <span class="crayon-v">version</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“${openflowplugin.version}”</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">odl</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">openflowplugin</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">flow</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">services</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">feature</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">feature </span>
  <span class="crayon-v">version</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“${mdsal.version}”</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">odl</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">mdsal</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">broker</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">feature</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">bundle</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">mvn</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">org</span>
  <span class="crayon-e">.sdnhub</span>
  <span class="crayon-e">.odl</span>
  <span class="crayon-e">.tutorial</span>
  <span class="crayon-e">.tapapp</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">tapapp</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">impl</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">tapapp</span>
  <span class="crayon-e">.version</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">bundle</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-v">bundle</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">mvn</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">org</span>
  <span class="crayon-e">.sdnhub</span>
  <span class="crayon-e">.odl</span>
  <span class="crayon-e">.tutorial</span>
  <span class="crayon-e">.tapapp</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">tapapp</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">model</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">tapapp</span>
  <span class="crayon-e">.version</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">bundle</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-e">configfile </span>
  <span class="crayon-v">finalname</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“etc/opendaylight/karaf/${tapapp.configfile}”</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">mvn</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">org</span>
  <span class="crayon-e">.sdnhub</span>
  <span class="crayon-e">.odl</span>
  <span class="crayon-e">.tutorial</span>
  <span class="crayon-e">.tapapp</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">tapapp</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">config</span>
  <span class="crayon-o">/</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">{</span>
  <span class="crayon-v">tapapp</span>
  <span class="crayon-e">.version</span>
  <span class="crayon-sy">}</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">xml</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">config</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">configfile</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">feature</span>
  <span class="crayon-o">&gt;</span>
</p>
  <p>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The above feature description dictates that the installation of the sdnhub-tutorial-tapapp features requires us to load all the following. Even if one of them cannot be loaded, the feature hangs:

1. OpenFlow plugin,
2. MD-SAL data broker,
3. TapApp model,
4. TapApp implementation,
5. TapApp configuration for the config subsystem

If you create a new application, do not forget to include its description in the feature file.

### <span id="23_Config_subsystem">2.3 Config subsystem</span>

OpenDaylight has a built-in feature called the config subsystem that instantiates bundles in the appropriate ordering with the MD-SAL right dependencies pre-loaded. This is achieved by having a configuration file, typically named with a number in the beginning to denote the order of loading.

For instance, the tap application has a 

<span id="crayon-56bcd202b0b2a416262952" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-cn">50</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">tapapp</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">config</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">xml</span>
</span>
</span>

 that is added to the karaf feature. This xml file is read at run-time and appropriate dependencies are injected.

To get your new application to load correctly, this 

<span id="crayon-56bcd202b0b33056217931" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">config</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">xml</span>
</span>
</span>

, and the config augmentation with the implementation YANG file (e.g., 

<span id="crayon-56bcd202b0b3b308315581" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">tap</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">impl</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">yang</span>
</span>
</span>

) needs to have the right namespace, package name, and artifact id.

### <span id="24_Mininet">2.4 Mininet</span>

Mininet is a network emulation software that works with Open vSwitch to create a set of OpenFlow switches connected to virtual hosts and interconnected in varying topologies. The tutorial VM has mininet installed already and can be kicked off in a new terminal with the following command:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-e">sudo </span>
  <span class="crayon-v">mn</span>
  <span class="crayon-o">–</span>
  <span class="crayon-e">topo </span>
  <span class="crayon-v">single</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">3</span>
  <span class="crayon-o">–</span>
  <span class="crayon-v">mac</span>
  <span class="crayon-o">–</span>
  <span class="crayon-st">switch</span>
  <span class="crayon-v">ovsk</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">protocols</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">OpenFlow13</span>
  <span class="crayon-o">–</span>
  <span class="crayon-e">controller </span>
  <span class="crayon-v">remote</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You will now see a mininet CLI prompt after starting 3 hosts and 1 switch. The switch also attempts connect to a remote controller. Since we started OpenDaylight, you should also see several lines written to the karaf console once the switch connects to it.

You can begin a ping between two of the hosts. However, the ping will fail because there is no intelligence in the switch to learn the MAC addresses of each host and forward traffic to the correct switch ports.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">mininet</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-e">h1 </span>
  <span class="crayon-e">ping </span>
  <span class="crayon-e">h2</span>
</p>
  <p>
  <span class="crayon-i">PING</span>
  <span class="crayon-cn">10.0.0.2</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-cn">10.0.0.2</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-cn">56</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-cn">84</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-e">bytes </span>
  <span class="crayon-e">of </span>
  <span class="crayon-v">data</span>
  <span class="crayon-sy">.</span>
</p>
  <p>
  <span class="crayon-i">From</span>
  <span class="crayon-cn">10.0.0.1</span>
  <span class="crayon-v">icmp_seq</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-e">Destination </span>
  <span class="crayon-e">Host </span>
  <span class="crayon-e">Unreachable</span>
</p>
  <p>
  <span class="crayon-i">From</span>
  <span class="crayon-cn">10.0.0.1</span>
  <span class="crayon-v">icmp_seq</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">2</span>
  <span class="crayon-e">Destination </span>
  <span class="crayon-e">Host </span>
  <span class="crayon-i">Unreachable</span>
</p>
  <p>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Let us add that intelligence by installing the sdnhub-tutorial-learning-switch feature. You will notice that after doing this the ping will start succeeding.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">opendaylight</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">user</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">root</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">feature</span>
  <span class="crayon-o">:</span>
  <span class="crayon-e">install </span>
  <span class="crayon-v">sdnhub</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">tutorial</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">learning</span>
  <span class="crayon-o">-</span>
  <span class="crayon-st">switch</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">mininet</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-e">h1 </span>
  <span class="crayon-e">ping </span>
  <span class="crayon-e">h2</span>
</p>
  <p>
  <span class="crayon-i">From</span>
  <span class="crayon-cn">10.0.0.1</span>
  <span class="crayon-v">icmp_seq</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-e">Destination </span>
  <span class="crayon-e">Host </span>
  <span class="crayon-e">Unreachable</span>
</p>
  <p>
  <span class="crayon-i">From</span>
  <span class="crayon-cn">10.0.0.1</span>
  <span class="crayon-v">icmp_seq</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">2</span>
  <span class="crayon-e">Destination </span>
  <span class="crayon-e">Host </span>
  <span class="crayon-v">Unreachable</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

ping still fails because there is no default rule in the switch to send packet-in messages to the controller. Let's add that and verify the ping

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">mininet</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-e">s1 </span>
  <span class="crayon-v">ovs</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">ofctl </span>
  <span class="crayon-v">add</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">flow </span>
  <span class="crayon-v">tcp</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">127.0.0.1</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">6634</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">OOpenFlow13 </span>
  <span class="crayon-v">priority</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">action</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">output</span>
  <span class="crayon-o">:</span>
  <span class="crayon-e">controller</span>
</p>
  <p>
  <span class="crayon-v">mininet</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-e">h1 </span>
  <span class="crayon-e">ping </span>
  <span class="crayon-i">h2</span>
</p>
  <p>
  <span class="crayon-cn">64</span>
  <span class="crayon-e">bytes </span>
  <span class="crayon-i">from</span>
  <span class="crayon-cn">10.0.0.2</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">icmp_req</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-v">ttl</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">64</span>
  <span class="crayon-v">time</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">94.1</span>
  <span class="crayon-i">ms</span>
</p>
  <p>
  <span class="crayon-cn">64</span>
  <span class="crayon-e">bytes </span>
  <span class="crayon-i">from</span>
  <span class="crayon-cn">10.0.0.2</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">icmp_req</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">2</span>
  <span class="crayon-v">ttl</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">64</span>
  <span class="crayon-v">time</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">21.2</span>
  <span class="crayon-i">ms</span>
</p>
  <p>
  <span class="crayon-cn">64</span>
  <span class="crayon-e">bytes </span>
  <span class="crayon-i">from</span>
  <span class="crayon-cn">10.0.0.2</span>
  <span class="crayon-o">:</span>
  <span class="crayon-v">icmp_req</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">3</span>
  <span class="crayon-v">ttl</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">64</span>
  <span class="crayon-v">time</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">21.9</span>
  <span class="crayon-v">ms</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You will have noticed that the pings are actually taking longer than they should for a switch (typically sub-1ms) for such a simple single-switch topology. This is because the controller is currently functioning in "Hub" mode and flooding every packet in the software.

In the next few sections, we will learn enough programming aspects to convert the hub into a switch and also build other functionality in the platform.

## <span id="3_Introduction_to_OpenDaylight_architecture">3\. Introduction to OpenDaylight architecture</span>

Before we jump into the code, a high-level overview of the OpenDaylight controller is in order. OpenDaylight is a modular platform with most modules reusing some common services and interfaces. Each module is developed under a multi-vendor sub-project. You can find the list of projects [**here**](https://wiki.opendaylight.org/view/Main_Page#Projects).<br>
[![opendaylight_helium](http://sdnhub.org/wp-content/uploads/2013/11/700x492xopendaylight_helium-1024x719.jpg.pagespeed.ic.AEEwoRopbL.jpg)](http://sdnhub.org/wp-content/uploads/2013/11/opendaylight_helium.jpg)

The idea with building applications on the OpenDaylight platform is to leverage functionality in other platform bundles, each of which export important services through Java interfaces. Many of these services are build in a provider-consumer model over an adaptation layer called MD-SAL.

[![MD-SAL data store at the core](http://sdnhub.org/wp-content/uploads/2015/06/Nx300xMD-SAL-data-store-at-the-core.png.pagespeed.ic.xnDYiTgkNj.png)](http://sdnhub.org/wp-content/uploads/2015/06/MD-SAL-data-store-at-the-core.png)Programming in OpenDaylight involves adopting the Model-View-Control approach for SDN application development:

1. YANG Model for data, RPC and notifications
2. REST API view autogenerated and accessible through RESTconf
3. Java Implementation coded to handle data changes, notifications and RPC call backs

### <span id="31_Model-driven_SAL_MD-SAL">3.1 Model-driven SAL (MD-SAL)</span>

[Model-driven Service Adaptation Layer (MD-SAL)](https://wiki.opendaylight.org/view/OpenDaylight_Controller:MD-SAL:FAQ), is the kernel of the platform where the different layers and modules are interconnected through well-defined API. Here are some important notes about MD-SAL:

- Each API is generated from models defined in the YANG language during build time and loaded into the controller when the model bundle is loaded onto the Karaf platform.
- At the core of the platform is a logically centralized data store that keeps relevant state in two buckets – 1) config data store, 2) operational data store.
- All event calls and data go from "provider" to a "consumer" through this central datastore using MD-SAL mapping logic.

### <span id="32_YANG_model">3.2 YANG model</span>

OpenDaylight heavily uses YANG to model any data, notification or remote procedure call (RPC) that goes between different modules. For those unfamiliar with YANG, we recommend going over the [YANG tutorial](http://www.slideshare.net/tailfsystems/netconf-yang-tutorial) by Tail-F systems. In a nutshell, YANG is a language for describing the basic structure of some application data that is s
tored in a tree hierarchy within containers.

As an example, here is an excerpt of the YANG model for storing nodes (i.e., switches) and node-connectors (i.e., interfaces or ports) defined within 

<span id="crayon-56bcd202b0b73010463476" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">opendaylight</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">inventory</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">yang</span>
</span>
</span>

:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">module</span>
  <span class="crayon-e">opendaylight</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">inventory</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-t">namespace</span>
  <span class="crayon-s">“urn:opendaylight:inventory”</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">container</span>
  <span class="crayon-e">nodes</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">list</span>
  <span class="crayon-e">node</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-i">key</span>
  <span class="crayon-s">“id”</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">leaf</span>
  <span class="crayon-e">id</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">type </span>
  <span class="crayon-v">node</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">id</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-i">list</span>
  <span class="crayon-s">“node-connector”</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-i">key</span>
  <span class="crayon-s">“id”</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">leaf</span>
  <span class="crayon-e">id</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">type </span>
  <span class="crayon-v">node</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">connector</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">id</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Once this model is provided to the MD-SAL platform by appropriately including in the feature file and loading in Karaf, MD-SAL creates two data stores for this module: 1) Config data store, 2) Operational data store. The config data store is persisted by default across different runs of the controller (The config data store is stored in snapshots and journals directories in the karaf run location).

# <span id="33_Instance_identifiers">3.3 Instance identifiers</span>

An application or external end-user can post data, either through MD-SAL transaction or RESTconf, to this data store. The individual objects are stored in a parent-child hierarchy and accessible through YANG instance identifiers. For the above YANG model, let's say there is a node-connector "openflow:1:1″ that is stored in the data store. One can access details of that node-connector by creating an instance identifier as follows:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">InstanceIdentifier </span>
  <span class="crayon-v">ncIID</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">InstanceIdentifier</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">builder</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">Nodes</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">class</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">child</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">Node</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">class</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-r">new</span>
  <span class="crayon-e">NodeKey</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">new</span>
  <span class="crayon-e">NodeId</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“openflow:1”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">child</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">NodeConnector</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">class</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-r">new</span>
  <span class="crayon-e">NodeConnectorKey</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">new</span>
  <span class="crayon-e">NodeConnectorId</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“openflow:1:1”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">build</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Or, it could be accessed through RESTconf by going to the URL <http://localhost:8181/restconf/config/opendaylight-inventory:nodes/node/openflow:1/node-connector/openflow:1:1>, where the keyword "opendaylight-inventory" corresponds to the namespace of that module, and "nodes" corresponds to the container at the root level of the tree.

Useful tip: In case you are provided with an instance identifier, it is possible to extract the instance identifier or key of the parent objects by performing firstKeyOf() or firstIdentifierOf() as follows:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">InstanceIdentifier </span>
  <span class="crayon-v">nodeIID</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">ncIID</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">firstIdentifierOf</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">Node</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">class</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-e">NodeId </span>
  <span class="crayon-v">nodeId</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">ncIID</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">firstKeyOf</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">Node</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">class</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">NodeKey</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">class</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">getId</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# <span id="34_Data_store_Transactions">3.4 Data store Transactions</span>

Once an instance identifier is created, a read or write transaction can be performed to that location in the data store using the DataBroker service. There are two classes (viz., WriteTransaction and ReadOnlyTransaction) that can help you with this.

Let's say we want to write a NodeConnector object to the data store, we use the following code:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">WriteTransaction </span>
  <span class="crayon-v">modification</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">dataBroker</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">newWriteOnlyTransaction</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">modification</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">merge</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">LogicalDataStoreType</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">CONFIGURATION</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">ncIID</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">nodeConnector</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-t">true</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">CheckedFuture</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-t">Void</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">TransactionCommitFailedException</span>
  <span class="crayon-o">&gt;</span>
  <span class="crayon-v">commitFuture</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">modification</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">submit</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">try</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">commitFuture</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">checkedGet</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">catch</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-i">Exception</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">modification</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">cancel</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Most transactions return a Java Future object. It is important to check on the status of this Future object to see if the transaction succeeded and if there are any specific outputs generated.

Let's say we want to read a NodeConnector object from the data store, we use the following code:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-e">ReadOnlyTransaction </span>
  <span class="crayon-v">readTransaction</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">dataBroker</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">newReadOnlyTransaction</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">try</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">Optional </span>
  <span class="crayon-v">optionalData</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">readTransaction</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">read</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">LogicalDataStoreType</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">CONFIGURATION</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">ncIID</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">get</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">if</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">optionalData</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">isPresent</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">NodeConnector </span>
  <span class="crayon-v">nc</span>
  <span class="crayon-o">=</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">NodeConnector</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-v">optionalData</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">get</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">}</span>
  <span class="crayon-st">catch</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">ExecutionException</span>
  <span class="crayon-o">|</span>
  <span class="crayon-i">InterruptedException</span>
  <span class="crayon-v">e</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">readTransaction</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">close</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Each transaction consumes a certain amount of CPU resources and further more when there is a cluster consistency being enforced. This can limit the number of transactions per second. In OpenDaylight MD-SAL, one can perform batch transactions and transaction chains to get better read and write perform. These are out of scope of this tutorial.

# <span id="35_Advanced_YANG_operations">3.5 Advanced YANG operations</span>

Besides basic data models as shown above, it is possible to define following constructs in YANG models to model additional services between modules:

- **Augmentations**: Unless specified otherwise, YANG models can be expanded, in a new namespace, to add extra data within an existing data model. Note that the augmentations must be declared at build time.

  - For instance, OpenFlow rules are augmented over the Node object shown above to create the flow table augmentation. Here is how the augmented tree looks like.

[![odl-restconf-inventory-nodes](http://sdnhub.org/wp-content/uploads/2013/11/1024x467xodl-restconf-inventory-nodes-1024x467.png.pagespeed.ic.yQflkRKZaf.jpg)](http://sdnhub.org/wp-content/uploads/2013/11/odl-restconf-inventory-nodes.png)

- **Notifications**: These are also called as YANG-notifications. It is used to oublish one or more notifications with modeled-data to registered listeners. Defining a notification in YANG generates Java interfaces that potential listeners can "implement" and receive the appropriate callback.

  - In the tap application in our sample project, we illustrate listening to the node and node-connector updates and removal.Following is code written to listen for notifications defined in the 

    <span id="crayon-56bcd202b0bad921577829" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-v">opendaylight</span>
      <span class="crayon-o">-</span>
      <span class="crayon-v">inventory</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">yang</span>
    </span>
    </span>

    . 

    <span id="crayon-56bcd202b0bb5847372052" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-v">OpendaylightInventoryListener</span>
    </span>
    </span>

     is an autogenerated interface that defines the callback function called 

    <span id="crayon-56bcd202b0bbd248928623" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-e">onNodeUpdated</span>
      <span class="crayon-sy">(</span>
      <span class="crayon-sy">)</span>
    </span>
    </span>

    .

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">public</span>
  <span class="crayon-t">class</span>
  <span class="crayon-e">TapProvider </span>
  <span class="crayon-r">implements</span>
  <span class="crayon-v">OpendaylightInventoryListener</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">public</span>
  <span class="crayon-e">TapAppProvider</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-v">notificationService</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">registerNotificationListener</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-sy">@</span>
  <span class="crayon-e">Override</span>
</p>
  <p>
  <span class="crayon-m">public</span>
  <span class="crayon-t">void</span>
  <span class="crayon-e">onNodeUpdated</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-e">NodeUpdated </span>
  <span class="crayon-v">nodeUpdated</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-c">//Automatically called when node object is updated        </span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

- **Remote Procedure Call (RPC)**: MD-SAL allows one module to perform a procedure call with input/output to another module, without worrying about who the actual provider for that procedure is. MD-SAL handles the appropriate routing to map the caller and the callee.

  - In the tap application in our sample project, we illustrate performing a RPC call to clear all OpenFlow rules on the underlying switch. Unlike a notification, RPC calls have a unique recipient.Following is code written to perform a RPC call defined in the 

    <span id="crayon-56bcd202b0bcf185237618" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-v">sal</span>
      <span class="crayon-o">-</span>
      <span class="crayon-v">flow</span>
      <span class="crayon-sy">.</span>
      <span class="crayon-v">yang</span>
    </span>
    </span>

     part of the OpenFlowPlugin project. First step is to object a reference to the service project, and second step is to perform the YANG-defined function call with the appropriate input.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-m">public</span>
  <span class="crayon-t">class</span>
  <span class="crayon-i">TapProvider</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-m">public</span>
  <span class="crayon-e">TapAppProvider</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-r">this</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">salFlowService</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">rpcProviderRegistry</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">getRpcService</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">SalFlowService</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-t">class</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-m">public</span>
  <span class="crayon-t">void</span>
  <span class="crayon-e">procedure1</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-e">RemoveFlowInputBuilder </span>
  <span class="crayon-v">flowBuilder</span>
  <span class="crayon-o">=</span>
  <span class="crayon-r">new</span>
  <span class="crayon-e">RemoveFlowInputBuilder</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">flowBuilder</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">setBarrier</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-t">true</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-v">flowBuilder</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">setNode</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">nodeRef</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-c">//RPC to SAL Flow Service in OpenFlowPlugin</span>
</p>
  <p>
  <span class="crayon-v">salFlowService</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">removeFlow</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">flowBuilder</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">build</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# <span id="36_Northbound_and_Southbound_plugins">3.6 Northbound and Southbound plugins</span>

[![generic-controller-architecture](http://sdnhub.org/wp-content/uploads/2015/06/400xNxgeneric-controller-architecture.png.pagespeed.ic.SMnkyKTgIe.png)](http://sdnhub.org/wp-content/uploads/2015/06/generic-controller-architecture.png)Typically plugins are code abstractions used to integrate OpenDaylight with external systems. Following are some of the common plugins that app developers need to be familiar with.

- **Northbound plugins**: The two most commonly used northbound plugins are RESTconf (enabled by installing 

  <span id="crayon-56bcd202b0be1075775935" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">restconf</span>
  </span>
  </span>

  ) and NETCONF (enabled by installing 

  <span id="crayon-56bcd202b0be9118681929" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">netconf</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">mdsal</span>
  </span>
  </span>

   feature) interface to MD-SAL. Both of these can be used in combination with an intent layer.
- **Southbound plugins**: OpenFlow plugin (enabled by installing 

  <span id="crayon-56bcd202b0bf1429846800" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">openflowplugin</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">southbound</span>
  </span>
  </span>

   feature) and NETCONF connector (enabled by installing 

  <span id="crayon-56bcd202b0bfa644622034" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">netconf</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">connector</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">all</span>
  </span>
  </span>

   feature) are two commonly used southbound plugins.

  - The rest of the tutorial builds applications using OpenFlow plugin, wherein OpenFlow FLOW_MOD, PACKET_IN, PACKET_OUT and other discovery operations are supported as YANG modeled data changes, notifications or RPC calls.
  - For an example of an application or example using NETCONF connector, look at the post on [experimenting with NETCONF](http://sdnhub.org/tutorials/opendaylight-tutorial/experimenting-with-netconf-connector-in-opendaylight/) using the 

    <span id="crayon-56bcd202b0c03879644231" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
      <span class="crayon-pre crayon-code">
      <span class="crayon-v">netconf</span>
      <span class="crayon-o">-</span>
      <span class="crayon-v">exercise</span>
    </span>
    </span>

     application.

## <span id="4_Basic_steps_to_writing_an_OpenDayLight_Application">4\. Basic steps to writing an OpenDayLight Application</span>

For beginners, we recommend using Eclipse for viewing and editing the source code, and command-line for compiling and running the controller. Open Eclipse using the desktop shortcut.

We have already setup the Eclipse environment for you. But, if you wish to do it from scratch, you should follow [these instructions](https://wiki.opendaylight.org/view/OpenDaylight_Controller:Eclipse_CLI_Setup) to setup Maven-Eclipse integration. After that you can and go to File->Import. Choose 'Maven > Existing Maven Projects'. On the next screen, choose as the Root Directory ~/ubuntu/SDNHub_Opendaylight_Tutorial. Select 'learning-switch' and 'tapapp' implementation pom.xml that it finds, click on 'Add project(s) to working set' below, and click 'Finish'.

Once Eclipse is setup, you should now see 'learning-switch' and 'tapapp' implementation folders on your Project explorer pane. Double-click on one of the two and src/main/java -> org.sdnhub.odl.tutorial.[learning-switch or tapapp].impl and walk-through through the code.

### <span id="Step_1_Define_data_model">Step 1: Define data model</span>

The first step to developing an application with the OpenDaylight framework is to visualize the state maintained by your application and modeling that in the YANG language.

If you inspect 

<span id="crayon-56bcd202b0c0d155931352" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">tapapp</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">model</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">src</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">main</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">yang</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">tap</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">yang</span>
</span>
</span>

, you will see an example YANG module, where the container for archiving the tap configurations is defined. The container has a list within it to track each tap configuration. Each tap configuration contains information about the source, sink, type of traffic to capture, src/dst IP address, and src/dst MAC address.

Once you add the model, you can build the project and run Karaf to immediately verify if your model is sufficient for your application. For instance, when we run karaf, even without any implementation or event handlers defined, you will be able to store data in the data store as follows:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">curl</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">H</span>
  <span class="crayon-s">“Content-Type:application/json”</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">X</span>
  <span class="crayon-v">PUT</span>
  <span class="crayon-o">-</span>
  <span class="crayon-i">d</span>
  <span class="crayon-s">’{“tap-spec”:</span>
</p>
  <p>
  <span class="crayon-s"> {“tap”:[</span>
</p>
  <p>
  <span class="crayon-s">   {“id”:“1”,</span>
</p>
  <p>
  <span class="crayon-s">    “node”:“openflow:1”,</span>
</p>
  <p>
  <span class="crayon-s">    “traffic-match”:“HTTP”,</span>
</p>
  <p>
  <span class="crayon-s">    “src-node-connector”:[“openflow:1:1”],</span>
</p>
  <p>
  <span class="crayon-s">    “sink-node-connector”:[“openflow:1:2”]</span>
</p>
  <p>
  <span class="crayon-s">}’</span>
  <span class="crayon-v">http</span>
  <span class="crayon-o">:</span>
  <span class="crayon-c">//localhost:8181/restconf/config/tap:tap-spec</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

You can open a browser and inspect the data store at <http://localhost:8181/restconf/config/tap:tap-spec>. This is a big advantage with MD-SAL and developing applications with OpenDaylight, wherein you can directly access the data store using REST right after a model is defined and includes in the runtime of karaf. Here is a [postman collection](http://sdnhub.org/downloads/tutorials/opendaylight/tutorial-postman.json) you can use for the tap creation and deletion. That collection also contains some sample OpenFlowPlugin REST calls for flow programming.

### <span id="Step_2_Activation">Step 2: Activation</span>

In OpenDaylight, we add activation code within the 

<span id="crayon-56bcd202b0c20534029380" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-e">createInstance</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
</span>
</span>

 method of the YANG generated implementation module class. For instance, if you look at the auto-generated 

<span id="crayon-56bcd202b0c29748633180" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">TutorialTapAppModule</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">java</span>
</span>
</span>

, you will see the 

<span id="crayon-56bcd202b0c31761207298" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-e">createInstance</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
</span>
</span>

 method with code written to create necessary helper classes. Since most of OpenDaylight applications will primarily interact with the MD-SAL, it is important to extract references to the following necessary services:

1. **DataBroker**: As mentioned earlier, this object is necessary for read or write transaction with the data store
2. **NotificationProviderService**: This is the service to which any listener needs to register to receive YANG defined notifications
3. **RpcProviderRegistry**: This service helps connect a RPC provider with the consumer by providing the consumer with an instance of the RPC interface.

### <span id="Step_3_Event_handlers_and_other_call_backs">Step 3: Event handlers and other call backs</span>

Once you have a model defined and the class activated, the application should perform one or more of the following, typically in the constructor of the helper class:

1. register for data change notifications with the MD-SAL data broker to receive 

  <span id="crayon-56bcd202b0c3a129188302" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-e">onDataChanged</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-sy">)</span>
  </span>
  </span>

   callback
2. register for YANG notifications with the MD-SAL notification service to receive custom callbacks
3. register as a provider for certain RPC calls if this application is a provider
4. obtain access to the relevant RPC provider interfaces to make calls

Then, the helper class will implement all necessary logic within the call back function for the event handlers, and RPC provider functions. The application can also publish notifications of its own to other listeners using the notificationProviderService.publish() call.

In the sample 

<span id="crayon-56bcd202b0c43655059085" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">TutorialTapProvider</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">java</span>
</span>
</span>

, you can see the skeleton for the callback handler included along with some debug logs. Whenever you perform a tap config addition to the data store, you can see what is sent to the 

<span id="crayon-56bcd202b0c4c197016158" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-e">onDataChanged</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
</span>
</span>

 callback.

## <span id="5_Sample_application">5\. Sample application</span>

### <span id="51_Tap_application">5.1 Tap application</span>

The traffic monitoring tap application is a simple proactive flow programmer that deals with source, sinks and traffic types. We will follow the steps described earlier to build this application. The model of the tap configuration data was described earlier in [Step 1](http://sdnhub.org/tutorials/opendaylight/#Step_1_Define_data_model). Based on that model, we perform following to make the tap work:

1. Extract header details from the tap object during the 

  <span id="crayon-56bcd202b0c54820204648" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-e">onDataChanged</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-sy">)</span>
  </span>
  </span>

   event handling
2. For each source-port, perform following steps to create a flow

  1. Create match object using appropriate builders
  2. Create action list with a list of actions specifying output to sink-port
  3. Create flow object with match and action list. Write this flow object to the Flow table of the node

Note that the traffic type is a special enum field defined in the YANG model to allow user to specific important traffic types worth monitoring, like ARP, ICMP, DNS, DHCP, TCP, UDP. For converting the enum to actual values for the 

<span id="crayon-56bcd202b0c5d007138944" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">dl_type</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">nw_proto </span>
  <span class="crayon-st">and</span>
  <span class="crayon-v">tp_port</span>
</span>
</span>

, one can use the following switch statement:

 | <div class="crayon-pre">
  <p>
  <span class="crayon-t">Integer</span>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-t">null</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">Short</span>
  <span class="crayon-v">nwProto</span>
  <span class="crayon-o">=</span>
  <span class="crayon-t">null</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-t">Integer</span>
  <span class="crayon-v">tpDst</span>
  <span class="crayon-o">=</span>
  <span class="crayon-t">null</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">switch</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">tap</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">getTrafficMatch</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">{</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-v">ARP</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x806</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-v">ICMP</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x800</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">nwProto</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-v">TCP</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x800</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">nwProto</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">6</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-v">HTTP</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x800</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">nwProto</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">6</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">tpDst</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">80</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-v">HTTPS</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x800</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">nwProto</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">6</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">tpDst</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">443</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-v">UDP</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x800</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">nwProto</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x11</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-v">DNS</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x800</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">nwProto</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x11</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">tpDst</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">53</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">case</span>
  <span class="crayon-v">DHCP</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-v">dlType</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x800</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">nwProto</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">0x11</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-v">tpDst</span>
  <span class="crayon-o">=</span>
  <span class="crayon-cn">67</span>
  <span class="crayon-sy">;</span>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
  <p>
  <span class="crayon-st">default</span>
  <span class="crayon-o">:</span>
</p>
  <p>
  <span class="crayon-st">break</span>
  <span class="crayon-sy">;</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In the skeleton code provided in the 

<span id="crayon-56bcd202b0c6f045393856" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">TutorialTapProvider</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">java</span>
</span>
</span>

 in the 

<span id="crayon-56bcd202b0c77212037658" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">tapapp</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">impl</span>
</span>
</span>

 directory, you will see the activation code already included. You can complete the rest based on the above logic.

### <span id="52_Learning_Switch">5.2 Learning Switch</span>

For the purposes of this tutorial, you should attempt to convert the hub learning switch to a MAC learning switch that programs flows. We encourage you to add the necessary code within 

<span id="crayon-56bcd202b0c7f048070154" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">TutorialL2Forwarding</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-v">java</span>
</span>
</span>

. The main logic for hub and learning switch are **[available here](http://sdnhub.org/tutorials/app-pseudo-code/)**. At a high-level here are the steps to perform:

1. Ignore LLDP packets
2. If behaving as "hub", perform a PACKET_OUT with FLOOD action
3. Else if behaving as "learning switch",

  1. Extract MAC addresses
  2. Update MAC table with source MAC address
  3. Lookup in MAC table for the target node connector of dst_mac

    1. If found, perform FLOW_MOD for that dst_mac through the target node connector, and perform PACKET_OUT of this packet to target node connector
    2. If not found, perform a PACKET_OUT with FLOOD action

### <span id="53_Your_homework">5.3 Your homework</span>

1. Complete the learning switch to perform flow programming such that the Open vSwitch switch "s1″ will have a single rule matching the destination MAC address and the latency of the ping between h1 and h2 will be < 1ms, and you will see output as follows in the mininet window.

   | <div class="crayon-pre">
    <p>
    <span class="crayon-v">mininet</span>
    <span class="crayon-o">&gt;</span>
    <span class="crayon-e">h1 </span>
    <span class="crayon-e">ping </span>
    <span class="crayon-e">h2</span>
  </p>
    <p>
    <span class="crayon-i">PING</span>
    <span class="crayon-cn">10.0.0.2</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-cn">10.0.0.2</span>
    <span class="crayon-sy">)</span>
    <span class="crayon-cn">56</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-cn">84</span>
    <span class="crayon-sy">)</span>
    <span class="crayon-e">bytes </span>
    <span class="crayon-e">of </span>
    <span class="crayon-v">data</span>
    <span class="crayon-sy">.</span>
  </p>
    <p>
    <span class="crayon-i">From</span>
    <span class="crayon-cn">10.0.0.1</span>
    <span class="crayon-v">icmp_seq</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">1</span>
    <span class="crayon-e">Destination </span>
    <span class="crayon-e">Host </span>
    <span class="crayon-e">Unreachable</span>
  </p>
    <p>
    <span class="crayon-i">From</span>
    <span class="crayon-cn">10.0.0.1</span>
    <span class="crayon-v">icmp_seq</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">2</span>
    <span class="crayon-e">Destination </span>
    <span class="crayon-e">Host </span>
    <span class="crayon-e">Unreachable</span>
  </p>
    <p>
    <span class="crayon-i">From</span>
    <span class="crayon-cn">10.0.0.1</span>
    <span class="crayon-v">icmp_seq</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">3</span>
    <span class="crayon-e">Destination </span>
    <span class="crayon-e">Host </span>
    <span class="crayon-e">Unreachable</span>
  </p>
    <p>
    <span class="crayon-i">From</span>
    <span class="crayon-cn">10.0.0.1</span>
    <span class="crayon-v">icmp_seq</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">4</span>
    <span class="crayon-e">Destination </span>
    <span class="crayon-e">Host </span>
    <span class="crayon-i">Unreachable</span>
  </p>
    <p>
    <span class="crayon-cn">64</span>
    <span class="crayon-e">bytes </span>
    <span class="crayon-i">from</span>
    <span class="crayon-cn">10.0.0.2</span>
    <span class="crayon-o">:</span>
    <span class="crayon-v">icmp_req</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">5</span>
    <span class="crayon-v">ttl</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">64</span>
    <span class="crayon-v">time</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0.529</span>
    <span class="crayon-i">ms</span>
  </p>
    <p>
    <span class="crayon-cn">64</span>
    <span class="crayon-e">bytes </span>
    <span class="crayon-i">from</span>
    <span class="crayon-cn">10.0.0.2</span>
    <span class="crayon-o">:</span>
    <span class="crayon-v">icmp_req</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">6</span>
    <span class="crayon-v">ttl</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">64</span>
    <span class="crayon-v">time</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0.133</span>
    <span class="crayon-i">ms</span>
  </p>
    <p>
    <span class="crayon-cn">64</span>
    <span class="crayon-e">bytes </span>
    <span class="crayon-i">from</span>
    <span class="crayon-cn">10.0.0.2</span>
    <span class="crayon-o">:</span>
    <span class="crayon-v">icmp_req</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">7</span>
    <span class="crayon-v">ttl</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">64</span>
    <span class="crayon-v">time</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0.047</span>
    <span class="crayon-v">ms</span>
  </p>
  </div>
   | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

2. Convert the MAC table in the 

  <span id="crayon-56bcd202b0c91879267042" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">TutorialL2Forwarding</span>
    <span class="crayon-sy">.</span>
    <span class="crayon-v">java</span>
  </span>
  </span>

   from a local hashmap to an augmented data in the MD-SAL data store. Perhaps within the 

  <span id="crayon-56bcd202b0c9a172587182" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">openDaylight</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">inventory</span>
  </span>
  </span>

   root like follows:

   | <div class="crayon-pre">
    <p>
    <span class="crayon-v">augment</span>
    <span class="crayon-o">/</span>
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">inv</span>
    <span class="crayon-o">:</span>
    <span class="crayon-v">nodes</span>
    <span class="crayon-o">/</span>
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">inv</span>
    <span class="crayon-o">:</span>
    <span class="crayon-e">node</span>
    <span class="crayon-sy">{</span>
  </p>
    <p>
    <span class="crayon-v">ext</span>
    <span class="crayon-o">:</span>
    <span class="crayon-v">augment</span>
    <span class="crayon-o">-</span>
    <span class="crayon-i">identifier</span>
    <span class="crayon-s">“MacTable”</span>
    <span class="crayon-sy">;</span>
  </p>
    <p>
    <span class="crayon-e">list</span>
    <span class="crayon-e">hosts</span>
    <span class="crayon-sy">{</span>
  </p>
    <p>
    <span class="crayon-e">leaf</span>
    <span class="crayon-e">mac</span>
    <span class="crayon-o">-</span>
    <span class="crayon-e">address</span>
    <span class="crayon-sy">{</span>
  </p>
    <p>
    <span class="crayon-e">type </span>
    <span class="crayon-v">yang</span>
    <span class="crayon-o">:</span>
    <span class="crayon-v">mac</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">address</span>
    <span class="crayon-sy">;</span>
  </p>
    <p>
    <span class="crayon-e">leaf</span>
    <span class="crayon-e">attachment</span>
    <span class="crayon-o">-</span>
    <span class="crayon-e">point</span>
    <span class="crayon-sy">{</span>
  </p>
    <p>
    <span class="crayon-c">//I choose simple one</span>
  </p>
    <p>
    <span class="crayon-e">type </span>
    <span class="crayon-v">inet</span>
    <span class="crayon-o">:</span>
    <span class="crayon-v">uri</span>
    <span class="crayon-sy">;</span>
  </p>
  </div>
   | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

3. Complete the tap application 

  <span id="crayon-56bcd202b0caa709390901" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-e">onDataChanged</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-sy">)</span>
  </span>
  </span>

   so that the OpenFlow rules are programmed when tap configurations are received. For instance, once the data is inserted using the curl (like in the example above), the rule will be pushed to the switch underneath as follows:

   | <div class="crayon-pre">
    <p>
    <span class="crayon-sy">$</span>
    <span class="crayon-e">sudo </span>
    <span class="crayon-v">ovs</span>
    <span class="crayon-o">-</span>
    <span class="crayon-e">ofctl </span>
    <span class="crayon-v">dump</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">flows</span>
    <span class="crayon-o">-</span>
    <span class="crayon-e">OOpenFlow13 </span>
    <span class="crayon-e">s1</span>
  </p>
    <p>
    <span class="crayon-e">OFPST_FLOW </span>
    <span class="crayon-e">reply</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-v">OF1</span>
    <span class="crayon-sy">.</span>
    <span class="crayon-cn">3</span>
    <span class="crayon-sy">)</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-v">xid</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0x2</span>
    <span class="crayon-sy">)</span>
    <span class="crayon-o">:</span>
  </p>
    <p>
    <span class="crayon-v">cookie</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0x0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">duration</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">477.342s</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">table</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">n_packets</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">n_bytes</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">tcp</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">in_port</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">1</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">tp_dst</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">80</span>
    <span class="crayon-v">actions</span>
    <span class="crayon-o">=</span>
    <span class="crayon-v">output</span>
    <span class="crayon-o">:</span>
    <span class="crayon-cn">2</span>
  </p>
  </div>
   | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

### <span id="54_Solutions">5.4 Solutions</span>

In the impl directory of the two applications, you will see *.solution files that implement the two applications. Copy them over the .java files and recompile to see it in action.

### 

1. Extend the learning switch to work with multiple switches. The solution code only works with single switch. In mininet, you can spawn a larger tree topology using following:

   | <div class="crayon-pre">
    <p>
    <span class="crayon-v">ubuntu</span>
    <span class="crayon-sy">@</span>
    <span class="crayon-v">sdnhubvm</span>
    <span class="crayon-o">:</span>
    <span class="crayon-o">~</span>
    <span class="crayon-sy">[</span>
    <span class="crayon-cn">22</span>
    <span class="crayon-o">:</span>
    <span class="crayon-cn">11</span>
    <span class="crayon-sy">]</span>
    <span class="crayon-sy">$</span>
    <span class="crayon-e">sudo </span>
    <span class="crayon-v">mn</span>
    <span class="crayon-o">–</span>
    <span class="crayon-e">topo </span>
    <span class="crayon-v">tree</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-cn">2</span>
    <span class="crayon-o">–</span>
    <span class="crayon-v">mac</span>
    <span class="crayon-o">–</span>
    <span class="crayon-st">switch</span>
    <span class="crayon-v">ovsk</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">protocols</span>
    <span class="crayon-o">=</span>
    <span class="crayon-v">OpenFlow13</span>
    <span class="crayon-o">–</span>
    <span class="crayon-e">controller </span>
    <span class="crayon-v">remote</span>
  </p>
  </div>
   | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

2. Support matching tap traffic in both direction, such that the rule programmed will look as follows:

   | <div class="crayon-pre">
    <p>
    <span class="crayon-sy">$</span>
    <span class="crayon-e">sudo </span>
    <span class="crayon-v">ovs</span>
    <span class="crayon-o">-</span>
    <span class="crayon-e">ofctl </span>
    <span class="crayon-v">dump</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">flows</span>
    <span class="crayon-o">-</span>
    <span class="crayon-e">OOpenFlow13 </span>
    <span class="crayon-e">s1</span>
  </p>
    <p>
    <span class="crayon-e">OFPST_FLOW </span>
    <span class="crayon-e">reply</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-v">OF1</span>
    <span class="crayon-sy">.</span>
    <span class="crayon-cn">3</span>
    <span class="crayon-sy">)</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-v">xid</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0x2</span>
    <span class="crayon-sy">)</span>
    <span class="crayon-o">:</span>
  </p>
    <p>
    <span class="crayon-v">cookie</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0x0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">duration</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">477.342s</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">table</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">n_packets</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">n_bytes</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">tcp</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">in_port</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">1</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">tp_dst</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">80</span>
    <span class="crayon-v">actions</span>
    <span class="crayon-o">=</span>
    <span class="crayon-v">output</span>
    <span class="crayon-o">:</span>
    <span class="crayon-cn">2</span>
  </p>
    <p>
    <span class="crayon-v">cookie</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0x0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">duration</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">477.364s</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">table</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">n_packets</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">n_bytes</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">tcp</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">in_port</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">1</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">tp_src</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">80</span>
    <span class="crayon-v">actions</span>
    <span class="crayon-o">=</span>
    <span class="crayon-v">output</span>
    <span class="crayon-o">:</span>
    <span class="crayon-cn">2</span>
  </p>
  </div>
   | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

3. Define a RPC call in YANG called "create-tap" and "delete-tap", and make the 

  <span id="crayon-56bcd202b0ccc326753442" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">TutorialTapProvider</span>
  </span>
  </span>

   class as a provider for those RPC calls.

4. Save the IP and TCP header information data, derived from the traffic type, as augmentation to the tap configuration (Note: The 

  <span id="crayon-56bcd202b0cd4280074172" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">tap</span>
    <span class="crayon-sy">.</span>
    <span class="crayon-v">yang</span>
  </span>
  </span>

   already defines this in the bottom)
5. Test persistence of tap configuration across reboot.

## <span id="6_Other_useful_Features">6\. Other useful Features</span>

The OpenDaylight distribution has some pre-written applications that will help develop and debug your application. Previously, we learned about how useful the 

<span id="crayon-56bcd202b0cdd944439012" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
  <span class="crayon-pre crayon-code">
  <span class="crayon-v">odl</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">restconf</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">noauth</span>
</span>
</span>

 feature can be in inspecting the data store. Besides that, we encourage you to also play with the following features:<br>
[![odl-mdsal-apidocs-explorer](http://sdnhub.org/wp-content/uploads/2013/11/294x300xodl-mdsal-apidocs-explorer-294x300.png.pagespeed.ic.WAtaT12LKH.jpg)](http://sdnhub.org/wp-content/uploads/2013/11/odl-mdsal-apidocs-explorer.png)

- **NETCONF client**: Install the 

  <span id="crayon-56bcd202b0ce6460757821" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">netconf</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">connector</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">all</span>
  </span>
  </span>

   feature to enable the OpenDaylight controller to configure and manage underlying devices using NETCONF protocol; typically, devices (like routers) with traditional control plane provide this NETCONF option for injecting configuration and performing RPC operations on the device. Here is a [tutorial post](http://sdnhub.org/tutorials/opendaylight-tutorial/experimenting-with-netconf-connector-in-opendaylight/) for experimenting with NETCONF support and using that to orchestrate non-OpenFlow devices.
- **l2switch**: Has code for ARP handlers, host tracking, more complex L2 switch. You can model your applications after this. Alternatively, you can install this 

  <span id="crayon-56bcd202b0cef955830403" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">l2switch</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">all</span>
  </span>
  </span>

   feature to test your switch before doing other applications.
- **dlux**: Dlux is the AngularJS-based web UI of OpenDaylight. It has several build in modules to visualize the data cached in the data store. Install 

  <span id="crayon-56bcd202b0cf8837243903" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">dlux</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">all</span>
  </span>
  </span>

   feature and visit <http://localhost:8181/dlux>
- **Link discovery**: Although we have used OpenFlow plugin in both the above applications, we did not use all its features. The plugin also tracks topology and adds it to the 

  <span id="crayon-56bcd202b0d00513553465" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">network</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">topology</span>
    <span class="crayon-sy">.</span>
    <span class="crayon-v">yang</span>
  </span>
  </span>

   model. You can view all LLDP discovered links from <http://localhost:8181/restconf/operational/network-topology:network-topology>. (**Quiz**: How to get notifications on new link additions? **Answer**: Register a data change listener for the link instance identifier)
- **apidocs**: OpenDaylight provides an interface to get a list of all the defined data models, notifications and RPC calls at runtime. To get list of all APIs exported over RESTConf, you can use the API explorer. Install feature 

  <span id="crayon-56bcd202b0d09640095296" class="crayon-syntax crayon-syntax-inline crayon-theme-classic crayon-theme-classic-inline crayon-font-monaco">
    <span class="crayon-pre crayon-code">
    <span class="crayon-v">odl</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">mdsal</span>
    <span class="crayon-o">-</span>
    <span class="crayon-v">apidocs</span>
  </span>
  </span>

   and see the list of API by accessing <http://localhost:8080/apidoc/explorer/>. On the right is an example of list of API.We encourage you to play with this data access and possibly create new flows through this REST interface. Once you create a new flow, you can see it in the Open vSwitch table using the command:<br>

  | <div class="crayon-pre">
    <p>
    <span class="crayon-sy">$</span>
    <span class="crayon-e">sudo </span>
    <span class="crayon-v">ovs</span>
    <span class="crayon-o">-</span>
    <span class="crayon-e">ofctl </span>
    <span class="crayon-v">dump</span>
    <span class="crayon-o">-</span>
    <span class="crayon-e">flows </span>
    <span class="crayon-v">s1</span>
    <span class="crayon-o">-</span>
    <span class="crayon-i">O</span>
    <span class="crayon-e">OpenFlow13</span>
  </p>
    <p>
    <span class="crayon-e">OFPST_FLOW </span>
    <span class="crayon-e">reply</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-v">OF1</span>
    <span class="crayon-sy">.</span>
    <span class="crayon-cn">3</span>
    <span class="crayon-sy">)</span>
    <span class="crayon-sy">(</span>
    <span class="crayon-v">xid</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0x2</span>
    <span class="crayon-sy">)</span>
    <span class="crayon-o">:</span>
  </p>
    <p>
    <span class="crayon-v">cookie</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0x0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">duration</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">35.255s</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">table</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">n_packets</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">22</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">n_bytes</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">1668</span>
    <span class="crayon-sy">,</span>
    <span class="crayon-v">priority</span>
    <span class="crayon-o">=</span>
    <span class="crayon-cn">0</span>
    <span class="crayon-v">actions</span>
    <span class="crayon-o">=</span>
    <span class="crayon-v">CONTROLLER</span>
    <span class="crayon-o">:</span>
    <span class="crayon-cn">65535</span>
  </p>
    <p><span class="crayon-v">cookie</span><span class="crayon-o">=</span><span class="crayon-cn">0x2a00000000000000</span><span class="crayon-sy">,</span><span class="crayon-v">duration</span><span class="crayon-o">=</span><span class="crayon-cn">35.248s</span><span class="crayon-sy">,</span><span class="crayon-v">table</span><span class="crayon-o">=</span><span class="crayon-cn">0</span><span class="crayon-sy">,</span><span class="crayon-v">n_packets</span><span class="crayon-o">=</span><span class="crayon-cn">36</span><span class="crayon-sy">,</span><span class="crayon-v">n_bytes</span><span class="crayon-o">=</span><span class="crayon-cn">3416</span><span class="crayon-sy">,</span><span class="crayon-v">priority</span><span class="crayon-o">=</span><span class="crayon-cn">512</span><span class="crayon-sy">,</span><span class="crayon-v">dl_src</span><span class="crayon-o">=</span><span class="crayon-cn">00</span><span class="crayon-o">:</span><span class="crayon-cn">00</span><span class="crayon-o">:</span><span class="crayon-cn">00</span><span class="crayon-o">:</span><span class="crayon-cn">00</span><span class="crayon-o">:</span><span class="crayon-cn">00</span><span class="crayon-o">:</span><span class="crayon-cn">02</span><span class="crayon-sy">,</span><span class="crayon-v">dl_dst</span><span class="crayon-o">=</span>&lt;span clas

  </p>
  </div>
   | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
s="crayon-cn">00

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">01</span>

<span class="crayon-v">actions</span>

<span class="crayon-o">=</span>

<span class="crayon-v">output</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">2</span>

<span class="crayon-v">cookie</span>

<span class="crayon-o">=</span>

<span class="crayon-cn">0x2a00000000000001</span>

<span class="crayon-sy">,</span>

<span class="crayon-v">duration</span>

<span class="crayon-o">=</span>

<span class="crayon-cn">35.247s</span>

<span class="crayon-sy">,</span>

<span class="crayon-v">table</span>

<span class="crayon-o">=</span>

<span class="crayon-cn">0</span>

<span class="crayon-sy">,</span>

<span class="crayon-v">n_packets</span>

<span class="crayon-o">=</span>

<span class="crayon-cn">35</span>

<span class="crayon-sy">,</span>

<span class="crayon-v">n_bytes</span>

<span class="crayon-o">=</span>

<span class="crayon-cn">3374</span>

<span class="crayon-sy">,</span>

<span class="crayon-v">priority</span>

<span class="crayon-o">=</span>

<span class="crayon-cn">512</span>

<span class="crayon-sy">,</span>

<span class="crayon-v">dl_src</span>

<span class="crayon-o">=</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">01</span>

<span class="crayon-sy">,</span>

<span class="crayon-v">dl_dst</span>

<span class="crayon-o">=</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">00</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">02</span>

<span class="crayon-v">actions</span>

<span class="crayon-o">=</span>

<span class="crayon-v">output</span>

<span class="crayon-o">:</span>

<span class="crayon-cn">1</span>



# <span id="7_Debugging">7\. Debugging</span>

Debugging is an important part of app development. With Eclipse (and other IDE), you will be able to connect to the karaf instance and perform a step-wise debug of your code. To debug your code, you need to start OpenDaylight including the command like parameter "debug", which will allow IDE (e.g., Eclipse) to connect to Karaf (listening at port 5005) for debugging purposes.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">SDNHub_Opendaylight_Tutorial</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-e">cd </span>
  <span class="crayon-v">distribution</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">opendaylight</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">karaf</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">target</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">assembly</span>
</p>
  <p>
  <span class="crayon-v">ubuntu</span>
  <span class="crayon-sy">@</span>
  <span class="crayon-v">sdnhubvm</span>
  <span class="crayon-o">:</span>
  <span class="crayon-o">~</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">SDNHub_Opendaylight_Tutorial</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">distribution</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">opendaylight</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">karaf</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">target</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">assembly</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-o">/</span>
  <span class="crayon-v">bin</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">karaf </span>
  <span class="crayon-v">debug</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Once you kick off karaf in debug mode, you can connect to that JVM from Eclipse by clicking on Debug -> Configurations and adding a new Remote Java Application as shown in the figure below. Once you have that entered and connected to the JVM, you can add breakpoints in your code to break at specific lines.<br>
[![eclipse_debug_configurations](http://sdnhub.org/wp-content/uploads/2015/06/Nx350xeclipse_debug_configurations.png.pagespeed.ic.ZWpdf8S3of.jpg)](http://sdnhub.org/wp-content/uploads/2015/06/eclipse_debug_configurations.png)<br>
If execution breaks at the line, you should be able to see the Debug tab, where you can decide how to proceed and debug.<br>
[![opendaylight_debug_tab](http://sdnhub.org/wp-content/uploads/2013/11/800xNxopendaylight_debug_tab.png.pagespeed.ic.lYScpUcibJ.jpg)](http://sdnhub.org/wp-content/uploads/2013/11/opendaylight_debug_tab.png)