---
title: Azure Key Vault oplossing in Azure Monitor | Microsoft Docs
description: U kunt de Azure Key Vault oplossing in Azure Monitor gebruiken om Azure Key Vault logboeken te controleren.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 24869841a106a68234cfec600d9a98e519f8c6cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734193"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault Analytics-oplossing in Azure Monitor

> [!NOTE]
> Deze oplossing is afgeschaft. [We raden u aan om Azure monitor voor Key Vault te gebruiken](./key-vault-insights-overview.md).

![Key Vault-symbool](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

U kunt de Azure Key Vault-oplossing in Azure Monitor gebruiken om Azure Key Vault audit event-logboeken te controleren.

Als u de oplossing wilt gebruiken, moet u logboek registratie van Azure Key Vault diagnostische gegevens inschakelen en de diagnostische gegevens naar een Log Analytics-werk ruimte sturen. Het is niet nodig om de logboeken te schrijven naar Azure Blob-opslag.

> [!NOTE]
> In januari 2017 wordt de ondersteunde manier voor het verzenden van logboeken van Key Vault naar Log Analytics gewijzigd. Als de Key Vault oplossing die u gebruikt, wordt weer gegeven *(afgeschaft)* in de titel, raadpleegt u [migreren van de oude Key Vault oplossing](#migrating-from-the-old-key-vault-solution) voor de stappen die u moet volgen.
>
>

## <a name="install-and-configure-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende instructies voor het installeren en configureren van de Azure Key Vault oplossing:

1. Gebruik het proces beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](./solutions.md) om de Azure Key Vault oplossing toe te voegen aan uw log Analytics-werk ruimte.
2. Logboek registratie van diagnostische gegevens inschakelen voor de Key Vault resources die moeten worden bewaakt met behulp van de [Portal](#enable-key-vault-diagnostics-in-the-portal) of [Power shell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Diagnostische gegevens van Key Vault inschakelen in de portal

1. Ga in het Azure Portal naar de Key Vault resource die u wilt bewaken
2. *Instellingen voor diagnostische gegevens* selecteren om de volgende pagina te openen

   ![Scherm afbeelding van de pagina Diagnostische instellingen voor de Key Vault resource ContosoKVSCUS. de optie voor het inschakelen van diagnostische gegevens is gemarkeerd.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klik op *Diagnostische gegevens inschakelen* om de volgende pagina te openen

   ![Scherm afbeelding van de pagina voor het configureren van diagnostische instellingen. De opties voor Send to Log Analytics, audit event log en AllMetrics zijn geselecteerd.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Geef een naam op voor de diagnostische instelling.
5. Klik op het selectie vakje voor *verzenden naar log Analytics*
6. Een bestaande Log Analytics-werk ruimte selecteren of een werk ruimte maken
7. Als u *audit event* -logboeken wilt inschakelen, klikt u op het selectie vakje onder logboek
8. Klik op *Opslaan* om de logboek registratie van diagnostische gegevens in log Analytics werk ruimte in te scha kelen.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Diagnostische gegevens van Key Vault inschakelen met behulp van Power shell
Het volgende Power shell-script bevat een voor beeld van het gebruik van het `Set-AzDiagnosticSetting` inschakelen van bron logboek registratie voor Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Details van Azure Key Vault verzamelen van gegevens controleren
Azure Key Vault oplossing verzamelt Diagnostische logboeken rechtstreeks vanuit de Key Vault.
Het is niet nodig om de logboeken te schrijven naar Azure Blob-opslag en er is geen agent vereist voor het verzamelen van gegevens.

De volgende tabel toont methoden voor gegevens verzameling en andere informatie over hoe gegevens worden verzameld voor Azure Key Vault.

| Platform | Directe agent | Operations Manager agent voor Systems Center | Azure | Operations Manager vereist? | Operations Manager agent gegevens die via een beheer groep zijn verzonden | Verzamelingsfrequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | bij aankomst |

## <a name="use-azure-key-vault"></a>Azure Key Vault gebruiken
Nadat u [de oplossing hebt geïnstalleerd](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?source=intercept.nl&tab=Overview), bekijkt u de Key Vault gegevens door te klikken op de tegel **Key Vault-analyse** van de Azure monitor **overzichts** pagina. Open deze pagina in het menu **Azure monitor** door te klikken op **meer** onder het gedeelte **inzichten** . 

![Scherm afbeelding van de Key Vault-analyse tegel op de pagina overzicht van Azure Monitor met een grafiek van het volume van de sleutel kluis in de loop van de tijd.](media/azure-key-vault/log-analytics-keyvault-tile.png)

Nadat u op de tegel **Key Vault-analyse** hebt geklikt, kunt u samen vattingen van uw logboeken weer geven en vervolgens inzoomen op Details voor de volgende categorieën:

* Volume van alle sleutel kluis bewerkingen gedurende een periode
* Mislukte bewerkings volumes na verloop van tijd
* Gemiddelde operationele latentie per bewerking
* Quality of service voor bewerkingen met het aantal bewerkingen dat langer is dan 1000 MS en een lijst met bewerkingen die meer dan 1000 MS duren

![Scherm afbeelding van het Azure Key Vault dash board met tegels met grafische gegevens voor alle bewerkingen, mislukte bewerkingen en gemiddelde operationele latentie.](media/azure-key-vault/log-analytics-keyvault01.png)

![Scherm afbeelding van het Azure Key Vault dash board met tegels met gegevens voor gemiddelde operationele latentie, Quality of Service en aanbevolen Zoek opdrachten.](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Details voor elke bewerking weer geven
1. Klik op de pagina **overzicht** op de tegel **Key Vault-analyse** .
2. Controleer op het **Azure Key Vault** -dash board de samenvattings informatie op een van de Blades en klik vervolgens op een van de pagina's om gedetailleerde informatie weer te geven op de pagina zoeken in Logboeken.

    Op een van de zoek pagina's in Logboeken kunt u de resultaten weer geven op tijd, gedetailleerde resultaten en de Zoek geschiedenis van het logboek. U kunt ook filteren op facetten om de resultaten te beperken.

## <a name="azure-monitor-log-records"></a>Azure Monitor logboek records
De Azure Key Vault oplossing analyseert records die een type sleutel **kluizen** hebben die worden verzameld uit audit event- [logboeken](../../key-vault/general/logging.md) in azure Diagnostics.  De eigenschappen voor deze records bevinden zich in de volgende tabel:  

| Eigenschap | Beschrijving |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |IP-adres van de client die de aanvraag heeft ingediend |
| `Category` | *AuditEvent* |
| `CorrelationId` |Een optionele GUID die de client kan doorgeven om de logboeken aan de clientzijde te relateren aan (Sleutelkluis-)logboeken aan de servicezijde. |
| `DurationMs` |De tijd die nodig was om de REST-API-aanvraag af te handelen in milliseconden. Deze tijd omvat geen netwerk latentie, waardoor de tijd die u aan de client zijde meet mogelijk niet overeenkomt met deze tijd. |
| `httpStatusCode_d` |De HTTP-status code die wordt geretourneerd door de aanvraag (bijvoorbeeld *200*) |
| `id_s` |Unieke ID van de aanvraag |
| `identity_claim_appid_g` | GUID voor de toepassings-ID |
| `OperationName` |De naam van de bewerking, zoals beschreven in [Azure Key Vault logboek registratie](../../key-vault/general/logging.md) |
| `OperationVersion` |REST API versie die door de client is aangevraagd (bijvoorbeeld *2015-06-01*) |
| `requestUri_s` |URI van de aanvraag |
| `Resource` |De naam van de sleutel kluis |
| `ResourceGroup` |Resource groep van de sleutel kluis |
| `ResourceId` |Azure Resource Manager-resource-id. Voor Key Vault-Logboeken is dit de Key Vault Resource-ID. |
| `ResourceProvider` |*Micro soft. KEYVAULT* |
| `ResourceType` | *KLUIZEN* |
| `ResultSignature` |HTTP-status (bijvoorbeeld *OK*) |
| `ResultType` |Resultaat van REST API aanvraag (bijvoorbeeld *geslaagd*) |
| `SubscriptionId` |Azure-abonnements-ID van het abonnement met de Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migreren van de oude Key Vault-oplossing
In januari 2017 wordt de ondersteunde manier voor het verzenden van logboeken van Key Vault naar Log Analytics gewijzigd. Deze wijzigingen bieden de volgende voor delen:
+ Logboeken worden rechtstreeks naar een Log Analytics-werk ruimte geschreven zonder dat u een opslag account hoeft te gebruiken
+ Minder latentie vanaf het moment dat Logboeken worden gegenereerd, zodat ze beschikbaar zijn in Log Analytics
+ Minder configuratie stappen
+ Een algemene indeling voor alle typen Azure Diagnostics

De bijgewerkte oplossing gebruiken:

1. [Configureren dat diagnostische gegevens rechtstreeks naar een Log Analytics-werk ruimte worden verzonden vanuit Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Schakel de Azure Key Vault-oplossing in met behulp van het proces dat wordt beschreven in [Azure monitor oplossingen toevoegen van de Oplossingengalerie](./solutions.md)
3. Alle opgeslagen query's, Dash boards of waarschuwingen bijwerken voor gebruik van het nieuwe gegevens type
   + Type is gewijzigd van: AzureDiagnostics. U kunt het resource type gebruiken om te filteren op Logboeken Key Vault.
   + Gebruik in plaats van: `KeyVaults``AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Velden: (veld namen zijn hoofdletter gevoelig)
   + Voor elk veld met een achtervoegsel van \_ s, \_ d of \_ g in de naam, wijzigt u het eerste teken in kleine letters
   + Voor een veld met het achtervoegsel \_ o in naam, worden de gegevens gesplitst in afzonderlijke velden op basis van de geneste veld namen. De UPN van de oproepende functie wordt bijvoorbeeld opgeslagen in een veld `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Veld CallerIpAddress gewijzigd in CallerIPAddress
   + Het veld RemoteIPCountry is niet meer aanwezig
4. Verwijder de oplossing *Key Vault-analyse (afgeschaft)* . Als u Power shell gebruikt, gebruikt u `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Gegevens die vóór de wijziging zijn verzameld, zijn niet zichtbaar in de nieuwe oplossing. U kunt door gaan met het zoeken naar deze gegevens met behulp van het oude type en de veld namen.

## <a name="troubleshooting"></a>Problemen oplossen
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Volgende stappen
* Gebruik [logboek query's in azure monitor](../logs/log-query-overview.md) om gedetailleerde Azure Key Vault gegevens weer te geven.

