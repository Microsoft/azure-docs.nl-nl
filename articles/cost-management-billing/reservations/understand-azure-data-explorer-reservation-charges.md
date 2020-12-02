---
title: Reserveringskorting toepassen op Azure Data Explorer
description: Inzicht in hoe reserveringskorting wordt toegepast op de markeringenmeter van Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: b222e4ff519fbe2fbb4fa58b39555ac9fead0917
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352912"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Reserveringskorting toepassen op Azure Data Explorer

Wanneer u gereserveerde capaciteit voor Azure Data Explorer hebt gekocht, wordt de reserveringskorting automatisch toegepast op Azure Data Explorer-resources die overeenkomen met de kenmerken en hoeveelheid van de reservering. Een reservering omvat de markeringskosten van Azure Data Explorer. Het omvat geen berekeningen, netwerken, opslag of andere Azure-resources die worden gebruikt om een Azure Data Explorer-cluster uit te voeren. Reserveringen voor die resources moeten afzonderlijk worden gekocht.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: '*gebruiken of verliezen*'. Als u voor een bepaald uur geen passende resources hebt, verliest u de reserveringshoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource afsluit, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Reserveringskorting die wordt toegepast op Azure Data Explorer-clusters

Een reserveringskorting wordt op uurbasis toegepast op Azure Data Explorer-markeringen. Voor Azure Data Explorer-resources die niet het volledige uur worden uitgevoerd, wordt de korting automatisch toegepast op andere Data Explorer-resources die overeenkomen met de reserveringskenmerken. De korting kan worden toegepast op Azure Data Explorer-resources die gelijktijdig worden uitgevoerd. Als u geen Azure Data Explorer-resources hebt die het hele uur worden uitgevoerd en voldoen aan de reserveringsvoorwaarden, profiteert u niet optimaal van de korting voor de reserveringscapaciteit voor dat uur.

> [!NOTE]
> * Het wordt **sterk aanbevolen** om [reserveringscapaciteit](../../virtual-machines/prepay-reserved-vm-instances.md) aan te schaffen voor virtuele machines die worden gebruikt voor het Azure Data Explorer-cluster om de besparingen met gereserveerde capaciteit te maximaliseren.
> * Met de aankoop van een reservering worden kortingen toegepast op alle regio's.

## <a name="examples"></a>Voorbeelden

In de volgende voorbeelden ziet u hoe de korting voor de gereserveerde capaciteit voor Azure Data Explorer wordt toegepast, afhankelijk van het aantal markeringseenheden dat u hebt aangeschaft en wanneer ze worden uitgevoerd.
Bijvoorbeeld voor de engineclustergrootte: **2 D11_v2-VM's**, zijn uw totale kosten voor vier eenheden van Azure Data Explorer-markeringenmeters per uur.

**Scenario 1**

U koopt gereserveerde capaciteit voor Azure Data Explorer voor acht Azure Data Explorer-markeringseenheden. U voert een enginecluster uit van twee D13_v2-VM's met in totaal 16 kernen voor 16 eenheden van Azure Data Explorer-markeringseenheden per uur en de rest van de kenmerken komen overeen met de reservering. U betaalt naar gebruik voor acht kernen van Azure Data Explorer-berekeningsgebruik en krijgt de reserveringskorting voor een uur van acht kernen aan Azure Data Explorer-markeringseenheidgebruik.

Voor de rest van deze voorbeelden wordt ervan uitgegaan dat u gereserveerde capaciteit voor Azure Data Explorer koopt voor een Azure Data Explorer met 16 kernen en dat de rest van de reserveringskenmerken overeenkomen met de actieve Azure Data Explorer-clusters.

**Scenario 2**

U hebt twee Azure Data Explorer-engineclusters met elk acht kernen voor een uur in twee verschillende regio's. De reserveringskorting voor 16 kernen wordt toegepast op zowel het cluster als de 16 eenheden van Azure Data Explorer-markeringen die worden gebruikt.

**Scenario 3**

U hebt een Azure Data Explorer-enginecluster met 16 kernen van 13:00 tot 13:30. U hebt nog een Azure Data Explorer-enginecluster met 16 kernen van 13:30 tot 14:00. Beide worden gedekt door de reserveringskorting.

**Scenario 4**

U hebt een Azure Data Explorer-enginecluster met 16 kernen van 13:00 tot 13:45. U hebt nog een Azure Data Explorer-enginecluster met 16 kernen van 13:30 tot 14:00. Voor de overlapping van 15 minuten wordt de prijs voor betalen per gebruik in rekening gebracht. De reserveringskorting geldt voor het Azure Data Explorer-markeringsgebruik voor de rest van die tijd.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](understand-reserved-instance-usage-ea.md) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Vooruitbetalen voor Azure Data Explorer-rekenresources met gereserveerde capaciteit voor Azure Data Explorer](/azure/data-explorer/pricing-reserved-capacity)  
* [Wat zijn reserveringen voor Azure](save-compute-costs-reservations.md)  
* [Azure-reserveringen beheren](manage-reserved-vm-instance.md)  
* [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
* [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
* [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](/partner-center/azure-reservations)