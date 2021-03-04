---
title: Objectreplicatie configureren
titleSuffix: Azure Storage
description: Meer informatie over het configureren van object replicatie voor het asynchroon kopiëren van blok-blobs van een container in het ene opslag account naar het andere.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2b6855d72b644a3fe1fa46c883eb7414383a1a57
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031698"
---
# <a name="configure-object-replication-for-block-blobs"></a>Object replicatie configureren voor blok-blobs

Met object replicatie worden blok-blobs tussen een bron opslag account en een doel account asynchroon gekopieerd. Zie [object replicatie](object-replication-overview.md)voor meer informatie over object replicatie.

Wanneer u object replicatie configureert, maakt u een replicatie beleid dat het bron opslag account en het doel account opgeeft. Een replicatie beleid bevat een of meer regels die een bron container en een doel container opgeven en aangeven welke blok-blobs in de bron container worden gerepliceerd.

In dit artikel wordt beschreven hoe u object replicatie configureert voor uw opslag account met behulp van de Azure Portal, Power shell of Azure CLI. U kunt ook een van de Azure Storage Resource provider-client bibliotheken gebruiken om object replicatie te configureren.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>Een replicatie beleid en-regels maken

Voordat u object replicatie configureert, moet u de bron-en doel opslag accounts maken als deze nog niet bestaan. Beide accounts moeten voor algemeen gebruik v2-opslag accounts zijn. Zie [een Azure Storage-account maken](../common/storage-account-create.md)voor meer informatie.

Voor object replicatie moet BLOB-versie beheer zijn ingeschakeld voor het bron-en doel account en dat de feed voor BLOB-wijzigingen is ingeschakeld voor het bron account. Zie [BLOB-versie beheer](versioning-overview.md)voor meer informatie over blob-versie beheer. Zie [ondersteuning voor feeds wijzigen in Azure Blob Storage](storage-blob-change-feed.md)voor meer informatie over wijzigings invoer. Houd er wel bij dat het inschakelen van deze functies kan leiden tot extra kosten.

Een opslag account kan fungeren als het bron account voor Maxi maal twee doel accounts. De bron-en doel account kunnen zich in dezelfde regio of in verschillende regio's bevinden. Ze kunnen zich ook in verschillende abonnementen en in verschillende Azure Active Directory-tenants (Azure AD) bevinden. Er kan slechts één replicatie beleid worden gemaakt voor elk paar accounts.

Wanneer u object replicatie configureert, maakt u een replicatie beleid voor het doel account via de resource provider van Azure Storage. Nadat het replicatie beleid is gemaakt, wordt door Azure Storage een beleids-ID toegewezen. Vervolgens moet u het replicatie beleid koppelen aan het bron account door de beleids-ID te gebruiken. De beleids-ID voor de bron-en doel accounts moet hetzelfde zijn om de replicatie te kunnen uitvoeren.

Als u een beleid voor object replicatie wilt configureren voor een opslag account, moet u de rol Azure Resource Manager **Inzender** toewijzen, het bereik instellen op het niveau van het opslag account of hoger. Zie voor meer informatie [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md) in de Azure-documentatie voor op rollen gebaseerde toegangs beheer (Azure RBAC).

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>Object replicatie configureren wanneer u toegang hebt tot beide opslag accounts

Als u toegang hebt tot de bron-en doel opslag accounts, kunt u het object replicatie beleid voor beide accounts configureren.

Voordat u object replicatie configureert in de Azure Portal, maakt u de bron-en doel containers in hun respectieve opslag accounts, als deze nog niet bestaan. Schakel ook BLOB-versie beheer in en wijzig de feed voor het bron account en schakel BLOB-versie beheer in op het doel account.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

De Azure Portal maakt automatisch het beleid voor het bron account nadat u dit hebt geconfigureerd voor het doel account.

Voer de volgende stappen uit om een replicatie beleid te maken in de Azure Portal:

