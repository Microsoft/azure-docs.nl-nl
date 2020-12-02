---
title: Een Azure-container aanbod maken-Azure Marketplace
description: Meer informatie over het maken en publiceren van een container aanbod naar Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: bcb334074d842fa5c35e4619f1c5d654405388a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459572"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Een Azure Container-aanbieding maken in Azure Marketplace

In dit artikel wordt beschreven hoe u een container aanbod voor Azure Marketplace maakt en publiceert. Voordat u begint, moet u [een commercieel Marketplace-account maken in Partner Center](./partner-center-portal/create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is inge schreven in het Commercial Marketplace-programma.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).

2. Selecteer in het menu links de optie **commerciële Marketplace**-  >  **overzicht**.

3. Selecteer op de pagina overzicht **+ nieuwe**  >  **Azure-container** aanbod.

   ![Illustreert het navigatie menu.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
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

Op deze pagina worden verschillende koppelingen weer gegeven op basis van de huidige status van de aanbieding. Bijvoorbeeld:

- Als de aanbieding een concept is-concept verwijderen
- Als de aanbieding Live is: [de aanbieding wordt niet meer verkocht](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Als de aanbieding in Preview- [Live](review-publish-offer.md#previewing-and-approving-your-offer) staat
- Als u Publisher-afmelding niet hebt voltooid, kunt u het [Publiceren annuleren.](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Installatie van aanbieding

Volg deze stappen om uw aanbieding in te stellen.

### <a name="customer-leads--optional"></a>Potentiële klanten – optioneel

Wanneer u uw aanbieding naar de commerciële Marketplace met het partner centrum publiceert, kunt u deze verbinding laten maken met uw CRM-systeem (Customer Relationship Management). Zo kunt u contact gegevens van klanten ontvangen zodra iemand interesse in of gebruikt voor uw product.

1. **Selecteer de doel locatie van de lead waarnaar we leads van klanten willen verzenden**. Het partner Centrum ondersteunt de volgende CRM-systemen:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) voor klant betrokkenheid
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Als uw CRM-systeem niet hierboven wordt vermeld, gebruikt u [Azure Table](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) of [https-eind punt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) om klant lead gegevens op te slaan en exporteert u de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de doel locatie van de lead wanneer u publiceert in het partner centrum.
3. Controleer of de verbinding met de doel locatie juist is geconfigureerd. Nadat u het in het partner centrum hebt gepubliceerd, valideren we de verbinding en sturen we u een test lead. Wanneer u een voor beeld van de aanbieding bekijkt voordat deze live gaat, kunt u ook uw lead verbinding testen door zelf te proberen de aanbieding aan te schaffen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de doel locatie van de lead bijgewerkt blijft, zodat u geen leads kwijtraakt.

Hier volgen enkele aanvullende bronnen voor het beheer van leads:

- [Leads van uw aanbieding in de commerciële marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Veelgestelde vragen over Lead beheer](lead-management-faq.md#common-questions-about-lead-management)
- [Problemen met configuratie fouten van de lead oplossen](lead-management-faq.md#publishing-config-errors)
- [Overzicht Lead beheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

Selecteer **concept opslaan** voordat u doorgaat.

### <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën definiëren die worden gebruikt voor het groeperen van uw aanbieding op Marketplace en de juridische contracten die uw aanbieding ondersteunen.

#### <a name="category"></a>Categorie

Selecteer categorieën en subcategorieën om uw aanbieding te plaatsen in de juiste Zoek gebieden voor Marketplace. Zorg ervoor dat u beschrijft hoe uw aanbod deze categorieën ondersteunt in de beschrijving van de aanbieding. Selecteer:

- Ten minste één en Maxi maal twee categorieën, met inbegrip van een primaire en secundaire categorie (optioneel).
- Maxi maal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

Bekijk de volledige lijst met categorieën en subcategorieën in [Aanbevolen procedures voor aanbiedingen](gtm-offer-listing-best-practices.md). Containers worden altijd weer gegeven onder **containers** en vervolgens de categorie **container installatie kopieën** .

#### <a name="legal"></a>Juridisch

U moet voor waarden voor de aanbieding opgeven. Er zijn twee opties:

- Gebruik het standaard contract voor micro soft Commercial Marketplace.
- Geef uw eigen voor waarden op.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standard-contract voor micro soft Commercial Marketplace

We bieden een standaard contract sjabloon om trans acties in de commerciële Marketplace te vergemakkelijken. U kunt ervoor kiezen om uw oplossing aan te bieden onder het standaard contract, wat klanten alleen eenmaal hoeven te controleren en accepteren. Dit is een goede optie als u geen aangepaste voor waarden wilt maken.

Zie voor meer informatie over het standaard contract [Standard-contract voor de micro soft Commercial Marketplace](standard-contract.md). U kunt ook het [standaard](https://go.microsoft.com/fwlink/?linkid=2041178) -PDF-bestand downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

Als u het standaard contract wilt gebruiken, selecteert u het * * gebruik het standaard contract voor de commerciële Marketplace van micro soft. /standard-contract.md)

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor micro soft Commercial Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. U kunt uw oplossing aanbieden onder het Standard-contract of onder uw eigen voor waarden.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Illustreert het gebruik van het standaard contract voor het selectie vakje commerciële Marketplace van micro soft.":::

##### <a name="your-own-terms-and-conditions"></a>Uw eigen voor waarden

Als u uw eigen aangepaste voor waarden wilt opgeven, voert u deze in het vak voor **waarden** in. U kunt een onbeperkt aantal tekens van tekst in dit vak invoeren. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie, aanbieding aanbieden.

## <a name="offer-listing"></a>Aanbieding weer geven

Op deze pagina kunt u de details van het aanbod definiëren dat wordt weer gegeven in de commerciële Marketplace. Dit omvat de naam, beschrijving en installatie kopieën van de aanbieding.

> [!NOTE]
> De details van de aanbieding hoeven niet in het Engels te staan als de beschrijving van de aanbieding begint met de woord groep ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '. Het is ook handig om een nuttige koppeling te bieden om inhoud te bieden in een andere taal dan die wordt gebruikt in de details van de aanbieding.

### <a name="name"></a>Naam

De naam die u hier opgeeft, wordt weer gegeven als de titel van uw aanbieding. Dit veld is vooraf ingevuld met de tekst die u hebt ingevoerd in het vak **aanbiedings alias** wanneer u de aanbieding hebt gemaakt. U kunt deze naam later wijzigen.

De naam:

- Kan worden gemerkt (en u kunt symbolen van het handels merk en copyright gebruiken).
- Mag niet langer zijn dan 50 tekens.
- Kan geen emojis bevatten.

### <a name="search-results-summary"></a>Samen vatting van zoek resultaten

Een korte beschrijving van uw aanbieding. Dit kan Maxi maal 100 tekens lang zijn en wordt gebruikt in Zoek resultaten voor Marketplace.

### <a name="long-summary"></a>Lange samen vatting

Een gedetailleerde beschrijving van uw aanbieding. Dit kan Maxi maal 256 tekens lang zijn en wordt gebruikt in Zoek resultaten voor Marketplace.

### <a name="description"></a>Beschrijving

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Koppeling Privacybeleid

Voer het webadres in van het privacybeleid van uw organisatie. U bent verantwoordelijk om ervoor te zorgen dat uw aanbieding voldoet aan de wetten en voor schriften van de privacy. U bent ook verantwoordelijk voor het posten van een geldig privacybeleid op uw website.

#### <a name="useful-links"></a>Handige koppelingen

Bied aanvullende online documenten over uw aanbieding. U kunt Maxi maal 25 koppelingen toevoegen. Selecteer **+ een koppeling toevoegen** en vul vervolgens de volgende velden in om een koppeling toe te voegen:

- **Titel** : klanten zien dit op de pagina met details van de aanbieding.
- **Koppeling (URL)** : Voer een koppeling in voor klanten om uw online document weer te geven. De koppeling moet beginnen met http://of https://.

### <a name="contact-information"></a>Contactgegevens

U moet de naam, het e-mail adres en het telefoon nummer opgeven voor een **ondersteunings contact** en een **technische contact persoon**. Deze informatie wordt niet weer gegeven aan klanten, maar is beschikbaar voor micro soft. Dit kan ook worden gegeven aan partners van de Cloud Solution Provider (CSP).

- Ondersteunings contact (vereist): voor algemene ondersteunings vragen.
- Technische contact persoon (vereist): voor technische vragen en certificerings problemen.
- CSP-programma: Neem contact op met de leverancier van het CSP-programma.

Geef in de sectie **ondersteunings contact op met** de **ondersteunings website** waar partners ondersteuning voor uw aanbieding kunnen vinden op basis van het feit of de aanbieding beschikbaar is in wereld wijd Azure, Azure Government of beide.

Geef in de sectie **contact persoon** van het CSP-programma de koppeling op (**CSP-programma marketing materiaal**) waarin CSP-partners marketing materiaal voor uw aanbieding kunnen vinden.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende bronnen voor Marketplace-lijst

Zie [Best practices](gtm-offer-listing-best-practices.md) voor aanbiedings aanbiedingen voor meer informatie over het maken van aanbiedings vermeldingen

### <a name="marketplace-images"></a>Marketplace-installatie kopieën

Bied logo's en installatie kopieën die u kunt gebruiken met uw aanbieding. Alle installatie kopieën moeten de PNG-indeling hebben. Vage afbeeldingen worden geweigerd.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door https://upload.xboxlive.com Partner Center gebruikte service niet blokkeert.

#### <a name="store-logos"></a>Winkel logo's

Geef een PNG-bestand voor het logo van **grote** grootte op. Het partner centrum gebruikt deze om een **klein** en **gemiddeld** logo te maken. U kunt deze desgewenst later vervangen door andere installatie kopieën.

- **Groot** (van 216 x 216 tot 350 x 350 px, vereist)
- **Gemiddeld** (90 x 90 px, optioneel)
- **Klein** (48 x 48 px, optioneel)

Deze logo's worden op verschillende plaatsen in de vermelding gebruikt:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Scherm afbeeldingen (optioneel)

Voeg Maxi maal vijf scherm opnamen toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot en in PNG-indeling zijn.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg Maxi maal vijf Video's toe die uw aanbieding aantonen. Voer de naam van de video, het webadres en een miniatuur van de PNG-afbeelding van de video in op 1280 x 720 pixels.

#### <a name="offer-examples"></a>Voorbeelden van aanbieding

In de volgende voor beelden ziet u hoe de velden van het aanbod lijst worden weer gegeven op verschillende plaatsen van de aanbieding.

Hiermee wordt de **aanbiedings** pagina weer gegeven in azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Illustreert de aanbiedings pagina in azure Marketplace." :::

Hiermee worden Zoek resultaten weer gegeven in azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Illustreert de zoek resultaten in azure Marketplace.":::

Hiermee **wordt de pagina aanbieding weer** gegeven in azure portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Illustreert de aanbiedings pagina van de aanbieding in Azure Portal.":::

Hiermee worden Zoek resultaten weer gegeven in Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Illustreert de zoek resultaten in Azure Portal.":::

## <a name="preview"></a>Preview

Op het tabblad preview kunt u een beperkte preview- **doel groep** kiezen voor het valideren van uw aanbieding voordat u deze live publiceert.

> [!IMPORTANT]
> Nadat u uw aanbieding in **Preview** hebt bekeken, moet u **Live go** selecteren om uw aanbieding naar het publiek te publiceren.

Geef uw preview-doel groep op met behulp van de Azure-abonnements-ID-GUID'S, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten.

> [!NOTE]
> U kunt uw Azure-abonnements-ID vinden op de pagina Abonnementen in Azure Portal.

Voeg ten minste één Azure-abonnements-ID afzonderlijk toe (Maxi maal 10) of door een CSV-bestand te uploaden (Maxi maal 100). Door deze abonnement-Id's toe te voegen, bepaalt u wie uw aanbieding kunnen bekijken voordat ze live worden gepubliceerd. Als uw aanbieding al Live is, kunt u een preview doel groep kiezen om wijzigingen of updates van uw aanbieding te testen.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="plan-overview"></a>Overzicht van plan

Op dit tabblad kunt u binnen dezelfde aanbieding verschillende plan opties opgeven. Plannen (voorheen Sku's) kunnen verschillen in wat voor Clouds beschikbaar zijn, zoals globale Clouds, overheids Clouds en de afbeelding waarnaar wordt verwezen door het plan. Als u uw aanbieding in de commerciële Marketplace wilt aanbieden, moet u ten minste één abonnement instellen.

U kunt Maxi maal 100 abonnementen maken voor elke aanbieding: Maxi maal 45 van deze kunnen privé zijn. Meer informatie over privé-abonnementen [vindt u in de micro soft Commercial Marketplace](private-offers.md).

Nadat u uw plannen hebt gemaakt, wordt op het tabblad Overzicht van het **plan** het volgende weer gegeven:

- Namen van plannen
- Prijsmodel
- Azure-regio's (wereld wijd of overheid)
- Huidige publicatie status
- Beschik bare acties

De acties die beschikbaar zijn in het plan overzicht variëren, afhankelijk van de huidige status van uw abonnement. Deze omvatten:

- **Concept verwijderen** : als de plan status een concept is.
- **Verkoop plan stoppen** : als de plan status Live is gepubliceerd.

### <a name="create-new-plan"></a>Nieuw plan maken

Selecteer **nieuw plan maken**. Het dialoog venster **nieuw plan** wordt weer gegeven.

Maak in het vak **abonnement-id** een unieke plan-id voor elk abonnement in deze aanbieding. Deze ID is zichtbaar voor klanten in het webadres van het product. Gebruik alleen kleine letters en cijfers, streepjes of onderstrepings tekens en Maxi maal 50.

> [!NOTE]
> De plan-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

Voer in het vak **naam van abonnement** een naam in voor dit abonnement. Klanten zien deze naam wanneer ze bepalen welk abonnement binnen uw aanbieding moet worden geselecteerd. Maak in deze aanbieding een unieke naam voor elk abonnement. U kunt bijvoorbeeld een aanbiedings naam van **Windows Server** gebruiken met plannen **Windows Server 2016** en **Windows Server 2019**.

### <a name="plan-setup"></a>Installatie plannen

Op dit tabblad kunt u kiezen in welke Clouds het plan beschikbaar is. Uw antwoorden op dit tabblad bepalen welke velden op andere tabbladen worden weer gegeven.

#### <a name="azure-regions"></a>Azure-regio's

Alle abonnementen voor Azure-container aanbiedingen worden automatisch beschikbaar gemaakt in **Azure Global**.  Uw abonnement kan door klanten worden gebruikt in alle wereld wijde Azure-regio's die gebruikmaken van de commerciële Marketplace. Zie [geografische Beschik baarheid en valuta ondersteuning](marketplace-geo-availability-currencies.md)voor meer informatie.

Selecteer de optie [Azure Government](../azure-government/documentation-government-welcome.md) om uw oplossing hier weer te geven. Dit is een community-Cloud met gecontroleerde toegang voor klanten van de Amerikaanse federale, staats-en lokale of tribale overheids instanties en partners die in aanmerking komen voor de overheid. Als uitgever bent u verantwoordelijk voor nalevings controles, beveiligings maatregelen en aanbevolen procedures voor deze Cloud community. Azure Government maakt gebruik van fysiek geïsoleerde data centers en netwerken (alleen in de Verenigde Staten). Voordat u naar Azure Government [publiceert](../azure-government/documentation-government-manage-marketplace-partners.md) , moet u de oplossing in dat gebied testen en bevestigen, omdat de resultaten mogelijk verschillend zijn. Vraag een proef account aan [Microsoft Azure Government proef versie](https://azure.microsoft.com/global-infrastructure/government/request/)om uw oplossing te maken en te testen.

> [!NOTE]
> Nadat uw plan is gepubliceerd en beschikbaar is in een specifieke regio, kunt u die regio niet verwijderen.

#### <a name="azure-government-certifications"></a>Azure Government-certificeringen

Deze optie kan alleen worden gezien als **Azure Government** is geselecteerd onder **Azure-regio's**.

Azure Government services verwerken gegevens die onderworpen zijn aan bepaalde wettelijke voor schriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD N4 en CJIS.

Als u uw certificeringen voor deze Program ma's wilt weer geven, kunt u Maxi maal 100 koppelingen opgeven waarin deze worden beschreven. Dit kunnen koppelingen zijn naar uw aanbiedingen op het programma, rechtstreeks of op uw eigen website. Deze koppelingen zijn alleen zichtbaar voor Azure Government klanten.

### <a name="plan-listing"></a>Aanbieding plannen

Dit tabblad bevat specifieke informatie voor elk abonnement binnen de huidige aanbieding.

### <a name="plan-name"></a>Plan naam

Dit is vooraf ingevuld met de naam die u hebt opgegeven bij het maken van het abonnement. U kunt deze naam indien nodig wijzigen. Het kan Maxi maal 50 tekens lang zijn. Deze naam wordt weer gegeven als de titel van dit plan in azure Marketplace en Azure Portal. De naam wordt als de standaard modulenaam gebruikt nadat het plan gereed is om te worden gebruikt.

### <a name="plan-summary"></a>Samen vatting plannen

Een korte samen vatting van het software abonnement (niet de aanbieding). Deze samen vatting wordt weer gegeven in de zoek resultaten van Azure Marketplace en kan Maxi maal 100 tekens bevatten.

### <a name="plan-description"></a>Plan beschrijving

Beschrijf wat dit software plan uniek is, evenals verschillen tussen de plannen binnen uw aanbieding. Beschrijf de aanbieding niet, alleen het abonnement. Deze beschrijving wordt weer gegeven in azure Marketplace en in de Azure Portal op de **aanbiedings** pagina. Dit kan hetzelfde zijn als de inhoud die u in de samen vatting van het abonnement hebt gegeven en Maxi maal 2.000 tekens bevat.

Selecteer **Opslaan** na het volt ooien van deze velden.

#### <a name="plan-examples"></a>Voor beelden plannen

In de volgende voor beelden ziet u hoe de velden van het plan vermelding worden weer gegeven in verschillende weer gaven.

Dit zijn de velden in azure Marketplace wanneer u de details van het abonnement bekijkt:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Illustreert de velden die u ziet wanneer u de details van het plan bekijkt in azure Marketplace.":::

Dit zijn de details van het abonnement op de Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Illustreert Details van het abonnement op de Azure Portal.":::

### <a name="plan-availability"></a>Beschik baarheid plannen

Als u uw gepubliceerde aanbieding wilt verbergen zodat klanten deze niet kunnen zoeken, zoeken of kopen op Marketplace, schakelt u het selectie vakje **plan verbergen** op het tabblad **Beschik baarheid** in.

Dit veld wordt gebruikt wanneer:

- De aanbieding is bedoeld om indirect te worden gebruikt wanneer ernaar wordt verwezen via een andere toepassing.
- De aanbieding mag niet afzonderlijk worden gekocht.
- Het plan werd gebruikt voor de eerste test en is niet meer relevant.
- Het abonnement werd gebruikt voor tijdelijke of seizoen aanbiedingen en kan niet meer worden aangeboden.

## <a name="technical-configuration"></a>Technische configuratie

Container installatie kopieën moeten worden gehost in een persoonlijke [Azure container Registry](https://azure.microsoft.com/services/container-registry/). Geef op het tabblad **technische configuratie** referentie gegevens op voor de opslag plaats van de container installatie kopie in de Azure container Registry.

Nadat de aanbieding is gepubliceerd, wordt uw container installatie kopie gekopieerd naar Azure Marketplace in een specifiek openbaar container register. Alle aanvragen voor het gebruik van uw container installatie kopie worden aangeboden vanuit het open bare container register van Azure Marketplace, niet aan uw persoonlijke voor naam. Zie [technische activa van Azure-container voorbereiden](create-azure-container-technical-assets.md)voor meer informatie.

### <a name="image-repository-details"></a>Details van de afbeeldings opslagplaats

Geef de volgende informatie op het tabblad Details van de **afbeeldings opslagplaats** .

**Azure-abonnements-id** : Geef de abonnements-id op waar het gebruik wordt gerapporteerd en de services worden gefactureerd voor de Azure container Registry die uw container installatie kopie bevatten. U kunt deze ID vinden op de [pagina Abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in het Azure Portal.

**Naam van de Azure-resource groep** : Geef de naam van de [resource groep](../azure-resource-manager/management/manage-resource-groups-portal.md) op die de Azure container Registry bevat met de container installatie kopie. De resource groep moet toegankelijk zijn in de abonnements-ID (hierboven). U vindt de naam op de pagina [resource groepen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) in de Azure Portal.

**Azure container Registry naam** : Geef de naam op van de [Azure container Registry](../container-registry/container-registry-intro.md) met de container installatie kopie. Het container register moet zich bevinden in de Azure-resource groep die u eerder hebt ingesteld. Neem alleen de register naam op, niet de volledige naam van de aanmeldings server. Zorg ervoor dat u **azurecr.io** weglaat uit de naam. U kunt de register naam vinden op de [pagina container registers](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in de Azure Portal.

**Gebruikers naam van de beheerder voor de Azure container Registry** : Geef de [gebruikers naam](../container-registry/container-registry-authentication.md#admin-account)van de beheerder op die is gekoppeld aan de Azure container Registry met de container installatie kopie. De gebruikers naam en het wacht woord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het REGI ster. Als u de gebruikers naam en het wacht woord van de beheerder wilt ophalen, stelt u de eigenschap **beheerder** in op **True** met behulp van de Azure Command-Line interface (CLI). U kunt optioneel een **admin-gebruiker** instellen om in azure Portal in te **scha kelen** .

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Illustreert het dialoog venster update container Registry.":::

**Wacht woord voor de Azure container Registry** : Geef het wacht woord op voor de beheerder van de gebruikers naam die is gekoppeld aan de Azure container Registry en heeft de container installatie kopie. De gebruikers naam en het wacht woord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het REGI ster. U kunt het wacht woord ophalen van de Azure portal door naar **container Registry**  >  **toegangs sleutels** of met Azure CLI te gaan met behulp van de [weer geven opdracht](/cli/azure/acr/credential#az-acr-credential-show).

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Illustreert het menu toegangs sleutel.":::

**De naam van de opslag plaats in de Azure container Registry**. Geef de naam op van de Azure Container Registry opslag plaats met uw installatie kopie. Neem de naam van de opslag plaats op wanneer u de installatie kopie naar het REGI ster pusht. U kunt de naam van de opslag plaats vinden door naar de pagina [container Registry](https://azure.microsoft.com/services/container-registry/)  >  **opslagplaatsen** te gaan. Zie [container register opslagplaatsen weer geven in azure Portal](../container-registry/container-registry-repositories.md)voor meer informatie.

> [!NOTE]
> Nadat de naam is ingesteld, kan deze niet meer worden gewijzigd. Gebruik een unieke naam voor elke aanbieding in uw account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Afbeeldings Tags voor nieuwe versies van uw aanbieding

Klanten moeten automatisch updates kunnen ophalen van de Azure Marketplace wanneer u een update publiceert. Als ze niet willen bijwerken, moeten ze een specifieke versie van de installatie kopie kunnen blijven. U kunt dit doen door telkens wanneer u een update voor de installatie kopie maakt, nieuwe afbeeldings tags toe te voegen.

### <a name="image-tag"></a>Afbeeldings code

Dit veld moet een **laatste** tag bevatten die verwijst naar de nieuwste versie van uw installatie kopie op alle ondersteunde platforms. Het moet ook een versie label bevatten (bijvoorbeeld vanaf xx. xx. xx, waarbij XX een getal is). Klanten moeten [manifest Tags](https://github.com/estesp/manifest-tool) gebruiken om meerdere platforms te bereiken. Alle labels waarnaar wordt verwezen door een manifest-tag, moeten ook worden toegevoegd, zodat ze kunnen worden geüpload.

Alle manifest Tags (met uitzonde ring van de laatste tag) moeten beginnen met X. Y **-** of x. y. z-waarbij x, Y en z een geheel getal zijn. Als een van de **nieuwste** Tags bijvoorbeeld verwijst naar 1.0.1-Linux-x64, 1.0.1-Linux-arm32 en 1.0.1-Windows-arm32, moeten deze zes Tags worden toegevoegd aan dit veld. Zie [technische activa van Azure-container voorbereiden](create-azure-container-technical-assets.md)voor meer informatie.

> [!NOTE]
> Vergeet niet om een test code toe te voegen aan uw installatie kopie, zodat u de afbeelding tijdens het testen kunt identificeren.

## <a name="review-and-publish"></a>Beoordelen en publiceren

Nadat u alle vereiste delen van de aanbieding hebt voltooid, kunt u deze verzenden om te controleren en te publiceren.

Selecteer in de rechter bovenhoek van de portal **controleren en** **publiceren**.

Op de pagina controleren kunt u het volgende doen:

- Bekijk de voltooiings status voor elke sectie van de aanbieding. U kunt pas publiceren als alle secties van de aanbieding zijn gemarkeerd als voltooid.
  - **Niet gestart** : niet gestart en moet worden voltooid.
  - **Onvolledig** : bevat fouten die moeten worden hersteld of waarvoor u meer informatie moet opgeven. Zie de secties eerder in dit document voor meer informatie.
  - **Volledig** : bevat alle vereiste gegevens zonder fouten. Alle onderdelen van de aanbieding moeten volledig zijn voordat u de aanbieding kunt indienen.
- Geef test instructies op het certificerings team om te controleren of uw aanbieding correct is getest. Geef ook aanvullende notities op die handig zijn voor het leren van uw aanbieding.

Selecteer **publiceren** om de aanbieding voor publicatie in te dienen.

We sturen u een e-mail om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren.

Als u uw aanbieding naar het publiek wilt publiceren, gaat u naar partner centrum en selecteert u **Go-Live**.

## <a name="next-step"></a>Volgende stap

- [Een bestaande aanbieding bijwerken in de commerciële Marketplace](./partner-center-portal/update-existing-offer.md)