# escape=`

# Installer image
FROM mcr.microsoft.com/windows/servercore:2009-amd64 AS installer

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop'; $ProgressPreference = 'SilentlyContinue';"]

# Retrieve .NET Core Runtime
RUN $dotnet_version = '3.1.9'; `
    Invoke-WebRequest -OutFile dotnet.zip https://dotnetcli.azureedge.net/dotnet/Runtime/$dotnet_version/dotnet-runtime-$dotnet_version-win-x64.zip; `
    $dotnet_sha512 = 'f5c8ec530851fe6b951dbf5d70d2443aebdb37341fe844e567e1023a12e237717e2b78b3be6d84928a177c9d6686d4c33c9d6c5f681583494a47605833649710'; `
    if ((Get-FileHash dotnet.zip -Algorithm sha512).Hash -ne $dotnet_sha512) { `
        Write-Host 'CHECKSUM VERIFICATION FAILED!'; `
        exit 1; `
    }; `
    `
    mkdir dotnet; `
    tar -C dotnet -oxzf dotnet.zip; `
    Remove-Item -Force dotnet.zip


# Runtime image
FROM mcr.microsoft.com/windows/nanoserver:2009-amd64

ENV `
    # Configure web servers to bind to port 80 when present
    ASPNETCORE_URLS=http://+:80 `
    # Enable detection of running in a container
    DOTNET_RUNNING_IN_CONTAINER=true

# In order to set system PATH, ContainerAdministrator must be used
USER ContainerAdministrator
RUN setx /M PATH "%PATH%;C:\Program Files\dotnet"
USER ContainerUser

COPY --from=installer ["/dotnet", "/Program Files/dotnet"]