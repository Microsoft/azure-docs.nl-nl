---
title: SSL/TLS-connectiviteit-Azure Database for MariaDB
description: Informatie voor het configureren van Azure Database for MariaDB en de bijbehorende toepassingen voor een juiste gebruik van SSL-verbindingen
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 6c4ee8c6d12a6350c319e40bbe30046af4932311
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550637"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>SSL/TLS-connectiviteit in Azure Database for MariaDB
Azure Database for MariaDB ondersteunt het verbinden van uw database server met client toepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

>[!NOTE]
> Op basis van de feedback van klanten hebben we de afschaffing van het basis certificaat uitgebreid voor onze bestaande Baltimore basis certificerings instantie van 15 februari 2021 (02/15/2021).

> [!IMPORTANT] 
> Het SSL-basis certificaat is ingesteld op verlopen vanaf 15 februari 2021 (02/15/2021). Werk uw toepassing bij om het [nieuwe certificaat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)te gebruiken. Zie [geplande certificaat updates](concepts-certificate-rotation.md) voor meer informatie.

## <a name="default-settings"></a>Standaardinstellingen
De database service moet standaard zodanig worden geconfigureerd dat SSL-verbindingen worden vereist wanneer verbinding wordt gemaakt met MariaDB.  U wordt aangeraden de SSL-optie zo mogelijk niet uit te scha kelen.

Wanneer u een nieuwe Azure Database for MariaDB-server inricht via de Azure Portal en CLI, wordt het afdwingen van SSL-verbindingen standaard ingeschakeld.

In sommige gevallen vereist toepassingen een lokaal certificaat bestand dat is gegenereerd op basis van een certificaat bestand van een vertrouwde certificerings instantie (CA) om veilig verbinding te kunnen maken. Momenteel kunnen klanten **alleen** het vooraf gedefinieerde certificaat gebruiken om verbinding te maken met een Azure database for MariaDB-server die zich bevindt in https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Op dezelfde manier verwijzen de volgende koppelingen naar de certificaten voor servers in soevereine Clouds: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)en [Azure Duitsland](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

Verbindings reeksen voor verschillende programmeer talen worden weer gegeven in de Azure Portal. Deze verbindings reeksen bevatten de vereiste SSL-para meters om verbinding te maken met uw data base. Selecteer uw server in de Azure Portal. Selecteer onder de kop **instellingen** de **verbindings reeksen**. De SSL-para meter varieert op basis van de connector, bijvoorbeeld "SSL = True" of "sslmode = vereist" of "sslmode = required" en andere variaties.

Zie [SSL configureren](howto-configure-ssl.md)voor meer informatie over het in-of uitschakelen van SSL-verbinding tijdens het ontwikkelen van een toepassing.

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>TLS-afdwinging in Azure Database for MariaDB

Azure Database for MariaDB ondersteunt versleuteling voor clients die verbinding maken met uw database server met behulp van Transport Layer Security (TLS). TLS is een industrie standaard protocol dat beveiligde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

### <a name="tls-settings"></a>TLS-instellingen

Azure Database for MariaDB biedt de mogelijkheid om de TLS-versie voor de client verbindingen af te dwingen. Als u de TLS-versie wilt afdwingen, gebruikt u de instelling **minimale TLS-versie** optie. De volgende waarden zijn toegestaan voor deze optie-instelling:

|  Minimale TLS-instelling             | TLS-versie van client wordt ondersteund                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (standaard) | Geen TLS vereist                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 en hoger         |
| TLS1_1                           | TLS 1,1, TLS 1,2 en hoger              |
| TLS1_2                           | TLS-versie 1,2 en hoger                  |


Als u bijvoorbeeld de waarde van de minimum versie van TLS-instelling instelt op TLS 1,0, betekent dit dat uw server verbindingen toestaat van clients die gebruikmaken van TLS 1,0, 1,1 en 1.2 +. U kunt dit ook instellen op 1,2 zodat u alleen verbindingen van clients met TLS 1.2 + kunt toestaan en alle verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd.

> [!Note] 
> Azure Database for MariaDB wordt standaard geen TLS-versie (de instelling) afgedwongen `TLSEnforcementDisabled` .
>
> Wanneer u een minimum versie van TLS afdwingt, kunt u de minimale afdwinging van de versie niet later uitschakelen.

Zie [TLS-instelling configureren](howto-tls-configurations.md)voor meer informatie over het instellen van de TLS-instelling voor uw Azure database for MariaDB.

## <a name="cipher-support-by-azure-database-for-mariadb"></a>Ondersteuning voor code ring door Azure Database for MariaDB

Als onderdeel van de SSL/TLS-communicatie worden de coderings suites gevalideerd en worden alleen coderings methoden toegestaan om te communiceren met de database server. De coderings Suite validatie wordt beheerd in de laag van de [Gateway](concepts-connectivity-architecture.md#connectivity-architecture) en niet expliciet op het knoop punt zelf. Als de coderings suites niet overeenkomen met een van de hieronder vermelde suites, worden binnenkomende client verbindingen afgewezen.

### <a name="cipher-suite-supported"></a>Coderings Suite ondersteund

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [firewall regels voor servers](concepts-firewall-rules.md)
- Meer informatie over het [configureren van SSL](howto-configure-ssl.md)
- Meer informatie over het [configureren van TLS](howto-tls-configurations.md)
