# js-to-container-to-bicep-to-aca
Javascript functions to container to Azure Containers apps.

### Create a directory for your JavaScript code and Dockerfile.

```javascript
mkdir myapp
cd myapp
```

### Create a new file named app.js and add the following code to generate a random storage key:

```javascript
function generateStorageKey() {
  const characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
  let result = '';
  const length = 64;
  for (let i = 0; i < length; i++) {
    result += characters.charAt(Math.floor(Math.random() * characters.length));
  }
  return result;
}

console.log(generateStorageKey());
```
### Create a new file named Dockerfile and add the following code to build the container image:

```SQL
FROM node:14-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "app.js"]

```

### Build the Docker image:

```SQL
docker build -t myapp:v1 .
```

### Test the container image by running it locally:

```bicep
resource appServicePlan 'Microsoft.Web/serverfarms@2021-02-01' = {
  name: 'myapp-asp'
  location: 'eastus'
  kind: 'linux'
  sku: {
    name: 'B1'
    tier: 'Basic'
    size: 'B1'
    family: 'B'
  }
}

resource webApp 'Microsoft.Web/sites@2021-02-01' = {
  name: 'myapp-web'
  location: 'eastus'
  kind: 'app'
  dependsOn: [
    appServicePlan
  ]
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      appSettings: [
        {
          name: 'STORAGE_KEY'
          value: '<generate a random value>'
        }
      ]
      linuxFxVersion: 'DOCKER|myapp:v1'
    }
  }
}

```


### This code defines an App Service Plan and a Web App that will be used to deploy the container image. It also includes an app setting named "STORAGE_KEY" that will be set to a random value generated during deployment.

### Use Azure CLI to deploy the Bicep file:

```bash
az deployment group create --resource-group myResourceGroup --template-file myapp.bicep
```

### This will deploy the container image to the Azure Container App, and set the "STORAGE_KEY" app setting to a random value. You can then access the container app's logs to see the output of the app.js script.

