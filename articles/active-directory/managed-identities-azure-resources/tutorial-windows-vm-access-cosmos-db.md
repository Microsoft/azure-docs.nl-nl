---
title: 'Zelfstudie: een beheerde identiteit gebruiken voor toegang tot Cosmos DB - Windows - Azure AD'
description: Een zelfstudie die u helpt bij het gebruiken van een door het systeem toegewezen beheerde identiteit op een Windows-VM voor toegang tot Azure Cosmos DB.
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
ms.openlocfilehash: 04508f1aa8ee9d6b4f730f57c60d959fab209122
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093799"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot Cosmos DB met een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine (VM). In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Cosmos DB-account maken
> * Een door het Windows-VM-systeem toegewezen beheerde identiteit toegang geven tot de toegangssleutels van het Cosmos DB-account
> * Een toegangstoken ophalen met behulp van de door het Windows-VM-systeem toegewezen beheerde identiteit om Azure Resource Manager aan te roepen
> * Toegangssleutels ophalen uit Azure Resource Manager voor Cosmos DB-aanroepen

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](overview.md). 
- Als u geen Azure-account hebt, [registreert u zich voor een gratis account](https://azure.microsoft.com/free/) voordat u verder gaat.
- Om de vereiste resources en het rolbeheer te maken, moet uw account 'Eigenaar'-machtigingen hebben voor het juiste bereik (uw abonnement of resourcegroep). Als u hulp nodig hebt bij roltoewijzing, raadpleegt u [Azure-rollen toewijzen om de toegang tot uw Azure-abonnements resources te beheren](../../role-based-access-control/role-assignments-portal.md).
- Installeer de nieuwste versie van [Azure PowerShell](/powershell/azure/install-az-ps)
- U hebt ook een virtuele Windows-machine nodig waarvoor door het systeem toegewezen beheerde identiteiten zijn ingeschakeld.
  - Als u voor deze zelfstudie een virtuele machine moet maken, kunt u dat doen met behulp van het artikel [Een virtuele machine maken waarvoor door het systeem toegewezen identiteiten ingeschakeld zijn](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="create-a-cosmos-db-account"></a>Cosmos DB-account maken 

Maak een Cosmos DB-account als u er nog geen hebt. U kunt deze stap overslaan en een bestaand Cosmos DB-account gebruiken. 

1. Klik op de knop **+ Een resource maken** in de linkerbovenhoek van Azure Portal.
2. Klik op **Databases** en vervolgens op **Azure Cosmos DB**. Er verschijnt een nieuw venster 'Nieuw account'.
3. Voer een **Id** in voor het Cosmos DB-account, die u later zult gebruiken.  
4. **API** moet worden ingesteld op 'SQL'. De aanpak die in deze zelfstudie wordt beschreven, kan worden gebruikt met de andere beschikbare API-typen, maar de stappen in deze zelfstudie zijn voor de SQL-API.
5. Zorg ervoor dat de waarden van **Abonnement** en **Resourcegroep** overeenkomen met de waarden die u hebt opgegeven bij het maken van de virtuele machine in de vorige stap.  Selecteer een **Locatie** waar Cosmos DB beschikbaar is.
6. Klik op **Create**.

### <a name="create-a-collection"></a>Een verzameling maken 

Voeg vervolgens een gegevensverzameling toe in het Cosmos DB-account, waarop u later query’s kunt uitvoeren.

1. Navigeer naar het zojuist gemaakte Cosmos DB-account.
2. Klik op het tabblad **Overzicht** op de knop **+/Verzameling toevoegen**. Het deelvenster 'Verzameling toevoegen' wordt weergegeven.
3. Geef de verzameling een database-ID en een verzameling-ID, selecteer een opslagcapaciteit, voer een partitiesleutel in, voer een doorvoerwaarde in en klik vervolgens op **OK**.  Voor deze zelfstudie volstaat het om ‘Test’ te gebruiken als database-ID en verzameling-ID en een vaste opslagcapaciteit en de laagste doorvoer (400 RU/s) te selecteren.  


## <a name="grant-access"></a>Toegang verlenen

In deze sectie wordt beschreven hoe u de door het Windows-VM-systeem toegewezen beheerde identiteit toegang kunt geven tot de toegangssleutels van het Cosmos DB-account. Cosmos DB biedt geen systeemeigen ondersteuning voor Azure AD-verificatie. U kunt echter een door het systeem toegewezen beheerde identiteit gebruiken om een Cosmos DB-toegangssleutel op te halen uit Resource Manager, en de sleutel gebruiken om toegang tot Cosmos DB te krijgen. In deze stap verleent u de door het Windows-VM-systeem toegewezen beheerde identiteit toegang tot de sleutels voor het Cosmos DB-account.

Werk de volgende waarden bij om de door het Windows-VM-systeem toegewezen beheerde identiteit met behulp van PowerShell toegang te geven tot het Cosmos DB-account in Azure Resource Manager:

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>`

Cosmos DB ondersteunt twee granulariteitsniveaus bij het gebruik van toegangssleutels: lees-/schrijftoegang tot het account en alleen-lezen toegang tot het account.  Wijs de rol `DocumentDB Account Contributor` toe als u sleutels voor lezen/schrijven voor het account wilt krijgen, de rol `Cosmos DB Account Reader Role` als u alleen-lezensleutels voor het account wilt ophalen.  Voor deze zelfstudie moet u de `Cosmos DB Account Reader Role` toewijzen:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Als u een bewerking niet kunt uitvoeren, hebt u mogelijk niet de juiste machtigingen. Als u schrijftoegang wilt hebben tot sleutels, moet u een Azure-rol gebruiken, zoals Inzender voor het DocumentDB-account of moet u een aangepaste rol maken. Raadpleeg [Op rollen gebaseerd Azure-toegangsbeheer in Azure Cosmos DB](../../cosmos-db/role-based-access-control.md) voor meer informatie

## <a name="access-data"></a>Toegang tot gegevens

In deze sectie wordt beschreven hoe u Azure Resource Manager kunt aanroepen met een toegangstoken voor het door het Windows-VM-systeem toegewezen beheerde identiteit. Voor de rest van de zelfstudie werken we op de virtuele machine die we eerder hebben gemaakt. 

Installeer de nieuwste versie van [Azure CLI](/cli/azure/install-azure-cli) op uw Windows-VM.

### <a name="get-an-access-token"></a>Een toegangstoken opvragen

1. In Azure Portal navigeert u naar **Virtuele machines**, gaat u naar uw virtuele Windows-machine, klikt u vervolgens boven aan de pagina **Overzicht** op **Verbinden**. 
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de virtuele Windows-machine. 
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u PowerShell in de externe sessie.
4. Dien met behulp van Powershell een Invoke-WebRequest-aanvraag in bij de lokale beheerde identiteiten om een toegangstoken voor Azure Resource Manager op te halen voor het Azure-resources-eindpunt.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > De waarde van de parameter 'resource' moet exact overeenkomen met wat er in Azure AD wordt verwacht. Wanneer u de resource-id van Azure Resource Manager gebruikt, moet u de URI opgeven met een slash op het einde.
    
   Extraheer vervolgens het element 'Content' (inhoud), dat is opgeslagen als een tekenreeks in JSON-indeling (JavaScript Object Notation) in het object $response. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Extraheer vervolgens het toegangstoken uit het antwoord.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Toegangssleutels ophalen 

In deze sectie wordt beschreven hoe u toegangssleutels kunt ophalen uit Azure Resource Manager om Cosmos DB-aanroepen te kunnen uitvoeren. We gebruiken PowerShell om Resource Manager aan te roepen met behulp van het toegangstoken dat eerder is verkregen, om de toegangssleutel voor het Cosmos DB-account op te halen. Wanneer we de toegangssleutel hebben, kunnen we query’s uitvoeren op de Cosmos DB. Gebruik uw eigen waarden om de onderstaande vermeldingen te vervangen:

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>` 
- Vervang de waarde `<ACCESS TOKEN>` door het toegangstoken dat u eerder hebt opgehaald. 

>[!NOTE]
>Als u lees-/schrijfsleutels wilt ophalen, gebruikt u sleutelbewerkingstype `listKeys`.  Als u alleen-lezensleutels wilt ophalen, gebruikt u sleutelbewerkingstype `readonlykeys`. Als u geen listkeys kunt ophalen, controleert u of u de [juiste rol](../../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role) hebt toegewezen aan de beheerde identiteit.

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/readonlykeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
In het antwoord ziet u de lijst met sleutels.  Als u bijvoorbeeld alleen-lezensleutels ophaalt:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Nu u de toegangssleutel voor het Cosmos DB-account hebt, kunt u deze doorgeven aan een Cosmos DB-SDK en aanroepen doen om toegang tot het account te krijgen.  Voor een snel voorbeeld kunt u de toegangssleutel doorgeven aan de Azure CLI.  U kunt de `<COSMOS DB CONNECTION URL>` vinden op het tabblad **Overzicht** op de Cosmos DB-accountblade in Azure Portal.  Vervang de `<ACCESS KEY>` door de waarde die u hierboven hebt opgehaald:

```azurecli
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


## <a name="disable"></a>Uitschakelen

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een door het Windows-VM-systeem toegewezen identiteit kunt gebruiken voor toegang tot Cosmos DB.  Zie voor meer informatie over Cosmos DB:

> [!div class="nextstepaction"]
>[Overzicht van Azure Cosmos DB](../../cosmos-db/introduction.md)