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
ms.openlocfilehash: f208752f13848f0f54648d934d1dfb518e2ea1fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500337"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Het roteren van een geheim automatiseren voor resources met twee sets verificatiereferenties

De beste manier om te verifiëren bij Azure-services is met behulp van een [beheerde identiteit](../general/authentication.md), maar in enkele gevallen is dat niet mogelijk. In dergelijke gevallen worden toegangssleutels of wachtwoorden gebruikt. U moet toegangssleutels en wachtwoorden regelmatig roteren.

In deze zelfstudie leert u hoe u de periodieke roulatie van geheimen voor databases en services die gebruikmaken van twee sets verificatiereferenties kunt automatiseren. Deze zelfstudie laat met name zien hoe u Azure Storage-accountsleutels kunt roteren die in Azure Key Vault zijn opgeslagen als geheimen. U gebruikt een functie die wordt geactiveerd via een Azure Event Grid-melding. 

> [!NOTE]
> Sleutels van opslagaccounts kunnen automatisch worden beheerd in Key Vault als u SAS-tokens (Shared Access Signature) opgeeft voor gedelegeerde toegang tot het opslagaccount. Er zijn services waarvoor verbindingsreeksen met toegangssleutels voor het opslagaccount zijn vereist. Voor dit scenario bevelen we deze oplossing aan.

Dit is de rotatieoplossing die wordt beschreven in deze zelfstudie: 

![Diagram van de rotatieoplossing.](../media/secrets/rotation-dual/rotation-diagram.png)

In deze oplossing worden afzonderlijke toegangssleutels voor opslagaccounts opgeslagen in Azure Key Vault als versies van hetzelfde geheim, die in volgende versies wisselen tussen de primaire en secundaire sleutel. Wanneer de ene toegangssleutel wordt opgeslagen in de nieuwste versie van het geheim, wordt de andere sleutel opnieuw gegenereerd en toegevoegd aan Key Vault als de nieuwe meest recente versie van het geheim. De oplossing biedt de volledige rotatiecyclus van de toepassing voor het vernieuwen van de nieuwste opnieuw gegenereerde sleutel. 

1. Dertig dagen vóór de vervaldatum van een geheim wordt in Key Vault deze bijna verlopende gebeurtenis gepubliceerd naar Event Grid.
1. De gebeurtenisabonnementen worden gecontroleerd in Event Grid en HTTP POST wordt gebruikt om het eindpunt voor de functie-app aan te roepen die is geabonneerd op de gebeurtenis.
1. De functie-app identificeert de alternatieve sleutel (niet de meest recente) en roept het opslagaccount aan om deze opnieuw te genereren.
1. De functie-app voegt de nieuwe opnieuw gegenereerde sleutel toe aan Azure Key Vault als de nieuwe versie van het geheim.

## <a name="prerequisites"></a>Vereisten
* Een Azure-abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure Key Vault.
* Twee Azure-opslagaccounts.

U kunt deze implementatiekoppeling gebruiken als u geen bestaande sleutelkluis en bestaande opslagaccounts hebt:

