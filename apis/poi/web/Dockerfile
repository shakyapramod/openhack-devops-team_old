FROM microsoft/dotnet:2.1-sdk AS build-env
WORKDIR /app

# copy csproj and restore as distinct layers
COPY  ./apis/poi/web/*.csproj ./

COPY ./apis/poi/tests/IntegrationTests/*.csproj ./tests/IntegrationTests/
COPY ./apis/poi/tests/UnitTests/*.csproj ./tests/UnitTests/

RUN dotnet restore

# copy everything else and build
COPY ./apis/poi/web ./
RUN dotnet publish -c Release -o out
COPY ./apis/poi/web/appsettings.*.json /app/out/
COPY ./apis/poi/web/appsettings.json /app/out/

FROM build-env AS unit-test
WORKDIR ./app/tests/UnitTests
RUN dotnet test

# build runtime image
FROM microsoft/dotnet:2.1-aspnetcore-runtime
WORKDIR /app

ENV SQL_USER="demousersa" \
SQL_PASSWORD="demo@pass123" \
SQL_SERVER="openhackwpn4z9r3sql.database.windows.net" \
SQL_DBNAME="mydrivingDB" \
WEB_PORT="8080" \
WEB_SERVER_BASE_URI="http://0.0.0.0" \
ASPNETCORE_ENVIRONMENT="Production"

COPY --from=build-env /app/out .

ENTRYPOINT ["dotnet", "poi.dll"]
