---
title: Problemen met on-premises connectiviteit via VPN-gateway vaststellen
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u on-premises connectiviteit via een VPN-gateway kunt vaststellen met Azure Network Watcher resource Troubleshooting.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 9014f24918013872ce102d094f62fd5703594ddc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961852"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnose van on-premises connectiviteit via VPN-gateways

Met Azure VPN Gateway kunt u hybride oplossingen maken die de nood zaak voor een beveiligde verbinding tussen uw on-premises netwerk en uw virtuele Azure-netwerk. Als uw vereisten uniek zijn, is dit de keuze uit een on-premises VPN-apparaat. Azure ondersteunt momenteel [meerdere VPN-apparaten](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) die voortdurend worden gevalideerd in samen werking met de leveranciers van het apparaat. Controleer de apparaatspecifieke configuratie-instellingen voordat u uw on-premises VPN-apparaat configureert. Op dezelfde manier wordt Azure VPN Gateway geconfigureerd met een set [ondersteunde IPsec-para meters](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) die worden gebruikt voor het tot stand brengen van verbindingen. Het is momenteel niet mogelijk om een specifieke combi natie van IPsec-para meters op te geven of te selecteren in de Azure-VPN Gateway. Voor het tot stand brengen van een verbinding tussen on-premises en Azure, moeten de instellingen voor het on-premises VPN-apparaat in overeenstemming zijn met de IPsec-para meters die zijn voorgeschreven door Azure VPN Gateway. Als de instellingen onjuist zijn, is er sprake van een verlies van de verbinding en tot het oplossen van deze problemen was niet lastig en duurde het meestal uur om het probleem te identificeren en op te lossen.

Met de functie voor het oplossen van problemen met Azure Network Watcher kunt u problemen met uw gateway en verbindingen vaststellen en binnen enkele minuten voldoende informatie hebben om een weloverwogen beslissing te nemen om het probleem te verhelpen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenario

U wilt een site-naar-site-verbinding tussen Azure en on-premises configureren met behulp van Forti Gate als de on-premises VPN Gateway. Voor dit scenario moet u de volgende instellingen instellen:

