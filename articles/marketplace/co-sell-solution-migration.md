---
title: Oplossingen voor co-sell migreren van OCP GTM naar partner Center voor Microsoft AppSource
description: Meer informatie over het migreren van oplossingen voor co-sell van OCP GTM naar partner Center voor Microsoft AppSource).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 12/07/2020
ms.openlocfilehash: 6ad58283ad9078088f241a67426657eb7a538e10
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781127"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>Migratie van oplossingen voor co-sell van een OCP-GTM naar de commerciële Marketplace

Micro soft verplaatst de publicatie-ervaring. De [commerciële Marketplace](overview.md) biedt een vereenvoudigd aanbod dat kan worden gepubliceerd om gezamenlijk te worden verkocht via de drie kanalen van micro soft, door het maken en beheren van aanbiedingen te centraliseren in Partner Center, waar u al uw relatie met micro soft beheert.

Als een micro soft-partner die is inge schreven in de commerciële Marketplace, kunt u het volgende doen:

- Publiceer uw aanbiedingen centraal en verkoop gezamenlijk over micro soft direct-klanten-, partner-en verkopers kanalen.
- Zorg ervoor dat uw aanbiedingen zich in de juiste online winkel bevinden,[Microsoft AppSource](https://appsource.microsoft.com) of [Azure Marketplace](https://azure.microsoft.com), om miljoenen Cloud klanten te bereiken die met de mogelijkheden van uw aanbod afstemmen.
- Stuur uw eigen publicatie-ervaring samen met de aanbiedingen die aansluiten bij uw bedrijfs doelen.
- Stem uw aanbiedings publicatie af binnen het partner centrum, waar u al uw micro soft relationships-en co-sell-mogelijkheden beheert.
- [Marketplace-beloningen](partner-center-portal/marketplace-rewards.md)ontgrendelen.

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Vereisten om door te gaan met het samen verkopen met micro soft

Zorg ervoor dat u een actief Microsoft Partner Network lidmaatschap hebt en dat u bent Inge schreven in de commerciële Marketplace in het partner centrum.

- Neem gratis lid [van](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)de Microsoft Partner Network. Als partner hebt u toegang tot exclusieve bronnen, Program ma's, hulpprogram ma's en verbindingen om uw bedrijf uit te breiden.
- Als u geen account hebt in commerciële Marketplace, kunt u zich [Nu inschrijven](partner-center-portal/create-account.md) om te blijven samen werken met micro soft en toegang krijgen tot de volledige publicatie.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Updates publiceren om de status van co-selling Ready te bereiken

Als u wilt dat uw oplossing kan worden gedetecteerd voor verkopers en partners van micro soft, moet deze voldoen aan de [vereisten voor klaar voor de verkoop](marketplace-co-sell.md). Als u een micro soft-verkoper wilt gemotiveerd, moet uw oplossing voldoen aan de [vereisten die in aanmerking komen](marketplace-co-sell.md)voor de prikkel. Voltooi deze vereisten op het tabblad samen verkopen in Partner Center (Zie [deze afbeelding](#cosell-tab) verderop in dit artikel).

> [!NOTE]
> In commerciële Marketplace worden uw oplossingen aangeduid als ' aanbiedingen ' tijdens de publicatie-ervaring.

Nadat u zich hebt Inge schreven in de commerciële Marketplace, bereidt u de migratie van uw oplossingen vanuit OCP GTM voor.

Volg deze stappen voordat u uw oplossingen importeert vanuit OCP GTM:

1. Ga naar de [lijst met uitgevers](https://partner.microsoft.com/dashboard/account/v3/publishers/list)van uw bedrijf. Het bevat de account eigenaar, managers en ontwikkel aars die toegang hebben tot publiceren. Meer informatie over [gebruikers rollen van het partner centrum](/azure/marketplace/partner-center-portal/manage-account#define-user-roles-and-permissions).
2. Vraag een van de vermelde contact personen aan om gebruikers toe te [voegen](https://partner.microsoft.com/dashboard/account/usermanagement) aan de commerciële Marketplace als *managers* of *ontwikkel aars*, omdat alleen deze rollen oplossingen kunnen bewerken en publiceren.
3. Werk samen met uw ontwikkel aars om uw oplossingen van uw OCP GTM-account naar de commerciële Marketplace te verplaatsen.
4. Bepaal welk van de volgende handelingen u wilt uitvoeren:
    1. Deze oplossing samen voegen met een vergelijk bare aanbieding in de commerciële Marketplace.
    1. Migreer deze oplossing van OCP GTM naar de commerciële Marketplace.
    1. Deze oplossing verwijderen.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>Migreer uw oplossingen vanuit OCP GTM

1. Begin [hier](https://partner.microsoft.com/solutions/migration#)de migratie.
2. Selecteer de pagina **overzicht** en **Klik hier om aan de slag te gaan**.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="Overzichts pagina van partner Center, tabblad Overzicht.":::

3. Als u wilt migreren, selecteert u het tabblad **oplossingen** , waarin alle oplossingen worden weer gegeven die aan uw MPN-id's zijn gekoppeld.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="Overzichts pagina van partner Center, tabblad oplossingen.":::

    > [!NOTE]
    > Dit tabblad geeft aan of er nog geen oplossingen moeten worden gemigreerd naar de commerciële Marketplace. Als u wilt door gaan met samen verkopen met micro soft, zorgt u ervoor dat de gemigreerde oplossingen worden gepubliceerd op de commerciële Marketplace.

    Meer informatie over de oplossings status vindt u in de tips van het hulp programma. Alle oplossingen die in behandeling zijn, worden weer gegeven onder **actie**.<a name="beginmigration"></a>

4. Selecteer **migratie starten** (zie afbeelding hierboven) voor de oplossing die u wilt migreren en selecteer vervolgens een van de volgende opties:

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="De drie opties voor migratie.":::

### <a name="merge-solution-with-a-similar-offer"></a>Oplossing samen voegen met een vergelijk bare aanbieding

Selecteer deze optie als de oplossing al is gepubliceerd in de commerciële Marketplace en de twee moet worden samengevoegd in één aanbieding. Zo voor komt u dat er dubbele aanbiedingen worden gemaakt.

1. De bestaande aanbieding identificeren.
    1. Selecteer **Ik wil deze oplossing samen voegen met een vergelijkbaar aanbod in de commerciële Marketplace** (Zie **actie vereist** afbeelding [hierboven](#beginmigration)).
    1. Op het tabblad **actie 1** ziet u live commerciële Marketplace-aanbiedingen waarmee uw OCP gtm-oplossing kan worden gekoppeld. Selecteer de Live-aanbieding in de lijst als u er een hebt. Als er geen lijst met aanbiedingen waaruit u kunt kiezen, voert u het adres van de klant (URL) in Microsoft AppSource of Azure Marketplace.
        [![Het tabblad actie 1 van het samenvoeg proces.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. Selecteer **Doorgaan**.
1. Vraag de samen voeging aan.
    1. Op het tabblad **actie 2** worden instructies weer gegeven voor het aanvragen van het samen voegen van uw OCP gtm-oplossing met de naam die u hebt opgegeven. Als u het samen voegen wilt aanvragen, selecteert u **opslaan & contact opnemen met ondersteuning**, waarmee de pagina partner ondersteuning wordt geopend in een browser.
    1. Selecteer **Details van probleem opgeven** en voer het volgende [ ![ in: het tabblad actie 2 van het samenvoeg proces.](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Selecteer **Indienen**. Het partner ondersteunings team neemt binnen twee werk dagen contact met u op.
    1. Partner ondersteuning werkt samen met u om ervoor te zorgen dat deze aanbieding succesvol wordt samengevoegd zodat deze kan worden gepubliceerd als een live-aanbieding.

### <a name="migrate-this-solution-from-ocp-gtm"></a>Deze oplossing migreren vanuit een OCP-GTM

Selecteer deze optie als u een OCP GTM-oplossing hebt die nog geen corresponderende aanbieding heeft gepubliceerd in de commerciële Marketplace. Als u deze oplossing wilt publiceren naar de commerciële Marketplace en u wilt door gaan met de co-selling met micro soft en deze oplossing te migreren, bespaart u tijd door de informatie en de stuk lijst te bewaren vanuit OCP GTM. Voor deze optie moet u een type aanbieding selecteren.

1. Selecteer **Ik wil deze oplossing migreren van OCP gtm naar de commerciële Marketplace** (Zie **actie vereist** afbeelding [hierboven](#beginmigration)) en ga vervolgens **door**.
1. Op het tabblad **actie 1** selecteert u het [type aanbieding](publisher-guide-by-offer-type.md)en **gaat u verder**.

    [![Het tabblad actie 1 van het migratie proces.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. Selecteer op het tabblad **actie 2** het profiel van de [Uitgever](partner-center-portal/create-account.md) in de lijst die wordt weer gegeven. Als u geen uitgevers account hebt, maakt u er een in het [partner centrum](https://partner.microsoft.com/solutions/migration)en selecteert u dit hier.

    [![Het tabblad actie 2 van het migratie proces.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. Selecteer **een concept aanbieding maken** om uw oplossing te migreren naar de commerciële Marketplace als concept. Het is nog niet actief.
1. Ga door met het publicatie proces in partner centrum. Voor hulp bij het publiceren in Partner Center raadpleegt [u uw aanbieding Live in de onderstaande commerciële Marketplace](#make-your-offer-live-in-the-marketplace) .

### <a name="discard-this-solution"></a>Deze oplossing verwijderen

Selecteer deze optie wanneer een oplossing in OCP GTM-oplossingen niet meer relevant is. U wordt gevraagd om het verwijderen te bevestigen en u kunt dit ook later ongedaan maken.

1. Selecteer **Ik wil deze oplossing verwijderen** (Zie **actie vereist** afbeelding [hierboven](#beginmigration)) en ga vervolgens **door**.
2. Selecteer **negeren**.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Bevestig het verwijderen.":::

3. Als u de verwijdering ongedaan wilt maken, selecteert u **verwijderen ongedaan maken**.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="De koppeling verwijderen ongedaan maken.":::

4. Als u meer hulp nodig hebt, selecteert u het tabblad **hulp vragen** om contact op te nemen met het partner ondersteunings team.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="De koppeling ondersteuning op het tabblad hulp opvragen.":::

## <a name="make-your-offer-live-in-the-marketplace"></a>Maak uw aanbieding Live in Marketplace

Als u hebt geselecteerd voor het migreren van uw aanbieding naar de commerciële Marketplace, ontvangt deze een concept. U moet nog steeds uw aanbieding publiceren om deze Live in de commerciële Marketplace te maken. Hiermee behoudt u de gezamenlijke verkoop status, prikkels en referentie pijplijn.

Lees de juiste [publicatie handleiding](publisher-guide-by-offer-type.md)voor gedetailleerde instructies over de informatie die u moet opgeven voordat uw aanbieding kan worden gepubliceerd. Lees hieronder voor een overzicht.

1. Selecteer op de pagina **overzicht** in partner centrum de naam van uw concept aanbieding.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="De overzichts pagina van de aanbieding in partner centrum.":::

<a name="cosell-tab"></a>

2. Vul de vereiste gegevens op elk tabblad in. U kunt eventueel de pagina **door verkopen via csp's** volt ooien (in het menu links hieronder) om door het programma voor Cloud Solution Provider (CSP) te verkopen. Aan de hand van de koppelingen en knop info vindt u **meer informatie over** de vereisten en Details.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="De tabbladen Overzicht instellen in partner centrum.":::

3. Sommige van de verkopers gerichte gegevens van micro soft zijn gekopieerd van de GTM-oplossing van OCP. Voltooi de resterende vereiste informatie op het tabblad **samen met micro soft** om te zorgen dat uw aanbieding klaar is voor samen verkopen. Wanneer u klaar bent, selecteert u **controleren en publiceren**.

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="Het tabblad co-sell bieden in het partner centrum.":::

4. Nadat u alle verzonden informatie hebt bekeken, selecteert u **publiceren** om uw concept aanbieding voor een certificerings beoordeling in te dienen.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="Het tabblad beoordeling van de aanbieding en de knop publiceren in partner centrum.":::

5. Volg de status van uw inzending op het tabblad **overzicht** .

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="De status balk voor het publiceren van aanbiedingen op het tabblad Overzicht van partner centrum.":::

6. U ontvangt een melding wanneer de certificerings beoordeling is voltooid. Als we feedback op maat kunnen hebben, kunt u deze adresseren en vervolgens **publiceren** selecteren om een hercertificering te initiëren.
7. Zodra uw aanbieding is goedgekeurd, kunt u een voor beeld van de aanbieding bekijken met de koppeling die u hebt ontvangen en de gewenste definitieve aanpassingen aanbrengen. Wanneer u klaar bent, selecteert u **Live** kijken (Zie de bovenstaande knop) om uw aanbieding naar de relevante Commercial Marketplace-winkel (s) te publiceren. Als uw aanbieding eenmaal actief is, behoudt de verkoop status van de oorspronkelijke OCP GTM-oplossing.

## <a name="next-steps"></a>Volgende stappen

- [Doorverkopen via CSP-partners](cloud-solution-providers.md)
- Deze [Veelgestelde vragen](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF) weer geven
