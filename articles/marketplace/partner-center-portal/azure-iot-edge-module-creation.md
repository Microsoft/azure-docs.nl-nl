---
title: Een aanbieding Azure IoT Edge module maken met Partner Center in Azure Marketplace
description: Meer informatie over het maken, configureren en publiceren van een aanbieding voor IoT Edge module in Azure Marketplace met Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 12600cadaa84ae116818eec06459d5db0c05304a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773416"
---
# <a name="create-an-iot-edge-module-offer"></a>Aanbieding voor IoT Edge-module maken

In dit artikel wordt beschreven hoe u een aanbieding voor Internet of Things Edge-module (IoT) maakt en publiceert voor Azure Marketplace. Voordat u begint, [maakt u een commerciële Marketplace-account in Partner Center](../create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is ingeschreven in het commerciële marketplace-programma.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan [bij Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het navigatiemenu aan de linkerkant **Commercial Marketplace**  >  **Overview**.
3. Selecteer op de pagina Overzicht **+ Nieuwe aanbieding IoT Edge**  >  **module**.

    ![Illustreert het linkernavigatiemenu.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Nadat een aanbieding is gepubliceerd, worden de wijzigingen in de aanbieding Partner Center alleen in online winkels weergegeven nadat de aanbieding opnieuw is gepubliceerd. Zorg ervoor dat u altijd opnieuw kunt publiceren nadat u wijzigingen hebt aangebracht.

### <a name="offer-id-and-alias"></a>Aanbiedings-id en -alias

Voer een **aanbiedings-id in.** Dit is een unieke id voor elke aanbieding in uw account.

- Deze id is zichtbaar voor klanten in het webadres voor de Marketplace-aanbieding en Azure Resource Manager sjablonen, indien van toepassing.
- Gebruik alleen kleine letters en cijfers. Het kan afbreekstreepingen en onderstrepingstekens bevatten, maar geen spaties en mag maximaal 50 tekens bevatten. Als u bijvoorbeeld **test-offer-1** op geeft, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- De aanbiedings-id kan niet worden gewijzigd nadat u **Maken hebt geselecteerd.**

Voer een **aanbiedingsalias in.** Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

- Deze naam wordt niet gebruikt in de marketplace en verschilt van de naam van de aanbieding en andere waarden die aan klanten worden weergegeven.
- Dit kan niet worden gewijzigd nadat u Maken **hebt geselecteerd.**

Selecteer **Maken om** de aanbieding te genereren en door te gaan.

## <a name="offer-overview"></a>Overzicht van aanbieding

Op **de pagina Overzicht** van aanbieding ziet u een visuele weergave van de stappen die nodig zijn om deze aanbieding te publiceren (zowel voltooid als binnenkort) en hoe lang elke stap moet duren.

Deze pagina bevat koppelingen voor het uitvoeren van bewerkingen op deze aanbieding op basis van de selectie die u maakt. Bijvoorbeeld:

- Als de aanbieding een concept is: conceptaanbieding verwijderen
- Als de aanbieding live is: verkoop [van de aanbieding stoppen](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Als de aanbieding in preview is - [Go-live](../review-publish-offer.md#previewing-and-approving-your-offer)
- Als u het afloggen van de uitgever nog niet hebt voltooid: [publicatie annuleren.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Aanbieding instellen

Volg deze stappen om uw aanbieding in te stellen.

### <a name="customer-leads"></a>Leads van klanten

Wanneer u uw aanbieding met een Partner Center naar marketplace publiceert, kunt u deze desgewenst verbinden met uw CRM-systeem (Customer Relationship Management). Hiermee kunt u contactgegevens van klanten ontvangen zodra iemand interesse heeft in uw product of uw product gebruikt.

1. Selecteer een leadbestemming waarnaar wij de klantenleads moeten sturen. Partner Center ondersteunt de volgende CRM-systemen:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Als uw CRM-systeem hierboven niet wordt vermeld, gebruikt u [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) of [Https Endpoint](commercial-marketplace-lead-management-instructions-https.md) om klant leadgegevens op te slaan en exporteert u de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de leadbestemming bij het publiceren in Partner Center.
3. Controleer of de verbinding met de leadbestemming juist is geconfigureerd. Nadat u deze hebt gepubliceerd in Partner Center, valideren we de verbinding en sturen we u een test lead. Terwijl u een voorbeeld van de aanbieding bekijkt voordat deze live gaat, kunt u uw leadverbinding ook testen door de aanbieding zelf te kopen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de leadbestemming bijgewerkt blijft, zodat u geen leads verliest.

Hier volgen enkele aanvullende resources voor leadbeheer:

- [Leads van uw aanbieding in de commerciële marketplace](commercial-marketplace-get-customer-leads.md)
- [Veelvoorkomende vragen over leadbeheer](../lead-management-faq.md#common-questions-about-lead-management)
- [Problemen met leadconfiguratie oplossen](../lead-management-faq.md#publishing-config-errors)
- [Overzicht van leadbeheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Zorg ervoor dat de pop-upblokkering is uitgeschakeld).

Selecteer **Concept opslaan voordat** u doorgaat.

### <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën definiëren die worden gebruikt voor het groepen van uw aanbieding op de marketplace en de juridische contracten die uw aanbieding ondersteunen.

#### <a name="category"></a>Categorie

Selecteer categorieën en subcategorieën om uw aanbieding in de juiste marketplace-zoekgebieden te plaatsen. Beschrijf in de beschrijving van de aanbieding hoe uw aanbieding ondersteuning biedt voor deze categorieën. Selecteer:

- Ten minste één en maximaal twee categorieën, waaronder een primaire en een secundaire categorie (optioneel).
- Maximaal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert **u Niet van toepassing.**

Zie de volledige lijst met categorieën en subcategorieën in [Best practices voor aanbiedingsvermeldingen.](../gtm-offer-listing-best-practices.md) In de marketplace worden IoT Edge modules altijd weergegeven onder de  **categorie Internet of Things**  >  **IoT Edge module.**  

#### <a name="legal"></a>Juridisch

U moet voorwaarden voor de aanbieding verstrekken. U hebt hiervoor twee opties:

- Gebruik de Standaardcontract voor de commerciële Marketplace van Microsoft.
- Geef uw eigen voorwaarden op.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standaardcontract voor de commerciële Marketplace van Microsoft

We bieden een Standaardcontract om transacties in de commerciële marketplace mogelijk te maken. U kunt ervoor kiezen om uw oplossing aan te bieden onder Standaardcontract, die klanten slechts één keer hoeven te controleren en accepteren. Dit is een goede optie als u geen aangepaste voorwaarden wilt maken.

Zie Voor meer informatie over de Standaardcontract, [Standaardcontract voor de commerciële Marketplace van Microsoft.](../standard-contract.md) U kunt ook de [Standaardcontract](https://go.microsoft.com/fwlink/?linkid=2041178) PDF downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

Als u de Standaardcontract wilt gebruiken, selecteert u het selectievakje De Standaardcontract gebruiken voor de commerciële marketplace van **Microsoft** en klikt u vervolgens op **Accepteren.**

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met behulp van het Standard-contract voor de commerciële marketplace van Microsoft, kunt u uw eigen aangepaste voorwaarden niet gebruiken. Bied uw oplossing aan onder de Standaardcontract of onder uw eigen voorwaarden.

![Illustreert het gebruik van Standaardcontract selectievakje voor de commerciële marketplace van Microsoft.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Uw eigen voorwaarden

Als u uw eigen aangepaste voorwaarden wilt verstrekken, voert u deze in het vak **Voorwaarden** in. U kunt een onbeperkt aantal tekens tekst invoeren in dit vak. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **Concept opslaan** voordat u verdergaat met de volgende sectie, Aanbiedingsvermelding.

## <a name="offer-listing"></a>Aanbiedingsvermelding

Hier definieert u de aanbiedingsgegevens die worden weergegeven in marketplace. Dit omvat de naam van de aanbieding, beschrijving, afbeeldingen, en meer. Zorg ervoor dat u het beleid volgt dat wordt beschreven op de beleidspagina van Microsoft tijdens het configureren van deze aanbieding.

> [!NOTE]
> Aanbiedingsdetails zijn niet vereist in het Engels als de beschrijving van de aanbieding begint met de zin 'Deze toepassing is alleen beschikbaar in [niet-Engelse taal].' U kunt ook een nuttige koppeling geven om inhoud aan te bieden in een andere taal dan de taal die wordt gebruikt in de aanbiedingsvermelding.

### <a name="name"></a>Name

De naam die u hier optreedt, wordt weergegeven als de titel van uw aanbieding. Dit veld wordt vooraf ingevuld met de tekst die u hebt ingevoerd in het **vak Aanbiedingsalias** toen u de aanbieding maakte. U kunt deze naam later wijzigen.

De naam:

- Kan handelsmerken zijn (en u kunt handelsmerken of auteursrechtsymbolen opnemen).
- Mag niet langer zijn dan 50 tekens.
- Kan geen emoji's bevatten.

### <a name="search-results-summary"></a>Samenvatting van zoekresultaten

Geef een korte beschrijving van uw aanbieding op. Dit kan maximaal 100 tekens lang zijn en wordt gebruikt in marketplace-zoekresultaten.

### <a name="long-summary"></a>Lange samenvatting

Geef een gedetailleerde beschrijving van uw aanbieding op. Deze mag maximaal 256 tekens lang zijn en wordt gebruikt in marketplace-zoekresultaten.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

IoT Edge moduleaanbiedingen moeten onder aan de beschrijving een alinea met minimale hardwarevereisten bevatten, zoals:

- Minimale hardwarevereisten: Linux x64 en arm32 OS, 1 GB RAM, 500 MB aan opslag

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>URL voor privacybeleid

Voer het webadres van het privacybeleid van uw organisatie in. U bent verantwoordelijk om ervoor te zorgen dat uw aanbieding voldoet aan de privacywetgeving en -regelgeving. U bent ook verantwoordelijk voor het plaatsen van een geldig privacybeleid op uw website.

#### <a name="useful-links"></a>Handige koppelingen

Geef aanvullende onlinedocumenten over uw aanbieding op. U kunt maximaal 25 koppelingen toevoegen. Als u een koppeling wilt toevoegen, **selecteert u + Een koppeling toevoegen** en vult u de volgende velden in:

- **Titel:** klanten zien de titel op de detailpagina van uw aanbieding.
- **Koppeling (URL) :** voer een koppeling in voor klanten om uw onlinedocument weer te geven. De koppeling moet beginnen met `http://` of `https://` .

Zorg ervoor dat u ten minste één koppeling naar uw documentatie en één koppeling naar de compatibele IoT Edge-apparaten uit de [Azure IoT-apparaatcatalogus toevoegt.](https://devicecatalog.azure.com/)

### <a name="contact-information"></a>Contactgegevens

U moet de naam, het e-mailadres en het telefoonnummer voor een contactpersoon **voor** ondersteuning en een technische **contactpersoon verstrekken.** Deze informatie wordt niet weergegeven aan klanten. Het is beschikbaar voor Microsoft en kan worden verstrekt aan Cloud Solution Provider (CSP)-partners.

- Contactpersoon voor ondersteuning (vereist): voor algemene ondersteuningsvragen.
- Technische contactpersoon (vereist): voor technische vragen en certificeringsproblemen.
- Contactpersoon voor CSP-programma (optioneel): voor vragen van resellers met betrekking tot het CSP-programma.

Geef **in** de sectie Contactpersoon voor  ondersteuning het webadres van de ondersteuningswebsite op waar partners ondersteuning voor uw aanbieding kunnen vinden op basis van of de aanbieding beschikbaar is in Azure, Azure Government wereldwijd of beide.

Geef in de sectie Contactpersoon voor **CSP-programma** de koppeling **(CSP Program Marketing Materials)** op waar CSP-partners marketingmateriaal voor uw aanbieding kunnen vinden.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende marketplace-vermeldingsbronnen

Zie Best practices voor aanbiedingsvermeldingen voor meer informatie over [het maken van aanbiedingen.](../gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

Geef logo's en afbeeldingen op die u met uw aanbieding kunt gebruiken. Alle afbeeldingen moeten de PNG-indeling hebben. Wazige afbeeldingen worden geweigerd.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Als u problemen hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de service die wordt gebruikt door de https://upload.xboxlive.com Partner Center.

#### <a name="store-logos"></a>Logo's opslaan

Geef een PNG-bestand op voor het logo **Van** grote grootte. Partner Center gebruikt deze om een **klein** en een medium logo **te** maken. U kunt deze eventueel later vervangen door andere afbeeldingen.

- **Groot** (van 216 x 216 tot 350 x 350 px, vereist)
- **Gemiddeld** (90 x 90 px, optioneel)
- **Klein** (48 x 48 px, optioneel)

Deze logo's worden op verschillende plaatsen in de lijst gebruikt:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Schermopnamen (optioneel)

Voeg maximaal vijf schermafbeeldingen toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot zijn en een PNG-indeling hebben.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg maximaal vijf video's toe die uw aanbieding demonstreren. Voer de naam van de video, het webadres en een PNG-miniatuurafbeelding van de video in met een grootte van 1280 x 720 pixels.

#### <a name="marketplace--examples"></a>Marketplace-voorbeelden

Hier ziet u een voorbeeld van hoe aanbiedingsgegevens worden weergegeven in Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Illustreert hoe deze aanbieding wordt weergegeven in Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Groot logo
2. Categorieën
3. Ondersteuningsadres (koppeling)
4. Voorwaarden
5. Adres van het privacybeleid (koppeling)
6. Name
7. Samenvatting
8. Description
9. Handige koppelingen
10. Schermopnamen/video's

<br>Hier is een voorbeeld van hoe aanbiedingsgegevens worden weergegeven in Azure Marketplace zoekresultaten:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Illustreert hoe deze aanbieding wordt weergegeven in Azure Marketplace zoekresultaten.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Klein logo
2. Naam van aanbieding
3. Samenvatting van zoekresultaten

<br>Hier ziet u een voorbeeld van hoe aanbiedingsgegevens worden weergegeven in de Azure Portal:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Illustreert hoe deze aanbieding wordt weergegeven in de Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Naam
2. Beschrijving
3. Handige koppelingen
4. Schermopnamen

<br>Hier is een voorbeeld van hoe aanbiedingsgegevens worden weergegeven in de Azure Portal zoekresultaten:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Illustreert hoe deze aanbieding wordt weergegeven in de Azure Portal zoekresultaten.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Klein logo
2. Naam van aanbieding
3. Samenvatting van zoekresultaten

<br>Selecteer **Concept opslaan voordat** u doorgaat met de volgende sectie, Preview.

## <a name="preview"></a>Preview

Op het **tabblad Preview kunt** u een beperkte **preview-doelgroep** kiezen voor het valideren van uw aanbieding voordat u deze live publiceert naar de bredere marketplace-doelgroep.

> [!IMPORTANT]
> Nadat u uw aanbieding in preview hebt bekeken, moet u **Live gaan selecteren** om uw aanbieding naar het publiek te publiceren.

Geef uw preview-doelgroep op met behulp van GUID's voor Azure-abonnements-id's, samen met een optionele beschrijving voor elke doelgroep. Geen van deze velden kan door klanten worden gezien.

> [!NOTE]
> U vindt uw Azure-abonnements-id op de pagina Abonnementen in de Azure Portal.

Voeg ten minste één Azure-abonnements-id toe, afzonderlijk (maximaal 10) of door een CSV-bestand (maximaal 100) te uploaden. Door deze abonnements-ID's toe te voegen, definieert u wie een preview van uw aanbieding kan bekijken voordat deze live wordt gepubliceerd. Als uw aanbieding al live is, kunt u een preview-doelgroep definiëren om wijzigingen of updates voor uw aanbieding te testen.

Selecteer **Concept opslaan** voordat u doorgaat met de volgende sectie, Planoverzicht.

## <a name="plan-overview"></a>Planoverzicht

Op dit tabblad kunt u verschillende abonnementsopties binnen dezelfde aanbieding in Partner Center. Plannen (voorheen SKU's genoemd) kunnen verschillen wat betreft de beschikbare clouds, zoals wereldwijde clouds, overheidswolken en de afbeelding waarnaar in het plan wordt verwezen. Als u uw aanbieding wilt aanbieden in de Marketplace, moet u ten minste één abonnement instellen.

U kunt maximaal 100 abonnementen maken voor elke aanbieding: maximaal 45 van deze abonnementen kunnen privé zijn. Meer informatie over privé-abonnementen in [Privéaanbiedingen in de commerciële marketplace van Microsoft.](../private-offers.md)

Nadat u uw plannen hebt gemaakt, wordt **op het tabblad Planoverzicht** het volgende weergegeven:

- Namen van plannen
- Prijsmodel
- Azure-regio's (wereldwijd of overheid)
- Huidige publicatiestatus
- Beschikbare acties

De acties die beschikbaar zijn in het planoverzicht variëren afhankelijk van de huidige status van uw plan. Deze omvatten:

- **Concept verwijderen:** als de status van het plan concept is.
- **Verkoopplan stoppen:** als de status van het plan live wordt gepubliceerd.

### <a name="create-new-plan"></a>Nieuw plan maken

Selecteer **Nieuw plan maken.** Het dialoogvenster Nieuw **plan** wordt weergegeven.

Maak in **het vak** Plan ID een unieke plan-id voor elk plan in deze aanbieding. Deze id is zichtbaar voor klanten in het webadres van het product. Gebruik alleen kleine letters en cijfers, streepjes of onderstrepingstekens en maximaal 50 tekens.

Voer in **het vak** Plannaam een naam in voor dit plan. Klanten zien deze naam bij het kiezen van een abonnement in uw aanbieding. Maak een unieke naam voor elk plan in deze aanbieding. U kunt bijvoorbeeld een aanbiedingsnaam van **Windows Server gebruiken met** abonnementen op Windows Server **2016** en **Windows Server 2019.**

> [!NOTE]
> De plan-id kan niet worden gewijzigd nadat u **Maken hebt geselecteerd.**

Selecteer **Maken**.

### <a name="plan-setup"></a>Installatie van plan

Op dit tabblad kunt u configureren in welke clouds het abonnement beschikbaar is. Uw antwoorden op dit tabblad beïnvloeden welke velden worden weergegeven op andere tabbladen.

#### <a name="azure-regions"></a>Azure-regio's

Alle abonnementen voor IoT Edge moduleaanbiedingen worden automatisch beschikbaar gesteld in **Azure Global.**  Uw plan kan worden gebruikt door klanten in alle Azure-regio's wereldwijd die gebruikmaken van de marketplace. Zie Geografische beschikbaarheid en valutaondersteuning [voor meer informatie.](../marketplace-geo-availability-currencies.md)

Selecteer de [Azure Government](../../azure-government/documentation-government-welcome.md) om uw oplossing hier weer te laten zien. Dit is een overheidscloud met beheerde toegang voor klanten van Amerikaanse federale, staats- en lokale of overheidsinstanties, en partners die in aanmerking komen om ze te bedienen. Als uitgever bent u verantwoordelijk voor alle nalevingscontroles, beveiligingsmaatregelen en best practices voor deze cloud-community. Azure Government fysiek geïsoleerde datacenters en netwerken (alleen in de Verenigde Staten). Voordat [u](../../azure-government/documentation-government-manage-marketplace-partners.md) publiceert Azure Government, test en bevestigt u uw oplossing binnen dat gebied, aangezien de resultaten mogelijk anders zijn. Als u uw oplossing wilt faseer en testen, vraagt u een proefaccount aan [bij Microsoft Azure Government proefversie.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke regio, kunt u die regio niet verwijderen.

#### <a name="azure-government-certifications"></a>Azure Government certificeringen

Deze optie is alleen zichtbaar **als** Azure Government is geselecteerd onder **Azure-regio's.**

Azure Government-services verwerken gegevens die onderhevig zijn aan bepaalde wettelijke voorschriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 en CJIS. Om uw certificeringen voor deze programma's onder de aandacht te brengen, kunt u maximaal 100 koppelingen opgeven die uw certificeringen beschrijven. Dit kunnen koppelingen naar uw vermeldingen op het programma rechtstreeks of naar uw eigen website zijn. Deze koppelingen zijn alleen zichtbaar Azure Government klanten.

### <a name="plan-listing"></a>Abonnementsvermelding

Op dit tabblad wordt specifieke informatie weergegeven voor elk ander abonnement binnen dezelfde aanbieding.

### <a name="plan-name"></a>Naam van plan

Deze wordt vooraf ingevuld met de naam die u aan uw plan hebt gegeven toen u het maakte. U kunt deze naam zo nodig wijzigen. Deze mag maximaal 50 tekens lang zijn. Deze naam wordt weergegeven als de titel van dit plan in Azure Marketplace en de Azure Portal. Het wordt gebruikt als de standaardnaam van de module nadat het plan gereed is om te worden gebruikt.

### <a name="plan-summary"></a>Abonnementsoverzicht

Geef een kort overzicht van uw abonnement (niet de aanbieding). Deze samenvatting wordt weergegeven in Azure Marketplace en kan maximaal 100 tekens bevatten.

### <a name="plan-description"></a>Beschrijving van het plan

Beschrijf wat dit plan uniek maakt, evenals de verschillen tussen plannen binnen uw aanbieding. Beschrijf de aanbieding niet, alleen het plan. Deze beschrijving wordt weergegeven in Azure Marketplace en in de Azure Portal op de aanbiedingspagina. Dit kan dezelfde inhoud zijn die u hebt opgegeven in het planoverzicht en maximaal 2000 tekens bevatten.

Selecteer **Concept opslaan** na het voltooien van deze velden.

#### <a name="plan-examples"></a>Planvoorbeelden

Hier is een voorbeeld van Azure Marketplace van een abonnement (alle vermelde prijzen zijn alleen bedoeld voor voorbeelddoeleinden en zijn niet bedoeld om de werkelijke kosten weer te geven):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Illustreert Azure Marketplace plandetails.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Naam van aanbieding
2. Plannaam
3. Beschrijving van het plan

<br>Hier is een voorbeeld van de details Azure Portal het abonnement (alle vermelde prijzen zijn alleen bedoeld voor voorbeelddoeleinden en zijn niet bedoeld om de werkelijke kosten weer te geven):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Illustreert de details Azure Portal uw abonnement.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Naam van aanbieding
2. Plannaam
3. Beschrijving van het plan

### <a name="availability"></a>Beschikbaarheid

Als u uw gepubliceerde aanbieding wilt verbergen zodat klanten deze niet kunnen zoeken, zoeken of kopen in marketplace, selecteert u het selectievakje Plan verbergen op het tabblad Beschikbaarheid. 

Dit veld wordt vaak gebruikt wanneer:

- De aanbieding is bedoeld om alleen indirect te worden gebruikt wanneer er naar wordt verwezen via een andere toepassing.
- De aanbieding mag niet afzonderlijk worden gekocht.
- Het plan is gebruikt voor de eerste test en is niet meer relevant.
- Het plan is gebruikt voor tijdelijke of seizoensgebonden aanbiedingen en mag niet meer worden aangeboden.

## <a name="technical-configuration"></a>Technische configuratie

Het **IoT Edge moduleaanbiedingstype** is een specifiek type container dat wordt uitgevoerd op een IoT Edge apparaat. Op het **tabblad Technische** configuratie geeft u referentie-informatie op voor de opslagplaats voor containerafbeeldingen in de [Azure Container Registry,](https://azure.microsoft.com/services/container-registry/)samen met configuratie-instellingen die klanten de module eenvoudig laten gebruiken.

Nadat de aanbieding is gepubliceerd, wordt IoT Edge containerkopie gekopieerd naar Azure Marketplace in een specifiek openbaar containerregister. Alle aanvragen van Azure-gebruikers om uw module te gebruiken, worden uitgevoerd vanuit Azure Marketplace openbare containerregister, niet uw persoonlijke containerregister.

U kunt zich richten op meerdere platforms en verschillende versies van de containerafbeelding van de module leveren met behulp van tags. Zie Prepare your IoT Edge module technical assets (Technische assets voor uw IoT Edge module voorbereiden) voor meer informatie over [tags en versiebeheer.](create-iot-edge-module-asset.md)

### <a name="image-repository-details"></a>Details van opslagplaats voor afbeeldingen

U geeft de volgende informatie op het tabblad Details van de opslagplaats **voor afbeeldingen** op.

**Selecteer de bron van de afbeelding:** selecteer **de Azure Container Registry** selecteren.

**Azure-abonnements-id:** geef de abonnements-id op waar resourcegebruik wordt gerapporteerd en services worden gefactureerd voor de Azure Container Registry die uw containerafbeelding bevat. U vindt deze id op de [pagina Abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure Portal.

**Naam van Azure-resourcegroep:** geef de [naam van de resourcegroep](../../azure-resource-manager/management/manage-resource-groups-portal.md) op die de Azure Container Registry de containerafbeelding bevat. De resourcegroep moet toegankelijk zijn in de abonnements-id (hierboven). U vindt de naam op de [pagina Resourcegroepen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) in de Azure Portal.

**Naam van Azure-containerregister:** geef de naam op van de [Azure Container Registry](../../container-registry/container-registry-intro.md) die uw containerafbeelding heeft. Het containerregister moet aanwezig zijn in de Azure-resourcegroep die u eerder hebt opgegeven. Geef alleen de registernaam op, niet de volledige aanmeldingsservernaam. Zorg ervoor dat u **azurecr.io** naam weglaten. U vindt de registernaam op de [pagina Containerregisters](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in Azure Portal.

**Gebruikersnaam van beheerder voor de Azure Container Registry:** geef de [gebruikersnaam](../../container-registry/container-registry-authentication.md#admin-account)van de beheerder op die is gekoppeld aan de Azure Container Registry die uw containerafbeelding heeft. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. Als u de gebruikersnaam en  het wachtwoord van de beheerder wilt op halen, stelt u de eigenschap met beheerdersrechten in op Waar met **behulp** van de Azure Command-Line Interface (CLI). U kunt eventueel Gebruiker **met beheerdersrechten instellen** **op Inschakelen** in Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Illustreert het dialoogvenster Containerregister bijwerken.":::

#### <a name="call-out-description"></a>Beschrijving van de oproep

1. Gebruiker met beheerdersrechten

<br>**Wachtwoord voor de Azure Container Registry:** geef het wachtwoord op voor de gebruikersnaam van de beheerder die is gekoppeld aan de Azure Container Registry en die uw containerafbeelding heeft. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. U kunt het wachtwoord ophalen uit de Azure Portal door naar Container Registry  >  **toegangssleutels** of met Azure CLI te gaan met behulp van de [opdracht show.](/cli/azure/acr/credential#az_acr_credential_show)

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Illustreert het scherm met de toegangssleutel in Azure Portal.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Toegangssleutels
2. Gebruikersnaam
3. Wachtwoord

**De naam van de opslagplaats in Azure Container Registry**. Geef de naam op van de Azure Container Registry opslagplaats met uw afbeelding. U geeft de naam van de opslagplaats op wanneer u de afbeelding naar het register pusht. U kunt de naam van de [](https://azure.microsoft.com/services/container-registry/)opslagplaats vinden door naar de pagina Container Registry  >  **opslagplaatsen te gaan.** Zie Opslagplaatsen voor [containerregisters](../../container-registry/container-registry-repositories.md)weergeven in de Azure Portal. Nadat de naam is ingesteld, kan deze niet meer worden gewijzigd. Gebruik een unieke naam voor elke aanbieding in uw account.

> [!NOTE]
> Encrypted Azure Container Registry voor edge-modulecertificering wordt niet ondersteund. Azure Container Registry moet worden gemaakt zonder versleuteling ingeschakeld.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Afbeeldingstags voor nieuwe versies van uw aanbieding

Klanten moeten automatisch updates kunnen ontvangen van de Azure Marketplace wanneer u een update publiceert. Als ze niet willen bijwerken, moeten ze een specifieke versie van uw afbeelding kunnen gebruiken. U kunt dit doen door nieuwe afbeeldingstags toe te voegen telkens wanneer u een update voor de afbeelding maakt.

**Afbeeldingstag**. Dit veld moet een nieuwste tag **bevatten** die naar de nieuwste versie van uw afbeelding op alle ondersteunde platforms wijst. Het moet ook een versietag bevatten (bijvoorbeeld beginnend met xx.xx.xx, waarbij xx een getal is). Klanten moeten [manifesttags gebruiken om](https://github.com/estesp/manifest-tool) zich op meerdere platforms te richten. Alle tags waarnaar wordt verwezen door een manifesttag moeten ook worden toegevoegd, zodat we ze kunnen uploaden. Alle manifesttags (met uitzondering van de meest recente tag) moeten beginnen met X.Y- of X.Y.Z, waarbij X, Y en Z gehele getallen zijn. Als een meest recente tag bijvoorbeeld wijst naar 1.0.1-linux-x64, 1.0.1-linux-arm32 en 1.0.1-windows-arm32, moeten deze zes tags aan dit veld worden toegevoegd. Zie Prepare your IoT Edge module technical assets (Technische assets voor uw IoT Edge module voorbereiden) voor meer informatie over [tags en versiebeheer.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Standaardimplementatie-instellingen (optioneel)

Definieer de meest voorkomende instellingen voor het implementeren van IoT Edge module. Optimaliseer klantimplementaties door ze uw IoT Edge-module standaard te laten starten met deze standaardinstellingen.

**Standaardroutes.** De IoT Edge Hub beheert de communicatie tussen modules, de IoT Hub en apparaten. U kunt routes instellen voor gegevensinvoer en -uitvoer tussen modules en de IoT Hub. Dit biedt u de flexibiliteit om berichten te verzenden waar ze naartoe moeten zonder dat er aanvullende services nodig zijn om berichten te verwerken of aanvullende code te schrijven. Routes worden gemaakt met behulp van naam/waarde-paren. U kunt maximaal vijf standaardroutenamen definiëren, elk maximaal 512 tekens lang.

Zorg ervoor dat u de juiste [routesyntaxis](../../iot-edge/module-composition.md#declare-routes)gebruikt in de routewaarde (meestal gedefinieerd als FROM/message/* INTO $upstream). Dit betekent dat berichten die door modules worden verzonden, naar uw IoT Hub. Als u naar uw module wilt verwijzen, gebruikt u de standaardnaam van de module. Dit is de naam van uw **aanbieding,** zonder spaties of speciale tekens. Als u wilt verwijzen naar andere modules die nog niet bekend zijn, gebruikt u de <FROM_MODULE_NAME> conventie om uw klanten te laten weten dat ze deze informatie moeten bijwerken. Zie Routes declare IoT Edge meer informatie [over de routes.](../../iot-edge/module-composition.md#declare-routes)

Als module ContosoModule bijvoorbeeld luistert naar invoer op ContosoInput- en uitvoergegevens in ContosoOutput, is het zinvol om de volgende twee standaardroutes te definiëren:

- Naam #1: ToContosoModule
- Waarde #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Naam #2: FromContosoModuleToCloud
- Waarde #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Standaard gewenste eigenschappen van module twin**. Een module dubbel is een JSON-document in de IoT Hub waarin de statusinformatie voor een module-exemplaar wordt op slaat, inclusief de gewenste eigenschappen. Gewenste eigenschappen worden samen met gerapporteerde eigenschappen gebruikt om moduleconfiguratie of -voorwaarden te synchroniseren. De back-end van de oplossing kan gewenste eigenschappen instellen en de module kan deze lezen. De module kan ook wijzigingsmeldingen ontvangen in de gewenste eigenschappen. Gewenste eigenschappen worden gemaakt met maximaal vijf naam-waardeparen en elke standaardwaarde moet uit minder dan 512 tekens bestaan. U kunt maximaal vijf gewenste eigenschappen voor de naam/waarde-dubbel definiëren. Waarden van de gewenste dubbeleigenschappen moeten geldige JSON zijn, zonder escape,zonder matrices met een maximale geneste hiërarchie van vier niveaus. In een scenario waarin een parameter die is vereist voor een standaardwaarde niet zinvol is (bijvoorbeeld het IP-adres van de server van een klant), kunt u een parameter toevoegen als de standaardwaarde. Zie Gewenste eigenschappen definiëren of bijwerken voor meer informatie over gewenste [dubbeleigenschappen.](../../iot-edge/module-composition.md#define-or-update-desired-properties)

Als een module bijvoorbeeld een dynamisch configureerbare vernieuwingssnelheid ondersteunt met behulp van de gewenste dubbeleigenschappen, is het zinvol om de volgende standaard gewenste eigenschap van de dubbel te definiëren:

- Naam #1: RefreshRate
- Waarde #1: 60

**Standaardomgevingsvariabelen**. Omgevingsvariabelen bieden aanvullende informatie aan een module die het configuratieproces helpt. Omgevingsvariabelen worden gemaakt met behulp van naam/waarde-paren. Elke standaardnaam en waarde van de omgevingsvariabele moet uit minder dan 512 tekens bestaan en u kunt maximaal vijf tekens definiëren. Wanneer een vereiste parameter voor een standaardwaarde niet zinvol is (bijvoorbeeld het IP-adres van de server van een klant), kunt u een parameter toevoegen als de standaardwaarde.

Als een module bijvoorbeeld gebruiksvoorwaarden moet accepteren voordat deze wordt gestart, kunt u de volgende omgevingsvariabele definiëren:

- Naam #1: ACCEPT_EULA
- Waarde #1: Y

**Standaardopties voor het maken van containers.** Opties voor het maken van containers leiden naar het maken van IoT Edge Docker-container voor de nieuwe module. IoT Edge ondersteunt opties voor het maken van containers voor de Docker-engine-API. Bekijk alle opties op [Containers lijst.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) Het veld Opties maken moet een geldige JSON, een niet-escape-teken en minder dan 512 tekens zijn.

Als voor een module bijvoorbeeld poortbinding is vereist, definieert u de volgende maakopties:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Beoordelen en publiceren

Nadat u alle vereiste secties van de aanbieding hebt voltooid, kunt u deze indienen om te controleren en te publiceren.

Selecteer controleren en publiceren in de rechterbovenhoek van de **portal.**

Op de controlepagina ziet u de publicatiestatus:

- Bekijk de voltooiingsstatus voor elke sectie van de aanbieding. U kunt pas publiceren als alle secties van de aanbieding zijn gemarkeerd als voltooid.
    - **Niet gestart:** de sectie is niet gestart en moet worden voltooid.
    - **Onvolledig:** de sectie bevat fouten die moeten worden opgelost of die vereisen dat u meer informatie verstrekt. Zie de secties eerder in dit document voor hulp.
    - **Voltooid:** de sectie bevat alle vereiste gegevens en er zijn geen fouten. Alle secties van de aanbieding moeten zijn voltooid voordat u de aanbieding kunt indienen.
- Geef het certificeringsteam testinstructies om ervoor te zorgen dat uw aanbieding correct is getest. Geef ook eventuele aanvullende opmerkingen op die nuttig zijn om inzicht te krijgen in uw aanbieding.

Selecteer Publiceren om de aanbieding voor publicatie in **te dienen.**

We sturen u een e-mailbericht om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren. Als u uw aanbieding wilt publiceren, gaat u naar Partner Center selecteert u **Go-live.**

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in de commerciële marketplace](update-existing-offer.md)
