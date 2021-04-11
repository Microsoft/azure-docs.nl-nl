---
title: Replica's lezen-Azure Database for PostgreSQL-grootschalige (Citus)
description: In dit artikel wordt de functie voor het lezen van replica's in Azure Database for PostgreSQL-grootschalige (Citus) beschreven.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cf8c64bf3858f0d3b7d633b94bc7302fbe563f87
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023964"
---
# <a name="read-replicas-in-azure-database-for-postgresql---hyperscale-citus"></a>Replica's lezen in Azure Database for PostgreSQL-grootschalige (Citus)

> [!IMPORTANT]
> Het lezen van replica's in grootschalige (Citus) zijn momenteel beschikbaar als preview-versie. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
>
> U kunt een volledige lijst met andere nieuwe functies bekijken in [Preview-functies voor grootschalige (Citus)](hyperscale-preview-features.md).

Met de functie replica lezen kunt u gegevens van een grootschalige-Server groep (Citus) repliceren naar een alleen-lezen Server groep. Replica's worden **asynchroon** bijgewerkt met postgresql fysieke replicatie technologie. U kunt van de primaire server repliceren naar een onbeperkt aantal replica's.

Replica's zijn nieuwe server groepen die u op dezelfde manier beheert als gewone grootschalige (Citus)-Server groepen. Voor elke Lees replica wordt u gefactureerd voor de ingerichte Compute in vCores en Storage in GB/maand.

Meer informatie over het [maken en beheren van replica's](howto-hyperscale-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Wanneer moet u een lees replica gebruiken?

De functie voor het lezen van replica's helpt bij het verbeteren van de prestaties en schaal baarheid van Lees bare werk belastingen. Lees werkbelastingen kunnen worden geïsoleerd voor de replica's, terwijl schrijf werkbelastingen kunnen worden omgeleid naar de primaire.

Een veelvoorkomend scenario is om BI-en analytische werk belastingen de Lees replica te laten gebruiken als gegevens bron voor rapportage.

Omdat replica's alleen-lezen zijn, beperken ze niet rechtstreeks de overhead van de schrijf capaciteit op de primaire.

### <a name="considerations"></a>Overwegingen

De functie is bedoeld voor scenario's waarbij replicatie vertraging acceptabel is en is bedoeld voor het offloaden van query's. Het is niet bedoeld voor synchrone replicatie scenario's waarbij wordt verwacht dat replica gegevens up-to-date zijn. Er is een meet bare vertraging tussen de primaire en de replica. De vertraging kan minuten of zelfs uren zijn, afhankelijk van de werk belasting en de latentie tussen de primaire en de replica.  De gegevens op de replica worden uiteindelijk consistent met de gegevens op de primaire. Gebruik deze functie voor werk belastingen die deze vertraging kunnen bevatten. 

## <a name="create-a-replica"></a>Replica's maken

Wanneer u de werk stroom voor het maken van de replica start, wordt er een lege grootschalige-Server groep (Citus) gemaakt. De nieuwe groep wordt gevuld met de gegevens die zich op de primaire server groep bevonden. De aanmaak tijd is afhankelijk van de hoeveelheid gegevens op de primaire en de tijd sinds de laatste wekelijkse volledige back-up. De tijd kan variëren van een paar minuten tot enkele uren.

De functie voor het lezen van replica's maakt gebruik van fysieke PostgreSQL-replicatie, geen logische replicatie. De standaard modus is replicatie streamen met behulp van replicatie sleuven.
Als dat nodig is, wordt de back-upfunctie voor logboek registratie gebruikt voor het bijwerken.

Meer informatie over [het maken van een lees replica in de Azure Portal](howto-hyperscale-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Verbinding maken met een replica

Wanneer u een replica maakt, neemt de firewall regels niet de primaire server groep over. Deze regels moeten onafhankelijk worden ingesteld voor de replica.

De replica neemt het beheerders account ("Citus") over van de primaire server groep.
Alle gebruikers accounts worden gerepliceerd naar de replica's die worden gelezen. U kunt alleen verbinding maken met een lees replica met behulp van de gebruikers accounts die beschikbaar zijn op de primaire server.

U kunt verbinding maken met het coördinator knooppunt van de replica door de hostnaam en een geldig gebruikers account te gebruiken, zoals u zou doen met een gewone grootschalige (Citus)-Server groep.
Voor een server met de naam **mijn replica** met de gebruikers naam van de beheerder **Citus** kunt u verbinding maken met het coördinator knooppunt van de replica met behulp van psql:

```bash
psql -h c.myreplica.postgres.database.azure.com -U citus@myreplica -d postgres
```

Voer bij de prompt het wacht woord voor het gebruikers account in.

## <a name="considerations"></a>Overwegingen

In deze sectie vindt u een overzicht van de overwegingen voor de functie replica lezen.

### <a name="new-replicas"></a>Nieuwe replica's

Er wordt een lees replica gemaakt als een nieuwe grootschalige-Server groep (Citus). Een bestaande server groep kan niet worden gemaakt in een replica. Het is niet mogelijk om een replica van een andere Lees replica te maken.

### <a name="replica-configuration"></a>Replica configuratie

Een replica wordt gemaakt met behulp van dezelfde compute-, opslag-en worker-knooppunt instellingen als de primaire. Nadat een replica is gemaakt, kunnen verschillende instellingen worden gewijzigd, waaronder opslag en back-up van de Bewaar periode. Andere instellingen kunnen niet worden gewijzigd in replica's, zoals de opslag grootte en het aantal worker-knoop punten.

Vergeet niet om replica's sterk genoeg te houden om wijzigingen op te slaan van de primaire. Zorg er bijvoorbeeld voor dat u de functie voor reken kracht in replica's bijwerkt als u deze op de primaire versie verbreidt.

Firewall regels en parameter instellingen worden niet overgenomen van de primaire server naar de replica wanneer de replica wordt gemaakt of daarna.

### <a name="regions"></a>Regio's

Grootschalige (Citus)-Server groepen ondersteunen alleen replicatie met dezelfde regio.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het maken en beheren van Lees replica's in de Azure Portal](howto-hyperscale-read-replicas-portal.md).
