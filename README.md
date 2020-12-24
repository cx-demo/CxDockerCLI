# CxDockerCLI
* Author:       Pedric kng
* Updated:      24 Dec 2020

As part of CxOSA scan, it is recommended to leverage on dependency resolution for better coverage and accuracy. 

This article will describe how to extend official image for package managers with CxCLI to support dependency resolution, see CxOSA supported package managers [[5]]

# Overview 

There are various docker official image for package manager available on Dockerhub e.g., [Maven](https://hub.docker.com/_/maven), [Gradle](https://hub.docker.com/_/gradle) that you can extend to include CxCLI [[1]] plugin to support OSA scans which requires local dependency resolution

## Extending images to support CxOSA scans

The [dotnet example](dotnet/Dockerfile) below extends the dotnet-sdk official image(https://hub.docker.com/_/microsoft-dotnet-sdk), and as part of the extension downloads the CxCLI into the container.

```bash
FROM mcr.microsoft.com/dotnet/sdk:3.1

LABEL Cx Demo SG <cxdemosg@gmail.com>

RUN apt-get update && apt-get -y install curl unzip openjdk-11-jdk-headless 

# Downloading and installing CxCLI
ARG CX_VERSION=9.0.0
ARG CXCLI_VERSION=2020.4.4
ARG CXCLI_BASE_URL=https://download.checkmarx.com/${CX_VERSION}/Plugins

RUN echo "Downloading CXCLI" \
  && curl -fsSL -o /tmp/CxConsolePlugin.zip ${CXCLI_BASE_URL}/CxConsolePlugin-${CXCLI_VERSION}.zip \
  \
  && echo "Unzip CxCLI" \
  && unzip /tmp/CxConsolePlugin.zip -d /opt/cxcli \
  \
  && echo "Fix DOS/Windows EOL encoding, if it exists" \
  && cd /opt/cxcli \
  && cat -v runCxConsole.sh | sed -e "s/\^M$//" > runCxConsole-fixed.sh \
  && rm -f runCxConsole.sh \
  && mv runCxConsole-fixed.sh runCxConsole.sh \
  && chmod +x runCxConsole.sh \
  \
  && echo "Cleaning and setting links" \
  && rm -rf /tmp/CxConsolePlugin.zip

WORKDIR /opt/cxcli

ENTRYPOINT ["/opt/cxcli/runCxConsole.sh"]
```

This extension might vary depending on the base image of choice, several key aspects to consider;
- Linux package tool availability in base image e.g., APT  
- 'Curl' and 'Unzip' command; to download zipped CxCLI plugin and unzipping the plugin
- Default entrypoint is to 'runCxConsole.sh', this can be overriden using docker argument '--entrypoint'

## Building the CxCLI image

```bash
docker build --build-arg CX_VERSION="{Checkmarx version}" --build-arg CXCLI_VERSION="{CxCLI version}" -t "cxdockercli/{Variant}:{CxCLI version}" . --no-cache

E.g., 
docker build --build-arg CX_VERSION="9.0.0" --build-arg CXCLI_VERSION="2020.4.4" -t cxdockercli/dotnet:2020.4.4 . --no-cache

```

The arguments are extracted from CLI download URL "https://download.checkmarx.com/<checkmarx version>/Plugins/CxConsolePlugin-<CxCLI version>.zip"

| Argument | Description |
|:------------- |:------------- |
| Checkmarx version | Checkmarx version e.g., 9.0.0, |
| CxCLI version | CLI plugin version e.g., 2020.4.12 |
| Variant | Package manager variant |

## Running the CxCLI container

```bash
docker run -it "cxdockercli/{Variant}:{CxCLI version}" <CxCLI command>

E.g., 

// Execute CxSAST & CxOSA Scan
docker run --rm cxdockercli/dotnet:2020.4.4 Scan -v -CxServer <SAST URL> -CxToken <SAST TOKEN> -ProjectName <TEAM\PROJECTNAME> -LocationType folder -LocationPath <SRC PATH> -enableosa -executepackagedependency

```

## Docker file examples

Package manager
- [maven](maven/Dockerfile)
- [gradle](gradle/Dockerfile)
- [dotnet](dotnet/Dockerfile)


## References
Checkmarx CxCLI Guide [[1]]  
Official Docker image with Maven [[2]]  
CxCLI-Docker [[3]]  
How to create a custom docker image with jdk8, maven, and gradle [[4]]  
CxOSA Supported languages and package managers [[5]]  

[1]:https://checkmarx.atlassian.net/wiki/spaces/KC/pages/44335590/CxSAST+CLI+Guide "Checkmarx CxCLI Guide"
[2]:https://github.com/carlossg/docker-maven "Official Docker image with Maven"
[3]:https://github.com/checkmarx-ts/CxCLI-Docker "CxCLI-Docker"
[4]:https://medium.com/@migueldoctor/how-to-create-a-custom-docker-image-with-jdk8-maven-and-gradle-ddc90f41cee4 "How to create a custom docker image with jdk8, maven, and gradle"  
[5]:https://checkmarx.atlassian.net/wiki/spaces/CCOD/pages/1345486898/Supported+Languages+and+Package+Managers "CxOSA Supported languages and package managers"