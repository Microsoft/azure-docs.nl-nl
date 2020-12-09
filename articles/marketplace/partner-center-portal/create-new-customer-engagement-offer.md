---
title: Een Dynamics 365 maken voor klant betrokkenheid & PowerApps-aanbieding in micro soft Commercial Marketplace
description: Het maken van een nieuwe Dynamics 365 voor klant betrokkenheid & PowerApps-aanbieding voor het aanbieden of verkopen in azure Marketplace, AppSource of via het programma Cloud Solution Provider (CSP) in partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: f20405add698c64ecedfd7375063ba934a98e158
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922441"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Een Dynamics 365 for Customer Engagement- en PowerApps-aanbieding maken

In dit onderwerp wordt uitgelegd hoe u een nieuwe Dynamics 365 maakt voor klant betrokkenheid & PowerApps-aanbieding. Alle apps voor Dynamics 365 voor klant betrokkenheid (PowerApps, Sales, service, project service en Field Service) moeten het certificerings proces door lopen en een proef ervaring bieden. Het certificerings proces controleert uw oplossing op standaard vereisten, compatibiliteit en goede prak tijken. Met de proef ervaring kunnen gebruikers uw oplossing implementeren in een live Dynamics 365-omgeving.

Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuw aanbod**  >  **Dynamics 365 voor klant betrokkenheid & PowerApps**.

    ![Illustreert het navigatie menu.](./media/new-offer-dynamics-365-customer-engagement-powerapps.png)

> [!NOTE]
> Nadat een aanbieding is gepubliceerd, worden wijzigingen die zijn aangebracht in het partner centrum alleen weer gegeven in online winkels na het opnieuw publiceren van de aanbieding. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

## <a name="new-offer"></a>Nieuwe aanbieding