1. Navigeer naar het bron opslag account in de Azure Portal.
1. Selecteer onder **BLOB service** **object replicatie**.
1. Selecteer **replicatie regels instellen**.
1. Selecteer het doel abonnement en het opslag account.
1. Selecteer in de sectie **container paren** een bron container van het bron account en een doel container van het doel account. U kunt Maxi maal 10 container paren per replicatie beleid maken.

    De volgende afbeelding toont een set replicatie regels.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Scherm opname van de replicatie regels in Azure Portal":::

1. Indien gewenst, geeft u een of meer filters op om alleen blobs te kopiëren die overeenkomen met het patroon van het voor voegsel. Als u bijvoorbeeld een voor voegsel opgeeft `b` , worden alleen blobs waarvan de naam begint met die letter, gerepliceerd. U kunt een virtuele map opgeven als onderdeel van het voor voegsel. De voorvoegsel teken reeks biedt geen ondersteuning voor joker tekens.

    De volgende afbeelding toont filters die bepalen welke blobs worden gekopieerd als onderdeel van een replicatie regel.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Scherm afbeelding met filters voor een replicatie regel":::

1. Standaard wordt het Kopieer bereik zo ingesteld dat alleen nieuwe objecten worden gekopieerd. Als u alle objecten in de container wilt kopiëren of objecten wilt kopiëren vanaf een aangepaste datum en tijd, selecteert u de koppeling **wijzigen** en configureert u het Kopieer bereik voor het container paar.

    In de volgende afbeelding ziet u een aangepast Kopieer bereik waarmee objecten worden gekopieerd vanaf een opgegeven datum en tijd.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Scherm opname van het aangepaste Kopieer bereik voor object replicatie":::

1. Selecteer **opslaan en Toep assen** om het replicatie beleid te maken en te beginnen met het repliceren van gegevens.

Nadat u object replicatie hebt geconfigureerd, worden in de Azure Portal het replicatie beleid en de regels weer gegeven, zoals u kunt zien in de volgende afbeelding.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Scherm opname van object replicatie beleid in Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een replicatie beleid met Power shell wilt maken, moet u eerst versie [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) of hoger van de AZ. Storage Power shell-module installeren. Zie [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps)(Engelstalig) voor meer informatie over het installeren van Azure PowerShell.

In het volgende voor beeld ziet u hoe u een replicatie beleid maakt voor de bron-en doel accounts. Vergeet niet om waarden tussen punt haken te vervangen door uw eigen waarden:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een replicatie beleid wilt maken met Azure CLI, installeert u eerst Azure CLI versie 2.11.1 of hoger. Zie [aan de slag met Azure cli](/cli/azure/get-started-with-azure-cli)voor meer informatie.

Schakel vervolgens BLOB-versie beheer in op de bron-en doel opslag accounts en schakel feed voor wijziging in het bron account in door de opdracht [AZ Storage account BLOB-Service-Properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) aan te roepen. Vergeet niet om waarden tussen punt haken te vervangen door uw eigen waarden:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Maak de bron-en doel containers in hun respectieve opslag accounts.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

Maak een nieuw replicatie beleid en een bijbehorende regel voor het doel account door het aanroepen [AZ Storage account of-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create).

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

Azure Storage stelt de beleids-ID voor het nieuwe beleid in wanneer dit wordt gemaakt. Als u aanvullende regels aan het beleid wilt toevoegen, roept u het [AZ Storage-account of de regel Policy toe](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) en geeft u de beleids-id op.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

Maak vervolgens het beleid op het bron account met behulp van de beleids-ID.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>Object replicatie configureren als u alleen toegang hebt tot het doel account

Als u geen machtigingen hebt voor het bron-opslag account, kunt u object replicatie configureren op het doel account en een JSON-bestand met de beleids definitie aan een andere gebruiker geven om hetzelfde beleid te maken voor het bron account. Bijvoorbeeld, als het bron account zich in een andere Azure AD-Tenant van het doel account bevindt, kunt u deze methode gebruiken om object replicatie te configureren.

