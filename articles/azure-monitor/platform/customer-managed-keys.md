---
title: Door de klant beheerde sleutel van Azure Monitor
description: Informatie en stappen voor het configureren van door de klant beheerde sleutel voor het versleutelen van gegevens in uw Log Analytics-werk ruimten met behulp van een Azure Key Vault sleutel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: f2807501b1e18d4cbffaa34d70bccf8d70565266
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747220"
---
# <a name="azure-monitor-customer-managed-key"></a>Door de klant beheerde sleutel van Azure Monitor 

Gegevens in Azure Monitor worden versleuteld met door micro soft beheerde sleutels. U kunt uw eigen versleutelings sleutel gebruiken voor het beveiligen van de gegevens en opgeslagen query's in uw werk ruimten. Wanneer u een door de klant beheerde sleutel opgeeft, wordt deze sleutel gebruikt voor het beveiligen en beheren van de toegang tot uw gegevens en eenmaal geconfigureerd, worden alle gegevens die naar uw werk ruimten worden verzonden, versleuteld met uw Azure Key Vault sleutel. Door de klant beheerde sleutels bieden meer flexibiliteit om toegangsbeheer te beheren.

U wordt aangeraden [beperkingen en beperkingen](#limitationsandconstraints) hieronder vóór de configuratie te bekijken.

## <a name="customer-managed-key-overview"></a>Overzicht van door de klant beheerde sleutels

[Versleuteling op rest](../../security/fundamentals/encryption-atrest.md) is een veelvoorkomende privacy-en beveiligings vereiste in organisaties. U kunt de versleuteling op de rest van Azure volledig beheren, terwijl u verschillende opties hebt om versleutelings-en versleutelings sleutels nauw keurig te beheren.

Azure Monitor zorgt ervoor dat alle gegevens en opgeslagen query's op rest worden versleuteld met behulp van door micro soft beheerde sleutels (MMK). Azure Monitor biedt ook een optie voor versleuteling met behulp van uw eigen sleutel die is opgeslagen in uw [Azure Key Vault](../../key-vault/general/overview.md), waarmee u de toegang tot uw gegevens op elk gewenst moment kunt intrekken. Azure Monitor versleuteling is hetzelfde als de manier waarop [Azure Storage versleuteling](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) werkt.

Door de klant beheerde sleutel wordt geleverd op [toegewezen clusters](../log-query/logs-dedicated-clusters.md) met een hoger beveiligings niveau en controle. Gegevens die zijn opgenomen in toegewezen clusters, worden twee maal versleuteld: eenmaal op service niveau met door micro soft beheerde sleutels of door de klant beheerde sleutels, en eenmaal op het niveau van de infra structuur met twee verschillende versleutelings algoritmen en twee verschillende sleutels. [Dubbele versleuteling](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) beschermt tegen een scenario waarbij een van de versleutelings algoritmen of-sleutels mogelijk is aangetast. In dit geval blijft de extra laag versleuteling uw gegevens beveiligen. Met toegewezen cluster kunt u uw gegevens ook beveiligen met behulp van een [lockbox](#customer-lockbox-preview) -besturings element.

De gegevens die in de afgelopen 14 dagen zijn opgenomen, worden ook opgeslagen in de Hot-cache (met SSD-back-ups) voor een efficiënte query-engine bewerking. Deze gegevens blijven versleuteld met micro soft-sleutels, ongeacht de configuratie van de door de klant beheerde sleutel, maar uw controle over SSD-gegevens voldoet aan de [sleutel intrekking](#key-revocation). Er wordt gewerkt aan SSD-gegevens die zijn versleuteld met door de klant beheerde sleutel in de eerste helft van 2021.

Log Analytics toegewezen clusters gebruiken een [prijs model](../log-query/logs-dedicated-clusters.md#cluster-pricing-model) voor capaciteits reservering, te beginnen bij 1000 GB per dag.

> [!IMPORTANT]
> Als gevolg van tijdelijke capaciteits beperkingen, moet u zich vooraf registreren bij voordat u een cluster maakt. Gebruik uw contact personen in micro soft of open de ondersteunings aanvraag om uw abonnementen-Id's te registreren.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Hoe door de klant beheerde sleutel werkt in Azure Monitor

Azure Monitor gebruikt beheerde identiteit om toegang tot uw Azure Key Vault te verlenen. De identiteit van het Log Analytics cluster wordt ondersteund op cluster niveau. Om door de klant beheerde sleutel beveiliging op meerdere werk ruimten toe te staan, wordt een nieuwe Log Analytics *cluster* resource uitgevoerd als een tussenliggende identiteits verbinding tussen uw Key Vault en uw log Analytics-werk ruimten. De opslag van het cluster maakt gebruik van de beheerde identiteit die \' is gekoppeld aan de *cluster* bron om de Azure Key Vault via Azure Active Directory te verifiëren. 

Na de configuratie van de door de klant beheerde sleutel, worden nieuwe opgenomen gegevens aan werk ruimten die zijn gekoppeld aan uw toegewezen cluster, versleuteld met uw sleutel. U kunt werk ruimten op elk gewenst moment ontkoppelen van het cluster. Nieuwe gegevens worden vervolgens opgenomen in Log Analytics opslag en versleuteld met de micro soft-sleutel, terwijl u uw nieuwe en oude gegevens naadloos kunt opvragen.

> [!IMPORTANT]
> Door de klant beheerde sleutel mogelijkheid is regionaal. Uw Azure Key Vault-, cluster-en gekoppelde Log Analytics-werk ruimten moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

![Overzicht van door de klant beheerde sleutels](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics *cluster* bron met beheerde identiteit met machtigingen voor Key Vault--de identiteit wordt door gegeven aan de toegewezen log Analytics-cluster opslag van aan
3. Toegewezen Log Analytics cluster
4. Werk ruimten die zijn gekoppeld aan een *cluster* bron 

### <a name="encryption-keys-operation"></a>Versleutelings sleutel bewerking

Er zijn drie soorten sleutels betrokken bij het versleutelen van opslag gegevens:

- **Kek** -sleutel versleutelings sleutel (uw door de klant beheerde sleutel)
- **AEK** -account versleutelings sleutel
- **Dek** -gegevens versleutelings sleutel

De volgende regels zijn van toepassing:

- De Log Analytics cluster-opslag accounts genereren een unieke versleutelings sleutel voor elk opslag account, dat wordt aangeduid als de AEK.
- De AEK wordt gebruikt om DEKs af te leiden. Dit zijn de sleutels die worden gebruikt voor het versleutelen van elk gegevens blok dat naar de schijf wordt geschreven.
- Wanneer u de sleutel in Key Vault configureert en ernaar verwijst in het cluster, Azure Storage verzendt aanvragen naar uw Azure Key Vault om in te pakken en de AEK te verpakken voor het uitvoeren van gegevens versleuteling en ontsleuteling.
- Uw KEK verlaat uw Key Vault nooit en in het geval van een HSM-sleutel verlaat het nooit de hardware.
- Azure Storage gebruikt de beheerde identiteit die is gekoppeld aan de *cluster* bron om te verifiëren en toegang te krijgen tot Azure Key Vault via Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Stappen voor het inrichten van Customer-Managed sleutels

1. Uw abonnement registreren zodat het cluster kan worden gemaakt
1. Azure Key Vault maken en de sleutel opslaan
1. Cluster maken
1. Machtigingen verlenen aan uw Key Vault
1. Het cluster bijwerken met sleutel-id-Details
1. Log Analytics-werk ruimten koppelen

Configuratie van door de klant beheerde sleutel wordt niet ondersteund in Azure Portal momenteel en inrichten kan worden uitgevoerd via [Power shell](/powershell/module/az.operationalinsights/), [cli](/cli/azure/monitor/log-analytics) of [rest](/rest/api/loganalytics/) -aanvragen.

### <a name="asynchronous-operations-and-status-check"></a>Asynchrone bewerkingen en status controle

Sommige van de configuratie stappen worden asynchroon uitgevoerd, omdat ze niet snel kunnen worden voltooid. De `status` in-reactie kan een van de volgende zijn: ' InProgress ', ' Updating ', ' deleted ', ' geslaagd ' of ' failed ' met de fout code.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N.v.t.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

N.v.t.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

N.v.t.

# <a name="rest"></a>[REST](#tab/rest)

Wanneer REST wordt gebruikt, retourneert de reactie in eerste instantie een HTTP-status code 202 (geaccepteerd) en de header met de eigenschap *Azure-AsyncOperation* :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

U kunt de status van de asynchrone bewerking controleren door een GET-aanvraag naar het eind punt te verzenden in *Azure-AsyncOperation-* header:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

### <a name="allowing-subscription"></a>Abonnement toestaan

Gebruik uw contact personen in micro soft of open de ondersteunings aanvraag in Log Analytics om uw abonnementen-Id's op te geven.

## <a name="storing-encryption-key-kek"></a>Versleutelings sleutel opslaan (KEK)

Maak of gebruik een Azure Key Vault die u al moet genereren of importeer een sleutel die moet worden gebruikt voor gegevens versleuteling. De Azure Key Vault moet worden geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw gegevens in Azure Monitor te beveiligen. U kunt deze configuratie controleren onder eigenschappen in uw Key Vault, de beveiliging van zowel *zacht verwijderen* als *leegmaken* moet zijn ingeschakeld.

![Zacht verwijderen en beveiligings instellingen opschonen](media/customer-managed-keys/soft-purge-protection.png)

Deze instellingen kunnen worden bijgewerkt in Key Vault via CLI en Power shell:

- [Voorlopig verwijderen](../../key-vault/general/soft-delete-overview.md)
- [Beveiligings](../../key-vault/general/soft-delete-overview.md#purge-protection) beveiligingen verwijderen tegen het verwijderen van het geheim of de kluis, zelfs na het zacht verwijderen

## <a name="create-cluster"></a>Cluster maken

Clusters ondersteunen twee [beheerde identiteits typen](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types): systeem toegewezen en door de gebruiker toegewezen, terwijl één identiteit kan worden gedefinieerd in een cluster, afhankelijk van uw scenario. 
- Door het systeem toegewezen beheerde identiteit is eenvoudiger en wordt automatisch gegenereerd met het maken van clusters wanneer de identiteit `type` is ingesteld op '*SystemAssigned*'. Deze identiteit kan later worden gebruikt om het cluster toegang te verlenen tot uw Key Vault. 
  
  Identiteits instellingen in het cluster voor door het systeem toegewezen beheerde identiteit
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Als u door de klant beheerde sleutel wilt configureren bij het maken van het cluster, moet u een door de gebruiker toegewezen identiteit in uw Key Vault vooraf hebben verleend en vervolgens het cluster maken met de volgende instellingen: identiteit `type` als '*UserAssigned*', `UserAssignedIdentities` met de resource-id van de identiteit.

  Identiteits instellingen in het cluster voor door de gebruiker toegewezen beheerde identiteit
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft. ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> U kunt door de klant beheerde sleutel niet gebruiken met door de gebruiker toegewezen beheerde identiteit als uw Key Vault zich in Private-Link (vNet) bevindt. In dit scenario kunt u door het systeem toegewezen beheerde identiteit gebruiken.

```json
{
  "identity": {
    "type": "SystemAssigned"
}
```
 
Door:

```json
{
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft. ManagedIdentity/UserAssignedIdentities/<user-assigned-managed-identity-name>"
      }
}
```


Volg de procedure die wordt geïllustreerd in het [artikel dedicated clusters](../log-query/logs-dedicated-clusters.md#creating-a-cluster). 

## <a name="grant-key-vault-permissions"></a>Key Vault machtigingen verlenen

Maak een toegangs beleid in Key Vault om machtigingen te verlenen aan uw cluster. Deze machtigingen worden gebruikt door de aan-Azure Monitor opslag. Open uw Key Vault in Azure Portal en klik vervolgens op *toegangs beleid* en vervolgens op *toegangs beleid toevoegen* om een beleid te maken met de volgende instellingen:

- Sleutel machtigingen: Selecteer *Get*, de *tekst ' wrap* ' en *' uitpakken sleutel*'.
- Selecteer Principal: afhankelijk van het identiteits type dat in het cluster (door systeem of gebruiker toegewezen beheerde identiteit) wordt gebruikt, voert u de cluster naam of de ID van het cluster-Principal in voor door het systeem toegewezen beheerde identiteit of de door de gebruiker toegewezen beheerde identiteits naam.

![Key Vault machtigingen verlenen](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

De machtiging *Get* is vereist om te controleren of uw Key Vault is geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw Azure monitor gegevens te beveiligen.

## <a name="update-cluster-with-key-identifier-details"></a>Cluster bijwerken met sleutel-id-Details

Voor alle bewerkingen op het cluster is de `Microsoft.OperationalInsights/clusters/write` actie machtiging vereist. Deze machtiging kan worden verleend via de eigenaar of Inzender die de `*/write` actie bevat of via de log Analytics rol Inzender die de `Microsoft.OperationalInsights/*` actie bevat.

Met deze stap wordt Azure Monitor Storage bijgewerkt met de sleutel en versie die moeten worden gebruikt voor gegevens versleuteling. Wanneer u de nieuwe sleutel bijwerkt, wordt deze gebruikt om de opslag sleutel (AEK) in of uit te pakken.

Selecteer de huidige versie van uw sleutel in Azure Key Vault om de details van de sleutel-id op te halen.

![Key Vault machtigingen verlenen](media/customer-managed-keys/key-identifier-8bit.png)

KeyVaultProperties in cluster bijwerken met sleutel-id-Details.

De bewerking is asynchroon en kan enige tijd duren.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

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

Het duurt enkele minuten voordat de sleutel is door gegeven. U kunt de status van de update op twee manieren controleren:
1. Kopieer de Azure-AsyncOperation URL-waarde uit het antwoord en volg de controle op de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
2. Verzend een GET-aanvraag op het cluster en Bekijk de *KeyVaultProperties* -eigenschappen. De laatst bijgewerkte sleutel moet in het antwoord worden geretourneerd.

Een antwoord op de GET-aanvraag moet er als volgt uitzien wanneer de sleutel update is voltooid: 202 (geaccepteerd) en header
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

## <a name="link-workspace-to-cluster"></a>Werk ruimte koppelen aan cluster

> [!IMPORTANT]
> Deze stap moet pas worden uitgevoerd nadat de inrichting van het Log Analytics cluster is voltooid. Als u werk ruimten en opname gegevens vóór het inrichten koppelt, worden opgenomen gegevens verwijderd en kunnen deze niet worden hersteld.

U moet schrijf machtigingen hebben voor uw werk ruimte en cluster om deze bewerking uit te voeren, waaronder `Microsoft.OperationalInsights/workspaces/write` en `Microsoft.OperationalInsights/clusters/write` .

Volg de procedure die wordt geïllustreerd in het [artikel dedicated clusters](../log-query/logs-dedicated-clusters.md#link-a-workspace-to-cluster).

## <a name="key-revocation"></a>Intrekking van sleutel

> [!IMPORTANT]
> - De aanbevolen manier om de toegang tot uw gegevens in te trekken, is door de sleutel uit te scha kelen of door het toegangs beleid in uw Key Vault te verwijderen.
> - Als u het cluster instelt `identity` `type` op geen, wordt de toegang tot uw gegevens ook ingetrokken, maar deze methode wordt niet aanbevolen omdat u de intrekking niet kunt herstellen wanneer u de in het cluster opnieuw indeelt `identity` zonder de ondersteunings aanvraag te openen.

De cluster opslag respecteert altijd wijzigingen in de sleutel machtigingen binnen een uur of binnenkort en de opslag wordt niet meer beschikbaar. Nieuwe gegevens die zijn opgenomen in werk ruimten die zijn gekoppeld aan het cluster, worden verwijderd en kunnen niet worden hersteld, gegevens worden ontoegankelijk en query's op deze werk ruimten mislukken. Eerder opgenomen gegevens blijven in de opslag, zolang uw cluster en uw werk ruimten niet worden verwijderd. Ontoegankelijke gegevens zijn onderworpen aan het Bewaar beleid voor gegevens en worden verwijderd wanneer de Bewaar termijn wordt bereikt. Opgenomen gegevens in de afgelopen 14 dagen worden ook opgeslagen in een hot-cache (met SSD-back-ups) voor een efficiënte bewerking van query-engine. Dit wordt verwijderd bij het verwijderen van de sleutel en wordt ontoegankelijk.

In de opslag ruimte van het cluster wordt uw Key Vault periodiek gecontroleerd om te proberen de versleutelings sleutel op te halen en wanneer deze eenmaal is geopend, wordt de opname en query van gegevens binnen 30 minuten hervat.

## <a name="key-rotation"></a>Sleutelroulatie

Voor de door de klant beheerde sleutel rotatie is een expliciete update van het cluster met de nieuwe sleutel versie in Azure Key Vault vereist. [Cluster met sleutel-id-Details bijwerken](#update-cluster-with-key-identifier-details). Als u de nieuwe sleutel versie in het cluster niet bijwerkt, blijft de Log Analytics cluster opslag uw vorige sleutel gebruiken voor versleuteling. Als u de oude sleutel uitschakelt of verwijdert voordat u de nieuwe sleutel in het cluster bijwerkt, krijgt u een [belang rijke intrekkings](#key-revocation) status.

Al uw gegevens blijven toegankelijk na de bewerking voor het wijzigen van de sleutel, omdat gegevens altijd worden versleuteld met de account versleutelings sleutel (AEK) terwijl AEK nu wordt versleuteld met uw nieuwe Key Encryption Key (KEK)-versie in Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Door de klant beheerde sleutel voor opgeslagen query's

De query taal die in Log Analytics wordt gebruikt, is een exprestje en kan gevoelige informatie bevatten in opmerkingen die u toevoegt aan query's of in de query syntaxis. Sommige organisaties vereisen dat dergelijke informatie wordt beveiligd onder het door de klant beheerde sleutel beleid en dat u uw query's die zijn versleuteld met uw sleutel, moet opslaan. Met Azure Monitor kunt u *opgeslagen Zoek opdrachten* en *waarschuwingen voor logboek registraties* die zijn versleuteld met uw sleutel in uw eigen opslag account opslaan wanneer u verbinding hebt met uw werk ruimte. 

> [!NOTE]
> Log Analytics query's kunnen worden opgeslagen in verschillende winkels, afhankelijk van het gebruikte scenario. Query's blijven versleuteld met micro soft-sleutel (MMK) in de volgende scenario's, ongeacht de door de klant beheerde sleutel configuratie: werkmappen in Azure Monitor, Azure-Dash boards, Azure Logic app, Azure Notebooks en Automation-Runbooks.

Wanneer u uw eigen opslag (BYOS) meebrengt en deze aan uw werk ruimte koppelt, worden de door de service *opgeslagen Zoek opdrachten* en *waarschuwingen voor logboek meldingen* naar uw opslag account geüpload. Dit betekent dat u het opslag account en het [beleid voor versleuteling op rest](../../storage/common/customer-managed-keys-overview.md) beheert met behulp van dezelfde sleutel die u gebruikt voor het versleutelen van gegevens in log Analytics cluster of een andere sleutel. U bent echter verantwoordelijk voor de kosten van het betreffende opslagaccount. 

**Overwegingen voor het instellen van door de klant beheerde sleutel voor query's**
* U moet schrijf machtigingen hebben voor de werk ruimte en het opslag account
* Zorg ervoor dat u uw opslag account in dezelfde regio maakt als uw Log Analytics werk ruimte zich bevindt
* De *opgeslagen Zoek opdrachten* in opslag worden beschouwd als service artefacten en de indeling ervan kan veranderen
* Bestaande *Zoek opdrachten voor opslaan* worden verwijderd uit uw werk ruimte. Kopieer en *Sla de Zoek opdrachten* die u nodig hebt vóór de configuratie. U kunt uw *opgeslagen Zoek opdrachten* weer geven met  [Power shell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* De query geschiedenis wordt niet ondersteund en u kunt geen query's zien die u hebt uitgevoerd
* U kunt één opslag account koppelen aan de werk ruimte voor het opslaan van query's, maar kan ook worden gebruikt in query's met de functie voor *opgeslagen Zoek opdrachten* en *logboek waarschuwingen*
* Vastmaken aan dash board wordt niet ondersteund

**BYOS configureren voor opgeslagen Zoek opdrachten**

Een opslag account voor een *query* aan uw werk ruimte koppelen: *opgeslagen Zoek opdrachten* query's worden opgeslagen in uw opslag account. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

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

Na de configuratie wordt een nieuwe *opgeslagen zoek opdracht* opgeslagen in uw opslag.

**BYOS configureren voor query's met betrekking tot logboek waarschuwingen**

Een opslag account voor *waarschuwingen* aan uw werk ruimte koppelen: query's voor *logboek waarschuwingen* worden opgeslagen in uw opslag account. 

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

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

Na de configuratie wordt een nieuwe waarschuwings query opgeslagen in uw opslag.

## <a name="customer-lockbox-preview"></a>Klanten-lockbox (preview-versie)

Met lockbox kunt u de micro soft Engineer-aanvraag voor toegang tot uw gegevens goed keuren of afwijzen tijdens een ondersteunings aanvraag.

In Azure Monitor hebt u dit controle over gegevens in werk ruimten die zijn gekoppeld aan uw Log Analytics toegewezen cluster. Het besturings element lockbox is van toepassing op gegevens die zijn opgeslagen in een Log Analytics toegewezen cluster, waar het wordt geïsoleerd in de opslag accounts van het cluster onder uw abonnement op uw lockbox.  

Meer informatie over [klanten-lockbox voor Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Sleutel bewerkingen Customer-Managed

Customer-Managed sleutel wordt op toegewezen cluster gegeven en deze bewerkingen worden vermeld in een [toegewezen cluster artikel](../log-query/logs-dedicated-clusters.md#change-cluster-properties)

- Alle clusters in de resource groep ophalen  
- Alle clusters in het abonnement ophalen
- *Capaciteits reservering* in cluster bijwerken
- *BillingType* in cluster bijwerken
- Een werk ruimte ontkoppelen van een cluster
- Cluster verwijderen

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

- Het maximale aantal clusters per regio en abonnement is 2

- Het maximum aantal werk ruimten dat kan worden gekoppeld aan een cluster is 1000

- U kunt een werk ruimte aan uw cluster koppelen en de koppeling vervolgens ontkoppelen. Het aantal bewerkingen voor werkruimte koppelingen op een bepaalde werk ruimte is beperkt tot 2 in een periode van 30 dagen.

- Door de klant beheerde sleutel versleuteling is van toepassing op nieuwe opgenomen gegevens na de configuratie tijd. Gegevens die vóór de configuratie zijn opgenomen, blijven versleuteld met de micro soft-sleutel. U kunt een query uitvoeren op gegevens die zijn opgenomen voor en na de configuratie van de door de klant beheerde sleutel naadloos.

- De Azure Key Vault moet worden geconfigureerd als herstelbaar. Deze eigenschappen zijn niet standaard ingeschakeld en moeten worden geconfigureerd met CLI of Power shell:<br>
  - [Voorlopig verwijderen](../../key-vault/general/soft-delete-overview.md)
  - Het [opschonen](../../key-vault/general/soft-delete-overview.md#purge-protection) van de beveiliging moet zijn ingeschakeld om te beschermen tegen het verwijderen van het geheim of de kluis, zelfs na het verwijderen van de software.

- Het cluster verplaatsen naar een andere resource groep of een ander abonnement wordt momenteel niet ondersteund.

- Uw Azure Key Vault, cluster en werk ruimten moeten zich in dezelfde regio bevinden en in dezelfde Azure Active Directory (Azure AD)-Tenant, maar ze kunnen zich in verschillende abonnementen bevinden.

- Lockbox is momenteel niet beschikbaar in China. 

- [Dubbele versleuteling](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) wordt automatisch geconfigureerd voor clusters die zijn gemaakt van oktober 2020 in ondersteunde regio's. U kunt controleren of uw cluster is geconfigureerd voor dubbele versleuteling door een GET-aanvraag te verzenden naar het cluster en te zien dat de `isDoubleEncryptionEnabled` waarde `true` voor clusters is waarvoor dubbele versleuteling is ingeschakeld. 
  - Als u een cluster maakt en er een fout melding krijgt met de naam ' <regio-name> ondersteunt geen dubbele versleuteling voor clusters. ' kunt u het cluster nog steeds maken zonder dubbele code ring door toe te voegen `"properties": {"isDoubleEncryptionEnabled": false}` in de hoofd tekst van de rest-aanvraag.
  - De instelling voor dubbele versleuteling kan niet worden gewijzigd nadat het cluster is gemaakt.

  - Als uw cluster is ingesteld met een door de gebruiker toegewezen beheerde identiteit, wordt `UserAssignedIdentities` `None` het cluster onderbroken en wordt de toegang tot uw gegevens voor komen, maar u kunt de intrekking niet herstellen en het cluster activeren zonder dat er een ondersteunings aanvraag wordt geopend. Deze beperking hebben ' toegepast op door het systeem toegewezen beheerde identiteit.

  - U kunt door de klant beheerde sleutel niet gebruiken met door de gebruiker toegewezen beheerde identiteit als uw Key Vault zich in Private-Link (vNet) bevindt. In dit scenario kunt u door het systeem toegewezen beheerde identiteit gebruiken.

## <a name="troubleshooting"></a>Problemen oplossen

- Gedrag met Key Vault Beschik baarheid
  - In normale werking: opslag caches AEK gedurende korte tijd en terugvallen op Key Vault om regel matig de vertraging op te lossen.
    
  - Tijdelijke verbindings fouten--opslag verwerkt tijdelijke fouten (time-outs, verbindings fouten, DNS-problemen) doordat sleutels gedurende langere tijd in de cache blijven staan. Dit geeft een kleine problemen in Beschik baarheid. De mogelijkheden voor het uitvoeren van query's en opname worden zonder onderbreking voortgezet.
    
  - Live site--de niet-beschik baarheid van ongeveer 30 minuten leidt ertoe dat het opslag account niet meer beschikbaar is. De query mogelijkheid is niet beschikbaar en opgenomen gegevens worden gedurende enkele uren in de cache opgeslagen met behulp van micro soft-code om gegevens verlies te voor komen. Wanneer toegang tot Key Vault wordt hersteld, wordt de query beschikbaar en worden de gegevens in de tijdelijke cache opgenomen in de gegevens opslag en versleuteld met door de klant beheerde sleutel.

  - Toegangs snelheid van Key Vault: de frequentie waarmee Azure Monitor toegang tot Key Vault voor verpakte en onverpakte bewerkingen tussen 6 en 60 seconden ligt.

- Als u het cluster bijwerkt terwijl de status van het cluster wordt ingericht of bijgewerkt, mislukt de update.

- Als er een conflict fout optreedt tijdens het maken van een cluster, is het mogelijk dat u uw cluster in de afgelopen 14 dagen hebt verwijderd en dat het een tijdelijke, verwijderings periode is. De cluster naam blijft gereserveerd tijdens de tijdelijke periode en u kunt geen nieuw cluster met die naam maken. De naam wordt vrijgegeven na de periode voor voorlopig verwijderen wanneer het cluster permanent wordt verwijderd.

- Werkruimte koppeling naar cluster zal mislukken als deze is gekoppeld aan een ander cluster.

- Als u een cluster maakt en de KeyVaultProperties onmiddellijk opgeeft, kan de bewerking mislukken omdat het toegangs beleid niet kan worden gedefinieerd totdat de systeem identiteit is toegewezen aan het cluster.

- Als u een bestaand cluster bijwerkt met KeyVaultProperties en het sleutel toegangs beleid Get ontbreekt in Key Vault, mislukt de bewerking.

- Als u het cluster niet implementeert, controleert u of uw Azure Key Vault-, cluster-en gekoppelde Log Analytics-werk ruimten zich in dezelfde regio bevinden. De kan zich in verschillende abonnementen bevindt.

- Als u de sleutel versie bijwerkt in Key Vault en de nieuwe sleutel-id-Details in het cluster niet bijwerkt, blijft de Log Analytics cluster uw vorige sleutel gebruiken en worden uw gegevens niet meer toegankelijk. Update de nieuwe sleutel-id in het cluster om gegevens opname en de mogelijkheid om gegevens op te vragen te hervatten.

- Sommige bewerkingen zijn lang en kunnen even duren: Dit zijn clusters maken, cluster sleutel updates en cluster verwijdering. U kunt de bewerkings status op twee manieren controleren:
  1. Wanneer u REST gebruikt, kopieert u de waarde van de Azure-AsyncOperation-URL uit het antwoord en volgt u de controle van de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
  2. Verzend aanvraag verzenden naar cluster of werk ruimte en Bekijk het antwoord. Niet-gekoppelde werk ruimte heeft bijvoorbeeld niet de *clusterResourceId* onder *functies*.

- Foutberichten
  
  **Cluster maken**
  -  400--de cluster naam is niet geldig. De cluster naam kan de tekens a-z, A-Z, 0-9 en de lengte van 3-63 bevatten.
  -  400--de hoofd tekst van de aanvraag is null of heeft een ongeldige indeling.
  -  400--SKU-naam is ongeldig. Stel de SKU-naam in op capacityReservation.
  -  400--er is capaciteit gegeven, maar SKU is niet capacityReservation. Stel de SKU-naam in op capacityReservation.
  -  400--ontbrekende capaciteit in SKU. Stel de capaciteits waarde in op 1000 of hoger in stappen van 100 (GB).
  -  400--capaciteit in SKU valt niet binnen het bereik. Moet mini maal 1000 zijn en tot de Maxi maal toegestane capaciteit beschikken die beschikbaar is onder gebruik en geschatte kosten in uw werk ruimte.
  -  400--capaciteit is 30 dagen vergrendeld. Het verminderen van de capaciteit is 30 dagen na de update toegestaan.
  -  400--er is geen SKU ingesteld. Stel de SKU-naam in op capacityReservation en capaciteits waarde in 1000 of hoger in stappen van 100 (GB).
  -  400--identiteit is null of leeg. Stel identiteit in met het type systemAssigned.
  -  400--KeyVaultProperties worden ingesteld bij het maken. KeyVaultProperties bijwerken na het maken van het cluster.
  -  400--bewerking kan nu niet worden uitgevoerd. De asynchrone bewerking bevindt zich in een andere status dan geslaagd. Het cluster moet de bewerking volt ooien voordat een update bewerking wordt uitgevoerd.

  **Cluster update**
  -  400--de status van het cluster wordt verwijderd. De asynchrone bewerking wordt uitgevoerd. Het cluster moet de bewerking volt ooien voordat een update bewerking wordt uitgevoerd.
  -  400--KeyVaultProperties is niet leeg, maar heeft een ongeldige indeling. Zie [sleutel-id bijwerken](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400--kan de sleutel in Key Vault niet valideren. Kan worden veroorzaakt door onvoldoende machtigingen of wanneer de sleutel niet bestaat. Controleer of u het [sleutel-en toegangs beleid hebt ingesteld](../platform/customer-managed-keys.md#grant-key-vault-permissions) in Key Vault.
  -  400--sleutel kan niet worden hersteld. Key Vault moet worden ingesteld op zacht verwijderen en de beveiliging op te schonen. Raadpleeg de [documentatie van Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400--bewerking kan nu niet worden uitgevoerd. Wacht tot de asynchrone bewerking is voltooid en probeer het opnieuw.
  -  400--de status van het cluster wordt verwijderd. Wacht tot de asynchrone bewerking is voltooid en probeer het opnieuw.

  **Cluster ophalen**
    -  404--het cluster is niet gevonden. mogelijk is het cluster verwijderd. Als u probeert een cluster met die naam te maken en conflicten op te halen, wordt het cluster gedurende 14 dagen voorlopig verwijderd. U kunt contact opnemen met de ondersteuning om het te herstellen, of een andere naam gebruiken om een nieuw cluster te maken. 

  **Cluster verwijderen**
    -  409--een cluster kan niet worden verwijderd tijdens de inrichtings status. Wacht tot de asynchrone bewerking is voltooid en probeer het opnieuw.

  **Werkruimte koppeling**
  -  404--werk ruimte is niet gevonden. De werk ruimte die u hebt opgegeven, bestaat niet of is verwijderd.
  -  409--werk ruimte koppeling of ontkoppelen bewerking in verwerking.
  -  400--het cluster is niet gevonden. het cluster dat u hebt opgegeven, bestaat niet of is verwijderd. Als u probeert een cluster met die naam te maken en conflicten op te halen, wordt het cluster gedurende 14 dagen voorlopig verwijderd. U kunt contact opnemen met de ondersteuning om deze te herstellen.

  **Werk ruimte ontkoppelen**
  -  404--werk ruimte is niet gevonden. De werk ruimte die u hebt opgegeven, bestaat niet of is verwijderd.
  -  409--werk ruimte koppeling of ontkoppelen bewerking in verwerking.
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [log Analytics toegewezen cluster facturering](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Meer informatie over het [goede ontwerp van log Analytics-werk ruimten](../platform/design-logs-deployment.md)