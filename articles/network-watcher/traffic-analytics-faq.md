---
title: Veelgestelde vragen over Azure Traffic Analytics | Microsoft Docs
description: Krijg antwoorden op enkele veelgestelde vragen over Traffic Analytics.
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
ms.openlocfilehash: f4d97b81bf8f11b8380f04dcbfdb72bd658805ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579245"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Veelgestelde vragen over Traffic Analytics

In dit artikel wordt één plek veel van de meest gestelde vragen over Traffic Analytics in azure Network Watcher verzameld.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Wat zijn de vereisten voor het gebruik van Traffic Analytics?

Traffic Analytics vereist de volgende vereisten:

- Een Network Watcher ingeschakeld abonnement.
- De stroom logboeken voor netwerk beveiligings groepen (NSG) zijn ingeschakeld voor de Nsg's die u wilt bewaken.
- Een Azure Storage-account om onbewerkte stroom logboeken op te slaan.
- Een Azure Log Analytics-werk ruimte, met lees-en schrijf toegang.

Uw account moet voldoen aan een van de volgende opties om Traffic Analytics in te scha kelen:

- Uw account moet een van de volgende Azure-rollen hebben op het abonnements bereik: eigenaar, bijdrager, lezer of netwerk bijdrager.
- Als uw account niet is toegewezen aan een van de eerder vermelde rollen, moet dit worden toegewezen aan een aangepaste rol waaraan de volgende acties zijn toegewezen op abonnements niveau.
            
    - Micro soft. Network/applicationGateways/lezen
    - Micro soft. netwerk/verbindingen/lezen
    - Micro soft. Network/loadBalancers/lezen 
    - Micro soft. Network/localNetworkGateways/lezen 
    - Micro soft. Network/networkInterfaces/lezen 
    - Micro soft. Network/networkSecurityGroups/lezen 
    - Microsoft.Network/publicIPAddresses/read
    - Micro soft. Network/routeTables/lezen
    - Micro soft. Network/virtualNetworkGateways/lezen 
    - Micro soft. Network/virtualNetworks/lezen
        
Rollen controleren die aan een gebruiker zijn toegewezen voor een abonnement:

1. Meld u aan bij Azure met behulp van **login-AzAccount**. 

2. Selecteer het vereiste abonnement met **Select-AzSubscription**. 

3. Gebruik  **Get-AzRoleAssignment-SignInName [e-mail adres van gebruiker]-IncludeClassicAdministrators** om alle rollen weer te geven die aan een opgegeven gebruiker zijn toegewezen. 

Als er geen uitvoer wordt weer gegeven, neemt u contact op met de beheerder van de desbetreffende abonnementen om de opdrachten uit te voeren. Zie [Azure-roltoewijzingen toevoegen of verwijderen met Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)voor meer informatie.


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>In welke Azure-regio's is Traffic Analytics beschikbaar?

U kunt Traffic Analytics voor Nsg's gebruiken in een van de volgende ondersteunde regio's:
- Canada - midden
- VS - west-centraal
- VS - oost
- VS - oost 2
- VS - noord-centraal
- VS - zuid-centraal
- Central US
- VS - west
- VS - west 2
- Frankrijk - centraal
- Europa -west
- Europa - noord
- Brazilië - zuid
- Verenigd Koninkrijk West
- Verenigd Koninkrijk Zuid
- Australië - oost
- Australië - zuidoost 
- Azië - oost
- Azië - zuidoost
- Korea - centraal
- India - centraal
- India - zuid
- Japan - oost
- Japan - west
- VS (overheid) - Virginia
- China - oost 2

