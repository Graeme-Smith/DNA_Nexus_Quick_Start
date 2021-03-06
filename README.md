***Quick Start Guide to using DNA Nexus***

This is a quick crib sheet to help bioinformaticians get up to speed with developing pipelines in DNA Nexus, it assumes familiarity with the Linux command line. The :link: symbol indicates an external link to more info.

DNA Nexus is a cloud-based provider offering a platform to run genomic analysis and runs it's service on Amazon Web Service (AWS) infrastructure, although it also supports Microsoft Azure.   

***Access DNA Nexus via your web browser*** 

Log in to your account at https://www.dnanexus.com/

Select the project which you are working on.  There should be 4 tabs in the taskbar:

Settings: 
* Get the project ID (Useful when navigating via the command line)
* Set billing info
* Change project name

Manage:
* See files uploaded to the project
* To find file ID - Right click file > Info 

Monitor:
* Monitor running jobs
* Terminate jobs

Visualise

Once created workflows can be run from within the browser.

***Install DNA Nexus command line client & upload agent***

Instructions for installing the DNAnexus Platform SDK can be found [here](https://wiki.dnanexus.com/Downloads#DNAnexus-Platform-SDK).  This allows you to interact with the DNA nexus platform from the command line.

There is a good [Quick Start Guide](https://wiki.dnanexus.com/Command-Line-Client/Quickstart) to using the SDK and a comprehensive list of commands can be found [here](https://wiki.dnanexus.com/Command-Line-Client/Index-of-dx-Commands).  Don't spend too much time learning how to build DNA nexus apps/applets, we will be using dxWDL and docker to streamline this process as described below.

The DNAnexus Upload Agent is a command-line client used to upload files to DNAnexus, download instructions can be found [here](https://wiki.dnanexus.com/Upload-Agent).

***Log into DNA Nexus via command line***

Run 
```bash
dx ssh_config
```

Select 0 to generate new SSH key.

```bash
dx login
```

Enter your username and password when prompted, then select the project you want to work in.

Logging in will set up a session API token on your system, so one login will allow you to use the SDK and upload agent.  If you need to set up persistant API tokens, for example to give a program access to your DNA nexus projects, these can be generated by navigating to your name in the taskbar > Profile > API tokens.  You can generate and manage API tokens from here.  NOTE: 1) Don't include your API token in any code pushed to Github, 2) Set the appropriate access for the API token (Upload, Viewer, Admin etc), 3) Make a reminder for when the API token will run out. 

***Create DNA Nexus Interactive Workstation***

For development purposes it can be helpful to create an interactive session with a DNA Nexus workstation in the cloud [:link:](https://wiki.dnanexus.com/developer-tutorials/cloud-workstations).  We can use this instance to test/troubleshoot commands for bioinformatic tools we plan to use in the pipeline. 

The default instance only has 4 cores, below we specify an instance with 8 cores.

```bash
dx run --instance-type mem1_ssd1_x8 app-cloud_workstation --ssh
```

A full list of instance types for AWS can be found [here](https://wiki.dnanexus.com/API-Specification-v1.0.0/Instance-Types#).  Make sure the instance type you choose has sufficient memory for your purposes.  Select a maximum life span for this Cloud Workstation, for example 24h. 

We then need to reset some environment variables so that we can access files in other projects:

```bash
unset DX_WORKSPACE_ID
dx cd $DX_PROJECT_CONTEXT_ID:
```

We need to download files from the project to our Cloud Workstation. To download a file named ```my-file.txt``` from the parent project:

```bash
dx download my-file.txt
```
When the Cloud Workstation is terminated all files will be lost.  We therefore need to upload any output we want to keep back up to the parent project:

```bash
# Upload single file to project home directory:
dx upload --path "$DX_PROJECT_CONTEXT_ID:" my-output.txt

#Upload a directory (note the -r recursive flag and that we now specify the directory name to --path)
dx upload -r --path "$DX_PROJECT_CONTEXT_ID:/demultiplexed_output/" demultiplexed_output/
```

***Using docker with an Interactive Workstation***

Using docker images simplifies the process of creating our pipeline and will make it more robust (an update to a dependency shouldn't break the pipeline).  DNA nexus now supports the docker CLI natively, see [here](https://wiki.dnanexus.com/Developer-Tutorials/Using-Docker-Images).  DNA nexus recommend pulling public images from an immutable image digest so that there will be no unexpected changes to tool if a dependency changes.  When the pipeline is in production we can store these docker images locally on the DNA nexus platform so that we are not reliant on quay.io for our pipeline to work.

The easiest way to find the relevant docker image is to search for the tool on https://bioconda.github.io/recipes.html. There will be a link to an available docker container hosted on [quay.io](quay.io]), click on the Tag icon on the left hand toolbar > click the download icon to the right of the version you want > select Docker Pull (by digest).  Copy this tag for use with DNA nexus commands and in scripts.

#TODO Add docker image code

```bash

```

***Reconnect to an Interactive Workstation***

If you loose your internet connection or logout of a terminal connection the cloud workstation will continue to persist until it reaches its preset time limit.  You can reconnect to that work station using the following command specifying the relevant job ID (this can be found via the browser; navigate to the project, select ```Monitor``` from the taskbar, and  clicking the relevant Cloud Workstation in the Name column.

```bash
dx ssh job-FQyBvF70Vy577bQq1vZgVXpa
```

***Terminate a workstation***

I prefer to terminate the workstation instance from the browser.  This shows me the cost of running that workstation and allows me to check if any other instances are open under that project.

Navigate to the project which the workstation was run from, click ```Monitor``` from the taskbar, select the appropriate Cloud Workstation, and then click terminate (the ```Terminate``` button only appears when hovering over the line for that Cloud Workstation). 

***Create pipeline with dxWDL***

When writing WDL scripts we can download a syntax highlighter for VS Code or Sublime 
(https://github.com/broadinstitute/wdl-sublime-syntax-highlighter) which helps when editing the code.

WDL[:link:](https://software.broadinstitute.org/wdl/) is a language for defining workflows developed at the Broad Institute which can be compiled into a pipeline.  DNA Nexus supports it's own platform specific compiler, [dxWDL](https://github.com/dnanexus/dxWDL), which will take a WDL script and compile it into a functional pipeline on their platform.

An example script can be seen below:

#TODO add example code

```wdl
# 16S rRNA pipeline written in WDL

# WDL syntax highlighting for Sublime and VS Code an be found at https://github.com/broadinstitute/wdl-sublime-syntax-highlighter

# NOTE: Validate syntax of workflow with wdltool (https://software.broadinstitute.org/wdl/documentation/validation)

# Define workflow
workflow nanopore_16s_pipeline {
    # Specify inputs
    File fastq

    # Call task to demultiplex fastq files
    call demultiplex_fastq {
        input : fastq = fastq
    }
    output {
        dm_multiplex.fastq
    }
}
```
***View data in DNA nexus***

[Custom viewers](https://wiki.dnanexus.com/Developer-Tutorials/Creating-Custom-Viewers) can be created if you have a working knowledge of HTML and Javascript.

***DNA Nexus and Continous Integration with Travis CI***

DNA nexus provides limited support for continous integration with [Travis](https://gist.github.com/mlin/3ad81f01efa640a52813).  This allows automated testing of the pipeline which should make maintenance and upgrades easier.

#TODO Add pitfalls encountered when using Travis with DNA nexus


