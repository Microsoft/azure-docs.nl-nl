---
title: Zelfstudie over roulatie voor resources met twee sets referenties
description: Gebruik deze zelfstudie om te leren hoe u het rouleren van een geheim kunt automatiseren voor resources die gebruikmaken van twee sets verificatiereferenties.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: a061cf493fba99c518448acd9c4bf4bd5949eb98
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94831805"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Het rouleren van een geheim voor resources met twee sets verificatiereferenties automatiseren

De beste manier om te verifiëren bij Azure-services is met behulp van een [beheerde identiteit](../general/authentication.md), maar in enkele gevallen is dat niet mogelijk. In dergelijke gevallen worden toegangssleutels of wachtwoorden gebruikt. Toegangssleutels en wachtwoorden moeten regelmatig worden gerouleerd.

In deze zelfstudie leert u hoe u de periodieke roulatie van geheimen voor databases en services die gebruikmaken van twee sets verificatiereferenties kunt automatiseren. Deze zelfstudie laat met name zien hoe Azure Storage-accountsleutels die in Azure Key Vault als geheimen zijn opgeslagen worden gerouleerd met behulp van een functie die wordt geactiveerd door een melding van Azure Event Grid. :

> [!NOTE]
> Sleutels van opslagaccounts kunnen automatisch worden beheerd in Key Vault door SAS-tokens (Shared Access Signature) op te geven voor gedelegeerde toegang tot het opslagaccount. Er zijn services die een opslagaccountverbindingsreeks met toegangssleutel vereisen, en voor dat scenario wordt deze oplossing aanbevolen

![Diagram van roulatieoplossing](../media/secrets/rotation-dual/rotation-diagram.png)

In de bovenstaande oplossing slaat Azure Key Vault afzonderlijke toegangssleutels voor opslagaccounts op als versies van dezelfde afwisseling van geheimen tussen de primaire en secundaire sleutel in volgende versies. Als één toegangssleutel wordt opgeslagen in de meest recente versie van het geheim, wordt er opnieuw een alternatieve sleutel gegenereerd en toegevoegd aan Key Vault als nieuwe en meest recente versie van het geheim. Deze oplossing biedt toepassingen een volledige roulatiecyclus voor het vernieuwen van de nieuwste opnieuw gegenereerde sleutel. 

1. 30 dagen voor de vervaldatum van een geheim publiceert Key Vault deze "bijna verlopende" gebeurtenis naar Event Grid.
1. Event Grid controleert de gebeurtenisabonnementen en maakt gebruik van HTTP POST om het eindpunt voor de functie-app aan te roepen die is geabonneerd op de gebeurtenis.
1. De functie-app identificeert de alternatieve sleutel (die niet de meest recente is) en roept het opslagaccount aan om deze opnieuw te genereren
1. De functie-app voegt de nieuwe opnieuw gegenereerde sleutel toe aan Azure Key Vault als nieuwe versie van het geheim.

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Azure Key Vault
* Twee Azure Storage-accounts

U kunt de onderstaande implementatiekoppeling gebruiken als u geen bestaande sleutelkluis en opslagaccounts hebt:

