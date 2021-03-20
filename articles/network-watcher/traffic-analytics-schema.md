---
title: Azure Traffic Analytics-schema | Microsoft Docs
description: Inzicht in het schema van Traffic Analytics voor het analyseren van stroom logboeken van de Azure-netwerk beveiligings groep.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 765a2728c5de49787a64fff723625e53e100e450
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98017779"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schema's en gegevens aggregatie in Traffic Analytics

Traffic Analytics is een Cloud oplossing die inzicht geeft in de activiteit van gebruikers en toepassingen in Cloud netwerken. Traffic Analytics analyseert Network Watcher-stroom logboeken voor netwerk beveiligings groepen (NSG) om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Met Traffic Analytics kunt u het volgende doen:

- Visualiseer netwerk activiteiten in uw Azure-abonnementen en Identificeer HOTS pots.
- Identificeer beveiligings Risico's voor en beveilig uw netwerk met informatie zoals open poorten, toepassingen die toegang proberen te krijgen tot internet en virtuele machines (VM) die verbinding maken met een Rogue-netwerk.
- Inzicht in de verkeers stromen in azure-regio's en Internet om uw netwerk implementatie te optimaliseren voor prestaties en capaciteit.
- Spoort onjuiste netwerk configuraties in de loop van mislukte verbindingen in uw netwerk.
- Ken netwerk gebruik in bytes, pakketten of stromen.

### <a name="data-aggregation"></a>Gegevensaggregatie

1. Alle stroom Logboeken in een NSG tussen ' FlowIntervalStartTime_t ' en ' FlowIntervalEndTime_t ' worden vastgelegd met een interval van één minuut in het opslag account als blobs voordat ze door Traffic Analytics worden verwerkt.
2. Het standaard verwerkings interval van Traffic Analytics is 60 minuten. Dit betekent dat elke 60 minuten Traffic Analytics blobs uit opslag voor aggregatie pickt. Als er een verwerkings interval van 10 minuten is gekozen, worden er na elke 10 minuten blobs uit het opslag account gekozen Traffic Analytics.
3. Stromen met dezelfde bron-IP, doel-IP, doel poort, NSG naam, NSG-regel, stroom richting en trans port Layer Protocol (TCP of UDP) (Opmerking: bron poort wordt uitgesloten voor aggregatie) zijn geregeld in één stroom door Traffic Analytics
4. Deze enkele record wordt gedecoreerd (Details in de onderstaande sectie) en opgenomen in Log Analytics door Traffic Analytics. dit proces kan Maxi maal 1 uur duren.
5. FlowStartTime_t veld geeft het eerste exemplaar van een dergelijke geaggregeerde stroom (dezelfde vier tupel) aan in het verwerkings interval van het stroom logboek tussen ' FlowIntervalStartTime_t ' en ' FlowIntervalEndTime_t '.
6. Voor alle resources in TA zijn de stromen die in de gebruikers interface worden aangegeven, de totale stromen die worden gezien door de NSG, maar in Log Analytics gebruiker ziet u alleen de enkele, gereduceerde record. Als u alle stromen wilt weer geven, gebruikt u het veld blob_id, waarnaar kan worden verwezen vanuit Storage. Het totale aantal stromen voor die record komt overeen met de afzonderlijke stromen die in de BLOB worden weer gegeven.

De onderstaande query helpt u tijdens de afgelopen 30 dagen te kijken naar alle subnetten die communiceren met niet-Azure open bare Ip's.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
Als u het pad naar de BLOB voor de stromen in de bovenstaande query wilt weer geven, gebruikt u de volgende query:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

Met de bovenstaande query wordt een URL gebouwd om rechtstreeks toegang te krijgen tot de blob. De URL met de plaatsings houders vindt u hieronder:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Velden die in Traffic Analytics schema worden gebruikt
  > [!IMPORTANT]
  > Het Traffic Analytics schema is bijgewerkt op 22 augustus 2019. Het nieuwe schema biedt bron-en doel-Ip's die afzonderlijk moeten worden verwijderd FlowDirection veld moet worden geparseerd om query's eenvoudiger te maken. </br>
  > FASchemaVersion_s bijgewerkt van 1 naar 2. </br>
  > Afgeschafte velden: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Nieuwe velden: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Afgeschafte velden zijn beschikbaar tot 22 november 2019.