Voer een **aanbiedings-id** in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- De aanbiedings-ID gecombineerd met de uitgevers-ID moet langer zijn dan 40 tekens.
- Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar geen spaties. Als uw uitgevers-ID bijvoorbeeld testpublisherid is en u **test-aanbieding-1** invoert, is het webadres van de aanbieding `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Voer een **alias** voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
- Dit kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-setup"></a>Installatie van aanbieding

Volg deze stappen om uw aanbieding in te stellen.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Hoe wilt u potentiële klanten interactie laten doen met deze aanbieding?

Selecteer de optie die u wilt gebruiken voor deze aanbieding.

#### <a name="get-it-now-free"></a>Nu downloaden (gratis)

Bied uw aanbod aan klanten gratis aan.

#### <a name="free-trial-listing"></a>Gratis proef versie (lijst)

Bied uw aanbieding aan klanten aan met een koppeling naar een gratis proef versie. Gratis proef versies van aanbieding worden gemaakt, beheerd en geconfigureerd door uw service en er zijn geen abonnementen die door micro soft worden beheerd.

> [!NOTE]
> De tokens die uw toepassing via uw proef koppeling ontvangt, kunnen alleen worden gebruikt voor het verkrijgen van gebruikers gegevens via Azure Active Directory (Azure AD) om het maken van accounts in uw app te automatiseren. Micro soft-accounts worden niet ondersteund voor verificatie met behulp van dit token.

#### <a name="contact-me"></a>Contact opnemen

Contact gegevens van klanten verzamelen door verbinding te maken met uw CRM-systeem (Customer Relationship Management). De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, ID en Marketplace-bron waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. Zie [leads van klanten](#customer-leads)voor meer informatie over het configureren van uw CRM.

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="test-drive"></a>Station testen

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen de mogelijkheid te bieden om te kopen voordat u aan de slag gaat, wat resulteert in een verhoogde conversie en de generatie van uiterst gekwalificeerde leads. Begin met [Wat is test drive](../what-is-test-drive.md)voor meer informatie.

Als u een test drive voor een bepaalde periode wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in. Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

### <a name="customer-leads"></a>Leads van klanten

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Zie [Lead Management Overview](./commercial-marketplace-get-customer-leads.md)voor meer informatie.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën en industrieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, uw app-versie en de juridische contracten die uw aanbieding ondersteunen.

### <a name="category"></a>Categorie

Selecteer categorieën en subcategorieën om uw aanbieding te plaatsen in de juiste Zoek gebieden voor Marketplace. Zorg ervoor dat u beschrijft hoe uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. Selecteer:

- Ten minste één en Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

Bekijk de volledige lijst met categorieën en subcategorieën in [Aanbevolen procedures voor aanbiedingen](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Branche

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Toepasselijke Dynamics 365-producten

Selecteer alle Dynamics 365-producten waarop deze aanbieding van toepassing is.

### <a name="app-version"></a>App-versie

Voer het versie nummer van uw aanbieding in. Klanten zien dat deze versie wordt weer gegeven op de detail pagina van de aanbieding. Als u het versie nummer alleen bijwerkt als gevolg van marketing/beschrijvende wijzigingen, schakelt u het selectie vakje **alleen marketing** in. Met deze optie kan de aanbieding de certificerings-en inrichtings fases omzeilen.

### <a name="terms-and-conditions"></a>Voorwaarden

Geef hier uw eigen juridische voor waarden op. U kunt ook het adres opgeven waar uw voor waarden kunnen worden gevonden. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="offer-listing"></a>Aanbieding weer geven

Op deze pagina worden de talen weer gegeven waarin uw aanbieding wordt weer gegeven. **Engels (Verenigde Staten)** is momenteel de enige beschik bare optie.

Geef hier Marketplace-Details op voor elke taal/markt, zoals aanbiedings naam, beschrijving en installatie kopieën. Selecteer de naam van de taal/markt om deze informatie op te geven.

> [!NOTE]
> De inhoud van het aanbiedings aanbod (zoals de beschrijving, documenten, scherm afbeeldingen en gebruiks voorwaarden) is niet vereist voor het Engels, zolang de beschrijving van het aanbod begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook acceptabel om een *nuttige koppelings-URL* te bieden om inhoud te bieden in een andere taal dan de versie die wordt gebruikt in de inhoud van de aanbieding.

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in Microsoft AppSource (alle prijzen zijn bijvoorbeeld alleen bedoeld als voor beeld van de werkelijke kosten):

:::image type="content" source="media/example-azure-marketplace-d365-customer-engagement.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Logo
2. Producten
3. Categorieën
4. Ondersteunings adres (koppeling)
5. Gebruiksvoorwaarden adres (koppeling)
6. Naam van aanbieding
7. Description
8. Scherm afbeeldingen/Video's

### <a name="name"></a>Name

De naam die u hier invoert, wordt aan klanten weer gegeven als de titel van de aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd voor de **aanbiedings alias** tijdens het maken van de aanbieding, maar u kunt deze waarde wijzigen. Deze naam kan worden aangemerkt (en u kunt symbolen van het handels merk of copyright bevatten). De naam mag niet langer zijn dan 50 tekens en mag geen emojis bevatten.

### <a name="short-description"></a>Korte beschrijving

Geef een korte beschrijving van uw aanbieding, Maxi maal 100 tekens. Deze beschrijving kan worden gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Tref woorden zoeken

U kunt eventueel Maxi maal drie Zoek trefwoorden invoeren om klanten te helpen uw aanbieding op Marketplace te vinden. Voor de beste resultaten gebruikt u deze tref woorden ook in uw beschrijving.

### <a name="products-your-app-works-with"></a>Producten waarmee uw app werkt

Als u klanten wilt laten weten dat uw app werkt met specifieke producten, voert u hier Maxi maal drie product namen in.

### <a name="support-urls"></a>Url's voor ondersteuning

In deze sectie vindt u koppelingen waarmee klanten meer inzicht kunnen krijgen in uw aanbieding.

#### <a name="help-link"></a>Help-koppeling

Voer het adres in waar klanten meer kunnen weten over uw aanbieding.

#### <a name="privacy-policy-url"></a>URL van privacybeleid

Voer het adres in voor het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy en om een geldig privacybeleid te bieden.

### <a name="contacts"></a>Contactpersonen

Geef de naam, het e-mail adres en het telefoon nummer op voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft en kan worden verschaft aan CSP-partners.

In het gedeelte **ondersteuning voor contact opnemen** moet u ook de **ondersteunings-URL** opgeven waar CSP-partners ondersteuning voor uw aanbieding kunnen vinden.

### <a name="supporting-documents"></a>Ondersteunende documenten

Geef hier ten minste één (en Maxi maal drie) gerelateerde marketing documenten op, zoals witboeken, brochures, controle lijsten of presentaties, in PDF-indeling.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

Bied logo's en installatie kopieën voor uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door https://upload.xboxlive.com Partner Center gebruikte service niet blokkeert.

#### <a name="store-logos"></a>Winkel logo's

Geef een PNG-bestand voor het logo van **grote** grootte op. Het partner centrum gebruikt deze om een **klein** logo te maken. U kunt dit eventueel later vervangen door een andere installatie kopie.

- **Groot** (van 216 x 216 tot 350 x 350 px, vereist)
- **Klein** (48 x 48 px, optioneel)

Deze logo's worden op verschillende plaatsen in de vermelding gebruikt:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Schermopnamen

Scherm afbeeldingen toevoegen die laten zien hoe uw aanbieding werkt. Ten minste één scherm opname is vereist en u kunt Maxi maal vijf toevoegen. Alle scherm afbeeldingen moeten 1280 x 720 pixels zijn.

#### <a name="videos"></a>Video's

U kunt optioneel Maxi maal vier Video's toevoegen die uw aanbieding aantonen. Deze Video's moeten worden gehost op YouTube en/of Vimeo. Voer voor elke video de naam, de URL en een miniatuur afbeelding van de video in (1280 x 720 pixels)

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

[Best practices voor Marketplace-aanbiedingen](../gtm-offer-listing-best-practices.md)

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="availability"></a>Beschikbaarheid

Op deze pagina kunt u definiëren waar en hoe uw aanbieding beschikbaar moet worden gesteld.

### <a name="markets"></a>Landen

Geef de markten op waarin uw aanbieding beschikbaar moet zijn. Als u dit wilt doen, selecteert u **markten bewerken** om het pop-upvenster **markt selectie** weer te geven.

Standaard zijn er geen markten geselecteerd, maar u moet ten minste één markt selecteren om uw aanbieding te publiceren. Kies  **Alles selecteren** om uw aanbieding beschikbaar te stellen op elke mogelijke markt of selecteer de specifieke markten die u wilt toevoegen.

Uw selecties zijn hier alleen van toepassing op nieuwe verwervingen; Als iemand uw app al in een bepaalde markt heeft en u deze markt later verwijdert, kunnen de personen die de aanbieding al op die markt hebben, deze blijven gebruiken, maar kunnen er geen nieuwe klanten op deze markt worden aangeboden.

> [!IMPORTANT]
> Het is uw verantwoordelijkheid om te voldoen aan alle lokale wettelijke vereisten, zelfs als deze vereisten hier niet worden vermeld of in het partner centrum. Zelfs als u alle markten selecteert, kunnen lokale wetten en beperkingen of andere factoren verhinderen dat bepaalde aanbiedingen in sommige landen en regio's worden weer gegeven.

### <a name="preview-audience"></a>Voor beeld van doel groep

Voordat u uw aanbieding naar de bredere Marketplace-aanbieding publiceert, moet u deze eerst beschikbaar maken voor een beperkte preview- **doel groep**. Voer hier een **Hide-toets** (wille keurige teken reeks met alleen kleine letters en/of cijfers) in. Leden van uw preview-doel groep kunnen deze verbergen sleutel als een token gebruiken om een voor beeld van uw aanbieding op Marketplace te bekijken.

Wanneer u klaar bent om uw aanbieding beschikbaar te maken en de preview-beperking te verwijderen, moet u de **sleutel verbergen** verwijderen en opnieuw publiceren.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="technical-configuration"></a>Technische configuratie

Op deze pagina worden de technische details gedefinieerd die worden gebruikt om verbinding te maken met uw aanbieding. Met deze verbinding kan ons uw aanbieding voor de eind klant inrichten als deze ervoor kiezen deze te verkrijgen.

### <a name="base-license-model"></a>Basis licentie model

Het basis licentie model bepaalt hoe klanten uw toepassing toewijzen in het CRM-beheer centrum. Selecteer **resource** voor op exemplaren gebaseerde licentie verlening of **gebruiker** als er één per Tenant aan licenties wordt toegewezen.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Vereist S2S-uitgaande en CRM Secure Store-toegang

Schakel dit selectie vakje in om de configuratie van de beveiligde Store van CRM of de server-naar-server (S2S) uitgaande toegang in te scha kelen. Deze functie vereist een specialistische overweging van het Dynamics 365-team tijdens de certificerings periode. Micro soft neemt contact met u op om extra stappen uit te voeren om deze functie te ondersteunen.

### <a name="application-configuration-url"></a>Configuratie-URL van de toepassing

Zorg ervoor dat dit veld leeg blijft. Dit wordt gebruikt voor toekomstig gebruik. 

### <a name="crm-package"></a>CRM-pakket

Voer in het veld **URL van uw pakket locatie** de URL in van een Azure Blob Storage-account dat het geüploade bestand CRM package. zip bevat. Neem een alleen-lezen SAS-sleutel op in de URL, zodat micro soft uw pakket voor verificatie kan ophalen.

> [!IMPORTANT]
> Als u een publicatie blok wilt voor komen, moet u ervoor zorgen dat de verval datum in de URL van de Blob-opslag nog niet is verlopen. U kunt de datum wijzigen door toegang tot uw beleid te krijgen. We raden u aan de **verloop tijd** ten minste één maand in de toekomst te hebben.

Selecteer het vak met het label **Er is meer dan één CRM-pakket in het pakket bestand**, indien van toepassing. Als dat het geval is, moet u alle pakketten in uw zip-bestand toevoegen.

Zie [stap 3: een AppSource-pakket maken voor uw app](/powerapps/developer/common-data-service/create-package-app-appsource)voor meer informatie over het bouwen van uw pakket en het bijwerken van de structuur.

### <a name="crm-package-availability"></a>Beschik baarheid van CRM-pakket

Selecteer in deze sectie **+ regio toevoegen** om de geografische regio's op te geven waarin uw CRM-pakket beschikbaar is voor klanten. Implementatie in de volgende soevereine regio's vereisen speciale machtigingen en validatie tijdens het certificerings proces: [Duitsland](../../germany/index.yml), [Amerikaanse OVERHEIDS Cloud](../../azure-government/documentation-government-welcome.md)en tip.

De **configuratie-URL** voor de toepassing die u hierboven hebt opgegeven, wordt standaard gebruikt voor elke regio. Als u wilt, kunt u een afzonderlijke configuratie-URL voor de toepassing invoeren voor een of meer specifieke regio's. 

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="test-drive-technical-configuration"></a>Technische configuratie van test drive

Op deze pagina kunt u een demonstratie ("test drive") instellen waarmee klanten uw aanbieding kunnen proberen voordat ze deze aanschaffen. Meer informatie vindt u in [Wat is test drive](../what-is-test-drive.md).

Als u een test drive wilt inschakelen, schakelt u het selectie vakje **een test drive inschakelen** in op het tabblad installatie van de [aanbieding](#test-drive) . Als u test drive uit uw aanbieding wilt verwijderen, schakelt u dit selectie vakje uit.

Wanneer u klaar bent met het instellen van uw test drive, selecteert u **concept opslaan** voordat u doorgaat.

## <a name="supplemental-content"></a>Aanvullende inhoud

Op deze pagina kunt u aanvullende informatie over uw aanbieding opgeven zodat we uw aanbieding kunnen valideren. Deze informatie wordt niet weer gegeven aan klanten of gepubliceerd op Marketplace.

### <a name="key-usage-scenario"></a>Scenario voor sleutel gebruik

Upload een PDF-bestand met een lijst met belang rijke scenario's voor het gebruik van uw aanbieding. Alle scenario's kunnen door ons validatie team worden geverifieerd voordat we uw aanbieding voor Marketplace goed keuren.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="publish"></a>Publiceren

### <a name="submit-offer-to-preview"></a>Aanbieding verzenden naar Preview

Nadat u alle vereiste delen van de aanbieding hebt voltooid, selecteert u **controleren en publiceren** in de rechter bovenhoek van de portal.

Als dit de eerste keer is dat u deze aanbieding publiceert, kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding.
    - **Niet gestart** : de sectie is niet gerakend en moet worden voltooid.
    - **Onvolledig** : de sectie bevat fouten die moeten worden hersteld of waarvoor meer informatie moet worden verstrekt. Ga terug naar de sectie (s) en werk deze bij.
    - **Voltooid** : de sectie is voltooid, alle vereiste gegevens zijn opgegeven en er zijn geen fouten. Alle secties van de aanbieding moeten een volledige status hebben voordat u de aanbieding kunt indienen.
- Geef in de sectie **opmerkingen voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest, naast eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.
- Verzend de aanbieding voor publicatie door **verzenden** te selecteren. We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Ga terug naar het partner centrum en selecteer **Go-Live** voor de aanbieding om het publiek te publiceren.

## <a name="next-step"></a>Volgende stap

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
