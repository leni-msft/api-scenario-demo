# API Scenario Test

## Prerequisites

1. Install [docker](https://docs.docker.com/get-docker/)


2. Install [oav](https://www.npmjs.com/package/oav)

```bash
npm i -g oav
```

3. [Prepare AAD app](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) and assign subscription contributor role to the app.

4. Prepare `env.json` file with following content:

```json
{
  "tenantId": "<AAD app tenantId>",
  "client_id": "<AAD app client_id>",
  "client_secret": "<AAD app client_secret>",
  "subscriptionId": "<subscriptionId>",
  "location": "westus"
}
```

## Steps to generate and run API Scenario Test

1. Compile swagger into dependencies.json with Restler

```bash
docker run --rm -v $(pwd)/specification:/swagger -w /swagger/.restler_output mcr.microsoft.com/restlerfuzzer/restler dotnet /RESTler/restler/Restler.dll compile --api_spec /swagger/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/appconfiguration.json
```

2. Generate example based API Scenario

```bash
oav generate-api-scenario static --specs specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/appconfiguration.json --dependency specification/.restler_output/Compile/dependencies.json -o specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/scenarios --useExample
```

3. Run API Scenario Test

```bash
oav run specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/scenarios/basic.yaml --tag=package-2022-05-01 -e env.json -l verbose
```

4. Check the test result

Check the raw report under `$(pwd)/.apitest/<scenario-file-name>/<runId>/<scenario-name>/report.json`.

Or add parameter `--report markdown` to the command line to generate a markdown report.
```bash
oav run specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/scenarios/basic.yaml --tag=package-2022-05-01 -e env.json -l verbose --report markdown
```

5. Debug API Scenario Test

Import the generated Postman collection into Postman and run the collection manually.

## References

https://aka.ms/apiscenario
