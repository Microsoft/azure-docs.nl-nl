---
title: Servers-Azure Database for MariaDB
description: In dit onderwerp vindt u overwegingen en richt lijnen voor het werken met Azure Database for MariaDB-servers.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: abe17556d9ff62c44a33bfe4c4546a284785522e
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664126"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Server concepten in Azure Database for MariaDB
In dit artikel vindt u overwegingen en richt lijnen voor het werken met Azure Database for MariaDB-servers.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Wat is een Azure Database for MariaDB-server?

Een Azure Database for MariaDB server is een centraal beheer punt voor meerdere data bases. Het is dezelfde MariaDB-server constructie die u mogelijk kent in de on-premises wereld. Met name de Azure Database for MariaDB-service wordt beheerd, levert prestatie garanties en biedt toegang en functies op server niveau.

Een Azure Database for MariaDB-server:

- Wordt gemaakt binnen een Azure-abonnement.
- Is de bovenliggende resource voor data bases.
- Biedt een naam ruimte voor data bases.
- Is een container met sterke levens duur semantiek: een server verwijderen en de Inge sloten data bases verwijdert.
- Groepeert bronnen in een regio.
- Biedt een verbindings eindpunt voor toegang tot de server en data base.
- Voorziet in het bereik voor beheer beleid dat van toepassing is op de bijbehorende data bases: aanmelden, firewall, gebruikers, rollen, configuraties, enzovoort.
- Is beschikbaar in de MariaDB-Engine versie 10,2. Zie [supported Azure database for MariaDB data base versions](./concepts-supported-versions.md)(Engelstalig) voor meer informatie.

Op een Azure Database for MariaDB-server kunt u een of meerdere databases maken. U kunt ervoor kiezen om één data base per server te maken voor het gebruik van alle resources of om meerdere data bases te maken om de resources te delen. De prijzen zijn gestructureerd per server, op basis van de configuratie van de prijs categorie, vCores en opslag (GB). Zie [prijs categorieën](./concepts-pricing-tiers.md)voor meer informatie.

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hoe kan ik een Azure Database for MariaDB server beveiligen?

De volgende elementen helpen veilige toegang tot uw data base te garanderen.

|||
| :--| :--|
| **Verificatie en autorisatie** | Azure Database for MariaDB-server ondersteunt native MySQL-verificatie. U kunt verbinding maken met en verifiëren met een server met de beheerders aanmelding van de server. |
| **Protocol** | De service ondersteunt een op berichten gebaseerd protocol dat wordt gebruikt door MySQL. |
| **TCP/IP** | Het protocol wordt ondersteund via TCP/IP en via Unix-domein sockets. |
| **Firewall** | Ter bescherming van uw gegevens voor komt een firewall regel alle toegang tot uw database server, totdat u opgeeft welke computers zijn gemachtigd. Zie [Azure database for MariaDB Server firewall-regels](./concepts-firewall-rules.md). |
| **SSL** | De service biedt ondersteuning voor het afdwingen van SSL-verbindingen tussen uw toepassingen en uw database server. Zie [SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure database for MariaDB](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Een Azure Database for MariaDB stoppen/starten (preview)
Azure Database for MariaDB biedt u de mogelijkheid om de server te **stoppen** wanneer deze niet wordt gebruikt en de server **te starten** wanneer u de activiteit hervat. Dit is in feite van toepassing op het besparen van kosten op de database servers en betaalt alleen voor de resource wanneer deze wordt gebruikt. Dit wordt nog belang rijker voor werk belastingen voor dev-test en wanneer u alleen de-server gebruikt voor een deel van de dag. Wanneer u de server stopt, worden alle actieve verbindingen verwijderd. Wanneer u de server later weer online wilt zetten, kunt u de [Azure Portal](../mysql/how-to-stop-start-server.md) of [cli](../mysql/how-to-stop-start-server.md)gebruiken.

Wanneer de server zich in de status **gestopt** bevindt, wordt de compute van de server niet gefactureerd. Opslag blijft echter gefactureerd omdat de opslag ruimte van de server bewaard blijft om ervoor te zorgen dat gegevens bestanden beschikbaar zijn wanneer de server weer wordt gestart.

> [!IMPORTANT]
> Wanneer u de server **stopt** , blijft deze voor de volgende zeven dagen in een stretch-status. Als u deze niet hand matig **Start** , wordt de server aan het eind van 7 dagen automatisch gestart. U kunt ervoor kiezen om deze opnieuw te **stoppen** als u de-server niet gebruikt.

Tijdens de tijd server wordt gestopt, kunnen er geen beheer bewerkingen worden uitgevoerd op de server. Als u de configuratie-instellingen op de server wilt wijzigen, moet u [de server starten](../mysql/how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Beperkingen van de bewerking stoppen/starten
- Niet ondersteund bij het lezen van replica configuraties (zowel bron-als replica).

## <a name="how-do-i-manage-a-server"></a>Hoe kan ik een server beheren?
U kunt Azure Database for MariaDB servers beheren door gebruik te maken van de Azure Portal of de Azure CLI.

## <a name="next-steps"></a>Volgende stappen
- Zie [overzicht van Azure database for MariaDB](./overview.md) voor een overzicht van de service
- Zie [service lagen](./concepts-pricing-tiers.md) voor informatie over specifieke resource quota en beperkingen op basis **van uw servicelaag**.

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
