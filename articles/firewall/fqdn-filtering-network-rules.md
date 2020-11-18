---
title: Azure Firewall FQDN-filtering in netwerk regels
description: Azure Firewall FQDN-filtering gebruiken in netwerk regels
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695942"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>FQDN-filtering gebruiken in netwerk regels

Een Fully Qualified Domain Name (FQDN) vertegenwoordigt een domein naam van een host of IP-adres (sen). U kunt FQDN-namen gebruiken in netwerk regels op basis van DNS-omzetting in Azure Firewall en firewall-beleid. Met deze mogelijkheid kunt u uitgaand verkeer filteren met elk TCP/UDP-protocol (met inbegrip van NTP, SSH, RDP en meer). U moet DNS-proxy inschakelen om FQDN-namen in uw netwerk regels te gebruiken. Zie [Azure firewall DNS-instellingen](dns-settings.md)voor meer informatie.

> [!NOTE]
> Daarom biedt FQDN-filtering geen ondersteuning voor joker tekens.

## <a name="how-it-works"></a>Uitleg

Wanneer u eenmaal hebt gedefinieerd welke DNS-server uw organisatie nodig heeft (Azure DNS of uw eigen aangepaste DNS), Azure Firewall de FQDN-naam naar een IP-adres (sen) vertaald op basis van de geselecteerde DNS-server. Deze vertaling gebeurt voor de verwerking van toepassingen en netwerk regels.

Wanneer er een nieuwe DNS-omzetting plaatsvindt, worden nieuwe IP-adressen toegevoegd aan de firewall regels. Oude IP-adressen die niet langer worden geretourneerd door de DNS-server, verlopen in 15 minuten. Azure Firewall regels worden elke 15 seconden bijgewerkt op basis van de DNS-omzetting van de FQDN-namen in netwerk regels.

### <a name="differences-in-application-rules-vs-network-rules"></a>Verschillen in toepassings regels versus netwerk regels

- FQDN-filtering in toepassings regels voor HTTP/S en MSSQL is gebaseerd op een transparentproxy op toepassings niveau en de SNI-header. Zo kan het onderscheid worden tussen twee FQDN-adressen die worden omgezet naar hetzelfde IP-adres. Dit is niet het geval met FQDN-filtering in netwerk regels. 

   Altijd toepassings regels gebruiken wanneer mogelijk:
     - Als het Protocol HTTP/S of MSSQL is, gebruikt u toepassings regels voor FQDN-filtering.
   - Voor andere protocollen dan HTTP/S of MSSQL kunt u toepassings-of netwerk regels gebruiken voor het filteren van FQDN-namen.

## <a name="next-steps"></a>Volgende stappen

[DNS-instellingen Azure Firewall](dns-settings.md)
