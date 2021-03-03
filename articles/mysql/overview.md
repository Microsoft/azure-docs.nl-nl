---
title: Overzicht - Azure Database for MySQL
description: Meer informatie over de Azure Database for MySQL-service, een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 20b54afdfbb5641def921ffc17b3c38f6650acab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736216"
---
# <a name="what-is-azure-database-for-mysql"></a>Wat is Azure Database for MySQL?

Azure Database for MySQL is een relationele databaseservice in de Microsoft Cloud die is gebaseerd op de [MySQL Community Edition](https://www.mysql.com/products/community/)-database-engine (beschikbaar onder de GPLv2-licentie, versies 5.6, 5.7 en 8.0). Azure Database for MySQL biedt het volgende:

- Ingebouwde hoge beschikbaarheid.
- Gegevensbescherming met behulp van automatische back-ups en herstel naar een tijdstip tot 35 dagen geleden.
- Automatisch onderhoud voor onderliggende hardware, besturingssysteem en database-engine om de service veilig en up-to-date te houden.
- Voorspelbare prestaties, tegen all-inclusive prijzen op basis van betalen per gebruik.
- Elastisch schalen binnen enkele seconden.
- Kostenoptimalisatiebeheer met de mogelijkheid om de server te stoppen/starten. 
- Beveiliging van ondernemingskwaliteit en toonaangevende compliance voor het beschermen van gevoelige gegevens, zowel in rust als in beweging.
- Bewaking en automatisering om het beheer en de bewaking voor grootschalige implementaties te vereenvoudigen.
- Toonaangevende ondersteuningservaring.

Deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. Dankzij deze mogelijkheden kunt u zich richten op het sneller ontwikkelen en op de markt brengen van apps, in plaats van kostbare tijd en middelen in te zetten voor het beheer van virtuele machines en infrastructuur. U kunt bovendien de open-source hulpprogramma's en het platform van uw keuze blijven gebruiken om uw toepassing verder te ontwikkelen, en deze te leveren met de snelheid en efficiëntie die uw bedrijf vereist zonder dat u nieuwe vaardigheden hoeft te leren.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Conceptueel diagram van Azure Database for MySQL":::

## <a name="deployment-models"></a>Implementatiemodellen

Azure Database for MySQL, mogelijk gemaakt door de MySQL Community Edition, is beschikbaar in twee implementatiemodi:
- Single Server 
- Flexible Server (preview)
  
### <a name="azure-database-for-mysql---single-server"></a>Azure Database for MySQL - Single Server

Azure Database for MySQL Single Server is een volledig beheerde databaseservice met minimale vereisten voor database-aanpassingen. Het Single Server-platform is ontworpen voor het verwerken van de meeste databasebeheerfuncties, zoals het toepassen van patches, back-ups, hoge beschikbaarheid, beveiliging met minimale gebruikersconfiguratie en beheer. De architectuur is geoptimaliseerd voor ingebouwde hoge beschikbaarheid met een beschikbaarheid van 99,99% in één beschikbaarheidszone. Het ondersteunt de community-versie van MySQL 5.6, 5.7 en 8.0. De service is momenteel algemeen beschikbaar in allerlei [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/).

De implementatieoptie Individuele server biedt de volgende drie prijscategorieën: Basic, Algemeen gebruik en Geoptimaliseerd voor geheugen. Elke categorie biedt verschillende resources ter ondersteuning van de workloads van uw databases. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt op het moment dat u ze nodig hebt. Zie [Prijscategorieën](concepts-pricing-tiers.md) voor meer details.

Eén server is het meest geschikt voor cloudtoepassingen die zijn ontworpen voor de verwerking van automatische patches, waarbij geen nauwkeurige controle op het patchschema en aangepaste configuratie-instellingen voor MySQL nodig zijn. 

Zie [overzicht van één server](single-server-overview.md)voor een gedetailleerd overzicht van de implementatiemodus met één server.

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL Flexible Server (Preview)

Azure Database for MySQL Flexible Server is een volledig beheerde databaseservice die is ontworpen om nauwkeurige controle en flexibiliteit te bieden als het gaat om databasebeheerfuncties en configuratie-instellingen. Over het algemeen biedt de service meer flexibiliteit en aanpassingen op basis van de gebruikersvereisten. Met de flexibele-serverarchitectuur kunnen gebruikers kiezen voor hoge beschikbaarheid binnen één beschikbaarheidszone en tussen meerdere beschikbaarheidszones. Flexibele servers bieden betere besturingselementen voor kostenoptimalisatie, met de mogelijkheid om de server en burstable compute-laag te stoppen of te starten, wat ideaal is voor workloads die niet voortdurend volledige rekencapaciteit nodig hebben. De service ondersteunt de Community-versie van MySQL 5,7 en 8,0. De service is momenteel beschikbaar openbare preview, en is beschikbaar in allerlei [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/).

Flexibele servers zijn het best geschikt voor 
- Ontwikkeling van toepassingen die betere controle en aanpassingen vereisen.
- Zone-redundante hoge beschikbaarheid
- Beheerde onderhoudsvensters

Zie [flexibele-serveroverzicht](flexible-server/overview.md) voor een gedetailleerd overzicht van de implementatiemodus met flexibele servers.

## <a name="contacts"></a>Contactpersonen
Voor eventuele vragen of suggesties over het werken met Azure Database for MySQL stuurt u een e-mailbericht naar het Azure Database for MySQL-team ([@AskAzure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Dit e-mailadres is geen alias voor technische ondersteuning.

Overweeg ook de volgende aanspreekpunten, indien van toepassing:

- Als u contact wilt opnemen met Azure-ondersteuning, kunt u een [ticket indienen vanuit Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Als u een probleem met uw account wilt oplossen, kunt u een [ondersteuningsaanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) indienen in Azure Portal.
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de twee implementatiemodi voor Azure Database for MySQL en het kiezen van de juiste opties op basis van uw behoeften.

- [Single Server](single-server/index.yml)
- [Flexible Server](flexible-server/index.yml)
- [Kies de juiste MySQL-implementatieoptie voor uw workload](select-right-deployment-type.md)
