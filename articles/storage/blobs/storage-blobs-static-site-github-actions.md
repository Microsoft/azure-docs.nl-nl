---
title: GitHub Actions gebruiken om een statische site te implementeren in Azure Storage
description: Azure Storage van statische website-hosting met GitHub-acties
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: d8727bd747ef6d035cabbccf2ad42b80937a06a8
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180197"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>Stel een GitHub actions-werk stroom in om uw statische website te implementeren in Azure Storage

Aan de slag met [github-acties](https://docs.github.com/en/free-pro-team@latest/actions) met behulp van een werk stroom voor het implementeren van een statische site naar een Azure Storage-account. Zodra u een werk stroom voor GitHub-acties hebt ingesteld, kunt u uw site automatisch implementeren in azure vanaf GitHub wanneer u wijzigingen aanbrengt in de code van uw site.

> [!NOTE]
> Als u [statische web apps van Azure](../../static-web-apps/index.yml)gebruikt, hoeft u de werk stroom voor github-acties niet hand matig in te stellen.
> Met Azure static Web Apps wordt automatisch een GitHub actions-werk stroom gemaakt. 

## <a name="prerequisites"></a>Vereisten

Een Azure-abonnement en GitHub-account. 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-opslag plaats met de code van uw statische website. Als u geen GitHub-account hebt, [kunt u zich gratis registreren](https://github.com/join).  
- Een werkende statische website die wordt gehost in Azure Storage. Meer informatie over het [hosten van een statische website in azure Storage](storage-blob-static-website-how-to.md). Als u dit voor beeld wilt volgen, moet u ook [Azure CDN](static-website-content-delivery-network.md)implementeren.

> [!NOTE]
> Het is gebruikelijk om een CDN (Content Delivery Network) te gebruiken om de latentie van uw gebruikers over de hele wereld te reduceren en om het aantal trans acties naar uw opslag account te verminderen. Het implementeren van statische inhoud naar een cloud-gebaseerde opslag service kan de nood zaak voor een dure reken instantie verminderen. Zie voor meer informatie een [patroon voor het hosten van statische inhoud](/azure/architecture/patterns/static-content-hosting).

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties

U kunt een [service-principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

Vervang de tijdelijke aanduiding door `myStaticSite` de naam van uw site die wordt gehost in azure Storage. 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

In het bovenstaande voorbeeld vervangt u de plaatsaanduidingen door uw abonnements-id en resourcegroepsnaam. De uitvoer is een JSON-object met de roltoewijzings referenties die toegang bieden tot uw opslag account zoals hieronder wordt beschreven. Kopieer dit JSON-object voor later gebruik.

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
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voor beeld is beperkt tot de specifieke App Service-app en niet de hele resource groep.

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

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="Menu-item GitHub acties":::

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

1. Gebruik de actie Azure CLI om uw code te uploaden naar Blob Storage en uw CDN-eind punt te verwijderen. `az storage blob upload-batch`Vervang de tijdelijke aanduiding voor door de naam van uw opslag account. Het script wordt geüpload naar de `$web` container. `az cdn endpoint purge`Vervang de tijdelijke aanduidingen door de naam van uw CDN-profiel, de naam van het CDN-eind punt en de resource groep.

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

Wanneer uw statische website en GitHub-opslag plaats niet meer nodig zijn, moet u de resources opschonen die u hebt geïmplementeerd door de resource groep en de GitHub-opslag plaats te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over statische Web Apps van Azure](../../static-web-apps/index.yml)