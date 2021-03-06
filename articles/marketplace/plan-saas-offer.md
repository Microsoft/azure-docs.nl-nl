---
title: Een SaaS-aanbieding plannen voor de micro soft Commercial Marketplace
description: Het plannen van een nieuwe SaaS-aanbieding (Software as a Service) voor het aanbieden of verkopen in Microsoft AppSource, Azure Marketplace of via het programma voor de Cloud Solution Provider (CSP) met behulp van het commerciële Marketplace-programma in micro soft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 6f08fa0b2126112fa17fd61be6f44bb5cc6d5396
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552152"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>Een SaaS-aanbieding plannen voor de commerciële Marketplace

In dit artikel worden de verschillende opties en vereisten voor het publiceren van SaaS-aanbiedingen (Software as a Service) in de micro soft Commercial Marketplace beschreven. SaaS biedt u de mogelijkheid om software oplossingen te leveren aan uw klanten via online abonnementen. Als SaaS-Uitgever beheert en betaalt u voor de infra structuur die nodig is om het gebruik van uw klanten van uw aanbieding te ondersteunen. Dit artikel helpt u bij het voorbereiden van uw aanbieding voor het publiceren naar de commerciële Marketplace met het partner centrum.

## <a name="listing-options"></a>Aanbiedingsopties

Tijdens de voor bereiding voor het publiceren van een nieuwe SaaS-aanbieding, moet u _beslissen welke optie_ u wilt kiezen. De optie die u kiest, bepaalt welke aanvullende informatie u moet opgeven bij het maken van uw aanbieding in Partner Center. U definieert uw aanbiedings optie op de pagina  **aanbieding instellen** , zoals wordt uitgelegd in [een SaaS-aanbieding maken in de commerciële Marketplace](create-new-saas-offer.md).

De volgende tabel bevat de vermeldings opties voor SaaS-aanbiedingen in de commerciële Marketplace.

