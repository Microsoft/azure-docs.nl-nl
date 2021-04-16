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
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 1f656a41b0f447b90f58ec14173e418a2defb72e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484826"
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
* Azure [Cloud Shell](https://shell.azure.com/). In deze zelfstudie wordt gebruikgemaakt van de portal Cloud Shell met PowerShell-omgeving
* Azure Key Vault.
* Twee Azure-opslagaccounts.

U kunt deze implementatiekoppeling gebruiken als u geen bestaande sleutelkluis en bestaande opslagaccounts hebt:

[![Koppeling met het label Implementeren in Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Selecteer onder **Resourcegroep** de optie **Nieuwe maken**. Geef de groep de naam **vaultrotation** en selecteer vervolgens **OK**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

    ![Schermopname van het maken van een resourcegroep.](../media/secrets/rotation-dual/dual-rotation-1.png)

U hebt nu een sleutelkluis en twee opslagaccounts. U kunt deze installatie controleren in de Azure CLI of Azure PowerShell door deze opdracht uit te voeren:
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

De resultaten zien er ongeveer uit zoals deze uitvoer:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
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

   [![Koppeling voor de Azure-sjabloonimplementatie.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Selecteer in de lijst **Resourcegroep** de optie **vaultrotation**.
1. Voer in het vak **Opslagaccount-RG** de naam in van de resourcegroep waarin het opslagaccount zich bevindt. Behoud de standaardwaarde **[resourceGroup().name]** als het opslagaccount zich al in dezelfde resourcegroep bevindt waar u de sleutelrotatiefunctie implementeert.
1. Voer in het vak **Naam van opslagaccount** de naam in van het opslagaccount dat de toegangssleutels bevat die moeten worden geroteerd. Laat de standaardwaarde **[concat(resourceGroup().name, 'storage')]** ongewijzigd als u het opslagaccount gebruikt dat u hebt gemaakt in [Vereisten](#prerequisites).
1. Voer in het vak **Sleutelkluis-RG** de naam in van de resourcegroep waarin uw sleutelkluis zich bevindt. Behoud de standaardwaarde **[resourceGroup().name]** als de sleutelkluis al bestaat in dezelfde resourcegroep waar u de sleutelrotatiefunctie implementeert.
1. Voer in het vak **Naam van sleutelkluis** de naam in van de sleutelkluis. Laat de standaardwaarde **[concat(resourceGroup().name, '-kv')]** ongewijzigd als u de sleutelkluis gebruikt die u hebt gemaakt in [Vereisten](#prerequisites).
1. Selecteer in het vak **Type App Service-plan** het hostingabonnement. **Een Premium-abonnement** hebt u alleen nodig als uw sleutelkluis zich achter een firewall bevindt.
1. Voer in het vak **Naam van functie-app** de naam in van de functie-app.
1. Voer in het vak **Naam van geheim** de naam in van het geheim waar u de toegangssleutels wilt opslaan.
1. Voer in het vak **Opslagplaats-URL** de GitHub-locatie in van de functiecode. In deze zelfstudie kunt u gebruikmaken van **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

   ![Schermopname waarin u ziet hoe u een functie-app maakt en implementeert.](../media/secrets/rotation-dual/dual-rotation-2.png)

Als u de voorgaande stappen hebt voltooid, beschikt u over een opslagaccount, een serverfarm, een functie-app, en Application Insights. Wanneer de implementatie is voltooid, ziet u deze pagina:

   ![Schermopname van de pagina Uw implementatie is voltooid.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Als er een fout optreedt, kunt u **Opnieuw implementeren** selecteren om de implementatie van onderdelen te voltooien.


In [Azure-voorbeelden](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell) vindt u implementatiesjablonen en code voor de rotatiefunctie.

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>De toegangssleutels voor een opslagaccount toevoegen aan Key Vault

Stel eerst uw toegangsbeleid in om machtigingen voor het **beheren van geheimen** te verlenen aan uw user principal:
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

U kunt nu een nieuw geheim maken met een toegangssleutel voor het opslagaccount als bijbehorende waarde. U moet ook de resource-id van het opslagaccount, de geldigheidsperiode van het geheim, en de sleutel-id toevoegen aan het geheim, zodat de rotatiefunctie de sleutel in het opslagaccount opnieuw kan genereren.

Achterhaal de resource-id voor het opslagaccount. U vindt deze waarde in de eigenschap `id`.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Vermeld de toegangssleutels van het opslagaccount zodat u de sleutelwaarden kunt ophalen:
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Voeg een geheim toe aan de sleutelkluis waarvan de vervaldatum is ingesteld op morgen, stel de geldigheidsperiode in op 60 dagen en voeg de resource-id van het opslagaccount toe. Voer deze opdracht uit met behulp van de opgehaalde waarden voor `key1Value` en `storageAccountResourceId`:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

In het bovenstaande geheim wordt gebeurtenis `SecretNearExpiry` binnen enkele minuten geactiveerd. Deze gebeurtenis activeert vervolgens de functie voor het roteren van het geheim waarvoor de vervaldatum van 60 dagen is ingesteld. In die configuratie wordt de gebeurtenis 'SecretNearExpiry' elke 30 dagen geactiveerd (30 dagen vóór de vervaldatum) en zal de rotatiefunctie om en om tussen sleutel1 en sleutel2 roteren.

U kunt controleren of de toegangssleutels opnieuw zijn gegenereerd door de opslagaccountsleutel en het Key Vault-geheim op te halen en te vergelijken.

Gebruik deze opdracht om de informatie over het geheim op te halen:
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

U ziet dat `CredentialId` is bijgewerkt naar de andere `keyName`, en dat `value` opnieuw is gegenereerd:

![Schermopname van de uitvoer van de opdracht a z keyvault secret show voor het eerste opslagaccount.](../media/secrets/rotation-dual/dual-rotation-4.png)

Haal de toegangssleutels op om de waarden te vergelijken:
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

U ziet dat `value` van de sleutel gelijk is aan het geheim in de sleutelkluis:

![Schermopname van de uitvoer van de opdracht a z storage account keys list voor het eerste opslagaccount.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Opslagaccounts toevoegen voor rotatie

U kunt dezelfde functie-app opnieuw gebruiken om sleutels voor meerdere opslagaccounts te roteren. 

Als u opslagaccountsleutels wilt toevoegen aan een bestaande functie voor rotatie, hebt u het volgende nodig:
- De servicerol Sleuteloperator voor opslagaccounts toegewezen aan de functie-app, voor toegang tot de toegangssleutels van opslagaccounts.
- Een Event Grid-gebeurtenisabonnement voor de gebeurtenis **SecretNearExpiry**.

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie: 

   [![Koppeling voor de Azure-sjabloonimplementatie.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Selecteer in de lijst **Resourcegroep** de optie **vaultrotation**.
1. Voer in het vak **Opslagaccount-RG** de naam in van de resourcegroep waarin het opslagaccount zich bevindt. Behoud de standaardwaarde **[resourceGroup().name]** als het opslagaccount zich al in dezelfde resourcegroep bevindt waar u de sleutelrotatiefunctie implementeert.
1. Voer in het vak **Naam van opslagaccount** de naam in van het opslagaccount dat de toegangssleutels bevat die moeten worden geroteerd.
1. Voer in het vak **Sleutelkluis-RG** de naam in van de resourcegroep waarin uw sleutelkluis zich bevindt. Behoud de standaardwaarde **[resourceGroup().name]** als de sleutelkluis al bestaat in dezelfde resourcegroep waar u de sleutelrotatiefunctie implementeert.
1. Voer in het vak **Naam van sleutelkluis** de naam in van de sleutelkluis.
1. Voer in het vak **Naam van functie-app** de naam in van de functie-app.
1. Voer in het vak **Naam van geheim** de naam in van het geheim waar u de toegangssleutels wilt opslaan.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

   ![Schermopname van het maken van een extra opslagaccount.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Nog een toegangssleutel voor het opslagaccount toevoegen aan Key Vault

Achterhaal de resource-id voor het opslagaccount. U vindt deze waarde in de eigenschap `id`.
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Vermeld de toegangssleutels van het opslagaccount zodat u de waarde van sleutel 2 kunt ophalen:
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Voeg een geheim toe aan de sleutelkluis waarvan de vervaldatum is ingesteld op morgen, stel de geldigheidsperiode in op 60 dagen en voeg de resource-id van het opslagaccount toe. Voer deze opdracht uit met behulp van de opgehaalde waarden voor `key2Value` en `storageAccountResourceId`:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Gebruik deze opdracht om de informatie over het geheim op te halen:
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

U ziet dat `CredentialId` is bijgewerkt naar de andere `keyName`, en dat `value` opnieuw is gegenereerd:

![Schermopname van de uitvoer van de opdracht a z keyvault secret show voor het tweede opslagaccount.](../media/secrets/rotation-dual/dual-rotation-8.png)

Haal de toegangssleutels op om de waarden te vergelijken:
# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

U ziet dat `value` van de sleutel gelijk is aan het geheim in de sleutelkluis:

![Schermopname van de uitvoer van de opdracht a z storage account keys list voor het tweede opslagaccount.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Rotatiefuncties van Key Vault voor twee referentiesets

De sjabloon voor roulatiefuncties voor twee referentiesets en verschillende kant-en-klare functies:

- [Projectsjabloon](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Redis Cache](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Opslagaccount](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> De bovenstaande roulatiefuncties zijn gemaakt door een lid van de community en niet door Microsoft. Azure Functions van de community worden niet ondersteund door een ondersteuningsprogramma of -service van Microsoft en worden beschikbaar gesteld zoals ze zijn zonder enige vorm van garantie.

## <a name="next-steps"></a>Volgende stappen

- Zelfstudie: [Geheimen roteren voor één referentieset](./tutorial-rotation.md)
- Overzicht: [Key Vault bewaken met Azure Event Grid](../general/event-grid-overview.md)
- Procedure: [Uw eerste functie maken in de Azure-portal](../../azure-functions/functions-get-started.md)
- Procedure: [E-mail ontvangen wanneer een Key Vault-geheim is gewijzigd](../general/event-grid-logicapps.md)
- Naslaginformatie: [Azure Event Grid-gebeurtenisschema voor Azure Key Vault](../../event-grid/event-schema-key-vault.md)
