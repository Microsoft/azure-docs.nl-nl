---
title: Inleiding tot het oplossen van resources
titleSuffix: Azure Network Watcher
description: Op deze pagina vindt u een overzicht van de mogelijkheden voor het oplossen van problemen met Network Watcher bronnen
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 5342c4505bd300d1287acba246ca59610f2697bd
ms.sourcegitcommit: 89c0482c16bfec316a79caa3667c256ee40b163f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97858634"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Inleiding tot het oplossen van resources in azure Network Watcher

Virtual Network gateways bieden connectiviteit tussen on-premises resources en andere virtuele netwerken in Azure. Het bewaken van gateways en hun verbindingen is essentieel om te zorgen dat de communicatie niet wordt vebroken. Network Watcher biedt de mogelijkheid om problemen met gateways en verbindingen op te lossen. De mogelijkheid kan worden aangeroepen via de portal, Power shell, Azure CLI of REST API. Als Network Watcher wordt aangeroepen, wordt de status van de gateway of de verbinding vastgesteld en worden de juiste resultaten geretourneerd. De aanvraag is een langlopende trans actie. De resultaten worden geretourneerd zodra de diagnose is voltooid.

![Scherm afbeelding toont Network Watcher V P N diagnostische gegevens.][2]

## <a name="results"></a>Resultaten

De voorafgaande resultaten die zijn geretourneerd, geven een algemene afbeelding van de status van de resource. Er kunnen dieper informatie worden verstrekt voor resources, zoals wordt weer gegeven in de volgende sectie:

De volgende lijst bevat de waarden die worden geretourneerd met de API voor het oplossen van problemen:

* **StartTime** -deze waarde is het tijdstip waarop de API-aanroep voor troubleshooting is gestart.
* **EndTime** -deze waarde is de tijd waarop de probleem oplossing is beëindigd.
* **code** : deze waarde is niet in orde als er één diagnose fout is opgetreden.
* **resultaten** -resultaten zijn een verzameling resultaten die worden geretourneerd via de verbinding of de gateway van het virtuele netwerk.
    * **id** : deze waarde is het fout type.
    * **samen vatting** : deze waarde is een samen vatting van de fout.
    * **gedetailleerd** : deze waarde geeft een gedetailleerde beschrijving van de fout.
    * **recommendedActions** : deze eigenschap is een verzameling aanbevolen acties die moeten worden uitgevoerd.
      * **actionText** : deze waarde bevat de tekst die beschrijft welke actie moet worden ondernomen.
      * **actionUri** : deze waarde biedt de URI aan de hand waarvan u kunt reageren.
      * **actionUriText** : deze waarde is een korte beschrijving van de actie tekst.

In de volgende tabellen ziet u de verschillende fout typen (id onder resultaten van de voor gaande lijst) die beschikbaar zijn en als het probleem logboeken maakt.

### <a name="gateway"></a>Gateway

| Fouttype | Reden | Logboek|
|---|---|---|
| NoFault | Als er geen fout wordt gedetecteerd |Ja|
| GatewayNotFound | Kan geen gateway of gateway vinden |Nee|
| PlannedMaintenance |  Het gateway-exemplaar is onderhouds werkzaamheden  |Nee|
| UserDrivenUpdate | Deze fout treedt op wanneer een gebruikersupdate wordt uitgevoerd. De update kan een wijziging van het formaat zijn. | Nee |
| VipUnResponsive | Deze fout treedt op wanneer het primaire exemplaar van de gateway niet kan worden bereikt als gevolg van een fout in de statustest. | Nee |
| PlatformInActive | Er is een probleem met het platform. | Nee|
| ServiceNotRunning | De onderliggende service is niet actief. | Nee|
| NoConnectionsFoundForGateway | Er zijn geen verbindingen op de gateway. Deze fout is slechts een waarschuwing.| Nee|
| ConnectionsNotConnected | Er zijn geen verbindingen met de verbinding. Deze fout is slechts een waarschuwing.| Ja|
| GatewayCPUUsageExceeded | Het CPU-gebruik van de huidige gateway is > 95%. | Ja |

### <a name="connection"></a>Verbinding

