---
title: Wijzigingen in de gegevens bijhouden in Azure SQL Edge
description: Meer informatie over het bijhouden van wijzigingen en het change data capture in Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dddaad3e171c757b353deb81ffcb77cfbe706340
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108259"
---
# <a name="track-data-changes-in-azure-sql-edge"></a>Wijzigingen in de gegevens bijhouden in Azure SQL Edge

Azure SQL Edge ondersteunt de twee SQL Server functies waarmee wijzigingen in gegevens worden bijgehouden in een Data Base: het bijhouden en [Change Data Capture](/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture) [wijzigen](/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking) . Met deze functies kunnen toepassingen bepalen welke taal wijzigingen voor het wijzigen van de gegevens (insert-, update-en delete-bewerkingen) zijn aangebracht in gebruikers tabellen in een Data Base. U kunt change data capture en het bijhouden van wijzigingen inschakelen voor dezelfde data base. Er zijn geen speciale overwegingen vereist.

De mogelijkheid om gegevens op te vragen die in een Data Base zijn gewijzigd, is een belang rijke vereiste voor sommige toepassingen om efficiënt te zijn. Om gegevens wijzigingen te bepalen, moeten ontwikkel aars van toepassingen meestal een aangepaste traceer methode implementeren in hun toepassingen met behulp van een combi natie van triggers, time stamp-kolommen en aanvullende tabellen. Het maken van deze toepassingen vergt meestal veel werk voor het implementeren van schema-updates en is vaak een hoge prestatie overhead.

In het geval van een IoT-oplossing, waar u regel matig gegevens van de rand naar een Cloud of Data Center moet verplaatsen, kan het bijhouden van wijzigingen erg nuttig zijn. Gebruikers kunnen snel en effectief query's uitvoeren op de wijzigingen van de laatste synchronisatie en deze wijzigingen uploaden naar de Cloud of het Data Center-doel. Zie voor meer informatie voor [delen van het gebruik van Change Data Capture of het bijhouden van wijzigingen](/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking). 

Deze twee functies zijn niet hetzelfde. Zie voor meer informatie [functie verschillen tussen Change Data Capture en het bijhouden van wijzigingen](/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>Gegevensregistratie wijzigen

Zie [over Change Data Capture](/sql/relational-databases/track-changes/about-change-data-capture-sql-server)voor meer informatie over de werking van deze functie.

Zie change data capture in- [en uitschakelen](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)voor meer informatie over het in-of uitschakelen van deze functie.

Zie [Change Data Capture beheren en controleren](/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)als u deze functie wilt beheren en controleren.

Zie [werken met gegevens wijzigen](/sql/relational-databases/track-changes/work-with-change-data-sql-server)als u wilt weten hoe u de gewijzigde gegevens kunt opvragen en ermee kunt werken.

> [!NOTE]
> Change Data Capture-functies die afhankelijk zijn van CLR, worden niet ondersteund in Azure SQL Edge.

## <a name="change-tracking"></a>Wijzigingen bijhouden

Zie [informatie over het bijhouden van wijzigingen](/sql/relational-databases/track-changes/about-change-tracking-sql-server)voor meer informatie over de werking van deze functie.

Zie voor meer informatie over het in-of uitschakelen van deze functie, het [bijhouden van wijzigingen in-en uitschakelen](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server).

Als u deze functie wilt beheren, bewaken en beheren, raadpleegt u [Wijzigingen bijhouden beheren en controleren](/sql/relational-databases/track-changes/manage-change-tracking-sql-server).

Zie [werken met gegevens wijzigen](/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)als u wilt weten hoe u de gewijzigde gegevens kunt opvragen en ermee kunt werken.

## <a name="temporal-tables"></a>Tijdelijke tabellen

Azure SQL Edge biedt ook ondersteuning voor de functie voor tijdelijke tabellen van SQL Server. Deze functie (ook wel de *tijdelijke systeem versie tabellen* genoemd) biedt ingebouwde ondersteuning voor het leveren van informatie over gegevens die in de tabel zijn opgeslagen op elk gewenst moment. De functie biedt niet alleen informatie over de gegevens die op het huidige tijdstip op dat moment juist zijn.

Een tijdelijke systeem versie tabel is een soort gebruikers tabel die is ontworpen om een volledige geschiedenis van gegevens wijzigingen te kunnen bijhouden en om eenvoudige point-in-time-analyses mogelijk te maken. Dit type tijdelijke tabel wordt een tijdelijke systeem versie tabel genoemd, omdat de geldigheids periode voor elke rij wordt beheerd door het systeem (dat wil zeggen, de data base-engine).

Elke tijdelijke tabel heeft twee expliciet gedefinieerde kolommen, elk met een `datetime2` gegevens type. Deze kolommen worden een *periode* kolom genoemd. Het systeem gebruikt deze periode kolommen uitsluitend voor het vastleggen van de geldigheids periode voor elke rij wanneer een rij wordt gewijzigd.

Naast deze periode kolommen bevat een tijdelijke tabel ook een verwijzing naar een andere tabel met een gespiegeld schema. Het systeem gebruikt deze tabel om automatisch de vorige versie van de rij op te slaan telkens wanneer een rij in de tijdelijke tabel wordt bijgewerkt of verwijderd. Deze aanvullende tabel wordt de *geschiedenis* tabel genoemd, terwijl de hoofd tabel waarin de huidige (werkelijke) versies van de rijen worden opgeslagen, wordt aangeduid als de *huidige* tabel of gewoon als de tijdelijke tabel. Tijdens het maken van een tijdelijke tabel kunnen gebruikers een bestaande geschiedenis tabel opgeven (deze moet voldoen aan het schema) of het systeem de standaard geschiedenis tabel laten maken.

Zie [tijdelijke tabellen](/sql/relational-databases/tables/temporal-tables)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Gegevens stromen in Azure SQL Edge ](stream-data.md)
- [Machine learning en AI met ONNX in Azure SQL Edge ](onnx-overview.md)
- [Replicatie naar Azure SQL Edge configureren](configure-replication.md)
- [Back-up en herstel van data bases in Azure SQL Edge](backup-restore.md)
