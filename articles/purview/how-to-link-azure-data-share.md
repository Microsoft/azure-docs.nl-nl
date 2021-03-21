---
title: Verbinding maken met de Azure-gegevens share
description: In dit artikel wordt beschreven hoe u een Azure data share-account verbindt met Azure controle sfeer liggen om assets te zoeken en gegevens afkomst bij te houden.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: edea881d67d5a677339c6ff357c1ac45f5dfd420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553356"
---
# <a name="how-to-connect-azure-data-share-and-azure-purview"></a>Verbinding maken met Azure data share en Azure controle sfeer liggen

In dit artikel wordt beschreven hoe u met Azure controle sfeer liggen verbinding maakt met uw [Azure data share](../data-share/overview.md) -account en hoe u de gedeelde gegevens sets (zowel uitgaand als binnenkomend) in uw data-erfgoed kunt beheren. Diverse data governance-personen kunnen afkomst van gegevens detecteren en volgen op grenzen als organisaties, afdelingen en zelfs data centers.

## <a name="common-scenarios"></a>Gangbare scenario's

Gegevens share afkomst is bedoeld om gedetailleerde informatie te geven voor analyse van de hoofd oorzaak en impact analyse.

### <a name="scenario-1-360-view-of-datasets-shared-inout-for-a-partner-organization-or-internal-department"></a>Scenario 1:360-weer gave van gegevens sets die worden gedeeld in/uit voor een partner organisatie of interne afdeling

Gegevens ambtenaren kunnen een lijst zien van alle gegevens sets die bidirectionele worden gedeeld met hun partner organisaties. Ze kunnen de gegevens sets zoeken en detecteren op organisatie naam en een volledig overzicht van alle uitgaande en binnenkomende shares bekijken.

### <a name="scenario-2-root-cause-analysis---upstream-dependency-on-datasets-coming-into-organization-consumer-view-of-incoming-shares"></a>Scenario 2: hoofd oorzaak analyse-upstream-afhankelijkheid van gegevens sets die in de organisatie worden weer gegeven (consumenten weergave van binnenkomende shares)

Een rapport bevat onjuiste informatie vanwege gegevens problemen uit een extern gegevens share account. De gegevens technici kunnen inzicht krijgen in Upstream-fouten, op de hoogte worden gesteld en contact opnemen met de eigenaar van de share om de problemen op te lossen die de gegevens discrepantie veroorzaken.

### <a name="scenario-3-impact-analysis-on-datasets-going-outside-organization-provider-view-of-outgoing-shares"></a>Scenario 3: impact analyse voor gegevens sets die buiten de organisatie worden weer gegeven (provider weergave van uitgaande shares)

Gegevens producenten willen weten wie er van invloed is op een wijziging in hun gegevensset. Met afkomst kan een gegevens producent eenvoudig inzicht krijgen in de impact van de downstream interne of externe partners die gegevens gebruiken met behulp van Azure data share.

## <a name="azure-data-share-and-purview-connected-experience"></a>Azure data share en controle sfeer liggen-verbonden ervaring

Ga als volgt te werk om verbinding te maken met uw Azure-gegevens share en Azure controle sfeer liggen-account:

1. Maak een controle sfeer liggen-account. Alle informatie over de gegevens share afkomst wordt verzameld door een controle sfeer liggen-account. U kunt een bestaande gebruiken of een nieuw controle sfeer liggen-account maken.

1. Verbind uw Azure-gegevens share met uw controle sfeer liggen-account.

    1. In de controle sfeer liggen-portal gaat u naar **Management Center** en maakt u verbinding met uw Azure-gegevens share via de sectie **externe verbindingen** .
    1. Selecteer **+ Nieuw** op de bovenste balk, Zoek uw Azure-gegevens share op de pop-upbalk en voeg het gegevens share-account toe. Voer een momentopname taak uit nadat u uw gegevens share hebt verbonden met controle sfeer liggen-account, zodat de gegevens delen assets en afkomst informatie zichtbaar zijn in controle sfeer liggen.

       :::image type="content" source="media/how-to-link-azure-data-share/connect-to-data-share.png" alt-text="Management Center om een Azure-gegevens share te koppelen":::

1. Voer uw moment opname uit in de Azure-gegevens share.

    - Zodra de Azure data share-verbinding tot stand is gebracht met Azure controle sfeer liggen, kunt u een moment opname voor uw bestaande shares uitvoeren. 
    - Als u geen bestaande shares hebt, gaat u naar de Azure data share-Portal om [uw gegevens te delen](../data-share/share-your-data.md) [en u te abonneren op een gegevens share](../data-share/subscribe-to-data-share.md).
    - Zodra de moment opname van de share is voltooid, kunt u de bijbehorende gegevens share activa en afkomst in controle sfeer liggen weer geven.

1. Analyseer gegevens share-accounts en deel informatie in uw controle sfeer liggen-account.

    - Selecteer op de start pagina van controle sfeer liggen-account **Bladeren per activum type** en selecteer de tegel **Azure-gegevens share** . U kunt zoeken naar een account naam, share naam, share momentopname of partner organisatie. U kunt ook filters toep assen op de pagina met zoek resultaten voor de account naam, het share type (verzonden VS-ontvangen shares).

       :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-search-result-page.png" alt-text="Pagina Azure-gegevens share in Zoek resultaten":::

    >[!Important]
    >Voor gegevens delen assets die in controle sfeer liggen worden weer gegeven, moet een momentopname taak worden uitgevoerd nadat u uw gegevens share hebt verbonden met controle sfeer liggen.

1. Spoor afkomst van gegevens sets die worden gedeeld met Azure data share.

    - Kies op de pagina Zoek resultaten van controle sfeer liggen de moment opname van de gegevens share (ontvangen/verzonden) en selecteer het tabblad **afkomst** om een afkomst Graph te zien met upstream-en downstream-afhankelijkheden.

    :::image type="content" source="media/how-to-link-azure-data-share/azure-data-share-lineage.png" alt-text="Afkomst van gegevens sets die worden gedeeld met Azure data share":::

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers handleiding voor catalogus afkomst](catalog-lineage-user-guide.md)
- [Koppeling naar Azure Data Factory voor afkomst](how-to-link-azure-data-factory.md)
