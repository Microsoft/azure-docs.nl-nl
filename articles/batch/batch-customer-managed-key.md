---
title: Door de klant beheerde sleutels voor uw Azure Batch account configureren met Azure Key Vault en beheerde identiteit
description: Meer informatie over het versleutelen van batch gegevens met door de klant beheerde sleutels.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100385061"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Door de klant beheerde sleutels voor uw Azure Batch account configureren met Azure Key Vault en beheerde identiteit

Standaard Azure Batch gebruikt door het platform beheerde sleutels voor het versleutelen van alle klant gegevens die zijn opgeslagen in de Azure Batch-service, zoals certificaten, taak-en taak gegevens. U kunt desgewenst uw eigen sleutels gebruiken, dat wil zeggen door de klant beheerde sleutels, om gegevens te versleutelen die zijn opgeslagen in Azure Batch.

De sleutels die u opgeeft, moeten worden gegenereerd in [Azure Key Vault](../key-vault/general/basic-concepts.md)en moeten worden geopend met [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md).

Er zijn twee soorten beheerde identiteiten: [ *systeem toegewezen* en door de *gebruiker toegewezen*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

U kunt een batch-account maken met een door het systeem toegewezen beheerde identiteit of een afzonderlijke door de gebruiker toegewezen beheerde identiteit maken die toegang heeft tot de door de klant beheerde sleutels. Bekijk de [vergelijkings tabel](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) om de verschillen te begrijpen en te bepalen welke optie het meest geschikt is voor uw oplossing. Als u bijvoorbeeld dezelfde beheerde identiteit wilt gebruiken om toegang te krijgen tot meerdere Azure-resources, is er een door de gebruiker toegewezen beheerde identiteit nodig. Als dat niet het geval is, kan een door het systeem toegewezen beheerde identiteit die is gekoppeld aan uw batch-account, voldoende zijn. Het gebruik van een door de gebruiker toegewezen beheerde identiteit biedt u de mogelijkheid om door de klant beheerde sleutels af te dwingen tijdens het maken van een batch-account, zoals wordt weer gegeven [in het onderstaande voor beeld](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Een batch-account maken met door het systeem toegewezen beheerde identiteit

Als u geen afzonderlijke door de gebruiker toegewezen beheerde identiteit nodig hebt, kunt u door het systeem toegewezen beheerde identiteit inschakelen wanneer u uw batch-account maakt.

### <a name="azure-portal"></a>Azure Portal

In de [Azure Portal](https://portal.azure.com/), wanneer u batch-accounts maakt, kiest u **systeem toegewezen** in het identiteits type onder het tabblad **Geavanceerd** .

![Scherm opname van een nieuw batch-account met een door het systeem toegewezen identiteits type.](./media/batch-customer-managed-key/create-batch-account.png)

Nadat het account is gemaakt, kunt u een unieke GUID vinden in het veld **id-Principal-id** onder de sectie **Eigenschappen** . Het **identiteits type** wordt weer gegeven `System assigned` .

![Scherm opname met een unieke GUID in het veld id-Principal-id.](./media/batch-customer-managed-key/linked-batch-principal.png)

U hebt deze waarde nodig om dit batch-account toegang te verlenen tot de Key Vault.

### <a name="azure-cli"></a>Azure CLI

Wanneer u een nieuw batch-account maakt, moet u `SystemAssigned` voor de `--identity` para meter opgeven.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Nadat het account is gemaakt, kunt u controleren of de door het systeem toegewezen beheerde identiteit is ingeschakeld voor dit account. Noteer de `PrincipalId` , aangezien deze waarde nodig is om dit batch-account toegang te verlenen tot de Key Vault.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> De door het systeem toegewezen beheerde identiteit die is gemaakt in een batch-account wordt alleen gebruikt voor het ophalen van door de klant beheerde sleutels van de Key Vault. Deze identiteit is niet beschikbaar in batch-Pools. Zie [beheerde identiteiten configureren in batch-Pools](managed-identity-pools.md)als u een door de gebruiker toegewezen beheerde identiteit wilt gebruiken in een pool.

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Indien gewenst kunt u een door de [gebruiker toegewezen beheerde identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) die kan worden gebruikt voor toegang tot uw door de klant beheerde sleutels.

U hebt de **client-ID-** waarde van deze identiteit nodig om toegang te krijgen tot de Key Vault.

## <a name="configure-your-azure-key-vault-instance"></a>Uw Azure Key Vault-exemplaar configureren

De Azure Key Vault waarin uw sleutels worden gegenereerd, moeten in dezelfde Tenant worden gemaakt als uw batch-account. Deze hoeft zich niet in dezelfde resource groep te benemen of zelfs in hetzelfde abonnement.

### <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken

Wanneer [u een Azure Key Vault-exemplaar](../key-vault/general/quick-create-portal.md) met door de klant beheerde sleutels voor Azure batch maakt, moet u ervoor zorgen dat de beveiliging van de functie voor **voorlopig verwijderen** en **leegmaken** is ingeschakeld.

![Scherm afbeelding van het Key Vault maken.](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Een toegangs beleid toevoegen aan uw Azure Key Vault-exemplaar

Voeg in de Azure Portal, nadat de Key Vault is gemaakt, in het **toegangs beleid** onder **instelling**, het batch-account toegang toe met behulp van beheerde identiteit. Selecteer bij **sleutel machtigingen** **Get**, **Inpakken** en de **toets inpakken**.

![Scherm afbeelding met het venster toegangs beleid toevoegen.](./media/batch-customer-managed-key/key-permissions.png)

Vul in het veld **selecteren** onder **Principal** een van de volgende opties in:

- Voor door het systeem toegewezen beheerde identiteit: Voer de `principalId` waarde in die u eerder hebt opgehaald of de naam van het batch-account.
- Voor door de gebruiker toegewezen beheerde identiteit: Voer de **client-id** in die u eerder hebt opgehaald of de naam van de door de gebruiker toegewezen beheerde identiteit.

![Scherm afbeelding van het hoofd scherm.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Een sleutel in Azure Key Vault genereren

In de Azure Portal gaat u naar de Key Vault instantie in de sectie **sleutel** en selecteert u **genereren/importeren**. Selecteer het **sleutel type** en de `RSA` **RSA-sleutel grootte** moet ten minste `2048` bits zijn. `EC` sleutel typen worden momenteel niet ondersteund als een door de klant beheerde sleutel voor een batch-account.

![Een sleutel maken](./media/batch-customer-managed-key/create-key.png)

Nadat de sleutel is gemaakt, klikt u op de zojuist gemaakte sleutel en de huidige versie en kopieert u de **sleutel-id** onder **Eigenschappen** sectie.  Zorg ervoor dat onder **toegestane bewerkingen**, **Terugloop sleutel** en de **sleutel voor uitpakken** beide zijn ingeschakeld.

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Door de klant beheerde sleutels inschakelen voor een batch-account

Nadat u de bovenstaande stappen hebt gevolgd, kunt u door de klant beheerde sleutels inschakelen voor uw batch-account.

### <a name="azure-portal"></a>Azure Portal

Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina batch-account. Schakel onder de sectie **versleuteling** de door de **klant beheerde sleutel** in. U kunt de sleutel-id rechtstreeks gebruiken, of u kunt de sleutel kluis selecteren en vervolgens op **een sleutel kluis en sleutel selecteren** klikken.

![Scherm opname van de sectie versleuteling en de optie voor het inschakelen van door de klant beheerde sleutel](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

Nadat het batch-account is gemaakt met een door het systeem toegewezen beheerde identiteit en de toegang tot Key Vault is verleend, werkt u het batch-account bij met de `{Key Identifier}` URL onder `keyVaultProperties` para meter. Stel ook **encryption_key_source** in als `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Een batch-account maken met door de gebruiker toegewezen beheerde identiteits-en door de klant beheerde sleutels

Met de Batch Management .NET-client kunt u een batch-account maken dat een door de gebruiker toegewezen beheerde identiteit en door de klant beheerde sleutels heeft.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>De door de klant beheerde sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, werkt u het batch-account bij voor het gebruik van de nieuwe versie. Volg deze stappen:

1. Navigeer naar uw batch-account in Azure Portal en geef de versleutelings instellingen weer.
2. Voer de URI in voor de nieuwe sleutel versie. U kunt ook de Key Vault en de sleutel opnieuw selecteren om de versie bij te werken.
3. Sla uw wijzigingen op.

U kunt ook Azure CLI gebruiken om de versie bij te werken.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Een andere sleutel gebruiken voor batch versleuteling

Voer de volgende stappen uit om de sleutel te wijzigen die wordt gebruikt voor batch versleuteling:

1. Navigeer naar uw batch-account en geef de versleutelings instellingen weer.
2. Voer de URI in voor de nieuwe sleutel. U kunt ook de Key Vault selecteren en een nieuwe sleutel kiezen.
3. Sla uw wijzigingen op.

U kunt ook Azure CLI gebruiken voor het gebruik van een andere sleutel.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

- **Worden door de klant beheerde sleutels ondersteund voor bestaande batch-accounts?** Nee. Door de klant beheerde sleutels worden alleen ondersteund voor nieuwe batch-accounts.
- **Kan ik RSA-sleutel grootten selecteren die groter zijn dan 2048 bits?** Ja, RSA-sleutel groottes `3072` en `4096` bits worden ook ondersteund.
- **Welke bewerkingen zijn beschikbaar nadat een door de klant beheerde sleutel is ingetrokken?** De enige bewerking die is toegestaan, is het verwijderen van een account als batch de toegang tot de door de klant beheerde sleutel verliest.
- **Hoe kan ik de toegang tot mijn batch-account herstellen als ik de Key Vault sleutel per ongeluk verwijder?** Aangezien het opschonen van de beveiliging en het voorlopig verwijderen zijn ingeschakeld, kunt u de bestaande sleutels herstellen. Zie [een Azure Key Vault herstellen](../key-vault/general/key-vault-recovery.md)voor meer informatie.
- **Kan ik door de klant beheerde sleutels uitschakelen?** U kunt het versleutelings type van het batch-account op elk gewenst moment weer instellen op ' micro soft Managed key '. Daarna kunt u de sleutel verwijderen of wijzigen.
- **Hoe kan ik mijn sleutels draaien?** Door de klant beheerde sleutels worden niet automatisch gedraaid. Als u de sleutel wilt draaien, werkt u de sleutel-id bij waaraan het account is gekoppeld.
- **Hoe lang duurt het voordat de toegang tot het batch-account is hersteld?** Het kan tot tien minuten duren voordat het account weer toegankelijk is nadat de toegang is hersteld.
- **Wat gebeurt er met mijn resources terwijl het batch-account niet beschikbaar is?** Alle groepen die worden uitgevoerd wanneer batch toegang tot door de klant beheerde sleutels verloren gaat, blijven actief. De knoop punten gaan echter over naar een niet-beschik bare status en taken worden niet meer uitgevoerd (en worden opnieuw in de wachtrij gezet). Zodra de toegang is hersteld, worden knoop punten weer beschikbaar en worden de taken opnieuw gestart.
- **Is dit versleutelings mechanisme van toepassing op VM-schijven in een batch-pool?** Nee. Voor Cloud service-configuratie groepen wordt er geen versleuteling toegepast voor het besturings systeem en de tijdelijke schijf. Voor virtuele-machine configuratie groepen worden het besturings systeem en de opgegeven gegevens schijven standaard versleuteld met een door micro soft platform beheerde sleutel. Op dit moment kunt u niet uw eigen sleutel opgeven voor deze schijven. Als u de tijdelijke schijf van Vm's voor een batch-pool wilt versleutelen met een door micro soft platform beheerde sleutel, moet u de eigenschap [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) in de configuratie groep van de [virtuele machine](/rest/api/batchservice/pool/add#virtualmachineconfiguration) inschakelen. Voor zeer gevoelige omgevingen wordt u aangeraden tijdelijke schijf versleuteling in te scha kelen en te voor komen dat gevoelige gegevens worden opgeslagen op het besturings systeem en gegevens schijven. Zie [een groep maken met schijf versleuteling is ingeschakeld](./disk-encryption.md) voor meer informatie.
- **Is de door het systeem toegewezen beheerde identiteit voor het batch-account dat beschikbaar is op de reken knooppunten?** Nee. De door het systeem toegewezen beheerde identiteit wordt momenteel alleen gebruikt voor toegang tot de Azure Key Vault voor de door de klant beheerde sleutel. Zie [beheerde identiteiten configureren in batch-Pools](managed-identity-pools.md)als u een door de gebruiker toegewezen beheerde identiteit wilt gebruiken op reken knooppunten.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Best practices voor beveiliging in azure batch](security-best-practices.md).
- Meer informatie over[Azure Key Vault](../key-vault/general/basic-concepts.md).
