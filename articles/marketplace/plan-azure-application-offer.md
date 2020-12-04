---
title: Een Azure-toepassing aanbieding plannen voor de commerciële Marketplace
description: Meer informatie over het plannen van een nieuwe Azure-toepassings aanbieding voor het aanbieden of verkopen in azure Marketplace of via het programma Cloud Solution Provider (CSP) met behulp van de portal voor commerciële Marketplace in het micro soft partner centrum.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 4e256d97d055b56df8c917c841416e5c7eb52465
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573524"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Een Azure-toepassing aanbieding plannen voor de commerciële Marketplace

In dit artikel worden de verschillende opties en vereisten beschreven voor het publiceren van een Azure-toepassing aanbieding naar de micro soft Commercial Marketplace.

## <a name="before-you-begin"></a>Voordat u begint

Voor het ontwerpen, bouwen en testen van Azure Application-aanbiedingen is technische kennis nodig van zowel het Azure-platform als de technologieën die worden gebruikt voor het bouwen van de aanbieding. Uw technische team moet kennis hebben van de volgende micro soft-technologieën:

- Basis informatie over [Azure-Services](https://azure.microsoft.com/services/).
- Het [ontwerpen en ontwikkelen van Azure-toepassingen](https://azure.microsoft.com/solutions/architecture/).
- Werk kennis van [azure virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking#networking).
- Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
- Werk kennis van [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Technische documentatie en bronnen

Bekijk de volgende resources bij het plannen van uw Azure-toepassings aanbieding voor de commerciële Marketplace.

- [Azure Resource Manager sjablonen begrijpen](../azure-resource-manager/templates/template-syntax.md)
- Quickstarts:
    - [Azure-quickstart-sjablonen](https://azure.microsoft.com/documentation/templates/)
    - [Hand leiding voor best practices voor Azure-sjablonen](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Toepassingsdefinitie publiceren](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [Servicecatalogus-app implementeren](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- Zelfstudies:
    - [Definitiebestanden maken](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- Voor beelden
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [Beheerde toepassingsoplossingen](../azure-resource-manager/managed-applications/sample-projects.md)

De video [buil ding Solution-sjablonen en beheerde toepassingen voor Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) bieden een uitgebreide inleiding tot het type Azure-toepassings aanbieding:

- Welke typen aanbieding beschikbaar zijn
- Welke technische middelen zijn vereist
- Een Azure Resource Manager sjabloon ontwerpen
- De gebruikers interface van de App ontwikkelen en testen
- De app-aanbieding publiceren
- Het beoordelings proces van de toepassing

### <a name="suggested-tools"></a>Aanbevolen hulpprogram ma's

Kies een of beide van de volgende script omgevingen om uw Azure-toepassing te beheren:

- [Azure PowerShell](/powershell/azure/)
- [Azure-CLI](/cli/azure)

Het is raadzaam om de volgende hulpprogram ma's toe te voegen aan uw ontwikkel omgeving:

- [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/) met de volgende extensies:
    - Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

U kunt de beschik bare hulpprogram ma's bekijken op de pagina [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/tools/) . Als u Visual Studio gebruikt, raadpleegt u [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="listing-options"></a>Aanbiedingsopties

Nadat uw aanbieding is gepubliceerd, worden de aanbiedings opties voor uw aanbieding weer gegeven als een knop in de linkerbovenhoek van de aanbiedings pagina van uw aanbieding. Met de volgende scherm afbeelding ziet u bijvoorbeeld een pagina met een aanbieding in azure Marketplace met de knop _nu downloaden_ . Als u ervoor hebt gekozen om een test drive aan te bieden, wordt de knop _test station_ ook weer gegeven.

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Illustreert een pagina met vermeldingen op Azure Marketplace.":::

## <a name="test-drive"></a>Station testen

U kunt ervoor kiezen om een test drive in te scha kelen voor uw Azure-toepassing-aanbieding waarmee klanten uw aanbieding kunnen uitproberen voordat deze wordt aangeschaft. Zie [Wat is een test drive?](what-is-test-drive.md)voor meer informatie over test stations. Zie [technische configuratie testen](test-drive-technical-configuration.md)voor meer informatie over het configureren van verschillende soorten test stations.

U kunt ook lezen over [test drive best practices](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) en de [overzicht van de test stations](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) -PDF downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

> [!NOTE]
> Informatie die de gebruiker moet zien, zelfs als skimmingBecause alle Azure-toepassingen worden geïmplementeerd met een Azure Resource Manager-sjabloon, is het enige type test drive dat beschikbaar is voor een [Azure-toepassing een Azure Resource Manager test drive](azure-resource-manager-test-drive.md).

## <a name="customer-leads"></a>Leads van klanten

U moet uw aanbieding verbinden met het CRM-systeem (Customer Relationship Management) om klant gegevens te verzamelen. De klant wordt gevraagd om toestemming te krijgen om hun gegevens te delen. Deze klant gegevens, samen met de naam van de aanbieding, de ID en de online winkel waar ze uw aanbieding vinden, worden verzonden naar het CRM-systeem dat u hebt geconfigureerd. De commerciële Marketplace ondersteunt een groot aantal CRM-systemen, samen met de optie voor het gebruik van een Azure-tabel of het configureren van een HTTPS-eind punt met behulp van energie automatisering.

U kunt op elk gewenst moment een CRM-verbinding toevoegen of wijzigen tijdens of na het maken van de aanbieding. Zie [leads van klanten van uw aanbieding voor commerciële Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md)voor gedetailleerde richt lijnen.

## <a name="categories-and-subcategories"></a>Categorieën en subcategorieën

U kunt ten minste één en Maxi maal twee categorieën kiezen voor het groeperen van uw aanbieding in de juiste Zoek gebieden voor commerciële Marketplace. U kunt Maxi maal twee subcategorieën voor elke primaire en secundaire categorie kiezen. Zie [Best practices](gtm-offer-listing-best-practices.md#categories)voor aanbiedings aanbiedingen voor een volledige lijst met categorieën en subcategorieën.

## <a name="legal-contracts"></a>Juridische contracten

Micro soft biedt een standaard contract dat u voor uw aanbiedingen kunt gebruiken in de commerciële Marketplace om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. Wanneer u uw software onder het Standard-contract aanbiedt, hoeven klanten slechts één keer te lezen en te accepteren en hoeft u geen aangepaste voor waarden te maken.

Als u kiest voor het gebruik van het standaard contract, hebt u de mogelijkheid om universele wijzigings voorwaarden toe te voegen en Maxi maal 10 aangepaste wijzigingen aan te brengen in het standaard contract. U kunt ook uw eigen voor waarden gebruiken in plaats van het standaard contract. U gaat deze details beheren op de pagina **Eigenschappen** . Zie voor gedetailleerde informatie het [standaard contract voor micro soft Commercial Marketplace](standard-contract.md).

> [!NOTE]
> Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor de commerciële Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. Het is een ' or '-scenario. U kunt uw oplossing aanbieden onder het standaard contract of uw eigen voor waarden. Als u de voor waarden van het standaard contract wilt wijzigen, kunt u dit doen via de standaard wijzigingen in contracten.

## <a name="offer-listing-details"></a>Details aanbiedings vermelding

Wanneer u een nieuwe Azure-toepassing aanbieding maakt in het partner centrum, voert u tekst, afbeeldingen, optionele Video's en andere informatie in op de pagina aanbieding. Dit is de informatie die klanten te zien krijgen wanneer ze uw aanbieding op de Azure Marketplace detecteren, zoals wordt weer gegeven in het volgende voor beeld.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Illustreert hoe deze aanbieding wordt weer gegeven in azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Logo
2. Categorieën
3. Ondersteunings adres (koppeling)
4. Gebruiksvoorwaarden
5. Adres van privacybeleid (koppeling)
6. Naam van aanbieding
7. Samenvatting
8. Beschrijving
9. Scherm afbeeldingen/Video's

In de volgende scherm afbeelding ziet u hoe informatie over de aanbieding wordt weer gegeven in de Azure Portal:

[![Illustreert hoe deze aanbieding wordt weer gegeven in de Azure Portal.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Beschrijvingen van aanroepen

1. Titel
2. Beschrijving
3. Handige koppelingen
4. Schermopnamen

> [!NOTE]
> De inhoud van het aanbiedings aanbod hoeft niet te worden weer gegeven in het Engels als de beschrijving van de aanbieding begint met de zin ' deze toepassing is alleen beschikbaar in [niet-Engelse taal] '.

Om uw aanbieding gemakkelijker te maken, moet u enkele van deze items vooraf voorbereiden. De volgende items zijn vereist tenzij anders vermeld.

- **Naam**: deze naam wordt weer gegeven als de titel van uw aanbieding in de commerciële Marketplace. De naam kan worden aangemerkt. Het mag geen emojis bevatten (tenzij ze de symbolen van het handels merk en copyright zijn) en moet beperkt zijn tot 50 tekens.
- **Samen vatting van zoek resultaten**: Beschrijf het doel of de functie van uw aanbieding als één zin, in tekst zonder opmaak, in 100 tekens of minder. Deze samen vatting wordt gebruikt in de zoek resultaten voor commerciële Marketplace-aanbiedingen.
- **Korte beschrijving**: maxi maal 256 tekens aan tekst zonder opmaak bevatten. Deze samen vatting wordt weer gegeven op de pagina met details van de aanbieding.
- **Beschrijving**: deze beschrijving wordt weer gegeven in het overzicht van Azure Marketplace-lijst (en). Overweeg het opnemen van een toegevoegde waarde, de belangrijkste voor delen, de beoogde gebruikers basis, alle categorieën of branche koppelingen, in-app aankoop verkoop kansen, klant behoeften of pijn dat de aanbieding adressen, vereiste informatie en een koppeling voor meer informatie.

    Dit tekstvak bevat besturings elementen voor de RTF-editor die u kunt gebruiken om uw beschrijving effectiever te maken. U kunt ook HTML-tags gebruiken om uw beschrijving op te maken. U kunt Maxi maal 3.000 tekens tekst in dit vak invoeren, inclusief HTML-opmaak en spaties. Zie [een fantastische beschrijving](/windows/uwp/publish/write-a-great-app-description) van de app en [HTML-tags schrijven die worden ondersteund in de beschrijvingen van de commerciële Marketplace-aanbieding](supported-html-tags.md)voor aanvullende tips.

- **Zoek trefwoorden** (optioneel): Geef Maxi maal drie Zoek trefwoorden op die klanten kunnen gebruiken om uw aanbieding te vinden in de online winkel. Voor de beste resultaten gebruikt u deze tref woorden ook in uw beschrijving. U hoeft de **naam** en **Beschrijving** van de aanbieding niet op te nemen. Deze tekst wordt automatisch opgenomen in de zoek opdracht.
- **Koppeling Privacybeleid**: de URL voor het privacybeleid van uw bedrijf. U moet een geldig privacybeleid opgeven en zijn verantwoordelijk om ervoor te zorgen dat uw app voldoet aan de wetten en voor schriften van de privacy.
- **Nuttige koppelingen** (optioneel): u kunt koppelingen naar verschillende resources opgeven voor gebruikers van uw aanbieding. Bijvoorbeeld forums, veelgestelde vragen en opmerkingen bij de release.
- **Contact gegevens**: u moet de volgende contact personen in uw organisatie aanwijzen:
  - **Contact opnemen met ondersteuning**: Geef de naam, het telefoon nummer en het e-mail adres op voor micro soft-partners die u kunt gebruiken wanneer uw klanten tickets openen. U moet ook de URL voor uw ondersteunings website toevoegen.
  - **Technische contact persoon**: Geef de naam, het telefoon nummer en het e-mail adres op die door micro soft rechtstreeks kunnen worden gebruikt wanneer er problemen zijn met uw aanbieding. Deze contact gegevens worden niet vermeld in de commerciële Marketplace.
  - **CSP-programma** : Geef de naam, het telefoon nummer en het e-mail adres op als u zich aanmeldt bij het CSP-programma (Cloud Solution Provider), zodat deze partners contact met u kunnen opnemen om vragen te beantwoorden. U kunt ook een URL naar uw marketing materiaal toevoegen.
- **Media – logo's**: Geef een PNG-bestand voor het logo van **grote** grootte op. Het partner centrum gebruikt deze om een **klein** en **gemiddeld** logo te maken. U kunt deze desgewenst later vervangen door andere installatie kopieën.
  - Groot (van 216 x 216 tot 350 x 350 px, vereist)
  - Gemiddeld (90 x 90 px, optioneel)
  - Klein (48 x 48 px, optioneel)

  Deze logo's worden op verschillende plaatsen in de online winkels gebruikt:
  - Het kleine logo wordt weer gegeven in de zoek resultaten voor Azure Marketplace.
  - Het logo gemiddeld wordt weer gegeven wanneer u een nieuwe resource maakt in Microsoft Azure.
  - Het grote logo wordt weer gegeven op de aanbiedings pagina van uw aanbieding in azure Marketplace.

  Volg deze richt lijnen voor uw logo's:

  - Het Azure-ontwerp heeft een eenvoudig kleurenpalet. Beperk het aantal primaire en secundaire kleuren in uw logo.
  - De themakleuren van de portal zijn wit en zwart. Gebruik deze kleuren niet als de achtergrondkleur voor uw logo. Gebruik een kleur waardoor uw logo opvalt in de portal. We adviseren eenvoudige primaire kleuren.
  - Als u een transparante achtergrond gebruikt, moeten het logo en de tekst niet wit, zwart of blauw zijn.
  - Het uiterlijk van uw logo moet plat zijn en voor komen dat er verlopen in het logo of de achtergrond. Plaats geen tekst in het logo, ook niet de naam van uw bedrijf of merk. Vage afbeeldingen zorgen ervoor dat uw inzending wordt afgewezen.
  - Zorg ervoor dat het logo niet is uitgerekt.

- **Media-scherm afbeeldingen** (optioneel): we raden u aan om scherm opnamen toe te voegen die laten zien hoe uw aanbieding werkt. U kunt Maxi maal vijf scherm opnamen toevoegen aan de volgende vereisten, die laten zien hoe uw aanbieding werkt:
  - 1280 x 720 pixels
  - PNG-bestand
  - Moet een bijschrift bevatten
- **Media – Video's** (optioneel): u kunt Maxi maal vijf Video's toevoegen aan de volgende vereisten, die uw aanbod demonstreren:
  - Naam
  - URL: moet alleen worden gehost op YouTube of Vimeo.
  - Miniatuur: 1280 x 720. png-bestand

> [!NOTE]
> Uw aanbieding moet voldoen aan het algemene [certificerings beleid voor commerciële Marketplace](/legal/marketplace/certification-policies#100-general.md) dat moet worden gepubliceerd op de commerciële Marketplace.

## <a name="preview-audience"></a>Voor beeld van doel groep

Een preview-doel groep heeft toegang tot uw aanbieding voordat u Live in de online winkels kunt publiceren om de end-to-end-functionaliteit te testen voordat u deze live publiceert.

> [!NOTE]
> Een preview-doel groep wijkt af van een privé-abonnement. Een privé-abonnement is één die u alleen beschikbaar maakt voor een specifieke doel groep. Zo kunt u een aangepast plan met specifieke klanten onderhandelen.

U definieert de preview-doel groep met behulp van Azure-abonnement-Id's, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten.

## <a name="technical-configuration"></a>Technische configuratie

Voor beheerde toepassingen die meter gebeurtenissen verzenden met behulp van de Api's van de [Marketplace metering-service](partner-center-portal/marketplace-metering-service-apis.md), moet u de identiteit opgeven die door uw service wordt gebruikt bij het uitzenden van meter gebeurtenissen.

Deze configuratie is vereist als u [batch-gebruiks gebeurtenis](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event)wilt gebruiken. Als u een [gebruiks gebeurtenis](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event)wilt verzenden, kunt u ook de [meta gegevens service](../active-directory/managed-identities-azure-resources/overview.md) gebruiken om het [JWT-Bearer-token (JSON Web token](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)) op te halen.

- **Azure Active Directory Tenant-id** (vereist): in de Azure Portal moet u [een Azure Active Directory (AD)-app maken](../active-directory/develop/howto-create-service-principal-portal.md) zodat de verbinding tussen de twee services achter een geverifieerde communicatie kan worden gevalideerd. Als u de [Tenant-id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) voor uw Azure Active Directory-app (Azure AD) wilt vinden, gaat u naar de blade [App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in uw Azure Active Directory. Selecteer de app in de kolom **weergave naam** . Zoek vervolgens naar **Eigenschappen** en vervolgens naar de **Directory-id (Tenant)** (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ).
- **Azure Active Directory toepassings-id** (vereist): u hebt ook uw [toepassings-id](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) en een verificatie sleutel nodig. Als u de toepassings-ID wilt vinden, gaat u naar de Blade [app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) in uw Azure Active Directory. Selecteer de app in de kolom **weergave naam** en zoek vervolgens naar de **toepassing (client)-ID** (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ). Ga naar **instellingen** en selecteer **sleutels** om de verificatie sleutel te vinden. U moet een beschrijving en een duur opgeven, en vervolgens een numerieke waarde.

> [!NOTE]
> De Azure-toepassings-ID wordt gekoppeld aan uw uitgevers-ID en kan alleen opnieuw worden gebruikt binnen dit uitgevers account.

## <a name="additional-sales-opportunities"></a>Aanvullende verkoop kansen

U kunt kiezen voor marketing-en verkoop kanalen die door micro soft worden ondersteund. Wanneer u uw aanbieding in Partner Center maakt, ziet u twee tabbladen naar het einde van het proces:

- **Door sturen via csp's**: gebruik deze optie om de partners van Microsoft Cloud Solution Providers (CSP) in staat te stellen uw oplossing door te verkopen als onderdeel van een gebundelde aanbieding. Zie het [Cloud Solution Provider-programma](./cloud-solution-providers.md) voor meer informatie.
- **Samen verkopen met micro soft: met** deze optie kunnen micro soft verkoop teams uw door de klant in aanmerking komende oplossing door nemen bij de evaluatie van de behoeften van uw klanten. Zie [optie voor co-sell in het partner centrum](commercial-marketplace-co-sell.md) voor gedetailleerde informatie over het voorbereiden van uw aanbieding voor evaluatie. Zie [Cloud Solution Providers](cloud-solution-providers.md)(Engelstalig) voor meer informatie over het marketing uw aanbod via de micro soft CSP-partner kanalen.

Zie [uw Cloud activiteiten uitbreiden met Azure Marketplace](https://azuremarketplace.microsoft.com/sell)voor meer informatie.

## <a name="plans"></a>Abonnementen

Azure-toepassing aanbiedingen vereisen ten minste één abonnement. Een plan definieert het bereik en de limieten van de oplossing en de bijbehorende prijzen, indien van toepassing. U kunt meerdere plannen maken voor uw aanbieding om uw klanten verschillende technische en prijs opties te geven.

Voor algemene richt lijnen over plannen, waaronder prijs modellen en privé plannen, Zie [Abonnementen en prijzen voor commerciële Marketplace-aanbiedingen](plans-pricing.md). In de volgende secties vindt u aanvullende informatie die specifiek is voor Azure-toepassing plannen.

### <a name="types-of-plans"></a>Typen abonnementen

Er zijn twee soorten Azure-toepassings abonnementen: _oplossings sjabloon_ en _beheerde toepassing_. Beide typen abonnementen ondersteunen het automatiseren van de implementatie en configuratie van een oplossing buiten één virtuele machine (VM). U kunt het proces van het bieden van meerdere resources, waaronder Vm's, netwerken en opslag resources, automatiseren om complexe oplossingen te bieden, zoals IaaS-oplossingen. Beide typen abonnementen kunnen veel verschillende soorten Azure-resources gebruiken, inclusief, maar niet beperkt tot virtuele machines.

- **Oplossingen voor oplossings sjablonen** zijn een van de belangrijkste manieren om een oplossing in de commerciële Marketplace te publiceren. Oplossingen voor oplossings sjablonen zijn niet transactable in de commerciële Marketplace, maar kunnen worden gebruikt voor het implementeren van betaalde VM-aanbiedingen die worden gefactureerd via de commerciële Marketplace. Gebruik het oplossings sjabloon type wanneer de klant de oplossing beheert en de trans acties worden gefactureerd via een ander abonnement. Zie [Wat is Azure Resource Manager?](../azure-resource-manager/management/overview.md) voor meer informatie over het bouwen van oplossings sjablonen.
- Met **beheerde toepassings** abonnementen kunt u eenvoudig volledig beheerde, kant-en-klare toepassingen voor uw klanten maken en leveren. Ze hebben dezelfde mogelijkheden als oplossings sjabloon plannen, met enkele belang rijke verschillen:
    - De resources worden geïmplementeerd in een resource groep en worden beheerd door de uitgever van de app. De resourcegroep is opgenomen in het abonnement van de consument, maar een identiteit in de tenant van de uitgever heeft toegang tot de resourcegroep. 
    - Als uitgever kunt u de kosten opgeven voor de continue ondersteuning van de oplossing en trans acties worden ondersteund via de commerciële Marketplace.
 
    Gebruik het type beheerd toepassings plan wanneer u of uw klant vereist dat de oplossing wordt beheerd door een partner of u een oplossing op basis van een abonnement implementeert. Zie [overzicht van Azure-beheerde toepassingen](../azure-resource-manager/managed-applications/overview.md)voor meer informatie over de voor-en andere typen beheerde toepassingen.

## <a name="next-steps"></a>Volgende stappen

- Zie [een oplossings sjabloon plannen voor een Azure-toepassings aanbieding](plan-azure-app-solution-template.md)voor het plannen van een oplossings sjabloon.
- Zie [een door Azure beheerde toepassing plannen voor een Azure-toepassings aanbieding](plan-azure-app-managed-app.md)voor het plannen van een door Azure beheerde toepassing.