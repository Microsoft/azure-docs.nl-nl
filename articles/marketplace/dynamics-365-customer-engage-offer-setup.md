---
title: Maak een Dynamics 365 for Customer Engagement-& PowerApps-aanbieding op Microsoft AppSource (Azure Marketplace).
description: Maak een Dynamics 365 for Customer Engagement-& PowerApps-aanbieding op Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: d8b3a2da7ccbbbf866763dbe5c8c59b00e7abd10
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820417"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Een Dynamics 365 for Customer Engagement-aanbieding maken & PowerApps

In dit artikel wordt beschreven hoe u een Dynamics 365 for Customer Engagement-& PowerApps-aanbieding maakt. Alle aanbiedingen voor Dynamics 365 doorlopen ons certificeringsproces. Met de proefversie kunnen gebruikers uw oplossing implementeren in een live Dynamics 365-omgeving.

Voordat u begint, maakt u een commerciële marketplace-account in [Partner Center](partner-center-portal/create-account.md) en zorgt u ervoor dat het is ingeschreven in het commerciële marketplace-programma.

## <a name="before-you-begin"></a>Voordat u begint

Bekijk [Plan a Dynamics 365 offer (Een Dynamics 365-aanbieding plannen).](marketplace-dynamics-365.md) Hier worden de technische vereisten voor deze aanbieding uitgelegd en worden de informatie en assets vermeld die u nodig hebt wanneer u deze maakt.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan [bij Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het navigatiemenu aan de linkerkant **Commerciële**  >  **marketplace-overzicht.**
3. Selecteer op de pagina Overzicht **+ Nieuwe aanbieding**  >  **Dynamics 365 for Customer Engagement & PowerApps.**

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Toont de menuopties in het linkerdeelvenster en de knop Nieuwe aanbieding met Customer Engagement selecteren.":::

> [!IMPORTANT]
> Nadat een aanbieding is gepubliceerd, worden alle wijzigingen die u aan de aanbieding aanbiedt, Partner Center pas weergegeven op Microsoft AppSource nadat u de aanbieding opnieuw hebt gepubliceerd. Zorg ervoor dat u een aanbieding altijd opnieuw wilt publiceren nadat u deze hebt veranderd.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id in.** Dit is een unieke id voor elke aanbieding in uw account.

- Deze id is zichtbaar voor klanten in het webadres voor de aanbieding en in Azure Resource Manager-sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. De id kan afbreekstreepingen en onderstrepingstekens bevatten, maar geen spaties en mag maximaal 50 tekens bevatten. Als uw uitgevers-id bijvoorbeeld is en u `testpublisherid` **test-offer-1** in voert, is het webadres van de aanbieding `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- De aanbiedings-id kan niet worden gewijzigd nadat u **Maken hebt geselecteerd.**

Voer een **aanbiedingsalias in.** Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in AppSource. Deze verschilt van de naam van de aanbieding en andere waarden die aan klanten worden weergegeven.
- Deze naam kan niet worden gewijzigd nadat u Maken **hebt geselecteerd.**

Selecteer **Maken om** de aanbieding te genereren. Partner Center wordt de pagina **Aanbieding instellen** geopend.

## <a name="alias"></a>Alias

Voer een beschrijvende naam in die we gebruiken om alleen binnen een bepaalde tijd naar deze aanbieding Partner Center. De alias van de aanbieding (vooraf ingevuld met wat u hebt ingevoerd tijdens het maken van de aanbieding) wordt niet gebruikt in marketplace en wijkt af van de naam van de aanbieding die aan klanten wordt weergegeven. Zie de pagina Aanbiedingsvermelding als u de naam van de aanbieding [later wilt](dynamics-365-customer-engage-offer-listing.md) bijwerken.

## <a name="setup-details"></a>Installatiedetails

Selecteer voor Hoe wilt u dat potentiële klanten interactie hebben met deze **aanbieding?** de optie die u wilt gebruiken voor deze aanbieding.

- **Nu downloaden (gratis)** - Maak gratis een lijst met uw aanbieding aan klanten.
- **Gratis proefversie (aanbieding)** : vermeld uw aanbieding aan klanten met een koppeling naar een gratis proefversie. Aanbiedingen met gratis proefversies worden gemaakt, beheerd en geconfigureerd door uw service en hebben geen abonnementen die worden beheerd door Microsoft.

    > [!NOTE]
    > De tokens die uw toepassing ontvangt via uw proefkoppeling, kunnen alleen worden gebruikt om gebruikersgegevens op te halen via Azure Active Directory (Azure AD) om het maken van een account in uw app te automatiseren. Microsoft-accounts worden niet ondersteund voor verificatie met behulp van dit token.

- **Neem contact met mij** op: verzamel contactgegevens van klanten door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming om zijn of haar gegevens te delen. Deze klantgegevens, samen met de naam van de aanbieding, de id en de marketplace-bron waar ze uw aanbieding hebben gevonden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie Klant leads voor meer informatie over het configureren [van uw CRM.](#customer-leads)

## <a name="test-drive"></a>Test drive

Een test drive is een uitstekende manier om uw aanbieding aan potentiële klanten te presenteren door hen voor een vast aantal uren toegang te geven tot een vooraf geconfigureerde omgeving. Het aanbieden test drive resulteert in een verhoogde conversiesnelheid en genereert hoog gekwalificeerde leads. Begin voor meer informatie met [Wat is een test drive?](what-is-test-drive.md).

> [!TIP]
> Een test drive verschilt van een gratis proefversie. U kunt een test drive, gratis proefversie of beide. Beide bieden klanten uw oplossing voor een vaste periode. Maar een test drive bevat ook een praktijkgestuurde rondleiding door de belangrijkste functies en voordelen van uw product, die in een praktijkscenario worden gedemonstreerd.

Als u een test drive wilt  inschakelen, selecteert u het selectievakje Een test drive inschakelen en selecteert u **het Type test drive**. U configureert de test drive later. Met test drive moet u uw aanbieding ook configureren voor een CRM-systeem voor leads van klanten (zie de volgende sectie). Als u test drive wilt verwijderen uit uw aanbieding, moet u dit selectievakje uit.

## <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

Zie Leads van klanten van uw commerciële [marketplace-aanbieding voor meer informatie.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

Selecteer **Concept opslaan** voordat u verdergaat met het volgende tabblad in het menu aan de linkerkant, **Eigenschappen.**

## <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van aanbieding configureren](dynamics-365-customer-engage-properties.md)
- [Best practices voor aanbiedingsvermeldingen](gtm-offer-listing-best-practices.md)