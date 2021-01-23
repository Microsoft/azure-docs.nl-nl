---
title: Melding over gepland onderhoud-Azure Database for PostgreSQL-één server
description: In dit artikel wordt de functie voor gepland onderhouds meldingen in Azure Database for PostgreSQL-één server beschreven
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 8db556709f68a1184046989a15fad147542a05a7
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735738"
---
# <a name="planned-maintenance-notification-in-azure-database-for-postgresql---single-server"></a>Melding over gepland onderhoud in Azure Database for PostgreSQL - Eén server

Meer informatie over het voorbereiden van geplande onderhouds gebeurtenissen op uw Azure Database for PostgreSQL.

## <a name="what-is-a-planned-maintenance"></a>Wat is een gepland onderhoud?

Azure Database for PostgreSQL-service voert automatische patching uit van de onderliggende hardware, het besturings systeem en de data base-engine. De patch omvat nieuwe service functies, beveiliging en software-updates. Voor de PostgreSQL-engine worden kleine versie-upgrades automatisch uitgevoerd. Deze zijn onderdeel van de patchcyclus. Gebruikers hoeven zelf geen actie te ondernemen en er zijn geen configuratie-instellingen vereist voor patches. De patch wordt uitgebreid getest en geïmplementeerd met behulp van veilige implementatie procedures.

Een gepland onderhoud is een onderhouds venster wanneer deze service-updates worden geïmplementeerd op servers in een bepaalde Azure-regio. Tijdens dit gepland onderhoud wordt een melding gecreëerd, om klanten te informeren wanneer de service-update is geïmplementeerd in de Azure-regio waarin hun servers worden gehost. De minimale duur tussen twee gepland onderhoud is 30 dagen. U ontvangt 72 uur van te voren een melding van het volgende onderhoudsvenster.

## <a name="planned-maintenance---duration-and-customer-impact"></a>Gepland onderhoud-duur en klant impact

Een gepland onderhoud voor een bepaalde Azure-regio wordt doorgaans binnen 15 uur verwacht. Dit tijd venster bevat ook buffer tijd voor het uitvoeren van een rollback-plan als dat nodig is. Azure Database for PostgreSQL-servers worden uitgevoerd in containers zodat het opnieuw opstarten van de database server 60-120 seconden duurt, maar er is geen deterministische manier om te weten wanneer binnen dit 15 uur het venster van uw server wordt beïnvloed. De volledige geplande onderhouds gebeurtenis, inclusief het opnieuw opstarten van de server, wordt zorgvuldig gecontroleerd door het technische team. De failovertijd van de server is afhankelijk van het herstel van de data base, waardoor de data base langer online kan worden uitgevoerd als er sprake is van langdurige trans actie op de server op het moment van de failover. Om te voor komen dat de computer opnieuw wordt opgestart, wordt aanbevolen om langlopende trans acties (bulksgewijs laden) te voor komen tijdens geplande onderhouds gebeurtenissen.

In samen vatting, terwijl de geplande onderhouds gebeurtenis gedurende 15 uur wordt uitgevoerd, is de impact van de afzonderlijke server 60 seconden lang, afhankelijk van de transactionele activiteit op de server. Er wordt een melding verzonden van 72 kalender uren voordat gepland onderhoud begint en een andere, terwijl er onderhoud wordt uitgevoerd voor een bepaalde regio.

## <a name="how-can-i-get-notified-of-planned-maintenance"></a>Hoe kan ik een melding ontvangen over gepland onderhoud?

U kunt de functie voor geplande onderhouds meldingen gebruiken om waarschuwingen te ontvangen voor een geplande onderhouds gebeurtenis. U ontvangt de melding over de komende onderhoud 72 kalender uren voor de gebeurtenis en een andere, terwijl onderhoud wordt uitgevoerd voor een bepaalde regio.

### <a name="planned-maintenance-notification"></a>Melding over gepland onderhoud

> [!IMPORTANT]
> Geplande onderhouds meldingen zijn momenteel beschikbaar als preview-versie in alle regio's **, behalve** voor West-Centraal VS

Met **geplande onderhouds meldingen** kunt u waarschuwingen ontvangen voor toekomstige geplande onderhouds gebeurtenissen op uw Azure database for PostgreSQL. Deze meldingen zijn geïntegreerd met [het geplande onderhoud van service Health](../service-health/overview.md) en bieden u de mogelijkheid om het geplande onderhoud voor uw abonnementen op één plek weer te geven. Het helpt ook om de melding te schalen naar de juiste doel groep voor verschillende resource groepen, omdat u mogelijk verschillende contact personen hebt die verantwoordelijk zijn voor verschillende resources. U ontvangt de melding over de komende onderhoud 72 kalender uren voor de gebeurtenis.