De Log Analytics-werk ruimte moet in de volgende regio's bestaan:
- Canada - midden
- VS - west-centraal
- VS - oost
- VS - oost 2
- VS - noord-centraal
- VS - zuid-centraal
- Central US
- VS - west
- VS - west 2
- Frankrijk - centraal
- Europa -west
- Europa - noord
- Verenigd Koninkrijk West
- Verenigd Koninkrijk Zuid
- Australië - oost
- Australië - zuidoost
- Azië - oost
- Azië - zuidoost 
- Korea - centraal
- India - centraal
- Japan - oost
- VS (overheid) - Virginia
- China - oost 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Kan de Nsg's ik stroom logboeken inschakelen voor andere regio's dan mijn werk ruimte?

Ja, deze Nsg's kunnen zich in verschillende regio's bevinden dan uw Log Analytics-werk ruimte.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Kunnen meerdere Nsg's in één werk ruimte worden geconfigureerd?

Ja.

## <a name="can-i-use-an-existing-workspace"></a>Kan ik een bestaande werk ruimte gebruiken?

Ja. Als u een bestaande werk ruimte selecteert, moet u ervoor zorgen dat deze is gemigreerd naar de nieuwe query taal. Als u de werk ruimte niet wilt bijwerken, moet u een nieuwe maken. Zie [Azure monitor-logboeken upgraden naar nieuwe zoek opdracht in Logboeken](../azure-monitor/logs/log-query-overview.md)voor meer informatie over de nieuwe query taal.

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Kan mijn Azure Storage-account zich in één abonnement bevinden en mijn Log Analytics werk ruimte bevindt zich in een ander abonnement?

Ja, uw Azure Storage-account kan zich in één abonnement bevinden en uw Log Analytics-werk ruimte kan zich in een ander abonnement bevinden.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Kan ik onbewerkte logboeken opslaan in een ander abonnement?

Ja. U kunt NSG-stroom logboeken configureren om te worden verzonden naar een opslag account in een ander abonnement, mits u de juiste bevoegdheden hebt, en dat het opslag account zich in dezelfde regio bevindt als de NSG. De NSG en het doel-opslag account moeten ook dezelfde Azure Active Directory Tenant delen.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Wat moet ik doen als ik een NSG voor Traffic Analytics niet kan configureren vanwege een fout "niet gevonden"?

Selecteer een ondersteunde regio. Als u een niet-ondersteunde regio selecteert, wordt het fout bericht ' niet gevonden ' weer gegeven. De ondersteunde regio's worden eerder in dit artikel vermeld.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Wat moet ik doen als ik de status ontvang: ' kan niet worden geladen ' op de pagina NSG-stroom logboeken?

De micro soft. Insights-provider moet zijn geregistreerd voor een goede werking van de flow-logboek registratie. Als u niet zeker weet of de provider van micro soft. Insights is geregistreerd voor uw abonnement, vervangt u *xxxxx-xxxxx-xxxxxx-xxxx* door de volgende opdracht en voert u de volgende opdrachten uit in Power shell:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Ik heb de oplossing geconfigureerd. Waarom zie ik niets in het dash board?

Het kan tot 30 minuten duren voordat het dash board de eerste keer wordt weer gegeven. De oplossing moet eerst voldoende gegevens verzamelen om zinvolle inzichten te kunnen afleiden. Vervolgens worden er rapporten gegenereerd. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Wat gebeurt er als ik dit bericht krijg: ' er zijn geen gegevens in deze werk ruimte gevonden voor het geselecteerde tijds interval. Wijzig het tijds interval of selecteer een andere werk ruimte. "?

Probeer de volgende opties:
- Wijzig het tijds interval in de bovenste balk.
- Selecteer een andere Log Analytics werk ruimte in de bovenste balk.
- Probeer na 30 minuten toegang te krijgen tot Traffic Analytics, als deze onlangs is ingeschakeld.
    
