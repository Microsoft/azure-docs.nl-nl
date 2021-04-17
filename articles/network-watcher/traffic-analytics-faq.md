---
title: Veelgestelde vragen over Azure Traffic Analytics | Microsoft Docs
description: Krijg antwoorden op een aantal van de meest gestelde vragen over Traffic Analytics.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: 98c0a6f88da717256e78a748902317a90a369a9c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533635"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Traffic Analytics veelgestelde vragen

In dit artikel worden veel veelgestelde vragen over verkeersanalyses in Azure Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Wat zijn de vereisten voor het gebruik van Traffic Analytics?

Traffic Analytics vereist de volgende vereisten:

- Een Network Watcher ingeschakeld.
- Stroomlogboeken van netwerkbeveiligingsgroep (NSG) ingeschakeld voor de NSG's die u wilt bewaken.
- Een Azure Storage voor het opslaan van onbewerkte stroomlogboeken.
- Een Azure Log Analytics-werkruimte met lees- en schrijftoegang.

Uw account moet voldoen aan een van de volgende opties om Traffic Analytics in te kunnenschakelen:

- Uw account moet een van de volgende Azure-rollen hebben voor het abonnementsbereik: eigenaar, inzender, lezer of netwerkbijdrager.
- Als uw account niet is toegewezen aan een van de eerder vermelde rollen, moet het worden toegewezen aan een aangepaste rol aan wie de volgende acties zijn toegewezen, op abonnementsniveau.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Rollen controleren die zijn toegewezen aan een gebruiker voor een abonnement:

1. Meld u aan bij Azure met **behulp van Login-AzAccount**. 

2. Selecteer het vereiste abonnement met **behulp van Select-AzSubscription.** 

3. Gebruik  **Get-AzRoleAssignment -SignInName [gebruikerse-mail] - IncludeClassicAdministrators** om alle rollen weer te bieden die zijn toegewezen aan een opgegeven gebruiker. 

Als u geen uitvoer ziet, neem dan contact op met de betreffende abonnementsbeheerder om toegang te krijgen om de opdrachten uit te voeren. Zie Azure-roltoewijzingen [toevoegen of verwijderen met behulp van](../role-based-access-control/role-assignments-powershell.md)Azure PowerShell.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Kunnen de NSG's waarin ik stroomlogboeken inschakelen zich in andere regio's dan mijn werkruimte?

Ja, deze NSG's kunnen zich in andere regio's dan uw Log Analytics-werkruimte.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Kunnen er meerdere NSG's worden geconfigureerd binnen één werkruimte?

Ja.

## <a name="can-i-use-an-existing-workspace"></a>Kan ik een bestaande werkruimte gebruiken?

Ja. Als u een bestaande werkruimte selecteert, moet u ervoor zorgen dat deze is gemigreerd naar de nieuwe querytaal. Als u de werkruimte niet wilt upgraden, moet u een nieuwe maken. Zie voor meer informatie over de nieuwe querytaal [Azure Monitor logboeken upgraden naar nieuwe zoeken in logboeken.](../azure-monitor/logs/log-query-overview.md)

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Kan mijn Azure Storage-account zich in één abonnement en mijn Log Analytics-werkruimte in een ander abonnement?

Ja, uw Azure Storage-account kan zich in één abonnement en uw Log Analytics-werkruimte kan zich in een ander abonnement.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Kan ik onbewerkte logboeken opslaan in een ander abonnement?

Ja. U kunt configureren dat NSG-stroomlogboeken worden verzonden naar een opslagaccount dat zich in een ander abonnement bevindt, mits u over de juiste bevoegdheden beschikt en het opslagaccount zich in dezelfde regio bevindt als de NSG. De NSG en het doelopslagaccount moeten ook dezelfde Azure Active Directory tenant.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Wat gebeurt er als ik een NSG niet kan configureren voor traffic analytics vanwege een fout 'Niet gevonden'?

Selecteer een ondersteunde regio. Als u een niet-ondersteunde regio selecteert, wordt de fout Niet gevonden weergegeven. De ondersteunde regio's worden eerder in dit artikel vermeld.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Wat gebeurt er als ik de status 'Kan niet laden' krijg op de pagina met NSG-stroomlogboeken?

