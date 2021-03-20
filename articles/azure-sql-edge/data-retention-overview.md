---
title: Overzicht van het beleid voor gegevens retentie-Azure SQL Edge
description: Meer informatie over het Bewaar beleid voor gegevens in Azure SQL Edge
keywords: SQL-rand, gegevens retentie
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90976348"
---
# <a name="data-retention-overview"></a>Overzicht van gegevensretentie

Het verzamelen en opslaan van gegevens van de verbonden IoT-apparaten is belang rijk om operationeel en zakelijk inzicht te krijgen en te stimuleren. Gezien het volume van de gegevens die afkomstig zijn van deze apparaten, is het belang rijk voor organisaties om de hoeveelheid gegevens die ze willen behouden en de granulariteit te plannen. Het is echter wenselijk om alle gegevens te bewaren, maar dit is niet altijd praktisch. Daarnaast is het volume van de gegevens dat kan worden bewaard beperkt door de hoeveelheid opslag ruimte die beschikbaar is op de IoT-of edge-apparaten. 

In Azure SQL Edge-database beheerders kunnen gegevens Bewaar beleid definiëren voor een SQL-EDGE-Data Base en de onderliggende tabellen. Zodra het beleid voor gegevens retentie is gedefinieerd, wordt een achtergrond systeem taak uitgevoerd om verouderde (oude) gegevens uit de gebruikers tabellen op te schonen. 

> [!Note]
> Gegevens die zijn verwijderd uit de tabel, kunnen niet worden hersteld. De enige manier om de verwijderde gegevens te herstellen is door de data base te herstellen vanuit een oudere back-up.

Quickstarts:

- [Het Bewaar beleid voor gegevens in-en uitschakelen](data-retention-enable-disable.md)
- [Historische gegevens beheren met Bewaar beleid](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>Hoe gegevens retentie werkt

Als u gegevens retentie wilt configureren, kunt u DDL-instructies gebruiken. Voor meer informatie schakelt u het [Bewaar beleid voor gegevens in en uit](data-retention-enable-disable.md). Voor het automatisch verwijderen van de verouderde records moet het bewaren van gegevens eerst worden ingeschakeld voor zowel de Data Base als de tabellen die u wilt leegmaken binnen die data base. 

Nadat het bewaren van gegevens is geconfigureerd voor een tabel, wordt een achtergrond taak uitgevoerd om de verouderde records in een tabel te identificeren en de records te verwijderen. Als er om de een of andere reden het automatisch opschonen van de taken niet wordt uitgevoerd of niet kan worden bewaard, kan de verwijdering niet worden bijgewerkt. vervolgens kan een hand matige opschoon bewerking worden uitgevoerd voor deze tabellen. Raadpleeg [automatische en hand matige opschoning](data-retention-cleanup.md)voor meer informatie over automatische en hand matige opschoning.

## <a name="limitations-and-restrictions"></a>Beperkingen en beperkingen

- Bewaren van gegevens, indien ingeschakeld, wordt automatisch uitgeschakeld wanneer de data base wordt hersteld vanuit een volledige back-up of opnieuw wordt gekoppeld. 
- Bewaren van gegevens kan niet worden ingeschakeld voor een tijdelijke geschiedenis tabel
- Het filter voor het bewaren van gegevens Colomn kan niet worden gewijzigd. Als u de kolom wilt wijzigen, schakelt u het bewaren van gegevens uit in de tabel.  

## <a name="next-steps"></a>Volgende stappen

- [Machine Learning en kunstmatige intelligentie met ONNX in SQL Edge](onnx-overview.md).
- [Een end-to-end IoT-oplossing bouwen met SQL Edge met behulp van IoT Edge](tutorial-deploy-azure-resources.md).
- [Gegevensstreaming in Azure SQL Edge](stream-data.md)
