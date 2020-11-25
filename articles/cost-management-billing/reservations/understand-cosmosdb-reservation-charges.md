---
title: Inzicht in reserveringskortingen in Azure Cosmos DB
description: Lees hoe een reserveringskorting wordt toegepast op ingerichte doorvoer (RU/s) in Azure Cosmos DB.
author: bandersmsft
ms.author: banders
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: ca4c1810912771f56661ca5b682b6991735f526e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023139"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Reserveringskorting toepassen op Azure Cosmos DB

Wanneer u gereserveerde capaciteit voor Azure Cosmos DB hebt gekocht, wordt de reserveringskorting automatisch toegepast op Azure Cosmos DB-resources die overeenkomen met de kenmerken en hoeveelheid van de reservering. Een reservering dekt de doorvoer die voor uw Azure Cosmos DB-resources is ingericht. De reservering geldt niet voor software, netwerken, opslag of vooraf gedefinieerde containerkosten.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: '*gebruiken of verliezen*'. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Reserveringskorting die wordt toegepast op Azure Cosmos DB-accounts

Een reserveringskorting wordt toegepast op de [ingerichte doorvoer](../../cosmos-db/request-units.md), uitgedrukt als het aantal aanvraageenheden per seconde (RU/s) op uurbasis. Voor Azure Cosmos DB-resources die niet het volledige uur worden uitgevoerd, wordt de korting automatisch toegepast op andere Cosmos DB-resources die overeenkomen met de reserveringskenmerken. De korting kan worden toegepast op Azure Cosmos DB-resources die gelijktijdig worden uitgevoerd. Als u geen Cosmos DB-resources hebt die het hele uur worden uitgevoerd en voldoen aan de reserveringsvoorwaarden, profiteert u niet optimaal van de korting voor de reserveringscapaciteit voor dat uur.

Kortingen worden gelaagd toegepast. Reserveringen met hogere aanvraageenheden bieden hogere kortingen.

Bij de reserveringsaankoop worden kortingen toegepast op alle regio's, in de verhouding die overeenkomt met de regionale on-demand prijzen. Zie de sectie [Reserveringskorting per regio](#reservation-discount-per-region) in dit artikel voor de verhoudingen van reserveringskortingen in elke regio.

## <a name="reservation-discount-per-region"></a>Reserveringskorting per regio
De reserveringskorting wordt toegepast op kosten van Azure Cosmos DB-doorvoer op uurbasis. De korting wordt toegepast op één abonnement of het geregistreerde bereik/accountbereik. De reserveringskorting wordt met de volgende verhoudingen toegepast op het gebruik van meters in verschillende regio's:

|Meterbeschrijving  |Regio |Verhouding  |
|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/uur - AP - zuidoost  |  AP - zuidoost    |   1      |
|Azure Cosmos DB - 100 RU/s/uur - AP - oost |   AP - oost   |    1     |
|Azure Cosmos DB - 100 RU/s/uur - EU - noord|  EU - noord       |    1     |
|Azure Cosmos DB - 100 RU/s/uur - KR - zuid|    KR - zuid     |     1    |
|Azure Cosmos DB - 100 RU/s/uur - EU - west|    EU - west     |      1   |
|Azure Cosmos DB - 100 RU/s/uur - KR - centraal|   KR - centraal    |       1  |
|Azure Cosmos DB - 100 RU/s/uur - VK - zuid|   Verenigd Koninkrijk Zuid      |     1    |
|Azure Cosmos DB - 100 RU/s/uur - VK - west|   Verenigd Koninkrijk West      |    1     |
|Azure Cosmos DB - 100 RU/s/uur - UK - noord |   UK - noord    |     1    |
|Azure Cosmos DB - 100 RU/s/uur - VK - zuid 2|   VK - zuid 2      |     1    |
|Azure Cosmos DB - 100 RU/s/uur - US - oost 2|  US - oost 2     |     1    |
|Azure Cosmos DB - 100 RU/s/uur - US - noord-centraal|   US - noord-centraal      |     1    |
|Azure Cosmos DB - 100 RU/s/uur - US - west|   US - west      |     1    |
|Azure Cosmos DB - 100 RU/s/uur - US - centraal| US - centraal        |     1    |
|Azure Cosmos DB - 100 RU/s/uur - US - west 2|   US - west 2      |      1   |
|Azure Cosmos DB - 100 RU/s/uur - US - west-centraal|   US - west-centraal      |       1  |
|Azure Cosmos DB - 100 RU/s/uur - US - oost|   US - oost      |  1       |
|Azure Cosmos DB - 100 RU/s/uur - SA - noord|     SA - noord    |   1      |
|Azure Cosmos DB - 100 RU/s/uur - SA - west |    SA west      |    1     |
|Azure Cosmos DB - 100 RU/s/uur - IN - zuid|    IN - zuid     |    1,0375    |
|Azure Cosmos DB - 100 RU/s/Hour - CA - oost|   CA - oost      |    1.1      |
|Azure Cosmos DB - 100 RU/s/Hour - JA - oost|   JA - oost      |    1,125     |
|Azure Cosmos DB - 100 RU/s/Hour - JA - west|     JA - west    |   1,125       |
|Azure Cosmos DB - 100 RU/s/Hour - IN - west|     IN - west    |    1,1375     |
|Azure Cosmos DB - 100 RU/s/Hour - IN - centraal|    IN - centraal     |  1,1375       |
|Azure Cosmos DB - 100 RU/s/Hour - AU - oost|     AU - oost    |   1,15       |
|Azure Cosmos DB - 100 RU/s/Hour - CA - centraal|  CA - centraal       |   1.2       |
|Azure Cosmos DB - 100 RU/s/Hour - FR - centraal|   FR - centraal      |    1,25      |
|Azure Cosmos DB - 100 RU/s/Hour - BR - zuid|  BR - zuid       |   1.5      |
|Azure Cosmos DB - 100 RU/s/Hour - AU - centraal|   AU - centraal      |   1.5      |
|Azure Cosmos DB - 100 RU/s/Hour - AU - centraal 2| AU - centraal 2        |    1.5     |
|Azure Cosmos DB - 100 RU/s/Hour - FR - zuid|    FR - zuid     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Scenario's die aantonen hoe de reserveringskorting wordt toegepast

