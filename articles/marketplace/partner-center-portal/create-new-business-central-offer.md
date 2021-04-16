---
title: Een Dynamics 365 Business Central-aanbieding maken in Microsoft AppSource
description: Een Dynamics 365 for Business Central-aanbieding maken in Microsoft AppSource. Vermeld of verkoop uw aanbieding in AppSource of via het Cloud Solution Provider (CSP)-programma.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: 001f7453c29e7a8525fb88a96dd9a867468460e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501430"
---
# <a name="create-a-dynamics-365-for-business-central-offer"></a>Een Dynamics 365 for Business Central-aanbieding maken

In dit artikel wordt beschreven hoe u een nieuwe Dynamics 365 Business Central-aanbieding maakt. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) is een ERP-service (Enterprise Resource Planning) die een breed scala aan bedrijfsprocessen verwerkt, waaronder financiën, activiteiten, toeleveringsketen, CRM, projectbeheer en elektronische handel. Premium-pakketten bieden ook ondersteuning voor het klassieke implementatiemodel en productie. Alle aanbiedingen voor Dynamics 365 Business Central moeten ons certificeringsproces doorlopen.

Voordat u begint, [maakt u een commerciële Marketplace-account in Partner Center](../create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is ingeschreven in het commerciële marketplace-programma.

>[!NOTE]
> Zodra een aanbieding is gepubliceerd, worden wijzigingen in de aanbieding alleen bijgewerkt in Partner Center en de online winkel nadat u de aanbieding opnieuw hebt aangeboden voor publicatie.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan [bij Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het navigatiemenu aan de linkerkant **Commercial Marketplace**  >  **Overview**.
3. Selecteer op de pagina Overzicht **+ Nieuwe aanbieding**  >  **Dynamics 365 Business Central.**

    ![Illustreert het linkernavigatiemenu.](./media/new-offer-dynamics-365-business-central.png)

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id in.** Deze waarde is een unieke id voor elke aanbieding in uw account.

- Deze id is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- De aanbiedings-id in combinatie met de uitgevers-id moet minder dan 40 tekens lang zijn.
- Gebruik alleen kleine letters en cijfers. Het kan afbreekstreepingstekens en onderstrepingstekens bevatten, maar geen spaties. Als uw uitgevers-id bijvoorbeeld is en u `testpublisherid` **test-offer-1** in voert, is het webadres van de aanbieding `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Deze id kan niet worden gewijzigd nadat u Maken **hebt geselecteerd.**

Voer een **aanbiedingsalias in.** Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in de marketplace en verschilt van de naam van de aanbieding en andere waarden die aan klanten worden weergegeven.
- Deze naam kan niet worden gewijzigd nadat u Maken **hebt geselecteerd.**

Selecteer **Maken om** de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Aanbieding instellen

### <a name="alias"></a>Alias

Voer een beschrijvende naam in die we gebruiken om alleen binnen een bepaalde tijd naar deze aanbieding Partner Center. Deze naam (vooraf ingevuld met wat u hebt ingevoerd toen u de aanbieding hebt gemaakt) wordt niet gebruikt in de marketplace en wijkt af van de naam van de aanbieding die aan klanten wordt weergegeven. Als u de naam van de aanbieding later wilt bijwerken, gaat u naar de pagina Aanbiedingsvermelding. [](#offer-listing)

### <a name="setup-details"></a>Installatiedetails

Selecteer het **pakkettype dat** van toepassing is op uw aanbieding:

- Een **invoeg-app** breidt de ervaring en de bestaande functionaliteit van Dynamics 365 Business Central uit. Zie [Add-on apps (Invoeg-apps) voor meer informatie.](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)
- Een **Connect-app** kan worden gebruikt in het scenario waarin er een punt-naar-punt-verbinding moet worden gemaakt tussen Dynamics 365 Business Central en een oplossing of service van derden. Zie Connect [Apps (Apps verbinden) voor meer informatie.](/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)

Selecteer voor Hoe wilt u dat potentiële klanten interactie hebben met deze **aanbieding?** de optie die u wilt gebruiken voor deze aanbieding.

- **Nu downloaden (gratis)** - Maak gratis een lijst met uw aanbieding aan klanten.
- **Gratis proefversie (vermelding)** : vermeld uw aanbieding aan klanten met een koppeling naar een gratis proefversie. Aanbiedingen met gratis proefversies worden gemaakt, beheerd en geconfigureerd door uw service en hebben geen abonnementen die worden beheerd door Microsoft.

    > [!NOTE]
    > De tokens die uw toepassing ontvangt via de proefkoppeling kunnen alleen worden gebruikt om gebruikersgegevens op te halen via Azure Active Directory (Azure AD) om het maken van een account in uw app te automatiseren. Microsoft-accounts worden niet ondersteund voor verificatie met behulp van dit token.

- **Neem contact met mij** op: verzamel contactgegevens van klanten door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming om zijn of haar gegevens te delen. Deze klantgegevens, samen met de naam van de aanbieding, de id en de marketplace-bron waar ze uw aanbieding hebben gevonden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie Klant leads voor meer informatie over het configureren [van uw CRM.](#customer-leads)

### <a name="test-drive"></a>Test drive

Een test drive is een uitstekende manier om uw aanbieding aan potentiële klanten te presenteren door hen de mogelijkheid te geven om 'te proberen voordat u iets koopt', wat resulteert in een verhoogde conversie en het genereren van hoog gekwalificeerde leads. Begin voor meer informatie met [Wat is test drive.](../what-is-test-drive.md)

Als u een test drive een vaste periode wilt inschakelen, selecteert u het selectievakje **Een** test drive inschakelen. Als u test drive wilt verwijderen uit uw aanbieding, moet u dit selectievakje uit.

### <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie Overzicht van leadbeheer [voor meer informatie.](./commercial-marketplace-get-customer-leads.md)

Selecteer **Concept opslaan voordat** u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën en branches definiëren die worden gebruikt voor het groepen van uw aanbieding op marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen.

### <a name="categories"></a>Categorieën

Selecteer categorieën en subcategorieën om uw aanbieding in de juiste marketplace-zoekgebieden te plaatsen. Beschrijf in de beschrijving van de aanbieding hoe uw aanbieding ondersteuning biedt voor deze categorieën. Selecteer:

- Ten minste één en maximaal twee categorieën, waaronder een primaire en een secundaire categorie (optioneel).
- Maximaal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert **u Niet van toepassing.**

Zie de volledige lijst met categorieën en subcategorieën in [Best practices voor aanbiedingsvermeldingen.](../gtm-offer-listing-best-practices.md)

### <a name="industries"></a>Branches

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>App-versie

Voer het versienummer van uw aanbieding in. Klanten zien deze versie op de detailpagina van de aanbieding.

### <a name="terms-and-conditions"></a>Voorwaarden

Geef hier uw eigen juridische voorwaarden op. U kunt ook het adres verstrekken waar uw voorwaarden kunnen worden gevonden. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **Concept opslaan voordat** u doorgaat.

## <a name="offer-listing"></a>Aanbiedingsvermelding

Op deze pagina kunt u aanbiedingsdetails definiëren, zoals de naam van de aanbieding, beschrijving, koppelingen en contactpersonen.

> [!NOTE]
> Geef de aanbiedingsvermeldingsgegevens slechts in één taal op. Het is niet vereist om in het Engels te zijn, zolang de beschrijving van de aanbieding begint met de zin 'Deze toepassing is alleen beschikbaar in [niet-Engelse taal].' Het is ook acceptabel om een url voor een nuttige *koppeling* op te geven om inhoud aan te bieden in een andere taal dan de taal die wordt gebruikt in de aanbiedingsvermeldingsinhoud.

Hier ziet u een voorbeeld van hoe aanbiedingsgegevens worden weergegeven in Microsoft AppSource (alle vermelde prijzen zijn alleen voorbeelden en zijn niet bedoeld om de werkelijke kosten weer te geven):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Illustreert hoe deze aanbieding wordt weergegeven in Microsoft AppSource.":::

### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Logo
2. Producten
3. Categorieën
4. Ondersteuningsadres (koppeling)
5. Gebruiksvoorwaarden
6. Privacybeleid
7. Naam van aanbieding
8. Samenvatting
9. Beschrijving
10. Schermopnamen/video's

### <a name="marketplace-details"></a>Marketplace-gegevens

De **naam** die u hier optreedt, wordt aan klanten weergegeven als de titel van uw aanbiedingsvermelding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd voor De **aanbiedingsalias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan handelsmerken zijn (en u kunt handelsmerken of auteursrechtsymbolen opnemen). De naam mag niet langer zijn dan 50 tekens en mag geen emoji's bevatten.

Geef een korte beschrijving van uw aanbieding( maximaal 100 tekens) op voor de **samenvatting van zoekresultaten.** Deze beschrijving kan worden gebruikt in marketplace-zoekresultaten.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

U kunt desgewenst maximaal  drie zoektermen invoeren om klanten te helpen uw aanbieding te vinden in marketplace. Gebruik deze trefwoorden ook in uw beschrijving voor de beste resultaten.

Als u klanten wilt laten weten met welke producten **uw app werkt,** voert u maximaal drie productnamen in.

### <a name="helpprivacy-urls"></a>Help-/privacy-URL's

Voer de **Help-koppeling voor uw app** (URL) in, waar klanten meer informatie over uw aanbieding kunnen vinden. Uw Help-URL mag niet hetzelfde zijn als uw ondersteunings-URL.

Voer de **koppeling naar het privacybeleid** (URL) van uw organisatie in. U bent verantwoordelijk voor het voldoen aan de privacywetgeving en -voorschriften van uw app en het verstrekken van een geldig privacybeleid.

### <a name="contact-information"></a>Contactgegevens

Geef de naam, het e-mailadres en het telefoonnummer op voor een **contactpersoon voor ondersteuning** en een technische **contactpersoon.** Deze informatie wordt niet weergegeven aan klanten, maar is beschikbaar voor Microsoft en kan worden verstrekt aan CSP-partners.

Geef in **de sectie Contactpersoon** voor ondersteuning de ondersteunings-URL op waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden.  Uw ondersteunings-URL mag niet hetzelfde zijn als uw Help-URL.

### <a name="supporting-documents"></a>Bewijsstukken

Geef hier ten minste één (en maximaal drie) gerelateerde marketingdocumenten op, zoals whitepapers, checklists of presentaties, in PDF-indeling.

### <a name="marketplace-media"></a>Marketplace-media

Geef logo's en afbeeldingen op die worden gebruikt bij het weergeven van uw aanbieding aan klanten. Alle afbeeldingen moeten de PNG-indeling hebben.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Als u problemen hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de service die wordt gebruikt door de `https://upload.xboxlive.com` Partner Center.

#### <a name="logos"></a>Logos

Geef een PNG-bestand op voor het logo **van** grote grootte. Partner Center gebruikt dit eerste bestand om andere vereiste grootten te maken. Desgewenst kunt u de afbeelding later vervangen door uw eigen afbeelding.

Deze logo's worden op verschillende plaatsen in de lijst gebruikt:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Schermopnamen

Schermopnamen toevoegen die laten zien hoe uw aanbieding werkt. Er zijn ten minste drie schermafbeeldingen vereist en u kunt maximaal vijf schermafbeeldingen toevoegen. Alle schermafbeeldingen moeten 1280 x 720 pixels en in PNG-indeling zijn.

#### <a name="videos"></a>Video's

U kunt eventueel maximaal vier video's toevoegen die uw aanbieding demonstreren. Video's moeten worden gehost op een externe site. Voer voor elke video de naam, het adres en een miniatuurafbeelding van de video (1280 x 720 pixels) in.

Zie Best practices for marketplace offer listings (Best practices voor aanbiedingen van [Marketplace-aanbiedingen) voor meer marketplace-vermeldingen.](../gtm-offer-listing-best-practices.md)

Selecteer **Concept opslaan voordat** u doorgaat.

## <a name="availability"></a>Beschikbaarheid

Op deze pagina kunt u definiëren waar en hoe u uw aanbieding beschikbaar kunt maken.

### <a name="markets"></a>Markten

Als u de markten wilt opgeven waar uw aanbieding beschikbaar moet zijn, selecteert u **Markten bewerken** om het pop-upvenster **Marktselectie** weer te geven.

Selecteer ten minste één markt. Kies **Alles selecteren** om uw aanbieding beschikbaar te maken op elke mogelijke markt of selecteer alleen de specifieke markten die u wilt. Selecteer **Opslaan** wanneer u klaar bent.

Uw selecties hier zijn alleen van toepassing op nieuwe overnames; Als iemand uw app al op een bepaalde markt heeft en u deze markt later verwijdert, kunnen de mensen die de aanbieding al in die markt hebben, deze blijven gebruiken, maar kunnen nieuwe klanten op die markt uw aanbieding niet meer krijgen.

> [!IMPORTANT]
> Het is uw verantwoordelijkheid om te voldoen aan lokale wettelijke vereisten, zelfs als deze vereisten hier niet worden vermeld of in Partner Center. Zelfs als u alle markten, lokale wetten, beperkingen of andere factoren selecteert, kan het voorkomen dat bepaalde aanbiedingen in sommige landen en regio's worden vermeld.

### <a name="preview-audience"></a>Preview-doelgroep

Voordat u uw aanbieding live publiceert naar de bredere Marketplace-aanbieding, moet u deze eerst beschikbaar maken voor een beperkte **Preview-doelgroep.** Voer hier **een sleutel verbergen** (een tekenreeks met alleen kleine letters en/of cijfers) in. Leden van uw preview-doelgroep kunnen deze verborgen sleutel gebruiken als een token om een preview van uw aanbieding in de marketplace weer te geven.

Wanneer u klaar bent om uw aanbieding beschikbaar te maken en de preview-beperking te verwijderen, moet u sleutel verbergen verwijderen en opnieuw publiceren. 

Selecteer **Concept opslaan voordat** u doorgaat.

## <a name="technical-configuration"></a>Technische configuratie

Deze pagina definieert de technische details die worden gebruikt om verbinding te maken met uw aanbieding. Met deze verbinding kunnen we uw aanbieding inrichten voor de eindklant als ze ervoor kiezen om deze te verkrijgen.

Extensies die zijn ingediend voor uw aanbieding, moeten voldoen aan de vereisten die zijn opgegeven in de [controlelijst voor technische validatie.](/dynamics365/business-central/dev-itpro/developer/devenv-checklist-submission)

### <a name="file-upload"></a>Bestand uploaden

Als u eerder **Toevoegen** aan hebt geselecteerd, uploadt u het pakketbestand van uw aanbieding, samen met de pakketbestanden voor elke extensie waarvoor deze afhankelijkheden heeft.

#### <a name="extension-package-file"></a>Extensiepakketbestand

Upload het extensiepakketbestand (.app) voor uw aanbieding.

#### <a name="library-extension-package-file"></a>Bibliotheekextensiepakketbestand

Vereist als uw aanbieding moet worden geïnstalleerd, samen met een andere extensie die niet naar marketplace wordt gepubliceerd. Als dit het .app-bestand is, uploadt u het hier.

>[!NOTE]
>Het afhankelijkheidspakketbestand wordt niet meer gebruikt. Upload in plaats daarvan een pakketbestand voor de bibliotheekextensie.

Selecteer **Concept opslaan voordat** u doorgaat.

## <a name="supplemental-content"></a>Aanvullende inhoud

Op deze pagina kunt u aanvullende informatie verstrekken om ons te helpen uw aanbieding te valideren. Deze informatie wordt niet weergegeven aan klanten of gepubliceerd op de marketplace.

### <a name="target-release"></a>Doelrelease

Geef aan op welke versie van Microsoft Dynamics Business Central uw oplossing is gericht: **Huidige,** **Volgende hoofd-** of **Volgende secundaire**. Met deze informatie kunnen we uw oplossing op de juiste wijze testen.

### <a name="supported-editions"></a>Ondersteunde edities

Als voor uw aanbieding de Premium-editie van Microsoft Dynamics 365 Business Central is vereist, selecteert u alleen **Premium.** Selecteer anders zowel **Essentials** als **Premium.**

### <a name="key-usage-scenario"></a>Scenario voor sleutelgebruik

Upload een PDF-bestand waarin de belangrijkste gebruiksscenario's van uw aanbieding worden vermeld. Alle scenario's die hier worden vermeld, kunnen door ons validatieteam worden geverifieerd voordat we uw aanbieding voor de marketplace goedkeuren.

### <a name="test-accounts"></a>Testaccounts

Als het certificeringsteam een testaccount nodig heeft om uw aanbieding goed te kunnen beoordelen, uploadt u een PDF-, .doc- of DOCX-bestand met de gegevens van uw **Test-accounts.**

### <a name="app-tests-automation"></a>Automatisering van app-tests

Als uw aanbieding een invoeg-app is, moet u een Automation-bestand (.app) voor **app-tests** uploaden. Dit bestand is niet van toepassing op Verbinding maken met apps.

Selecteer **Concept opslaan voordat** u doorgaat.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Aanbieding indienen voor preview

Nadat u alle vereiste secties van de aanbieding hebt doorlopen, selecteert u **Controleren** en publiceren in de rechterbovenhoek van de portal.

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiingsstatus voor elke sectie van de aanbieding.
  - **Niet gestart:** de sectie is niet aan de slag gegaan en moet worden voltooid.
  - **Onvolledig:** sectie bevat fouten die moeten worden opgelost of die meer informatie vereisen. Terug naar de sectie(s) en werk deze bij.
  - **Voltooid:** sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef in **de** sectie Notities voor certificering testinstructies aan het certificeringsteam om ervoor te zorgen dat uw app correct is getest, naast eventuele aanvullende opmerkingen die nuttig zijn voor het begrijpen van uw app.
- Verzend de aanbieding voor publicatie door **Verzenden te selecteren.** We sturen u een e-mail wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug Partner Center selecteer **Go-live om** uw aanbieding naar het publiek te publiceren.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