[![Koppeling met het label Implementeren in Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Selecteer onder **Resourcegroep** de optie **Nieuwe maken**. Geef de groep de naam **akvrotation** en selecteer vervolgens **OK**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

    ![Schermopname van het maken van een resourcegroep.](../media/secrets/rotation-dual/dual-rotation-1.png)

U hebt nu een sleutelkluis en twee opslagaccounts. U kunt deze configuratie controleren in Azure CLI door deze opdracht uit te voeren:

```azurecli
az resource list -o table -g akvrotation
```

De resultaten zien er ongeveer uit zoals deze uitvoer:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>De sleutelrotatiefunctie maken en implementeren

Vervolgens maakt u een functie-app met een in het systeem beheerde identiteit, naast andere vereiste onderdelen. U implementeert ook de rotatiefunctie voor de sleutels van de opslagaccounts.

De volgende onderdelen en configuratie zijn vereist voor de rotatiefunctie van de functie-app:
- Een Azure App Service-plan
- Een opslagaccount om de triggers voor de functie-app te beheren
- Een toegangsbeleid voor het openen van geheimen in Key Vault
- De servicerol Sleuteloperator voor opslagaccounts toegewezen aan de functie-app, voor toegang tot de toegangssleutels van het opslagaccount
- Een sleutelrotatiefunctie met een gebeurtenistrigger en een HTTP-trigger (roulatie op aanvraag)
- Een Event Grid-gebeurtenisabonnement voor de gebeurtenis **SecretNearExpiry**

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie: 

   [![Koppeling voor de Azure-sjabloonimplementatie.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Selecteer in de lijst **Resourcegroep** de optie **akvrotation**.
1. Voer in het vak **Opslagaccount-RG** de naam in van de resourcegroep waarin het opslagaccount zich bevindt. Behoud de standaardwaarde **[resourceGroup().name]** als het opslagaccount zich al in dezelfde resourcegroep bevindt waar u de sleutelrotatiefunctie implementeert.
1. Voer in het vak **Naam van opslagaccount** de naam in van het opslagaccount dat de toegangssleutels bevat die moeten worden geroteerd.
1. Voer in het vak **Sleutelkluis-RG** de naam in van de resourcegroep waarin uw sleutelkluis zich bevindt. Behoud de standaardwaarde **[resourceGroup().name]** als de sleutelkluis al bestaat in dezelfde resourcegroep waar u de sleutelrotatiefunctie implementeert.
1. Voer in het vak **Naam van sleutelkluis** de naam in van de sleutelkluis.
1. Voer in het vak **Naam van functie-app** de naam in van de functie-app.
1. Voer in het vak **Naam van geheim** de naam in van het geheim waar u de toegangssleutels wilt opslaan.
1. Voer in het vak **Opslagplaats-URL** de GitHub-locatie in van de functiecode: **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

   ![Schermopname van het maken van het eerste opslagaccount.](../media/secrets/rotation-dual/dual-rotation-2.png)

Als u de voorgaande stappen hebt voltooid, beschikt u over een opslagaccount, een serverfarm, een functie-app, en Application Insights. Wanneer de implementatie is voltooid, ziet u deze pagina: ![Schermopname van de pagina Uw implementatie is voltooid.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Als er een fout optreedt, kunt u **Opnieuw implementeren** selecteren om de implementatie van onderdelen te voltooien.


In [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell) vindt u implementatiesjablonen en code voor de roulatiefunctie.

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>De toegangssleutels voor een opslagaccount toevoegen aan Key Vault

Stel eerst uw toegangsbeleid in om machtigingen voor het **beheren van geheimen** te verlenen aan gebruikers:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

U kunt nu een nieuw geheim maken met een toegangssleutel voor het opslagaccount als bijbehorende waarde. U moet ook de resource-id van het opslagaccount, de geldigheidsperiode van het geheim, en de sleutel-id toevoegen aan het geheim, zodat de rotatiefunctie de sleutel in het opslagaccount opnieuw kan genereren.

Achterhaal de resource-id voor het opslagaccount. U vindt deze waarde in de eigenschap `id`.
```azurecli
az storage account show -n akvrotationstorage
```

Vermeld de toegangssleutels van het opslagaccount zodat u de sleutelwaarden kunt ophalen:

```azurecli
az storage account keys list -n akvrotationstorage 
```

Voer deze opdracht uit met behulp van de opgehaalde waarden voor `key1Value` en `storageAccountResourceId`:

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Als u een geheim maakt met een korte verloopdatum, wordt binnen enkele minuten een gebeurtenis `SecretNearExpiry` gepubliceerd. Deze gebeurtenis activeert vervolgens de functie voor het roteren van het geheim.

U kunt controleren of de toegangssleutels opnieuw zijn gegenereerd door de opslagaccountsleutel en het Key Vault-geheim op te halen en te vergelijken.

Gebruik deze opdracht om de informatie over het geheim op te halen:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
U ziet dat `CredentialId` is bijgewerkt naar de andere `keyName`, en dat `value` opnieuw is gegenereerd: ![Schermopname van de uitvoer van de opdracht a z keyvault secret show voor het eerste opslagaccount.](../media/secrets/rotation-dual/dual-rotation-4.png)

Haal de toegangssleutels op om de waarden te vergelijken:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Schermopname van de uitvoer van de opdracht a z storage account keys list voor het eerste opslagaccount.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Opslagaccounts toevoegen voor rotatie

U kunt dezelfde functie-app opnieuw gebruiken om sleutels voor meerdere opslagaccounts te roteren. 

Als u opslagaccountsleutels wilt toevoegen aan een bestaande functie voor rotatie, hebt u het volgende nodig:
- De servicerol Sleuteloperator voor opslagaccounts toegewezen aan de functie-app, voor toegang tot de toegangssleutels van opslagaccounts.
- Een Event Grid-gebeurtenisabonnement voor de gebeurtenis **SecretNearExpiry**.

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie: 

   [![Koppeling voor de Azure-sjabloonimplementatie.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Selecteer in de lijst **Resourcegroep** de optie **akvrotation**.
1. Voer in het vak **Naam van opslagaccount** de naam in van het opslagaccount dat de toegangssleutels bevat die moeten worden geroteerd.
1. Voer in het vak **Naam van sleutelkluis** de naam in van de sleutelkluis.
1. Voer in het vak **Naam van functie-app** de naam in van de functie-app.
1. Voer in het vak **Naam van geheim** de naam in van het geheim waar u de toegangssleutels wilt opslaan.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

   ![Schermopname van het maken van een extra opslagaccount.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Nog een toegangssleutel voor het opslagaccount toevoegen aan Key Vault

Achterhaal de resource-id voor het opslagaccount. U vindt deze waarde in de eigenschap `id`.
```azurecli
az storage account show -n akvrotationstorage2
```

Vermeld de toegangssleutels van het opslagaccount zodat u de waarde van sleutel 2 kunt ophalen:

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Voer deze opdracht uit met behulp van de opgehaalde waarden voor `key2Value` en `storageAccountResourceId`:

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Gebruik deze opdracht om de informatie over het geheim op te halen:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
U ziet dat `CredentialId` is bijgewerkt naar de andere `keyName`, en dat `value` opnieuw is gegenereerd: ![Schermopname van de uitvoer van de opdracht a z keyvault secret show voor het tweede opslagaccount.](../media/secrets/rotation-dual/dual-rotation-8.png)

Haal de toegangssleutels op om de waarden te vergelijken:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Schermopname van de uitvoer van de opdracht a z storage account keys list voor het tweede opslagaccount.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Key Vault-rotatiefuncties voor dubbele referenties

- [Opslagaccount](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>Volgende stappen
- Overzicht: [Key Vault bewaken met Azure Event Grid](../general/event-grid-overview.md)
- Procedure: [Uw eerste functie maken in de Azure-portal](../../azure-functions/functions-create-first-azure-function.md)
- Procedure: [E-mail ontvangen wanneer een Key Vault-geheim is gewijzigd](../general/event-grid-logicapps.md)
- Naslaginformatie: [Azure Event Grid-gebeurtenisschema voor Azure Key Vault](../../event-grid/event-schema-key-vault.md)
