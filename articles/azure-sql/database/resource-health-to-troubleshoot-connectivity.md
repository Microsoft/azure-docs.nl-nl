---
title: Azure Resource Health gebruiken om database status te bewaken
description: Gebruik Azure Resource Health om de status van Azure SQL Database en Azure SQL Managed instance te bewaken, helpt u bij het vaststellen en verkrijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw SQL-resources.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: wiassaf, sstein
ms.date: 03/24/2021
ms.openlocfilehash: 4434c796a41126444b8785440427123e88e0b528
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105107785"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>Resource Health gebruiken om problemen met de verbinding voor Azure SQL Database en Azure SQL Managed instance op te lossen
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[Resource Health](../../service-health/resource-health-overview.md#get-started) voor Azure SQL database en Azure SQL Managed instance helpt u bij het vaststellen en verkrijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw SQL-resources. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning wanneer u hulp nodig hebt bij problemen met de Azure-service.

![Overzicht](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Status controles

Resource Health bepaalt de status van uw SQL-resource door het slagen en mislukken van aanmeldingen bij de resource te controleren. Momenteel onderzoekt Resource Health voor uw SQL Database resource alleen de aanmeldings fouten vanwege een systeem fout en geen gebruikers fout. De Resource Health status wordt elke 1 tot 2 minuten bijgewerkt.

## <a name="health-states"></a>Statussen

### <a name="available"></a>Beschikbaar

De status **beschikbaar** betekent dat resource Health geen mislukte aanmeldingen heeft gedetecteerd vanwege systeem fouten in uw SQL-resource.

![Beschikbaar](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Verminderd beschikbaar

De status **Gedegradeerd** betekent dat Resource Health een meerderheid aan geslaagde aanmeldingen, maar ook een aantal mislukte aanmeldingen, heeft gedetecteerd. Dit zijn de meest waarschijnlijke tijdelijke aanmeldings fouten. Om de gevolgen van verbindings problemen die worden veroorzaakt door tijdelijke aanmeldings fouten te verminderen, implementeert u de [logica voor opnieuw proberen](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) in uw code.

![Verminderd beschikbaar](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Niet beschikbaar

De status **niet beschikbaar** betekent dat resource Health consistente aanmeldings fouten heeft gedetecteerd in uw SQL-resource. Als uw resource gedurende lange tijd in deze status blijft, neemt u contact op met de ondersteuning.

![Niet beschikbaar](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Onbekend

De status **onbekend** geeft aan dat resource Health meer dan tien minuten geen informatie over deze resource heeft ontvangen. Hoewel deze status geen definitieve indicatie is van de status van de resource, is het een belang rijk gegevens punt in het probleemoplossings proces. Als de resource op de verwachte manier wordt uitgevoerd, wordt de status van de resource na een paar minuten gewijzigd in beschikbaar. Als u problemen ondervindt met de resource, kan de onbekende status Voorst Ellen dat een gebeurtenis in het platform van invloed is op de resource.

![Onbekend](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historische gegevens

U hebt toegang tot de status geschiedenis van Maxi maal 14 dagen in het gedeelte status geschiedenis van Resource Health. De sectie bevat ook de downtime-reden (indien beschikbaar) voor de downtime die door Resource Health worden gerapporteerd. Momenteel toont Azure de uitval tijd voor uw database resource met een granulatie van twee minuten. De werkelijke downtime is waarschijnlijk minder dan een minuut. Het gemiddelde is 8 seconden.

### <a name="downtime-reasons"></a>Oorzaken van uitval tijd

Wanneer uw data base downtime betoont, wordt de analyse uitgevoerd om een reden te bepalen. Wanneer beschikbaar, wordt de downtime-reden gerapporteerd in het gedeelte status geschiedenis van Resource Health. De oorzaken van uitval tijd worden doorgaans binnen 45 minuten na een gebeurtenis gepubliceerd.

#### <a name="planned-maintenance"></a>Gepland onderhoud

De Azure-infra structuur voert periodiek gepland onderhoud uit: de upgrade van hardware-of software onderdelen in het Data Center. Terwijl de data base onderhoud ondergaat, kan Azure SQL sommige bestaande verbindingen beëindigen en nieuwe weigeren. De aanmeldings fouten die zijn opgetreden tijdens gepland onderhoud zijn doorgaans tijdelijk en de logica van het [opnieuw proberen](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) vermindert de impact. Neem contact op met de ondersteuning als u aanmeldings fouten blijft ervaren.

#### <a name="reconfiguration"></a>Herconfiguratie

Herconfiguraties worden beschouwd als tijdelijke omstandigheden en worden verwacht van tijd tot tijd. Deze gebeurtenissen kunnen worden geactiveerd door taak verdeling of software/hardwarestoringen. Bij elke client productie toepassing die verbinding maakt met een Cloud database, moet een robuuste logica voor verbindings [poging](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)worden geïmplementeerd, omdat deze de situatie zou kunnen verhelpen en de fouten doorgaans transparant moet maken voor de eind gebruiker.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logica voor opnieuw proberen voor tijdelijke fouten](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- [SQL-verbindings fouten oplossen, diagnosticeren en voor komen](troubleshoot-common-connectivity-issues.md).
- Meer informatie over het [configureren van resource Health-waarschuwingen](../../service-health/resource-health-alert-arm-template-guide.md).
- Bekijk een overzicht van [resource Health](../../service-health/resource-health-overview.md).
- Lees de [resource Health Veelgestelde vragen](../../service-health/resource-health-faq.md).
