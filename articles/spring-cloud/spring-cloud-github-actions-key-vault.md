---
title: Azure Spring Cloud verifiëren met Key Vault in GitHub Actions
description: Sleutel kluis met CI/CD-werk stroom gebruiken voor Azure lente-Cloud met GitHub-acties
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0ea0db1faf8c452958b8d95c193d45506057777c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673329"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Azure Spring Cloud verifiëren met Key Vault in GitHub Actions

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Sleutel kluis is een veilige plaats voor het opslaan van sleutels. Zakelijke gebruikers moeten referenties opslaan voor CI/CD-omgevingen binnen het bereik dat ze beheren. De sleutel voor het ophalen van referenties in de sleutel kluis moet beperkt zijn tot het bron bereik.  Het heeft alleen toegang tot het sleutel kluis bereik, niet voor het hele Azure-bereik. Het lijkt erop dat een sleutel die alleen een sterk vak kan openen, geen hoofd sleutel is die alle deuren in een gebouw kan openen. Het is een manier om een sleutel te verkrijgen met een andere sleutel. Dit is handig in een CICD-werk stroom. 

## <a name="generate-credential"></a>Referentie genereren
Voor het genereren van een sleutel voor toegang tot de sleutel kluis voert u de volgende opdracht uit op de lokale computer:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
Met het bereik dat is opgegeven door de `--scopes` para meter wordt de sleutel toegang tot de bron beperkt.  Het kan alleen toegang krijgen tot het sterke vak.

Met resultaten:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Sla de resultaten vervolgens op in GitHub **geheimen** zoals beschreven in [uw github-opslag plaats instellen en verifiëren met Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Toegangs beleid voor de referentie toevoegen
Met de referentie die u hierboven hebt gemaakt, krijgt u alleen algemene informatie over de Key Vault, niet de inhoud die wordt opgeslagen.  Als u geheimen wilt ophalen die zijn opgeslagen in de Key Vault, moet u toegangs beleid instellen voor de referentie.

Ga naar het **Key Vault** dash board in azure Portal, klik op het **toegangs beheer** menu en open vervolgens het tabblad **roltoewijzingen** . Selecteer **apps** voor **type** en `This resource` voor **bereik**.  De referenties die u in de vorige stap hebt gemaakt, worden weer geven:

 ![Toegangs beleid instellen](./media/github-actions/key-vault1.png)

Kopieer de referentie naam, bijvoorbeeld `azure-cli-2020-01-19-04-39-02` . Open het menu **toegangs beleid** en klik op koppeling naar **toegangs beleid toevoegen** .  Selecteer `Secret Management` voor **sjabloon** en selecteer vervolgens **Principal**. Plak de referentie naam in **Principal** invoervak / **selecteren** :

 ![Selecteer](./media/github-actions/key-vault2.png)

 Klik op de knop **toevoegen** in het dialoog venster **toegangs beleid toevoegen** en klik vervolgens op **Opslaan**.

## <a name="generate-full-scope-azure-credential"></a>Azure-referentie met een volledige bereik genereren
Dit is de hoofd sleutel voor het openen van alle deuren in het gebouw. De procedure is vergelijkbaar met de vorige stap, maar hier wijzigen we het bereik voor het genereren van de hoofd sleutel:

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Opnieuw, resultaten:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Kopieer de volledige JSON-teken reeks.  Bo terug naar **Key Vault** dash board. Open het menu **geheimen** en klik vervolgens op de knop **genereren/importeren** . Voer de geheime naam in, bijvoorbeeld `AZURE-CREDENTIALS-FOR-SPRING` . Plak de JSON-referentie teken reeks in het invoervak **waarde** . U ziet het invoervak waarde is een tekst veld met één regel in plaats van een tekst gebied met meerdere regels.  U kunt de volledige JSON-teken reeks plakken.

 ![Volledige bereik referentie](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Referenties combi neren in GitHub acties
Stel de referenties in die worden gebruikt wanneer de CICD-pijp lijn wordt uitgevoerd:

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Volgende stappen
* [GitHub acties voor de lente in de Cloud](./spring-cloud-howto-github-actions.md)