De Microsoft.Insights-provider moet zijn geregistreerd om stroomlogregistratie goed te laten werken. Als u niet zeker weet of de Microsoft.Insights-provider is geregistreerd voor uw abonnement, vervangt u *xxxxx-xxxxx-xxxxxx-xxxx* in de volgende opdracht en voert u de volgende opdrachten uit vanuit PowerShell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Ik heb de oplossing geconfigureerd. Waarom zie ik niets op het dashboard?

Het kan tot 30 minuten duren voordat het dashboard voor het eerst wordt weergegeven. De oplossing moet eerst voldoende gegevens aggregeren om zinvolle inzichten af te leiden. Vervolgens worden er rapporten gegenereerd. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Wat gebeurt er als ik dit bericht krijg: "We kunnen geen gegevens vinden in deze werkruimte voor het geselecteerde tijdsinterval. Probeer het tijdsinterval te wijzigen of selecteer een andere werkruimte.'

Probeer de volgende opties:
- Wijzig het tijdsinterval in de bovenste balk.
- Selecteer een andere Log Analytics-werkruimte in de bovenste balk.
- Probeer na 30 minuten toegang te krijgen tot Traffic Analytics, als deze functie onlangs is ingeschakeld.
    
Als er problemen blijven bestaan, kunt u zorgen maken in het [User Voice-forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Wat gebeurt er als ik het volgende bericht krijg: Uw NSG-stroomlogboeken voor de eerste keer analyseren. Dit proces kan 20-30 minuten duren. Ga na enige tijd terug. 2) Als de bovenstaande stap niet werkt en uw werkruimte onder de gratis SKU valt, controleert u hier het gebruik van uw werkruimte om het quotum te valideren. Raadpleeg dan Veelgestelde vragen voor meer informatie.?

Mogelijk ziet u dit bericht omdat:
- Traffic Analytics is onlangs ingeschakeld en heeft mogelijk nog niet voldoende gegevens geaggregeerd om zinvolle inzichten af te leiden.
- U gebruikt de gratis versie van de Log Analytics-werkruimte en de quotumlimieten zijn overschreden. Mogelijk moet u een werkruimte met een grotere capaciteit gebruiken.
    
