#See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
ARG BUILD_VERSION
# If BUILD_VERSION is set/non-empty, use it, otherwise use a default value
ARG VERSION=${BUILD_VERSION:-1.0.0}
RUN echo "Version will be $VERSION or ${VERSION}"
WORKDIR /src
COPY ["Yarp/Yarp.csproj", "Yarp/"]
RUN dotnet restore "Yarp/Yarp.csproj"
COPY . .
WORKDIR "/src/Yarp"
RUN dotnet build "Yarp.csproj" -c Release -o /app/build -p:Version=$VERSION -p:AssemblyVersion=$VERSION

FROM build AS publish
RUN dotnet publish "Yarp.csproj" -c Release -o /app/publish /p:UseAppHost=false -p:Version=$VERSION -p:AssemblyVersion=$VERSION

FROM base AS final
ARG BUILD_VERSION
# If BUILD_VERSION is set/non-empty, use it, otherwise use a default value
ARG VERSION=${BUILD_VERSION:-1.0.0}
RUN echo "Version will be $VERSION or ${VERSION}"
WORKDIR /app
COPY --from=publish /app/publish .
RUN echo $VERSION >> version.txt 
ENTRYPOINT ["dotnet", "Yarp.dll"]