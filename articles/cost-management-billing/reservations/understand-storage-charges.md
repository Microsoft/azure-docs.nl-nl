---
title: Meer informatie over de manier waarop reserverings kortingen worden toegepast op Azure Storage-services | Microsoft Docs
description: Meer informatie over hoe gereserveerde capaciteits kortingen worden toegepast op Azure Blob-opslag, Azure Files en Azure Data Lake Storage Gen2 resources.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024027"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>Meer informatie over de manier waarop reserverings kortingen worden toegepast op Azure Storage-services 
Met Azure Storage-services kunt u geld besparen op opslag kosten door capaciteit te reserveren. Azure Blob-opslag, Azure Files en Azure Data Lake Storage gen 2-ondersteunings exemplaren. Nadat u de gereserveerde capaciteit hebt aangeschaft, wordt de reserverings korting automatisch toegepast op de opslag resources die overeenkomen met de voor waarden van de reserve ring. De reserveringskorting wordt alleen toegepast op de opslagcapaciteit. Voor de bandbreedte en aanvraagsnelheid gelden tarieven per gebruik.

Zie [kosten optimaliseren voor Blob Storage met gereserveerde capaciteit](../../storage/blobs/storage-blob-reserved-capacity.md)voor meer informatie over Azure Blob-opslag en de gereserveerde capaciteit van Azure data Lake opslag van generatie 1. Zie voor meer informatie over Azure Files gereserveerde capaciteit [kosten optimaliseren voor Azure files met gereserveerde capaciteit](../../storage/files/files-reserve-capacity.md).

Voor informatie over prijzen voor Azure Blob Storage-reserve ringen raadpleegt u de prijzen voor [blok-blobs](https://azure.microsoft.com/pricing/details/storage/blobs/) en [Azure data Lake Storage prijs gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/). Voor informatie over Azure Files prijzen voor opslag reservering, Zie [Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files).

## <a name="how-the-reservation-discount-is-applied"></a>Hoe de reserveringskorting wordt toegepast
De korting voor gereserveerde capaciteit wordt op elk uur toegepast op ondersteunde opslag resources.

De korting voor gereserveerde capaciteit is de korting ' use-it-or-kwijtraakt-it '. Als u geen blok-blobs, Azure-bestands shares of Azure Data Lake Storage Gen2 resources hebt die voldoen aan de voor waarden van de reserve ring voor een bepaald uur, verliest u een reserverings hoeveelheid voor dat uur. U kunt ongebruikte gereserveerde uren niet meenemen.

Wanneer u een resource verwijdert, wordt de reserveringskorting automatisch toegepast op een andere overeenkomstige resource in het opgegeven bereik. Als er geen overeenkomstige resources in het opgegeven bereik worden gevonden, verliest u de gereserveerde uren.

## <a name="discount-examples"></a>Kortingsvoorbeelden
De volgende voor beelden laten zien hoe de gereserveerde capaciteits korting van toepassing is, afhankelijk van de implementaties.

Stel dat u voor een periode van 1 jaar 100 TiB van gereserveerde capaciteit hebt aangeschaft in de regio vs West 2. Uw reserve ring is voor opslag van lokaal redundante opslag (LRS) in de laag Hot Access.

De kosten van deze voorbeeldreservering bedragen $ 18.540. U kunt ervoor kiezen om het volledige bedrag vooraf te betalen of om de komende twaalf maanden een vaste maandelijkse termijn van $ 1545 te betalen.

In deze voorbeelden wordt ervan uitgegaan dat u zich hebt geregistreerd voor een maandelijks betalingsplan voor uw reserveringen. In de volgende scenario's wordt beschreven wat er gebeurt als u uw gereserveerde capaciteit te weinig of te veel gebruikt.

### <a name="underusing-your-capacity"></a>Uw capaciteit onderschrijden
Stel dat u in een bepaald uur binnen de reserverings periode slechts 80 TiB van uw gereserveerde capaciteit van 100 TiB hebt gebruikt. De resterende 20 TiB wordt niet voor dat uur toegepast en wordt niet overgedragen.

### <a name="overusing-your-capacity"></a>Uw capaciteit overschrijden
Stel dat u in een bepaald uur binnen de reserverings periode 101 TiB van de opslag capaciteit hebt gebruikt. De reserverings korting is van toepassing op 100 TiB van uw gegevens en de resterende 1 TiB wordt in rekening gebracht tegen betalen naar gebruik-tarieven voor dat uur. Als uw gebruik in het volgende uur wordt gewijzigd in 100 TiB, wordt al het gebruik gedekt door de reserve ring.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen
Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- [Kosten optimaliseren voor blob-opslag met gereserveerde capaciteit](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Kosten optimaliseren voor Azure Files met gereserveerde capaciteit](../../storage/files/files-reserve-capacity.md)
- [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
