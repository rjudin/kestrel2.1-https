FROM microsoft/dotnet:2.1-aspnetcore-runtime AS base
WORKDIR /app

EXPOSE 80
EXPOSE 443

FROM microsoft/dotnet:2.1-sdk AS build
WORKDIR /src
COPY TestAPI_2_1_Kestrel/TestAPI_2_1_Kestrel.csproj TestAPI_2_1_Kestrel/
RUN dotnet restore TestAPI_2_1_Kestrel/TestAPI_2_1_Kestrel.csproj
COPY . .
WORKDIR /src/TestAPI_2_1_Kestrel

RUN dotnet build TestAPI_2_1_Kestrel.csproj -c Release -o /app

FROM build AS publish
RUN dotnet publish TestAPI_2_1_Kestrel.csproj -c Release -o /app

FROM base AS final
WORKDIR /app
COPY --from=publish /app .

# add created pfx cert to runtime container
COPY TestAPI_2_1_Kestrel/cert/https.pfx cert/https.pfx
RUN ls -la /app/cert/

ENTRYPOINT ["dotnet", "TestAPI_2_1_Kestrel.dll"]

# CREATE SELF-SIGNED CERT
# https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/
# openssl req -config ./cert/https.config -new -out ./cert/csr.pem
# openssl genrsa -out ./cert/key.pem 2048
# openssl x509 -req -days 365 -extfile ./cert/https.config -extensions v3_req -in ./cert/csr.pem -signkey ./cert/key.pem -out ./cert/https.crt
# openssl pkcs12 -export -out ./cert/https.pfx -inkey ./cert/key.pem -in ./cert/https.crt -password pass:vistex
# chmod 775 -R ./cert/
