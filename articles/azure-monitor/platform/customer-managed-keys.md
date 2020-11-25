---
title: Door de klant beheerde sleutel van Azure Monitor
description: Informatie en stappen voor het configureren van Customer-Managed sleutel voor het versleutelen van gegevens in uw Log Analytics-werk ruimten met behulp van een Azure Key Vault sleutel.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/18/2020
ms.openlocfilehash: 7bfd951d7cec27e0b8264aaabf9bc3a17875256a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000722"
---
# <a name="azure-monitor-customer-managed-key"></a>Door de klant beheerde sleutel van Azure Monitor 

Dit artikel bevat achtergrond informatie en stappen voor het configureren van door de klant beheerde sleutels voor uw Log Analytics-werk ruimten. Eenmaal geconfigureerd, worden alle gegevens die naar uw werk ruimten worden verzonden, versleuteld met uw Azure Key Vault sleutel.

U wordt aangeraden [beperkingen en beperkingen](#limitationsandconstraints) hieronder vóór de configuratie te bekijken.

## <a name="customer-managed-key-overview"></a>Overzicht van door de klant beheerde sleutels

[Versleuteling op rest](../../security/fundamentals/encryption-atrest.md) is een veelvoorkomende privacy-en beveiligings vereiste in organisaties. U kunt de versleuteling op de rest van Azure volledig beheren, terwijl u verschillende opties hebt om versleutelings-en versleutelings sleutels nauw keurig te beheren.

Azure Monitor zorgt ervoor dat alle gegevens en opgeslagen query's op rest worden versleuteld met behulp van door micro soft beheerde sleutels (MMK). Azure Monitor biedt ook een optie voor versleuteling met uw eigen sleutel die is opgeslagen in uw [Azure Key Vault](../../key-vault/general/overview.md) en geeft u het beheer om de toegang tot uw gegevens op elk gewenst moment in te trekken. Azure Monitor versleuteling is hetzelfde als de manier waarop [Azure Storage versleuteling](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) werkt.

Customer-Managed sleutel wordt geleverd op toegewezen Log Analytics clusters met een hoger beveiligings niveau en controle. Gegevens die zijn opgenomen in toegewezen clusters, worden twee maal versleuteld: eenmaal op service niveau met door micro soft beheerde sleutels of door de klant beheerde sleutels, en eenmaal op het niveau van de infra structuur met twee verschillende versleutelings algoritmen en twee verschillende sleutels. [Dubbele versleuteling](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) beschermt tegen een scenario waarbij een van de versleutelings algoritmen of-sleutels mogelijk is aangetast. In dit geval blijft de extra laag versleuteling uw gegevens beveiligen. Met toegewezen cluster kunt u uw gegevens ook beveiligen met behulp van een [lockbox](#customer-lockbox-preview) -besturings element.

De gegevens die in de afgelopen 14 dagen zijn opgenomen, worden ook opgeslagen in de Hot-cache (met SSD-back-ups) voor een efficiënte query-engine bewerking. Deze gegevens blijven versleuteld met micro soft-sleutels, ongeacht de configuratie van de door de klant beheerde sleutel, maar uw controle over SSD-gegevens voldoet aan de [sleutel intrekking](#key-revocation). Er wordt gewerkt aan SSD-gegevens die zijn versleuteld met Customer-Managed sleutel in de eerste helft van 2021.

Het [prijs model van log Analytics clusters](./manage-cost-storage.md#log-analytics-dedicated-clusters) maakt gebruik van capaciteits reserveringen vanaf een niveau van 1000 GB per dag.

> [!IMPORTANT]
> Als gevolg van tijdelijke capaciteits beperkingen, moet u zich vooraf registreren bij voordat u een cluster maakt. Gebruik uw contact personen in micro soft of open de ondersteunings aanvraag om uw abonnementen-Id's te registreren.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Hoe Customer-Managed sleutel werkt in Azure Monitor

Azure Monitor maakt gebruik van door het systeem toegewezen beheerde identiteit om toegang tot uw Azure Key Vault te verlenen. Door het systeem toegewezen beheerde identiteiten kunnen alleen worden gekoppeld aan één Azure-resource, terwijl de identiteit van het Log Analytics cluster wordt ondersteund op cluster niveau. Dit bepaalt of de mogelijkheid wordt geleverd op een toegewezen Log Analytics cluster. Ter ondersteuning van Customer-Managed sleutel op meerdere werk ruimten, wordt een nieuwe Log Analytics *cluster* resource uitgevoerd als een tussenliggende identiteits verbinding tussen uw Key Vault en uw log Analytics-werk ruimten. De Log Analytics cluster opslag maakt gebruik van de beheerde identiteit die \' aan de *cluster* bron is gekoppeld om de Azure Key Vault via Azure Active Directory te verifiëren. 

Na de configuratie worden gegevens die zijn opgenomen in werk ruimten die zijn gekoppeld aan uw toegewezen cluster, versleuteld met uw sleutel in Key Vault. U kunt werk ruimten op elk gewenst moment ontkoppelen van het cluster. Nieuwe gegevens worden vervolgens opgenomen in Log Analytics opslag en versleuteld met de micro soft-sleutel, terwijl u uw nieuwe en oude gegevens naadloos kunt opvragen.


![Overzicht van Customer-Managed-sleutel](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics *cluster* bron met beheerde identiteit met machtigingen voor Key Vault--de identiteit wordt door gegeven aan de toegewezen log Analytics-cluster opslag van aan
3. Toegewezen Log Analytics cluster
4. Werk ruimten die zijn gekoppeld aan een *cluster* bron 

## <a name="encryption-keys-operation"></a>Versleutelings sleutel bewerking

Er zijn drie soorten sleutels betrokken bij het versleutelen van opslag gegevens:

- **Kek** -sleutel versleutelings sleutel (uw Customer-Managed sleutel)
- **AEK** -account versleutelings sleutel
- **Dek** -gegevens versleutelings sleutel

De volgende regels zijn van toepassing:

- De Log Analytics cluster-opslag accounts genereren een unieke versleutelings sleutel voor elk opslag account, dat wordt aangeduid als de AEK.
- De AEK wordt gebruikt om DEKs af te leiden. Dit zijn de sleutels die worden gebruikt voor het versleutelen van elk gegevens blok dat naar de schijf wordt geschreven.
- Wanneer u de sleutel in Key Vault configureert en ernaar verwijst in het cluster, Azure Storage verzendt aanvragen naar uw Azure Key Vault om in te pakken en de AEK te verpakken voor het uitvoeren van gegevens versleuteling en ontsleuteling.
- Uw KEK verlaat uw Key Vault nooit en in het geval van een HSM-sleutel verlaat het nooit de hardware.
- Azure Storage gebruikt de beheerde identiteit die is gekoppeld aan de *cluster* bron om te verifiëren en toegang te krijgen tot Azure Key Vault via Azure Active Directory.

## <a name="customer-managed-key-provisioning-procedure"></a>Procedure voor het inrichten van Customer-Managed sleutels

1. Uw abonnement registreren zodat het cluster kan worden gemaakt
1. Azure Key Vault maken en de sleutel opslaan
1. Cluster maken
1. Machtigingen verlenen aan uw Key Vault
1. Log Analytics-werk ruimten koppelen

Customer-Managed-sleutel configuratie wordt niet ondersteund in Azure Portal en het inrichten wordt uitgevoerd via [Power shell](https://docs.microsoft.com/powershell/module/az.operationalinsights/), [cli](https://docs.microsoft.com/cli/azure/monitor/log-analytics) of [rest](https://docs.microsoft.com/rest/api/loganalytics/) -aanvragen.

### <a name="asynchronous-operations-and-status-check"></a>Asynchrone bewerkingen en status controle

Sommige van de configuratie stappen worden asynchroon uitgevoerd, omdat ze niet snel kunnen worden voltooid. Bij gebruik van REST retourneert de reactie in eerste instantie een HTTP-status code 200 (OK) en koptekst met de eigenschap *Azure-AsyncOperation* wanneer deze wordt geaccepteerd:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

U kunt de status van de asynchrone bewerking controleren door een GET-aanvraag te verzenden naar de waarde van de *Azure-AsyncOperation-* header:
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

De `status` in-antwoord bevat kan een van de volgende zijn: InProgress, bijwerken, verwijderen, geslaagd of mislukt, inclusief de fout code.

### <a name="allowing-subscription"></a>Abonnement toestaan

> [!IMPORTANT]
> De functionaliteit van Customer-Managed-sleutel is regionaal. Uw Azure Key Vault-, cluster-en gekoppelde Log Analytics-werk ruimten moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

### <a name="storing-encryption-key-kek"></a>Versleutelings sleutel opslaan (KEK)

Maak of gebruik een Azure Key Vault die u al moet genereren of importeer een sleutel die moet worden gebruikt voor gegevens versleuteling. De Azure Key Vault moet worden geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw gegevens in Azure Monitor te beveiligen. U kunt deze configuratie controleren onder eigenschappen in uw Key Vault, de beveiliging van zowel *zacht verwijderen* als *leegmaken* moet zijn ingeschakeld.

![Zacht verwijderen en beveiligings instellingen opschonen](media/customer-managed-keys/soft-purge-protection.png)

Deze instellingen kunnen worden bijgewerkt in Key Vault via CLI en Power shell:

- [Voorlopig verwijderen](../../key-vault/general/soft-delete-overview.md)
- [Beveiligings](../../key-vault/general/soft-delete-overview.md#purge-protection) beveiligingen verwijderen tegen het verwijderen van het geheim of de kluis, zelfs na het zacht verwijderen

### <a name="create-cluster"></a>Cluster maken

Volg de procedure die wordt geïllustreerd in het [artikel dedicated clusters](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#creating-a-cluster). 

> [!IMPORTANT]
> Kopieer het antwoord en sla het op, omdat u de details in de volgende stappen nodig hebt.

### <a name="grant-key-vault-permissions"></a>Key Vault machtigingen verlenen

Maak een toegangs beleid in Key Vault om machtigingen te verlenen aan uw cluster. Deze machtigingen worden gebruikt door de aan Azure Monitor opslag voor gegevens versleuteling. Open uw Key Vault in Azure Portal en klik vervolgens op toegangs beleid en vervolgens op toegangs beleid toevoegen om een beleid te maken met de volgende instellingen:

- Belang rijke machtigingen: Selecteer Get, terugloop sleutel en de machtigingen voor de uitpakken sleutel.
- Selecteer Principal: Voer de naam van het cluster of de principal-id in die in de vorige stap is geretourneerd.

![Key Vault machtigingen verlenen](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

De machtiging *Get* is vereist om te controleren of uw Key Vault is geconfigureerd als herstelbaar om uw sleutel en de toegang tot uw Azure monitor gegevens te beveiligen.

### <a name="update-cluster-with-key-identifier-details"></a>Cluster bijwerken met sleutel-id-Details

Voor alle bewerkingen in het cluster is de machtiging micro soft. OperationalInsights/clusters/schrijf actie vereist. Deze machtiging kan worden verleend via de eigenaar of Inzender die de */Write-actie bevat of via de log Analytics rol Inzender die de micro soft. OperationalInsights/Action bevat* .

Met deze stap wordt Azure Monitor Storage bijgewerkt met de sleutel en versie die moeten worden gebruikt voor gegevens versleuteling. Wanneer u de nieuwe sleutel bijwerkt, wordt deze gebruikt om de opslag sleutel (AEK) in of uit te pakken.

Selecteer de huidige versie van uw sleutel in Azure Key Vault om de details van de sleutel-id op te halen.

![Key Vault machtigingen verlenen](media/customer-managed-keys/key-identifier-8bit.png)

KeyVaultProperties in cluster bijwerken met sleutel-id-Details.

De bewerking is asynchroon en kan enige tijd duren.

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name"?api-version=2020-08-01
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

Het duurt de doorgifte van de sleutel-id enkele minuten om te volt ooien. U kunt de status van de update op twee manieren controleren:
1. Kopieer de Azure-AsyncOperation URL-waarde uit het antwoord en volg de controle op de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
2. Verzend een GET-aanvraag op het cluster en Bekijk de *KeyVaultProperties* -eigenschappen. Uw recent bijgewerkte sleutel-id-details moeten in het antwoord worden geretourneerd.

Een antwoord op de GET-aanvraag moet er als volgt uitzien wanneer de sleutel-id-Update is voltooid: 200 OK en koptekst
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

### <a name="link-workspace-to-cluster"></a>Werk ruimte koppelen aan cluster

U moet schrijf machtigingen hebben voor uw werk ruimte en cluster om deze bewerking uit te voeren, waaronder de volgende acties:

- In de werk ruimte: micro soft. OperationalInsights/werk ruimten/schrijven
- In cluster: micro soft. OperationalInsights/clusters/schrijven

> [!IMPORTANT]
> Deze stap moet pas worden uitgevoerd nadat de inrichting van het Log Analytics cluster is voltooid. Als u werk ruimten en opname gegevens vóór het inrichten koppelt, worden opgenomen gegevens verwijderd en kunnen deze niet worden hersteld.

Deze bewerking is asynchroon en kan enige tijd worden voltooid.

Volg de procedure die wordt geïllustreerd in het [artikel dedicated clusters](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters#link-a-workspace-to-the-cluster).

## <a name="key-revocation"></a>Intrekking van sleutel

U kunt de toegang tot gegevens intrekken door de sleutel uit te scha kelen of door het toegangs beleid van het cluster in uw Key Vault te verwijderen. De Log Analytics-cluster opslag respecteert altijd wijzigingen in de sleutel machtigingen binnen een uur of eerder, en de opslag wordt niet meer beschikbaar. Nieuwe gegevens die zijn opgenomen in werk ruimten die zijn gekoppeld aan het cluster, worden verwijderd en kunnen niet worden hersteld, gegevens zijn niet toegankelijk en query's naar deze werk ruimten mislukken. Eerder opgenomen gegevens blijven in de opslag, zolang uw cluster en uw werk ruimten niet worden verwijderd. Ontoegankelijke gegevens zijn onderworpen aan het Bewaar beleid voor gegevens en worden verwijderd wanneer de Bewaar termijn wordt bereikt. 

Opgenomen gegevens in de afgelopen 14 dagen worden ook opgeslagen in een hot-cache (met SSD-back-ups) voor een efficiënte bewerking van query-engine. Dit wordt verwijderd bij het verwijderen van de sleutel en wordt ook niet toegankelijk.

Met opslag worden uw Key Vault periodiek gecontroleerd om te proberen om de versleutelings sleutel op te slaan en na het openen van de gegevens en het hervatten van query's binnen 30 minuten.

## <a name="key-rotation"></a>Sleutelroulatie

Voor het roteren van Customer-Managed sleutels is een expliciete update van het cluster vereist met de nieuwe sleutel versie in Azure Key Vault. Volg de instructies in de stap ' cluster bijwerken met sleutel-id Details '. Als u de nieuwe sleutel-id-Details in het cluster niet bijwerkt, blijft de Log Analytics cluster opslag uw vorige sleutel gebruiken voor versleuteling. Als u de oude sleutel uitschakelt of verwijdert voordat u de nieuwe sleutel in het cluster bijwerkt, krijgt u een [belang rijke intrekkings](#key-revocation) status.

Al uw gegevens blijven toegankelijk na de bewerking voor het wijzigen van de sleutel, omdat gegevens altijd worden versleuteld met de account versleutelings sleutel (AEK) terwijl AEK nu wordt versleuteld met uw nieuwe Key Encryption Key (KEK)-versie in Key Vault.

## <a name="customer-managed-key-for-queries"></a>Customer-Managed sleutel voor query's

De query taal die in Log Analytics wordt gebruikt, is een exprestje en kan gevoelige informatie bevatten in opmerkingen die u toevoegt aan query's of in de query syntaxis. Sommige organisaties vereisen dat dergelijke informatie wordt beveiligd onder Customer-Managed-sleutel beleid en dat u uw query's die met uw sleutel zijn versleuteld, moet opslaan. Met Azure Monitor kunt u *opgeslagen Zoek opdrachten* en *waarschuwingen voor logboek registraties* die zijn versleuteld met uw sleutel in uw eigen opslag account opslaan wanneer u verbinding hebt met uw werk ruimte. 

> [!NOTE]
> Log Analytics query's kunnen worden opgeslagen in verschillende winkels, afhankelijk van het gebruikte scenario. Query's blijven versleuteld met micro soft-sleutel (MMK) in de volgende scenario's, ongeacht Customer-Managed sleutel configuratie: werkmappen in Azure Monitor, Azure-Dash boards, Azure Logic app, Azure Notebooks en Automation-Runbooks.

Wanneer u uw eigen opslag (BYOS) meebrengt en deze aan uw werk ruimte koppelt, worden de door de service *opgeslagen Zoek opdrachten* en *waarschuwingen voor logboek meldingen* naar uw opslag account geüpload. Dit betekent dat u het opslag account en het [beleid voor versleuteling op rest](../../storage/common/customer-managed-keys-overview.md) beheert met behulp van dezelfde sleutel die u gebruikt voor het versleutelen van gegevens in log Analytics cluster of een andere sleutel. U bent echter verantwoordelijk voor de kosten van het opslag account. 

**Overwegingen voor het instellen van Customer-Managed sleutel voor query's**
* U moet schrijf machtigingen hebben voor de werk ruimte en het opslag account
* Zorg ervoor dat u uw opslag account in dezelfde regio maakt als uw Log Analytics werk ruimte zich bevindt
* De *opgeslagen Zoek opdrachten* in opslag worden beschouwd als service artefacten en de indeling ervan kan veranderen
* Bestaande *Zoek opdrachten voor opslaan* worden verwijderd uit uw werk ruimte. Kopieer en *Sla de Zoek opdrachten* die u nodig hebt vóór de configuratie. U kunt uw *opgeslagen Zoek opdrachten* weer geven met  [Power shell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch)
* De query geschiedenis wordt niet ondersteund en u kunt geen query's zien die u hebt uitgevoerd
* U kunt één opslag account koppelen aan de werk ruimte voor het opslaan van query's, maar kan ook worden gebruikt in query's met de functie voor *opgeslagen Zoek opdrachten* en *logboek waarschuwingen*
* Vastmaken aan dash board wordt niet ondersteund

**BYOS configureren voor opgeslagen Zoek opdrachten**

Een opslag account voor een *query* aan uw werk ruimte koppelen: *opgeslagen Zoek opdrachten* query's worden opgeslagen in uw opslag account. 

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

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

Na de configuratie wordt een nieuwe *opgeslagen zoek opdracht* opgeslagen in uw opslag.

**BYOS configureren voor query's met betrekking tot logboek waarschuwingen**

Een opslag account voor *waarschuwingen* aan uw werk ruimte koppelen: query's voor *logboek waarschuwingen* worden opgeslagen in uw opslag account. 

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

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

Na de configuratie wordt een nieuwe waarschuwings query opgeslagen in uw opslag.

## <a name="customer-lockbox-preview"></a>Klanten-lockbox (preview-versie)
Met lockbox kunt u de micro soft Engineer-aanvraag voor toegang tot uw gegevens goed keuren of afwijzen tijdens een ondersteunings aanvraag.

In Azure Monitor hebt u dit controle over gegevens in werk ruimten die zijn gekoppeld aan uw Log Analytics toegewezen cluster. Het besturings element lockbox is van toepassing op gegevens die zijn opgeslagen in een Log Analytics toegewezen cluster, waar het wordt geïsoleerd in de opslag accounts van het cluster onder uw abonnement op uw lockbox.  

Meer informatie over [klanten-lockbox voor Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Sleutel bewerkingen Customer-Managed

- **Alle clusters in een resource groep ophalen**
  
  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Response**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- **Alle clusters in een abonnement ophalen**

  ```azurecli
  az monitor log-analytics cluster list
  ```

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Response**
    
  Hetzelfde antwoord als voor een cluster in een resource groep, maar in het abonnements bereik.

- ***Capaciteits reservering* in cluster bijwerken**

  Wanneer het gegevens volume naar uw gekoppelde werk ruimten in de loop van de tijd verandert en u het capaciteits reserverings niveau op de juiste wijze wilt bijwerken. Volg de [update cluster](#update-cluster-with-key-identifier-details) en geef de nieuwe capaciteits waarde op. Dit kan binnen het bereik van 1000 tot 3000 GB per dag zijn en in stappen van 100. Voor een niveau hoger dan 3000 GB per dag, bereikt u uw micro soft-contact persoon om dit in te scha kelen. Houd er rekening mee dat u geen volledige REST-aanvraag tekst hoeft op te geven, maar moet de SKU bevatten:

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

- ***BillingType* in cluster bijwerken**

  De eigenschap *billingType* bepaalt de facturerings toewijzing voor het cluster en de bijbehorende gegevens:
  - *cluster* (standaard): de facturering wordt toegeschreven aan het abonnement dat als host fungeert voor uw cluster bron
  - *werk ruimten* : de facturering wordt toegeschreven aan de abonnementen die proportioneel als host fungeren voor uw werk ruimten
  
  Volg de [update cluster](#update-cluster-with-key-identifier-details) en geef de nieuwe waarde voor billingType op. Houd er rekening mee dat u de volledige REST-aanvraag tekst niet hoeft op te geven en moet de *billingType* bevatten:

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Werkruimte ontkoppelen**

  U hebt schrijf machtigingen voor de werk ruimte en het cluster nodig om deze bewerking uit te voeren. U kunt een werk ruimte op elk gewenst moment ontkoppelen van het cluster. Nieuwe opgenomen gegevens na de ontkoppelings bewerking worden opgeslagen in Log Analytics opslag en versleuteld met de micro soft-sleutel. U kunt een query uitvoeren op gegevens die zijn opgenomen in uw werk ruimte vóór en na de ontkoppeling, zolang het cluster is ingericht en geconfigureerd met een geldige Key Vault sleutel.

  Deze bewerking is asynchroon en kan enige tijd worden voltooid.

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  - **Status van werkruimte koppeling controleren**
  
  Een Get-bewerking uitvoeren op de werk ruimte en bekijken of de eigenschap *clusterResourceId* aanwezig is in de reactie onder *functies*. Een gekoppelde werk ruimte heeft de eigenschap *clusterResourceId* .

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

- **Uw cluster verwijderen**

  U hebt schrijf machtigingen op het cluster nodig om deze bewerking uit te voeren. Er wordt een bewerking voor zacht verwijderen uitgevoerd om het herstel van uw cluster met gegevens binnen 14 dagen mogelijk te maken, of het verwijderen per ongeluk of opzettelijk is geslaagd. De cluster naam blijft gereserveerd tijdens de tijdelijke periode en u kunt geen nieuw cluster met die naam maken. Na de voorlopig verwijderde periode wordt de naam van het cluster vrijgegeven en worden uw cluster en de bijbehorende gegevens permanent verwijderd en kunnen ze niet worden hersteld. Een gekoppelde werk ruimte wordt ontkoppeld van het cluster bij een Verwijder bewerking. Nieuwe opgenomen gegevens worden opgeslagen in Log Analytics Storage en versleuteld met de micro soft-sleutel. 
  
  De bewerking ontkoppelen is asynchroon en kan Maxi maal 90 minuten duren.

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```
 
  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
  
- **Uw cluster en uw gegevens herstellen** 
  
  Een cluster dat in de afgelopen 14 dagen is verwijderd, bevindt zich in de status voorlopig verwijderen en kan worden hersteld met de bijbehorende gegevens. Omdat alle werk ruimten die niet zijn gekoppeld aan het cluster, worden verwijderd, moet u uw werk ruimten na het herstel van het cluster opnieuw koppelen. De herstel bewerking wordt momenteel hand matig uitgevoerd door de product groep. Gebruik uw micro soft-kanaal of open ondersteunings aanvraag voor herstel van een verwijderd cluster.

## <a name="limitations-and-constraints"></a>Beperkingen en beperkingen

- Customer-Managed sleutel wordt ondersteund op toegewezen Log Analytics cluster en is geschikt voor klanten die 1 TB per dag of langer verzenden.

- Het maximale aantal clusters per regio en abonnement is 2

- Het maximum aantal gekoppelde werk ruimten voor het cluster is 1000

- U kunt een werk ruimte aan uw cluster koppelen en de koppeling vervolgens ontkoppelen. Het aantal bewerkingen voor werkruimte koppelingen op een bepaalde werk ruimte is beperkt tot 2 in een periode van 30 dagen.

- Werkruimte koppeling naar het cluster moet alleen worden uitgevoerd nadat u hebt gecontroleerd of de inrichting van het Log Analytics cluster is voltooid. Gegevens die vóór de voltooiing naar uw werk ruimte worden verzonden, worden verwijderd en kunnen niet worden hersteld.

- Customer-Managed sleutel versleuteling is van toepassing op nieuwe opgenomen gegevens na de configuratie tijd. Gegevens die vóór de configuratie zijn opgenomen, blijven versleuteld met de micro soft-sleutel. U kunt een query uitvoeren op gegevens die zijn opgenomen voor en na de configuratie van de Customer-Managed-sleutel naadloos.

- De Azure Key Vault moet worden geconfigureerd als herstelbaar. Deze eigenschappen zijn niet standaard ingeschakeld en moeten worden geconfigureerd met CLI of Power shell:<br>
  - [Voorlopig verwijderen](../../key-vault/general/soft-delete-overview.md)
  - Het [opschonen](../../key-vault/general/soft-delete-overview.md#purge-protection) van de beveiliging moet zijn ingeschakeld om te beschermen tegen het verwijderen van het geheim of de kluis, zelfs na het verwijderen van de software.

- Het cluster verplaatsen naar een andere resource groep of een ander abonnement wordt momenteel niet ondersteund.

- Uw Azure Key Vault, cluster en gekoppelde werk ruimten moeten zich in dezelfde regio en in dezelfde Azure Active Directory (Azure AD) Tenant bevinden, maar ze kunnen zich in verschillende abonnementen bevinden.

- Werkruimte koppeling naar cluster zal mislukken als deze is gekoppeld aan een ander cluster.

## <a name="troubleshooting"></a>Problemen oplossen

- Gedrag met Key Vault Beschik baarheid
  - In normale werking: opslag caches AEK gedurende korte tijd en terugvallen op Key Vault om regel matig de vertraging op te lossen.
    
  - Tijdelijke verbindings fouten--opslag verwerkt tijdelijke fouten (time-outs, verbindings fouten, DNS-problemen) doordat sleutels gedurende langere tijd in de cache blijven staan. Dit geeft een kleine problemen in Beschik baarheid. De mogelijkheden voor het uitvoeren van query's en opname worden zonder onderbreking voortgezet.
    
  - Live site--de niet-beschik baarheid van ongeveer 30 minuten leidt ertoe dat het opslag account niet meer beschikbaar is. De query mogelijkheid is niet beschikbaar en opgenomen gegevens worden gedurende enkele uren in de cache opgeslagen met behulp van micro soft-code om gegevens verlies te voor komen. Wanneer de toegang tot Key Vault wordt hersteld, wordt de query beschikbaar en worden de gegevens in de tijdelijke cache opgenomen in de gegevens opslag en versleuteld met Customer-Managed sleutel.

  - Toegangs snelheid van Key Vault: de frequentie waarmee Azure Monitor toegang tot Key Vault voor verpakte en onverpakte bewerkingen tussen 6 en 60 seconden ligt.

- Als u een cluster maakt en de KeyVaultProperties onmiddellijk opgeeft, kan de bewerking mislukken omdat het toegangs beleid niet kan worden gedefinieerd totdat de systeem identiteit is toegewezen aan het cluster.

- Als u een bestaand cluster bijwerkt met KeyVaultProperties en het sleutel toegangs beleid Get ontbreekt in Key Vault, mislukt de bewerking.

- Als er een conflict fout optreedt tijdens het maken van een cluster, is het mogelijk dat u uw cluster in de afgelopen 14 dagen hebt verwijderd en dat het een tijdelijke, verwijderings periode is. De cluster naam blijft gereserveerd tijdens de tijdelijke periode en u kunt geen nieuw cluster met die naam maken. De naam wordt vrijgegeven na de periode voor voorlopig verwijderen wanneer het cluster permanent wordt verwijderd.

- Als u het cluster bijwerkt terwijl er een bewerking wordt uitgevoerd, mislukt de bewerking.

- Als u het cluster niet implementeert, controleert u of uw Azure Key Vault-, cluster-en gekoppelde Log Analytics-werk ruimten zich in dezelfde regio bevinden. De kan zich in verschillende abonnementen bevindt.

- Als u de sleutel versie bijwerkt in Key Vault en de nieuwe sleutel-id-Details in het cluster niet bijwerkt, blijft de Log Analytics cluster uw vorige sleutel gebruiken en worden uw gegevens niet meer toegankelijk. Update de nieuwe sleutel-id in het cluster om gegevens opname en de mogelijkheid om gegevens op te vragen te hervatten.

- Sommige bewerkingen zijn lang en kunnen even duren: Dit zijn clusters maken, cluster sleutel updates en cluster verwijdering. U kunt de bewerkings status op twee manieren controleren:
  1. Wanneer u REST gebruikt, kopieert u de waarde van de Azure-AsyncOperation-URL uit het antwoord en volgt u de controle van de [asynchrone bewerkings status](#asynchronous-operations-and-status-check).
  2. Verzend aanvraag verzenden naar cluster of werk ruimte en Bekijk het antwoord. Niet-gekoppelde werk ruimte heeft bijvoorbeeld niet de *clusterResourceId* onder *functies*.

- [Dubbele versleuteling](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) wordt automatisch geconfigureerd voor clusters die zijn gemaakt van oktober 2020 als dubbele versleuteling in de regio is opgenomen. Als u een cluster maakt en er een fout melding krijgt met de naam ' <regio-name> ondersteunt geen dubbele versleuteling voor clusters. ' kunt u het cluster nog steeds maken, maar met dubbele versleuteling uitgeschakeld. Het kan niet worden in-of uitgeschakeld nadat het cluster is gemaakt. Als u een cluster wilt maken wanneer dubbele versleuteling niet wordt ondersteund in de regio, voegt u `"properties": {"isDoubleEncryptionEnabled": false}` in de rest-aanvraag tekst toe.

- Foutberichten
  
  Cluster maken:
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

  Cluster update
  -  400--de status van het cluster wordt verwijderd. De asynchrone bewerking wordt uitgevoerd. Het cluster moet de bewerking volt ooien voordat een update bewerking wordt uitgevoerd.
  -  400--KeyVaultProperties is niet leeg, maar heeft een ongeldige indeling. Zie [sleutel-id bijwerken](#update-cluster-with-key-identifier-details).
  -  400--kan de sleutel in Key Vault niet valideren. Kan worden veroorzaakt door onvoldoende machtigingen of wanneer de sleutel niet bestaat. Controleer of u het [sleutel-en toegangs beleid hebt ingesteld](#grant-key-vault-permissions) in Key Vault.
  -  400--sleutel kan niet worden hersteld. Key Vault moet worden ingesteld op zacht verwijderen en de beveiliging op te schonen. Raadpleeg de [documentatie van Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400--bewerking kan nu niet worden uitgevoerd. Wacht tot de asynchrone bewerking is voltooid en probeer het opnieuw.
  -  400--de status van het cluster wordt verwijderd. Wacht tot de asynchrone bewerking is voltooid en probeer het opnieuw.

  Cluster ophalen:
    -  404--het cluster is niet gevonden. mogelijk is het cluster verwijderd. Als u probeert een cluster met die naam te maken en conflicten op te halen, wordt het cluster gedurende 14 dagen voorlopig verwijderd. U kunt contact opnemen met de ondersteuning om het te herstellen, of een andere naam gebruiken om een nieuw cluster te maken. 

  Cluster verwijderen
    -  409--een cluster kan niet worden verwijderd tijdens de inrichtings status. Wacht tot de asynchrone bewerking is voltooid en probeer het opnieuw.

  Koppeling naar werk ruimte:
  -  404--werk ruimte is niet gevonden. De werk ruimte die u hebt opgegeven, bestaat niet of is verwijderd.
  -  409--werk ruimte koppeling of ontkoppelen bewerking in verwerking.
  -  400--het cluster is niet gevonden. het cluster dat u hebt opgegeven, bestaat niet of is verwijderd. Als u probeert een cluster met die naam te maken en conflicten op te halen, wordt het cluster gedurende 14 dagen voorlopig verwijderd. U kunt contact opnemen met de ondersteuning om deze te herstellen.

  Werk ruimte ontkoppelen:
  -  404--werk ruimte is niet gevonden. De werk ruimte die u hebt opgegeven, bestaat niet of is verwijderd.
  -  409--werk ruimte koppeling of ontkoppelen bewerking in verwerking.
