---
title: Azure Monitor logboeken toegewezen clusters
description: Klanten die meer dan 1 TB een dag aan bewakings gegevens hebben opgenomen, kunnen toegewezen worden gebruikt in plaats van gedeelde clusters
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: d261640dfdb59b2b06cfe3066fca26640a0bed54
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874641"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor logboeken toegewezen clusters

Azure Monitor logboeken toegewezen clusters zijn een implementatie optie die beschikbaar is voor het verbeteren van klanten met een hoog volume. Klanten die meer dan 4 TB gegevens per dag opnemen, zullen toegewezen clusters gebruiken. Klanten met toegewezen clusters kunnen de werk ruimten kiezen die worden gehost op deze clusters.

Met uitzonde ring van de ondersteuning voor hoog volume zijn er andere voor delen van het gebruik van speciale clusters:

- **Frequentie limiet** : een klant kan alleen hogere [opname frequentie limieten](../service-limits.md#data-ingestion-volume-rate) hebben op een toegewezen cluster.
- **Functies** : bepaalde bedrijfs functies zijn alleen beschikbaar voor speciale clusters, met name door de klant beheerde sleutels (CMK) en lockbox-ondersteuning. 
- **Consistentie** : klanten hebben hun eigen toegewezen resources en er is geen invloed op de andere klanten die worden uitgevoerd op dezelfde gedeelde infra structuur.
- **Kosten efficiëntie** : het is mogelijk rendabeler om een toegewezen cluster te gebruiken, omdat de toegewezen capaciteits reserverings lagen rekening houden met alle cluster opname en van toepassing zijn op alle werk ruimten, zelfs als deze klein zijn en niet in aanmerking komen voor de korting op capaciteits reservering.
- Query's in **meerdere werk ruimten** worden sneller uitgevoerd als alle werk ruimten zich op hetzelfde cluster bevinden.

Voor toegewezen clusters moeten klanten een capaciteit van ten minste 1 TB gegevens opname per dag door voeren. Migratie naar een toegewezen cluster is eenvoudig. Er is geen gegevens verlies of onderbreking van de service. 

> [!IMPORTANT]
> Toegewezen clusters zijn goedgekeurd en volledig ondersteund voor productie-implementaties. Vanwege tijdelijke capaciteits beperkingen, hebben we echter uw voorafgaande registratie nodig voor het gebruik van de functie. Gebruik uw contact personen in micro soft om uw abonnementen-Id's op te geven.

## <a name="management"></a>Beheer 

Toegewezen clusters worden beheerd via een Azure-resource die Azure Monitor logboek clusters vertegenwoordigt. Alle bewerkingen worden uitgevoerd op deze resource met behulp van Power shell of de REST API.

Zodra het cluster is gemaakt, kan het worden geconfigureerd en worden de werk ruimten hieraan gekoppeld. Wanneer een werk ruimte aan een cluster is gekoppeld, bevinden nieuwe gegevens die worden verzonden naar de werk ruimte zich op het cluster. Alleen werk ruimten die zich in dezelfde regio bevinden als het cluster kunnen worden gekoppeld aan het cluster. Werk ruimten kunnen in tegens telling tot een cluster, met enkele beperkingen. Meer informatie over deze beperkingen vindt u in dit artikel. 

Gegevens die zijn opgenomen in toegewezen clusters, worden twee maal versleuteld: eenmaal op service niveau met door micro soft beheerde sleutels of door de [klant beheerde sleutel](../platform/customer-managed-keys.md), en eenmaal op het niveau van de infra structuur met twee verschillende versleutelings algoritmen en twee verschillende sleutels. [Dubbele versleuteling](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) beschermt tegen een scenario waarbij een van de versleutelings algoritmen of-sleutels mogelijk is aangetast. In dit geval blijft de extra laag versleuteling uw gegevens beveiligen. Met toegewezen cluster kunt u uw gegevens ook beveiligen met behulp van een [lockbox](../platform/customer-managed-keys.md#customer-lockbox-preview) -besturings element.

Voor alle bewerkingen op het cluster niveau is de `Microsoft.OperationalInsights/clusters/write` actie machtiging vereist voor het cluster. Deze machtiging kan worden verleend via de eigenaar of Inzender die de `*/write` actie bevat of via de log Analytics rol Inzender die de `Microsoft.OperationalInsights/*` actie bevat. Zie [toegang tot logboek gegevens en werk ruimten in azure monitor beheren](../platform/manage-access.md)voor meer informatie over log Analytics machtigingen. 


## <a name="cluster-pricing-model"></a>Prijs model voor cluster

Log Analytics toegewezen clusters gebruiken een prijs model voor capaciteits reservering dat ten minste 1000 GB/dag is. Elk gebruik boven het reserverings niveau wordt gefactureerd op basis van het betalen naar gebruik-tarief.  De prijs informatie voor capaciteits reservering is beschikbaar op de [pagina met Azure monitor prijzen]( https://azure.microsoft.com/pricing/details/monitor/).  

Het reserverings niveau voor cluster capaciteit wordt via een programma geconfigureerd met Azure Resource Manager met behulp van de `Capacity` para meter onder `Sku` . De `Capacity` is opgegeven in eenheden van GB en kan waarden hebben van 1000 GB/dag of meer in stappen van 100 GB/dag.

Er zijn twee facturerings methoden voor gebruik in een cluster. Deze kunnen worden opgegeven met de `billingType` para meter bij het configureren van uw cluster. 

1. **Cluster**: in dit geval (dit is de standaard instelling), wordt de facturering voor opgenomen gegevens uitgevoerd op het cluster niveau. De opgenomen gegevens aantallen uit elke werk ruimte die aan een cluster is gekoppeld, worden geaggregeerd voor het berekenen van de dagelijkse factuur voor het cluster. 

2. **Werk ruimten**: de kosten voor de capaciteits reservering voor uw cluster worden proportioneel toegeschreven aan de werk ruimten in het cluster (na de accounting van toewijzingen per knoop punt van [Azure Security Center](../../security-center/index.yml) voor elke werk ruimte.)

Houd er rekening mee dat als uw werk ruimte verouderde prijs categorie per knoop punt gebruikt, wanneer deze is gekoppeld aan een cluster, wordt gefactureerd op basis van gegevens die zijn opgenomen in de capaciteits reservering van het cluster en niet langer per knoop punt. Gegevens toewijzingen per knoop punt van Azure Security Center worden nog steeds toegepast.

[Hier]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)vindt u meer informatie over facturering voor log Analytics toegewezen clusters.


## <a name="creating-a-cluster"></a>Een cluster maken

U maakt eerst cluster bronnen om te beginnen met het maken van een toegewezen cluster.

De volgende eigenschappen moeten worden opgegeven:

- **Clustername**: wordt gebruikt voor administratieve doel einden. Gebruikers worden niet blootgesteld aan deze naam.
- **ResourceGroupName**: voor elke Azure-resource behoren clusters tot een resource groep. We raden u aan een centrale IT-resource groep te gebruiken omdat clusters meestal worden gedeeld door veel teams in de organisatie. Raadpleeg [uw implementatie van Azure monitor-logboeken ontwerpen](../platform/design-logs-deployment.md) voor meer overwegingen bij het ontwerpen
- **Locatie**: een cluster bevindt zich in een specifieke Azure-regio. Alleen werk ruimten in deze regio kunnen worden gekoppeld aan dit cluster.
- **SkuCapacity**: u moet het *capaciteits reserverings* niveau (SKU) opgeven bij het maken van een *cluster* bron. Het *capaciteits reserverings* niveau kan zich in het bereik van 1.000 gb tot 3.000 GB per dag bevindt. U kunt deze indien nodig bijwerken in de stappen van 100. Als u een capaciteits reserverings niveau nodig hebt dat hoger is dan 3.000 GB per dag, neemt u contact met ons op LAIngestionRate@microsoft.com . Zie [kosten voor log Analytics clusters beheren](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) voor meer informatie over de cluster kosten

Nadat u de *cluster* bron hebt gemaakt, kunt u extra eigenschappen bewerken, zoals *SKU*, * keyVaultProperties of *billingType*. Zie hieronder voor meer informatie.

> [!WARNING]
> Het maken van een cluster activeert resource toewijzing en inrichting. Het kan een uur duren voordat deze bewerking is voltooid. Het wordt aanbevolen deze asynchroon uit te voeren.

Het gebruikers account dat de clusters maakt, moet beschikken over de standaard machtiging voor het maken van een Azure-resource: `Microsoft.Resources/deployments/*` en de schrijf machtiging voor het cluster `(Microsoft.OperationalInsights/clusters/write)` .

### <a name="create"></a>Maken 

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Call* 
```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

*Response*

Moet 200 OK en een kop zijn.

### <a name="check-provisioning-status"></a>De inrichtingsstatus controleren

Het inrichten van het Log Analytics cluster duurt enige tijd. U kunt de inrichtings status op verschillende manieren controleren:

- Voer Get-AzOperationalInsightsCluster Power shell-opdracht uit met de naam van de resource groep en controleer de eigenschap ProvisioningState. De waarde is *ProvisioningAccount* *tijdens het inrichten en voltooid* .
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Kopieer de Azure-AsyncOperation URL-waarde uit het antwoord en volg de controle op de asynchrone bewerkings status.

- Verzend een GET-aanvraag op de *cluster* resource en kijk naar de waarde *provisioningState* . De waarde is *ProvisioningAccount* *tijdens het inrichten en voltooid* .

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
   Authorization: Bearer <token>
   ```

   **Response**

   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-id"
       },
     "sku": {
       "name": "capacityReservation",
       "capacity": 1000,
       "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
       },
     "properties": {
       "provisioningState": "ProvisioningAccount",
       "billingType": "cluster",
       "clusterId": "cluster-id"
       },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

De *principalId* GUID wordt gegenereerd door de beheerde identiteits service voor de *cluster* bron.

## <a name="change-cluster-properties"></a>Cluster eigenschappen wijzigen

Nadat u de *cluster* bron hebt gemaakt en volledig is ingericht, kunt u aanvullende eigenschappen bewerken op cluster niveau met behulp van Power shell of rest API. Met uitzonde ring van de eigenschappen die beschikbaar zijn tijdens het maken van het cluster, kunnen extra eigenschappen alleen worden ingesteld nadat het cluster is ingericht:

- **keyVaultProperties**: wordt gebruikt om de Azure Key Vault te configureren die wordt gebruikt om een Azure monitor door de [klant beheerde sleutel](../platform/customer-managed-keys.md#customer-managed-key-provisioning-procedure)in te richten. Het bevat de volgende para meters:  *KeyVaultUri*, *naam* van de *versie*. 
- **billingType** : de eigenschap *billingType* bepaalt het facturerings kenmerk voor de *cluster* bron en de bijbehorende gegevens.
  - **Cluster** (standaard): de kosten voor de capaciteits reservering voor uw cluster worden toegeschreven aan de *cluster* bron.
  - **Werk ruimten** : de kosten voor de capaciteits reservering voor uw cluster worden proportioneel toegeschreven aan de werk ruimten in het cluster, waarbij een deel van de *cluster* resource wordt gefactureerd als de totale opgenomen gegevens voor de dag onder de capaciteits reservering vallen. Zie [log Analytics toegewezen clusters](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) voor meer informatie over het prijs model van het cluster. 

> [!NOTE]
> De eigenschap *billingType* wordt niet ondersteund in Power shell.
> Updates van cluster eigenschappen kunnen asynchroon worden uitgevoerd en kunnen even duren.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> U kunt de *cluster* resource- *SKU*, *keyVaultProperties* of *billingType* bijwerken met behulp van patch.

Bijvoorbeeld: 

*Call*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

' KeyVaultProperties ' bevat de details van de Key Vault sleutel-id.

*Response*

200 OK en koptekst

### <a name="check-cluster-update-status"></a>Status van cluster update controleren

Het duurt enkele minuten om de doorgifte van de sleutel-id uit te voeren. U kunt de status van de update op twee manieren controleren:

- Kopieer de Azure-AsyncOperation URL-waarde uit het antwoord en volg de controle op de asynchrone bewerkings status. 

   OF

- Verzend een GET-aanvraag voor de *cluster* bron en Bekijk de *KeyVaultProperties* -eigenschappen. Uw recent bijgewerkte sleutel-id-details moeten in het antwoord worden geretourneerd.

   Een antwoord op de GET-aanvraag voor de *cluster* bron moet er als volgt uitzien wanneer de sleutel-id-Update is voltooid:

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

## <a name="link-a-workspace-to-the-cluster"></a>Een werk ruimte aan het cluster koppelen

Wanneer een werk ruimte is gekoppeld aan een toegewezen cluster, worden nieuwe gegevens die worden opgenomen in de werk ruimte doorgestuurd naar het nieuwe cluster, terwijl bestaande gegevens op het bestaande cluster blijven staan. Als het toegewezen cluster wordt versleuteld met door de klant beheerde sleutels (CMK), worden alleen nieuwe gegevens versleuteld met de sleutel. Dit verschil van de gebruikers wordt door het systeem abstracter en de gebruikers voeren gewoon een query uit op de werk ruimte terwijl het systeem query's op meerdere clusters uitvoert op de back-end.

Een cluster kan worden gekoppeld aan Maxi maal 100 werk ruimten. Gekoppelde werk ruimten bevinden zich in dezelfde regio als het cluster. Een werk ruimte kan niet meer dan twee keer per maand aan een cluster worden gekoppeld om de systeem back-end te beveiligen en fragmentatie van gegevens te voor komen.

Als u de koppelings bewerking wilt uitvoeren, moet u schrijf machtigingen hebben voor zowel de werk ruimte als de *cluster* Bron:

- In de werk ruimte: *micro soft. OperationalInsights/werk ruimten/schrijven*
- In de *cluster* resource: *micro soft. OperationalInsights/clusters/schrijven*

Met uitzonde ring van de facturerings aspecten, behouden de gekoppelde werk ruimte eigen instellingen, zoals de lengte van het bewaren van gegevens.
De werk ruimte en het cluster kunnen zich in verschillende abonnementen bevinden. Het is mogelijk dat de werk ruimte en het cluster zich in verschillende tenants bevinden als Azure Lighthouse wordt gebruikt om beide te koppelen aan één Tenant.

Bij elke cluster bewerking kan het koppelen van een werk ruimte alleen worden uitgevoerd na het volt ooien van de Log Analytics cluster inrichting.

> [!WARNING]
> Voor het koppelen van een werk ruimte aan een cluster moet u meerdere back-end-onderdelen synchroniseren en de cache Hydration. Het kan tot twee uur duren voordat deze bewerking is voltooid. U wordt aangeraden deze asynchroon uit te voeren.


**PowerShell**

Gebruik de volgende Power shell-opdracht om een koppeling te maken naar een cluster:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Gebruik de volgende REST-aanroep om een koppeling met een cluster te maken:

*Verzenden*

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Response*

200 OK en koptekst.

### <a name="using-customer-managed-keys-with-linking"></a>Door de klant beheerde sleutels gebruiken met koppelen

Als u door de klant beheerde sleutels gebruikt, worden opgenomen gegevens na de koppelings bewerking met uw beheerde sleutel opgeslagen. Dit kan Maxi maal 90 minuten duren. 

U kunt de status van de werkruimte koppeling op twee manieren controleren:

- Kopieer de Azure-AsyncOperation URL-waarde uit het antwoord en volg de controle op de asynchrone bewerkings status.

- Een [werk ruimte verzenden – aanvraag ophalen](/rest/api/loganalytics/workspaces/get) en het antwoord bekijken. De gekoppelde werk ruimte bevat een clusterResourceId onder ' features '.

Een verzend aanvraag ziet er als volgt uit:

*Verzenden*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*Response*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Een werk ruimte ontkoppelen van een toegewezen cluster

U kunt een werk ruimte ontkoppelen van een cluster. Nadat een werk ruimte is ontkoppeld van het cluster, worden nieuwe gegevens die zijn gekoppeld aan deze werk ruimte, niet naar het toegewezen cluster verzonden. De facturering van de werk ruimte wordt ook niet meer via het cluster uitgevoerd. De oude gegevens van de niet-gekoppelde werk ruimte kunnen in het cluster blijven staan. Als deze gegevens zijn versleuteld met door de klant beheerde sleutels (CMK), worden de Key Vault geheimen bewaard. Het systeem wordt deze wijziging van Log Analytics gebruikers abstract. Gebruikers kunnen alleen op de gebruikelijke manier een query uitvoeren op de werk ruimte. Het systeem voert query's van meerdere clusters op de back-end uit zonder dat er gebruikers worden aangegeven.  

> [!WARNING] 
> Er is een limiet van twee koppelings bewerkingen per werk ruimte binnen een maand. Houd rekening met het nemen en plannen van de koppeling van de acties dienovereenkomstig. 

## <a name="delete-a-dedicated-cluster"></a>Een toegewezen cluster verwijderen

Een toegewezen cluster bron kan worden verwijderd. U moet alle werk ruimten ontkoppelen van het cluster voordat u deze verwijdert. U hebt schrijf machtigingen voor de *cluster* bron nodig om deze bewerking uit te voeren. 

Zodra de cluster bron is verwijderd, voert het fysieke cluster een verwijderings-en verwijder proces uit. Als een cluster wordt verwijderd, worden alle gegevens die zijn opgeslagen op het cluster verwijderd. De gegevens kunnen afkomstig zijn uit werk ruimten die in het verleden zijn gekoppeld aan het cluster.

Een *cluster* bron die in de afgelopen 14 dagen is verwijderd, is in de status voorlopig verwijderen en kan worden hersteld met de bijbehorende gegevens. Omdat alle werk ruimten die niet zijn gekoppeld aan de *cluster* bron *,* worden verwijderd, moet u na het herstel uw werk ruimten opnieuw koppelen. De herstel bewerking kan niet worden uitgevoerd door de gebruiker. Neem contact op met uw micro soft-kanaal of ondersteuning voor herstel aanvragen.

Binnen 14 dagen na de verwijdering is de naam van de cluster resource gereserveerd en kan deze niet worden gebruikt door andere resources.

**PowerShell**

Gebruik de volgende Power shell-opdracht voor het verwijderen van een cluster:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Gebruik de volgende REST-aanroep om een cluster te verwijderen:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**

  200 OK



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [log Analytics toegewezen cluster facturering](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Meer informatie over het [goede ontwerp van log Analytics-werk ruimten](../platform/design-logs-deployment.md)