Houd er rekening mee dat u de rol Azure Resource Manager **Inzender** moet zijn toegewezen aan het niveau van het doel-opslag account of hoger om het beleid te kunnen maken. Zie voor meer informatie [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md) in de Azure-documentatie voor op rollen gebaseerde toegangs beheer (Azure RBAC).

De volgende tabel bevat een overzicht van de waarden die moeten worden gebruikt voor de beleids-ID en regel-Id's in het JSON-bestand in elk scenario.

| Wanneer u het JSON-bestand voor dit account maakt... | De beleids-ID instellen op deze waarde | Regel-Id's instellen op deze waarde |
|-|-|-|
| Doel account | De *standaard* waarde voor de teken reeks. Azure Storage wordt de waarde voor de beleids-ID voor u gemaakt. | Een lege teken reeks. Azure Storage worden de regel-ID-waarden voor u gemaakt. |
| Bron account | De waarde van de beleids-ID die wordt geretourneerd wanneer u het beleid dat is gedefinieerd op het doel account downloadt als een JSON-bestand. | De waarden van de regel-Id's die worden geretourneerd bij het downloaden van het beleid dat is gedefinieerd op het doel account als een JSON-bestand. |

In het volgende voor beeld wordt een replicatie beleid gedefinieerd voor het doel account met één regel die overeenkomt met het voor voegsel *b* en wordt de minimale aanmaak tijd ingesteld voor blobs die moeten worden gerepliceerd. Vergeet niet om waarden tussen punt haken te vervangen door uw eigen waarden:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om de object replicatie te configureren voor het doel account met een JSON-bestand in de Azure Portal:

1. Maak een lokaal JSON-bestand dat het replicatie beleid voor het doel account definieert. Stel het veld **policyId** in op *standaard* zodat Azure Storage de beleids-id definieert.

    Een eenvoudige manier om een JSON-bestand te maken dat een replicatie beleid definieert, is om eerst een test replicatie beleid te maken tussen twee opslag accounts in de Azure Portal. U kunt vervolgens de replicatie regels downloaden en het JSON-bestand zo nodig wijzigen.

1. Navigeer naar de **object replicatie** -instellingen voor het doel account in de Azure Portal.
1. Selecteer **replicatie regels uploaden**.
1. Upload het JSON-bestand. In het Azure Portal worden het beleid en de regels weer gegeven die worden gemaakt, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Scherm afbeelding die laat zien hoe u een JSON-bestand kunt uploaden om een replicatie beleid te definiëren":::

1. Selecteer **uploaden** om het replicatie beleid voor het doel account te maken.

U kunt vervolgens een JSON-bestand met de beleids definitie downloaden dat u aan een andere gebruiker kunt aanbieden om het bron account te configureren. Voer de volgende stappen uit om dit JSON-bestand te downloaden:

1. Navigeer naar de **object replicatie** -instellingen voor het doel account in de Azure Portal.
1. Selecteer de knop **meer** naast het beleid dat u wilt downloaden en selecteer vervolgens **regels downloaden**, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Scherm afbeelding die laat zien hoe replicatie regels worden gedownload naar een JSON-bestand":::

1. Sla het JSON-bestand op uw lokale computer op om te delen met een andere gebruiker om het beleid op het bron account te configureren.

Het gedownloade JSON-bestand bevat de beleids-ID die Azure Storage gemaakt voor het beleid op het doel account. U moet dezelfde beleids-ID gebruiken om de object replicatie op het bron account te configureren.

Houd er rekening mee dat het uploaden van een JSON-bestand voor het maken van een replicatie beleid voor het doel account via de Azure Portal niet automatisch hetzelfde beleid maakt in het bron account. Een andere gebruiker moet het beleid maken op het bron account voordat Azure Storage begint met het repliceren van objecten.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een JSON-bestand met de replicatie beleids definitie voor het doel account uit Power shell wilt downloaden, roept u de opdracht [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) aan om het beleid te retour neren. Converteer het beleid vervolgens naar JSON en sla dit op als een lokaal bestand, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om waarden tussen punt haken en het bestandspad te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Als u het JSON-bestand wilt gebruiken voor het definiëren van het replicatie beleid voor het bron account met Power shell, haalt u het lokale bestand op en converteert u deze van JSON naar een-object. Roep vervolgens de opdracht [set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) aan om het beleid op het bron account te configureren, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om waarden tussen punt haken en het bestandspad te vervangen door uw eigen waarden:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de replicatie beleids definitie voor het doel account wilt schrijven naar een JSON-bestand vanuit Azure CLI, roept u de opdracht [AZ Storage account of-Policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) aan en voert u uit naar een bestand.

