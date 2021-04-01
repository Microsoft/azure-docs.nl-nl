---
title: Een Azure IoT Edge-module aanbieding maken met het partner centrum in azure Marketplace
description: Meer informatie over het maken, configureren en publiceren van een IoT Edge module aanbod in azure Marketplace met behulp van partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 23af7b3f7cfd5ec0531c5cac73cf8a334ef4aa62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99096437"
---
# <a name="create-an-iot-edge-module-offer"></a>Aanbieding voor IoT Edge-module maken

In dit artikel wordt beschreven hoe u een Internet of Things (IoT) Edge-module aanbieding voor Azure Marketplace maakt en publiceert. Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op de pagina overzicht **+ nieuwe aanbieding**  >  **IOT Edge module**.

    ![Illustreert het navigatie menu.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Nadat een aanbieding is gepubliceerd, worden wijzigingen die zijn aangebracht in het partner centrum alleen weer gegeven in online winkels na het opnieuw publiceren van de aanbieding. Zorg ervoor dat u altijd opnieuw publiceert nadat u wijzigingen hebt aangebracht.

### <a name="offer-id-and-alias"></a>Aanbiedings-ID en alias

Voer een **aanbiedings-id** in. Dit is een unieke id voor elke aanbieding in uw account.

- Deze ID is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50. Als u bijvoorbeeld **test-aanbieding-1** invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Voer een **alias** voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in Marketplace en wijkt af van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
- Dit kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="offer-overview"></a>Overzicht van aanbieding

De **overzichts** pagina van de aanbieding bevat een visuele weer gave van de stappen die nodig zijn om deze aanbieding (zowel voltooid als gepland) te publiceren en hoe lang elke stap moet worden voltooid.

Deze pagina bevat koppelingen voor het uitvoeren van bewerkingen op deze aanbieding op basis van de selectie die u maakt. Bijvoorbeeld:

- Als de aanbieding een concept is-concept verwijderen
- Als de aanbieding Live is: [de aanbieding wordt niet meer verkocht](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Als de aanbieding in Preview- [Live](../review-publish-offer.md#previewing-and-approving-your-offer) staat
- Als u Publisher-afmelding niet hebt voltooid, kunt u het [Publiceren annuleren.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Installatie van aanbieding

Volg deze stappen om uw aanbieding in te stellen.

### <a name="customer-leads"></a>Leads van klanten

Wanneer u uw aanbieding naar Marketplace publiceert met het partner centrum, kunt u deze optioneel koppelen aan uw CRM-systeem (Customer Relationship Management). Zo kunt u contact gegevens van klanten ontvangen zodra iemand interesse in of gebruikt voor uw product.

1. Selecteer een leadbestemming waarnaar wij de klantenleads moeten sturen. Het partner Centrum ondersteunt de volgende CRM-systemen:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) voor klant betrokkenheid
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Als uw CRM-systeem niet hierboven wordt vermeld, gebruikt u [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) of [https-eind punt](commercial-marketplace-lead-management-instructions-https.md) om klant lead gegevens op te slaan en exporteert u de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de doel locatie van de lead wanneer u publiceert in het partner centrum.
3. Controleer of de verbinding met de doel locatie juist is geconfigureerd. Nadat u het in het partner centrum hebt gepubliceerd, valideren we de verbinding en sturen we u een test lead. Wanneer u een voor beeld van de aanbieding bekijkt voordat deze live gaat, kunt u ook uw lead verbinding testen door zelf te proberen de aanbieding aan te schaffen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de doel locatie van de lead bijgewerkt blijft, zodat u geen leads kwijtraakt.

Hier volgen enkele aanvullende bronnen voor het beheer van leads:

- [Leads van uw aanbieding in de commerciële marketplace](commercial-marketplace-get-customer-leads.md)
- [Veelgestelde vragen over Lead beheer](../lead-management-faq.md#common-questions-about-lead-management)
- [Problemen met configuratie fouten van de lead oplossen](../lead-management-faq.md#publishing-config-errors)
- [Overzicht Lead beheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace en de juridische contracten die uw aanbieding ondersteunen.

#### <a name="category"></a>Categorie

Selecteer categorieën en subcategorieën om uw aanbieding te plaatsen in de juiste Zoek gebieden voor Marketplace. Zorg ervoor dat u beschrijft hoe uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. Selecteer:

- Ten minste één en Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

Bekijk de volledige lijst met categorieën en subcategorieën in [Aanbevolen procedures voor aanbiedingen](../gtm-offer-listing-best-practices.md). In Marketplace worden IOT Edge-modules altijd weer gegeven in de categorie  **Internet of Things**  >  **IOT Edge module**   .

#### <a name="legal"></a>Juridisch

U moet voor waarden voor de aanbieding opgeven. U hebt hiervoor twee opties:

- Gebruik het standaard contract voor micro soft Commercial Marketplace.
- Geef uw eigen voor waarden op.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard-contract voor micro soft Commercial Marketplace

We bieden een standaard contract sjabloon om trans acties in de commerciële Marketplace te vergemakkelijken. U kunt ervoor kiezen om uw oplossing aan te bieden onder het standaard contract, wat klanten alleen eenmaal hoeven te controleren en accepteren. Dit is een goede optie als u geen aangepaste voor waarden wilt aanwijzen.

Zie voor meer informatie over het standaard contract [Standard-contract voor de micro soft Commercial Marketplace](../standard-contract.md). U kunt ook het [standaard](https://go.microsoft.com/fwlink/?linkid=2041178) -PDF-bestand downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

Als u het standaard contract wilt gebruiken, schakelt u het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** in en klikt u op **accepteren**.

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor micro soft Commercial Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. U kunt uw oplossing aanbieden onder het Standard-contract of onder uw eigen voor waarden.

![Illustreert het gebruik van het standaard contract voor het selectie vakje commerciële Marketplace van micro soft.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Uw eigen voor waarden

Als u uw eigen aangepaste voor waarden wilt opgeven, voert u deze in het vak voor **waarden** in. U kunt een onbeperkte hoeveelheid tekst tekens in dit vak invoeren. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie, aanbieding aanbieden.

## <a name="offer-listing"></a>Aanbieding weer geven

Hier definieert u de details van het aanbod dat op Marketplace wordt weer gegeven. Dit omvat de naam van de aanbieding, beschrijving, installatie kopieën, enzovoort. Zorg ervoor dat u het beleid volgt dat wordt beschreven op de beleids pagina van micro soft tijdens het configureren van deze aanbieding.

> [!NOTE]
> De details van de aanbieding hoeven niet in het Engels te zijn als de beschrijving van de aanbieding begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook handig om een nuttige koppeling te bieden om inhoud te bieden in een andere taal dan die wordt gebruikt in de details van de aanbieding.

### <a name="name"></a>Name

De naam die u hier opgeeft, wordt weer gegeven als de titel van uw aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd in het vak **aanbiedings alias** wanneer u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan worden gemerkt (en u kunt symbolen van het handels merk of copyright bevatten).
- Mag niet langer zijn dan 50 tekens.
- Kan geen emojis bevatten.

### <a name="search-results-summary"></a>Samen vatting van zoek resultaten

Geef een korte beschrijving van uw aanbieding. Dit kan Maxi maal 100 tekens lang zijn en wordt gebruikt in Zoek resultaten voor Marketplace.

### <a name="long-summary"></a>Lange samen vatting

Geef een gedetailleerdere beschrijving van uw aanbieding. Dit kan Maxi maal 256 tekens lang zijn en wordt gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge module-aanbiedingen moeten aan de onderkant van de beschrijving een alinea met minimale hardwarevereisten bevatten, zoals:

- Minimale hardwarevereisten: Linux x64 en arm32 OS, 1 GB RAM, 500 MB aan opslag ruimte

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>URL van privacybeleid

Voer het webadres in van het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw aanbieding voldoet aan de wetten en voor schriften van de privacy. U bent ook verantwoordelijk voor het posten van een geldig privacybeleid op uw website.

#### <a name="useful-links"></a>Handige koppelingen

Bied aanvullende online documenten over uw aanbieding. U kunt Maxi maal 25 koppelingen toevoegen. Selecteer **+ een koppeling toevoegen** en vul vervolgens de volgende velden in om een koppeling toe te voegen:

- **Titel** : klanten zien de titel op de pagina met details van de aanbieding.
- **Koppeling (URL)** : Voer een koppeling voor klanten in om uw online document weer te geven. De koppeling moet beginnen met `http://` of `https://` .

Zorg ervoor dat u ten minste één koppeling naar uw documentatie en een koppeling naar de compatibele IoT Edge apparaten toevoegt vanuit de [Azure IOT-apparaatinstantie](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Contactgegevens

U moet de naam, het e-mail adres en het telefoon nummer opgeven voor een **ondersteunings contact** en een **technische contact persoon.** Deze informatie wordt niet weer gegeven aan klanten. Het is beschikbaar voor micro soft en kan worden meegeleverd met de Cloud Solution Provider (CSP)-partners.

- Ondersteunings contact (vereist): voor algemene ondersteunings vragen.
- Technische contact persoon (vereist): voor technische vragen en certificerings problemen.
- CSP-programma: Neem contact op met de leverancier van het CSP-programma.

Geef in de sectie **ondersteunings contact persoon** het webadres op van de **ondersteunings website** waar partners ondersteuning voor uw aanbieding kunnen vinden op basis van het feit of de aanbieding beschikbaar is in wereld wijd Azure, Azure Government of beide.

Geef in de sectie **contact persoon** van het CSP-programma de koppeling op (**CSP-programma marketing materiaal**) waarin CSP-partners marketing materiaal voor uw aanbieding kunnen vinden.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

Zie [Aanbevolen procedures](../gtm-offer-listing-best-practices.md)voor aanbieding voor meer informatie over het maken van aanbiedingen.

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

Bied logo's en installatie kopieën die u kunt gebruiken met uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben. Vage afbeeldingen worden geweigerd.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door https://upload.xboxlive.com Partner Center gebruikte service niet blokkeert.

#### <a name="store-logos"></a>Winkel logo's

Geef een PNG-bestand voor het logo van **grote** grootte op. Het partner centrum gebruikt deze om een **klein** en **gemiddeld** logo te maken. U kunt deze desgewenst later vervangen door andere installatie kopieën.

- **Groot** (van 216 x 216 tot 350 x 350 px, vereist)
- **Gemiddeld** (90 x 90 px, optioneel)
- **Klein** (48 x 48 px, optioneel)

Deze logo's worden op verschillende plaatsen in de vermelding gebruikt:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Scherm afbeeldingen (optioneel)

Voeg Maxi maal vijf scherm opnamen toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot en in PNG-indeling zijn.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg Maxi maal vijf Video's toe die uw aanbieding aantonen. Voer de naam van de video, het webadres en een miniatuur van de PNG-afbeelding van de video in op 1280 x 720 pixels.

#### <a name="marketplace--examples"></a>Marketplace-voor beelden

Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Groot logo
2. Categorieën
3. Ondersteunings adres (koppeling)
4. Voorwaarden
5. Adres van privacybeleid (koppeling)
6. Name
7. Samenvatting
8. Description
9. Handige koppelingen
10. Scherm afbeeldingen/Video's

<br>Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de zoek resultaten van Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de zoek resultaten voor Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Klein logo
2. Naam van aanbieding
3. Samen vatting van zoek resultaten

<br>Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de Azure Portal:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Naam
2. Beschrijving
3. Handige koppelingen
4. Schermopnamen

<br>Hier volgt een voor beeld van hoe de aanbiedings gegevens worden weer gegeven in de Azure Portal Zoek resultaten:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal Zoek resultaten.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Klein logo
2. Naam van aanbieding
3. Samen vatting van zoek resultaten

<br>Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie, voor beeld.

## <a name="preview"></a>Preview

Op het **tabblad preview** kunt u een beperkte preview- **doel groep** kiezen voor het valideren van uw aanbieding voordat u deze live publiceert naar het bredere publiek van de Marketplace.

> [!IMPORTANT]
> Nadat u uw aanbieding in Preview hebt bekeken, moet u **Live go** selecteren om uw aanbieding naar het publiek te publiceren.

Geef uw preview-doel groep op met behulp van de Azure-abonnements-ID-GUID'S, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten.

> [!NOTE]
> U kunt uw Azure-abonnements-ID vinden op de pagina Abonnementen in de Azure Portal.

Voeg ten minste één Azure-abonnements-ID afzonderlijk toe (Maxi maal 10) of door een CSV-bestand te uploaden (Maxi maal 100). Door deze abonnement-Id's toe te voegen, definieert u wie een voor beeld van uw aanbieding kan bekijken voordat deze Live wordt gepubliceerd. Als uw aanbieding al Live is, kunt u een preview-doel groep definiëren om wijzigingen of updates van uw aanbieding te testen.

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie, plan overzicht.

## <a name="plan-overview"></a>Overzicht van plan

Op dit tabblad kunt u verschillende plan opties bieden binnen dezelfde aanbieding in Partner Center. Plannen (voorheen Sku's) kunnen verschillen in wat voor Clouds beschikbaar zijn, zoals globale Clouds, overheids Clouds en de afbeelding waarnaar wordt verwezen door het plan. Als u uw aanbieding in Marketplace wilt aanbieden, moet u ten minste één abonnement instellen.

U kunt Maxi maal 100 abonnementen maken voor elke aanbieding: Maxi maal 45 van deze kunnen privé zijn. Meer informatie over privé-abonnementen [vindt u in de micro soft Commercial Marketplace](../private-offers.md).

Nadat u uw plannen hebt gemaakt, wordt op het tabblad Overzicht van het **plan** het volgende weer gegeven:

- Namen van plannen
- Prijsmodel
- Azure-regio's (wereld wijd of overheid)
- Huidige publicatie status
- Beschik bare acties

De acties die beschikbaar zijn in het plan overzicht variëren, afhankelijk van de huidige status van uw abonnement. Deze omvatten:

- **Concept verwijderen**: als de plan status een concept is.
- **Verkoop plan stoppen**: als de plan status Live is gepubliceerd.

### <a name="create-new-plan"></a>Nieuw plan maken

Selecteer **nieuw plan maken**. Het dialoog venster **nieuw plan** wordt weer gegeven.

Maak in het vak **abonnement-id** een unieke plan-id voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in het webadres van het product. Gebruik alleen kleine letters en cijfers, streepjes of onderstrepings tekens en Maxi maal 50.

Voer in het vak **naam van abonnement** een naam in voor dit abonnement. Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak in deze aanbieding een unieke naam voor elk abonnement. U kunt bijvoorbeeld een aanbiedings naam van **Windows Server** gebruiken met plannen **Windows Server 2016** en **Windows Server 2019**.

> [!NOTE]
> De plan-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Selecteer **Maken**.

### <a name="plan-setup"></a>Installatie plannen

Op dit tabblad kunt u configureren in welke Clouds het plan beschikbaar is. Uw antwoorden op dit tabblad bepalen welke velden op andere tabbladen worden weer gegeven.

#### <a name="azure-regions"></a>Azure-regio's

Alle plannen voor IoT Edge module aanbiedingen worden automatisch beschikbaar gemaakt in **Azure Global**.  Uw abonnement kan door klanten worden gebruikt in alle wereld wijde Azure-regio's die gebruikmaken van Marketplace. Zie [geografische Beschik baarheid en valuta ondersteuning](../marketplace-geo-availability-currencies.md)voor meer informatie.

Selecteer de optie [Azure Government](../../azure-government/documentation-government-welcome.md) om uw oplossing hier weer te geven. Dit is een community-Cloud met gecontroleerde toegang voor klanten van de Amerikaanse federale, staats-en lokale of tribale overheids instanties en partners die in aanmerking komen voor de overheid. Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures voor deze Cloud community. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de Verenigde Staten). Voordat u naar Azure Government [publiceert](../../azure-government/documentation-government-manage-marketplace-partners.md) , moet u de oplossing in dat gebied testen en bevestigen, omdat de resultaten mogelijk verschillend zijn. Als u uw oplossing wilt klaarzetten en testen, vraagt u een proef account aan bij [Microsoft Azure Government proef versie](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Nadat uw plan is gepubliceerd en beschikbaar is in een specifieke regio, kunt u die regio niet verwijderen.

#### <a name="azure-government-certifications"></a>Azure Government-certificeringen

Deze optie is alleen zichtbaar als **Azure Government** is geselecteerd onder **Azure-regio's**.

Azure Government services verwerken gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS. Als u uw certificeringen voor deze Program ma's wilt behalen, kunt u Maxi maal 100 koppelingen opgeven die uw certificeringen beschrijven. Dit kunnen koppelingen zijn naar uw aanbiedingen op het programma, rechtstreeks of op uw eigen website. Deze koppelingen zijn alleen zichtbaar voor Azure Government klanten.

### <a name="plan-listing"></a>Aanbieding plannen

Dit tabblad bevat specifieke informatie voor elk abonnement binnen dezelfde aanbieding.

### <a name="plan-name"></a>Plan naam

Dit is vooraf ingevuld met de naam die u hebt opgegeven bij het maken van het abonnement. U kunt deze naam, indien nodig, wijzigen. Het kan Maxi maal 50 tekens lang zijn. Deze naam wordt weer gegeven als de titel van dit plan in azure Marketplace en de Azure Portal. De naam wordt als de standaard modulenaam gebruikt nadat het plan gereed is om te worden gebruikt.

### <a name="plan-summary"></a>Samen vatting plannen

Geef een korte samen vatting van uw abonnement (niet de aanbieding). Deze samen vatting wordt weer gegeven in de zoek resultaten van Azure Marketplace en kan Maxi maal 100 tekens bevatten.

### <a name="plan-description"></a>Plan beschrijving

Beschrijf wat dit plan uniek maakt, evenals verschillen tussen de plannen binnen uw aanbieding. Beschrijf de aanbieding niet, alleen het abonnement. Deze beschrijving wordt weer gegeven in azure Marketplace en in de Azure Portal op de aanbiedings pagina. Dit kan hetzelfde zijn als de inhoud die u in de samen vatting van het abonnement hebt gegeven en Maxi maal 2.000 tekens bevat.

Selecteer **concept opslaan** nadat u deze velden hebt voltooid.

#### <a name="plan-examples"></a>Voor beelden plannen

Hier volgt een voor beeld van de details van het Azure Marketplace-plan (alle prijzen in de lijst zijn alleen bedoeld als voor beeld van de werkelijke kosten):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Illustreert Details van het Azure Marketplace-abonnement.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Naam van aanbieding
2. Plan naam
3. Plan beschrijving

<br>Hier volgt een voor beeld van de details van het Azure Portal plan (alle prijzen in de lijst zijn alleen bedoeld als voor beeld van de werkelijke kosten):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Illustreert de details van het Azure Portal plan.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Naam van aanbieding
2. Plan naam
3. Plan beschrijving

### <a name="availability"></a>Beschikbaarheid

Als u uw gepubliceerde aanbieding wilt verbergen zodat klanten deze niet kunnen zoeken, zoeken of kopen op Marketplace, schakelt u het selectie vakje **plan verbergen** op het tabblad Beschik baarheid in.

Dit veld wordt meestal gebruikt wanneer:

- De aanbieding is bedoeld om alleen indirect te worden gebruikt wanneer er wordt verwezen naar een andere toepassing.
- De aanbieding mag niet afzonderlijk worden gekocht.
- Het plan werd gebruikt voor de eerste test en is niet meer relevant.
- Het abonnement werd gebruikt voor tijdelijke of seizoen aanbiedingen en kan niet meer worden aangeboden.

## <a name="technical-configuration"></a>Technische configuratie

Het **IOT Edge-module** aanbod type is een specifiek type container dat wordt uitgevoerd op een IOT edge apparaat. Op het tabblad **technische configuratie** vindt u referentie gegevens voor de opslag plaats van de container installatie kopie in de [Azure container Registry](https://azure.microsoft.com/services/container-registry/), samen met de configuratie-instellingen waarmee klanten de module eenvoudig kunnen gebruiken.

Nadat de aanbieding is gepubliceerd, wordt uw IoT Edge container installatie kopie gekopieerd naar Azure Marketplace in een specifiek openbaar container register. Alle aanvragen van Azure-gebruikers die gebruikmaken van uw module, worden aangeboden vanuit het open bare container register van Azure Marketplace, niet in uw persoonlijke container register.

U kunt richten op meerdere platformen en verschillende versies van de module container installatie kopie bieden met behulp van tags. Zie [de technische activa van uw IOT Edge-module voorbereiden](create-iot-edge-module-asset.md)voor meer informatie over tags en versie beheer.

### <a name="image-repository-details"></a>Details van de afbeeldings opslagplaats

U geeft de volgende informatie op het tabblad **Details van de afbeeldings opslagplaats** .

**Selecteer de installatie kopie bron**: Selecteer de optie **Azure container Registry** .

**Azure-abonnements-id**: Geef de abonnements-id op waarvoor het resource gebruik wordt gerapporteerd en de services worden gefactureerd voor de Azure container Registry die uw container installatie kopie bevatten. U kunt deze ID vinden op de [pagina Abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in het Azure Portal.

**Naam van de Azure-resource groep**: Geef de naam op van de [resource groep](../../azure-resource-manager/management/manage-resource-groups-portal.md) die de Azure container Registry bevat met de container installatie kopie. De resource groep moet toegankelijk zijn in de abonnements-ID (hierboven). U vindt de naam op de pagina [resource groepen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) in de Azure Portal.

**Azure container Registry-naam**: Geef de naam op van de [Azure container Registry](../../container-registry/container-registry-intro.md) met de container installatie kopie. Het container register moet aanwezig zijn in de Azure-resource groep die u eerder hebt opgegeven. Geef alleen de register naam op, niet de volledige naam van de aanmeldings server. Zorg ervoor dat u **azurecr.io** weglaat uit de naam. U kunt de register naam vinden op de [pagina container registers](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in de Azure Portal.

**Gebruikers naam van de beheerder voor de Azure container Registry**: Geef de [gebruikers naam](../../container-registry/container-registry-authentication.md#admin-account)van de beheerder op die is gekoppeld aan de Azure container Registry met de container installatie kopie. De gebruikers naam en het wacht woord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het REGI ster. Als u de gebruikers naam en het wacht woord van de beheerder wilt ophalen, stelt u de eigenschap **beheerder** in op **True** met behulp van de Azure Command-Line interface (CLI). U kunt desgewenst **beheerder** instellen om in te **scha kelen** in de Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Illustreert het dialoog venster update container Registry.":::

#### <a name="call-out-description"></a>Beschrijving van aanroep

1. Gebruiker met beheerdersrechten

<br>**Wacht woord voor de Azure container Registry**: Geef het wacht woord op voor de beheerder van de gebruikers naam die is gekoppeld aan de Azure container Registry en heeft de container installatie kopie. De gebruikers naam en het wacht woord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het REGI ster. U kunt het wacht woord ophalen van de Azure portal door naar **container Registry**  >  **toegangs sleutels** of met Azure CLI te gaan met behulp van de [weer geven opdracht.](/cli/azure/acr/credential#az-acr-credential-show)

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Illustreert het scherm toegangs sleutel in de Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Toegangssleutels
2. Gebruikersnaam
3. Wachtwoord

**De naam van de opslag plaats in de Azure container Registry**. Geef de naam op van de Azure Container Registry opslag plaats met uw installatie kopie. U geeft de naam van de opslag plaats op wanneer u de installatie kopie naar het REGI ster pusht. U kunt de naam van de opslag plaats vinden door naar de [](https://azure.microsoft.com/services/container-registry/)  >  **pagina container Registry opslagplaatsen** te gaan. Zie [container register opslagplaatsen weer geven in de Azure Portal](../../container-registry/container-registry-repositories.md)voor meer informatie. Nadat de naam is ingesteld, kan deze niet meer worden gewijzigd. Gebruik een unieke naam voor elke aanbieding in uw account.

> [!NOTE]
> Er wordt geen ondersteuning geboden voor versleutelde Azure Container Registry voor de module voor Edge-certificaten. Azure Container Registry moet worden gemaakt zonder dat versleuteling is ingeschakeld.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Afbeeldings Tags voor nieuwe versies van uw aanbieding

Klanten moeten automatisch updates kunnen ophalen van de Azure Marketplace wanneer u een update publiceert. Als ze niet willen bijwerken, moeten ze een specifieke versie van de installatie kopie kunnen blijven. U kunt dit doen door telkens wanneer u een update voor de installatie kopie maakt, nieuwe afbeeldings tags toe te voegen.

**Afbeeldings code**. Dit veld moet een **laatste** tag bevatten die verwijst naar de nieuwste versie van uw installatie kopie op alle ondersteunde platforms. Het moet ook een versie label bevatten (bijvoorbeeld vanaf xx. xx. xx, waarbij XX een getal is). Klanten moeten [manifest Tags](https://github.com/estesp/manifest-tool) gebruiken om meerdere platforms te bereiken. Alle labels waarnaar wordt verwezen door een manifest-tag, moeten ook worden toegevoegd, zodat ze kunnen worden geüpload. Alle manifest Tags (met uitzonde ring van de laatste tag) moeten beginnen met X. Y-of X. Y. Z-waarbij X, Y en Z gehele getallen zijn. Als een van de nieuwste Tags bijvoorbeeld verwijst naar 1.0.1-Linux-x64, 1.0.1-Linux-arm32 en 1.0.1-Windows-arm32, moeten deze zes Tags worden toegevoegd aan dit veld. Zie [de technische activa van uw IOT Edge-module voorbereiden](create-iot-edge-module-asset.md) voor meer informatie over tags en versie beheer.

### <a name="default-deployment-settings-optional"></a>Standaard implementatie-instellingen (optioneel)

Definieer de meest voorkomende instellingen voor het implementeren van uw IoT Edge-module. Optimaliseer klant implementaties door uw IoT Edge module out-of-the-box te laten starten met deze standaard instellingen.

**Standaard routes**. De IoT Edge hub beheert de communicatie tussen modules, de IoT Hub en apparaten. U kunt routes instellen voor de invoer van gegevens en de uitvoer tussen modules en de IoT Hub, waarmee u de flexibiliteit krijgt om berichten te verzenden waar ze moeten zijn, zonder dat er extra services nodig zijn om berichten te verwerken of extra code te schrijven. Routes worden gemaakt met behulp van naam/waarde-paren. U kunt Maxi maal vijf standaard route namen definiëren, elk Maxi maal 512 tekens lang.

Zorg ervoor dat u de juiste [route syntaxis](../../iot-edge/module-composition.md#declare-routes)gebruikt) in uw route waarde (meestal gedefinieerd als van/Message/* in $upstream). Dit betekent dat alle berichten die door modules worden verzonden, naar uw IoT Hub gaan. Als u wilt verwijzen naar uw module, gebruikt u de standaard module naam. de naam van uw **aanbieding** is geen spaties of speciale tekens. Als u wilt verwijzen naar andere modules die nog niet bekend zijn, gebruikt u de <FROM_MODULE_NAME> Conventie om uw klanten te laten weten dat ze deze gegevens moeten bijwerken. Zie voor meer informatie over IoT Edge routes [routes declareren](../../iot-edge/module-composition.md#declare-routes).

Als module ContosoModule bijvoorbeeld luistert naar invoer op ContosoInput en uitvoer gegevens op ContosoOutput, is het zinvol om de volgende twee standaard routes te definiëren:

- #1 van naam: ToContosoModule
- Waarde #1: van/messages/modules/<FROM_MODULE_NAME>/outputs/* in BrokeredEndpoint ("/modules/ContosoModule/inputs/ContosoInput")
- #2 van naam: FromContosoModuleToCloud
- Waarde #2: van/messages/modules/ContonsoModule/outputs/ContosoOutput naar $upstream

**Standaard module dubbele gewenste eigenschappen**. Een module dubbele is een JSON-document in de IoT Hub waarin de status informatie voor een module-exemplaar wordt opgeslagen, inclusief gewenste eigenschappen. Gewenste eigenschappen worden samen met gerapporteerde eigenschappen gebruikt voor het synchroniseren van module configuratie of-voor waarden. De back-end van de oplossing kan gewenste eigenschappen instellen en de module kan deze lezen. De module kan ook wijzigings meldingen ontvangen in de gewenste eigenschappen. Gewenste eigenschappen worden gemaakt met Maxi maal vijf paren naam/waarde en elke standaard waarde moet kleiner zijn dan 512 tekens. U kunt Maxi maal vijf naam/waarde dubbele gewenste eigenschappen definiëren. Waarden van dubbele gewenste eigenschappen moeten een geldige JSON, niet-escaped zijn, zonder matrices met een Maxi maal geneste hiërarchie van vier niveaus. In een scenario waarbij een para meter die vereist is voor een standaard waarde niet logisch is (bijvoorbeeld het IP-adres van de server van een klant), kunt u een para meter toevoegen als de standaard waarde. Zie [gewenste eigenschappen definiëren of bijwerken](../../iot-edge/module-composition.md#define-or-update-desired-properties)voor meer informatie over dubbele gewenste eigenschappen.

Als een module bijvoorbeeld een dynamisch Configureer bare vernieuwings frequentie ondersteunt met behulp van dubbele gewenste eigenschappen, is het zinvol om de volgende standaard dubbele gewenste eigenschap te definiëren:

- #1 van naam: RefreshRate
- Waarde #1:60

**Standaard omgevings variabelen**. Omgevings variabelen bieden aanvullende informatie over een module die het configuratie proces helpt. Omgevings variabelen worden gemaakt met behulp van naam/waarde-paren. De naam en waarde van elke standaard omgevings variabele moet kleiner zijn dan 512 tekens en u kunt Maxi maal vijf definiëren. Als een para meter die vereist is voor een standaard waarde niet logisch is (bijvoorbeeld het IP-adres van de server van een klant), kunt u een para meter toevoegen als de standaard waarde.

Als er bijvoorbeeld een module vereist is om de gebruiksrecht overeenkomst te accepteren voordat deze wordt gestart, kunt u de volgende omgevings variabele definiëren:

- Name #1: ACCEPT_EULA
- Waarde #1: Y

**Standaard opties voor container maken**. Opties voor het maken van containers Maak het maken van de docker-container van de IoT Edge module direct. IoT Edge ondersteunt docker engine API-container opties maken. Bekijk alle opties in [lijst containers.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Het veld voor het maken van opties moet een geldige JSON, een niet-escape teken en minder dan 512 tekens zijn.

Als een module bijvoorbeeld poort binding vereist, definieert u de volgende Create-opties:

"HostConfig": {"PortBindings": {"5012/TCP": [{"HostPort": "5012"}]}

## <a name="review-and-publish"></a>Beoordelen en publiceren

Nadat u alle vereiste delen van de aanbieding hebt voltooid, kunt u deze verzenden om te controleren en te publiceren.

Selecteer in de rechter bovenhoek van de portal **controleren en publiceren**.

Op de pagina controleren kunt u de publicatie status zien:

- Bekijk de voltooiings status voor elke sectie van de aanbieding. U kunt pas publiceren als alle secties van de aanbieding zijn gemarkeerd als voltooid.
    - **Niet gestart** : de sectie is niet gestart en moet worden voltooid.
    - **Onvolledig** : de sectie bevat fouten die moeten worden hersteld of waarvoor u meer informatie moet opgeven. Zie de secties eerder in dit document voor meer informatie.
    - **Voltooid** : de sectie bevat alle vereiste gegevens en er zijn geen fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding kunt indienen.
- Geef test instructies op het certificerings team om te controleren of uw aanbieding correct is getest. Geef ook aanvullende notities op die handig zijn voor het leren van uw aanbieding.

Selecteer **publiceren** om de aanbieding voor publicatie in te dienen.

We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Als u uw aanbieding naar het publiek wilt publiceren, gaat u naar partner centrum en selecteert u **Go-Live**.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in de commerciële Marketplace](update-existing-offer.md)
