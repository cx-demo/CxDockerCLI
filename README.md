# CxDockerCLI
* Author:       Pedric kng
* Updated:      13 June 2020

Dockerized Checkmarx CLI installed with Package managers for dependency resolution, extended or inspired by other docker images with special thanks (Refer to the references)

## Supported tags and docker file links

Linux Based images
***
- [openjdk-maven-cli-2020.2.3](maven/Dockerfile)

## How to build 

```bash
docker build --build-arg CX_CLI_URL="{download url}" -t cxdockercli:{tag} . --no-cache
```

## How to run

```bash
docker run -it --name cxcli cxdockercli:{tag}
```

## References
Checkmarx CxCLI Guide [[1]]  
Official Docker image with Maven [[2]]  
CxCLI-Docker [[3]]  
How to create a custom docker image with jdk8, maven, and gradle [[4]]  

[1]:https://checkmarx.atlassian.net/wiki/spaces/KC/pages/44335590/CxSAST+CLI+Guide "Checkmarx CxCLI Guide"
[2]:https://github.com/carlossg/docker-maven "Official Docker image with Maven"
[3]:https://github.com/checkmarx-ts/CxCLI-Docker "CxCLI-Docker"
[4]:https://medium.com/@migueldoctor/how-to-create-a-custom-docker-image-with-jdk8-maven-and-gradle-ddc90f41cee4 "How to create a custom docker image with jdk8, maven, and gradle"