[![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Selecteer onder **Resourcegroep** de optie **Nieuwe maken**. Geef de groep de naam **akvrotation** en klik op **OK**.
1. Selecteer **Beoordelen en maken**.
1. Selecteer **Maken**

    ![Een resourcegroep maken](../media/secrets/rotation-dual/dual-rotation-1.png)

U hebt nu een sleutelkluis en twee opslagaccounts. U kunt deze instelling in de Azure CLI controleren door de volgende opdracht uit te voeren:

```azurecli
az resource list -o table -g akvrotation
```

Het resultaat ziet er ongeveer als volgt uit:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>De functie voor het rouleren van opslagaccountsleutels maken en implementeren

Maak vervolgens een functie-app met een door het systeem beheerde identiteit, naast de andere vereiste onderdelen, en implementeer functies voor het rouleren van opslagaccountsleutels

Deze onderdelen en configuratie zijn vereist voor de roulatiefunctie van functie-apps:
- Een Azure App Service-plan
- Er is een opslagaccount vereist voor het beheer van de functie-app-trigger
- Een toegangsbeleid voor het openen van geheimen in Key Vault
- De servicerol Sleuteloperator voor opslagaccounts toewijzen aan de functie-app voor toegang tot de toegangssleutels van het opslagaccount
- Opslagaccountroulatiefuncties met gebeurtenistrigger en http-trigger (roulatie op aanvraag)
- EventGrid-gebeurtenisabonnement voor de gebeurtenis **SecretNearExpiry**

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie: 

   [![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Selecteer in de lijst **Resourcegroep** de optie **akvrotation**.
1. Voer, in **Storage-account RG**, de naam van de resourcegroep in waar uw opslagaccount zich bevindt. Behoud de standaardwaarde **[resourceGroup().name]** als uw opslagaccount al bestaat in dezelfde resourcegroep waar u de functie voor sleutelrotatie implementeert.
1. Typ, in **Naam van opslagaccount**, de naam van het opslagaccount met de toegangssleutels die u wilt rouleren.
1. In **Key Vault RG** voert u de naam van de resourcegroep in waarin uw sleutelkluis zich bevindt. Behoud de standaardwaarde **[resourceGroup().name]** als uw sleutelkluis al bestaat in dezelfde resourcegroep waar u de functie voor sleutelrotatie implementeert.
1. Voer in **Key Vault-naam** de naam van de sleutelkluis invoeren.
1. Voer in **Naam van de functie-app** de naam van de functie-app in.
1. Typ, in **Geheime naam**, de naam van het geheim waarin de toegangssleutels zouden worden opgeslagen.
1. Typ, in **Opslagplaats-URL**, de GitHub-locatie van de functiecode ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** ).
1. Selecteer **Beoordelen en maken**.
1. Selecteer **Maken**.

   ![Het eerste opslagaccount controleren en maken](../media/secrets/rotation-dual/dual-rotation-2.png)

Nadat u de voorgaande stappen hebt voltooid, hebt u een opslagaccount, een serverfarm en een functie-app. Wanneer de implementatie is voltooid, ziet u het volgende scherm: ![Implementatie voltooid](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Als er storingen zijn, kunt u op **Opnieuw implementeren** klikken om de implementatie van resterende onderdelen te voltooien.


De code voor implementatiesjablonen en roulatiefuncties vindt u op [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Toegangssleutel voor opslagaccount toevoegen aan Key Vault

Stel eerst uw toegangsbeleid in om machtigingen voor het *beheren van geheimen* te verlenen aan gebruikers:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

U kunt nu een nieuw geheim maken met een toegangssleutel voor een opslagaccount als waarde. U hebt ook de resource-id van het opslagaccount, de geldigheidsperiode van het geheim en de sleutel-id nodig om aan het geheim toe te voegen, zodat de roulatiefunctie de sleutel in het opslagaccount opnieuw kan genereren.

Resource-id voor opslagaccount ophalen. U kunt de waarde vinden onder de eigenschap `id`
```azurecli
az storage account show -n akvrotationstorage
```

De toegangssleutels van het opslagaccount weergeven om sleutelwaarden op te halen

```azurecli
az storage account keys list -n akvrotationstorage 
```

Opgehaalde waarden invullen voor **key1Value** en **storageAccountResourceId**

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Als u een geheim met een korte vervaldatum maakt, wordt een `SecretNearExpiry`-gebeurtenis binnen enkele minuten gepubliceerd, waardoor de functie voor roulatie van het geheim wordt geactiveerd.

U kunt controleren of de toegangssleutels opnieuw zijn gegenereerd door de sleutels voor het opslagaccount en het Key Vault geheim op te halen en te vergelijken.

U kunt de gegevens van het geheim weergeven met behulp van de onderstaande opdracht:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
U ziet dat `CredentialId` is bijgewerkt om `keyName` af te wisselen, en dat `value` opnieuw wordt gegenereerd ![Uitvoer van ‘az keyvault secret show’ voor het eerste opslagaccount](../media/secrets/rotation-dual/dual-rotation-4.png)

Toegangssleutels ophalen voor het valideren van de waarde
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Uitvoer van ‘az storage account keys list’ voor het eerste opslagaccount](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Extra opslagaccounts toevoegen voor roulatie

Dezelfde functie-app kan opnieuw worden gebruikt voor het rouleren van meerdere opslagaccounts. 

Voor het toevoegen van extra sleutels voor het opslagaccount voor roulatie aan een bestaande functie is het volgende vereist:
- De servicerol Sleuteloperator voor opslagaccounts toewijzen aan de functie-app voor toegang tot de toegangssleutels van het opslagaccount
- EventGrid-gebeurtenisabonnement voor de gebeurtenis **SecretNearExpiry**

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie: 

   [![Afbeelding met een knop met het label Implementeren naar Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Selecteer in de lijst **Resourcegroep** de optie **akvrotation**.
1. Typ in **Naam van opslagaccount** de naam van het opslagaccount met de toegangssleutels die u wilt rouleren
1. Typ in **Naam voor Key Vault** de naam van de sleutelkluis
1. Typ in **Naam van de functie-app** de naam van de functie-app
1. Typ in **Geheime naam** de naam van het geheim waarin de toegangssleutels moeten worden opgeslagen
1. Selecteer **Beoordelen en maken**.
1. Selecteer **Maken**

   ![Het tweede opslagaccount controleren en maken](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Nog een toegangssleutel voor het opslagaccount toevoegen aan Key Vault

Resource-id voor opslagaccount ophalen. U kunt de waarde vinden onder de eigenschap `id`
```azurecli
az storage account show -n akvrotationstorage2
```

De toegangssleutels van het opslagaccount weergeven om de key2-waarde op te halen

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Opgehaalde waarden invullen voor **key2Value** en **storageAccountResourceId**

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Geef de gegevens van het geheim weer met behulp van de onderstaande opdracht:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
U ziet dat `CredentialId` is bijgewerkt om `keyName` af te wisselen, en dat `value` opnieuw wordt gegenereerd ![Uitvoer van ‘az keyvault secret show’ voor het tweede opslagaccount](../media/secrets/rotation-dual/dual-rotation-8.png)

Toegangssleutels ophalen voor het valideren van de waarde
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Uitvoer van ‘az storage account keys list’ voor het tweede opslagaccount](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Beschikbare Key Vault-roulatiefuncties voor dubbele referenties

- [Opslagaccount](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Meer informatie
- Overzicht: [Key Vault bewaken met Azure Event Grid](../general/event-grid-overview.md)
- Procedure: [Uw eerste functie maken in de Azure-portal](../../azure-functions/functions-create-first-azure-function.md)
- Procedure: [E-mail ontvangen wanneer een sleutelkluisgeheim verandert](../general/event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault](../../event-grid/event-schema-key-vault.md)