| Fouttype | Reden | Logboek|
|---|---|---|
| NoFault | Als er geen fout wordt gedetecteerd |Ja|
| GatewayNotFound | Kan geen gateway of gateway vinden |Nee|
| PlannedMaintenance | Het gateway-exemplaar is onderhouds werkzaamheden  |Nee|
| UserDrivenUpdate | Deze fout treedt op wanneer een gebruikersupdate wordt uitgevoerd. De update kan een wijziging van het formaat zijn.  | Nee |
| VipUnResponsive | Deze fout treedt op wanneer het primaire exemplaar van de gateway niet kan worden bereikt als gevolg van een fout in de statustest. | Nee |
| ConnectionEntityNotFound | De configuratie van de verbinding ontbreekt | Nee |
| ConnectionIsMarkedDisconnected | De verbinding is gemarkeerd als ' verbinding verbroken ' |Nee|
| ConnectionNotConfiguredOnGateway | De onderliggende service is niet geconfigureerd voor de verbinding. | Ja |
| ConnectionMarkedStandby | De onderliggende service is gemarkeerd als stand-by.| Ja|
| Verificatie | Vooraf gedeelde sleutel komt niet overeen | Ja|
| PeerReachability | De peer gateway is niet bereikbaar. | Ja|
| IkePolicyMismatch | De peer gateway heeft een IKE-beleid dat niet wordt ondersteund door Azure. | Ja|
| WfpParse-fout | Er is een fout opgetreden bij het parseren van het WFP-logboek. |Ja|

## <a name="supported-gateway-types"></a>Ondersteunde gateway typen

De volgende tabel geeft een lijst van de gateways en verbindingen die worden ondersteund met Network Watcher probleem oplossing:

| Gateway of verbinding | Ondersteund  |
|---------|---------|
|**Gatewaytypen**   |         |
|VPN      | Ondersteund        |
|ExpressRoute | Niet ondersteund |
|**VPN-typen** | |
|Route op basis | Ondersteund|
|Op basis van beleid | Niet ondersteund|
|**Verbindingstypen**||
|IPsec| Ondersteund|
|VNet2Vnet| Ondersteund|
|ExpressRoute| Niet ondersteund|
|VPNClient| Niet ondersteund|

## <a name="log-files"></a>Logboekbestanden

De logboek bestanden voor het oplossen van problemen met bronnen worden opgeslagen in een opslag account nadat het oplossen van resources is voltooid. De volgende afbeelding toont de voorbeeld inhoud van een aanroep die een fout heeft veroorzaakt.

![zip-bestand][1]

> [!NOTE]
> In sommige gevallen wordt slechts een subset van de logboek bestanden naar opslag geschreven.

Raadpleeg aan de [slag met Azure Blob Storage met .net](../storage/blobs/storage-quickstart-blobs-dotnet.md)voor instructies voor het downloaden van bestanden van Azure Storage-accounts. Een ander hulp programma dat kan worden gebruikt, is Storage Explorer. Meer informatie over Storage Explorer kunt u vinden op de volgende koppeling: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Het **ConnectionStats.txt** bestand bevat de algemene statistieken van de verbinding, inclusief binnenkomend en uitgaand aantal bytes, verbindings status en de tijd dat de verbinding tot stand is gebracht.

> [!NOTE]
> Als de aanroep van de API voor probleem oplossing in orde wordt geretourneerd, is het enige resultaat dat in het zip-bestand wordt geretourneerd een **ConnectionStats.txt** bestand.

De inhoud van dit bestand is vergelijkbaar met het volgende voor beeld:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Het **CPUStats.txt** bestand bevat CPU-gebruik en geheugen dat beschikbaar is op het moment van testen.  De inhoud van dit bestand is vergelijkbaar met het volgende voor beeld:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Het **IKEErrors.txt** bestand bevat IKE-fouten die tijdens de bewaking zijn gevonden.

In het volgende voor beeld wordt de inhoud van een IKEErrors.txt-bestand weer gegeven. Uw fouten kunnen afwijken, afhankelijk van het probleem.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Het logboek bestand van de **Scrubbed-wfpdiag.txt** bevat het WFP-logboek. Dit logboek bevat logboek registratie van pakket-drop en IKE/AuthIP-fouten.

In het volgende voor beeld wordt de inhoud van het Scrubbed-wfpdiag.txt-bestand weer gegeven. In dit voor beeld was de gedeelde sleutel van een verbinding niet juist, wat vanaf de derde regel van de onderkant kan worden weer gegeven. Het volgende voor beeld is slechts een fragment van het hele logboek, omdat het logboek lang kan zijn, afhankelijk van het probleem.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt. Sum

Het bestand **wfpdiag.txt. Sum** is een logboek waarin de gewerkte buffers en gebeurtenissen worden weer gegeven.

Het volgende voor beeld is de inhoud van een bestand wfpdiag.txt. sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="considerations"></a>Overwegingen 
* CLI-fout: als u Azure CLI gebruikt om de opdracht uit te voeren, moeten de VPN Gateway en het opslag account zich in dezelfde resource groep bevinden. Klanten met de resources in verschillende resource groepen kunnen in plaats daarvan Power shell of de Azure Portal gebruiken.  

## <a name="next-steps"></a>Volgende stappen

Zie problemen met de [communicatie tussen netwerken vaststellen](diagnose-communication-problem-between-networks.md)voor meer informatie over het vaststellen van een probleem met een gateway of gateway verbinding.
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
