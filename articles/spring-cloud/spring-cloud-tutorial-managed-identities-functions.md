---
title: Zelfstudie - Beheerde identiteit gebruiken om Azure Functions aan te roepen vanuit een Azure Spring Cloud-app
description: Beheerde identiteit gebruiken om Azure Functions aan te roepen vanuit een Azure Spring Cloud-app
author: MarkGardner
ms.author: margard
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/10/2020
ms.openlocfilehash: 861463b13c6ce8b29911432ad96f98fed527745b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501053"
---
# <a name="tutorial-use-a-managed-identity-to-invoke-azure-functions-from-an-azure-spring-cloud-app"></a>Zelfstudie: Een beheerde identiteit gebruiken om Azure Functions aan te roepen vanuit een Azure Spring Cloud-app

Dit artikel laat zien hoe u een beheerde identiteit kunt maken voor een Azure Spring Cloud-app en deze kunt gebruiken om door Http geactiveerde functies aan te roepen.

Zowel Azure Functions als App Services hebben ingebouwde ondersteuning voor Azure Active Directory-verificatie (Azure AD). Door gebruik te maken van deze ingebouwde verificatiemogelijkheden én beheerde identiteiten voor Azure Spring Cloud kunnen we RESTful-services aanroepen met moderne OAuth-semantiek. Deze methode vereist geen opslag van geheimen in code en biedt u meer gedetailleerde besturingselementen voor het beheren van de toegang tot externe bronnen. 


## <a name="prerequisites"></a>Vereisten

