---
title: Zelfstudie`:` Een beheerde identiteit gebruiken voor toegang tot Cosmos DB - Linux - Azure AD
description: Een zelfstudie die u helpt bij het gebruiken van een door het Linux-VM-systeem toegewezen beheerde identiteit voor toegang tot Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b81f80af69b47152f7111066070e173bb1ede5f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093942"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Zelfstudie: een door het Linux-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Cosmos DB 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot Azure Cosmos DB met een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM). In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Cosmos DB-account maken
> * Een verzameling maken in het Cosmos DB-account
> * De door het systeem toegewezen beheerde identiteit toegang verlenen tot een Azure Cosmos DB-instantie
> * De `principalID` van de door het systeem toegewezen beheerde identiteit voor de Linux-VM ophalen
> * Een toegangstoken ophalen en daarmee Azure Resource Manager aanroepen
> * Toegangssleutels ophalen uit Azure Resource Manager voor Cosmos DB-aanroepen

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](overview.md). 
- Als u geen Azure-account hebt, [registreert u zich voor een gratis account](https://azure.microsoft.com/free/) voordat u verder gaat.
- Om de vereiste resources en het rolbeheer te maken, moet uw account 'Eigenaar'-machtigingen hebben voor het juiste bereik (uw abonnement of resourcegroep). Als u hulp nodig hebt bij roltoewijzing, raadpleegt u [Azure-rollen toewijzen om de toegang tot uw Azure-abonnements resources te beheren](../../role-based-access-control/role-assignments-portal.md).
- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **Probeer het nu** in de rechterbovenhoek van Code::Blocks.
    - Voer scripts lokaal uit door de nieuwste versie van de [Azure CLI](/cli/azure/install-azure-cli) te installeren en u vervolgens aan te melden bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waarin u resources wilt maken.

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-account maken 

Maak een Cosmos DB-account als u er nog geen hebt. U kunt deze stap overslaan en een bestaand Cosmos DB-account gebruiken. 

1. Klik op de knop **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Klik op **Databases** en vervolgens op **Azure Cosmos DB**. Er verschijnt een nieuw venster 'Nieuw account'.
3. Voer een **Id** in voor het Cosmos DB-account, die u later zult gebruiken.  
4. **API** moet worden ingesteld op 'SQL'. De aanpak die in deze zelfstudie wordt beschreven, kan worden gebruikt met de andere beschikbare API-typen, maar de stappen in deze zelfstudie zijn voor de SQL-API.
5. Zorg ervoor dat de waarden van **Abonnement** en **Resourcegroep** overeenkomen met de waarden die u hebt opgegeven bij het maken van de virtuele machine in de vorige stap.  Selecteer een **Locatie** waar Cosmos DB beschikbaar is.
6. Klik op **Create**.

### <a name="create-a-collection-in-the-cosmos-db-account"></a>Een verzameling maken in het Cosmos DB-account

Voeg vervolgens een gegevensverzameling toe in het Cosmos DB-account, waarop u later query’s kunt uitvoeren.

1. Navigeer naar het zojuist gemaakte Cosmos DB-account.
2. Klik op het tabblad **Overzicht** op de knop **+/Verzameling toevoegen**. Het deelvenster 'Verzameling toevoegen' wordt weergegeven.
3. Geef de verzameling een database-ID en een verzameling-ID, selecteer een opslagcapaciteit, voer een partitiesleutel in, voer een doorvoerwaarde in en klik vervolgens op **OK**.  Voor deze zelfstudie volstaat het om ‘Test’ te gebruiken als database-ID en verzameling-ID en een vaste opslagcapaciteit en de laagste doorvoer (400 RU/s) te selecteren.  

## <a name="grant-access"></a>Toegang verlenen

Om in de volgende sectie vanuit Resource Manager toegang te krijgen tot de toegangssleutels van het Cosmos DB-account, moet u de `principalID` van de door het systeem toegewezen beheerde identiteit van de Linux-VM ophalen.  Vervang de waarden van de parameters `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (resourcegroep waar de VM zich bevindt) en `<VM NAME>` door uw eigen waarden.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
Het antwoord bevat de details van de door het systeem toegewezen beheerde identiteit (noteer de principalID, want die wordt gebruikt in de volgende sectie):

```output  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }
```

### <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>Uw door het systeem toegewezen identiteit op de Linux-VM toegang verlenen tot de toegangssleutels van het Cosmos DB-account

Cosmos DB biedt geen systeemeigen ondersteuning voor Azure AD-verificatie. U kunt echter een beheerde identiteit gebruiken om een Cosmos DB-toegangssleutel op te halen uit Resource Manager, en vervolgens die sleutel gebruiken om toegang tot Cosmos DB te krijgen. In deze stap verleent u de door het systeem toegewezen beheerde identiteit toegang tot de sleutels voor het Cosmos DB-account.

Werk de waarden voor `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` en `<COSMOS DB ACCOUNT NAME>` bij voor uw omgeving om de door het systeem toegewezen beheerde identiteit met behulp van de Azure CLI toegang te geven tot het Cosmos DB-account in Azure Resource Manager. Vervang `<MI PRINCIPALID>` door de `principalId`-eigenschap die wordt geretourneerd door de opdracht `az resource show` in De principalID van de Linux-VM-MI ophalen.  Cosmos DB ondersteunt twee granulariteitsniveaus bij het gebruik van toegangssleutels: lees-/schrijftoegang tot het account en alleen-lezen toegang tot het account.  Wijs de rol `DocumentDB Account Contributor` toe als u sleutels voor lezen/schrijven voor het account wilt krijgen, de rol `Cosmos DB Account Reader Role` als u alleen-lezensleutels voor het account wilt ophalen:

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Het antwoord bevat de details voor de gemaakte roltoewijzing:

```output
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="access-data"></a>Toegang tot gegevens

