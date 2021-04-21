---
title: GitHub Actions gebruiken om een statische site te implementeren in Azure Storage
description: Azure Storage statische website hosten met GitHub Actions
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 3ae0904eda2608026ad09ba8b8993008380725f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788525"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Een werkstroom GitHub Actions om uw statische website te implementeren in Azure Storage

Ga aan de [slag GitHub Actions](https://docs.github.com/en/actions) met behulp van een werkstroom om een statische site te implementeren in een Azure-opslagaccount. Zodra u een GitHub Actions hebt ingesteld, kunt u uw site automatisch vanuit GitHub implementeren in Azure wanneer u wijzigingen aan de code van uw site aan brengt.

> [!NOTE]
> Als u een [Azure Static Web Apps](../../static-web-apps/index.yml)gebruikt, hoeft u niet handmatig een werkstroom voor GitHub Actions instellen.
> Azure Static Web Apps maakt automatisch een GitHub Actions werkstroom voor u. 

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement en Een GitHub-account. 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-opslagplaats met de code van uw statische website. Als u geen GitHub-account hebt, [kunt u zich gratis registreren](https://github.com/join).  
- Een werkende statische website die wordt gehost in Azure Storage. Meer informatie over het [hosten van een statische website in Azure Storage.](storage-blob-static-website-how-to.md) Als u dit voorbeeld wilt volgen, moet u ook [Azure CDN.](static-website-content-delivery-network.md)

> [!NOTE]
> Het is gebruikelijk om een netwerk voor contentlevering (CDN) te gebruiken om de latentie voor uw gebruikers over de hele wereld te verminderen en het aantal transacties naar uw opslagaccount te verminderen. Het implementeren van statische inhoud in een cloudopslagservice kan de behoefte aan mogelijk dure rekenkracht verminderen. Zie Static [Content Hosting pattern (Patroon Hosting van statische inhoud) voor meer informatie.](/azure/architecture/patterns/static-content-hosting)

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

U kunt een [service-principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

Vervang de tijdelijke aanduiding `myStaticSite` door de naam van uw site die wordt gehost in Azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

In het bovenstaande voorbeeld vervangt u de plaatsaanduidingen door uw abonnements-id en resourcegroepsnaam. De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw opslagaccount, vergelijkbaar met het onderstaande. Kopieer dit JSON-object voor later gebruik.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voorbeeld is beperkt tot de specifieke App Service app en niet tot de hele resourcegroep.

## <a name="configure-the-github-secret"></a>Het GitHub-geheim configureren

1. In [GitHub](https://github.com/), bladert u in uw opslagplaats.

1. Selecteer **Instellingen > Geheimen > Nieuwe geheimen**.

1. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim een naam zoals `AZURE_CREDENTIALS` .

    Wanneer u het werkstroombestand later configureert, gebruikt u het geheim voor de invoer `creds` van de Azure-aanmeldingsactie. Bijvoorbeeld:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>Voeg uw werkstroom toe

1. Ga naar **Acties** voor uw GitHub-opslagplaats. 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Menu-item GitHub-acties":::

1. Selecteer **Uw werkstroom zelf instellen**. 

1. Verwijder alles na de sectie `on:` van uw werkstroombestand. Uw resterende werkstroom kan er bijvoorbeeld als volgt uitzien. 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. Wijzig de naam van uw werkstroom `Blob storage website CI` en voeg de acties voor uitchecken en aanmelden toe. Met deze acties wordt uw sitecode uitgecheckt en geverifieerd met Azure met behulp van het `AZURE_CREDENTIALS` GitHub-geheim dat u eerder hebt gemaakt. 

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Gebruik de Azure CLI-actie om uw code te uploaden naar blob-opslag en om uw CDN-eindpunt op te purpen. Vervang `az storage blob upload-batch` voor de tijdelijke aanduiding door de naam van uw opslagaccount. Het script wordt geüpload naar de `$web` container. Vervang `az cdn endpoint purge` voor de tijdelijke aanduidingen door uw CDN-profielnaam, CDN-eindpuntnaam en resourcegroep.

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Voltooi uw werkstroom door een actie toe te voegen aan de afmelding van Azure. Dit is de voltooide werkstroom. Het bestand wordt weergegeven in de map `.github/workflows` van uw opslagplaats.

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
      
      # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>Beoordeel uw implementatie

1. Ga naar **Acties** voor uw GitHub-opslagplaats. 

1. Open het eerste resultaat om gedetailleerde logboeken van de uitvoering van de werkstroom weer te geven. 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="Logboek van uitgevoerde GitHub-acties":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer uw statische website en GitHub-opslagplaats niet meer nodig zijn, schoont u de resources op die u hebt geïmplementeerd door de resourcegroep en uw GitHub-opslagplaats te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Static Web Apps](../../static-web-apps/index.yml)
