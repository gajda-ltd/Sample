FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /src

COPY Sample.WebUI/Sample.WebUI.csproj Sample.WebUI/Sample.WebUI.csproj
COPY Sample.sln Sample.sln

RUN dotnet restore

COPY . .

RUN dotnet build -c Release -o /app/build
RUN dotnet publish -c Release -o /app/publish

FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app

RUN apt-get update && apt-get install -y curl=7.64.0-4+deb10u2 --no-install-recommends && rm -rf /var/lib/apt/lists/*

COPY --from=build /app/publish .

RUN groupadd -g 10000 dotnet && useradd -u 10000 -g dotnet -d /app dotnet && chown -R dotnet:dotnet /app
USER dotnet:dotnet

ENV ASPNETCORE_URLS http://*:5080
EXPOSE 5080

HEALTHCHECK --interval=30s --timeout=10s --retries=3 CMD curl --fail http://localhost:5005/health || exit 1
ENTRYPOINT ["dotnet", "Sample.WebUI.dll"]