---
title: Meer informatie over reserve ringen voor Azure Data Factory gegevens stromen | Microsoft Docs
description: Meer informatie over hoe een reserverings korting wordt toegepast op het uitvoeren van ADF-gegevens stromen. De korting wordt op elk uur toegepast op deze gegevens stromen.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716292"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Hoe een reserverings korting wordt toegepast op Azure Data Factory gegevens stromen

Nadat u de gereserveerde capaciteit van de ADF-gegevens stroom hebt gekocht, wordt de reserverings korting automatisch toegepast op gegevens stromen met behulp van een Azure Integration runtime die overeenkomt met het reken type en het kern aantal van de reserve ring.

## <a name="how-reservation-discount-is-applied"></a>De manier waarop reserveringskorting wordt toegepast

Voor een reserveringskorting geldt: '*gebruiken of verliezen*'. Als u dus geen overeenkomende Azure-integratie resources hebt gebruikt voor een uur, verliest u een reserverings hoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u stopt met het gebruik van de Integration runtime voor gegevens stromen, wordt de reserverings korting automatisch toegepast op een andere overeenkomende resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, *verliest* u de gereserveerde uren.

## <a name="discount-applied-to-adf-data-flows"></a>Korting toegepast op ADF-gegevens stromen

De gereserveerde capaciteits korting voor de ADF-gegevens stroom wordt toegepast voor het uitvoeren van Integration Runtimes per uur. De reserve ring die u aanschaft, komt overeen met het reken type dat wordt gebruikt door de Integration Runtimes voor uw gegevens stromen. Voor gegevens stromen die niet het volledige uur uitvoeren, wordt de reserve ring automatisch toegepast op andere gegevens stromen die overeenkomen met de reserverings kenmerken. De korting kan ook worden toegepast op gegevens stromen die gelijktijdig worden uitgevoerd. Als u geen gegevens stromen hebt die worden uitgevoerd voor het volledige uur dat overeenkomt met de reserverings kenmerken, krijgt u niet het volledige voor deel van de reserverings korting voor dat uur.

In de volgende voor beelden ziet u hoe de gereserveerde capaciteit van de ADF-gegevens stroom van toepassing is, afhankelijk van het aantal kernen dat u hebt aangeschaft en wanneer ze worden uitgevoerd.

- Scenario 1: u koopt een data transport-reserve ring van ADF voor 1 uur 80 kern geheugens van de reken kracht door 80 in te voeren als de hoeveelheid voor het reken type geoptimaliseerd voor geheugen. U voert een gegevens stroom uit met een Azure Integration runtime-set op 144 kern geheugens die gedurende één uur zijn geoptimaliseerd. U betaalt de betalen naar gebruik-prijs voor 64 kernen van het gebruik van gegevens stromen gedurende één uur. U krijgt de reserverings korting voor een uur van 80 kern geheugen gebruik.
- Scenario 2: u koopt een data transport-reserve ring van ADF voor 1 uur van 32 kernen van algemene doel einden door 32 in te voeren als het aantal voor het reken type voor algemene doel einden. U kunt gedurende één uur fouten opsporen in uw gegevens stromen met behulp van 32 kernen van algemene Compute Azure Integration runtime. U krijgt de reserverings korting voor het hele uur gebruik.

Raadpleeg [Meer informatie over Azure-reserveringsgebruik](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) voor meer informatie over en inzicht in hoe uw Azure-reserveringen worden toegepast in uw gebruiksrapporten voor facturering.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over Azure Reservations:

- [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)