***Quick Start Guide to using DNA Nexus***

This is a quick crib sheet to help bioinformaticians get up to speed with developing pipelines in DNA Nexus, it assumes familiarity with the Linux commandline. The :link: symbol indicates an external link to more info.

DNA Nexus is a cloud-based provider offering a platform to run genomic analysis and runs it's service on Amazon Web Service (AWS) infrastructure, although it also supports Microsoft Azure. 

***Access DNA Nexus via your web browser*** 

https://www.dnanexus.com/

Monitor jobs

***Install DNA Nexus commandline client & upload agent***

Instructions for installing the DNAnexus Platform SDK can be found [here](https://wiki.dnanexus.com/Downloads#DNAnexus-Platform-SDK).  This allows you to interacte with the DNA nexus platform from the commandline.

There is a good [Quick Start Guide](https://wiki.dnanexus.com/Command-Line-Client/Quickstart) to using the SDK and a comprehensive list of commands can be found [here](https://wiki.dnanexus.com/Command-Line-Client/Index-of-dx-Commands).  Don't spend too much time learning how to build DNA nexus apps/applets, we will be using dxWDL and docker to streamline this process as described below.

The DNAnexus Upload Agent is a command-line client used to upload files to DNAnexus, download instructions can be found [here](https://wiki.dnanexus.com/Upload-Agent).

***Log into DNA Nexus via commandline***

```bash
dx login
```

Enter your username and password when prompted.

Set up API key

***Create DNA Nexus Interactive Workstation***

For development purposes it can be helpful to create an interactive session with a DNA Nexus workstation in the cloud [:link:](https://wiki.dnanexus.com/developer-tutorials/cloud-workstations).  We can use this instance to test/troubleshoot commands for bioinformatic tools we plan to use in the pipeline. 

The default instance only has 4 cores, below we specify an instance with 8 cores.

```bash
dx run --instance-type mem1_ssd1_x8 app-cloud_workstation --ssh
```

A full list of instance types for AWS can be found [here](https://wiki.dnanexus.com/API-Specification-v1.0.0/Instance-Types#).  Make sure the instance type you choose has sufficient memory for your purposes. 

We then need to reset some environment variables so that we can access files other projects:

```bash
unset DX_WORKSPACE_ID
dx cd $DX_PROJECT_CONTEXT_ID:
```

To download a file named ```my-file.txt``` from the parent project:

```bash
dx download my-file.txt
```

***Using docker with an Interactive Workstation***

Using docker images simplifies the process of creating our pipeline and will make it more robust  

***Reconnect to an Interactive Workstation***

If you loose internet connection or logout of a terminal connected to a worksation will persist until it reaches its preset time limit.  You can reconnect to that work station using the following command and specifying the relevan job ID (this can be found via the browser

```bash
dx ssh job-FQyBvF70Vy577bQq1vZgVXpa
```

***Terminate a workstation***

I prefer to terminate the workstation instance from the browser.  This allows shows me the cost of running that workstation and allows me to check if any other instances are open under that project.

Navigate to the project which the workstation was run from, click ```Monitor``` from the taskbar, select the appropriate Cloud Workstation, and then click terminate. 

***Create pipeline with dxWDL***

When writing WDL scripts we can download a syntax highlighter for VS Code or Sublime 
(https://github.com/broadinstitute/wdl-sublime-syntax-highlighter) which helps when editing the code.

WDL[:link:](https://software.broadinstitute.org/wdl/) is a language for defining workflows developed at the Broad Institute which can be compiled into a pipeline.  DNA Nexus supports it's own platform specific compiler, [dxWDL](https://github.com/dnanexus/dxWDL), which will take a WDL script and compile it into a functional pipeline on their platform.

An example script can be seen below:

```wdl

```

***DNA Nexus and Continous Integration with Travis CI***

DNA nexus provides limited support for continous integration with [Travis](https://gist.github.com/mlin/3ad81f01efa640a52813).  This allows automated testing of the pipeline which should make maintenance and upgrades easier.