1. Virtual Network gateway: de VPN Gateway op Azure
1. Lokale netwerk gateway: de [on-premises (Forti Gate) VPN gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) weer gave in de Azure-Cloud
1. Site-naar-site-verbinding (op route gebaseerd): [de verbinding tussen de VPN gateway en de on-premises router](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#CreateConnection)
1. [Forti Gate configureren](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Gedetailleerde stapsgewijze richt lijnen voor het configureren van een site-naar-site-configuratie vindt u op: [een VNet met een site-naar-site-verbinding maken met behulp van de Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Een van de kritieke configuratie stappen is het configureren van de IPsec-communicatie parameters, elke onjuiste configuratie leidt tot verlies van connectiviteit tussen het on-premises netwerk en Azure. Momenteel zijn Azure VPN-gateways geconfigureerd ter ondersteuning van de volgende IPsec-para meters voor fase 1. Zoals u kunt zien in de volgende tabel, zijn de versleutelings algoritmen die door Azure VPN Gateway worden ondersteund, AES256, AES128 en 3DES.

### <a name="ike-phase-1-setup"></a>Setup van IKE fase 1

| **Eigenschap** | **PolicyBased** | **RouteBased en Standard-of High-Performance VPN-gateway** |
| --- | --- | --- |
| IKE-versie |IKEv1 |IKEv2 |
| Diffie-Hellman-groep |Groep 2 (1024 bits) |Groep 2 (1024 bits) |
| Verificatiemethode |Vooraf gedeelde sleutel |Vooraf gedeelde sleutel |
| Versleutelingsalgoritmen |AES256 AES128 3DES |AES256 3DES |
| Hash-algoritme |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Levensduur (tijd) van beveiligingskoppeling (SA) fase 1 |28.800 seconden |28.800 seconden |

Als gebruiker bent u verplicht uw Forti Gate te configureren. u kunt een voorbeeld configuratie vinden op [github](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). U hebt uw Forti Gate niet bewust geconfigureerd voor het gebruik van SHA-512 als hash-algoritme. Omdat dit algoritme geen ondersteund algoritme is voor verbindingen op basis van beleid, werkt uw VPN-verbinding.

Deze problemen zijn moeilijk op te lossen en hoofd oorzaken zijn vaak niet-intuïtief. In dit geval kunt u een ondersteunings ticket openen om hulp te krijgen bij het oplossen van het probleem. Maar met Azure Network Watcher het oplossen van problemen met de API, kunt u deze problemen zelf identificeren.

## <a name="troubleshooting-using-azure-network-watcher"></a>Problemen oplossen met Azure Network Watcher

Maak verbinding met Azure PowerShell en start de cmdlet om uw verbinding te diagnosticeren `Start-AzNetworkWatcherResourceTroubleshooting` . U kunt de details over het gebruik van deze cmdlet vinden bij het [oplossen van problemen Virtual Network gateway en verbindingen-Power shell](network-watcher-troubleshoot-manage-powershell.md). Het kan enkele minuten duren voordat deze cmdlet is voltooid.

Zodra de cmdlet is voltooid, kunt u naar de opslag locatie die in de cmdlet is opgegeven, zoeken naar gedetailleerde informatie over het probleem en de logboeken. Azure Network Watcher maakt een zip-map die de volgende logboek bestanden bevat:

![1][1]

Open het bestand IKEErrors.txt en Hiermee wordt de volgende fout weer gegeven. Dit geeft aan dat er een probleem is met een onjuiste configuratie van de on-premises IKE-instelling.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

U kunt gedetailleerde informatie verkrijgen van de Scrubbed-wfpdiag.txt over de fout, zoals in dit geval wordt vermeld dat er een probleem is `ERROR_IPSEC_IKE_POLICY_MATCH` waardoor de verbinding niet goed werkt.

Een andere algemene onjuiste configuratie is het opgeven van onjuiste gedeelde sleutels. Als u in het voor gaande voor beeld verschillende gedeelde sleutels hebt opgegeven, wordt in de IKEErrors.txt de volgende fout weer gegeven: `Error: Authentication failed. Check shared key` .

Met de functie voor het oplossen van problemen met Azure Network Watcher kunt u uw VPN Gateway en verbinding met het gemak van een eenvoudige Power shell-cmdlet opsporen en oplossen. Momenteel ondersteunen we het vaststellen van de volgende voor waarden en werken ze met het toevoegen van meer voor waarden.

### <a name="gateway"></a>Gateway

| Fouttype | Reden | Logboek|
|---|---|---|
| NoFault | Als er geen fout is gedetecteerd. |Ja|
| GatewayNotFound | De gateway of gateway is niet gevonden. |Nee|
| PlannedMaintenance |  Er worden onderhoudswerkzaamheden uitgevoerd op het gatewayexemplaar.  |Nee|
| UserDrivenUpdate | Wanneer een gebruikers update wordt uitgevoerd. Dit kan een bewerking voor het wijzigen van de grootte zijn. | Nee |
| VipUnResponsive | Kan het primaire exemplaar van de gateway niet bereiken. Dit gebeurt wanneer de status test mislukt. | Nee |
| PlatformInActive | Er is een probleem met het platform. | Nee|
| ServiceNotRunning | De onderliggende service is niet actief. | Nee|
| NoConnectionsFoundForGateway | Er zijn geen verbindingen op de gateway. Dit is slechts een waarschuwing.| Nee|
| ConnectionsNotConnected | Geen van de verbindingen is verbonden. Dit is slechts een waarschuwing.| Ja|
| GatewayCPUUsageExceeded | Het huidige CPU-gebruik van de gateway is > 95%. | Ja |

### <a name="connection"></a>Verbinding

| Fouttype | Reden | Logboek|
|---|---|---|
| NoFault | Als er geen fout is gedetecteerd. |Ja|
| GatewayNotFound | De gateway of gateway is niet gevonden. |Nee|
| PlannedMaintenance | Er worden onderhoudswerkzaamheden uitgevoerd op het gatewayexemplaar.  |Nee|
| UserDrivenUpdate | Wanneer een gebruikers update wordt uitgevoerd. Dit kan een bewerking voor het wijzigen van de grootte zijn.  | Nee |
| VipUnResponsive | Kan het primaire exemplaar van de gateway niet bereiken. Deze fout treedt op wanneer de status test mislukt. | Nee |
| ConnectionEntityNotFound | De configuratie van de verbinding ontbreekt. | Nee |
| ConnectionIsMarkedDisconnected | De verbinding is gemarkeerd als ' losgekoppeld '. |Nee|
| ConnectionNotConfiguredOnGateway | De onderliggende service is niet geconfigureerd voor de verbinding. | Ja |
| ConnectionMarkedStandby | De onderliggende service is gemarkeerd als stand-by.| Ja|
| Verificatie | De vooraf gedeelde sleutel komt niet overeen. | Ja|
| PeerReachability | De peer gateway is niet bereikbaar. | Ja|
| IkePolicyMismatch | De peer gateway heeft een IKE-beleid dat niet wordt ondersteund door Azure. | Ja|
| WfpParse-fout | Er is een fout opgetreden bij het parseren van het WFP-logboek. |Ja|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het controleren van VPN Gateway connectiviteit met Power shell en Azure Automation door te bezoeken [VPN-gateways bewaken met Azure Network Watcher probleem oplossing](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png