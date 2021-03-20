---
title: Beveiliging in Azure Database for PostgreSQL-één server
description: Een overzicht van de beveiligings functies in Azure Database for PostgreSQL-één server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be042a0ec076538cf0f0d155667acea6f1ae19cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91710478"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Beveiliging in Azure Database for PostgreSQL-één server

Er zijn meerdere beveiligings lagen die beschikbaar zijn om de gegevens op uw Azure Database for PostgreSQL-server te beveiligen. In dit artikel vindt u een overzicht van deze beveiligings opties.

## <a name="information-protection-and-encryption"></a>Gegevens beveiliging en-versleuteling

### <a name="in-transit"></a>In-transit
Azure Database for PostgreSQL uw gegevens beveiligen door in-transit gegevens te versleutelen met Transport Layer Security. Versleuteling (SSL/TLS) wordt standaard afgedwongen.

### <a name="at-rest"></a>Op rest
De Azure Database for PostgreSQL-service gebruikt de door FIPS 140-2 gevalideerde cryptografische module voor opslag versleuteling van gegevens in rust. Gegevens, inclusief back-ups, worden op schijf versleuteld, met inbegrip van de tijdelijke bestanden die worden gemaakt tijdens het uitvoeren van query's. De service maakt gebruik van de AES 256-bits code ring opgenomen in azure Storage-versleuteling en de sleutels worden beheerd door het systeem. Opslagversleuteling is altijd actief en kan niet worden uitgeschakeld.


## <a name="network-security"></a>Netwerkbeveiliging
Verbindingen met een Azure Database for PostgreSQL-server worden eerst gerouteerd via een regionale gateway. De gateway heeft een openbaar toegankelijk IP-adres, terwijl de IP-adressen van de server zijn beveiligd. Ga naar het artikel over de [connectiviteits architectuur](concepts-connectivity-architecture.md)voor meer informatie over de gateway.  

Een nieuw gemaakte Azure Database for PostgreSQL server heeft een firewall waarmee alle externe verbindingen worden geblokkeerd. Hoewel ze de gateway bereiken, zijn ze niet gemachtigd om verbinding te maken met de server. 

### <a name="ip-firewall-rules"></a>IP-firewallregels
IP-firewall regels verlenen toegang tot servers op basis van het oorspronkelijke IP-adres van elke aanvraag. Zie het [overzicht van firewall regels](concepts-firewall-rules.md) voor meer informatie.

### <a name="virtual-network-firewall-rules"></a>Firewallregels voor virtueel netwerk
Met Service-eind punten van een virtueel netwerk breidt u de connectiviteit van uw virtuele netwerk uit via de Azure-backbone. Met regels voor virtuele netwerken kunt u uw Azure Database for PostgreSQL-server in staat stellen verbindingen van geselecteerde subnetten in een virtueel netwerk toe te staan. Zie het overzicht van het [virtuele netwerk service-eind punt](concepts-data-access-and-security-vnet.md)voor meer informatie.

### <a name="private-ip"></a>Privé IP-adres
Met persoonlijke koppeling kunt u verbinding maken met uw Azure Database for PostgreSQL één server in azure via een persoonlijk eind punt. Met Azure Private Link worden Azure-services binnen uw persoonlijke virtuele network (VNet) geplaatst. De PaaS-resources kunnen worden geopend met behulp van het privé-IP-adres, net zoals elke andere resource op het VNet. Zie voor meer informatie het [overzicht van persoonlijke koppelingen](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>Toegangsbeheer

Tijdens het maken van de Azure Database for PostgreSQL-server geeft u referenties op voor een beheerdersrol. Deze beheerdersrol kan worden gebruikt om aanvullende postgresql- [rollen](https://www.postgresql.org/docs/current/user-manag.html)te maken.

U kunt ook verbinding maken met de server met behulp van [Azure Active Directory (Aad)-verificatie](concepts-aad-authentication.md).


## <a name="threat-protection"></a>Bescherming tegen bedreigingen

U kunt ervoor kiezen om [geavanceerde bedreigingen te beveiligen](concepts-data-access-and-security-threat-protection.md) , waarmee afwijkende activiteiten worden gedetecteerd die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot servers of aanvallen te maken.

[Controle logboek registratie](concepts-audit.md) is beschikbaar om de activiteiten in uw data bases bij te houden. 

## <a name="migrating-from-oracle"></a>Migreren vanuit Oracle

Oracle ondersteunt Transparent Data Encryption (TDE) om tabel-en tabel ruimte gegevens te versleutelen. In azure voor PostgreSQL worden de gegevens automatisch versleuteld op verschillende lagen. Zie de sectie ' aan de rest ' op deze pagina en Raadpleeg ook de verschillende onderwerpen over beveiliging, waaronder door de [klant beheerde sleutels](./concepts-data-encryption-postgresql.md) en [infra structuur met dubbele code ring](./concepts-infrastructure-double-encryption.md). U kunt ook overwegen om de [pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html) -extensie te gebruiken die wordt ondersteund in [Azure voor postgresql](./concepts-extensions.md).

## <a name="next-steps"></a>Volgende stappen
- Firewall regels voor [IP-adressen](concepts-firewall-rules.md) of [virtuele netwerken](concepts-data-access-and-security-vnet.md) inschakelen
- Meer informatie over [Azure Active Directory verificatie](concepts-aad-authentication.md) in azure database for PostgreSQL
