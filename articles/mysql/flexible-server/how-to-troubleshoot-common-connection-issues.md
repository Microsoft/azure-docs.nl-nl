---
title: Verbindings problemen oplossen-Azure Database for MySQL-flexibele server
description: Meer informatie over het oplossen van verbindings problemen met Azure Database for MySQL flexibele server.
keywords: MySQL-verbinding, connection string, connectiviteits problemen, persistente fout, verbindings fout
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: bdd52ba77fd9a65ce27985ff3c86a93fc887ddf9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109979"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Verbindings problemen met Azure Database for MySQL-flexibele server oplossen

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Verbindings problemen kunnen worden veroorzaakt door diverse dingen, waaronder:

* Firewallinstellingen
* Time-out van verbinding
* Onjuiste aanmeldings gegevens
* De maximum limiet is bereikt op enkele Azure Database for MySQL flexibele server bronnen

In dit artikel wordt beschreven hoe u een aantal veelvoorkomende fouten en stappen kunt oplossen om deze fouten op te lossen.

## <a name="troubleshoot-common-errors"></a>Veelvoorkomende fouten oplossen

Als de toepassing permanent geen verbinding kan maken met Azure Database for MySQL flexibele server, duidt dit meestal op een probleem met een van de volgende:

* Versleutelde verbinding met TLS/SSL: flexibele server ondersteunt alleen versleutelde verbindingen met behulp van Transport Layer Security (TLS 1,2) en alle **binnenkomende verbindingen met tls 1,0 en tls 1,1 worden geweigerd**. U kunt de TLS-versie niet uitschakelen of wijzigen. Meer informatie over [versleutelde connectiviteit met Transport Layer Security (TLS 1,2) in azure database for MySQL-flexibele server](./how-to-connect-tls-ssl.md).
- Flexibele server in *Private Access (VNet-integratie)*: Zorg ervoor dat u verbinding maakt vanuit hetzelfde virtuele netwerk als de flexibele server. Raadpleeg [virtueel netwerk in Azure Database for MySQL flexibele server]<!--(./concepts-networking-virtual-network.md)-->
- Flexibele server met *open bare toegang (toegestane IP-adressen)*, zorg ervoor dat de firewall zodanig is geconfigureerd dat verbindingen van uw client worden toegestaan. Raadpleeg voor [het maken en beheren van flexibele server firewall regels met behulp van de Azure Portal](./how-to-manage-firewall-portal.md).
* Firewall configuratie van de client: de firewall op uw client moet verbindingen met uw database server toestaan. IP-adressen en poorten van de server die u niet wilt toestaan en toepassings namen zoals MySQL in sommige firewalls.
* Gebruikers fout: mogelijk hebt u een type verbindings parameter, zoals de server naam in de connection string.

### <a name="resolve-connectivity-issues"></a>Verbindings problemen oplossen

* Raadpleeg de [versleutelde connectiviteit met Transport Layer Security (TLS 1,2) in azure database for MySQL-flexibele server](./how-to-connect-tls-ssl.md) --> voor meer informatie over versleutelde verbindingen.
* Als u **open bare toegang (toegestane IP-adressen)** gebruikt, moet u [firewall regels](./how-to-manage-firewall-portal.md) instellen om het client-IP-adres toe te staan. Stel voor tijdelijke test doeleinden alleen een firewall regel in met behulp van 0.0.0.0 als het begin-IP-adres en gebruik 255.255.255.255 als het laatste IP-adres. Hiermee wordt de server op alle IP-adressen geopend. Als uw connectiviteits probleem hiermee wordt opgelost, verwijdert u deze regel en maakt u een firewall regel voor een adequaat beperkt IP-adres of adres bereik.
* Controleer op alle firewalls tussen de client en Internet of poort 3306 is geopend voor uitgaande verbindingen.
* Controleer uw connection string en andere Verbindings instellingen. Raadpleeg de vooraf gedefinieerde verbindings reeksen in de pagina **verbindings reeksen** die beschikbaar zijn voor uw server in de Azure portal voor algemene talen.

## <a name="next-steps"></a>Volgende stappen
- [Gebruik MySQL Workbench om verbinding te maken en gegevens op te vragen in azure database for MySQL flexibele server](./connect-workbench.md).
- [Gebruik PHP om verbinding te maken en gegevens op te vragen in azure database for MySQL flexibele server](./connect-php.md).
- [Gebruik python om verbinding te maken en gegevens op te vragen in azure database for MySQL flexibele server](./connect-python.md).