Elke keer dat er wordt gewaarschuwd voor **gepland onderhouds melding** 72 uur voor alle gebeurtenissen. In het geval van essentiële of beveiligings patches kunnen meldingen echter dichter bij het evenement worden verzonden of worden wegge laten.

U kunt de geplande onderhouds melding controleren op Azure Portal of waarschuwingen configureren voor het ontvangen van meldingen. 

### <a name="check-planned-maintenance-notification-from-azure-portal"></a>Controleer de geplande onderhouds melding van Azure Portal

1. Selecteer **service Health** In het [Azure Portal](https://portal.azure.com).
2. Tabblad **gepland onderhoud** selecteren
3. Selecteer **abonnement**, * * regio en **service** waarvoor u het geplande onderhouds bericht wilt controleren. 
   
### <a name="to-receive-planned-maintenance-notification"></a>Een melding over gepland onderhoud ontvangen

1. Selecteer in de [portal](https://portal.azure.com) **service Health**.
2. Selecteer in de sectie **waarschuwingen** **Health Alerts**.
3. Selecteer **+ service Health alert toevoegen** en vul de velden in.
4. Vul de vereiste velden in. 
5. Kies het **gebeurtenis type**, selecteer **gepland onderhoud** of **Alles selecteren**
6. Geef in **actie groepen** op hoe u de waarschuwing wilt ontvangen (ontvang een e-mail, Activeer een logische app, enz.)  
7. Zorg ervoor dat regel inschakelen bij maken is ingesteld op Ja.
8. Selecteer **waarschuwings regel maken** om uw waarschuwing te volt ooien

Zie [waarschuwingen voor activiteiten logboek maken voor service meldingen](../service-health/alerts-activity-log-service-notifications-portal.md)voor gedetailleerde stappen voor het maken van **service Health-waarschuwingen**.

## <a name="can-i-cancel-or-postpone-planned-maintenance"></a>Kan ik gepland onderhoud annuleren of uitstellen?

Er is onderhoud nodig om uw server veilig, stabiel en up-to-date te houden. De geplande onderhouds gebeurtenis kan niet worden geannuleerd of uitgesteld. Zodra de melding is verzonden naar een bepaalde Azure-regio, kunnen de wijzigingen in het patch schema niet worden gemaakt voor een afzonderlijke server in die regio. De patch wordt uitgerold voor de hele regio tegelijk. Azure Database for PostgreSQL-service met één server is ontworpen voor native Cloud toepassingen waarvoor geen gedetailleerde controle of aanpassing van de service nodig is. Als u op zoek bent naar het plannen van onderhoud voor uw servers, raden we u aan [flexibele servers](./flexible-server/overview.md)te overwegen.

## <a name="are-all-the-azure-regions-patched-at-the-same-time"></a>Worden alle Azure-regio's tegelijkertijd patches uitgevoerd?

Nee, alle Azure-regio's worden bijgewerkt tijdens de timing van het implementatie venster. Het venster implementaties wordt doorgaans uitgerekt van 5 PM-8 uur lokale tijd volgende dag in een bepaalde Azure-regio. Geografisch gekoppelde Azure-regio's worden op verschillende dagen gepatcheerd. Voor hoge Beschik baarheid en bedrijfs continuïteit van database servers wordt het gebruik van [meerdere regio's met lees replica's](./concepts-read-replicas.md#cross-region-replication) aanbevolen.

## <a name="retry-logic"></a>Logica voor opnieuw proberen

Een tijdelijke fout, ook wel bekend als tijdelijke fout, is een fout die zichzelf zal oplossen. [Tijdelijke fouten](./concepts-connectivity.md#transient-errors) kunnen optreden tijdens onderhoud. De meeste van deze gebeurtenissen worden in minder dan 60 seconden automatisch door het systeem beperkt. Tijdelijke fouten moeten worden afgehandeld met behulp van [logica opnieuw proberen](./concepts-connectivity.md#handling-transient-errors).


## <a name="next-steps"></a>Volgende stappen

- Voor vragen of suggesties die u mogelijk hebt over het werken met Azure Database for PostgreSQL, stuurt u een e-mail naar het Azure Database for PostgreSQL team op AskAzureDBforPostgreSQL@service.microsoft.com
- Zie [waarschuwingen instellen](howto-alert-on-metric.md) voor hulp bij het maken van een waarschuwing op metrische gegevens.
- [Verbindings problemen met Azure Database for PostgreSQL-één server oplossen](howto-troubleshoot-common-connection-issues.md)
- [Tijdelijke fouten afhandelen en efficiënt verbinding maken met Azure Database for PostgreSQL-één server](concepts-connectivity.md)
