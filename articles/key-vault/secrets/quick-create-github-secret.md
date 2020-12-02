---
title: 'Quickstart: Azure Key Vault-geheimen gebruiken in GitHub Actions-werkstromen'
description: Gebruik Key Vault-geheimen in GitHub Actions om uw werkstromen voor software ontwikkeling te automatiseren
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 9509f84b14a42180189a529282b5db348deab279
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920229"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Key Vault-geheimen gebruiken in GitHub Actions-werkstromen

Gebruik Key Vault-geheimen in uw [GitHub Actions](https://help.github.com/en/articles/about-github-actions) en bewaar wachtwoorden en andere geheimen veilig in een Azure-sleutelkluis. Meer informatie over [Key Vault](../general/overview.md). 

Met Key Vault-en GitHub Actions profiteert u zowel van de voordelen van een centraal beheerprogramma voor geheimen als alle voordelen van GitHub Actions. GitHub Actions is een reeks functies in GitHub voor het automatiseren van uw werkstromen voor software-ontwikkeling. U kunt werkstromen implementeren op dezelfde locatie waar u code opslaat en samenwerken aan pull-aanvragen en problemen. 


## <a name="prerequisites"></a>Vereisten 
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.  
- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een Azure-app die is verbonden met een GitHub-opslagplaats. In dit voorbeeld wordt [Deploy containers to Azure App Service](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01) (Containers implementeren in Azure App Service) gebruikt. 
- Een Azure-sleutelkluis.  U kunt een Azure-sleutelkluis maken met Azure Portal, Azure CLI of Azure PowerShell.

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand moet worden geverifieerd met twee secties in GitHub Actions:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal. <br /> 2. Maak een GitHub-opslagplaats. <br /> 3. Voeg een roltoewijzing toe. |
|**Key Vault** | 1. Voeg de sleutelkluisactie toe. <br /> 2. Verwijs naar het sleutelkluisgeheim. |

## <a name="authentication"></a>Verificatie

U kunt een [service-principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

In het bovenstaande voorbeeld vervangt u de plaatsaanduidingen door uw abonnements-id en resourcegroepsnaam. Vervang de plaatsaanduiding `myApp` door de naam van uw toepassing. De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw App Service-app, vergelijkbaar met hieronder. Kopieer dit JSON-object voor later gebruik. U hebt alleen de secties nodig met de waarden `clientId`, `clientSecret`, `subscriptionId` en `tenantId`. 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

Maak geheimen voor uw Azure-referenties, resourcegroep en abonnementen. 

1. In [GitHub](https://github.com/), bladert u in uw opslagplaats.

1. Selecteer **Instellingen > Geheimen > Nieuwe geheimen**.

1. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim de naam `AZURE_CREDENTIALS`.

1. Kopieer de waarde van `clientId` voor later gebruik. 

### <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen 
 
U moet toegang tot de Azure-service-principal verlenen zodat u toegang hebt tot uw sleutelkluis voor `get`- en `list`-bewerkingen. Als u dit niet doet, kunt u de service-principal niet gebruiken. 

Vervang `keyVaultName` door de naam van uw sleutelkluis en `clientIdGUID` door de waarde van uw `clientId`. 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>De actie voor de Azure-sleutelkluis gebruiken

Met de [actie voor de Azure-sleutelkluis](https://github.com/marketplace/actions/azure-key-vault-get-secrets) kunt u een of meer geheimen uit een Azure Key Vault-exemplaar ophalen en deze gebruiken in uw GitHub Actions-werkstromen.

De opgehaalde geheimen worden ingesteld als uitvoer en ook als omgevingsvariabelen. Variabelen worden automatisch gemaskeerd wanneer ze worden afgedrukt naar de console of naar logboeken.

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

Als u een sleutelkluis in uw werkstroom wilt gebruiken, hebt u zowel de sleutelkluisactie als een verwijzing naar die actie nodig. 

In dit voorbeeld heeft de sleutelkluis de naam `containervault`. Er worden twee sleutelkluisgeheimen aan de omgeving toegevoegd met de sleutelkluisactie: `containerPassword` en `containerUsername`. 

Naar de sleutelkluiswaarden wordt later verwezen in de docker-aanmeldingstaak met het voorvoegsel `steps.myGetSecretAction.outputs`. 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer uw Azure-app, GitHub-opslagplaats en sleutelkluis niet meer nodig zijn, moet u de geïmplementeerde resources opschonen door de resourcegroep voor de app, de GitHub-opslagplaats en de sleutelkluis te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Key Vault](../general/overview.md)
