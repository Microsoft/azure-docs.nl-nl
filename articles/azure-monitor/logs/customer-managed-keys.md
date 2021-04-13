---
title: Door de klant beheerde sleutel van Azure Monitor
description: Informatie en stappen voor het configureren van door de klant beheerde sleutel voor het versleutelen van gegevens in uw Log Analytics-werkruimten met behulp van Azure Key Vault sleutel.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 4033421095ead47e2bd1e97c4f2f42672644d7df
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364852"
---
# <a name="azure-monitor-customer-managed-key"></a>Door de klant beheerde sleutel van Azure Monitor 

Gegevens in Azure Monitor worden versleuteld met door Microsoft beheerde sleutels. U kunt uw eigen versleutelingssleutel gebruiken om de gegevens en opgeslagen query's in uw werkruimten te beveiligen. Wanneer u een door de klant beheerde sleutel opgeeft, wordt die sleutel gebruikt om de toegang tot uw gegevens te beveiligen en te beheren. Zodra de sleutel is geconfigureerd, worden alle gegevens die naar uw werkruimten worden verzonden, versleuteld met uw Azure Key Vault sleutel. Door de klant beheerde sleutels bieden meer flexibiliteit om toegangsbeheer te beheren.

U wordt aangeraden beperkingen [en beperkingen hieronder te bekijken vóór](#limitationsandconstraints) de configuratie.

## <a name="customer-managed-key-overview"></a>Overzicht van door de klant beheerde sleutel

[Versleuteling-at-rest](../../security/fundamentals/encryption-atrest.md) is een algemene privacy- en beveiligingsvereiste in organisaties. U kunt versleuteling in rust volledig laten beheren in Azure, terwijl u verschillende opties hebt om versleutelings- en versleutelingssleutels nauwkeurig te beheren.

Azure Monitor zorgt ervoor dat alle gegevens en opgeslagen query's in rust worden versleuteld met behulp van door Microsoft beheerde sleutels (MMK). Azure Monitor biedt ook een optie voor versleuteling met behulp van uw eigen sleutel die is opgeslagen in uw [Azure Key Vault,](../../key-vault/general/overview.md)waarmee u de toegang tot uw gegevens op elk moment kunt intrekken. Azure Monitor gebruik van versleuteling is identiek aan de manier [waarop Azure Storage versleuteling](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) werkt.

Door de klant beheerde sleutel wordt geleverd op [toegewezen clusters die](./logs-dedicated-clusters.md) een hoger beveiligingsniveau en beheer bieden. Gegevens die worden opgenomen in toegewezen clusters, worden twee keer versleuteld: eenmaal op serviceniveau met behulp van door Microsoft beheerde sleutels of door de klant beheerde sleutels en één keer op infrastructuurniveau met behulp van twee verschillende versleutelingsalgoritmen en twee verschillende sleutels. [Dubbele versleuteling](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) beschermt tegen een scenario waarin een van de versleutelingsalgoritmen of sleutels kan worden aangetast. In dit geval blijft de extra versleutelingslaag uw gegevens beschermen. Met toegewezen clusters kunt u uw gegevens ook beveiligen met [lockbox-besturingselementen.](#customer-lockbox-preview)

Gegevens die in de afgelopen 14 dagen zijn opgenomen, worden ook bewaard in hot-cache (ssd-backed) voor een efficiënte werking van de query-engine. Deze gegevens blijven versleuteld met Microsoft-sleutels, ongeacht de configuratie van de door de klant beheerde sleutel, maar uw controle over SSD-gegevens voldoet aan de sleutel [intrekken.](#key-revocation) We werken aan het versleutelen van SSD-gegevens met door de klant beheerde sleutel in de eerste helft van 2021.

Toegewezen Log Analytics-clusters maken gebruik van een prijsmodel voor [capaciteitsreserveringen](./logs-dedicated-clusters.md#cluster-pricing-model) vanaf 1000 GB per dag.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Hoe door de klant beheerde sleutel werkt in Azure Monitor

Azure Monitor maakt gebruik van beheerde identiteit om toegang te verlenen tot uw Azure Key Vault. De identiteit van het Log Analytics-cluster wordt ondersteund op clusterniveau. Om door de klant beheerde sleutelbeveiliging toe te staan voor meerdere werkruimten, wordt een nieuwe Log *Analytics-clusterresource* uitgevoerd als een tussenliggende identiteitsverbinding tussen uw Key Vault en uw Log Analytics-werkruimten. De opslag van het cluster maakt gebruik van de beheerde identiteit die is gekoppeld aan de clusterresource voor verificatie bij uw \' Azure Key Vault via Azure Active Directory.  

Na de configuratie van de door de klant beheerde sleutel worden nieuwe opgenomen gegevens in werkruimten die zijn gekoppeld aan uw toegewezen cluster versleuteld met uw sleutel. U kunt werkruimten op elk moment loskoppelen van het cluster. Nieuwe gegevens worden vervolgens opgenomen in Log Analytics-opslag en versleuteld met Microsoft-sleutel, terwijl u naadloos query's kunt uitvoeren op uw nieuwe en oude gegevens.

> [!IMPORTANT]
> De mogelijkheid van door de klant beheerde sleutels is regionaal. Uw Azure Key Vault, het cluster en de gekoppelde Log Analytics-werkruimten moeten zich in dezelfde regio, maar ze kunnen zich in verschillende abonnementen.

![Overzicht van door de klant beheerde sleutels](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log *Analytics-clusterresource* met beheerde identiteit met machtigingen voor Key Vault: de identiteit wordt doorgegeven aan de toegewezen Log Analytics-clusteropslag aan de onderlaag
3. Toegewezen Log Analytics-cluster
4. Werkruimten die zijn gekoppeld aan *clusterresource* 

### <a name="encryption-keys-operation"></a>Bewerking versleutelingssleutels

Er zijn drie soorten sleutels betrokken bij opslaggegevensversleuteling:

- **KEK:** sleutelversleutelingssleutel (uw door de klant beheerde sleutel)
- **AEK** : accountversleutelingssleutel
- **DEK** - Gegevensversleutelingssleutel

De volgende regels zijn van toepassing:

- De Log Analytics-clusteropslagaccounts genereren een unieke versleutelingssleutel voor elk opslagaccount, ook wel de AEK genoemd.
- De AEK wordt gebruikt om DEK's af te leiden. Dit zijn de sleutels die worden gebruikt voor het versleutelen van elk gegevensblok dat naar de schijf wordt geschreven.
- Wanneer u uw sleutel in Key Vault configureert en er in het cluster naar verwijst, verzendt Azure Storage aanvragen naar uw Azure Key Vault om de AEK te verpakken en uit te pakken om bewerkingen voor gegevensversleuteling en -ontsleuteling uit te voeren.
- Uw KEK verlaat nooit uw Key Vault.
- Azure Storage gebruikt de beheerde identiteit die is gekoppeld aan de *clusterresource* voor verificatie en toegang tot Azure Key Vault via Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Customer-Managed voor het inrichten van sleutels

1. Een Azure Key Vault maken en sleutel opslaan
1. Cluster maken
1. Machtigingen verlenen aan uw Key Vault
1. Cluster bijwerken met sleutel-id-details
1. Log Analytics-werkruimten koppelen

Configuratie van door de klant beheerde sleutels wordt momenteel niet ondersteund in Azure Portal en inrichting kan worden uitgevoerd via [PowerShell-,](/powershell/module/az.operationalinsights/) [CLI-](/cli/azure/monitor/log-analytics) of [REST-aanvragen.](/rest/api/loganalytics/)

### <a name="asynchronous-operations-and-status-check"></a>Asynchrone bewerkingen en statuscontrole

Sommige configuratiestappen worden asynchroon uitgevoerd omdat ze niet snel kunnen worden voltooid. Het antwoord kan een van de volgende `status` zijn: 'InProgress', 'Updating', 'Deleting', 'Succeeded or 'Failed' met foutcode.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N.v.t.

# <a name="rest"></a>[REST](#tab/rest)

Wanneer u REST gebruikt, retourneert het antwoord in eerste instantie een HTTP-statuscode 202 (Geaccepteerd) en een header met de *eigenschap Azure-AsyncOperation:*
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

U kunt de status van de asynchrone bewerking controleren door een GET-aanvraag te verzenden naar het eindpunt in *de Azure-AsyncOperation-header:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Versleutelingssleutel (KEK) opslaan

Maak of gebruik een Azure Key Vault die u al moet genereren, of importeer een sleutel die moet worden gebruikt voor gegevensversleuteling. De Azure Key Vault moeten worden geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw gegevens in de Azure Monitor. U kunt deze configuratie controleren onder eigenschappen in  uw  Key Vault. Zowel de beveiliging voor het verwijderen van de software als de beveiliging tegen opseen is ingeschakeld.

![Beveiligingsinstellingen voor soft delete en purge](media/customer-managed-keys/soft-purge-protection.png)

Deze instellingen kunnen worden bijgewerkt in Key Vault cli en PowerShell:

- [Voorlopig verwijderen](../../key-vault/general/soft-delete-overview.md)
- [Beveiliging tegen opsluizen](../../key-vault/general/soft-delete-overview.md#purge-protection) beschermt tegen gedwongen verwijdering van het geheim/de kluis, zelfs na een zachte verwijdering

## <a name="create-cluster"></a>Cluster maken

Clusters ondersteunen twee [beheerde identiteitstypen:](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)Door het systeem toegewezen en door de gebruiker toegewezen, terwijl één identiteit kan worden gedefinieerd in een cluster, afhankelijk van uw scenario. 
- Door het systeem toegewezen beheerde identiteit is eenvoudiger en wordt automatisch gegenereerd met het maken van het cluster wanneer identiteit `type` is ingesteld op ' Systeem *toegewezen'.* Deze identiteit kan later worden gebruikt om opslagtoegang te verlenen tot uw Key Vault voor inpak- en uitpakbewerkingen. 
  
  Identiteitsinstellingen in cluster voor door het systeem toegewezen beheerde identiteit
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Als u een door de klant beheerde sleutel wilt configureren tijdens het maken van het cluster, moet u vooraf een sleutel en een door de gebruiker toegewezen identiteit in uw Key Vault hebben verleend. Maak vervolgens het cluster met de volgende instellingen: identiteit als Gebruiker toegewezen, met de `type`  `UserAssignedIdentities` *resource-id* van uw identiteit.

  Identiteitsinstellingen in cluster voor door de gebruiker toegewezen beheerde identiteit
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> U kunt de door de gebruiker toegewezen beheerde identiteit niet gebruiken als uw Key Vault zich in Private-Link (vNet). In dit scenario kunt u een door het systeem toegewezen beheerde identiteit gebruiken.

Volg de procedure die wordt geïllustreerd in [het artikel Toegewezen clusters.](./logs-dedicated-clusters.md#creating-a-cluster) 

## <a name="grant-key-vault-permissions"></a>Machtigingen Key Vault verlenen

Maak toegangsbeleid in Key Vault om machtigingen te verlenen aan uw cluster. Deze machtigingen worden gebruikt door de onderlaag Azure Monitor opslag. Open uw Key Vault in Azure Portal klik vervolgens op Toegangsbeleid *en* *vervolgens op +Toegangsbeleid toevoegen* om een beleid te maken met de volgende instellingen:

- Sleutelmachtigingen: *selecteer 'Get'*, *'Wrap Key'* en *'Unwrap Key'*.
- Principal selecteren: afhankelijk van het identiteitstype dat wordt gebruikt in het cluster (door het systeem of de gebruiker toegewezen beheerde identiteit) voert u de clusternaam of de principal-id van het cluster in voor de door het systeem toegewezen beheerde identiteit of de door de gebruiker toegewezen beheerde identiteitsnaam.

![machtigingen Key Vault verlenen](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

De *machtiging Get* is vereist om te controleren of uw Key Vault is geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw Azure Monitor beschermen.

## <a name="update-cluster-with-key-identifier-details"></a>Cluster bijwerken met sleutel-id-details

Voor alle bewerkingen in het cluster is de `Microsoft.OperationalInsights/clusters/write` actiemachtiging vereist. Deze machtiging kan worden verleend via de eigenaar of inzender die de actie bevat of via de `*/write` rol Log Analytics-inzender die de actie `Microsoft.OperationalInsights/*` bevat.

Met deze stap wordt Azure Monitor Storage bijgewerkt met de sleutel en versie die moeten worden gebruikt voor gegevensversleuteling. Wanneer deze wordt bijgewerkt, wordt uw nieuwe sleutel gebruikt om de Opslagsleutel (AEK) in te pakken en uit te pakken.

Selecteer de huidige versie van uw sleutel in Azure Key Vault om de details van de sleutel-id op te halen.

![Machtigingen Key Vault verlenen](media/customer-managed-keys/key-identifier-8bit.png)

KeyVaultProperties in het cluster bijwerken met details van de sleutel-id.

>[!NOTE]
>Sleutelrotatie ondersteunt twee modi: automatische rotatie of expliciete sleutelversie-update. Zie Sleutelrotatie [om](#key-rotation) de beste aanpak voor u te bepalen.

De bewerking is asynchroon en kan even duren.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Response**

Het duurt enkele minuten voordat de sleutel is doorgegeven. U kunt de status van de update op twee manieren controleren:
1. Kopieer de waarde Azure-AsyncOperation URL uit het antwoord en volg de statuscontrole voor [asynchrone bewerkingen.](#asynchronous-operations-and-status-check)
2. Verzend een GET-aanvraag op het cluster en bekijk de *eigenschappen van KeyVaultProperties.* Uw onlangs bijgewerkte sleutel moet in het antwoord worden retourneren.

Een antwoord op de GET-aanvraag moet er als volgende uitzien wanneer de sleutelupdate is voltooid: 202 (geaccepteerd) en header
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>Werkruimte koppelen aan cluster

> [!IMPORTANT]
> Deze stap moet alleen worden uitgevoerd nadat de Log Analytics-cluster is ingericht. Als u werkruimten koppelt en gegevens op neemt vóór de inrichting, worden opgenomen gegevens uit de werkruimten gedropt en kunnen ze niet worden hersteld.

U moet schrijfmachtigingen hebben voor zowel uw werkruimte als het cluster om deze bewerking uit te voeren, waaronder `Microsoft.OperationalInsights/workspaces/write` en `Microsoft.OperationalInsights/clusters/write` .

Volg de procedure die wordt geïllustreerd in [het artikel Toegewezen clusters.](./logs-dedicated-clusters.md#link-a-workspace-to-cluster)

## <a name="key-revocation"></a>Sleutel intrekken

> [!IMPORTANT]
> - De aanbevolen manier om de toegang tot uw gegevens in te trekken, is door uw sleutel uit te Key Vault.
> - Als u de van het cluster instelt op 'Geen', wordt ook de toegang tot uw gegevens ingetrokken, maar deze methode wordt niet aanbevolen omdat u de intrekking niet kunt terugdraaien wanneer u de in het cluster opnieuw instemt zonder een `identity` `type` ondersteuningsaanvraag te `identity` openen.

De clusteropslag respecteert wijzigingen in sleutelmachtigingen altijd binnen een uur of eerder en opslag is dan niet meer beschikbaar. Nieuwe gegevens die worden opgenomen in werkruimten die zijn gekoppeld aan uw cluster, worden uit het cluster weggetrokken en kunnen niet worden hersteld, gegevens worden ontoegankelijk en query's op deze werkruimten mislukken. Eerder opgenomen gegevens blijven in de opslag zolang uw cluster en uw werkruimten niet worden verwijderd. Niet-toegankelijke gegevens vallen onder het beleid voor gegevensretentie en worden verwijderd wanneer de retentie is bereikt. Opgenomen gegevens in de afgelopen 14 dagen worden ook bewaard in hot-cache (SSD-backed) voor een efficiënte werking van de query-engine. Dit wordt verwijderd bij het intrekken van de sleutel en wordt niet meer toegankelijk.

De opslag van het cluster controleert periodiek uw Key Vault om te proberen de versleutelingssleutel uit te pakken. Zodra deze is gebruikt, worden de gegevensops nemen en query's binnen 30 minuten hervat.

## <a name="key-rotation"></a>Sleutelroulatie

Sleutelrotatie heeft twee modi: 
- Automatisch rouleren: wanneer u uw cluster bijwerkt met maar de eigenschap weglaat, of als u deze in stelt op , gebruikt opslag automatisch de nieuwste ```"keyVaultProperties"``` ```"keyVersion"``` ```""``` versies.
- Expliciete update van sleutelversie: wanneer u uw cluster bij te werken en de sleutelversie op te geven in de eigenschap , is voor nieuwe sleutelversies een expliciete update in het cluster vereist. Zie Cluster bijwerken met ```"keyVersion"``` sleutel-id-details. ```"keyVaultProperties"``` [](#update-cluster-with-key-identifier-details) Als u een nieuwe sleutelversie genereert in Key Vault maar deze niet bij te werken in het cluster, blijft de Log Analytics-clusteropslag uw vorige sleutel gebruiken. Als u uw oude sleutel uit- of verwijdert voordat u de nieuwe sleutel in het cluster bij te werken, krijgt u de status Van sleutel [intrekken.](#key-revocation)

Al uw gegevens blijven toegankelijk na de sleutelrotatiebewerking, omdat gegevens altijd zijn versleuteld met AEK (Account Encryption Key), terwijl AEK nu wordt versleuteld met uw nieuwe KEK-versie (Key Encryption Key) in Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Door de klant beheerde sleutel voor opgeslagen query's

De querytaal die in Log Analytics wordt gebruikt, is expressief en kan gevoelige informatie bevatten in opmerkingen die u toevoegt aan query's of in de querysyntaxis. Sommige organisaties vereisen dat dergelijke informatie wordt beveiligd onder door de klant beheerd sleutelbeleid en u moet uw query's versleuteld opslaan met uw sleutel. Azure Monitor kunt u opgeslagen  zoekopdrachten en query's voor *logboekwaarschuwingen* versleuteld met uw sleutel opslaan in uw eigen opslagaccount wanneer u verbinding hebt met uw werkruimte. 

> [!NOTE]
> Log Analytics-query's kunnen worden opgeslagen in verschillende winkels, afhankelijk van het gebruikte scenario. Query's blijven versleuteld met Microsoft Key (MMK) in de volgende scenario's, ongeacht de configuratie van de door de klant beheerde sleutel: Werkmappen in Azure Monitor, Azure-dashboards, Azure Logic App, Azure Notebooks en Automation-runbooks.

Wanneer u Bring Your Own Storage (BYOS) gebruikt en  deze aan uw  werkruimte koppelt, uploadt de service opgeslagen zoekopdrachten en query's voor logboekwaarschuwingen naar uw opslagaccount. Dit betekent dat u het opslagaccount en het [versleuteling-at-rest-beleid](../../storage/common/customer-managed-keys-overview.md) bestuurt met dezelfde sleutel die u gebruikt voor het versleutelen van gegevens in een Log Analytics-cluster of een andere sleutel. U bent echter verantwoordelijk voor de kosten van het betreffende opslagaccount. 

**Overwegingen voor het instellen van door de klant beheerde sleutel voor query's**
* U moet schrijfmachtigingen hebben voor zowel uw werkruimte als het opslagaccount
* Zorg ervoor dat u uw opslagaccount maakt in dezelfde regio als uw Log Analytics-werkruimte
* De *zoekopdrachten opgeslagen* in de opslag worden beschouwd als serviceartefacten en de indeling kan veranderen
* Bestaande *zoekopdrachten voor opslaan* worden verwijderd uit uw werkruimte. Kopieer de *zoekopdrachten die u nodig hebt* vóór de configuratie en eventuele zoekopdrachten. U kunt uw *opgeslagen zoekopdrachten weergeven* met  [behulp van PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* Querygeschiedenis wordt niet ondersteund en u kunt geen query's zien die u hebt uitgevoerd
* U kunt één opslagaccount koppelen aan de werkruimte om query's op  te slaan, maar kan worden gebruikt voor zowel opgeslagen zoekopdrachten als query's *voor logboekwaarschuwingen*
* Vastmaken aan dashboard wordt niet ondersteund

**BYOS configureren voor query's opgeslagen zoekopdrachten**

Een opslagaccount voor *Query aan uw werkruimte* koppelen: *query's voor opgeslagen* zoekopdrachten worden opgeslagen in uw opslagaccount. 

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Na de configuratie wordt elke nieuwe *opgeslagen zoekquery* opgeslagen in uw opslag.

**BYOS configureren voor query's voor logboekwaarschuwingen**

Een opslagaccount voor waarschuwingen *aan uw* werkruimte koppelen: query's voor *logboekwaarschuwingen* worden opgeslagen in uw opslagaccount. 

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

N.v.t.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Na de configuratie wordt een nieuwe waarschuwingsquery opgeslagen in uw opslag.

## <a name="customer-lockbox-preview"></a>Klanten-lockbox (preview)

Lockbox geeft u de controle om de aanvraag van een Microsoft-technicus voor toegang tot uw gegevens goed te keuren of af te wijzen tijdens een ondersteuningsaanvraag.

In Azure Monitor hebt u dit besturingselement voor gegevens in werkruimten die zijn gekoppeld aan uw toegewezen Log Analytics-cluster. Het vergrendelingsvakbesturingselement is van toepassing op gegevens die zijn opgeslagen in een toegewezen Log Analytics-cluster, waar het geïsoleerd wordt gehouden in de opslagaccounts van het cluster onder uw met Lockbox beveiligde abonnement.  

Meer informatie over [Klanten-lockbox voor Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Customer-Managed sleutelbewerkingen

Customer-Managed sleutel wordt verstrekt op toegewezen cluster en deze bewerkingen worden beschreven in het [artikel Toegewezen cluster](./logs-dedicated-clusters.md#change-cluster-properties)

- Alle clusters in de resourcegroep op halen  
- Alle clusters in een abonnement krijgen
- *Capaciteitsreservering in* cluster bijwerken
- *BillingType in* cluster bijwerken
- Een werkruimte loskoppelen van een cluster
- Cluster verwijderen

## <a name="limitations-and-constraints"></a>Beperkingen

- Het maximumaantal clusters per regio en abonnement is 2

- Het maximum aantal werkruimten dat aan een cluster kan worden gekoppeld, is 1000

- U kunt een werkruimte aan uw cluster koppelen en deze vervolgens ontkoppelen. Het aantal koppelingsbewerkingen voor werkruimten in een bepaalde werkruimte is beperkt tot 2 in een periode van 30 dagen.

- Door de klant beheerde sleutelversleuteling is van toepassing op nieuw opgenomen gegevens na de configuratietijd. Gegevens die zijn opgenomen vóór de configuratie, blijven versleuteld met de Microsoft-sleutel. U kunt naadloos query's uitvoeren op gegevens die zijn opgenomen vóór en na de configuratie van de door de klant beheerde sleutel.

- De Azure Key Vault moeten worden geconfigureerd als herstelbaar. Deze eigenschappen zijn niet standaard ingeschakeld en moeten worden geconfigureerd met CLI of PowerShell:<br>
  - [Voorlopig verwijderen](../../key-vault/general/soft-delete-overview.md)
  - [Beveiliging tegen opsluizen](../../key-vault/general/soft-delete-overview.md#purge-protection) moet worden ingeschakeld om bescherming te bieden tegen het geforceer verwijderen van het geheim/de kluis, zelfs na een zachte verwijdering.

- Cluster verplaatsen naar een andere resourcegroep of een ander abonnement wordt momenteel niet ondersteund.

- Uw Azure Key Vault, het cluster en de werkruimten moeten zich in dezelfde regio en in dezelfde Azure Active Directory-tenant (Azure AD) hebben, maar ze kunnen zich in verschillende abonnementen hebben.

- Lockbox is momenteel niet beschikbaar in China. 

- [Dubbele versleuteling](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) wordt automatisch geconfigureerd voor clusters die zijn gemaakt vanaf oktober 2020 in ondersteunde regio's. U kunt controleren of uw cluster is geconfigureerd voor dubbele versleuteling door een GET-aanvraag op het cluster te verzenden en te controleren of de waarde is voor clusters met `isDoubleEncryptionEnabled` dubbele versleuteling `true` ingeschakeld. 
  - Als u een cluster maakt en de foutmelding '<region-name> doesn't support Double Encryption for clusters' (<region-name> biedt geen ondersteuning voor dubbele versleuteling voor clusters) wordt weergegeven, kunt u het cluster nog steeds maken zonder dubbele versleuteling door toe te voegen in de `"properties": {"isDoubleEncryptionEnabled": false}` rest-aanvraag.
  - Dubbele versleutelingsinstelling kan niet worden gewijzigd nadat het cluster is gemaakt.

  - Als uw cluster is ingesteld met een door de gebruiker toegewezen beheerde identiteit, wordt het cluster door instelling met opgeschort en wordt de toegang tot uw gegevens voorkomen. U kunt de intrekking echter niet terugdraaien en het cluster activeren zonder een `UserAssignedIdentities` `None` ondersteuningsaanvraag te openen. Deze beperking wordt toegepast op door het systeem toegewezen beheerde identiteit.

  - U kunt de door de klant beheerde sleutel niet gebruiken met een door de gebruiker toegewezen beheerde identiteit als uw Key Vault zich in Private-Link (vNet). In dit scenario kunt u een door het systeem toegewezen beheerde identiteit gebruiken.

## <a name="troubleshooting"></a>Problemen oplossen

- Gedrag met Key Vault beschikbaarheid
  - Bij normale werking wordt AEK gedurende korte tijd in de cache opgeslagen en wordt Key Vault om de AEK periodiek uit te pakken.
    
  - Tijdelijke verbindingsfouten: Opslag verwerkt tijdelijke fouten (time-outs, verbindingsfouten, DNS-problemen) doordat sleutels even in de cache kunnen blijven en hierdoor kleine problemen in beschikbaarheid worden ondervangen. De query- en opnamemogelijkheden worden zonder onderbreking voortgezet.
    
  - Livesite: als de beschikbaarheid van ongeveer 30 minuten niet beschikbaar is, is het opslagaccount niet meer beschikbaar. De queryfunctie is niet beschikbaar en opgenomen gegevens worden enkele uren in de cache opgeslagen met behulp van de Microsoft-sleutel om gegevensverlies te voorkomen. Wanneer de toegang tot Key Vault wordt hersteld, wordt de query beschikbaar en worden de tijdelijke gegevens in de cache opgenomen in het gegevensopslag en versleuteld met door de klant beheerde sleutel.

  - Key Vault toegangssnelheid: de frequentie waarmee Azure Monitor Storage toegang heeft tot Key Vault voor wrap- en uitpakbewerkingen ligt tussen 6 en 60 seconden.

- Als u uw cluster bij werkt terwijl het cluster de inrichtings- of updatetoestand heeft, mislukt de update.

- Als er een conflictfout wordt weergegeven bij het maken van een cluster: het kan zijn dat u het cluster in de afgelopen 14 dagen hebt verwijderd en dat het zich in een periode voor het verwijderen van een zachte status heeft. De clusternaam blijft gereserveerd tijdens de periode voor voorlopig verwijderen en u kunt geen nieuw cluster met die naam maken. De naam wordt vrijgegeven na de periode waarin het cluster definitief wordt verwijderd.

- De werkruimtekoppeling naar het cluster mislukt als deze is gekoppeld aan een ander cluster.

- Als u een cluster maakt en de KeyVaultProperties onmiddellijk opgeeft, kan de bewerking mislukken omdat het toegangsbeleid pas kan worden gedefinieerd als de systeemidentiteit aan het cluster is toegewezen.

- Als u een bestaand cluster bij werkt met KeyVaultProperties en sleuteltoegangsbeleid 'Get' ontbreekt in Key Vault, mislukt de bewerking.

- Als u het cluster niet kunt implementeren, controleert u of uw Azure Key Vault, het cluster en de gekoppelde Log Analytics-werkruimten zich in dezelfde regio. De kan zich in verschillende abonnementen.

- Als u uw sleutelversie in Key Vault bij te werken en de nieuwe sleutel-id-details in het cluster niet bij te werken, blijft het Log Analytics-cluster uw vorige sleutel gebruiken en worden uw gegevens ontoegankelijk. Werk nieuwe sleutel-id-gegevens in het cluster bij om de opname van gegevens en de mogelijkheid om query's uit te voeren op gegevens te hervatten.

- Sommige bewerkingen zijn lang en kunnen even duren. Dit zijn het maken van clusters, het bijwerken van clustersleutels en het verwijderen van clusters. U kunt de bewerkingsstatus op twee manieren controleren:
  1. wanneer u REST gebruikt, kopieert u Azure-AsyncOperation URL-waarde uit het antwoord en volgt u de statuscontrole voor [asynchrone bewerkingen.](#asynchronous-operations-and-status-check)
  2. Verzend de GET-aanvraag naar het cluster of de werkruimte en bekijk het antwoord. Een niet-gekoppelde werkruimte heeft bijvoorbeeld niet de *clusterResourceId* onder *functies*.

- Foutberichten
  
  **Cluster maken**
  -  400: de clusternaam is ongeldig. De clusternaam kan de tekens a-z, A-Z, 0-9 en een lengte van 3-63 bevatten.
  -  400: de body van de aanvraag is null of heeft een ongeldige indeling.
  -  400: de SKU-naam is ongeldig. Stel SKU-naam in op capacityReservation.
  -  400: er is capaciteit opgegeven, maar SKU is geen capacityReservation. Stel SKU-naam in op capacityReservation.
  -  400: ontbrekende capaciteit in SKU. Stel capaciteitswaarde in op 1000 of hoger in stappen van 100 (GB).
  -  400: de capaciteit in de SKU valt niet binnen het bereik. Moet minimaal 1000 zijn en maximaal de maximaal toegestane capaciteit die beschikbaar is onder Gebruik en geschatte kosten in uw werkruimte.
  -  400: capaciteit wordt 30 dagen vergrendeld. Capaciteit verlagen is 30 dagen na de update toegestaan.
  -  400: er is geen SKU ingesteld. Stel de SKU-naam in op capacityReservation en Capacity in stappen van 100 (GB) op 1000 of hoger.
  -  400: identiteit is null of leeg. Stel Identiteit in met het type systemAssigned.
  -  400: KeyVaultProperties worden ingesteld bij het maken. Werk KeyVaultProperties bij nadat het cluster is gemaakt.
  -  400: de bewerking kan nu niet worden uitgevoerd. Async-bewerking heeft een andere status dan geslaagd. Het cluster moet de bewerking voltooien voordat een updatebewerking wordt uitgevoerd.

  **Clusterupdate**
  -  400: het cluster wordt verwijderd. Er wordt een asynsynsync-bewerking uitgevoerd. Het cluster moet de bewerking voltooien voordat een updatebewerking wordt uitgevoerd.
  -  400: KeyVaultProperties is niet leeg, maar heeft een slechte indeling. Zie [Sleutel-id bijwerken.](#update-cluster-with-key-identifier-details)
  -  400: kan sleutel niet valideren in Key Vault. Dit kan komen door een gebrek aan machtigingen of wanneer de sleutel niet bestaat. Controleer of u [het sleutel- en toegangsbeleid](#grant-key-vault-permissions) in de Key Vault.
  -  400-- Sleutel kan niet worden hersteld. Key Vault moeten worden ingesteld op Soft-delete en Purge-protection. Zie [Key Vault documentatie](../../key-vault/general/soft-delete-overview.md)
  -  400: de bewerking kan nu niet worden uitgevoerd. Wacht tot de Async-bewerking is voltooid en probeer het opnieuw.
  -  400: het cluster wordt verwijderd. Wacht tot de Async-bewerking is voltooid en probeer het opnieuw.

  **Cluster Get**
    -  404-- Cluster niet gevonden, het cluster is mogelijk verwijderd. Als u probeert een cluster met die naam te maken en een conflict op te lossen, is het cluster 14 dagen in de soft delete-status. U kunt contact opnemen met de ondersteuning om dit te herstellen of een andere naam gebruiken om een nieuw cluster te maken. 

  **Cluster verwijderen**
    -  409: kan een cluster niet verwijderen terwijl deze de inrichtingstoestand heeft. Wacht tot de Async-bewerking is voltooid en probeer het opnieuw.

  **Werkruimtekoppeling**
  -  404-- Werkruimte niet gevonden. De werkruimte die u hebt opgegeven, bestaat niet of is verwijderd.
  -  409: een werkruimtekoppeling maken of de bewerking ontkoppelen tijdens het proces.
  -  400-- Cluster niet gevonden, het cluster dat u hebt opgegeven, bestaat niet of is verwijderd. Als u probeert een cluster met die naam te maken en een conflict op te lossen, wordt het cluster 14 dagen lang in de soft delete-status verwijderd. U kunt contact opnemen met de ondersteuning om deze te herstellen.

  **Werkruimte ontkoppelen**
  -  404-- Werkruimte niet gevonden. De werkruimte die u hebt opgegeven, bestaat niet of is verwijderd.
  -  409: een werkruimtekoppeling maken of de bewerking ontkoppelen tijdens het proces.
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [facturering van toegewezen Log Analytics-clusters](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- Meer informatie [over het juiste ontwerp van Log Analytics-werkruimten](./design-logs-deployment.md)