Traffic Analytics is gebaseerd op Log Analytics, zodat u aangepaste query's kunt uitvoeren op gegevens die zijn gedecoreerd door Traffic Analytics en op dezelfde manier waarschuwingen instellen.

Hieronder vindt u de velden in het schema en wat ze aangeven

| Veld | Indeling | Opmerkingen |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabel voor Traffic Analytics gegevens
| SubType_s | NSF | Subtype voor de stroom Logboeken. Alleen "NSF" gebruiken, andere waarden van SubType_s zijn voor de interne werking van het product |
| FASchemaVersion_s |   2   | Schema versie. Weerspiegelt niet de versie van het NSG-stroom logboek |
| TimeProcessed_t   | Datum en tijd in UTC  | Tijdstip waarop de Traffic Analytics de onbewerkte stroom logboeken van het opslag account heeft verwerkt |
| FlowIntervalStartTime_t | Datum en tijd in UTC |  Begin tijd van het verwerkings interval van het stroom logboek. Dit is het tijdstip waarop het stroom interval wordt gemeten |
| FlowIntervalEndTime_t | Datum en tijd in UTC | Eind tijd van het verwerkings interval van het stroom logboek |
| FlowStartTime_t | Datum en tijd in UTC |  Het eerste exemplaar van de stroom (die wordt geaggregeerd) in het verwerkings interval van het stroom logboek tussen ' FlowIntervalStartTime_t ' en ' FlowIntervalEndTime_t '. Deze stroom wordt geaggregeerd op basis van aggregatie logica |
| FlowEndTime_t | Datum en tijd in UTC | Laatste exemplaar van de stroom (die wordt geaggregeerd) in het verwerkings interval van het stroom logboek tussen ' FlowIntervalStartTime_t ' en ' FlowIntervalEndTime_t '. In het geval van flow-logboek v2 bevat dit veld de tijd waarop de laatste stroom met dezelfde vier tuple is gestart (gemarkeerd als ' B ' in de onbewerkte stroom record) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Onbekende persoonlijke <br> * Onbekend | Definitie in notities onder de tabel |
| SrcIP_s | IP-adres van bron | Is leeg in het geval van AzurePublic-en ExternalPublic-stromen |
| DestIP_s | IP-adres van doel | Is leeg in het geval van AzurePublic-en ExternalPublic-stromen |
| VMIP_s | IP-adres van de virtuele machine | Gebruikt voor AzurePublic-en ExternalPublic-stromen |
| PublicIP_s | Openbare IP-adressen | Gebruikt voor AzurePublic-en ExternalPublic-stromen |
| DestPort_d | Doelpoort | Poort waarop het verkeer binnenkomt |
| L4Protocol_s  | * T <br> * U  | Transport Protocol. T = TCP <br> U = UDP |
| L7Protocol_s  | Protocol naam | Afgeleid van doel poort |
| FlowDirection_s | * I = inkomend<br> * O = uitgaande | Richting van de stroom in/uit NSG met per stroom logboek |
| FlowStatus_s  | * A = toegestaan door de regel NSG <br> * D = geweigerd door de NSG-regel  | Status van de stroom die is toegestaan/nblocked door NSG volgens het stroom logboek |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | De netwerk beveiligings groep (NSG) die aan de stroom is gekoppeld |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  NSG-regel die deze stroom heeft toegestaan of geweigerd |
| NSGRule_s | NSG_RULENAME |  NSG-regel die deze stroom heeft toegestaan of geweigerd |
| NSGRuleType_s | * Door de gebruiker gedefinieerd * standaard |   Het type NSG-regel dat door de stroom wordt gebruikt |
| MACAddress_s | MAC-adres | MAC-adres van de NIC waarop de stroom is vastgelegd |
| Subscription_s | Het abonnement van het virtuele netwerk van Azure/de netwerk interface/virtuele machine is ingevuld in dit veld | Alleen van toepassing op de stroom typen FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow en UnknownPrivate (stroom typen waarbij slechts één zijde van Azure is) |
| Subscription1_s | Abonnements-id | Abonnements-ID van het virtuele netwerk/netwerk interface/virtuele machine waartoe de bron-IP in de stroom behoort |
| Subscription2_s | Abonnements-id | Abonnements-ID van het virtuele netwerk/netwerk interface/virtuele machine waartoe het doel-IP in de stroom behoort |
| Region_s | De Azure-regio van het virtuele netwerk/de netwerk interface/virtuele machine waartoe het IP-adres in de stroom behoort | Alleen van toepassing op de stroom typen FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow en UnknownPrivate (stroom typen waarbij slechts één zijde van Azure is) |
| Region1_s | Azure-regio | Azure-regio van het virtuele netwerk/de netwerk interface/virtuele machine waartoe het bron-IP-adres in de stroom behoort |
| Region2_s | Azure-regio | De Azure-regio van het virtuele netwerk waartoe het doel-IP in de stroom behoort |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  NIC gekoppeld aan de VM die het verkeer verzendt of ontvangt |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC gekoppeld aan het bron-IP-adres in de stroom |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | NIC gekoppeld aan het doel-IP in de stroom |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | De virtuele machine die is gekoppeld aan de netwerk interface NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | De virtuele machine die is gekoppeld aan het bron-IP-adres in de stroom |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | De virtuele machine die is gekoppeld aan de doel-IP in de stroom |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Het subnet dat is gekoppeld aan de NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Het subnet dat is gekoppeld aan het bron-IP-adres in de stroom |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Het subnet dat is gekoppeld aan het doel-IP in de stroom |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | De toepassings gateway die is gekoppeld aan het bron-IP-adres in de stroom |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | De toepassings gateway die is gekoppeld aan het doel-IP in de stroom |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | De Load Balancer die is gekoppeld aan het bron-IP-adres in de stroom |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | De Load Balancer die is gekoppeld aan het doel-IP in de stroom |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | De lokale netwerk gateway die is gekoppeld aan het bron-IP-adres in de stroom |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | De lokale netwerk gateway die is gekoppeld aan het doel-IP in de stroom |
| ConnectionType_s | Mogelijke waarden zijn VNetPeering, VpnGateway en ExpressRoute |    Verbindingstype |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Verbindings naam. Voor flowtype P2S wordt dit opgemaakt als <gateway name> _<VPN Client IP> |
| ConnectingVNets_s | Door spaties gescheiden lijst met namen van virtuele netwerken | In het geval van hub-en spoke-topologie worden de virtuele netwerken van de hub hier ingevuld |
| Country_s | Land code van twee letters (ISO 3166-1 alpha-2) | Gevuld voor stroom type ExternalPublic. Alle IP-adressen in PublicIPs_s veld hebben hetzelfde land nummer |
| AzureRegion_s | Azure-regio locaties | Gevuld voor stroom type AzurePublic. Alle IP-adressen in PublicIPs_s veld delen de Azure-regio |
| AllowedInFlows_d | | Aantal binnenkomende stromen dat is toegestaan. Hiermee wordt het aantal stromen aangegeven dat dezelfde vier tuple heeft gedeeld naar de netwerk interface waarop de stroom is vastgelegd |
| DeniedInFlows_d |  | Aantal inkomende stromen dat is geweigerd. (Binnenkomend naar de netwerk interface waarop de stroom is vastgelegd) |
| AllowedOutFlows_d | | Aantal uitgaande stromen dat is toegestaan (uitgaand naar de netwerk interface waarop de stroom is vastgelegd) |
| DeniedOutFlows_d  | | Aantal uitgaande stromen dat is geweigerd (uitgaand naar de netwerk interface waarop de stroom is vastgelegd) |
| FlowCount_d | Afgeschaft. Het totale aantal stromen dat overeenkomt met dezelfde vier tuple. In het geval van stroom typen ExternalPublic en AzurePublic omvat Count ook de stromen van verschillende PublicIP-adressen.
| InboundPackets_d | Pakketten die zijn ontvangen als vastgelegd op de netwerk interface waarop NSG regel is toegepast | Dit wordt alleen ingevuld voor het schema van versie 2 van het NSG-stroom logboek |
| OutboundPackets_d  | Pakketten die zijn verzonden als vastgelegd op de netwerk interface waarop NSG regel is toegepast | Dit wordt alleen ingevuld voor het schema van versie 2 van het NSG-stroom logboek |
| InboundBytes_d |  Ontvangen bytes zoals vastgelegd op de netwerk interface waarop NSG regel is toegepast | Dit wordt alleen ingevuld voor het schema van versie 2 van het NSG-stroom logboek |
| OutboundBytes_d | Verzonden bytes zoals vastgelegd op de netwerk interface waarop NSG regel is toegepast | Dit wordt alleen ingevuld voor het schema van versie 2 van het NSG-stroom logboek |
| CompletedFlows_d  |  | Dit wordt alleen ingevuld met een waarde die niet gelijk is aan nul voor het schema van versie 2 van het NSG-stroom logboek |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Vermeldingen gescheiden door balken |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Vermeldingen gescheiden door balken |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Vermeldingen gescheiden door balken |

