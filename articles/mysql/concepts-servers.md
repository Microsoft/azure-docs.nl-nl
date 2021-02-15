---
title: Server concepten-Azure Database for MySQL
description: In dit onderwerp vindt u overwegingen en richt lijnen voor het werken met Azure Database for MySQL-servers.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed6d5d676fd2c6eefd3288b7609446eb61611ed6
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517974"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Server concepten in Azure Database for MySQL

In dit artikel vindt u overwegingen en richt lijnen voor het werken met Azure Database for MySQL-servers.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Wat is een Azure Database for MySQL-server?

Een Azure Database for MySQL server is een centraal beheer punt voor meerdere data bases. Het is dezelfde MySQL-server constructie die u mogelijk kent in de on-premises wereld. Met name de Azure Database for MySQL-service wordt beheerd, levert prestatie garanties en biedt toegang en functies op server niveau.

Een Azure Database for MySQL-server:

- Wordt gemaakt binnen een Azure-abonnement.
- Is de bovenliggende resource voor data bases.
- Biedt een naam ruimte voor data bases.
- Is een container met sterke levens duur semantiek: een server verwijderen en de Inge sloten data bases verwijdert.
- Groepeert bronnen in een regio.
- Biedt een verbindings eindpunt voor toegang tot de server en data base.
- Voorziet in het bereik voor beheer beleid dat van toepassing is op de bijbehorende data bases: aanmelden, firewall, gebruikers, rollen, configuraties, enzovoort.
- Is beschikbaar in meerdere versies. Zie [supported Azure database for MySQL data base versions](./concepts-supported-versions.md)(Engelstalig) voor meer informatie.

Op een Azure Database voor MySQL-server kunt u een of meerdere databases maken. U kunt ervoor kiezen om één data base per server te maken voor het gebruik van alle resources of om meerdere data bases te maken om de resources te delen. De prijzen zijn gestructureerd per server, op basis van de configuratie van de prijs categorie, vCores en opslag (GB). Zie [prijs categorieën](./concepts-pricing-tiers.md)voor meer informatie.

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hoe kan ik verbinding maken en verifiëren met een Azure Database for MySQL-server?

De volgende elementen helpen veilige toegang tot uw data base te garanderen.

|     |     |
| :-- | :-- |
| **Verificatie en autorisatie** | Azure Database for MySQL-server ondersteunt native MySQL-verificatie. U kunt verbinding maken met en verifiëren met een server met de beheerders aanmelding van de server. |
| **Protocol** | De service ondersteunt een op berichten gebaseerd protocol dat wordt gebruikt door MySQL. |
| **TCP/IP** | Het protocol wordt ondersteund via TCP/IP en via Unix-domein sockets. |
| **Firewall** | Ter bescherming van uw gegevens voor komt een firewall regel alle toegang tot uw database server, totdat u opgeeft welke computers zijn gemachtigd. Zie [Azure database for MySQL Server firewall-regels](./concepts-firewall-rules.md). |
| **SSL** | De service biedt ondersteuning voor het afdwingen van SSL-verbindingen tussen uw toepassingen en uw database server.  Zie [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure-database voor MySQL) voor meer informatie. |

## <a name="stopstart-an-azure-database-for-mysql"></a>Een Azure Database for MySQL stoppen/starten

Azure Database for MySQL biedt u de mogelijkheid om de server te **stoppen** wanneer deze niet wordt gebruikt en de server **te starten** wanneer u de activiteit hervat. Dit is in feite van toepassing op het besparen van kosten op de database servers en betaalt alleen voor de resource wanneer deze wordt gebruikt. Dit wordt nog belang rijker voor werk belastingen voor dev-test en wanneer u alleen de-server gebruikt voor een deel van de dag. Wanneer u de server stopt, worden alle actieve verbindingen verwijderd. Wanneer u de server later weer online wilt zetten, kunt u de [Azure Portal](how-to-stop-start-server.md) of [cli](how-to-stop-start-server.md)gebruiken.

Wanneer de server zich in de status **gestopt** bevindt, wordt de compute van de server niet gefactureerd. Opslag blijft echter gefactureerd omdat de opslag ruimte van de server bewaard blijft om ervoor te zorgen dat gegevens bestanden beschikbaar zijn wanneer de server weer wordt gestart.

> [!IMPORTANT]
> Wanneer u de server **stopt** , blijft deze voor de volgende zeven dagen in een stretch-status. Als u deze niet hand matig **Start** , wordt de server aan het eind van 7 dagen automatisch gestart. U kunt ervoor kiezen om deze opnieuw te **stoppen** als u de-server niet gebruikt.

Tijdens de tijd server wordt gestopt, kunnen er geen beheer bewerkingen worden uitgevoerd op de server. Als u de configuratie-instellingen op de server wilt wijzigen, moet u [de server starten](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>Beperkingen van de bewerking stoppen/starten
- Niet ondersteund bij het lezen van replica configuraties (zowel bron-als replica).

## <a name="how-do-i-manage-a-server"></a>Hoe kan ik een server beheren?

U kunt Azure Database for MySQL servers beheren door gebruik te maken van de Azure Portal of de Azure CLI.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure database for MySQL](./overview.md) voor een overzicht van de service
- Zie [prijs categorieën](./concepts-pricing-tiers.md) voor informatie over specifieke resource quota en beperkingen op basis van uw **prijs categorie**
- Zie [verbindings bibliotheken voor Azure database for MySQL](./concepts-connection-libraries.md)voor meer informatie over het maken van een verbinding met de service.