Als de problemen zich blijven voordoen, kunt u zorgen maken in het [User Voice-forum](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Wat gebeurt er als ik dit bericht krijg: 'Het lijkt erop dat we resourcesgegevens (topologie) hebben en geen stromen. Klik in de tussentijd hier om de gegevens van resources te bekijken en raadpleeg veelgestelde vragen voor meer informatie.'

U ziet de informatie over resources op het dashboard; Er zijn echter geen stroomgerelateerde statistieken aanwezig. Gegevens zijn mogelijk niet aanwezig vanwege geen communicatiestromen tussen de resources. Wacht 60 minuten en controleer de status opnieuw. Als het probleem zich blijft voordoen en u zeker weet dat er communicatiestromen tussen resources bestaan, kunt u zorgen maken in het [User Voice-forum.](https://feedback.azure.com/forums/217313-networking?category_id=195844)

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Kan ik verkeersanalyse configureren met behulp van PowerShell of een Azure Resource Manager-sjabloon of -client?

U kunt Traffic Analytics configureren met behulp Windows PowerShell versie 6.2.1 en meer. Zie [Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog)als u logboekregistratie van stromen en verkeersanalyses voor een specifieke NSG wilt configureren met behulp van de cmdlet Set. Zie [Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus)voor informatie over de status van stroomlogregistratie en verkeersanalyse voor een specifieke NSG.

Op dit moment kunt u geen sjabloon voor Azure Resource Manager gebruiken om verkeersanalyses te configureren.

Zie de volgende voorbeelden voor het configureren van traffic analytics Azure Resource Manager een client voor een netwerkanalyse.

**Voorbeeld van cmdlet instellen:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Voorbeeld van cmdlet:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Wat is Traffic Analytics prijs?

Traffic Analytics wordt gemeten. De meting is gebaseerd op de verwerking van stroomlogboekgegevens door de service en het opslaan van de resulterende verbeterde logboeken in een Log Analytics-werkruimte. 

Bijvoorbeeld, volgens het [](https://azure.microsoft.com/pricing/details/network-watcher/)prijsplan , gezien de regio VS - west-centraal, zijn de toepasselijke kosten: 10 x 2,3$ + 1 x 2,76$ = 25,76$ als gegevens van stroomlogboeken die zijn opgeslagen in een opslagaccount dat is verwerkt door Traffic Analytics 10 GB is en uitgebreide logboeken die zijn opgenomen in de Log Analytics-werkruimte, 1 GB zijn: 10 x 2,3$ + 1 x 2,76$ = 25,76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Hoe vaak worden Traffic Analytics verwerkt?

Raadpleeg de sectie [gegevensaggregatie](./traffic-analytics-schema.md#data-aggregation) in Traffic Analytics Document schema en gegevensaggregatie

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hoe bepaalt Traffic Analytics dat een IP-adres schadelijk is? 

Traffic Analytics is afhankelijk van interne bedreigingsinformatiesystemen van Microsoft om een IP-adres als schadelijk te achten. Deze systemen maken gebruik van diverse telemetriebronnen, zoals Microsoft-producten en -services, de Microsoft Digital Crimes Unit (DCU), de Microsoft Security Response Center (MSRC) en externe feeds en bouwen er een groot aantal intelligentie op. Sommige van deze gegevens zijn Intern van Microsoft. Als een bekend IP-adres wordt gemarkeerd als kwaadwillend, kunt u een ondersteuningsticket maken om de details te weten.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hoe kan ik waarschuwingen instellen voor Traffic Analytics gegevens?

Traffic Analytics geen ingebouwde ondersteuning voor waarschuwingen. Omdat er echter Traffic Analytics gegevens worden opgeslagen in Log Analytics, kunt u aangepaste query's schrijven en er waarschuwingen voor instellen. Stappen:
- U kunt de shortlink voor Log Analytics in Traffic Analytics. 
- Gebruik het [schema dat hier wordt beschreven](traffic-analytics-schema.md) om uw query's te schrijven 
- Klik op Nieuwe waarschuwingsregel om de waarschuwing te maken
- Raadpleeg de documentatie [voor logboekwaarschuwingen](../azure-monitor/alerts/alerts-log.md) om de waarschuwing te maken

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Hoe kan ik controleren welke VM's het meeste on-premises verkeer ontvangen?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
| render timechart
```

  Voor IP's:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
| render timechart
```

Gebruik voor tijd de notatie : yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Hoe kan ik standaardafwijking in het verkeer controleren dat door mijn VM's van on-premises machines wordt ontvangen?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

Voor IP's:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Hoe kan ik controleren welke poorten bereikbaar (of geblokkeerd) zijn tussen IP-paren met NSG-regels?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
| extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
| mvexpand SourceIp = sourceIPs to typeof(string)
| mvexpand DestIp = destIPs to typeof(string)
| project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
| summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s
```

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hoe kan ik navigeren met behulp van het toetsenbord in de geokaartweergave?

De geokaartpagina bevat twee hoofdsecties:
    
- **Banner:** de banner boven aan de geokaart bevat knoppen om filters voor verkeersdistributie te selecteren (bijvoorbeeld Implementatie, Verkeer van landen/regio's en Schadelijk). Wanneer u een knop selecteert, wordt het desbetreffende filter toegepast op de kaart. Als u bijvoorbeeld de knop Actief selecteert, worden op de kaart de actieve datacenters in uw implementatie aangegeven.
- **Kaart:** Onder de banner ziet u in de kaartsectie verkeersdistributie tussen Azure-datacenters en landen/regio's.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbordnavigatie in de banner
    
- Standaard is de selectie op de geokaartpagina voor de banner het filter 'Azure DC's'.
- Als u naar een ander filter wilt gaan, gebruikt u `Tab` de - of `Right arrow` -sleutel. Als u terug wilt gaan, gebruikt u `Shift+Tab` de - of `Left arrow` -sleutel. Navigatie doorsturen is van links naar rechts, gevolgd door van boven naar beneden.
- Druk `Enter` op of de `Down` pijltoets om het geselecteerde filter toe te passen. Op basis van filterselectie en -implementatie zijn een of meer knooppunten onder de kaartsectie gemarkeerd.
- Als u wilt schakelen tussen banner en kaart, drukt u op `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-map"></a>Toetsenbordnavigatie op de kaart
    
- Nadat u een filter in de banner hebt geselecteerd en op hebt gedrukt, wordt de focus verplaatst naar een van de gemarkeerde knooppunten `Ctrl+F6` **(Azure-datacenter** of **Land/regio)** in de kaartweergave.
- Als u wilt verplaatsen naar andere gemarkeerde knooppunten op de kaart, gebruikt u `Tab` of de sleutel voor `Right arrow` voorwaartse verplaatsing. Gebruik `Shift+Tab` of de sleutel voor `Left arrow` achterwaartse verplaatsing.
- Als u een gemarkeerd knooppunt op de kaart wilt selecteren, gebruikt u de `Enter` sleutel of `Down arrow` .
- Bij selectie van dergelijke knooppunten gaat de focus naar de **Informatiehulpprogrammavak** voor het knooppunt. Standaard wordt de focus verplaatst naar de gesloten knop in de **Information Tool Box**. Als u verder  wilt gaan in de Box-weergave, gebruikt u de sleutels en om respectievelijk vooruit `Right arrow` en achteruit te `Left arrow` gaan. Drukken `Enter` heeft hetzelfde effect als het selecteren van de gerichte knop in de **informatiehulpprogrammavak.**
- Wanneer u op drukt terwijl de focus op de Information Tool Box is, wordt de focus verplaatst naar de eindpunten op hetzelfde `Tab` continent als het geselecteerde knooppunt.  Gebruik de `Right arrow` sleutels en om door deze `Left arrow` eindpunten te gaan.
- Als u wilt overstromen naar andere stroom-eindpunten of continentclusters, gebruikt u voor `Tab` voorwaartse verplaatsing en `Shift+Tab` voor achterwaartse verplaatsing.
- Wanneer de focus op **continentclusters ligt,** gebruikt u de pijltoetsen of om de eindpunten in het `Enter` `Down` continentcluster te markeren. Als u door eindpunten en de knop Sluiten op het informatievak van het continentcluster wilt gaan, gebruikt u respectievelijk de sleutel of voor voorwaartse en `Right arrow` `Left arrow` achterwaartse verplaatsing. Op elk eindpunt kunt u gebruiken om van het geselecteerde knooppunt naar het eindpunt over te schakelen `Shift+L` naar de verbindingsregel. U kunt opnieuw `Shift+L` op drukken om naar het geselecteerde eindpunt te gaan.
        
### <a name="keyboard-navigation-at-any-stage"></a>Toetsenbordnavigatie in elke fase
    
- `Esc` vouwt de uitgevouwen selectie samen.
- Met `Up-arrow` de -sleutel wordt dezelfde actie uitgevoerd als `Esc` . Met `Down arrow` de sleutel wordt dezelfde actie uitgevoerd als `Enter` .
- Gebruik `Shift+Plus` om in te zoomen en uit te `Shift+Minus` zoomen.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hoe kan ik navigeren met behulp van het toetsenbord in de topologieweergave van het virtuele netwerk?

De topologiepagina van virtuele netwerken bevat twee hoofdsecties:
    
- **Banner:** de banner boven aan de topologie van virtuele netwerken bevat knoppen voor het selecteren van filters voor verkeersdistributie (bijvoorbeeld Verbonden virtuele netwerken, Niet-verbonden virtuele netwerken en Openbare IP's). Wanneer u een knop selecteert, wordt het desbetreffende filter toegepast op de topologie. Als u bijvoorbeeld de knop Actief selecteert, markeert de topologie de actieve virtuele netwerken in uw implementatie.
- **Topologie:** onder de banner wordt in de sectie topologie de verkeersdistributie tussen virtuele netwerken weergegeven.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbordnavigatie in de banner
    
- De selectie op de topologiepagina van virtuele netwerken voor de banner is standaard het filter Verbonden VNets.
- Als u naar een ander filter wilt gaan, gebruikt `Tab` u de -sleutel om verder te gaan. Gebruik de sleutel om terug te `Shift+Tab` gaan. Navigatie doorsturen is van links naar rechts, gevolgd door van boven naar beneden.
- Druk `Enter` op om het geselecteerde filter toe te passen. Op basis van de filterselectie en -implementatie zijn een of meer knooppunten (virtueel netwerk) onder de sectie topologie gemarkeerd.
- Als u wilt schakelen tussen de banner en de topologie, drukt u op `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Toetsenbordnavigatie op de topologie
    
- Nadat u een filter in de banner hebt geselecteerd en op hebt gedrukt, wordt de focus verplaatst naar een van de gemarkeerde knooppunten `Ctrl+F6` **(VNet)** in de topologieweergave.
- Als u naar andere gemarkeerde knooppunten in de topologieweergave wilt gaan, gebruikt u de `Shift+Right arrow` -sleutel voor voorwaartse verplaatsing. 
- Op gemarkeerde knooppunten wordt de focus verplaatst naar de **Informatiehulpprogrammavak** voor het knooppunt. Standaard gaat de focus naar de **knop Meer details** in de Information Tool **Box.** Als u verder wilt gaan in **de Box-weergave,** gebruikt u respectievelijk de toetsen en om vooruit en achteruit te `Right arrow` `Left arrow` gaan. Drukken `Enter` heeft hetzelfde effect als het selecteren van de gerichte knop in de **informatiehulpprogrammavak.**
- Bij selectie van dergelijke knooppunten kunt u alle verbindingen ervan een voor een bezoeken door op de toets te `Shift+Left arrow` drukken. De focus wordt verplaatst naar **de Information Tool Box** van die verbinding. Op elk moment kan de focus naar het knooppunt worden verschoven door opnieuw op te `Shift+Right arrow` drukken.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hoe kan ik navigeren met behulp van het toetsenbord in de subnettopologieweergave?

De pagina met de virtuele subnetworks-topologie bevat twee hoofdsecties:
    
- **Banner:** de banner boven aan de topologie van virtuele subnetten bevat knoppen voor het selecteren van distributiefilters voor verkeer (bijvoorbeeld actieve, middelgrote en gatewaysubnetten). Wanneer u een knop selecteert, wordt het desbetreffende filter toegepast op de topologie. Als u bijvoorbeeld de knop Actief selecteert, markeert de topologie het actieve virtuele subnetwork in uw implementatie.
- **Topologie:** onder de banner toont de sectie topologie verkeersdistributie tussen virtuele subnetten.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbordnavigatie in de banner
    
- Standaard is de selectie op de pagina van de virtuele subnetworks-topologie voor de banner het filter Subnetten.
- Als u naar een ander filter wilt gaan, gebruikt `Tab` u de -sleutel om verder te gaan. Gebruik de sleutel om terug te `Shift+Tab` gaan. Navigatie doorsturen is van links naar rechts, gevolgd door van boven naar beneden.
- Druk `Enter` op om het geselecteerde filter toe te passen. Op basis van filterselectie en -implementatie zijn een of meer knooppunten (Subnet) onder de sectie topologie gemarkeerd.
- Als u wilt schakelen tussen de banner en de topologie, drukt u op `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Toetsenbordnavigatie op de topologie
    
- Nadat u een filter in de banner hebt geselecteerd en op hebt gedrukt, wordt de focus verplaatst naar een van de gemarkeerde knooppunten `Ctrl+F6` (**Subnet**) in de topologieweergave.
- Als u naar andere gemarkeerde knooppunten in de topologieweergave wilt gaan, gebruikt u de `Shift+Right arrow` -sleutel voor voorwaartse verplaatsing. 
- Op gemarkeerde knooppunten wordt de focus verplaatst naar de **Informatiehulpprogrammavak** voor het knooppunt. Standaard wordt de focus verplaatst naar de **knop Meer details** in de Information Tool **Box.** Als u verder  wilt gaan in de Box-weergave, gebruikt u de sleutels en om respectievelijk vooruit en achteruit `Right arrow` te `Left arrow` gaan. Drukken `Enter` heeft hetzelfde effect als het selecteren van de gerichte knop in de Information Tool **Box**.
- Bij selectie van dergelijke knooppunten kunt u alle verbindingen ervan een voor een bezoeken door op de toets te `Shift+Left arrow` drukken. De focus wordt verplaatst naar **de Information Tool Box** van die verbinding. De focus kan op elk moment weer naar het knooppunt worden verplaatst door opnieuw te `Shift+Right arrow` drukken.

## <a name="are-classic-nsgs-supported"></a>Worden klassieke NSG's ondersteund?
Nee, Traffic Analytics geen ondersteuning voor klassieke NSG. Het wordt aanbevolen om IaaS-resources te migreren van klassieke naar Azure Resource Manager klassieke resources [worden afgeschaft.](../virtual-machines/classic-vm-deprecation.md) Raadpleeg dit artikel om te begrijpen [hoe u migreert.](../virtual-machines/migration-classic-resource-manager-overview.md)