In het volgende voor beeld wordt de beleids definitie geschreven naar een JSON-bestand met de naam *policy.jsop*. Vergeet niet om waarden tussen punt haken en het bestandspad te vervangen door uw eigen waarden:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Als u het JSON-bestand wilt gebruiken voor het configureren van het replicatie beleid voor het bron account met Azure CLI, roept u de opdracht [AZ Storage account of-Policy Create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) aan en verwijst u naar de *policy.jsin* het bestand. Vergeet niet om waarden tussen punt haken en het bestandspad te vervangen door uw eigen waarden:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>De replicatie status van een BLOB controleren

U kunt de replicatie status voor een BLOB in het bron account controleren met behulp van de Azure Portal, Power shell of Azure CLI. Eigenschappen van object replicatie worden pas ingevuld nadat de replicatie is voltooid of mislukt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om de replicatie status voor een BLOB in het bron account in de Azure Portal te controleren:

1. Navigeer naar het bron account in de Azure Portal.
1. Zoek de container die de bron-BLOB bevat.
1. Selecteer de blob om de eigenschappen ervan weer te geven. Als de blob is gerepliceerd, ziet u in de sectie **object replicatie** dat de status is ingesteld op *voltooid*. De ID van het replicatie beleid en de ID voor de regel voor object replicatie voor deze container worden ook weer gegeven.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="Scherm opname van replicatie status voor een BLOB in het bron account":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u de replicatie status voor een BLOB in het bron account met Power shell wilt controleren, haalt u de waarde van de eigenschap **ReplicationStatus** van de object replicatie op, zoals wordt weer gegeven in het volgende voor beeld. Vergeet niet om waarden tussen punt haken te vervangen door uw eigen waarden:

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de replicatie status voor een BLOB in het bron account met Azure CLI wilt controleren, haalt u de waarde van de eigenschap object replicatie **status** op, zoals wordt weer gegeven in het volgende voor beeld:

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

Als de replicatie status voor een BLOB in het bron account mislukt, wordt de volgende mogelijke oorzaken onderzocht:

- Zorg ervoor dat het object replicatie beleid is geconfigureerd op het doel account.
- Controleer of de doel container nog bestaat.
- Als de bron-blob is versleuteld met een door de klant verschafte sleutel als onderdeel van een schrijf bewerking, mislukt de object replicatie. Zie [een versleutelings sleutel voor een aanvraag voor Blob-opslag bieden](encryption-customer-provided-keys.md)voor meer informatie over door de klant geleverde sleutels.

## <a name="remove-a-replication-policy"></a>Een replicatie beleid verwijderen

Als u een replicatie beleid en de bijbehorende regels wilt verwijderen, gebruikt u Azure Portal, Power shell of CLI.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om een replicatie beleid te verwijderen in de Azure Portal:

1. Navigeer naar het bron opslag account in de Azure Portal.
1. Onder **instellingen** selecteert u **object replicatie**.
1. Klik op de knop **meer** naast de naam van het beleid.
1. Selecteer **regels verwijderen**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u een replicatie beleid wilt verwijderen, verwijdert u het beleid van zowel het bron account als het doel account. Als u het beleid verwijdert, worden ook alle bijbehorende regels verwijderd.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een replicatie beleid wilt verwijderen, verwijdert u het beleid van zowel het bron account als het doel account. Als u het beleid verwijdert, worden ook alle bijbehorende regels verwijderd.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van object replicatie](object-replication-overview.md)
- [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)
- [Wijzigings feed in Azure Blob-opslag verwerken](storage-blob-change-feed-how-to.md)