* [Aanmelden voor een Azure-abonnement](https://azure.microsoft.com/free/)
* [Azure CLI-versie 2.0.67 of hoger installeren](/cli/azure/install-azure-cli)
* [Maven 3.0 of hoger installeren](https://maven.apache.org/download.cgi)
* [Installeer Azure Functions Core Tools versie 3.0.2009 of hoger](../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools)


## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een resourcegroep die zowel de Functie-app als de Spring Cloud bevat met behulp van de opdracht [az group create](/cli/azure/group#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```


## <a name="create-a-function-app"></a>Een functie-app maken
Als u een functie-app wilt maken, moet u eerst een back-upopslagaccount maken met behulp van de opdracht [az storage account create](/cli/azure/storage/account#az-storage-account-create):

> [!Important]
> Elke functie-app en elk opslagaccount moeten een unieke naam hebben. Vervang in de volgende voorbeelden <your-functionapp-name> door de naam van uw functie-app en <your-storageaccount-name> door de naam van uw opslagaccount.

```azurecli-interactive
az storage account create --name <your-storageaccount-name> --resource-group myResourceGroup --location eastus --sku Standard_LRS
```

Zodra het opslagaccount is gemaakt, kunt u de functie-app maken.

```azurecli-interactive
az functionapp create --name <your-functionapp-name> --resource-group myResourceGroup --consumption-plan-location eastus --os-type windows --runtime node --storage-account <your-storageaccount-name> --functions-version 3
```

Noteer de geretourneerde **hostNamen**, die de notatie 'https://<your-functionapp-name>.azurewebsites.net' hebben. Deze zullen in de volgende stap worden gebruikt.


## <a name="enable-azure-active-directory-authentication"></a>Azure Active Directory-verificatie inschakelen

Open de zojuist gemaakte functie-app via de [Azure Portal](https://portal.azure.com) en selecteer 'Verificatie/autorisatie' in het menu Instellingen. Schakel verificatie van App Service in en stel 'Te ondernemen actie wanneer de aanvraag niet is geverifieerd' in op 'Aanmelden met Azure Active Directory'. Met deze instelling wordt voorkomen dat alle niet-geverifieerde aanvragen worden geweigerd (401-antwoord).

![Verificatie-instellingen met Azure Active Directory als standaardprovider](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-1.jpg)

Selecteer onder Verificatieproviders Azure Active Directory om de registratie van de toepassing te configureren. Als u de modus voor snel beheer selecteert, wordt er automatisch een toepassingsregistratie in uw Azure AD-Tenant gemaakt met de juiste configuratie.

![Azure Active Directory-provider ingesteld op snelle beheermodus](media/spring-cloud-tutorial-managed-identities-functions/function-auth-config-2.jpg)

Zodra u de instellingen hebt opgeslagen, wordt de functie-app opnieuw gestart en moeten alle volgende aanvragen zich aanmelden via Azure AD. U kunt testen of niet-geverifieerde aanvragen nu worden geweigerd door te navigeren naar de basis-URL van de functie-apps (deze zijn geretourneerd in de uitvoer **hostNamen** in de bovenstaande stap). U moet worden omgeleid naar het Azure AD-aanmeldingsscherm van uw organisatie.


## <a name="create-an-http-triggered-function"></a>Een door http geactiveerde functie maken

Maak in een lege lokale map een nieuwe functie-app en voeg een door http geactiveerde functie toe.

```console
func init --worker-runtime node
func new --template HttpTrigger --name HttpTrigger
```

Standaard gebruikt Functions op sleutel gebaseerde verificatie om http-eindpunten te beveiligen. Omdat we Azure AD-verificatie inschakelen om de toegang tot de functies te beveiligen, willen we het [verificatieniveau voor de functie instellen op anoniem](../azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production).

```json function.json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      ...
    }
  ]
}
```

De app kan nu worden gepubliceerd in het exemplaar van de [functie-app](#create-a-function-app) dat u in de vorige stap hebt gemaakt.

```console
func azure functionapp publish <your-functionapp-name>
```

De uitvoer van de opdracht Publiceren moet de URL van de zojuist gemaakte functie vermelden.

```output
Deployment completed successfully.
Syncing triggers...
Functions in <your-functionapp-name>:
    HttpTrigger - [httpTrigger]
        Invoke url: https://<your-functionapp-name>.azurewebsites.net/api/httptrigger
```


## <a name="create-azure-spring-cloud-service-and-app"></a>Azure Spring Cloud-service en -app maken
Nadat u de bijbehorende uitbreiding voor Spring Cloud hebt geïnstalleerd, maakt u een Azure Spring Cloud-exemplaar met de Azure CLI-opdracht `az spring-cloud create`. 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create --name mymsispringcloud --resource-group myResourceGroup --location eastus
```

In het volgende voorbeeld wordt een app met de naam `msiapp` gemaakt met een door het systeem toegewezen beheerde identiteit, zoals is gevraagd met de parameter `--assign-identity`.

```azurecli
az spring-cloud app create --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --assign-endpoint true --assign-identity
```

## <a name="build-sample-spring-boot-app-to-invoke-the-function"></a>Voorbeeld van Spring Boot-app bouwen om de functie aan te roepen

Met dit voorbeeld wordt de door http geactiveerde functie aangeroepen door eerst een toegangstoken op te vragen van het [MSI-eindpunt](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) en dat token te gebruiken om de HTTP-aanvraag van de functie te verifiëren.

1. Kloon het voorbeeldproject. 

    ```console
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Geef uw functie-URI en de naam van de trigger op in de app-eigenschappen. 

    ```bash
    cd Azure-Spring-Cloud-Samples/managed-identity-function
    vim src/main/resources/application.properties
    ```

    Als u beheerde identiteit voor Azure Spring Cloud-apps wilt gebruiken, voegt u eigenschappen met de volgende inhoud toe aan *src/main/resources/application.properties*.

    ```
    azure.function.uri=https://<your-functionapp-name>.azurewebsites.net
    azure.function.triggerPath=httptrigger
    ```

3. Verpak uw voorbeeld-app. 

    ```console
    mvn clean package
    ```

4. Nu kunt u de app met de Azure CLI-opdracht `az spring-cloud app deploy` implementeren in Azure. 

    ```azurecli
    az spring-cloud app deploy  --name "msiapp" --service "mymsispringcloud" --resource-group "myResourceGroup" --jar-path target/sc-managed-identity-function-sample-0.1.0.jar
    ```

5. Open het openbare eindpunt of testeindpunt om de app te testen. 

    ```console
    curl https://mymsispringcloud-msiapp.azuremicroservices.io/func/springcloud
    ```

    Het volgende bericht wordt weergegeven in de hoofdtekst van de reactie.
    ```output
    Function Response: Hello, springcloud. This HTTP triggered function executed successfully.
    ```
    
    U kunt proberen verschillende waarden door te geven aan de functie door de parameter van het pad te wijzigen.

## <a name="next-steps"></a>Volgende stappen

* [Door het systeem toegewezen beheerde identiteit inschakelen voor de Azure Spring Cloud-toepassing](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Meer informatie over beheerde identiteiten voor Azure-resources](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Client-apps configureren voor toegang tot uw App Service](../app-service/configure-authentication-provider-aad.md#configure-client-apps-to-access-your-app-service)
