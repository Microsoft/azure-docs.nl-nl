---
title: Gepland onderhoud - Azure Database for PostgreSQL - Flexibele server
description: In dit artikel wordt de functie voor gepland onderhoud in Azure Database for PostgreSQL - Flexibele server beschreven.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 50ef040f1cb7d8c533ec5ee31e9bffa2e6dca2f5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478009"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Gepland onderhoud in Azure Database for PostgreSQL – Flexible server
 
Azure Database for PostgreSQL- Flexibele server voert periodiek onderhoud uit om uw beheerde database veilig, stabiel en up-to-date te houden. Tijdens onderhoud ontvangt de server nieuwe functies, updates en patches.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexibele server is in preview
 
## <a name="selecting-a-maintenance-window"></a>Een onderhoudsvenster selecteren
 
U kunt onderhoud plannen op een specifieke dag van de week en een tijdvenster binnen die dag. U kunt het systeem ook automatisch een dag en een tijdvenster laten kiezen. Hoe dan ook, het systeem waarschuwt u vijf dagen voordat onderhoud wordt uitgevoerd. Het systeem laat u ook weten wanneer het onderhoud wordt gestart en wanneer het is voltooid.
 
Meldingen over gepland onderhoud kunnen zijn:
 
* Via e-mail verzonden naar een specifiek adres
* Per e-mail verzonden naar Azure Resource Manager rol
* Verzonden in een sms-bericht naar mobiele apparaten
* Gepusht als een melding naar een Azure-app
* Bezorgd als een voicemail-bericht
 
Wanneer u voorkeuren voor het onderhoudsschema opgeeft, kunt u een dag van de week en een tijdvenster kiezen. Als u geen voorkeuren opgeeft, kiest het systeem een tijd tussen 23:00 uur en 07:00 uur in de regiotijd van uw server. U kunt verschillende planningen definiëren voor elke flexibele server in uw Azure-abonnement. 
 
> [!IMPORTANT]
> Normaal gesproken zijn er ten minste 30 dagen tussen geslaagde geplande onderhoudsgebeurtenissen voor een server.
>
> In het geval van een essentiële nood-update, zoals een ernstig beveiligingsprobleem, kan het meldingsvenster echter korter zijn dan vijf dagen. De essentiële update kan worden toegepast op uw server, zelfs als in de afgelopen 30 dagen geslaagd gepland onderhoud is uitgevoerd.

U kunt de planningsinstellingen op elk moment bijwerken. Als er onderhoud is gepland voor uw Flexibele server en u de planningsvoorkeuren bijwerkt, wordt de huidige implementatie volgens planning uitgevoerd en worden de wijziging in de planningsinstellingen van kracht zodra deze is voltooid voor het volgende geplande onderhoud.

U kunt een door het systeem beheerd schema of aangepaste planning definiëren voor elke flexibele server in uw Azure-abonnement.  
* Met een aangepast schema kunt u het onderhoudsvenster voor de server opgeven door de dag van de week en een tijdvenster van één uur te kiezen.  
* Met een door het systeem beheerd schema kiest het systeem een periode van één uur tussen 23:00 en 07:00 uur in de regiotijd van uw server.  

Als onderdeel van het uitrollen van wijzigingen passen we de updates toe op de servers die zijn geconfigureerd met een door het systeem beheerde planning, gevolgd door servers met een aangepast schema na een minimale hiaat van 7 dagen binnen een bepaalde regio. Als u van plan bent om vroege updates te ontvangen op de ontwikkel- en testomgevingsservers, raden we u aan om een door het systeem beheerd schema te configureren voor servers die worden gebruikt in de ontwikkel- en testomgeving. Hierdoor kunt u eerst de nieuwste update ontvangen in uw Dev/Test-omgeving voor testen en evaluatie voor validatie. Als u te maken krijgt met gedrag of wijzigingen die wijzigingen veroorzaken, hebt u tijd om deze aan te pakken voordat dezelfde update wordt uitgerold naar productieservers met een aangepast beheerd schema. De update wordt na 7 dagen op flexibele servers met aangepaste planning uitgebracht en wordt toegepast op uw server in het gedefinieerde onderhoudsvenster. Op dit moment is er geen optie om de update uit te stellen nadat de melding is verzonden. Aangepaste planning wordt alleen aanbevolen voor productieomgevingen. 

In zeldzame gevallen kan een onderhoudsgebeurtenis worden geannuleerd door het systeem of kan deze niet worden voltooid. Als de update mislukt, wordt de update teruggezet en wordt de vorige versie van de binaire bestanden hersteld. In dergelijke mislukte updatescenario's kan het zijn dat de server tijdens het onderhoudsvenster opnieuw wordt opgestart. Als de update is geannuleerd of mislukt, maakt het systeem een melding over respectievelijk geannuleerde of mislukte onderhoudsgebeurtenissen. De volgende onderhoudspoging wordt gepland volgens uw huidige planningsinstellingen en u ontvangt hier vijf dagen van tevoren een melding over. 

 
## <a name="next-steps"></a>Volgende stappen
 
* Meer informatie over het [wijzigen van de onderhoudsplanning](how-to-maintenance-portal.md)
* Meer informatie over het [ontvangen van meldingen over aanstaand onderhoud met](../../service-health/service-notifications.md) Azure Service Health
* Meer informatie over [het instellen van waarschuwingen over geplande onderhoudsgebeurtenissen](../../service-health/resource-health-alert-monitor-guide.md)