Werk voor de rest van de zelfstudie op de virtuele machine.

U hebt een SSH-client nodig om deze stappen uit te voeren. Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](/windows/wsl/install-win10) gebruiken. Zie [De sleutels van uw SSH-client gebruiken onder Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) of [Een sleutelpaar met een openbare SSH-sleutel en een privé-sleutel maken en gebruiken voor virtuele Linux-machines in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) als u hulp nodig hebt bij het configureren van de sleutels van uw SSH-client.

1. In Azure Portal navigeert u naar **Virtuele machines**, gaat u naar uw virtuele Linux-machine, klikt u vervolgens boven aan de pagina **Overzicht** op **Verbinden**. Kopieer de verbindingsreeks voor uw virtuele machine. 
2. Maak verbinding met uw virtuele machine via de SSH-client.  
3. Vervolgens wordt u gevraagd om het **wachtwoord** in te voeren dat u hebt toegevoegd bij het maken van de **Linux-VM**. Als het goed is, bent u daarna aangemeld.  
4. Gebruik CURL om een toegangstoken voor Azure Resource Manager op te halen: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > In de bovenstaande aanvraag moet de waarde van de parameter 'resource' exact overeenkomen met wat er in Azure AD wordt verwacht. Wanneer u de resource-id van Azure Resource Manager gebruikt, moet u de URI opgeven met een slash op het einde.
    > In het volgende antwoord is het element access_token ingekort.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
### <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Toegangssleutels ophalen uit Azure Resource Manager voor Cosmos DB-aanroepen  

Gebruik nu CURL voor het aanroepen van Resource Manager met behulp van het toegangstoken dat in de vorige sectie is opgehaald, om de toegangssleutel voor het Cosmos DB-account op te halen. Wanneer we de toegangssleutel hebben, kunnen we query’s uitvoeren op de Cosmos DB. Vervang de parameterwaarden `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` en `<COSMOS DB ACCOUNT NAME>` door uw eigen waarden. Vervang de waarde `<ACCESS TOKEN>` door het toegangstoken dat u eerder hebt opgehaald.  Als u lees-/schrijfsleutels wilt ophalen, gebruikt u sleutelbewerkingstype `listKeys`.  Als u alleen-lezensleutels wilt ophalen, gebruikt u sleutelbewerkingstype `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> De tekst in de voorgaande URL is hoofdlettergevoelig. Gebruik daarom in de naam van de resourcegroep al dan niet hoofdletters. Daarnaast is het belangrijk om te weten dat dit een POST-aanvraag is en niet een GET-aanvraag. Zorg ervoor dat u een waarde doorgeeft voor het vastleggen van een maximale lengte met -d. Deze waarde kan NULL zijn.  

In het CURL-antwoord vindt u de lijst met sleutels.  Als u bijvoorbeeld de alleen-lezensleutels ophaalt:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Nu u de toegangssleutel voor het Cosmos DB-account hebt, kunt u deze doorgeven aan een Cosmos DB-SDK en aanroepen doen om toegang tot het account te krijgen.  Voor een snel voorbeeld kunt u de toegangssleutel doorgeven aan de Azure CLI.  U kunt de `<COSMOS DB CONNECTION URL>` vinden op het tabblad **Overzicht** op de Cosmos DB-accountblade in Azure Portal.  Vervang de `<ACCESS KEY>` door de waarde die u hierboven hebt opgehaald:

```azurecli-interactive
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Deze CLI-opdracht retourneert details over de verzameling:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een door het systeem toegewezen beheerde identiteit op een virtuele Linux-machine te gebruiken om toegang tot Cosmos DB te krijgen.  Zie voor meer informatie over Cosmos DB:

> [!div class="nextstepaction"]
>[Overzicht van Azure Cosmos DB](../../cosmos-db/introduction.md)