### <a name="notes"></a>Notities

1. In het geval van AzurePublic-en ExternalPublic stromen wordt het Azure VM-IP-adres van de klant ingevuld in VMIP_s veld, terwijl de open bare IP-adressen worden ingevuld in het veld PublicIPs_s. Voor deze twee stroom typen moeten we VMIP_s en PublicIPs_s gebruiken in plaats van SrcIP_s en DestIP_s velden. Voor AzurePublic-en ExternalPublicIP-adressen, verzamelen we verder, zodat het aantal records dat is opgenomen in de werk ruimte van de klant log Analytics mini maal is. (Dit veld wordt binnenkort afgeschaft en er moeten SrcIP_ en DestIP_s worden gebruikt, afhankelijk van of de Azure-VM de bron of de bestemming in de stroom is)
1. Details voor stroom typen: op basis van de IP-adressen die in de stroom zijn betrokken, categoriseren we de stromen in naar de volgende stroom typen:
1. IntraVNet: zowel de IP-adressen in de stroom bevinden zich in dezelfde Azure-Virtual Network.
1. InterVNet-IP-adressen in de stroom bevinden zich in de twee verschillende virtuele netwerken van Azure.
1. S2S – (site naar site) een van de IP-adressen behoort tot Azure Virtual Network terwijl het andere IP-adres behoort tot het netwerk van de klant (site) die is verbonden met de Azure-Virtual Network via een VPN-gateway of Express route.
1. P2S-(wijs site toe) een van de IP-adressen behoort tot Azure Virtual Network terwijl het andere IP-adres behoort tot het netwerk van de klant (site) die via VPN-gateway is verbonden met de Azure-Virtual Network.
1. AzurePublic: een van de IP-adressen maakt deel uit van Azure Virtual Network terwijl het andere IP-adres behoort tot de interne open bare IP-adressen van Azure die eigendom zijn van micro soft. De open bare IP-adressen van de klant maken geen deel uit van dit type stroom. Zo zou een door de klant in eigendom zijnde VM die verkeer naar een Azure-service (opslag eindpunt) verzendt, onder dit type stroom wordt gecategoriseerd.
1. ExternalPublic: een van de IP-adressen maakt deel uit van de Azure-Virtual Network terwijl het andere IP-adres een open bare IP is die zich niet in azure bevindt, wordt niet gerapporteerd als schadelijk in de ASC-feeds die Traffic Analytics gebruikt voor het verwerkings interval tussen "FlowIntervalStartTime_t" en "FlowIntervalEndTime_t".
1. MaliciousFlow: een van de IP-adressen maakt deel uit van het virtuele Azure-netwerk, terwijl het andere IP-adres een open bare IP is die zich niet in azure bevindt en als schadelijk wordt gerapporteerd in de ASC-feeds die Traffic Analytics gebruikt voor het verwerkings interval tussen ' FlowIntervalStartTime_t ' en ' FlowIntervalEndTime_t '.
1. UnknownPrivate: een van de IP-adressen bevindt zich in azure Virtual Network, terwijl het andere IP-adres deel uitmaakt van het privé-IP-bereik zoals gedefinieerd in RFC 1918 en kan niet worden toegewezen door Traffic Analytics aan een site die eigendom is van de klant of Azure Virtual Network.
1. Onbekend: kan de IP-adressen in de stromen niet toewijzen met de klant topologie in Azure en on-premises (site).
1. Sommige veld namen worden toegevoegd met \_ s of \_ d. Deze geven niet de bron en het doel aan, maar wijzen de teken reeks en decimaal van het gegevens type aan.

### <a name="next-steps"></a>Volgende stappen
Voor antwoorden op veelgestelde vragen raadpleegt u veelgestelde vragen over [Traffic Analytics](traffic-analytics-faq.md) voor meer informatie over functionaliteit, raadpleegt u de [documentatie van Traffic Analytics](traffic-analytics.md)
