---
title: Plannen maken voor uw Azure-toepassings aanbieding
description: Meer informatie over het maken van plannen voor uw Azure-toepassings aanbieding in Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370090"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Plannen maken voor uw Azure-toepassings aanbieding

Aanbiedingen die via micro soft Commercial Marketplace worden verkocht, moeten ten minste één abonnement hebben om uw aanbieding in de commerciële Marketplace te vermelden. U kunt diverse plannen maken met verschillende opties binnen dezelfde aanbieding. Deze plannen (ook wel Sku's genoemd) kunnen verschillen per plan type (_oplossings sjabloon_ of _beheerde toepassing_), verdiensten maximaliseren of doel groep. Zie [Abonnementen en prijzen voor commerciële Marketplace-aanbiedingen](plans-pricing.md)voor algemene instructies voor abonnementen.

## <a name="create-a-plan"></a>Een plan maken

1. Selecteer boven aan het tabblad **plan overzicht** de optie **+ nieuw plan maken**.
1. Voer in het dialoog venster dat wordt weer gegeven in het vak **plan-id** een unieke plan-id in. Deze ID is zichtbaar voor klanten in de product-URL. Gebruik Maxi maal 50 kleine letters, streepjes of onderstrepings tekens. U kunt de plan-ID niet wijzigen nadat u **maken** hebt geselecteerd.
1. Voer in het vak **naam van abonnement** een unieke naam in voor dit abonnement. Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Gebruik Maxi maal 50 tekens.
1. Selecteer **Maken**.

## <a name="define-the-plan-setup"></a>De plannings instellingen definiëren

Op het tabblad **installatie plannen** kunt u het type plan instellen, ongeacht of de technische configuratie van een ander abonnement opnieuw wordt gebruikt, en in welke Azure-regio's het plan beschikbaar moet zijn. Uw antwoorden op dit tabblad beïnvloeden welke velden worden weer gegeven op andere tabbladen voor dit abonnement.

### <a name="select-the-plan-type"></a>Het plan type selecteren

- Selecteer in het **type plan** lijst de **sjabloon oplossing** of de **beheerde toepassing**.

Een **oplossings sjabloon** plan wordt volledig door de klant beheerd. Met een **beheerd toepassings** abonnement kunnen uitgevers de toepassing namens de klant beheren. Zie [typen plannen](plan-azure-application-offer.md#types-of-plans)voor meer informatie over deze twee typen abonnementen.

### <a name="re-use-technical-configuration-optional"></a>Technische configuratie opnieuw gebruiken (optioneel)

Als u meer dan één abonnement van hetzelfde type in deze aanbieding hebt gemaakt en de technische configuratie identiek is, kunt u de technische configuratie van een ander abonnement gebruiken. Deze instelling kan niet worden gewijzigd nadat dit abonnement is gepubliceerd.

#### <a name="to-re-use-technical-configuration"></a>De technische configuratie opnieuw gebruiken

1. Schakel het selectie vakje de **technische configuratie van een ander abonnement van hetzelfde type opnieuw gebruiken** in.
1. Selecteer in de lijst die wordt weer gegeven het basis plan dat u wilt.

> [!NOTE]
> Wanneer u pakketten uit een ander abonnement opnieuw gebruikt, verdwijnt het hele tabblad **technische configuratie** uit dit plan. De technische configuratie details van het andere abonnement, inclusief eventuele updates die u in de toekomst maakt, worden ook voor dit abonnement gebruikt.

### <a name="select-azure-regions-clouds"></a>Azure-regio's (Clouds) selecteren

Uw abonnement moet beschikbaar worden gesteld in ten minste één Azure-regio. Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke Azure-regio, kunt u die regio niet verwijderen uit uw aanbieding.

#### <a name="azure-global-region"></a>Wereld wijde Azure-regio

Het selectie vakje **globaal Azure** is standaard ingeschakeld. Hierdoor is uw plan beschikbaar voor klanten in alle wereld wijde Azure-regio's die een commerciële Marketplace-integratie hebben. Voor beheerde toepassings abonnementen kunt u selecteren met markten waarvoor u uw plan beschikbaar wilt maken.

Als u uw aanbieding uit deze regio wilt verwijderen, schakelt u het selectie vakje **Azure Global** uit.

#### <a name="azure-government-region"></a>Azure Government regio

Deze regio voorziet in gecontroleerde toegang voor klanten van Amerikaanse federale, staats-, lokale of tribalee entiteiten, evenals partners die in aanmerking komen voor hen. Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de Verenigde Staten).

Azure Government services verwerken gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS. Als u uw certificeringen voor deze Program ma's wilt controleren, kunt u Maxi maal 100 koppelingen opgeven. Deze kunnen links naar uw vermelding in het programma staan, of koppelingen naar beschrijvingen van uw naleving met hen op uw eigen websites. Deze koppelingen zijn alleen zichtbaar voor Azure Government klanten.

##### <a name="to-select-the-azure-government-region"></a>De Azure Government regio selecteren

1. Schakel het selectie vakje **Azure Government** in.
1. Onder **Azure Government-certificeringen** selecteert u **+ certificering toevoegen (Maxi maal 100)**.
1. Geef in de weer gegeven vakken een naam en een koppeling naar een certificering op.
1. Herhaal stap 2 en 3 om een andere certificering toe te voegen.

Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: plan vermelding.

## <a name="define-the-plan-listing"></a>De plan vermelding definiëren

Op het tabblad **plan vermelding** kunt u de vermeldings gegevens van het plan configureren. Dit tabblad bevat specifieke informatie over het verschil tussen plannen in dezelfde aanbieding. U kunt de naam, samen vatting en beschrijving van het abonnement definiëren zoals u wilt dat deze in de commerciële Marketplace worden weer gegeven.

1. In het vak **naam van abonnement** is de naam die u eerder hebt ingesteld voor dit plan hier weer gegeven. U kunt deze op elk gewenst moment wijzigen. Deze naam wordt weer gegeven in de commerciële Marketplace als de titel van het software abonnement van uw aanbieding en is beperkt tot 100 tekens.
1. Geef in het vak **samen vatting van abonnement** een korte samen vatting van uw plan op (niet de aanbieding). Deze samen vatting is beperkt tot 100 tekens.
1. In het vak **plan beschrijving** wordt uitgelegd wat dit software plan uniek is en wat de verschillen zijn van andere abonnementen binnen uw aanbieding. Beschrijf de aanbieding niet, alleen het abonnement. Deze beschrijving mag Maxi maal 2.000 tekens bevatten.
1. Selecteer **concept opslaan** voordat u doorgaat.

## <a name="next-steps"></a>Volgende stappen

Voer een van de volgende handelingen uit:

- Als u een oplossings sjabloon plan wilt configureren, gaat u naar [een oplossings sjabloon plan configureren](create-new-azure-apps-offer-solution.md).
- Als u een beheerd toepassings abonnement wilt configureren, gaat u naar [een beheerd toepassings plan configureren](create-new-azure-apps-offer-managed.md).
