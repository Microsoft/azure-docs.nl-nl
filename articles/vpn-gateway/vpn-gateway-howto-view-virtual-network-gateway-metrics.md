---
title: VPN Gateway metrische gegevens voor Azure weer geven
description: Stappen om VPN Gateway metrische gegevens weer te geven
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 4b33af3e64726e124373f57920836bce145cd891
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89443174"
---
# <a name="view-vpn-gateway-metrics"></a>Metrische gegevens van VVPN Gateway weergeven

U kunt Azure VPN-gateways bewaken met behulp van Azure Monitor. In dit artikel worden metrische gegevens beschreven die beschikbaar zijn via de portal. Metrische gegevens zijn licht gewicht en kunnen bijna realtime-scenario's ondersteunen, waardoor ze nuttig zijn voor waarschuwingen en snelle detectie van problemen.


|**Meting**   | **Eenheid** | **Granulariteit** | **Beschrijving** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| Bytes/s  | 5 minuten| Gemiddelde gecombineerde bandbreedte gebruik van alle site-naar-site-verbindingen op de gateway.     |
|**P2SBandwidth**| Bytes/s  | 1 minuut  | Gemiddelde gecombineerde bandbreedte gebruik van alle punt-naar-site-verbindingen op de gateway.    |
|**P2SConnectionCount**| Count  | 1 minuut  | Aantal Point-to-site-verbindingen op de gateway.   |
|**TunnelAverageBandwidth** | Bytes/s    | 5 minuten  | Gemiddeld bandbreedte gebruik van tunnels die zijn gemaakt op de gateway. |
|**TunnelEgressBytes** | Bytes | 5 minuten | Uitgaand verkeer op tunnels die zijn gemaakt op de gateway.   |
|**TunnelEgressPackets** | Count | 5 minuten | Aantal uitgaande pakketten op tunnels die zijn gemaakt op de gateway.   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 minuten | Aantal uitgaande pakketten dat is verwijderd op tunnels die zijn veroorzaakt door een niet-overeenkomend verkeers selectie. |
|**TunnelIngressBytes** | Bytes | 5 minuten | Binnenkomend verkeer op tunnels die zijn gemaakt op de gateway.   |
|**TunnelIngressPackets** | Count | 5 minuten | Aantal inkomende pakketten op tunnels die zijn gemaakt op de gateway.   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 minuten | Aantal binnenkomende pakketten dat is verwijderd op tunnels die zijn veroorzaakt door een niet-overeenkomend Traffic-selector. |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>De volgende stappen helpen u metrische gegevens te zoeken en weer te geven:

1. Ga naar de gateway bron van het virtuele netwerk in de portal
2. Selecteer **overzicht** om de totale tunnel ingangen en uitvoer gegevens te bekijken.

   ![Selecties voor het maken van een waarschuwings regel](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "Weergave")

3. Om een van de hierboven vermelde gegevens weer te geven. Klik op het gedeelte **metrische gegevens** onder de resource van de virtuele netwerk gateway en selecteer de metriek in de vervolg keuzelijst.

   ![De knop selecteren en de VPN-gateway in de lijst met resources](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Selecteer")

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen instellen voor VPN gateway metrische gegevens](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)voor het configureren van waarschuwingen voor metrische gegevens van de tunnel.

Zie [waarschuwingen instellen op VPN gateway bron logboeken](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)voor informatie over het configureren van waarschuwingen voor tunnel bron Logboeken.
