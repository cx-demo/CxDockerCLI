# CxCLI installed with Maven
FROM maven:openjdk

LABEL Cx Demo SG <cxdemosg@gmail.com>

# Downloading and installing Cx CLI
ARG CX_CLI_URL="https://download.checkmarx.com/9.0.0/Plugins/CxConsolePlugin-2020.2.3.zip"

WORKDIR /opt

RUN yum -y install unzip && \
    curl ${CX_CLI_URL} -o cli.zip && \
    unzip cli.zip -d /opt/cxcli && \
    rm -rf cli.zip && \
    cd cxcli && \
    # Fix DOS/Windows EOL encoding, if it exists
    cat -v runCxConsole.sh | sed -e "s/\^M$//" > runCxConsole-fixed.sh && \
    rm -f runCxConsole.sh && \
    mv runCxConsole-fixed.sh runCxConsole.sh && \
    chmod +x runCxConsole.sh

WORKDIR /opt/cxcli

ENTRYPOINT ["/opt/cxcli/runCxConsole.sh"]
