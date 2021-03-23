---
title: Problemen met Azure VPN Gateway oplossen met Diagnostische logboeken
description: Problemen met Azure VPN Gateway oplossen met Diagnostische logboeken
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771993"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Problemen met Azure VPN Gateway oplossen met Diagnostische logboeken

In dit artikel vindt u meer informatie over de verschillende logboeken die beschikbaar zijn voor VPN Gateway diagnose en hoe u deze kunt gebruiken om problemen met de VPN-gateway effectief op te lossen.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

De volgende logboeken zijn beschikbaar in Azure:

|***Naam** _ | _ *_Beschrijving_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Bevat Diagnostische logboeken voor configuratie gebeurtenissen van de gateway, primaire wijzigingen en onderhouds gebeurtenissen. |
|**TunnelDiagnosticLog** | Bevat status wijzigings gebeurtenissen voor de tunnel. Tunnel Connect/Disconnect-gebeurtenissen hebben een samenvattings reden voor de status wijziging, indien van toepassing. |
|**RouteDiagnosticLog** | Registreert wijzigingen aan statische routes en BGP-gebeurtenissen die optreden op de gateway. |
|**IKEDiagnosticLog** | Registreert IKE-besturings berichten en gebeurtenissen op de gateway. |
|**P2SDiagnosticLog** | Registreert Point-to-site Control-berichten en gebeurtenissen op de gateway. |

U ziet dat er meerdere kolommen beschikbaar zijn in deze tabellen. In dit artikel worden alleen de meest relevante voor waarden gepresenteerd voor eenvoudiger logboek gebruik.

## <a name="set-up-logging"></a><a name="setup"></a>Logboek registratie instellen

Zie [waarschuwingen instellen voor gebeurtenissen in het diagnostische logboek van VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log)voor meer informatie over het instellen van diagnostische logboek gebeurtenissen van Azure VPN gateway met behulp van Azure log Analytics.

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

Wijzigingen in de configuratie worden gecontroleerd in de tabel **GatewayDiagnosticLog** . Het kan enkele minuten duren voordat wijzigingen die u uitvoert, worden weer gegeven in de logboeken.

Hier ziet u een voor beeld van een query als verwijzing.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

Met deze query op **GatewayDiagnosticLog** worden meerdere kolommen weer gegeven.

|***Naam** _ | _ *_Beschrijving_** |
|---        | ---               |
|**TimeGenerated** | de tijds tempel van elke gebeurtenis, in UTC-tijd zone.|
|**OperationName** |de gebeurtenis die heeft plaatsgevonden. Dit kan een van *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration* zijn.|
|**Bericht** | de details van wat er gebeurt, en geeft een overzicht van geslaagde/mislukte resultaten.|

In het volgende voor beeld ziet u hoe de activiteit wordt geregistreerd wanneer een nieuwe configuratie werd toegepast:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Voor beeld van een set-gateway bewerking die wordt weer gegeven in GatewayDiagnosticLog.":::


U ziet dat een SetGatewayConfiguration wordt geregistreerd elke keer dat er een configuratie wordt gewijzigd op een VPN Gateway of een lokale netwerk gateway.
Door Kruis te verwijzen naar de resultaten van de **GatewayDiagnosticLog** -tabel met die van de **TunnelDiagnosticLog** -tabel kan ons bepalen of een tunnel connectiviteits fout op hetzelfde moment is gestart als een configuratie werd gewijzigd of een onderhoud plaatsvond. Als dat het geval is, is er een goede aanwijzer voor de mogelijke hoofd oorzaak.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

De **TunnelDiagnosticLog** -tabel is zeer nuttig om de historische connectiviteits status van de tunnel te controleren.

Hier ziet u een voor beeld van een query als verwijzing.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

Met deze query op **TunnelDiagnosticLog** worden meerdere kolommen weer gegeven.