Als er problemen blijven optreden, moet u zorgen voor het forum van de [gebruiker](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Wat gebeurt er als ik dit bericht krijg: ' de NSG-stroom logboeken voor de eerste keer analyseren. Dit proces kan 20-30 minuten duren. Probeer het na enige tijd opnieuw. 2) als de bovenstaande stap niet werkt en uw werk ruimte zich onder de gratis SKU bevindt, controleert u het gebruik van uw werk ruimte hier om het quotum te valideren. Raadpleeg anders de veelgestelde vragen voor meer informatie. "?

Dit bericht kan worden weer gegeven omdat:
- Traffic Analytics onlangs is ingeschakeld en er zijn mogelijk nog voldoende gegevens verzameld om zinvolle inzichten te verkrijgen.
- U gebruikt de gratis versie van de Log Analytics-werk ruimte en de quotum limieten zijn overschreden. Mogelijk moet u een werk ruimte met een grotere capaciteit gebruiken.
    
Als er problemen blijven optreden, moet u zorgen voor het forum van de [gebruiker](https://feedback.azure.com/forums/217313-networking?category_id=195844).
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Wat gebeurt er als ik dit bericht krijg: "er zijn resource gegevens (topologie) en er zijn geen gegevens over stromen. Ondertussen klikt u hier om resource gegevens weer te geven en raadpleegt u veelgestelde vragen voor meer informatie. "?

U ziet de informatie over de resources op het dash board. Er zijn echter geen gegevens met betrekking tot de stroom aanwezig. Gegevens zijn mogelijk niet aanwezig vanwege geen communicatie stromen tussen de resources. Wacht 60 minuten en controleer de status opnieuw. Als het probleem zich blijft voordoen en u zeker weet dat er communicatie stromen tussen bronnen bestaan, kunt u problemen opdoen in het forum van de [gebruiker](https://feedback.azure.com/forums/217313-networking?category_id=195844).

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Kan ik Traffic Analytics configureren met Power shell of een Azure Resource Manager sjabloon of client?

U kunt Traffic Analytics configureren met behulp van Windows Power shell vanaf versie 6.2.1. Zie [set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog)voor informatie over het configureren van flow logboek registratie en Traffic Analytics voor een specifieke NSG met behulp van de set-cmdlet. Zie [Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus)om de logboek registratie van de stroom en de status van het Traffic Analytics voor een specifieke NSG op te halen.

Op dit moment kunt u geen Azure Resource Manager-sjabloon gebruiken om Traffic Analytics te configureren.

Als u Traffic Analytics wilt configureren met behulp van een Azure Resource Manager-client, raadpleegt u de volgende voor beelden.

**Voor beeld van de cmdlet instellen:**
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
**Voor beeld van cmdlet ophalen:**
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


## <a name="how-is-traffic-analytics-priced"></a>Hoe wordt Traffic Analytics geprijsd?

Traffic Analytics wordt gemeten. De meting is gebaseerd op de verwerking van gegevens van stroom logboeken door de service en de resulterende uitgebreide logboeken worden opgeslagen in een Log Analytics-werk ruimte. 

Als de gegevens in een opslag account dat is verwerkt door Traffic Analytics 10 GB en uitgebreide logboeken die zijn opgenomen in Log Analytics werk ruimte 1 GB [zijn, worden](https://azure.microsoft.com/pricing/details/network-watcher/)de volgende kosten in rekening gebracht: 10 x $2,3 + 1 x $2,76 = $25,76

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Hoe vaak worden gegevens Traffic Analytics verwerkt?

Raadpleeg het [gedeelte gegevens aggregatie](./traffic-analytics-schema.md#data-aggregation) in Traffic Analytics schema en gegevens aggregatie document

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hoe beslist Traffic Analytics dat een IP schadelijk is? 

Traffic Analytics is afhankelijk van micro soft interne Threat Intelligence-systemen om als schadelijk te worden beschouwd als een IP-adres. Deze systemen maken gebruik van diverse telemetrie-bronnen, zoals micro soft-producten en-services, de micro soft Digital misdrijven Unit (DCU), het micro soft Security Response Center (MSRC) en externe feeds, en een grote verscheidenheid aan informatie. Enkele van deze gegevens is micro soft Internal. Als een bekend IP-adres wordt gevlagd als schadelijk, moet u een ondersteunings ticket geven om de details te weten.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hoe kan ik waarschuwingen instellen voor Traffic Analytics gegevens?

Traffic Analytics biedt geen ingebouwde ondersteuning voor waarschuwingen. Omdat Traffic Analytics gegevens echter worden opgeslagen in Log Analytics kunt u aangepaste query's schrijven en waarschuwingen instellen. Stappen
- U kunt de snelkoppeling voor Log Analytics gebruiken in Traffic Analytics. 
- Gebruik het [schema dat hier wordt beschreven](traffic-analytics-schema.md) om uw query's te schrijven 
- Klik op nieuwe waarschuwings regel om de waarschuwing te maken
- Raadpleeg de [documentatie over logboek waarschuwingen](../azure-monitor/alerts/alerts-log.md) voor het maken van de waarschuwing

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Hoe kan ik controleren welke Vm's het meest lokale verkeer ontvangen?

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

  Voor IP-adressen:

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

Gebruik voor tijd notatie: jjjj-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Hoe kan ik standaard afwijking in verkeer dat door mijn Vm's wordt ontvangen, van on-premises machines controleren?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

Voor IP-adressen:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Hoe kan ik controleren welke poorten bereikbaar zijn (of geblokkeerd) tussen IP-paren met NSG-regels?

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

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hoe kan ik navigeren met behulp van het toetsen bord in de geo-kaart weergave?

De pagina GeoMap bevat twee hoofd secties:
    
- **Banner**: de banner aan de bovenkant van de geokaart bevat knoppen voor het selecteren van verkeers distributie filters (bijvoorbeeld implementatie, verkeer van landen/regio's en kwaad aardig). Wanneer u een knop selecteert, wordt het respectieve filter toegepast op de kaart. Als u bijvoorbeeld de knop actief selecteert, markeert de kaart de actieve Data Centers in uw implementatie.
- **Kaart**: onder de banner ziet u de distributie van verkeer onder Azure-data centers en landen/regio's.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbord navigatie op de banner
    
- Standaard is de selectie op de pagina GeoMap voor de banner het filter ' Azure DCs '.
- Als u wilt overstappen op een ander filter, gebruikt u de `Tab` of de- `Right arrow` sleutel. Als u naar achteren wilt gaan, gebruikt u de `Shift+Tab` of de- `Left arrow` sleutel. Forward navigatie is van links naar rechts, gevolgd door boven naar beneden.
- Druk op `Enter` de `Down` pijl toets om het geselecteerde filter toe te passen. Op basis van filter selectie en-implementatie worden een of meer knoop punten onder de kaart sectie gemarkeerd.
- Als u wilt scha kelen tussen banners en kaarten, drukt u op `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-map"></a>Toetsenbord navigatie op de kaart
    
- Nadat u een filter op de banner hebt geselecteerd en erop hebt geklikt `Ctrl+F6` , wordt de focus verplaatst naar een van de gemarkeerde knoop punten (**Azure Data Center** of **land/regio**) in de kaart weergave.
- Als u wilt overstappen op andere gemarkeerde knoop punten in de kaart, gebruikt u `Tab` of de `Right arrow` sleutel voor voorwaartse verplaatsing. Gebruik `Shift+Tab` of de `Left arrow` sleutel voor achterwaartse verplaatsing.
- Als u een gemarkeerd knoop punt in de kaart wilt selecteren, gebruikt u de `Enter` of- `Down arrow` sleutel.
- Wanneer u een van deze knoop punten selecteert, wordt de focus verplaatst naar het **vak informatie** van het knoop punt. De focus wordt standaard verplaatst naar de knop gesloten in het **dialoog venster informatie**. Als u verder wilt gaan in de weer gave van het **vak** , gebruikt u `Right arrow` en de `Left arrow` toetsen om respectievelijk vooruit en achteruit te gaan. Wanneer `Enter` u op hetzelfde effect klikt, wordt de knop prioriteit geselecteerd in het **dialoog venster informatie**.
- Wanneer u op de `Tab` **knop info** klikt terwijl de focus is, wordt de focus verplaatst naar de eind punten in hetzelfde continent als het geselecteerde knoop punt. Gebruik de- `Right arrow` en- `Left arrow` sleutels om deze eind punten te door lopen.
- Als u wilt overstappen op andere flow-eind punten of continenten clusters, gebruikt u `Tab` voor voorwaartse verplaatsing en `Shift+Tab` voor achterwaartse verplaatsing.
- Wanneer de focus zich op **continent-clusters** bevindt, gebruikt u de `Enter` of de `Down` pijl toetsen om de eind punten in het continent-cluster te markeren. Als u de eind punten wilt door lopen en de knop Sluiten in het vak informatie van het continent-cluster, gebruikt u `Right arrow` respectievelijk de of `Left arrow` -sleutel voor voorwaartse en achterwaartse verplaatsing. Op elk eind punt kunt u gebruiken `Shift+L` om over te scha kelen naar de verbindings lijn van het geselecteerde knoop punt naar het eind punt. U kunt `Shift+L` opnieuw op het geselecteerde eind punt drukken om door te gaan.
        
### <a name="keyboard-navigation-at-any-stage"></a>Toetsenbord navigatie in elk stadium
    
- `Esc` Hiermee wordt de uitgevouwen selectie samengevouwen.
- De `Up-arrow` sleutel voert dezelfde actie uit als `Esc` . De `Down arrow` sleutel voert dezelfde actie uit als `Enter` .
- Gebruiken `Shift+Plus` om in te zoomen en `Shift+Minus` uit te zoomen.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hoe kan ik navigeren met behulp van het toetsen bord in de topologie weergave van het virtuele netwerk?

De pagina topologie virtuele netwerken bevat twee hoofd secties:
    
- **Banner**: de banner aan de bovenkant van de topologie met virtuele netwerken biedt knoppen om filters voor het distribueren van verkeer te selecteren (bijvoorbeeld verbonden virtuele netwerken, niet-verbonden virtuele netwerken en open bare ip's). Wanneer u een knop selecteert, wordt het respectieve filter toegepast op de topologie. Als u bijvoorbeeld de knop actief selecteert, markeert de topologie de actieve virtuele netwerken in uw implementatie.
- **Topologie**: onder de banner ziet u in het gedeelte topologie verkeer distributie tussen virtuele netwerken.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbord navigatie op de banner
    
- De selectie op de topologie pagina met virtuele netwerken voor de banner is standaard het filter ' verbonden VNets '.
- Als u wilt overstappen op een ander filter, gebruikt u de `Tab` sleutel om door te gaan. Gebruik de sleutel om terug te gaan `Shift+Tab` . Forward navigatie is van links naar rechts, gevolgd door boven naar beneden.
- Druk op `Enter` om het geselecteerde filter toe te passen. Op basis van de filter selectie en-implementatie worden een of meer knoop punten (virtueel netwerk) onder de sectie topologie gemarkeerd.
- Als u wilt scha kelen tussen de banner en de topologie, drukt u op `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Toetsenbord navigatie op de topologie
    
- Nadat u een filter op de banner hebt geselecteerd en erop hebt geklikt `Ctrl+F6` , wordt de focus verplaatst naar een van de gemarkeerde knoop punten (**VNet**) in de weer gave topologie.
- Als u wilt overstappen op andere gemarkeerde knoop punten in de topologie weergave, gebruikt u de `Shift+Right arrow` sleutel voor voorwaarts verplaatsen. 
- Focus wordt op gemarkeerde knoop punten verplaatst naar het **vak informatie** van het knoop punt. Focus gaat standaard naar de knop **meer details** in het **dialoog venster informatie**. Als u verder wilt verplaatsen in de weer gave van het **vak** , gebruikt `Right arrow` u de `Left arrow` -en-sleutels om respectievelijk vooruit en achteruit te gaan. Wanneer `Enter` u op hetzelfde effect klikt, wordt de knop prioriteit geselecteerd in het **dialoog venster informatie**.
- Wanneer u een van deze knoop punten selecteert, kunt u alle verbindingen met één voor één bekijken door op de `Shift+Left arrow` toets te drukken. Focus wordt verplaatst naar het **vak informatie** van deze verbinding. De focus kan op elk gewenst moment worden teruggedraaid naar het knoop punt door opnieuw op te drukken `Shift+Right arrow` .
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hoe kan ik navigeren met behulp van het toetsen bord in de weer gave van de subnet-topologie?

De pagina topologie virtuele subnetwerken bevat twee hoofd secties:
    
- **Banner**: de banner aan de bovenkant van de topologie met virtuele subnetwerken biedt knoppen om filters voor het distribueren van verkeer te selecteren (bijvoorbeeld Active, medium en gateway subnetten). Wanneer u een knop selecteert, wordt het respectieve filter toegepast op de topologie. Als u bijvoorbeeld de knop actief selecteert, markeert de topologie het actieve virtuele subnetwerk in uw implementatie.
- **Topologie**: onder de banner ziet u in het gedeelte topologie verkeer distributie over virtuele subnetwerken.
    
### <a name="keyboard-navigation-on-the-banner"></a>Toetsenbord navigatie op de banner
    
- De selectie op de topologie pagina virtuele subnetwerken voor de banner is standaard het filter subnetten.
- Als u wilt overstappen op een ander filter, gebruikt u de `Tab` sleutel om door te gaan. Gebruik de sleutel om terug te gaan `Shift+Tab` . Forward navigatie is van links naar rechts, gevolgd door boven naar beneden.
- Druk op `Enter` om het geselecteerde filter toe te passen. Op basis van filter selectie en-implementatie worden een of meer knoop punten (subnet) onder de sectie topologie gemarkeerd.
- Als u wilt scha kelen tussen de banner en de topologie, drukt u op `Ctrl+F6` .
        
### <a name="keyboard-navigation-on-the-topology"></a>Toetsenbord navigatie op de topologie
    
- Nadat u een filter op de banner hebt geselecteerd en erop hebt geklikt `Ctrl+F6` , wordt de focus verplaatst naar een van de gemarkeerde knoop punten (**subnet**) in de weer gave topologie.
- Als u wilt overstappen op andere gemarkeerde knoop punten in de topologie weergave, gebruikt u de `Shift+Right arrow` sleutel voor voorwaarts verplaatsen. 
- Focus wordt op gemarkeerde knoop punten verplaatst naar het **vak informatie** van het knoop punt. Focus gaat standaard naar de knop **meer details** in het **dialoog venster informatie**. Als u verder wilt gaan in de weer gave van het **vak** , gebruikt u `Right arrow` en de `Left arrow` toetsen om respectievelijk vooruit en achteruit te gaan. Wanneer `Enter` u op hetzelfde effect klikt, wordt de knop prioriteit geselecteerd in het **dialoog venster informatie**.
- Wanneer u een van deze knoop punten selecteert, kunt u alle verbindingen met één voor één bekijken door op de toets te drukken `Shift+Left arrow` . Focus wordt verplaatst naar het **vak informatie** van deze verbinding. De focus kan op elk gewenst moment worden teruggedraaid naar het knoop punt door opnieuw op te drukken `Shift+Right arrow` .

## <a name="are-classic-nsgs-supported"></a>Worden klassieke Nsg's ondersteund?
Nee, Traffic Analytics biedt geen ondersteuning voor klassieke NSG. Het is raadzaam om IaaS-resources van klassiek naar Azure Resource Manager te migreren omdat klassieke resources worden [afgeschaft](../virtual-machines/classic-vm-deprecation.md). Raadpleeg dit artikel voor meer informatie [over het migreren](../virtual-machines/migration-classic-resource-manager-overview.md)van.