| Optie voor vermelding | Transactie proces |
| ------------ | ------------- |
| Contact opnemen | De klant neemt rechtstreeks contact op met de informatie in uw aanbieding.``*`` |
| Gratis proefversie | De klant wordt omgeleid naar de doel-URL via Azure Active Directory (Azure AD).``*`` |
| Nu downloaden (gratis) | De klant wordt omgeleid naar de doel-URL via Azure AD.``*`` |
| Verkopen via micro soft  | Aanbiedingen die via micro soft worden verkocht, worden _aanbiedingen genoemd._ Een aanbieding die kan worden transactable is een waarin micro soft de uitwisseling van geld voor een software licentie voor de uitgever vereenvoudigt. Er worden SaaS-aanbiedingen gefactureerd op basis van het prijs model dat u kiest, en u kunt klant transacties namens u beheren. Gebruiks kosten voor Azure-infra structuur worden direct aan u, de partner, in rekening gebracht. U moet rekening met de kosten van de infra structuur in uw prijs model. Dit wordt gedetailleerd beschreven in de onderstaande [SaaS-facturering](#saas-billing) .  |
|||

``*`` Uitgevers zijn verantwoordelijk voor de ondersteuning van alle aspecten van de software licentie transacties, inclusief, maar niet beperkt tot order, verwerking, meting, facturering, facturering, betaling en incasso.

Zie voor meer informatie over deze aanbiedings opties [commerciële Marketplace Transact-mogelijkheden](marketplace-commercial-transaction-capabilities-and-considerations.md).

Nadat uw aanbieding is gepubliceerd, wordt de optie voor de aanbieding die u voor uw aanbieding hebt gekozen, weer gegeven als een knop in de linkerbovenhoek van de aanbiedings pagina van uw aanbieding. Met de volgende scherm afbeelding ziet u bijvoorbeeld een pagina met een aanbieding in azure Marketplace met de knop **nu downloaden** .

![Illustreert een aanbiedings vermelding in de online winkel.](./media/saas/listing-options-saas.png)

## <a name="technical-requirements"></a>Technische vereisten

De technische vereisten variëren, afhankelijk van de optie die u voor uw aanbieding kiest.

De optie _contact opnemen met_ een aanbieding heeft geen technische vereisten. U hebt de mogelijkheid om verbinding te maken met een CRM-systeem (Customer Relationship Management) om leads van klanten te beheren. Dit wordt beschreven in de sectie [leads van klanten](#customer-leads) , verderop in dit artikel.

De aanbiedings opties gratis _downloaden_, _gratis proef versie_ en _verkopen via micro soft_ bieden de volgende technische vereisten:

- Uw SaaS-toepassing moet een multi tenant oplossing zijn.
- U kunt zowel micro soft-accounts (MSA) als [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) inschakelen voor het verifiëren van gebruikers.
- U moet een landings pagina maken. Nadat een gebruiker uw aanbieding heeft gekocht, wordt deze omgeleid naar de landings pagina. Op deze manier kunt u aanvullende inrichtingen of installatie vereisten volt ooien. Zie de volgende artikelen voor meer informatie over het maken van de landings pagina:
  - [Bouw de landings pagina voor uw voor transactable SaaS-aanbieding in de commerciële Marketplace](azure-ad-transactable-saas-landing-page.md)
  - [Bouw de landings pagina voor uw gratis of proef SaaS-aanbieding in de commerciële Marketplace](azure-ad-free-or-trial-landing-page.md)

Deze aanvullende technische vereisten zijn alleen van toepassing op de aanbiedings optie _micro soft_ (alleen voor trans acties):

- Azure AD met eenmalige aanmelding (SSO) en verificatie is vereist voor de kopende gebruiker die toegang krijgt tot de landings pagina. Zie [Azure AD en transactable SaaS-aanbiedingen in de commerciële Marketplace](azure-ad-saas.md)voor gedetailleerde richt lijnen.
- U moet de [SaaS-fulfillment-api's](./partner-center-portal/pc-saas-fulfillment-api-v2.md) gebruiken om te integreren met Azure Marketplace en Microsoft AppSource. U moet een service weer geven die kan communiceren met het SaaS-abonnement om een gebruikers account en service plan te maken, bij te werken en te verwijderen. Essentiële wijzigingen in de API moeten binnen 24 uur worden ondersteund. Wijzigingen van niet-kritieke API'S worden periodiek vrijgegeven. Diagrammen en gedetailleerde uitleg die het gebruik van de verzamelde velden beschrijven, zijn beschikbaar in de documentatie voor de [api's](./partner-center-portal/pc-saas-fulfillment-api-v2.md).
- U moet ten minste één abonnement maken voor uw aanbieding. De prijs van uw abonnement is gebaseerd op het prijs model dat u selecteert voordat u publiceert: _vast tarief_ of _per gebruiker_. Verderop in dit artikel vindt u meer informatie over [plannen](#plans) .
- De klant kan uw aanbieding op elk gewenst moment annuleren.

### <a name="technical-information"></a>Technische informatie

Als u een voor bereide aanbieding maakt, moet u de volgende informatie verzamelen voor de pagina **technische configuratie** . Als u trans acties onafhankelijk wilt verwerken in plaats van een transactable-aanbieding te maken, kunt u deze sectie overs Laan en naar [test stations](#test-drives)gaan.

- **URL van de landings pagina**: de URL van de SaaS-site (bijvoorbeeld: `https://contoso.com/signup` ) waarmee gebruikers na het verkrijgen van uw aanbieding van de commerciële Marketplace worden omgeleid naar het configuratie proces van het zojuist gemaakte SaaS-abonnement. Deze URL ontvangt een token dat kan worden gebruikt om de fulfillment-Api's aan te roepen om de inrichtings gegevens voor uw interactieve registratie pagina op te halen.

  Deze URL wordt aangeroepen met de para meter voor het kopen van id-tokens voor Marketplace waarmee de SaaS-aankoop van de specifieke klant uniek wordt geïdentificeerd. U moet dit token omruilen voor de bijbehorende details van het SaaS-abonnement met behulp van de [API voor omzetten](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Deze details en andere personen die u wilt verzamelen als onderdeel van een klant-interactieve webpagina kunnen worden gebruikt om de onboarding van de klant te starten, die uiteindelijk moet worden afgesloten met een activerings oproep voor de API voor het starten van de abonnements periode. Op deze pagina moet de gebruiker zich aanmelden met één klik op verificatie met behulp van Azure Active Directory (Azure AD).

  Deze URL met Marketplace-aankoop identificatie token para meter wordt ook aangeroepen wanneer de klant een beheerde SaaS-ervaring start vanuit het Azure Portal of Microsoft 365 beheer centrum. U moet beide stromen afhandelen: wanneer het token voor de eerste keer wordt ingesteld na een nieuwe klant, en wanneer deze opnieuw wordt ingevoerd voor een bestaande klant die de SaaS-oplossing beheert.

    De landings pagina die u configureert, moet 24/7 zijn. Dit is de enige manier waarop u wordt geïnformeerd over nieuwe aankopen van uw SaaS-aanbiedingen die zijn gemaakt in de commerciële Marketplace of configuratie aanvragen voor een actief abonnement op een aanbieding.

- **Verbindings-webhook**: voor alle asynchrone gebeurtenissen die micro soft naar u moet sturen (bijvoorbeeld wanneer een SaaS-abonnement is geannuleerd), moet u een URL voor de verbindings-webhook opgeven. Deze URL wordt gebeld om u op de hoogte te stellen van de gebeurtenis.

  De webhook die u opgeeft, moet 24/7 zijn. Dit is de enige manier waarop u wordt geïnformeerd over updates over de SaaS-abonnementen van uw klanten die zijn aangeschaft via de commerciële Marketplace.

  > [!NOTE]
  > In het Azure Portal moet u een app-registratie voor één Tenant [Azure Active Directory (Azure AD)](../active-directory/develop/howto-create-service-principal-portal.md)maken. Gebruik de gegevens van de app-registratie om uw oplossing te verifiëren bij het aanroepen van Marketplace-Api's. Als u de [Tenant-id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)wilt vinden, gaat u naar uw Azure Active Directory en selecteert u **Eigenschappen**. vervolgens zoekt u naar de map-id die wordt weer gegeven. Bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e`.

- **Azure Active Directory Tenant-id**: (ook wel bekend als Directory-id). In het Azure Portal moet u [een Azure Active Directory (AD)-app registreren](../active-directory/develop/howto-create-service-principal-portal.md) , zodat we deze kunnen toevoegen aan de toegangs beheer lijst (ACL) van de API om ervoor te zorgen dat u gemachtigd bent om deze aan te roepen. Ga naar de Blade [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in azure Active Directory om de Tenant-id voor uw Azure Active Directory-app (AD) te vinden. Selecteer de app in de kolom **weergave naam** . Zoek vervolgens naar de **map (Tenant) ID** die wordt weer gegeven (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory toepassings-id**: u hebt ook uw [toepassings-id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)nodig. Als u de waarde wilt ophalen, gaat u naar de Blade [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in azure Active Directory. Selecteer de app in de kolom **weergave naam** . Zoek vervolgens naar het ID-nummer van de toepassing (client) in de lijst (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ).

  De Azure AD-toepassings-ID is gekoppeld aan uw uitgevers-ID in uw partner centrum-account. U moet dezelfde toepassings-ID gebruiken voor alle aanbiedingen in dat account.

  > [!NOTE]
  > Als de uitgever twee of meer verschillende accounts in het partner centrum heeft, kunnen de registratie gegevens van de Azure AD-App alleen in één account worden gebruikt. Met dezelfde Tenant-ID wordt het combi neren van de App-ID voor een aanbieding onder een ander uitgevers account niet ondersteund.

## <a name="test-drives"></a>Test drives
U kunt ervoor kiezen om een test drive in te scha kelen voor uw SaaS-app. Test stations geven klanten een vast aantal uur toegang tot een vooraf geconfigureerde omgeving. U kunt test stations voor elke publicatie optie inschakelen, maar deze functie heeft aanvullende vereisten. Zie [Wat is een test drive?](what-is-test-drive.md)voor meer informatie over test stations. Zie [technische configuratie testen](test-drive-technical-configuration.md)voor meer informatie over het configureren van verschillende soorten test stations.

> [!TIP]
> Een test drive wijkt af van een [gratis proef versie](plans-pricing.md#free-trials). U kunt een test drive, een gratis proef versie of beide aanbieden. Ze bieden uw klanten uw oplossing voor een vaste periode. Een test drive bevat echter ook een zelf doorgeleide rond leiding door de belangrijkste functies en voor delen van uw product die worden getoond in een scenario met een praktijk implementatie.

## <a name="customer-leads"></a>Leads van klanten

U moet uw aanbieding verbinden met het CRM-systeem (Customer Relationship Management) om klant gegevens te verzamelen. De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, de ID en de online winkel waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. De commerciële Marketplace ondersteunt een groot aantal CRM-systemen, samen met de optie voor het gebruik van een Azure-tabel of het configureren van een HTTPS-eind punt met behulp van energie automatisering.

U kunt op elk gewenst moment een CRM-verbinding toevoegen of wijzigen tijdens of na het maken van de aanbieding. Zie [leads van klanten van uw aanbieding voor commerciële Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md)voor gedetailleerde richt lijnen.

## <a name="selecting-an-online-store"></a>Een online winkel selecteren

Wanneer u een SaaS-aanbieding publiceert, wordt deze weer gegeven in Microsoft AppSource, Azure Marketplace of beide. Elke online winkel dient unieke klant vereisten. AppSource is voor bedrijfs oplossingen en Azure Marketplace is voor IT-oplossingen. Met het type aanbieding, de Transact-functionaliteit en de categorieën kunt u bepalen waar uw aanbieding wordt gepubliceerd. Categorieën en subcategorieën worden toegewezen aan elke online winkel op basis van het oplossings type. 

Als uw SaaS-aanbieding *zowel* een IT-oplossing (Azure Marketplace) als een bedrijfs oplossing (AppSource) is, selecteert u een categorie en een subcategorie die van toepassing is op elke online winkel. Aanbiedingen die naar beide online winkels worden gepubliceerd, moeten een waarde hebben die is opgenomen als een IT-oplossing *en* een bedrijfs oplossing.

> [!IMPORTANT]
> SaaS-aanbiedingen met [facturering via data limiet](partner-center-portal/saas-metered-billing.md) zijn beschikbaar via Azure Marketplace en de Azure Portal. SaaS-aanbiedingen met alleen persoonlijke abonnementen zijn beschikbaar via de Azure Portal.

| Factuur met data limiet | Openbaar abonnement | Persoonlijk abonnement | Beschikbaar in: |
|---|---|---|---|
| Ja             | Ja         | Nee           | Azure Marketplace en Azure Portal |
| Ja             | Ja         | Ja          | Azure Marketplace en Azure Portal * |
| Ja             | Nee          | Ja          | Alleen Azure Portal |
| Nee              | Nee          | Ja          | Alleen Azure Portal |
|||||

&#42; het privé-abonnement van de aanbieding is alleen beschikbaar via de Azure Portal

Zo wordt een aanbieding met een facturerings regeling en een privé-abonnement alleen (geen openbaar abonnement) gekocht door klanten in de Azure Portal. Meer informatie over [privé aanbiedingen in micro soft Commercial Marketplace](private-offers.md).

Zie voor gedetailleerde informatie over de aanbiedings opties die worden ondersteund door online winkels de [Opties aanbieding en prijs per online winkel](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). Zie voor meer informatie over categorieën en subcategorieën [Categorieën en subcategorieën in de commerciële Marketplace](categories.md).

## <a name="legal-contracts"></a>Juridische contracten

Micro soft biedt een standaard contract dat u voor uw aanbiedingen kunt gebruiken in de commerciële Marketplace om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. Wanneer u uw software onder het Standard-contract aanbiedt, hoeven klanten slechts één keer te lezen en te accepteren en hoeft u geen aangepaste voor waarden te maken.

Als u kiest voor het gebruik van het standaard contract, hebt u de mogelijkheid om universele wijzigings voorwaarden toe te voegen en Maxi maal 10 aangepaste wijzigingen aan te brengen in het standaard contract. U kunt ook uw eigen voor waarden gebruiken in plaats van het standaard contract. U gaat deze details beheren op de pagina **Eigenschappen** . Zie voor gedetailleerde informatie het [standaard contract voor micro soft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor de commerciële Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. Het is een ' or '-scenario. U kunt uw oplossing aanbieden onder het standaard contract of uw eigen voor waarden. Als u de voor waarden van het standaard contract wilt wijzigen, kunt u dit doen via de standaard wijzigingen in contracten.


## <a name="microsoft-365-integration"></a>Integratie van Microsoft 365

Dankzij integratie met Microsoft 365 kan uw SaaS-aanbod verbonden ervaring bieden voor meerdere Microsoft 365 app-Opper vlakken via gerelateerde gratis invoeg toepassingen, zoals teams-apps, Office-invoeg toepassingen en share point-Framework oplossingen. U kunt uw klanten helpen om eenvoudig alle facetten van uw E2E-oplossing (web service + gerelateerde invoeg toepassingen) te detecteren en ze binnen één proces te implementeren door de volgende informatie op te geven. 
  - Als uw SaaS-aanbieding is geïntegreerd met Microsoft Graph, geeft u de ID van de Azure Active Directory (AAD) op die wordt gebruikt door uw SaaS-aanbieding voor de integratie. Beheerders kunnen toegangs machtigingen controleren die vereist zijn voor de juiste werking van uw SaaS-aanbieding zoals deze is ingesteld op de AAD-App-ID en toegang verlenen als er een geavanceerde beheerders rechten nodig zijn tijdens de implementatie. 
    
     Als u ervoor kiest om uw aanbieding via micro soft te verkopen, is dit dezelfde AAD-App-ID die u hebt geregistreerd voor gebruik op uw landings pagina om basis gebruikers gegevens op te halen die nodig zijn om de activering van het klanten abonnement te volt ooien. Zie voor gedetailleerde richt lijnen [de landings pagina voor uw voor transactable SaaS-aanbieding bouwen in de commerciële Marketplace](azure-ad-transactable-saas-landing-page.md). 
    
   -    Geef een lijst met verwante invoeg toepassingen op die samen werken met uw SaaS-aanbieding die u wilt koppelen. Klanten kunnen uw E2E-oplossing vinden op AppSource en beheerders kunnen zowel de SaaS als alle gerelateerde invoeg toepassingen implementeren die u in hetzelfde proces hebt gekoppeld via Microsoft 365 beheer centrum.
    
        Als u gerelateerde invoeg toepassingen wilt koppelen, moet u de AppSource-koppeling van de invoeg toepassing opgeven. Dit betekent dat de invoeg toepassing eerst moet worden gepubliceerd op AppSource. Ondersteunde typen invoeg toepassingen zijn: teams-apps, Office-invoeg toepassingen en share point Framework (SPFx)-oplossingen. Elke gekoppelde invoeg toepassing moet uniek zijn voor een SaaS-aanbieding. 

Voor gekoppelde producten wordt met een zoek opdracht op AppSource een resultaat geretourneerd dat zowel SaaS als alle gekoppelde invoeg toepassingen bevat. De klant kan navigeren tussen de product detail pagina's van de SaaS-aanbieding en gekoppelde invoeg toepassingen. IT-beheerders kunnen zowel de SaaS-als gekoppelde invoeg toepassingen binnen hetzelfde proces bekijken en implementeren via een geïntegreerde en verbonden ervaring binnen het Microsoft 365-beheer centrum. Zie voor meer informatie [Microsoft 365-Apps testen en implementeren-Microsoft 365-beheerder](/microsoft-365/admin/manage/test-and-deploy-microsoft-365-apps).

### <a name="microsoft-365-integration-support-limitations"></a>Beperkingen voor de ondersteuning van Microsoft 365-integratie
Detectie als één E2E-oplossing wordt voor alle gevallen ondersteund op AppSource. een vereenvoudigde implementatie van de E2E-oplossing zoals hierboven beschreven in het Microsoft 365-beheer centrum wordt echter niet ondersteund voor de volgende scenario's:

   - Dezelfde invoeg toepassing is gekoppeld aan meer dan één SaaS-aanbieding.
   - De SaaS-aanbieding is gekoppeld aan invoeg toepassingen, maar is niet geïntegreerd met Microsoft Graph en er is geen AAD-App-ID beschikbaar.
  - De SaaS-aanbieding is gekoppeld aan invoeg toepassingen, maar de AAD-App-ID die is geleverd voor Microsoft Graph integratie wordt gedeeld in meerdere SaaS-aanbiedingen.

 
## <a name="offer-listing-details"></a>Details aanbiedings vermelding

Wanneer u [een nieuwe SaaS-aanbieding](create-new-saas-offer.md) in het partner centrum maakt, voert u tekst, afbeeldingen, optionele Video's en andere informatie in op **de pagina aanbieding.** Dit is de informatie die klanten te zien krijgen wanneer ze uw aanbieding in de commerciële Marketplace detecteren, zoals wordt weer gegeven in het volgende voor beeld.

:::image type="content" source="./media/saas/example-saas-1.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in Microsoft AppSource.":::

**Beschrijvingen van aanroepen**

1. Logo
2. Categorieën
3. Branches
4. Ondersteunings adres (koppeling)
5. Gebruiksvoorwaarden
6. Privacybeleid
7. Naam van aanbieding
8. Samenvatting
9. Beschrijving
10. Scherm afbeeldingen/Video's
11. Documenten

In het volgende voor beeld wordt een aanbieding weer gegeven in de Azure Portal.

![Illustreert een aanbiedings vermelding in de Azure Portal.](./media/example-managed-service-azure-portal.png)

**Beschrijvingen aanroepen**

1. Titel
1. Beschrijving
1. Handige koppelingen
1. Schermopnamen

> [!NOTE]
> De inhoud van het aanbiedings aanbod hoeft niet te worden weer gegeven in het Engels als de beschrijving van de aanbieding begint met de zin ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '.

Om uw aanbieding gemakkelijker te maken, moet u enkele van deze items vooraf voorbereiden. De volgende items zijn vereist tenzij anders vermeld.

- **Naam**: deze naam wordt weer gegeven als de titel van uw aanbieding in de commerciële Marketplace. De naam kan worden aangemerkt. Het mag geen emojis bevatten (tenzij ze de symbolen van het handels merk en copyright zijn) en moet beperkt zijn tot 50 tekens.
- **Samen vatting van zoek resultaten**: Beschrijf het doel of de functie van uw aanbieding als één zin zonder regel einden van 100 tekens of minder. Deze samen vatting wordt gebruikt in de zoek resultaten voor commerciële Marketplace-aanbiedingen.
- **Beschrijving**: deze beschrijving wordt weer gegeven in het overzicht van commerciële Marketplace-lijst (en). Overweeg het opnemen van een toegevoegde waarde, de belangrijkste voor delen, de beoogde gebruikers basis, alle categorieën of branche koppelingen, verkoop kansen in de app, vereiste informatie en een koppeling voor meer informatie.

    Dit tekstvak bevat besturings elementen voor de RTF-editor die u kunt gebruiken om uw beschrijving effectiever te maken. U kunt ook HTML-tags gebruiken om uw beschrijving op te maken. U kunt tot 3.000 tekens tekst in dit vak invoeren, inclusief HTML-opmaak codes. Zie [een fantastische app-beschrijving schrijven](/windows/uwp/publish/write-a-great-app-description)voor aanvullende tips.

- **Aan** de slag-instructies: als u ervoor kiest om uw aanbieding te verkopen via micro soft (voor de transactable-aanbieding), is dit veld verplicht. Deze instructies helpen klanten om verbinding te maken met uw SaaS-aanbieding. U kunt Maxi maal 3.000 tekens tekst en koppelingen naar meer gedetailleerde online documentatie toevoegen.
- **Zoek woorden** (optioneel): Geef Maxi maal drie Zoek trefwoorden op die klanten kunnen gebruiken om uw aanbieding in de online winkels te vinden. U hoeft de **naam** en **Beschrijving** van de aanbieding niet op te nemen: de tekst wordt automatisch opgenomen in de zoek opdracht.
- **Koppeling Privacybeleid**: de URL voor het privacybeleid van uw bedrijf. U moet een geldig privacybeleid opgeven en zijn verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy.
- **Contact gegevens**: u moet de volgende contact personen in uw organisatie opgeven:
  - **Contact opnemen met ondersteuning**: Geef de naam, het telefoon nummer en het e-mail adres op voor micro soft-partners die u kunt gebruiken wanneer uw klanten tickets openen. U moet ook de URL voor uw ondersteunings website toevoegen.
  - **Technische contact persoon**: Geef de naam, het telefoon nummer en het e-mail adres op die door micro soft rechtstreeks kunnen worden gebruikt wanneer er problemen zijn met uw aanbieding. Deze contact gegevens worden niet vermeld in de commerciële Marketplace.
  - **CSP-programma contact persoon** (optioneel): Geef de naam, het telefoon nummer en het e-mail adres op als u zich aanmeldt bij het CSP-programma, zodat deze partners contact met u kunnen opnemen om vragen te beantwoorden. U kunt ook een URL naar uw marketing materiaal toevoegen.
- **Nuttige koppelingen** (optioneel): u kunt koppelingen naar verschillende resources opgeven voor gebruikers van uw aanbieding. Bijvoorbeeld forums, veelgestelde vragen en opmerkingen bij de release.
- **Ondersteunende documenten**: u kunt Maxi maal drie klant gerichte documenten opgeven, zoals witboeken, brochures, controle lijsten of Power Point-presentaties.
- **Media – logo's**: Geef een PNG-bestand op voor het **grote** logo. Het partner centrum gebruikt deze om een **klein** en **gemiddeld** logo te maken. U kunt deze desgewenst later vervangen door andere installatie kopieën.

   - Groot (van 216 x 216 tot 350 x 350 px, vereist)
   - Gemiddeld (90 x 90 px, optioneel)
   - Klein (48 x 48 px, optioneel)

  Deze logo's worden op verschillende plaatsen in de online winkels gebruikt:

  - Het kleine logo wordt weer gegeven in de zoek resultaten voor Azure Marketplace en op de pagina Microsoft AppSource hoofd pagina en zoek resultaten.
  - Het logo gemiddeld wordt weer gegeven wanneer u een nieuwe resource maakt in Microsoft Azure.
  - Het grote logo wordt weer gegeven op de aanbiedings pagina van uw aanbieding in azure Marketplace en Microsoft AppSource.

- **Media-scherm afbeeldingen**: u moet ten minste één en Maxi maal vijf scherm opnamen toevoegen met de volgende vereisten, die laten zien hoe uw aanbieding werkt:
  - 1280 x 720 pixels
  - PNG-bestand
  - Moet een bijschrift bevatten
- **Media-Video's** (optioneel): u kunt Maxi maal vier Video's toevoegen aan de volgende vereisten, die uw aanbod demonstreren:
  - Name
  - URL: moet alleen worden gehost op YouTube of Vimeo.
  - Miniatuur: 1280 x 720. png-bestand

> [!Note]
> Uw aanbieding moet voldoen aan het algemene [certificerings beleid voor commerciële Marketplace](/legal/marketplace/certification-policies#100-general) en de [software als een service beleid](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) dat moet worden gepubliceerd op de commerciële Marketplace.

> [!NOTE]
> Een preview-doel groep wijkt af van een privé-abonnement. Een privé-abonnement is één die u alleen beschikbaar maakt voor een specifieke doel groep. Zo kunt u een aangepast plan met specifieke klanten onderhandelen. Zie de volgende sectie: plannen voor meer informatie.

U kunt uitnodigingen verzenden naar e-mail adressen van micro soft-accounts (MSA) of Azure Active Directory (Azure AD). Voeg Maxi maal 10 e-mail adressen hand matig toe of importeer Maxi maal 20 met een CSV-bestand. Als uw aanbieding al Live is, kunt u nog steeds een preview-doel groep definiëren om eventuele wijzigingen of updates naar uw aanbieding te testen.

## <a name="plans"></a>Abonnementen

Voor aanbiedingen die moeten worden gepland, is ten minste één abonnement vereist. Een plan definieert het bereik en de limieten van de oplossing en de bijbehorende prijzen. U kunt meerdere plannen maken voor uw aanbieding om uw klanten verschillende technische en prijs opties te geven. Als u trans acties onafhankelijk wilt verwerken in plaats van een transactable-aanbieding te maken, is de pagina **plannen** niet zichtbaar. Als dit het geval is, slaat u deze sectie over en gaat u naar [extra verkoop kansen](#additional-sales-opportunities).

Bekijk [plannen en prijzen voor commerciële Marketplace-aanbiedingen](plans-pricing.md) voor algemene richt lijnen voor abonnementen, waaronder prijs modellen, gratis proef versies en privé abonnementen. In de volgende secties wordt beschreven welke aanvullende informatie specifiek is voor SaaS-aanbiedingen.

### <a name="saas-pricing-models"></a>Prijsmodellen van SaaS

SaaS-aanbiedingen kunnen gebruikmaken van een van de twee prijs modellen voor elk abonnement: een _vast tarief_ of _per gebruiker_. Alle abonnementen in dezelfde aanbieding moeten aan hetzelfde prijs model zijn gekoppeld. Een aanbieding kan bijvoorbeeld niet één abonnement hebben dat een vast tempo heeft en een ander abonnement dat per gebruiker is.

**Vast** : Hiermee kunt u toegang tot uw aanbieding bieden met één of een vaste prijs per maand of per jaar. Dit wordt soms ook wel site-gebaseerde prijzen genoemd. Met dit prijs model kunt u optioneel plannen voor data limieten definiëren die gebruikmaken van de API voor de Marketplace-meter service om klanten in rekening te brengen voor gebruik dat niet wordt gedekt door het vaste tarief. Zie voor meer informatie over gefactureerde facturering [voor SaaS met de commerciële Marketplace-meet service](./partner-center-portal/saas-metered-billing.md). U moet deze optie ook gebruiken als het gebruiks gedrag voor uw SaaS-service in bursts is.

**Per gebruiker** : Schakel toegang tot uw aanbieding in met een prijs op basis van het aantal gebruikers dat toegang heeft tot de aanbieding of stoelen kan innemen. Met dit op gebruikers gebaseerd model kunt u het minimale en maximale aantal gebruikers instellen dat door het plan wordt ondersteund. U kunt meerdere plannen maken om verschillende prijs punten te configureren op basis van het aantal gebruikers. Deze velden zijn optioneel. Als deze optie niet is geselecteerd, wordt het aantal gebruikers geïnterpreteerd als een limiet van Mini maal 1 en Maxi maal zo lang uw service kan ondersteunen. Deze velden kunnen worden bewerkt als onderdeel van een update voor uw abonnement.

> [!IMPORTANT]
> Nadat uw aanbieding is gepubliceerd, kunt u het prijs model niet meer wijzigen. Daarnaast moeten alle abonnementen voor hetzelfde aanbod hetzelfde prijs model delen.

### <a name="saas-billing"></a>SaaS-facturering

Voor SaaS-apps die worden uitgevoerd in het Azure-abonnement van uw (de uitgever), wordt het gebruik van de infra structuur rechtstreeks in rekening gebracht. klanten zien geen werkelijke gebruiks kosten voor de infra structuur. U moet de gebruiks kosten voor Azure-infra structuur bundelen in uw prijzen voor software licenties om de kosten te compenseren van de infra structuur die u hebt geïmplementeerd om de oplossing uit te voeren.

SaaS-app-aanbiedingen die worden verkocht via micro soft ondersteuning maandelijks of jaarlijks worden gefactureerd op basis van een vast bedrag, per gebruiker of verbruiks kosten met behulp van de [service voor facturering via data limiet](./partner-center-portal/saas-metered-billing.md). De commerciële Marketplace werkt op een agentuur model, waardoor uitgevers prijzen instellen, micro soft billt klanten en micro soft de inkomsten van uitgevers betaalt bij het vastleggen van een tarief van een organisatie.

In het volgende voor beeld ziet u een voor beeld van een uitsplitsing van kosten en uitbetalingen om het agentuur model te demonstreren. In dit voor beeld wordt micro soft billt $100,00 aan de klant voor uw software licentie en betaalt $80,00 de uitgever.

| De licentie kosten | $100 per maand |
| ------------ | ------------- |
| Kosten voor Azure-gebruik (D1/1-core) | Rechtstreeks aan de uitgever gefactureerd, niet de klant |
| Klant wordt gefactureerd door micro soft | $100,00 per maand (uitgever moet rekening worden gehouden met de kosten voor het ontstaan of door geven van infra structuur in de licentie kosten) |
| **Micro soft-facturen** | **$100 per maand** |
| Micro soft betaalt u 80% van uw licentie kosten<br>`*` Voor gekwalificeerde SaaS-apps betaalt micro soft 90% van uw licentie kosten| $80,00 per maand<br>``*`` $90,00 per maand |
|||

**`*` Lagere kosten voor Marketplace-service** : voor bepaalde SaaS-aanbiedingen die u hebt gepubliceerd op de commerciële Marketplace, verlaagt micro soft de kosten voor Marketplace-service van 20% (zoals beschreven in de overeenkomst voor micro soft Publisher) tot 10%. Voor uw aanbieding (en) die u wilt kwalificeren, moeten uw aanbieding (en) zijn aangewezen door micro soft als Azure IP-gemotiveerd. Voor het einde van elke kalender maand moet aan de geschiktheid ten minste vijf (5) werk dagen worden voldaan om de lagere kosten voor Marketplace-service te ontvangen. Zodra aan de voor waarden is voldaan, worden de gereduceerde service kosten in rekening gebracht voor alle trans acties met ingang van de eerste dag van de volgende maand en blijven ze worden toegepast totdat de gemotiveerd-status van Azure IP co-sell gaat verloren. Zie [vereisten voor co-sell-status](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)voor meer informatie over de geschiktheid voor het verkopen van IP-adressen. De gereduceerde service kosten voor Marketplace zijn ook van toepassing op Azure IP-gemotiveerd Vm's, beheerde apps en alle andere gekwalificeerd transactable-IaaS die beschikbaar worden gesteld via de commerciële Marketplace.

## <a name="preview-audience"></a>Voor beeld van doel groep

Een preview-doel groep kan toegang krijgen tot uw aanbieding voordat u deze Live in de online winkels publiceert. Ze kunnen zien hoe uw aanbieding eruitziet in de commerciële Marketplace en de end-to-end-functionaliteit testen voordat u deze live publiceert. 

Op de pagina **voor beeld van doel groep** kunt u een beperkte preview-doel groep definiëren. Deze instelling is niet beschikbaar als u trans acties afzonderlijk wilt verwerken in plaats van uw aanbieding via micro soft te verkopen. Als dit het geval is, kunt u deze sectie overs Laan en naar [extra verkoop kansen](#additional-sales-opportunities)gaan.

## <a name="test-offer"></a>Test aanbieding

Voordat u uw aanbieding Live publiceert, moet u de Preview-functionaliteit gebruiken om uw technische implementatie te ontwikkelen, te testen en te experimenteren met verschillende prijs modellen.

Voor het ontwikkelen en testen van uw SaaS-aanbieding met de laagst mogelijke risico, raden we u aan om een test-en ontwikkelings aanbod (DEV) te maken voor experimenteren en testen. De ontwikkel aanbieding is gescheiden van uw productie-aanbieding.

Als u onbedoelde aankopen van de ONTWIKKELINGs aanbieding wilt voor komen, duwt u nooit de knop **Go Live** om de ontwikkel aanbieding Live te publiceren.

![Illustreert de pagina overzicht van aanbieding voor een aanbieding in partner centrum. De knop Go Live en de preview-koppelingen worden weer gegeven. De koppeling weergave validatie rapport wordt ook weer gegeven onder Automatische validatie.](./media/review-publish-offer/publish-status-saas.png)

Hier volgen enkele redenen voor het maken van een afzonderlijke ontwikkel aanbieding voor het ontwikkelings team dat moet worden gebruikt voor het ontwikkelen en testen van de productie-aanbod:

- Voorkom onbedoelde klant kosten
- Prijs modellen evalueren
- Geen plannen toevoegen die geen werkelijke klanten bereiken

### <a name="avoid-accidental-customer-charges"></a>Voorkom onbedoelde klant kosten

Door gebruik te maken van een ontwikkel aanbod in plaats van de productie-aanbieding en te behandelen als ontwikkel-en productie omgeving, kunt u onbedoelde kosten voor klanten vermijden.

We raden u aan om twee verschillende Azure AD-apps te registreren om de Marketplace-Api's aan te roepen. Ontwikkel aars gebruiken een Azure AD-app met de instellingen van de ONTWIKKELINGs aanbieding en het operations-team gebruikt de registratie van de app voor de productie. Op deze manier kunt u het ontwikkelings team isoleren om per ongeluk fouten te maken, zoals het aanroepen van de API voor het annuleren van het abonnement van een klant die $100.000 per maand betaalt. U kunt ook voor komen dat een klant wordt geloosd voor gebruik in de data limiet die ze niet hebben gebruikt.

### <a name="evaluate-pricing-models"></a>Prijs modellen evalueren

Het testen van prijs modellen in de ontwikkel aanbieding vermindert het risico wanneer ontwikkel aars experimenteren met verschillende prijs modellen.

Uitgevers kunnen de plannen maken die ze nodig hebben in de ONTWIKKELINGs aanbieding om te bepalen welk prijs model het meest geschikt is voor hun aanbieding. Ontwikkel aars willen mogelijk meerdere plannen maken in de ONTWIKKELINGs aanbieding om verschillende prijs combinaties te testen. U kunt bijvoorbeeld plannen maken met verschillende sets aangepaste dimensies met meting. U kunt een ander plan maken met een combi natie van een vlakke prijs en aangepaste dimensies met meting.

Als u meerdere prijs opties wilt testen, moet u een plan maken voor elk uniek prijs model. Zie [abonnementen](#plans)voor meer informatie.

### <a name="not-adding-plans-that-do-not-target-actual-customers"></a>Geen plannen toevoegen die geen werkelijke klanten bereiken

Door gebruik te maken van een ontwikkel aanbod voor ontwikkeling en testen, kunt u de overbodige wirwar van items in de productie-aanbieding verminderen. U kunt bijvoorbeeld geen plannen verwijderen die u maakt om verschillende prijs modellen of technische configuraties te testen (zonder een ondersteunings ticket in te dienen). Door plannen te maken voor het testen in de ontwikkel aanbieding, vermindert u de wirwar van de productie-aanbieding.

Wirwar van de productie-aanbod is het product en het marketing team van het bedrijf, aangezien ze alle plannen verwachten van het doel van de daad werkelijke klanten. Met name bij grote teams die niet zijn verbonden en die alle verschillende sandboxs willen gebruiken, bieden twee aanbiedingen twee verschillende omgevingen voor DEV en PROD. In sommige gevallen wilt u mogelijk meerdere ontwikkel aanbiedingen maken ter ondersteuning van een groter team dat verschillende personen heeft die verschillende test scenario's uitvoeren. Het is de bedoeling dat verschillende team leden in de ontwikkel aanbieding gescheiden van de productie-aanbieding werken, zodat productie plannen zo dicht mogelijk bij de productie kunnen worden bewaard.

Als u een ontwikkel aanbod test, kunt u voor komen dat de 30 aangepaste limiet voor meet dimensies per aanbieding. Ontwikkel aars kunnen verschillende combi Naties van maat eenheden uitproberen in de ONTWIKKELINGs aanbieding zonder dat dit van invloed is op de aangepaste limiet voor het meten van limieten in de productie aanbieding.

## <a name="additional-sales-opportunities"></a>Aanvullende verkoop kansen

U kunt kiezen voor marketing-en verkoop kanalen die door micro soft worden ondersteund. Wanneer u uw aanbieding in Partner Center maakt, ziet u twee tabbladen naar het einde van het proces:

- **Door sturen via csp's**: gebruik deze optie om de partners van Microsoft Cloud Solution Providers (CSP) in staat te stellen uw oplossing door te verkopen als onderdeel van een gebundelde aanbieding. Zie het [Cloud Solution Provider-programma](cloud-solution-providers.md)voor meer informatie over dit programma.

- **Samen verkopen met micro soft: met** deze optie kunnen micro soft verkoop teams uw door de klant in aanmerking komende oplossing door nemen bij de evaluatie van de behoeften van uw klanten. Zie [vereisten voor co-sell status](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)voor meer informatie over de geschiktheid voor co-sell. Voor gedetailleerde informatie over het voorbereiden van uw aanbieding voor evaluatie, Zie [optie voor co-sell in Partner Center](commercial-marketplace-co-sell.md).

## <a name="next-steps"></a>Volgende stappen

- [Een SaaS-aanbieding maken in de commerciële Marketplace](create-new-saas-offer.md)
- [Best practices voor aanbiedingsvermeldingen](gtm-offer-listing-best-practices.md)
