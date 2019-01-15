***Quick Start Guide to using DNA Nexus***

This is a quick crib sheet to help bioinformaticians get up to speed with developing pipelines in DNA Nexus, it assumes familiarity with the Linux commandline. The :link: symbol indicates a link to an external site with additional info.

***Install DNA Nexus commandline client & upload agent****

Instructions for installing the dx-toolkit and upload agent can be found here and here

***Log into DNA Nexus***

```bash
dx login
```

Enter your username and password when prompted.

Set up API key

***Create DNA Nexus Interactive Workstation***

For development purposes it can be helpful to create an interactive session with a DNA Nexus workstation in the cloud [:link:](https://wiki.dnanexus.com/developer-tutorials/cloud-workstations):


```bash
dx run --instance-type mem1_ssd1_x8 app-cloud_workstation --ssh
```
***Using docker with an Interactive Workstation ***

***Reconnect to an Interactive Workstation***

***Create pipeline with dxWDL***

When writing WDL scripts we can download a syntax highlighter for VS Code or Sublime 
(https://github.com/broadinstitute/wdl-sublime-syntax-highlighter) which helps when editing the code.

WDL is 

```wdl

```


