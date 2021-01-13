---
title: Azure-prijzen voor uw organisatie bekijken en downloaden
description: Krijg meer informatie over het bekijken en downloaden van prijzen, of het schatten van de kosten met de prijzen van uw organisatie.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d563907d3567607e537eebfc5c91be02e27fd758
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014758"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Azure-prijzen voor uw organisatie bekijken en downloaden

Azure-klanten met een Azure Enterprise Agreement (EA), Microsoft-klantovereenkomst (MCA) of Microsoft Partner-overeenkomst (MPA) kunnen hun prijzen bekijken en downloaden in de Azure-portal. [Ontdek hoe u uw type factureringsrekening kunt controleren](#check-your-billing-account-type).

## <a name="download-pricing-for-an-enterprise-agreement"></a>Prijzen voor een Enterprise Agreement downloaden

Afhankelijk van beleid dat door de ondernemingsbeheerder is ingesteld voor uw organisatie, bieden alleen bepaalde beheerdersrollen toegang tot de EA-prijsgegevens van uw organisatie. Zie [Inzicht in Azure Enterprise Overeenkomst-beheerdersrollen in Azure](understand-ea-roles.md) voor meer informatie.

1. Meld u als ondernemingsbeheerder aan bij de [Azure-portal.](https://portal.azure.com/)
1. Zoek naar *Kostenbeheer en facturering*.  
   ![Schermopname van de zoekopdracht in Azure Portal.](./media/ea-pricing/portal-cm-billing-search.png)
1. Selecteer in het factureringsaccount de optie **Gebruik en kosten**.  
   ![Schermopname van het gebruik en de kosten onder Facturering](./media/ea-pricing/ea-pricing-usage-charges-nav.png)
1. Selecteer het ![downloadpictogram.](./media/ea-pricing/download-icon.png) Selecteer **Downloaden** voor de maand.
1. Selecteer onder **Prijzenoverzicht** de optie **CSV-bestand downloaden**.  
    :::image type="content" source="./media/ea-pricing/download-enterprise-agreement-price-sheet-01.png" alt-text="Schermopname van de optie Gebruik + kosten downloaden." :::

## <a name="download-pricing-for-an-mca-or-mpa-account"></a>Prijzen voor een MCA- of MPA-account downloaden

Als u een MCA hebt, moet u een eigenaar van het factureringsprofiel, inzender, lezer of factuurbeheerder zijn om prijzen te kunnen bekijken en downloaden. Als u een MPA hebt, moet u de rol Globale beheerder en de rol Beheerderagent in de partnerorganisatie hebben om prijzen te kunnen bekijken en downloaden.

### <a name="download-price-sheets-for-billed-charges"></a>Prijzenoverzichten voor gefactureerde kosten downloaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.
1. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
1. Selecteer **Facturen**.
1. Ga in het factuurraster naar de rij van de factuur die overeenkomt met het prijzenoverzicht dat u wilt downloaden.
1. Klik op het weglatingsteken (`...`) aan het einde van de rij.  
    ![Schermopname met het geselecteerde weglatingsteken](./media/ea-pricing/billingprofile-invoicegrid-new.png)
1. Als u de prijzen voor de services in de geselecteerde factuur wilt bekijken, selecteert u **Prijzenoverzicht voor facturering**.
1. Als u de prijzen voor alle Azure-services voor de betreffende factureringsperiode wilt zien, selecteert u **Azure-prijzenoverzicht**.  
    ![Schermopname van het contextmenu met prijzenoverzichten](./media/ea-pricing/contextmenu-pricesheet01.png)

### <a name="download-price-sheets-for-the-current-billing-period"></a>Prijzenoverzichten voor de huidige factureringsperiode downloaden

Als u een MCA hebt, kunt u de prijzen voor de huidige factureringsperiode downloaden.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar *Kostenbeheer en facturering*.
1. Selecteer een factureringsprofiel. Afhankelijk van uw toegang moet u mogelijk eerst een factureringsrekening selecteren.
1. Zoek in het gebied **Overzicht** de downloadkoppelingen onder de kosten (maand tot heden).
1. Selecteer **Prijzenoverzicht voor Azure**.  
    ![Schermopname waarop downloaden uit Overzicht wordt weergegeven](./media/ea-pricing/open-pricing01.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Kosten schatten met de Azure-prijscalculator

U kunt ook de prijzen van uw organisatie gebruiken om de kosten te schatten met de Azure-prijscalculator.

1. Ga naar de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator).
1. Selecteer **Aanmelden** in de rechterbovenhoek.
1. Selecteer **EA (Enterprise Agreement)** onder **Programma's en aanbieding** > **Licentieprogramma**.
1. Selecteer **Geen geselecteerd** onder **Programma's en aanbieding** > **Geselecteerde overeenkomst**.  
    ![Schermopname van de beschikbare programma's en aanbiedingen.](./media/ea-pricing/ea-pricing-calculator-estimate.png)
1. Kies de organisatie.
1. Selecteer **Toepassen**.
1. Zoek producten en voeg deze vervolgens toe aan uw schatting.
1. Geschatte prijzen die worden weergegeven, zijn gebaseerd op prijzen voor de organisatie die u hebt geselecteerd.

## <a name="check-your-billing-account-type"></a>Uw type factureringsrekening controleren
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Volgende stappen

Als u een EA-klant bent, raadpleegt u:

- [Toegang tot uw EA-factureringsgegevens voor Azure beheren](manage-billing-access.md)
- [Uw Microsoft Azure-factuur weergeven en downloaden](../understand/download-azure-invoice.md)
- [Uw Microsoft Azure-gebruik en -kosten weergeven en downloaden](../understand/download-azure-daily-usage.md)
- [Inzicht in de factuur voor Enterprise Agreement-klanten](../understand/review-enterprise-agreement-bill.md)

Als u een Microsoft-klantovereenkomst hebt, raadpleegt u:

- [Inzicht in de voorwaarden in uw prijzenoverzicht voor een Microsoft-klantovereenkomst](mca-understand-pricesheet.md)
- [Uw Microsoft Azure-factuur weergeven en downloaden](../understand/download-azure-invoice.md)
- [Uw Microsoft Azure-gebruik en -kosten weergeven en downloaden](../understand/download-azure-daily-usage.md)
- [Belastingdocumenten voor uw factureringsprofiel bekijken en downloaden](../understand/mca-download-tax-document.md)
- [Inzicht in de kosten voor de factuur van uw factureringsprofiel](../understand/review-customer-agreement-bill.md)
