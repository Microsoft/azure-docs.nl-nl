---
title: Servers in Azure Database for PostgreSQL-flexibele server (preview-versie)
description: In dit artikel vindt u overwegingen en richt lijnen voor het configureren en beheren van Azure Database for PostgreSQL-flexibele server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 60edbcf97b43747246e84517cc65bb0dca4ff546
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626840"
---
# <a name="servers---azure-database-for-postgresql---flexible-server"></a>Servers-Azure Database for PostgreSQL-flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In dit artikel vindt u overwegingen en richt lijnen voor het werken met Azure Database for PostgreSQL-flexibele server.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Wat is een Azure Database for PostgreSQL-server?

Een server in de Azure Database for PostgreSQL-flexibele server implementatie optie is een centraal beheer punt voor meerdere data bases. Het is dezelfde PostgreSQL-server constructie die u mogelijk kent in de on-premises wereld. Met name de PostgreSQL-service wordt beheerd, biedt prestatie garanties, geeft toegang en functies op server niveau.

Een Azure Database for PostgreSQL-server:

- Wordt gemaakt binnen een Azure-abonnement.
- Is de bovenliggende resource voor data bases.
- Biedt een naam ruimte voor data bases.
- Is een container met sterke levens duur semantiek: een server verwijderen en de Inge sloten data bases verwijdert.
- Groepeert bronnen in een regio.
- Biedt een verbindings eindpunt voor Server-en database toegang
- Voorziet in het bereik voor beheer beleid dat van toepassing is op de bijbehorende data bases: aanmelden, firewall, gebruikers, rollen, configuraties, enzovoort.
- Is beschikbaar in meerdere versies. Zie [supported postgresql data base versions](concepts-supported-versions.md)(Engelstalig) voor meer informatie.
- Is uitbreidbaar door gebruikers. Zie [postgresql Extensions](concepts-extensions.md)(Engelstalig) voor meer informatie.

Binnen een Azure Database for PostgreSQL-server kunt u een of meer data bases maken. U kunt kiezen voor het maken van één database per server om gebruik te maken van alle resources of voor meerdere databases om de resources te delen. De prijzen zijn gestructureerd per server, op basis van de configuratie van de prijs categorie, vCores en opslag (GB). Zie [reken-en opslag opties](concepts-compute-storage.md)voor meer informatie.

## <a name="how-do-i-connect-and-authenticate-to-the-database-server"></a>Hoe kan ik verbinding maken met de database server en deze verifiëren?

De volgende elementen zorgen voor veilige toegang tot uw Data Base:

| Beveiligings concept | Description |
| :-- | :-- |
| **Verificatie en autorisatie** | Azure Database for PostgreSQL-Server ondersteunt native PostgreSQL-verificatie. U kunt verbinding maken met en verifiëren met de server met de beheerders aanmelding van de server. |
| **Protocol** | De service ondersteunt een op berichten gebaseerd protocol dat door PostgreSQL wordt gebruikt. |
| **TCP/IP** | Het protocol wordt ondersteund via TCP/IP en via Unix-domein sockets. |
| **Firewall** | Ter bescherming van uw gegevens voor komt een firewall regel alle toegang tot de server en de data bases, totdat u opgeeft welke computers zijn gemachtigd. Zie [Azure database for PostgreSQL Server firewall-regels](how-to-manage-firewall-portal.md). |

## <a name="managing-your-server"></a>Uw server beheren

U kunt Azure Database for PostgreSQL servers beheren door gebruik te maken van de [Azure Portal](https://portal.azure.com) of de [Azure cli](/cli/azure/postgres).

Tijdens het maken van een server stelt u de referenties voor de gebruiker van de beheerder in. De gebruiker met beheerders rechten is de hoogste bevoegdheids gebruiker op de server. Deze behoort tot de rol azure_pg_admin. Deze rol heeft geen machtigingen voor volledige super gebruiker. 

Het kenmerk PostgreSQL user wordt toegewezen aan de azure_superuser, die bij de beheerde service hoort. U hebt geen toegang tot deze rol.

Een Azure Database for PostgreSQL server heeft standaard databases: 

- **post gres** : een standaard database waarmee u verbinding kunt maken zodra de server is gemaakt.
- **azure_maintenance** : deze data base wordt gebruikt om de processen die de beheerde service bieden van gebruikers acties te scheiden. U hebt geen toegang tot deze data base.

## <a name="server-parameters"></a>Serverparameters

De para meters van de PostgreSQL-server bepalen de configuratie van de server. In Azure Database for PostgreSQL kan de lijst met para meters worden weer gegeven en bewerkt met behulp van de Azure Portal of de Azure CLI.

Als beheerde service voor post gres zijn de Configureer bare para meters in Azure Database for PostgreSQL een subset van de para meters in een lokale post gres-instantie (Zie de [documentatie van postgresql](https://www.postgresql.org/docs/12/static/runtime-config.html)voor meer informatie over post gres-para meters). Uw Azure Database for PostgreSQL-server is ingeschakeld met standaard waarden voor elke para meter bij het maken. Sommige para meters waarvoor het opnieuw opstarten van de server of de toegang tot de gebruikers beheerder moet worden toegepast, kunnen niet door de gebruiker worden geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure database for PostgreSQL](overview.md)voor een overzicht van de service.
- Zie [reken-en opslag opties](concepts-compute-storage.md)voor informatie over specifieke resource quota en beperkingen op basis van uw **configuratie**.
- Server parameters weer geven en bewerken via [Azure Portal](howto-configure-server-parameters-using-portal.md) of [Azure cli](howto-configure-server-parameters-using-cli.md).
