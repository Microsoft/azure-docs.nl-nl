---
title: Azure Firewall manager in netwerk regels filteren (preview-versie)
description: FQDN-filtering gebruiken in netwerk regels
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87460147"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>FQDN-filtering in netwerk regels (preview-versie)

> [!IMPORTANT]
> FQDN-filtering in netwerk regels is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Een Fully Qualified Domain Name (FQDN) vertegenwoordigt een domein naam van een host of IP-adres (sen). U kunt FQDN-namen gebruiken in netwerk regels op basis van DNS-omzetting in Azure Firewall en firewall-beleid. Met deze mogelijkheid kunt u uitgaand verkeer filteren met elk TCP/UDP-protocol (met inbegrip van NTP, SSH, RDP en meer). U moet DNS-proxy inschakelen om FQDN-namen in uw netwerk regels te gebruiken. Zie [Azure firewall-beleid DNS-instellingen (preview)](dns-settings.md)voor meer informatie.

## <a name="how-it-works"></a>Uitleg

Wanneer u eenmaal hebt gedefinieerd welke DNS-server uw organisatie nodig heeft (Azure DNS of uw eigen aangepaste DNS), Azure Firewall de FQDN-naam naar een IP-adres (sen) vertaald op basis van de geselecteerde DNS-server. Deze vertaling gebeurt voor de verwerking van toepassingen en netwerk regels.

Wat is het verschil tussen het gebruik van domein namen in toepassings regels vergeleken met die van netwerk regels? 

- FQDN-filtering in toepassings regels voor HTTP/S en MSSQL is gebaseerd op een transparentproxy op toepassings niveau en de SNI-header. Zo kan het onderscheid worden tussen twee FQDN-adressen die worden omgezet naar hetzelfde IP-adres. Dit is niet het geval met FQDN-filtering in netwerk regels. Altijd toepassings regels gebruiken wanneer dit mogelijk is.
- In toepassings regels kunt u HTTP/S en MSSQL als uw geselecteerde protocollen gebruiken. In netwerk regels kunt u elk TCP/UDP-protocol gebruiken met uw doel-FQDN-namen.

## <a name="next-steps"></a>Volgende stappen

[DNS-instellingen Azure Firewall](dns-settings.md)
