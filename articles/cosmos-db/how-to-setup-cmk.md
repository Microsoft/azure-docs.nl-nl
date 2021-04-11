---
title: Door de klant beheerde sleutels voor uw Azure Cosmos DB account configureren
description: Meer informatie over het configureren van door de klant beheerde sleutels voor uw Azure Cosmos DB-account met Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/19/2021
ms.author: thweiss
ms.openlocfilehash: 3ee566a598ea7fdf060712c934305ef63467e548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101656513"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Door de klant beheerde sleutels configureren voor uw Azure Cosmos-account met Azure Key Vault
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Voor het gebruik van door de klant beheerde sleutels met de Azure Cosmos DB [analytische opslag](analytical-store-introduction.md) is momenteel extra configuratie vereist voor uw account. Neem contact op met [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) voor meer informatie.

Gegevens die zijn opgeslagen in uw Azure Cosmos-account, worden automatisch en naadloos versleuteld met sleutels die worden beheerd door micro soft (door **service beheerde sleutels**). U kunt desgewenst een tweede laag versleuteling toevoegen met sleutels die u beheert (door de **klant beheerde sleutels**).

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Lagen van versleuteling rondom klant gegevens":::

U moet door de klant beheerde sleutels opslaan in [Azure Key Vault](../key-vault/general/overview.md) en een sleutel opgeven voor elk Azure Cosmos-account dat is ingeschakeld met door de klant beheerde sleutels. Deze sleutel wordt gebruikt voor het versleutelen van alle gegevens die in dat account zijn opgeslagen.

> [!NOTE]
> Op dit moment zijn door de klant beheerde sleutels alleen beschikbaar voor nieuwe Azure Cosmos-accounts. U moet ze configureren tijdens het maken van het account.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> De Azure Cosmos DB resource provider registreren voor uw Azure-abonnement

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/), ga naar uw Azure-abonnement en selecteer **resource providers** op het tabblad **instellingen** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Resource providers-vermelding in het menu links":::

1. Zoek naar de resource provider van de **Microsoft.DocumentDB** . Controleer of de resource provider al is gemarkeerd als geregistreerd. Als dat niet het geval is, kiest u de resource provider en selecteert u **registreren**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="De resource provider voor de Microsoft.DocumentDB registreren":::

## <a name="configure-your-azure-key-vault-instance"></a>Uw Azure Key Vault-exemplaar configureren

Voor het gebruik van door de klant beheerde sleutels met Azure Cosmos DB moet u twee eigenschappen instellen voor het Azure Key Vault exemplaar dat u wilt gebruiken voor het hosten van de versleutelings sleutels: **voorlopig verwijderen** en **beveiliging opschonen**.

Als u een nieuw exemplaar van Azure Key Vault maakt, schakelt u deze eigenschappen tijdens het maken in:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Zacht verwijderen inschakelen en de beveiliging opschonen voor een nieuw Azure Key Vault-exemplaar":::

Als u een bestaand Azure Key Vault exemplaar gebruikt, kunt u controleren of deze eigenschappen zijn ingeschakeld door te kijken naar de sectie **Eigenschappen** op de Azure Portal. Als een van deze eigenschappen niet is ingeschakeld, raadpleegt u de secties ' voorlopig verwijderen inschakelen ' en ' beveiliging opschonen inschakelen ' in een van de volgende artikelen:

- [Voorlopig verwijderen gebruiken met PowerShell](../key-vault/general/key-vault-recovery.md)
- [Voorlopig verwijderen gebruiken met Azure CLI](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Een toegangs beleid toevoegen aan uw Azure Key Vault-exemplaar

1. Ga vanuit het Azure Portal naar het Azure Key Vault exemplaar dat u wilt gebruiken voor het hosten van de versleutelings sleutels. Selecteer **toegangs beleid** in het menu links:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Beleids regels openen vanuit het menu links":::

1. Selecteer **+ toegangs beleid toevoegen**.

1. Selecteer in de vervolg keuzelijst **sleutel machtigingen** de optie **ophalen**, **uitpakken van sleutel** en **verpakkende sleutel** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="De juiste machtigingen selecteren":::

1. Selecteer onder **Principal selecteren** de optie **geen geselecteerd**. Vervolgens zoekt u naar **Azure Cosmos DB** Principal en selecteert u deze (om eenvoudiger te vinden, kunt u ook zoeken op Principal-id: `a232010e-820c-4083-83bb-3ace5fc29d0b` voor elke Azure-regio, met uitzonde ring van Azure Government regio's waar de principal-id is `57506a73-e302-42a9-b869-6f12d9ec29e9` ). Kies tot slot onderaan **selecteren** . Als de principal van de **Azure Cosmos DB** zich niet in de lijst bevindt, moet u de resource provider voor **Microsoft.DocumentDB** mogelijk opnieuw registreren zoals beschreven in de sectie [de resource provider registreren](#register-resource-provider) van dit artikel.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Selecteer de Azure Cosmos DB-Principal":::

1. Selecteer **toevoegen** om het nieuwe toegangs beleid toe te voegen.

1. Selecteer **Opslaan** in het Key Vault-exemplaar om alle wijzigingen op te slaan.

## <a name="generate-a-key-in-azure-key-vault"></a>Een sleutel in Azure Key Vault genereren

1. Ga vanuit het Azure Portal naar de Azure Key Vault instantie die u wilt gebruiken voor het hosten van uw versleutelings sleutels. Selecteer vervolgens **sleutels** in het menu links:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Vermelding van sleutels in het menu links":::

1. Selecteer **genereren/importeren**, geef een naam op voor de nieuwe sleutel en selecteer een RSA-sleutel grootte. Mini maal 3072 wordt aanbevolen voor de beste beveiliging. Selecteer vervolgens **Maken**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Een nieuwe sleutel maken":::

1. Nadat de sleutel is gemaakt, selecteert u de zojuist gemaakte sleutel en vervolgens de huidige versie.

1. Kopieer de sleutel- **id** van de sleutel, behalve het deel na de laatste slash:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="De sleutel-id van de sleutel kopiëren":::

## <a name="create-a-new-azure-cosmos-account"></a>Een nieuw Azure Cosmos-account maken

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Wanneer u een nieuw Azure Cosmos DB-account maakt vanuit de Azure Portal, kiest u door de **klant beheerde sleutel** in de **versleutelings** stap. Plak in het veld **sleutel-URI** de URI/sleutel-id van de Azure Key Vault sleutel die u hebt gekopieerd uit de vorige stap:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="CMK-para meters instellen in de Azure Portal":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Azure PowerShell gebruiken

Wanneer u een nieuw Azure Cosmos DB-account maakt met Power shell:

- Geef de URI van de Azure Key Vault sleutel die u eerder hebt gekopieerd door in de eigenschap **keyVaultKeyUri** in **PropertyObject**.

- Gebruik **2019-12-12** of hoger als de API-versie.

> [!IMPORTANT]
> U moet de `locations` eigenschap expliciet instellen voor het account dat is gemaakt met door de klant beheerde sleutels.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

Nadat het account is gemaakt, kunt u controleren of door de klant beheerde sleutels zijn ingeschakeld door de URI van de Azure Key Vault sleutel op te halen:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken

Wanneer u een nieuw Azure Cosmos-account maakt met behulp van een Azure Resource Manager sjabloon:

- Geef de URI van de Azure Key Vault sleutel die u eerder hebt gekopieerd, op onder de eigenschap **keyVaultKeyUri** in het object **Properties** .

- Gebruik **2019-12-12** of hoger als de API-versie.

> [!IMPORTANT]
> U moet de `locations` eigenschap expliciet instellen voor het account dat is gemaakt met door de klant beheerde sleutels.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

Implementeer de sjabloon met het volgende Power shell-script:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> De Azure CLI gebruiken

Wanneer u een nieuw Azure Cosmos-account maakt via de Azure CLI, geeft u de URI op van de Azure Key Vault sleutel die u eerder onder de `--key-uri` para meter hebt gekopieerd.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

Nadat het account is gemaakt, kunt u controleren of door de klant beheerde sleutels zijn ingeschakeld door de URI van de Azure Key Vault sleutel op te halen:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="key-rotation"></a>Sleutelroulatie

Het draaien van de door de klant beheerde sleutel die wordt gebruikt door uw Azure Cosmos-account kan op twee manieren worden uitgevoerd.

- Maak een nieuwe versie van de sleutel die momenteel wordt gebruikt vanuit Azure Key Vault:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Een nieuwe sleutel versie maken":::

- Wissel de sleutel die momenteel wordt gebruikt door een volledig andere toe door de sleutel-URI in uw account bij te werken. Ga vanuit het Azure Portal naar uw Azure Cosmos-account en selecteer **gegevens versleuteling** in het menu links:

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="De menu vermelding voor gegevens versleuteling":::

    Vervang vervolgens de **sleutel-URI** door de nieuwe sleutel die u wilt gebruiken en selecteer **Opslaan**:

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="De sleutel-URI bijwerken":::

    Ga als volgt te werk om hetzelfde resultaat te krijgen in Power shell:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

De vorige sleutel of sleutel versie kan na 24 uur worden uitgeschakeld, of nadat de [Azure Key Vault controle logboeken](../key-vault/general/logging.md) geen activiteit meer van Azure Cosmos DB op die sleutel of sleutel versie weer geven.
    
## <a name="error-handling"></a>Foutafhandeling

Bij het gebruik van Customer-Managed Keys (CMK) in Azure Cosmos DB, als er fouten zijn opgetreden, Azure Cosmos DB retourneert de fout Details samen met een HTTP-substatuscode in het antwoord. U kunt deze substatuscode gebruiken om fouten in de hoofd oorzaak van het probleem op te sporen. Zie de [HTTP-status codes voor Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artikel om de lijst met ondersteunde HTTP-substatus codes op te halen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Zijn er extra kosten verbonden aan het inschakelen van door de klant beheerde sleutels?

Nee, er worden geen kosten in rekening gebracht om deze functie in te scha kelen.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Hoe kunnen door de klant beheerde sleutels de capaciteits planning beïnvloeden?

Wanneer door de klant beheerde sleutels worden gebruikt, zien de door uw database bewerkingen verbruikte [aanvraag eenheden](./request-units.md) toe aan de extra verwerking die is vereist voor het uitvoeren van versleuteling en ontsleuteling van uw gegevens. Dit kan leiden tot iets hoger gebruik van uw ingerichte capaciteit. Gebruik de onderstaande tabel voor hulp:

| Het type bewerking | Toename van aanvraag eenheid |
|---|---|
| Punt-lezen (items ophalen met hun ID) | + 5% per bewerking |
| Een schrijf bewerking | + 6% per bewerking<br/>ong. + 0,06 RU per geïndexeerde eigenschap |
| Query's, invoer van wijzigingen lezen of een conflict feed | + 15% per bewerking |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Welke gegevens worden versleuteld met de door de klant beheerde sleutels?

Alle gegevens die zijn opgeslagen in uw Azure Cosmos-account, worden versleuteld met de door de klant beheerde sleutels, met uitzonde ring van de volgende meta gegevens:

- De namen van uw Azure Cosmos DB [accounts, data bases en containers](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account)

- De namen van uw [opgeslagen procedures](./stored-procedures-triggers-udfs.md)

- De eigenschaps paden die zijn gedeclareerd in uw [indexerings beleid](./index-policy.md)

- De waarden van de [partitie sleutels](./partitioning-overview.md) van de containers

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Worden door de klant beheerde sleutels ondersteund voor bestaande Azure Cosmos-accounts?

Deze functie is momenteel alleen beschikbaar voor nieuwe accounts.

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>Is het mogelijk om door de klant beheerde sleutels te gebruiken in combi natie met de Azure Cosmos DB [Analytical Store](analytical-store-introduction.md)?

Ja, maar hiervoor is nog meer configuratie vereist voor uw account. Neem contact op met [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) voor meer informatie.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Is er een plan om de nauw keurigheid van sleutels op account niveau te ondersteunen?

Momenteel worden er geen sleutels op container niveau overwogen.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Hoe kan ik zien of door de klant beheerde sleutels zijn ingeschakeld op mijn Azure Cosmos-account?

Ga vanuit het Azure Portal naar uw Azure Cosmos-account en Bekijk de **gegevens versleutelings** vermelding in het menu links. Als dit item bestaat, worden door de klant beheerde sleutels ingeschakeld voor uw account:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="De menu vermelding voor gegevens versleuteling":::

U kunt de gegevens van uw Azure Cosmos-account ook programmatisch ophalen en zoeken naar de aanwezigheid van de `keyVaultKeyUri` eigenschap. Zie hierboven voor manieren om dit te doen [in Power shell](#using-powershell) en [de Azure CLI te gebruiken](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Wat is de invloed van door de klant beheerde sleutels op een back-up?

Azure Cosmos DB maakt [regel matig en automatisch een back-up](./online-backup-and-restore.md) van de gegevens die in uw account zijn opgeslagen. Met deze bewerking wordt een back-up gemaakt van de versleutelde gegevens. Als u de herstelde back-up wilt gebruiken, is de versleutelings sleutel die u hebt gebruikt op het moment van de back-up, vereist. Dit betekent dat er geen intrekken is gemaakt en dat de versie van de sleutel die werd gebruikt op het moment van de back-up nog steeds is ingeschakeld.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hoe kan ik een versleutelings sleutel intrekken?

Het intrekken van de sleutel wordt uitgevoerd door de nieuwste versie van de sleutel uit te scha kelen:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="De versie van een sleutel uitschakelen":::

Als u alle sleutels wilt intrekken van een Azure Key Vault-exemplaar, kunt u ook het toegangs beleid verwijderen dat is verleend aan de Azure Cosmos DB Principal:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Het toegangs beleid voor de Azure Cosmos DB-Principal verwijderen":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Welke bewerkingen zijn beschikbaar nadat een door de klant beheerde sleutel is ingetrokken?

De enige bewerking die mogelijk is wanneer de versleutelings sleutel is ingetrokken, is account verwijdering.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gegevens versleuteling in azure Cosmos DB](./database-encryption-at-rest.md).
- Bekijk een overzicht van [beveiligde toegang tot gegevens in Cosmos DB](secure-access-to-data.md).