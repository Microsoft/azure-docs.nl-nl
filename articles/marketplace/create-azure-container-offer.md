---
title: Een Azure-containeraanbieding maken - Azure Marketplace
description: Meer informatie over het maken en publiceren van een containeraanbieding naar Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: dc7a81f1646fc9f51a4e0bcaf37ef61ca669414e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780511"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Een Azure Container-aanbieding maken in Azure Marketplace

In dit artikel wordt beschreven hoe u een containeraanbieding voor Azure Marketplace. Voordat u begint, [maakt u een commerciële Marketplace-account in Partner Center](create-account.md) als u dit nog niet hebt gedaan. Zorg ervoor dat uw account is ingeschreven in het commerciële marketplace-programma.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan [bij Partner Center](https://partner.microsoft.com/dashboard/home).

2. Selecteer in het navigatiemenu aan de linkerkant **Commerciële**  >  **marketplace-overzicht.**

3. Selecteer op de pagina Overzicht **de optie + Nieuwe aanbieding** Azure  >  **Container.**

   ![Illustreert het linkernavigatiemenu.](./partner-center-portal/media/new-offer-azure-container.png)

> [!TIP]
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

Op deze pagina worden verschillende koppelingen weergegeven op basis van de huidige status van de aanbieding. Bijvoorbeeld:

- Als de aanbieding een concept is: conceptaanbieding verwijderen
- Als de aanbieding live is: verkoop [van de aanbieding stoppen](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)
- Als de aanbieding in preview is - [Go-live](review-publish-offer.md#previewing-and-approving-your-offer)
- Als u het afloggen van de uitgever nog niet hebt voltooid: [publicatie annuleren.](review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Aanbieding instellen

Volg deze stappen om uw aanbieding in te stellen.

### <a name="customer-leads--optional"></a>Leads van klanten – optioneel

Wanneer u uw aanbieding publiceert op de commerciële marketplace Partner Center, kunt u deze verbinden met uw CRM-systeem (Customer Relationship Management). Hiermee kunt u contactgegevens van klanten ontvangen zodra iemand interesse heeft in uw product of uw product gebruikt.

1. **Selecteer een leadbestemming waar u wilt dat we leads van klanten verzenden.** Partner Center ondersteunt de volgende CRM-systemen:

   - [Dynamics 365](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > Als uw CRM-systeem hierboven niet wordt vermeld, gebruikt u [Azure Table](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) of [Https Endpoint](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) om klant leadgegevens op te slaan en exporteert u de gegevens vervolgens naar uw CRM-systeem.

2. Verbind uw aanbieding met de leadbestemming bij het publiceren in Partner Center.
3. Controleer of de verbinding met de leadbestemming juist is geconfigureerd. Nadat u deze hebt gepubliceerd in Partner Center, valideren we de verbinding en sturen we u een test lead. Terwijl u een voorbeeld van de aanbieding bekijkt voordat deze live gaat, kunt u uw leadverbinding ook testen door de aanbieding zelf te kopen in de preview-omgeving.
4. Zorg ervoor dat de verbinding met de leadbestemming bijgewerkt blijft, zodat u geen leads verliest.

Hier volgen enkele aanvullende resources voor leadbeheer:

- [Leads van uw aanbieding in de commerciële marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md)
- [Veelvoorkomende vragen over leadbeheer](lead-management-faq.md#common-questions-about-lead-management)
- [Problemen met leadconfiguratie oplossen](lead-management-faq.md#publishing-config-errors)
- [Overzicht van leadbeheer](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Zorg ervoor dat de pop-upblokkering is uitgeschakeld).

Selecteer **Concept opslaan voordat** u doorgaat.

### <a name="properties"></a>Eigenschappen

Op deze pagina kunt u de categorieën definiëren die worden gebruikt voor het groepen van uw aanbieding op de marketplace en de juridische contracten die uw aanbieding ondersteunen.

#### <a name="category"></a>Categorie

Selecteer categorieën en subcategorieën om uw aanbieding in de juiste marketplace-zoekgebieden te plaatsen. Beschrijf in de beschrijving van de aanbieding hoe uw aanbieding ondersteuning biedt voor deze categorieën. Selecteer:

- Ten minste één en maximaal twee categorieën, waaronder een primaire en een secundaire categorie (optioneel).
- Maximaal twee subcategorieën voor elke primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert **u Niet van toepassing.**

Zie de volledige lijst met categorieën en subcategorieën in [Best practices voor aanbiedingsvermeldingen.](gtm-offer-listing-best-practices.md) Containers worden altijd weergegeven **onder Containers** en vervolgens onder de categorie **Container images.**

#### <a name="legal"></a>Juridisch

U moet de voorwaarden voor de aanbieding verstrekken. Er zijn twee opties:

- Gebruik de Standaardcontract voor de commerciële marketplace van Microsoft.
- Geef uw eigen voorwaarden op.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standaardcontract voor de commerciële marketplace van Microsoft

We bieden een Standaardcontract om transacties in de commerciële marketplace mogelijk te maken. U kunt ervoor kiezen om uw oplossing aan te bieden onder Standaardcontract, die klanten slechts één keer hoeven te controleren en accepteren. Dit is een goede optie als u geen aangepaste voorwaarden wilt maken.

Zie voor meer informatie over de Standaardcontract, [Standaardcontract voor de commerciële marketplace van Microsoft.](standard-contract.md) U kunt ook de [Standaardcontract](https://go.microsoft.com/fwlink/?linkid=2041178) PDF downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

Als u de Standaardcontract wilt gebruiken, selecteert u de optie **De Standaardcontract voor de commerciële marketplace van Microsoft](.. /standard-contract.md)

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met behulp van het Standard-contract voor de commerciële marketplace van Microsoft, kunt u uw eigen aangepaste voorwaarden niet gebruiken. Bied uw oplossing aan onder de Standaardcontract of onder uw eigen voorwaarden.

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Illustreert het selectievakje Using the Standaardcontract for Microsoft's commercial marketplace (De Standaardcontract gebruiken voor de commerciële marketplace van Microsoft).":::

##### <a name="your-own-terms-and-conditions"></a>Uw eigen voorwaarden

Als u uw eigen aangepaste voorwaarden wilt verstrekken, voert u deze in het **vak Voorwaarden** in. U kunt een onbeperkt aantal tekens tekst invoeren in dit vak. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

Selecteer **Concept opslaan** voordat u verdergaat met de volgende sectie, Aanbiedingsvermelding.

## <a name="offer-listing"></a>Aanbiedingsvermelding

Op deze pagina kunt u de details van de aanbieding definiëren die worden weergegeven in de commerciële marketplace. Dit omvat de naam, beschrijving en afbeeldingen van de aanbieding.

> [!NOTE]
> Aanbiedingsdetails hoeft niet in het Engels te zijn als de beschrijving van de aanbieding begint met de zin 'Deze toepassing is alleen beschikbaar in [niet-Engelse taal].' U kunt ook een nuttige koppeling geven om inhoud aan te bieden in een andere taal dan de taal die wordt gebruikt in de aanbiedingsvermelding.

### <a name="name"></a>Name

De naam die u hier optreedt, wordt weergegeven als de titel van uw aanbieding. Dit veld wordt vooraf ingevuld met de tekst die u hebt ingevoerd in het **vak Aanbiedingsalias** toen u de aanbieding maakte. U kunt deze naam later wijzigen.

De naam:

- Kan handelsmerken zijn (en u kunt handelsmerken en auteursrechtsymbolen opnemen).
- Mag niet langer zijn dan 50 tekens.
- Kan geen emoji's bevatten.

### <a name="search-results-summary"></a>Samenvatting van zoekresultaten

Een korte beschrijving van uw aanbieding. Dit kan maximaal 100 tekens lang zijn en wordt gebruikt in marketplace-zoekresultaten.

### <a name="long-summary"></a>Lange samenvatting

Een gedetailleerde beschrijving van uw aanbieding. Deze mag maximaal 256 tekens lang zijn en wordt gebruikt in marketplace-zoekresultaten.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Koppeling naar privacybeleid

Voer het webadres van het privacybeleid van uw organisatie in. U bent verantwoordelijk om ervoor te zorgen dat uw aanbieding voldoet aan de privacywetgeving en -regelgeving. U bent ook verantwoordelijk voor het plaatsen van een geldig privacybeleid op uw website.

#### <a name="useful-links"></a>Handige koppelingen

Geef aanvullende onlinedocumenten over uw aanbieding op. U kunt maximaal 25 koppelingen toevoegen. Als u een koppeling wilt toevoegen, **selecteert u + Een koppeling toevoegen** en vult u de volgende velden in:

- **Titel:** klanten zien dit op de detailpagina van uw aanbieding.
- **Koppeling (URL) :** voer een koppeling in voor klanten om uw onlinedocument weer te geven. De koppeling moet beginnen met http:// of https://.

### <a name="contact-information"></a>Contactgegevens

U moet de naam, het e-mailadres en het telefoonnummer voor een contactpersoon **voor** ondersteuning en een technische **contactpersoon verstrekken.** Deze informatie wordt niet weergegeven aan klanten, maar is wel beschikbaar voor Microsoft. Het kan ook worden verstrekt aan Cloud Solution Provider (CSP)-partners.

- Contactpersoon voor ondersteuning (vereist): voor algemene ondersteuningsvragen.
- Technische contactpersoon (vereist): voor technische vragen en certificeringsproblemen.
- Contactpersoon voor CSP-programma (optioneel): voor vragen van resellers met betrekking tot het CSP-programma.

Geef in **de sectie**  Contactpersoon voor ondersteuning de ondersteuningswebsite op waar partners ondersteuning voor uw aanbieding kunnen vinden op basis van of de aanbieding beschikbaar is in Azure, Azure Government wereldwijd of beide.

Geef in de sectie Contactpersoon voor **CSP-programma** de koppeling **(CSP Program Marketing Materials)** op waar CSP-partners marketingmateriaal voor uw aanbieding kunnen vinden.

#### <a name="additional-marketplace-listing-resources"></a>Aanvullende marketplace-vermeldingsbronnen

Zie Best practices voor aanbiedingsvermeldingen voor meer informatie over [het maken van aanbiedingen](gtm-offer-listing-best-practices.md)

### <a name="marketplace-images"></a>Marketplace-afbeeldingen

Geef logo's en afbeeldingen op die u met uw aanbieding kunt gebruiken. Alle afbeeldingen moeten de PNG-indeling hebben. Wazige afbeeldingen worden geweigerd.

[!INCLUDE [logo tips](./includes/graphics-suggestions.md)]

>[!Note]
>Als u problemen hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de service die wordt gebruikt door de https://upload.xboxlive.com Partner Center.

#### <a name="store-logos"></a>Logo's opslaan

Geef een PNG-bestand op voor het logo **van** grote grootte. Partner Center gebruikt deze om een **klein** en een medium logo **te** maken. U kunt deze eventueel later vervangen door andere afbeeldingen.

- **Groot** (van 216 x 216 tot 350 x 350 px, vereist)
- **Gemiddeld** (90 x 90 px, optioneel)
- **Klein** (48 x 48 px, optioneel)

Deze logo's worden op verschillende plaatsen in de lijst gebruikt:

[!INCLUDE [logos-azure-marketplace-only](./includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](./includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>Schermopnamen (optioneel)

Voeg maximaal vijf schermafbeeldingen toe die laten zien hoe uw aanbieding werkt. Elk moet 1280 x 720 pixels groot en PNG-indeling hebben.

#### <a name="videos-optional"></a>Video's (optioneel)

Voeg maximaal vijf video's toe die uw aanbieding demonstreren. Voer de naam van de video, het webadres en een PNG-miniatuurafbeelding van de video in met een grootte van 1280 x 720 pixels.

#### <a name="offer-examples"></a>Voorbeelden van aanbieding

In de volgende voorbeelden ziet u hoe de velden voor aanbiedingsvermelding op verschillende plaatsen van de aanbieding worden weergegeven.

Hiermee wordt de **pagina Aanbiedingsvermelding** weergegeven in Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Illustreert de pagina Aanbiedingsvermelding in Azure Marketplace." :::

Hiermee worden zoekresultaten in de volgende Azure Marketplace:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Illustreert de zoekresultaten in Azure Marketplace.":::

Hiermee wordt de **pagina Aanbiedingsvermelding** weergegeven in Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Illustreert de pagina aanbiedingsvermelding in Azure Portal.":::

Hiermee worden zoekresultaten in de volgende Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Illustreert de zoekresultaten in Azure Portal.":::

## <a name="preview"></a>Preview

Op het tabblad Preview kunt u een beperkte **preview-doelgroep kiezen om** uw aanbieding te valideren voordat u deze live publiceert.

> [!IMPORTANT]
> Nadat u uw aanbieding in **preview hebt bekeken,** moet u **Live gaan selecteren** om uw aanbieding naar het publiek te publiceren.

Geef uw preview-doelgroep op met behulp van GUID's voor Azure-abonnements-id's, samen met een optionele beschrijving voor elke doelgroep. Geen van deze velden kan door klanten worden gezien.

> [!NOTE]
> U vindt uw Azure-abonnements-id op de pagina Abonnementen in Azure Portal.

Voeg ten minste één Azure-abonnements-id toe, afzonderlijk (maximaal 10) of door een CSV-bestand (maximaal 100) te uploaden. Door deze abonnements-ID's toe te voegen, bepaalt u wie een voorbeeld van uw aanbieding kan bekijken voordat deze live wordt gepubliceerd. Als uw aanbieding al live is, kunt u een preview-doelgroep kiezen om wijzigingen of updates voor uw aanbieding te testen.

Selecteer **Concept opslaan voordat** u doorgaat.

## <a name="plan-overview"></a>Planoverzicht

Op dit tabblad kunt u verschillende abonnementsopties binnen dezelfde aanbieding bieden. Plannen (voorheen SKU's genoemd) kunnen verschillen wat betreft de beschikbare clouds, zoals wereldwijde clouds, overheidswolken en de afbeelding waarnaar in het plan wordt verwezen. Als u uw aanbieding wilt in de commerciële marketplace, moet u ten minste één abonnement instellen.

U kunt maximaal 100 abonnementen voor elke aanbieding maken. Maximaal 45 van deze abonnementen kunnen privé zijn. Meer informatie over privé-abonnementen in [Privéaanbiedingen in de commerciële marketplace van Microsoft.](private-offers.md)

Nadat u uw plannen hebt gemaakt, ziet u **op het tabblad Planoverzicht** het volgende:

- Plannamen
- Prijsmodel
- Azure-regio's (wereldwijd of overheid)
- Huidige publicatiestatus
- Beschikbare acties

De acties die beschikbaar zijn in het planoverzicht variëren afhankelijk van de huidige status van uw plan. Deze omvatten:

- **Concept verwijderen:** als de status van het plan concept is.
- **Verkoopplan stoppen:** als de status van het plan live wordt gepubliceerd.

### <a name="create-new-plan"></a>Nieuw plan maken

Selecteer **Nieuw abonnement maken.** Het dialoogvenster Nieuw **plan** wordt weergegeven.

Maak in **het vak Plan-id** een unieke plan-id voor elk plan in deze aanbieding. Deze id is zichtbaar voor klanten in het webadres van het product. Gebruik alleen kleine letters en cijfers, streepjes of onderstrepingstekens en maximaal 50 tekens.

> [!NOTE]
> De plan-id kan niet worden gewijzigd nadat u **Maken hebt geselecteerd.**

Voer in **het vak** Plannaam een naam in voor dit plan. Klanten zien deze naam wanneer ze bepalen welk abonnement ze in uw aanbieding willen selecteren. Maak een unieke naam voor elk plan in deze aanbieding. U kunt bijvoorbeeld de naam van een aanbieding **van Windows Server gebruiken** met de abonnementen Windows Server **2016** en **Windows Server 2019.**

### <a name="plan-setup"></a>Plan instellen

Op dit tabblad kunt u kiezen in welke clouds het abonnement beschikbaar is. Uw antwoorden op dit tabblad zijn van invloed op welke velden worden weergegeven op andere tabbladen.

#### <a name="azure-regions"></a>Azure-regio's

Alle abonnementen voor Azure Container-aanbiedingen worden automatisch beschikbaar gesteld in **Azure Global**.  Uw plan kan worden gebruikt door klanten in alle Azure-regio's wereldwijd die gebruikmaken van de commerciële marketplace. Zie Geografische beschikbaarheid en [valutaondersteuning voor meer informatie.](marketplace-geo-availability-currencies.md)

Selecteer de [Azure Government](../azure-government/documentation-government-welcome.md) om uw oplossing hier weer te laten zien. Dit is een overheidscloud met beheerde toegang voor klanten van Amerikaanse federale, staats- en lokale of overheidsinstanties, en partners die in aanmerking komen om ze te bedienen. Als uitgever bent u verantwoordelijk voor alle nalevingscontroles, beveiligingsmaatregelen en best practices voor deze cloud-community. Azure Government fysiek geïsoleerde datacenters en netwerken (alleen in de Verenigde Staten). Voordat [u](../azure-government/documentation-government-manage-marketplace-partners.md) publiceert Azure Government, test en bevestigt u uw oplossing binnen dat gebied, aangezien de resultaten mogelijk anders zijn. Als u uw oplossing wilt maken en testen, vraagt u een proefaccount aan [bij Microsoft Azure Government proefversie](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Nadat uw abonnement is gepubliceerd en beschikbaar is in een specifieke regio, kunt u die regio niet verwijderen.

#### <a name="azure-government-certifications"></a>Azure Government certificeringen

Deze optie kan alleen worden gezien als **Azure Government** is geselecteerd onder **Azure-regio's.**

Azure Government-services verwerken gegevens die onderhevig zijn aan bepaalde wettelijke voorschriften en vereisten. Bijvoorbeeld FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 en CJIS.

Als u uw certificeringen voor deze programma's wilt tonen, kunt u maximaal 100 koppelingen opgeven die deze beschrijven. Dit kunnen koppelingen naar uw vermeldingen op het programma zijn, rechtstreeks of naar uw eigen website. Deze koppelingen zijn alleen zichtbaar Azure Government klanten.

### <a name="plan-listing"></a>Abonnementsvermelding

Op dit tabblad wordt specifieke informatie weergegeven voor elk ander abonnement binnen de huidige aanbieding.

### <a name="plan-name"></a>Plannaam

Dit is vooraf ingevuld met de naam die u aan uw plan hebt gegeven toen u het maakte. U kunt deze naam naar behoefte wijzigen. Deze mag maximaal 50 tekens lang zijn. Deze naam wordt weergegeven als de titel van dit plan in Azure Marketplace en Azure Portal. Het wordt gebruikt als de standaardnaam van de module nadat het plan gereed is om te worden gebruikt.

### <a name="plan-summary"></a>Abonnementsoverzicht

Een kort overzicht van uw softwareabonnement (niet de aanbieding). Deze samenvatting wordt weergegeven in Azure Marketplace en mag maximaal 100 tekens bevatten.

### <a name="plan-description"></a>Beschrijving van het plan

Beschrijf wat dit softwareabonnement uniek maakt, evenals de verschillen tussen abonnementen binnen uw aanbieding. Beschrijf de aanbieding niet, alleen het abonnement. Deze beschrijving wordt weergegeven in Azure Marketplace en in de Azure Portal op de **aanbiedingspagina.** Dit kan dezelfde inhoud zijn als die u hebt opgegeven in het planoverzicht en maximaal 2000 tekens bevatten.

Selecteer **Opslaan na** het voltooien van deze velden.

#### <a name="plan-examples"></a>Planvoorbeelden

In de volgende voorbeelden ziet u hoe de velden voor planvermelding in verschillende weergaven worden weergegeven.

Dit zijn de velden in Azure Marketplace bij het weergeven van plandetails:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Illustreert de velden die u ziet bij het weergeven van plandetails in Azure Marketplace.":::

Dit zijn de details van het Azure Portal:

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Illustreert plandetails van de Azure Portal.":::

### <a name="plan-availability"></a>Beschikbaarheid van plannen

Als u uw gepubliceerde aanbieding wilt verbergen zodat klanten deze niet kunnen zoeken, zoeken of kopen in marketplace, selecteert u het selectievakje Plan verbergen op het **tabblad** Beschikbaarheid. 

Dit veld wordt gebruikt wanneer:

- De aanbieding is bedoeld om indirect te worden gebruikt wanneer er via een andere toepassing naar wordt verwezen.
- De aanbieding mag niet afzonderlijk worden gekocht.
- Het plan is gebruikt voor de eerste test en is niet meer relevant.
- Het plan is gebruikt voor tijdelijke of seizoensgebonden aanbiedingen en mag niet meer worden aangeboden.

## <a name="technical-configuration"></a>Technische configuratie

Container-afbeeldingen moeten worden gehost in een [Azure Container Registry.](https://azure.microsoft.com/services/container-registry/) Geef op **het tabblad Technische** configuratie referentiegegevens op voor de opslagplaats voor de containerafbeelding in de Azure Container Registry.

Nadat de aanbieding is gepubliceerd, wordt uw containerkopie gekopieerd naar Azure Marketplace in een specifiek openbaar containerregister. Alle aanvragen voor het gebruik van uw container-afbeelding worden Azure Marketplace het openbare containerregister, niet uw persoonlijke containerregister. Zie Uw technische assets voor [Azure Container voorbereiden voor meer informatie.](create-azure-container-technical-assets.md)

### <a name="image-repository-details"></a>Details van opslagplaats voor afbeeldingen

Geef de volgende informatie op het tabblad **Details van de opslagplaats voor afbeeldingen** op.

**Azure-abonnements-id:** geef de abonnements-id op waar het gebruik wordt gerapporteerd en services worden gefactureerd voor de Azure Container Registry uw containerafbeelding bevat. U vindt deze id op de [pagina Abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure Portal.

**Naam van Azure-resourcegroep:** geef de [naam van de resourcegroep](../azure-resource-manager/management/manage-resource-groups-portal.md) op die de Azure Container Registry de containerafbeelding bevat. De resourcegroep moet toegankelijk zijn in de abonnements-id (hierboven). U vindt de naam op de [pagina Resourcegroepen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) in Azure Portal.

**Azure Container Registry:** geef de naam op van de [Azure Container Registry](../container-registry/container-registry-intro.md) die uw containerafbeelding heeft. Het containerregister moet zich in de Azure-resourcegroep staan die u eerder hebt opgegeven. Neem alleen de registernaam op, niet de volledige naam van de aanmeldingsserver. Zorg ervoor dat u **azurecr.io** naam weglaten. U vindt de registernaam op de [pagina Containerregisters](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) in Azure Portal.

**Gebruikersnaam van beheerder voor de Azure Container Registry:** geef de [gebruikersnaam](../container-registry/container-registry-authentication.md#admin-account)van de beheerder op die is gekoppeld aan de Azure Container Registry die uw containerafbeelding heeft. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. Als u de gebruikersnaam en  het wachtwoord van de beheerder wilt op halen, stelt u de eigenschap met beheerdersrechten in op Waar met **behulp** van de Azure Command-Line Interface (CLI). U kunt eventueel Gebruiker **met beheerdersrechten instellen** op **Inschakelen** in Azure Portal.

 :::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Illustreert het dialoogvenster Containerregister bijwerken.":::

**Wachtwoord voor de Azure Container Registry:** geef het wachtwoord op voor de gebruikersnaam van de beheerder die is gekoppeld aan de Azure Container Registry en de container-afbeelding heeft. De gebruikersnaam en het wachtwoord zijn vereist om ervoor te zorgen dat uw bedrijf toegang heeft tot het register. U kunt het wachtwoord ophalen uit de Azure Portal door naar Container Registry  >  **toegangssleutels** of met Azure CLI te gaan met behulp van [de opdracht show.](/cli/azure/acr/credential#az_acr_credential_show)

:::image type="content" source="./partner-center-portal/media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Illustreert het menu Toegangssleutel.":::

**De naam van de opslagplaats in Azure Container Registry**. Geef de naam op van de Azure Container Registry opslagplaats met uw afbeelding. Neem de naam van de opslagplaats op wanneer u de afbeelding naar het register pusht. U kunt de naam van de [](https://azure.microsoft.com/services/container-registry/)opslagplaats vinden door naar de pagina Container Registry  >  **opslagplaatsen te** gaan. Zie Opslagplaatsen voor [containerregisters](../container-registry/container-registry-repositories.md)weergeven in Azure Portal.

> [!NOTE]
> Nadat de naam is ingesteld, kan deze niet meer worden gewijzigd. Gebruik een unieke naam voor elke aanbieding in uw account.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Afbeeldingstags voor nieuwe versies van uw aanbieding

Klanten moeten automatisch updates kunnen ontvangen van de Azure Marketplace wanneer u een update publiceert. Als ze niet willen bijwerken, moeten ze een specifieke versie van uw afbeelding kunnen gebruiken. U kunt dit doen door nieuwe afbeeldingstags toe te voegen telkens wanneer u een update voor de afbeelding maakt.

### <a name="image-tag"></a>Afbeeldingstag

Dit veld moet een nieuwste tag **bevatten** die naar de nieuwste versie van uw afbeelding op alle ondersteunde platforms wijst. Het moet ook een versietag bevatten (bijvoorbeeld beginnend met xx.xx.xx, waarbij xx een getal is). Klanten moeten [manifesttags gebruiken om](https://github.com/estesp/manifest-tool) zich op meerdere platforms te richten. Alle tags waarnaar wordt verwezen door een manifesttag moeten ook worden toegevoegd, zodat we ze kunnen uploaden.

Alle manifesttags (met uitzondering van de meest recente tag) moeten beginnen met X.Y of **-** X.Y.Z, waarbij X, Y en Z gehele getallen zijn. Als een meest recente **tag** bijvoorbeeld wijst naar 1.0.1-linux-x64, 1.0.1-linux-arm32 en 1.0.1-windows-arm32, moeten deze zes tags aan dit veld worden toegevoegd. Zie Uw technische assets voor [Azure Container voorbereiden voor meer informatie.](create-azure-container-technical-assets.md)

> [!NOTE]
> Vergeet niet om een testtag toe te voegen aan uw afbeelding, zodat u de afbeelding tijdens het testen kunt identificeren.

## <a name="review-and-publish"></a>Beoordelen en publiceren

Nadat u alle vereiste secties van de aanbieding hebt voltooid, kunt u deze indienen om te controleren en te publiceren.

Selecteer controleren en publiceren in de rechterbovenhoek **van** de **portal.**

Op de controlepagina kunt u het volgende doen:

- Bekijk de voltooiingsstatus voor elke sectie van de aanbieding. U kunt pas publiceren als alle secties van de aanbieding als voltooid zijn gemarkeerd.
  - **Niet gestart:** is niet gestart en moet worden voltooid.
  - **Onvolledig:** bevat fouten die moeten worden opgelost of die vereisen dat u meer informatie verstrekt. Zie de secties eerder in dit document voor hulp.
  - **Volledig:** bevat alle vereiste gegevens zonder fouten. Alle secties van de aanbieding moeten zijn voltooid voordat u de aanbieding kunt indienen.
- Geef het certificeringsteam testinstructies om ervoor te zorgen dat uw aanbieding correct is getest. Geef ook eventuele aanvullende opmerkingen op die nuttig zijn om inzicht te krijgen in uw aanbieding.

Selecteer Publiceren om de aanbieding voor publicatie in **te dienen.**

We sturen u een e-mailbericht om u te laten weten wanneer een preview-versie van de aanbieding beschikbaar is om te controleren en goed te keuren.

Als u uw aanbieding wilt publiceren, gaat u naar Partner Center selecteert u **Go-live.**

## <a name="next-step"></a>Volgende stap

- [Een bestaande aanbieding bijwerken in de commerciële marketplace](./partner-center-portal/update-existing-offer.md)