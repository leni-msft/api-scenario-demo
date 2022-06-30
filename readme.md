# API Scenario Test

## Prerequisites

1. Install [docker](https://docs.docker.com/get-docker/)

2. Install [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) and authenticate with ACR

```bash
az login
az acr login --name rpaasoneboxacr
```

3. Install [oav](https://www.npmjs.com/package/oav)

```bash
npm i -g oav@beta
```

4. [Prepare AAD app](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) and assign subscription contributor role to the app.

5. Prepare env.json file with following content:

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
docker run --rm -v $(pwd)/specification:/swagger -w /swagger/.restler_output mcr.microsoft.com/restlerfuzzer/restler:v8.5.0 dotnet /RESTler/restler/Restler.
dll compile --api_spec /swagger/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/appconfiguration.json
```

2. Generate example based API Scenario

```bash
oav generate-api-scenario static --specs specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/appconfiguration.json --dependency specification/appconfiguration/.restler_output/Compile/dependencies.json -o specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/scenarios --useExample
```

3. Run API Scenario Test

```bash
oav run specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/scenarios/basic.yaml --tag=package-2022-05-01 -e ~/dogfooding/test-apiscenario/.env --verbose
```

4. Check the test result

Check the raw report under `$(pwd)/generated/<scenario-file-name>/<runId>/<scenario-name>/report.json`.

Or add parameter `--markdown <report-path>` to the command line to generate a markdown report.
```bash
oav run specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2022-05-01/scenarios/basic.yaml --tag=package-2022-05-01 -e ~/dogfooding/test-apiscenario/.env --verbose --markdown $(pwd)/generated/report.md
```

5. Debug API Scenario Test

Import the generated Postman collection into Postman and run the collection manually.
