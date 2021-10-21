ARG core=mcr.microsoft.com/windows/servercore:1809
ARG target=mcr.microsoft.com/windows/nanoserver:1809-amd64
FROM $core as download

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

ENV NODE_VERSION 14.18.1

RUN Invoke-WebRequest $('https://nodejs.org/dist/v{0}/node-v{0}-win-x64.zip' -f $env:NODE_VERSION) -OutFile 'node.zip' -UseBasicParsing ; \
    Expand-Archive node.zip -DestinationPath C:\ ; \
    Rename-Item -Path $('C:\node-v{0}-win-x64' -f $env:NODE_VERSION) -NewName 'C:\nodejs'

ENV GIT_VERSION 2.33.0
ENV GIT_DOWNLOAD_URL https://github.com/git-for-windows/git/releases/download/v${GIT_VERSION}.windows.1/MinGit-${GIT_VERSION}-busybox-64-bit.zip
ENV GIT_SHA256 273f55e881094d00877d64f56570b0c997c4da5dedcb26738d56481033c1eba1

RUN [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 ; \
    Invoke-WebRequest -UseBasicParsing $env:GIT_DOWNLOAD_URL -OutFile git.zip; \
    Expand-Archive git.zip -DestinationPath C:\git; \
    Remove-Item git.zip

FROM $target

LABEL MAINTAINER="Siritas Dho <dahoba@gmail.com>"
LABEL org.opencontainers.image.source="https://github.com/dahoba/node"

ENV NPM_CONFIG_LOGLEVEL info

COPY --from=download /nodejs /nodejs
COPY --from=download /git /git

ARG SETX=/M
USER ContainerAdministrator
RUN setx %SETX% PATH "%PATH%;C:\nodejs;C:\git\cmd;C:\git\mingw64\bin;C:\git\usr\bin"
USER ContainerUser

CMD [ "node.exe" ]