|***Naam** _ | _ *_Beschrijving_** |
|---        | ---               |
|**TimeGenerated** | de tijds tempel van elke gebeurtenis, in UTC-tijd zone.|
|**OperationName** | de gebeurtenis die heeft plaatsgevonden. De naam kan *TunnelConnected* of *TunnelDisconnected* zijn.|
| **Exemplaar \_ s** | de instantie van de gateway die de gebeurtenis heeft geactiveerd. Dit kan GatewayTenantWorker \_ in \_ 0 of GatewayTenantWorker \_ in \_ 1 zijn. Dit zijn de namen van de twee exemplaren van de gateway.|
| **Resource** | Hiermee wordt de naam van de VPN-gateway aangegeven. |
| **ResourceGroup** | Hiermee wordt de resource groep aangegeven waarin de gateway zich bevindt.|


Voorbeelduitvoer:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Voor beeld van een met een tunnel verbonden gebeurtenis gezien in TunnelDiagnosticLog.":::


De **TunnelDiagnosticLog** is zeer nuttig voor het oplossen van problemen met eerdere gebeurtenissen over onverwachte VPN-verbindingen. De licht gewichts categorie biedt de mogelijkheid om grote Peri Oden over enkele dagen te analyseren met weinig inspanning.
Pas nadat u de tijds tempel van een verbroken verbinding hebt geïdentificeerd, kunt u overstappen op de gedetailleerde analyse van de **IKEdiagnosticLog** -tabel om de reden van het verbreken van de verbinding te verrijken. Dit zijn IPSec-gerelateerde.


Tips voor probleemoplossing:
- Als er in een paar seconden een gebeurtenis voor het verbreken van de verbinding wordt weer geven in één gateway-exemplaar, gevolgd door een verbindings gebeurtenis in het **andere** exemplaar van de gateway, bekijkt u een failover van de gateway. Dit is doorgaans een verwacht gedrag als gevolg van onderhoud aan een gateway-exemplaar. Zie [over Azure VPN gateway redundantie](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy)voor meer informatie over dit gedrag.
- Hetzelfde gedrag treedt op als u opzettelijk een gateway reset uitvoert op de Azure-kant, waardoor het actieve gateway-exemplaar opnieuw wordt opgestart. Zie [een VPN gateway opnieuw instellen](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic)voor meer informatie over dit gedrag.
- Als er in een paar seconden een gebeurtenis voor het verbreken van de verbinding wordt weer gegeven in één gateway-exemplaar, gevolgd door een verbindings gebeurtenis in **hetzelfde** gateway-exemplaar, is het mogelijk dat er een netwerk fout optreedt die een DPD-out heeft veroorzaakt, of dat de verbinding niet is verzonden door het on-premises apparaat.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

De **RouteDiagnosticLog** -tabel traceert de activiteit voor statisch gewijzigde routes of routes die via BGP worden ontvangen.

Hier ziet u een voor beeld van een query als verwijzing.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Met deze query op **RouteDiagnosticLog** worden meerdere kolommen weer gegeven.

|***Naam** _ | _ *_Beschrijving_** |
|---        | ---               |
|**TimeGenerated** | de tijds tempel van elke gebeurtenis, in UTC-tijd zone.|
|**OperationName** | de gebeurtenis die heeft plaatsgevonden. Dit kan een van *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent* zijn.|
| **Bericht** | de details van de werking van de bewerking.|

De uitvoer geeft nuttige informatie weer over BGP-peers verbonden/verbroken en gerouteerde routes.

Voorbeeld:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Voor beeld van BGP route Exchange-activiteit die wordt weer gegeven in RouteDiagnosticLog.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

De **IKEDiagnosticLog** -tabel biedt uitgebreide logboek registratie voor fout opsporing voor IKE/IPSec. Dit is zeer nuttig om te controleren bij het oplossen van problemen met de verbinding of het verbinden van VPN-scenario's.

Hier ziet u een voor beeld van een query als verwijzing.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

Met deze query op **IKEDiagnosticLog** worden meerdere kolommen weer gegeven.


