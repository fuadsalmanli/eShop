FROM mcr.microsoft.com/dotnet/aspnet:9.0 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 8080
EXPOSE 8081

FROM mcr.microsoft.com/dotnet/sdk:9.0 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src

COPY ["nuget.config", "."]
COPY ["Directory.Packages.props", "."]
COPY ["Directory.Build.props", "."]
COPY ["Directory.Build.targets", "."]

COPY ["src/Mobile.Bff.Shopping/Mobile.Bff.Shopping.csproj", "src/Mobile.Bff.Shopping/"]
COPY ["src/eShop.ServiceDefaults/eShop.ServiceDefaults.csproj", "src/eShop.ServiceDefaults/"]
RUN dotnet restore "src/Mobile.Bff.Shopping/Mobile.Bff.Shopping.csproj"
COPY . .
WORKDIR "src/Mobile.Bff.Shopping"
RUN dotnet build "./Mobile.Bff.Shopping.csproj" -c $BUILD_CONFIGURATION -o /app/build

FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Mobile.Bff.Shopping.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Mobile.Bff.Shopping.dll"]
