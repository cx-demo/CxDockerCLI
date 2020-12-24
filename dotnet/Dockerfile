# CxCLI installed with dotnet
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