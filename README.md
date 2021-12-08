# Deploying Spring Petclinic Angular on Azure Spring Cloud

## Angular frontend for Spring Petclinic on Azure Spring Cloud


Warning: **client only**. 
  Use REST API from backend deployed on Azure Spring Cloud [spring-petclinic-rest project](https://github.com/selvasingh/spring-petclinic-rest/blob/azure/README.md). Following steps need to be done before deploying this project 
1) You need to deploy Spring petclinic rest project on Azure Spring Cloud and assign a public end point. 
2) Copy the Assigned end point and modify the code in **environment.ts** and **environment.prod.ts** for production deploy.

### Build
```
git clone https://github.com/selvasingh/spring-petclinic-angular.git
cd spring-petclinic-angular
git checkout azure
ng build 
```

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `-prod` flag for a production build.

You can also build the application in a dedicated docker image using the provided `Dockerfile` as follows:

```
docker build -t spring-petclinic-angular:latest .
```

Then you will be able to use it as follows:

```
docker run --rm -p 1025:1025 spring-petclinic-angular:latest
```
After running the docker client locally the application should be able to hit the exposed rest end point of the Spring Petclinic Rest project
 
![Screenshot of SPring Petclinic Angular](https://cloud.githubusercontent.com/assets/838318/23263243/f4509c4a-f9dd-11e6-951b-69d0ef72d8bd.png)

## Publish container image

 1. [Install docker](https://docs.docker.com/engine/install/)
 2. Open a shell and cd to the same directory as this README.
 3. Export a variable with your registry name:

```cli
export REGISTRY=myregistry
```

 4. Run the following Docker commands to build, tag, and publish the image from the `httpapi` directory:

```cli
docker build -t spring-petclinic-angular -f Dockerfile .
docker tag spring-petclinic-angular $REGISTRY/spring-petclinic-angular:latest
docker push $REGISTRY/spring-petclinic-angular:latest
```

## Deploy to Azure Spring Cloud

After building and testing locally with the Dockerfile, you can now deploy the application to Azure Spring Cloud service. Following are the steps needed to deploy to Azure Spring Cloud.

### Install the CLI and extensions
 [Install](https://docs.microsoft.com/cli/azure/install-azure-cli) the Azure CLI to run CLI reference commands. 

- If you're using a local installation, sign in to the Azure CLI by using the [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login) command. To finish the authentication process, follow the steps displayed in your terminal. For additional sign-in options, see [Sign in with the Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli).

 - Run [az version](https://docs.microsoft.com/cli/azure/reference-index?#az_version) to find the version and dependent libraries that are installed. To upgrade to the latest version, run [az upgrade](https://docs.microsoft.com/cli/azure/reference-index?#az_upgrade).

Install the **Azure Spring Cloud extension**

Before installing the Azure Spring Cloud CLI extension, please ensure that an older verison of the extension is not installed:

```azurecli-interactive
az extension remove -n spring-cloud
```

Install the latest version of the extension with the following command:

```azurecli-interactive
az extension add --source https://ascprivatecli.blob.core.windows.net/cli-extension/spring_cloud-2.11.2_container-py3-none-any.whl
```

For more information about extensions, see [Use extensions with the Azure CLI](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview).

 
### Create Azure Spring Cloud service instance

Follow [Create Azure Spring Cloud service instance](https://github.com/selvasingh/spring-petclinic-rest/blob/azure/README.md#create-azure-spring-cloud-service-instance) to create an Azure Spring Cloud service instance.

### Create microservice applications

Create a microservice app.

```azurecli-interactive
az spring-cloud app create --name spring-petclinic-angular --instance-count 1 --assign-endpoint true
```

### Deploy Spring Petclinic Angular application

Deploy Spring Petclinic Angular application to Azure. 

**Important Note: Application must expose 1025 port for Azure Spring Cloud health probing. Check [How does Azure Spring Cloud monitor the health status of my application?](https://docs.microsoft.com/en-us/azure/spring-cloud/faq?pivots=programming-language-java#how-does-azure-spring-cloud-monitor-the-health-status-of-my-application) for details.**

```azurecli-interactive
az spring-cloud app deploy --name spring-petclinic-angular --container-image $REGISTRY/spring-petclinic-angular:latest
```

And please check [Customize container for Azure Spring Cloud](https://github.com/LGDoor/azure-cli-extensions/blob/xiangy/byoc/src/spring-cloud/README_CONTAINER.md) for the full CLI commands list.

Congratulations! You've successfully deployed Spring Petclinic Angular to Azure Spring Cloud.
