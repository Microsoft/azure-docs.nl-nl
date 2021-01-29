---
title: Gebruik van Azure-reservering voor een afzonderlijk abonnement
description: Lees hoe u uw gebruik kunt lezen om te begrijpen hoe de Azure-reservering voor uw afzonderlijke abonnement met tarieven voor betalen-naar-gebruik wordt toegepast.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: f6aad15d70157dda110fce233fbdafb7f3d896ad
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055056"
---
# <a name="understand-azure-reservation-usage-for-your-individual-subscription-with-pay-as-you-go-rates-subscription"></a>Meer informatie over het gebruik van Azure-reserveringen voor uw afzonderlijke abonnement met tarieven op basis van betalen-naar-gebruik

Gebruik de pagina ReservationId van de [reserve ring](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) en het gebruiks bestand van de [Azure Portal](https://portal.azure.com) om uw reserverings gebruik te evalueren.

Als u een klant bent met een Enterprise Overeenkomst, raadpleegt u [Het gebruik van reserveringen voor uw Enterprise-inschrijving begrijpen.](understand-reserved-instance-usage-ea.md).

In dit artikel wordt ervan uitgegaan dat de reservering wordt toegepast op een enkel abonnement. Als de reservering wordt toegepast op meer dan één abonnement, kan het reserveringsvoordeel meerdere CSV-bestanden voor gebruik omvatten.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Gebruik voor gereserveerde instanties van virtuele machines

Voor de volgende secties wordt ervan uitgegaan dat u een Standard_DS1_v2 Windows-VM in de regio VS-Oost gebruikt en dat de gegevens van uw gereserveerde instantie van de virtuele machine eruitzien als in de volgende tabel:

| Veld | Waarde |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Aantal |1|
|SKU | Standard_DS1_v2|
|Regio | eastus |

Het hardware-gedeelte van de virtuele machine wordt gedekt omdat de geïmplementeerde virtuele machine overeenkomt met de reserveringskenmerken. Zie [Azure Reserve VM Instances Windows-softwarekosten](reserved-instance-windows-software-costs.md) voor meer informatie over de Windows-software die niet wordt gedekt door de gereserveerde VM-instantie

### <a name="statement-section-of-csv-file-for-vms"></a>Overzichtssectie van het CSV-bestand voor VM's

In deze sectie van uw CSV-bestand wordt het totale gebruik voor uw reservering weergegeven. Pas het filter toe op het veld **Subcategorie van de meter** met de naam **"Reservering-"** . Er verschijnt uitvoer die er ongeveer als volgt uitziet:

![Schermopname van gefilterde details en kosten van reserveringsgebruik](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

De regel **Reservering - Basis-VM** bevat het totale aantal uren dat wordt gedekt door de reservering. Deze regel is $ 0,00, omdat dit wordt gedekt door de reservering. De regel **Reservering-Windows Svrr (1-kern)** omvat de kosten van Windows-software.

### <a name="daily-usage-section-of-csv-file"></a>De sectie Dagelijks gebruik in het CSV-bestand

Filter op **Extra gegevens** en typ uw **Reserverings-id**. Op de volgende schermopname ziet u de velden met betrekking tot de reservering.

![Schermopname van de details en kosten van dagelijks gebruik](./media/understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** in het veld **Aanvullende informatie** is de reservering die wordt toegepast op de virtuele machine.
2. **ConsumptionMeter** is de meter-id voor de virtuele machine.
3. De regel **Reservering - Basis-VM** **Subcategorie van de meter** vertegenwoordigt de kosten van $ 0 in de overzichtssectie. De kosten voor het uitvoeren van deze virtuele machine zijn al betaald door de reservering.
4. **Meter-id** is de meter-id voor de reservering. De kosten van deze meter zijn $ 0. Deze meter-id wordt weergegeven voor elke VM die in aanmerking komt voor de reserveringskorting.
5. Standard_DS1_v2 is een vCPU-VM en de virtuele machine wordt zonder Azure Hybrid Benefit geïmplementeerd. Deze meter dekt dus de extra kosten van de Windows-software. Zie [Windows-softwarekosten voor gereserveerde Azure VM-instanties](reserved-instance-windows-software-costs.md) als u de meter wilt vinden die overeenkomt met de D-serie 1-core-VM. Als u de Azure Hybrid Benefit hebt, worden deze extra kosten niet toegepast.

## <a name="usage-for-sql-database--cosmos-db-reservations"></a>Gebruik voor SQL Database- Cosmos DB-reserveringen

In de volgende secties wordt Azure SQL Database als voorbeeld gebruikt om het gebruiksrapport te beschrijven. U kunt dezelfde stappen ook gebruiken om het gebruik van Azure Cosmos DB te verkrijgen.

Stel dat u een SQL Database Gen 4 in de regio VS-Oost gebruikt en dat uw reserveringsgegevens eruitzien zoals in de volgende tabel:

| Veld | Waarde |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|Aantal |2|
|Product| SQL Database Gen 4 (2 Core)|
|Regio | eastus |

### <a name="statement-section-of-csv-file"></a>Overzichtssectie van het CSV-bestand

Filter op meternaam **Gebruik gereserveerde instanties** en kies de vereiste **Metercategorie** - Azure SQL Database of Azure Cosmos DB. Er verschijnt uitvoer die er ongeveer als volgt uitziet:

![Schermopname van een Metercategorie-vermelding.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

De regel **Gebruik gereserveerde instanties** bevat het totale aantal core-uren waarop de reservering betrekking heeft. Het tarief voor deze regel is $ 0 als de reservering de kosten dekt.

### <a name="detail-section-of-csv-file"></a>Overzichtssectie van het CSV-bestand

Filter op **Extra gegevens** en typ uw **Reserverings-id**. De volgende schermopname toont de velden die zijn gerelateerd aan de in SQL Database gereserveerde capaciteitsreservering.

![Schermopname met details van een CSV-bestand voor gereserveerde capaciteit.](./media/understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **ReservationId** in het veld **Aanvullende informatie** is de in SQL Database gereserveerde capaciteitsreservering die wordt toegepast op de SQL Database-resource.
2. **ConsumptionMeter** is de meter-id voor de SQL Database-resource.
3. **Meter-id** is de meter-id voor de reservering. De kosten van deze meter zijn $ 0. Voor alle SQL Database-resources die in aanmerking komen voor de reserveringskorting, wordt deze meter-id in het CSV-bestand weergegeven.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Vooruitbetalen voor compute-resources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure-reserveringen beheren](manage-reserved-vm-instance.md)
- [Begrijpen hoe de reserveringskorting wordt toegepast](../manage/understand-vm-reservation-charges.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
- [Kosten van Windows-software zijn niet inbegrepen bij reserveringen](reserved-instance-windows-software-costs.md)