Dit zijn de vereisten voor een reservering:

* Vereiste doorvoer: 50.000 RU/s  
* Gebruikte regio's: 2

In dit geval bestaan de totale kosten voor on-demand uit 500 x 100 RU/s in deze twee regio's. Het totale verbruik aan RU/s is dan 100.000 per uur.

**Scenario 1**

Stel dat u Azure Cosmos DB implementaties nodig hebt in de regio's US - noord-centraal en US - west. Elke regio heeft een doorvoer van 50.000 RU/s. Een reserveringsaankoop van 100.000 RU/s zou precies voldoende zijn om uw on-demand kosten volledig te dekken.

De korting van een reservering wordt als volgt berekend: doorvoer * verhouding_reserveringskorting_voor_die_regio. Voor de regio's US - noord-centraal en US - west is de verhouding van de reserveringskorting 1. Het totale aantal RU/s met korting is 100.000. Deze waarde wordt berekend als: 50.000 * 1 + 50.000 * 1 = 100.000 RU/s. U hoeft geen extra kosten te betalen op de normale tarieven voor betalen naar gebruik.

|Meterbeschrijving | Regio |Doorvoer (RU/s) |Reserveringskorting toegepast op RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/uur - US - noord-centraal  |   US - noord-centraal  | 50,000  | 50,000  |
|Azure Cosmos DB - 100 RU/s/uur - US - west  |  US - west   |  50,000  |  50,000 |

**Scenario 2**

Stel dat u Azure Cosmos DB implementaties nodig hebt in de regio's AU - centraal 2 en FR - zuid. Elke regio heeft een doorvoer van 50.000 RU/s. Een reserveringsaankoop van 100.000 RU/s zou als volgt worden toegepast (ervan uitgaande dat de korting eerst wordt toegepast op AU - centraal 2):

|Meterbeschrijving | Regio |Doorvoer (RU/s) |Reserveringskorting toegepast op RU/s |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/s/Hour - AU - centraal 2  |  AU - centraal 2   |  50,000  |  50,000   |
|Azure Cosmos DB - 100 RU/s/Hour - FR - zuid  |  FR - zuid   |  50,000 |  15.384  |

Een gebruik van 50.000 eenheden in de regio AU - centraal 2 komt overeen met 75.000 RU/s van het factureerbare gebruik (of genormaliseerd gebruik). Deze waarde wordt als berekend als: doorvoer * verhouding_reserveringskorting_voor_die_regio. De berekening is gelijk aan 75.000 RU/s factureerbaar of genormaliseerd gebruik. Deze waarde wordt berekend als: 50.000 * 1,5 = 75.000 RU/s.

De 100.000 RU/s van de reserveringsaankoop worden eerst gebruikt voor de 75.000 RU/s in AU - centraal 2. Er blijven dan nog 25.000 RU/s over voor de regio FR - zuid. Van de resterende 25.000 RU/s wordt een reserveringskorting van 15,384 RU/s toegepast op de regio FR - zuid. De kortingswaarde wordt berekend als: 25.000 / 1,625 = 15.384 RU/s. De resterende 34.616 RU/s in de regio FR - zuid worden in rekening gebracht tegen de normale tarieven voor betalen naar gebruik.

Het factureringssysteem van Azure wijst het voordeel van de reserveringsfacturering toe aan de eerste instantie die wordt verwerkt en die overeenkomt met de reserveringsconfiguratie. In dit voorbeeld is dat AU - centraal 2.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](understand-reserved-instance-usage-ea.md) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Wat zijn reserveringen voor Azure](save-compute-costs-reservations.md)  
* [Vooruitbetalen voor Azure Cosmos DB-resources met gereserveerde capaciteit van Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Vooruitbetalen voor compute-resources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)  
* [Reserveringen voor Azure beheren](manage-reserved-vm-instance.md)  
* [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)  
* [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
* [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](/partner-center/azure-reservations)