|***Naam** _ | _ *_Beschrijving_** |
|---        | ---               |
|**TimeGenerated** | de tijds tempel van elke gebeurtenis, in UTC-tijd zone.|
| **RemoteIP** | het IP-adres van het on-premises VPN-apparaat. In praktijk scenario's is het nuttig om te filteren op het IP-adres van het betreffende on-premises apparaat. er zijn er meer dan één. |
|**LocalIP** | het IP-adres van de VPN Gateway het oplossen van problemen. In praktijk scenario's is het handig om te filteren op het IP-adres van de relevante VPN-gateway. er zijn er meer dan één in uw abonnement. |
|**Gebeurtenis** | bevat een diagnostisch bericht dat nuttig is voor het oplossen van problemen. Normaal gesp roken beginnen ze met een tref woord en verwijzen we naar de acties die worden uitgevoerd door de Azure-gateway: **\[ verzenden \]** geeft aan dat een gebeurtenis is veroorzaakt door een IPSec-pakket dat door de Azure-gateway is verzonden.  **\[ Ontvangen \]** een gebeurtenis die het gevolg is van een pakket dat is ontvangen van een on-premises apparaat.  **\[ Local \]** geeft een actie aan die lokaal door de Azure-gateway wordt uitgevoerd. |


U ziet dat de kolommen RemoteIP, LocalIP en event niet aanwezig zijn in de oorspronkelijke kolom lijst op AzureDiagnostics-data base, maar worden toegevoegd aan de query door de uitvoer van de kolom ' Message ' te parseren om de analyse te vereenvoudigen.

Tips voor probleem oplossing:

- Als u het begin van een IPSec-onderhandeling wilt identificeren, moet u het oorspronkelijke SA \_ init-bericht vinden. Dit bericht kan door beide zijden van de tunnel worden verzonden. Degene die het eerste pakket verzendt, wordt ' initiator ' genoemd in IPsec-terminologie, terwijl de andere kant de ' responder ' wordt. Het eerste SA \_ init-bericht is altijd de naam waar rCookie = 0.

- Als de IPsec-tunnel niet tot stand kan worden gebracht, zal Azure elke paar seconden blijven proberen. Daarom is het oplossen van problemen met ' VPN-down ' zeer handig in IKEdiagnosticLog omdat u niet hoeft te wachten op een specifiek tijdstip om het probleem te reproduceren. Daarnaast is de fout in theorie altijd hetzelfde als we proberen, zodat u op elk gewenst moment net kunt inzoomen op één ' voor beeld ' van een mislukte onderhandeling.

- De SA \_ init bevat de IPSec-para meters die de peer wil gebruiken voor deze IPsec-onderhandeling. Het officiële document   
[Standaard IPSec/IKE-para meters](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) geeft de IPSec-para meters weer die door de Azure-gateway worden ondersteund met de standaard instellingen.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

De laatst beschik bare tabel voor de diagnostische gegevens van de VPN-verbinding is **P2SDiagnosticLog**. Deze tabel traceert de activiteit voor punt-naar-site.

Hier ziet u een voor beeld van een query als verwijzing.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

Met deze query op **P2SDiagnosticLog** worden meerdere kolommen weer gegeven.

|***Naam** _ | _ *_Beschrijving_** |
|---        | ---               |
|**TimeGenerated** | de tijds tempel van elke gebeurtenis, in UTC-tijd zone.|
|**OperationName** | de gebeurtenis die heeft plaatsgevonden. Wordt *P2SLogEvent*.|
| **Bericht** | de details van de werking van de bewerking.|

In de uitvoer ziet u alle site-instellingen die op de gateway zijn toegepast, evenals het IPsec-beleid dat is geïmplementeerd.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Voor beeld van punt-naar-site verbinding gezien in P2SDiagnosticLog.":::

Wanneer een client verbinding maakt via IKEv2 of OpenVPN Point to site, registreert de tabel ook pakket activiteiten, EAP/RADIUS-conversaties en geslaagde/mislukte resultaten door de gebruiker.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Voor beeld van EAP-verificatie gezien in P2SDiagnosticLog.":::

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen instellen op VPN gateway bron logboeken](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)voor informatie over het configureren van waarschuwingen voor tunnel bron Logboeken.

