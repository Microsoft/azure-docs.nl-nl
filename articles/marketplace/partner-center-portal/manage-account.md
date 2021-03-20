---
title: Een commerciële Marketplace-account beheren in micro soft Partner Center
description: Meer informatie over het beheren van een commerciële Marketplace-account in micro soft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 11/02/2020
ms.openlocfilehash: cc853285e193dd3ac4e348525b8bf0db9f8cb023
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533941"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Uw commerciële Marketplace-account beheren in het partner centrum

Zodra u [een partner centrum-account hebt gemaakt](./create-account.md), kunt u het [dash board voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) gebruiken voor het beheren van uw account en aanbiedingen.

In dit artikel leert u hoe u uw partner Center-account kunt beheren, met inbegrip van het volgende:

- [Toegang tot de account instellingen van uw partner centrum](#access-your-account-settings)
- [Zoek uw uitgevers-ID, Symantec-id, verkoper-ID, gebruikers-ID, MPN-ID en Azure AD-tenants](#account-details)
- [Contact gegevens bijwerken](#contact-info)
- [Tracering-GUID'S instellen voor het bewaken van klant gebruik](#tracking-guids)
- [Gebruikers beheren](#manage-users)
- [Groepen beheren](#manage-groups)
- [Azure AD-toepassingen beheren](#manage-azure-ad-applications)
- [Gebruikersrollen en machtigingen definiëren](#define-user-roles-and-permissions)
- [Azure AD-tenants beheren (werk accounts)](#manage-tenants)
- [Partner Center-overeenkomsten beheren](#agreements)

## <a name="access-your-account-settings"></a>Toegang tot uw account instellingen

Als u dit nog niet hebt gedaan, moet u (of de beheerder van uw organisatie) toegang hebben tot de [account instellingen](https://partner.microsoft.com/dashboard/account/management) voor uw partner centrum-account:

- Controleer de status van de account verificatie van uw bedrijf.
- Bevestig uw Symantec-ID, de gebruikers-id van de Microsoft Partner Network verkoper, de ID van de MPN, de uitgever-ID en de contact gegevens, inclusief de goed keurder van het bedrijf en de contact persoon van de verkoper.
- Maak gebruikers accounts voor iedereen die uw bedrijfs account in het partner centrum gaat gebruiken.

### <a name="open-account-settings"></a>Account instellingen openen

Selecteer in het [dash board voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace) in partner centrum **instellingen** (tandwiel pictogram) in de rechter bovenhoek en klik vervolgens op **account instellingen**.

![Het menu account instellingen in het partner centrum](./media/dashboard-developer-settings.png)

In **account instellingen** kunt u het volgende bekijken:

- **Account Details**: account type en account status
- **Uitgevers-id's**: verkopers-id, gebruikers-id, uitgevers-id, Azure AD-tenants, enzovoort
- **Contact gegevens**: de weergave naam van de uitgever, de naam van de contact persoon, de e-mail, het telefoon nummer en het adres
- **Tracking-guid's**: alle tracerings-guid's die aan uw account zijn gekoppeld

### <a name="account-details"></a>Account gegevens

In het gedeelte account details ziet u basis informatie, zoals uw **account type** (bedrijf of individu) en de **verificatie status** van uw account. Tijdens uw account verificatie worden in deze instellingen elke vereiste stap weer gegeven, waaronder e-mail verificatie, werkgelegenheids verificatie en zakelijke verificatie. U kunt uw e-mail adres hier ook bijwerken en de verificatie zo nodig opnieuw verzenden.

### <a name="publisher-ids"></a>Uitgevers-Id's

In de sectie uitgevers-Id's ziet u uw **Symantec-id**, **de verkoper-** ID, de **gebruikers-ID**, de **MPN-id** en **Azure AD-tenants**. Deze waarden worden door micro soft toegewezen om uw ontwikkelaars account uniek te identificeren en kunnen niet worden bewerkt.

### <a name="contact-info"></a>Contactgegevens

In de sectie contact gegevens ziet u de **weergave naam** van de uitgever, de **contact gegevens** van de verkoper (de naam van de contact persoon, het e-mail adres en het telefoon nummer van de verkoper van het bedrijf) en de door het **bedrijf gefiatteurde** persoon (de naam, de e-mail en het telefoon nummer van de persoon met autoriteit voor het goed keuren van beslissingen voor het bedrijf)

#### <a name="payout-account"></a>Uitbetalings account

Een uitbetalings account is de Bank rekening waarnaar de opbrengst wordt verzonden vanuit uw verkoop. Deze bank rekening moet zich in hetzelfde land/dezelfde regio bevinden als waar u uw partner centrum-account hebt geregistreerd.

Uw account voor betaling instellen:

1. Ga naar de [overzichts pagina voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) in partner centrum.
2. Selecteer in de sectie profiel naast **uitbetalings profiel** de optie **bijwerken**.
3. **Kies een betalings wijze**: Bank account of PayPal.
4. **Betalings gegevens toevoegen**: dit kan onder andere het kiezen van een account type (controleren of sparen), de naam van de account houder, het account nummer en het route nummer, het facturerings adres, het telefoon nummer of het e-mail adres van PayPal opgeven. * Zie [PayPal-info](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)voor meer informatie over het gebruik van PayPal als uw betalings methode voor uw account en om te bepalen of het wordt ondersteund in uw markt regio.

> [!IMPORTANT]
> Het wijzigen van uw uitbetalings account kan uw betalingen vertragen met Maxi maal één betalings cyclus. Deze vertraging treedt op omdat de account wijziging moet worden gecontroleerd, net zoals bij de eerste instelling van het uitbetalings account. U ontvangt nog steeds voor het volledige bedrag nadat uw account is geverifieerd. alle betalingen die moeten worden uitgevoerd voor de huidige betalings cyclus, worden toegevoegd aan de volgende betaling.  

#### <a name="tax-profile"></a>BTW-profiel

Controleer de huidige status van het BTW-profiel en bevestig dat het juiste **entiteits type** en de gegevens van het **BTW-certificaat** worden weer gegeven. Selecteer **bewerken** om de vereiste formulieren bij te werken of te volt ooien.

Als u uw belasting status wilt instellen, moet u uw land of regio van verblijf en burgerschap opgeven en de juiste BTW-formulieren volt ooien die zijn gekoppeld aan uw land/regio.

Ongeacht uw land/regio waar u woont of burgerschap, moet u Verenigde Staten BTW-formulieren invullen om aanbiedingen via micro soft te verkopen. Partners die voldoen aan bepaalde Verenigde Staten locatie-vereisten, moeten een IRS W-9-formulier invullen. Andere partners buiten de Verenigde Staten moeten een IRS W-8-formulier invullen. U kunt deze formulieren online invullen tijdens het volt ooien van uw BTW-profiel.

Een Verenigde Staten afzonderlijke belastingplichtige-id (of ITIN) is niet vereist voor het ontvangen van betalingen van micro soft of voor het claimen van de voor delen van belasting verdragen.

U kunt uw belasting formulieren elektronisch volt ooien en verzenden in het partner centrum. in de meeste gevallen hoeft u geen formulieren af te drukken en te verzenden.

Verschillende landen en regio's hebben verschillende belasting vereisten. De exacte hoeveelheid die u in BTW moet betalen, is afhankelijk van de landen en regio's waar u uw aanbiedingen verkoopt. Micro soft verdeelt de omzet en maakt gebruik van de belasting namens u in sommige landen/regio's. Deze landen/regio's worden geïdentificeerd in het proces van het aanbieden van uw aanbieding. In andere landen/regio's, afhankelijk van waar u bent Inge schreven, moet u mogelijk verkoop-en gebruiks belasting voor uw verkoop rechtstreeks aan de lokale belasting dienst remitteren. Daarnaast kan de verkoop opbrengst worden belast als bate. We raden u aan om contact op te nemen met de relevante instantie voor uw land of regio, die u het beste kan helpen bij het bepalen van de juiste belasting gegevens voor uw micro soft-verkoop transacties.

##### <a name="withholding-rates"></a>Inhoudings tarieven

De gegevens die u in uw belasting formulieren verzendt, bepalen het juiste tarief voor belasting heffing. Het inhoudings bedrag geldt alleen voor verkopen die u in de Verenigde Staten. verkoop van niet-Amerikaanse locaties is niet van toepassing op inhoud. De inhoudings tarieven variëren, maar voor de meeste ontwikkel aars die buiten het Verenigde Staten worden geregistreerd, is de standaard tarief 30%. U hebt de mogelijkheid om dit tarief te verlagen als uw land/regio heeft ingestemd op een inkomen uit de inkomsten belasting met de Verenigde Staten.

##### <a name="tax-treaty-benefits"></a>Voor delen van belasting verdrag

Als u zich buiten het Verenigde Staten bevindt, kunt u profiteren van de voor delen van belasting verdragen. Deze voor delen variëren van land/regio tot land/regio en kunnen u de belasting van micro soft-inhoud beperken. U kunt voor delen van fiscale verdragen claimen door deel II van het formulier W-8BEN te volt ooien. We raden u aan om te communiceren met de juiste resources in uw land of regio om te bepalen of deze voor delen van toepassing zijn op u.

Meer [informatie over belasting gegevens voor Windows app/Game-ontwikkel aars en Azure Marketplace-uitgevers](/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Status uitbetalings blokkering

Micro soft verzendt standaard betalingen op maand basis. U kunt eventueel ook uw uitbetalingen in de wacht zetten, waardoor het verzenden van betalingen naar uw account wordt voor komen. Als u ervoor kiest om uw uitbetalingen in de wacht te zetten, blijven we de omzet die u behaalt vastleggen en de details in de **samen vatting** van de betaling opgeven. Er worden echter geen betalingen naar uw account verzonden totdat u de blok kering verwijdert.

Ga naar **account instellingen** om uw betalingen in de wacht te zetten. Onder **financiële details**, in de sectie **status van uitbetalings blokkering** , schakelt u de schuif regelaar in **op aan**. U kunt de status van uw uitbetalings blokkering op elk gewenst moment wijzigen, maar houd er rekening mee dat uw beslissing van invloed is op de volgende maandelijkse uitbetaling. Als u bijvoorbeeld de uitbetaling van april wilt bewaren, moet u ervoor zorgen dat u vóór het einde van maart de status van uw uitbetalings blokkering hebt ingesteld **op aan.**

Zodra u de status van de uitbetalings blokkering hebt ingesteld op **aan**, worden alle uitbetalingen in de wacht stand **gezet** totdat u de schuif regelaar weer inschakelt. Wanneer u dit doet, wordt u opgenomen in de volgende maandelijkse uitbetalings cyclus (op voor waarde dat er aan de toepasselijke betalings drempels is voldaan). Als u bijvoorbeeld uw uitbetalingen in wacht hebt, maar graag een toekenning wilt genereren die in juni is gegenereerd, moet u ervoor zorgen dat de status van de uitbetalings blokkering wordt **uitgeschakeld** voor het einde van mei.

> [!NOTE]
> De status selectie van uw **uitbetalings blokkering** geldt voor **alle** opbrengst bronnen die worden betaald via micro soft Partner Center, waaronder Azure Marketplace, AppSource, Microsoft Store, reclame, enzovoort. U kunt voor elke opbrengst bron niet verschillende wacht statussen selecteren.

### <a name="devices"></a>Apparaten

De instellingen voor Apparaatbeheer zijn alleen van toepassing op UWP-publicatie. [Meer informatie](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Tracering van GUID'S

GUID'S (Globally Unique Identifiers) zijn unieke referentie nummers (met 32 hexadecimale cijfers) die kunnen worden gebruikt voor het bijhouden van uw Azure-gebruik. 

Als u GUID'S voor bijhouden wilt maken, moet u een GUID-generator gebruiken. Het Azure Storage team heeft een [GUID-Generator formulier](https://aka.ms/StoragePartners) gemaakt dat u een GUID van de juiste indeling stuurt en kan worden hergebruikt in de verschillende tracking systemen.

We raden u aan om voor elk product een unieke GUID te maken voor elk aanbod en distributie kanaal. U kunt ervoor kiezen om een enkele GUID voor de meerdere distributie kanalen van het product te gebruiken als u niet wilt dat rapportage wordt gesplitst.

Als u een product implementeert met behulp van een sjabloon en deze beschikbaar is op zowel Azure Marketplace als op GitHub, kunt u twee afzonderlijke GUID'S maken en registreren:

- Product A in azure Marketplace
- Product A op GitHub

Rapportage wordt uitgevoerd door de partner waarde (micro soft-partner-ID) en de GUID'S. U kunt ook GUID'S op een meer gedetailleerd niveau bijhouden voor elk abonnement binnen uw aanbieding.

Zie de [Veelgestelde vragen over het bijhouden van Azure-klanten met guid's](../azure-partner-customer-usage-attribution.md#faq)voor meer informatie.

## <a name="create-a-billing-profile"></a>Een facturerings profiel maken

Als u een [Dynamics 365 voor klant betrokkenheid publiceert & Power apps](./create-new-customer-engagement-offer.md) of [Dynamics 365 for Operations](./create-new-operations-offer.md) -aanbieding, moet u uw **facturerings profiel** volt ooien.

Het factuur adres is vooraf ingevuld op basis van uw rechts persoon en u kunt dit adres later bijwerken. De velden BTW-ID en BTW-nummer zijn optioneel.  De land/regio naam en bedrijfs naam kunnen niet worden bewerkt.

## <a name="multi-user-account-management"></a>Beheer van meerdere gebruikers accounts

Het partner centrum gebruikt [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) voor toegang tot en beheer van meerdere gebruikers accounts. De Azure AD van uw organisatie wordt automatisch gekoppeld aan uw partner centrum-account als onderdeel van het inschrijvings proces.

## <a name="manage-users"></a>Gebruikers beheren

Met de sectie **gebruikers** van het partner centrum (onder **account instellingen**) kunt u Azure AD gebruiken voor het beheren van de gebruikers, groepen en Azure AD-toepassingen die toegang hebben tot uw partner centrum-account. Uw account moet machtigingen op [**beheerders niveau**](#define-user-roles-and-permissions) hebben voor het [werk account (Azure AD-Tenant)](./company-work-accounts.md) waarin u gebruikers wilt toevoegen of bewerken. Als u gebruikers binnen een ander werk account/Tenant wilt beheren, moet u zich afmelden en vervolgens weer aanmelden als een gebruiker met **beheerders** machtigingen voor dat werk account/Tenant.

Zodra u bent aangemeld met uw werk account (Azure AD-Tenant), kunt u het volgende doen:

- [Gebruikers toevoegen of verwijderen](#add-existing-users)
- [Een gebruikers wachtwoord wijzigen](#change-a-user-password)
- [Groepen toevoegen of verwijderen](#manage-groups)
- [Azure AD-toepassingen toevoegen of verwijderen](#add-new-azure-ad-applications)
- [Sleutels voor een Azure AD-toepassing beheren](#manage-keys-for-an-azure-ad-application)
- [Gebruikersrollen en machtigingen definiëren](#define-user-roles-and-permissions)

Houd er rekening mee dat alle gebruikers van het partner centrum (met inbegrip van groepen en Azure AD-toepassingen) een actief werk account moeten hebben in een [Azure AD-Tenant](#manage-tenants) die is gekoppeld aan uw partner centrum-account.

### <a name="add-existing-users"></a>Bestaande gebruikers toevoegen

Gebruikers toevoegen aan uw partner centrum-account dat al aanwezig is in het werk account van uw bedrijf [(Azure AD-Tenant)](./company-work-accounts.md):

1. Ga naar **gebruikers** (onder **account instellingen**) en selecteer **gebruikers toevoegen**.
2. Selecteer een of meer gebruikers in de lijst die wordt weer gegeven. U kunt het zoekvak gebruiken om te zoeken naar specifieke gebruikers.
* Als u meer dan één gebruiker selecteert om aan uw partner Center-account toe te voegen, moet u deze dezelfde rol of set aangepaste machtigingen toewijzen. Als u meerdere gebruikers met verschillende rollen/machtigingen wilt toevoegen, herhaalt u deze stappen voor elke rol of set aangepaste machtigingen.
3. Wanneer u klaar bent met het kiezen van gebruikers, selecteert u **geselecteerde toevoegen**.
4. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de geselecteerde gebruiker (s) op.
5. Selecteer **Opslaan**.

### <a name="create-new-users"></a>Nieuwe gebruikers maken

Als u gloed nieuwe gebruikers accounts wilt maken, moet u een account hebben met [**globale beheerders**](../../active-directory/roles/permissions-reference.md) machtigingen.

1. Ga naar **gebruikers** (onder **account instellingen**), selecteer **gebruikers toevoegen** en kies vervolgens **nieuwe gebruikers maken**.
1. Voer voor elke nieuwe gebruiker een voor naam, achternaam en gebruikers naam in. 
1. Als u wilt dat de nieuwe gebruiker een globaal beheerders account in de adres lijst van uw organisatie heeft, schakelt u het selectie vakje **deze gebruiker een globale beheerder in uw Azure AD maken in en volledig beheer over alle Directory bronnen**. Hiermee krijgt de gebruiker volledige toegang tot alle beheer functies in de Azure AD van uw bedrijf. Ze kunnen gebruikers toevoegen en beheren in het werk account van uw organisatie (Azure AD-Tenant), maar niet in partner centrum, tenzij u het account de juiste rol/machtigingen verleent.
1. Als u het selectie vakje inschakelt om **deze gebruiker een globale beheerder te maken**, moet u een **e-mail voor wachtwoord herstel** opgeven zodat de gebruiker het wacht woord zo nodig kan herstellen.
1. Selecteer in de sectie **groepslid maatschap** de groepen waartoe u de nieuwe gebruiker wilt maken.
1. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de gebruiker op.
1. Selecteer **Opslaan**.

Als u een nieuwe gebruiker in partner centrum maakt, wordt er ook een account voor die gebruiker gemaakt in het werk account (Azure AD-Tenant) waarbij u bent aangemeld. Als u wijzigingen aanbrengt aan de naam van een gebruiker in het partner centrum, worden dezelfde wijzigingen aangebracht in het werk account van uw organisatie (Azure AD-Tenant).

### <a name="invite-new-users-by-email"></a>Nieuwe gebruikers uitnodigen via e-mail

Als u gebruikers wilt uitnodigen die momenteel geen deel uitmaken van uw werk account (Azure AD-Tenant) via e-mail, moet u een account hebben met [**globale beheerders**](../../active-directory/roles/permissions-reference.md) machtigingen.

1. Ga naar **gebruikers** (onder **account instellingen**), selecteer **gebruikers toevoegen** en kies **gebruikers uitnodigen per e-mail**.
2. Voer een of meer e-mail adressen (Maxi maal 10) in, gescheiden door komma's of punt komma's.
3. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de gebruiker op.
4. Selecteer **Opslaan**.

De gebruikers die u hebt uitgenodigd, krijgen een e-mail uitnodiging om lid te worden van uw partner centrum-account. Er wordt een nieuw gast gebruikers account gemaakt in uw werk account (Azure AD-Tenant). Elke gebruiker moet de uitnodiging accepteren voordat ze toegang krijgen tot uw account.

Als u een uitnodiging opnieuw moet verzenden, gaat u naar de pagina **gebruikers** , zoekt u de uitnodiging in de lijst met gebruikers, selecteert u hun e-mail adres (of de tekst met de melding *uitnodiging in behandeling*). Selecteer vervolgens aan de onderkant van de pagina **uitnodiging opnieuw verzenden**.

> [!NOTE]
> Als uw organisatie gebruikmaakt van [adreslijst integratie](/previous-versions/azure/azure-services/jj573653(v=azure.100)) om de on-premises adreslijst service te synchroniseren met uw Azure AD, kunt u geen nieuwe gebruikers, groepen of Azure AD-toepassingen maken in het partner centrum. U (of een andere beheerder in uw on-premises Directory) moet u deze rechtstreeks in de on-premises map maken voordat u deze kunt bekijken en toevoegen in het partner centrum.

### <a name="remove-a-user"></a>Een gebruiker verwijderen

Als u een gebruiker uit uw werk account (Azure AD-Tenant) wilt verwijderen, gaat u naar **gebruikers** (onder **account instellingen**), selecteert u de gebruiker die u wilt verwijderen met het selectie vakje in de kolom uiterst rechts en kiest u vervolgens **verwijderen** in de beschik bare acties. Er wordt een pop-upvenster weer gegeven waarin u kunt bevestigen dat u de geselecteerde gebruiker (s) wilt verwijderen.

### <a name="change-a-user-password"></a>Een gebruikers wachtwoord wijzigen

Als een van uw gebruikers hun wacht woord moet wijzigen, kunnen ze dat zelf doen als u een **e-mail voor wachtwoord herstel** hebt opgegeven tijdens het maken van het gebruikers account. U kunt ook de volgende stappen uitvoeren om het wacht woord van een gebruiker bij te werken. Als u het wacht woord van een gebruiker in uw bedrijfs account (Azure AD-Tenant) wilt wijzigen, moet u zijn aangemeld bij een account met [**globale beheerders**](../../active-directory/roles/permissions-reference.md) machtigingen. Hiermee wordt het wacht woord van de gebruiker in uw Azure AD-Tenant gewijzigd, samen met het wacht woord dat ze gebruiken voor toegang tot het partner centrum.

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) de naam van het gebruikers account dat u wilt bewerken.
2. Selecteer de knop **wacht woord opnieuw instellen** onder aan de pagina.
3. Er verschijnt een bevestigings pagina om de aanmeldings gegevens voor de gebruiker weer te geven, met inbegrip van een tijdelijk wacht woord. Zorg ervoor dat u deze gegevens afdrukt of kopieert en deze aan de gebruiker verstrekt, omdat u geen toegang meer hebt tot het tijdelijke wacht woord nadat u deze pagina verlaat.

## <a name="manage-groups"></a>Groepen beheren

Met groepen kunt u meerdere gebruikers rollen en machtigingen tegelijk beheren.

### <a name="add-an-existing-group"></a>Een bestaande groep toevoegen

Een groep toevoegen die al bestaat in het werk account van uw organisatie (Azure AD-Tenant) aan uw partner centrum-account:

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) **groepen toevoegen**.
2. Selecteer een of meer groepen in de lijst die wordt weer gegeven. U kunt het zoekvak gebruiken om te zoeken naar specifieke groepen.
Als u meer dan één groep selecteert om aan uw partner Center-account toe te voegen, moet u deze dezelfde rol of set aangepaste machtigingen toewijzen. Als u meerdere groepen met verschillende rollen/machtigingen wilt toevoegen, herhaalt u deze stappen voor elke rol of set aangepaste machtigingen.
3. Wanneer u klaar bent met het kiezen van groepen, selecteert u **geselecteerde toevoegen**.
4. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de geselecteerde groep (en) op. Alle leden van de groep hebben toegang tot uw partner centrum-account met de machtigingen die u toepast op de groep, ongeacht de rollen en machtigingen die zijn gekoppeld aan het afzonderlijke account.
5. Selecteer **Opslaan**.

Wanneer u een bestaande groep toevoegt, kan elke gebruiker die lid is van deze groep, toegang krijgen tot uw partner centrum-account, met de machtigingen die zijn gekoppeld aan de toegewezen rol van de groep.

### <a name="add-a-new-group"></a>Een nieuwe groep maken

Een gloed nieuwe groep toevoegen aan uw partner centrum-account:

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) **groepen toevoegen**.
2. Selecteer op de volgende pagina **nieuwe groep**.
3. Voer de weergave naam voor de nieuwe groep in.
4. Geef de rol (len) of aangepaste machtigingen voor de groep op. Alle leden van de groep hebben toegang tot uw partner centrum-account met de machtigingen die u hier toepast, ongeacht de rollen/machtigingen die zijn gekoppeld aan het afzonderlijke account.
5. Selecteer gebruiker (s) voor de nieuwe groep in de lijst die wordt weer gegeven. U kunt het zoekvak gebruiken om te zoeken naar specifieke gebruikers.
6. Wanneer u klaar bent met het selecteren van gebruikers, selecteert u **selectie toevoegen** om ze toe te voegen aan de nieuwe groep.
7. Selecteer **Opslaan**.

Deze nieuwe groep wordt ook gemaakt in het werk account van uw organisatie (Azure AD-Tenant), niet alleen in uw partner centrum-account.

### <a name="remove-a-group"></a>Een groep verwijderen

Als u een groep uit uw werk account (Azure AD-Tenant) wilt verwijderen, gaat u naar **gebruikers** (onder **account instellingen**), selecteert u de groep die u wilt verwijderen met het selectie vakje in de kolom uiterst rechts en kiest u vervolgens **verwijderen** in de beschik bare acties. Er wordt een pop-upvenster weer gegeven waarin u kunt bevestigen dat u de geselecteerde groep (en) wilt verwijderen.

## <a name="manage-azure-ad-applications"></a>Azure AD-toepassingen beheren

U kunt toepassingen of services die deel uitmaken van de Azure AD van uw bedrijf, toestaan om toegang te krijgen tot uw partner centrum-account.

### <a name="add-existing-azure-ad-applications"></a>Bestaande Azure AD-toepassingen toevoegen

Toepassingen toevoegen die al bestaan in de Azure Active Directory van uw bedrijf:

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) de optie **Azure AD-toepassingen toevoegen**.
2. Selecteer een of meer Azure AD-toepassingen in de lijst die wordt weer gegeven. U kunt het zoekvak gebruiken om te zoeken naar specifieke Azure AD-toepassingen. Als u meer dan één Azure AD-toepassing selecteert om aan uw partner Center-account toe te voegen, moet u deze dezelfde rol of set aangepaste machtigingen toewijzen. Als u meerdere Azure AD-toepassingen met verschillende rollen/machtigingen wilt toevoegen, herhaalt u deze stappen voor elke rol of set aangepaste machtigingen.
3. Wanneer u klaar bent met het selecteren van Azure AD-toepassingen, selecteert u **geselecteerde toevoegen**.
4. Geef in de sectie **functies** de rol (len) of aangepaste machtigingen voor de geselecteerde Azure AD-toepassing (en) op.
5. Selecteer **Opslaan**.

### <a name="add-new-azure-ad-applications"></a>Nieuwe Azure AD-toepassingen toevoegen

Als u partner Center toegang wilt verlenen tot een gloed nieuwe Azure AD-toepassings account, kunt u er een maken in de sectie **gebruikers** . Hiermee maakt u een nieuw account in uw werk account (Azure AD-Tenant), niet alleen in uw partner centrum-account. Als u hoofd zakelijk gebruikmaakt van deze Azure AD-toepassing voor Partner Center-verificatie en gebruikers geen toegang meer nodig hebt, kunt u een geldig adres invoeren voor de **antwoord-URL** en de URI van de **App-ID**, zolang deze waarden niet worden gebruikt door een andere Azure AD-toepassing in uw Directory.

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) de optie **Azure AD-toepassingen toevoegen**.
2. Selecteer op de volgende pagina de optie **nieuwe Azure AD-toepassing**.
3. Voer de **antwoord-URL** in voor de nieuwe Azure AD-toepassing. Dit is de URL waar gebruikers zich kunnen aanmelden en uw Azure AD-toepassing gebruiken (ook wel de URL van de app of de Sign-On URL genoemd). De **antwoord-URL** mag niet langer zijn dan 256 tekens en moet uniek zijn binnen uw Directory.
4. Voer de **App-ID-URI** in voor de nieuwe Azure AD-toepassing. Dit is een logische id voor de Azure AD-toepassing die wordt weer gegeven wanneer een eenmalige aanmelding wordt verzonden naar Azure AD. De **URI van de App-ID** moet uniek zijn voor elke Azure AD-toepassing in uw Directory. Deze ID mag niet langer zijn dan 256 tekens. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)) voor meer informatie over de id-URI van de app.
5. Geef in de sectie **rollen** de rol (len) of aangepaste machtigingen voor de Azure AD-toepassing op.
6. Selecteer **Opslaan**.

Nadat u een Azure AD-toepassing hebt toegevoegd of gemaakt, kunt u teruggaan naar de sectie **gebruikers** en de toepassings naam selecteren om de instellingen voor de toepassing te controleren, waaronder de Tenant-id, de client-id, de antwoord-URL en de URI van de App-ID.

### <a name="remove-an-azure-ad-application"></a>Een Azure AD-toepassing verwijderen

Als u een toepassing uit uw werk account (Azure AD-Tenant) wilt verwijderen, gaat u naar **gebruikers** (onder **account instellingen**), selecteert u de toepassing die u wilt verwijderen met het selectie vakje in de kolom uiterst rechts en kiest u vervolgens **verwijderen** in de beschik bare acties. Er wordt een pop-upvenster weer gegeven waarin u kunt bevestigen dat u de geselecteerde toepassing (en) wilt verwijderen.

### <a name="manage-keys-for-an-azure-ad-application"></a>Sleutels voor een Azure AD-toepassing beheren

Als uw Azure AD-toepassing gegevens in Microsoft Azure AD leest en schrijft, is er een sleutel nodig. U kunt sleutels voor een Azure AD-toepassing maken door de gegevens te bewerken in Partner Center. U kunt ook sleutels verwijderen die niet meer nodig zijn.

1. Selecteer op de pagina **gebruikers** (onder **account instellingen**) de naam van de Azure AD-toepassing. U ziet alle actieve sleutels voor de Azure AD-toepassing, met inbegrip van de datum waarop de sleutel is gemaakt en het verlopen van 50.
2. Selecteer **verwijderen** om een sleutel te verwijderen die niet meer nodig is.
3. Selecteer **nieuwe sleutel toevoegen** om een nieuwe sleutel toe te voegen.
4. Er wordt een scherm weer gegeven met de **client-id** en **sleutel waarden**. Zorg ervoor dat u deze informatie afdrukt of kopieert, omdat u deze niet meer kunt openen nadat u deze pagina verlaat.
5. Als u meer sleutels wilt maken, selecteert u **nog een sleutel toevoegen**.

## <a name="define-user-roles-and-permissions"></a>Gebruikersrollen en machtigingen definiëren

De gebruikers van uw bedrijf kunnen de volgende rollen en machtigingen toewijzen aan het programma voor commerciële Marketplace in het partner centrum:

- **Eigenaar**
  - Dit is de persoon die het account voor het eerst heeft gemaakt en volledige toegang heeft, inclusief de mogelijkheid om alle account gebruikers te maken en te bewerken en om alle financiële en account instellingen te wijzigen. Een account heeft meestal slechts één eigenaar.
- **Manager**
  - Heeft toegang tot alle Microsoft-account functies behalve de instellingen voor belasting en betaling
  - Kan gebruikers, rollen en werk accounts (tenants) beheren
- **Developer**
  - Kan aanbiedingen beheren en publiceren
  - Kan sommige uitgevers rapporten weer geven

> [!NOTE]
> Voor het programma voor commerciële Marketplace worden de rollen globale beheerder, zakelijke bijdrager, financiële bijdrager en markt beheerder niet gebruikt. Het toewijzen van deze rollen aan gebruikers heeft geen effect. Alleen de rollen Manager en ontwikkelaar verlenen machtigingen aan gebruikers.

Zie voor meer informatie over het beheren van rollen en machtigingen in andere gebieden van partner centrum, zoals Azure Active Directory (AD), Cloud Solution Provider (CSP), leverancier van het configuratie scherm (CPV), gast gebruikers of Microsoft Partner Network (MPN), [gebruikers rollen en machtigingen toewijzen in partner centrum](/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Tenants beheren

Een Azure Active Directory-Tenant (AD), ook wel uw ' werk account ' genoemd, is een weer gave van uw organisatie die in de Azure Portal is ingesteld en helpt u bij het beheren van een specifiek exemplaar van micro soft-Cloud Services voor uw interne en externe gebruikers. Als uw organisatie zich abonneert op een micro soft-Cloud service, zoals Azure, Microsoft Intune of Microsoft 365, is er een Azure AD-Tenant voor u gemaakt.

U kunt meerdere tenants instellen voor gebruik met partner Center. Elke gebruiker met de rol **Manager** in het partner centrum-account heeft de mogelijkheid om Azure AD-tenants toe te voegen aan of te verwijderen uit het account.  

### <a name="add-an-existing-tenant"></a>Een bestaande Tenant toevoegen

Een andere Azure AD-Tenant koppelen aan uw partner centrum-account:

1. Selecteer op de pagina **tenants** (onder **account instellingen**) **een andere Azure AD-Tenant koppelen**.
2. Voer uw Azure AD-referenties in voor de Tenant die u wilt koppelen.
3. Controleer de organisatie-en domein naam voor uw Azure AD-Tenant. Selecteer **bevestigen** om de koppeling te volt ooien.

Als de koppeling is geslaagd, kunt u account gebruikers toevoegen en beheren in de sectie **gebruikers** van het partner centrum.

### <a name="create-a-new-tenant"></a>Een nieuwe tenant maken

Een gloed nieuwe Azure AD-Tenant maken met uw partner centrum-account:

1. Selecteer op de pagina **tenants** (onder **account instellingen**) **een nieuwe Azure AD-Tenant maken**.
2. Voer de Directory-informatie in voor uw nieuwe Azure AD:
    - **Domein naam**: de unieke naam die we gebruiken voor uw Azure AD-domein, samen met '. onmicrosoft.com '. Als u bijvoorbeeld ' voor beeld ' hebt ingevoerd, zou uw Azure AD-domein ' example.onmicrosoft.com ' zijn.
    - **Contact opnemen met e-mail**: een e-mail adres waar wij zo nodig contact met u kunnen opnemen over uw account.
    - **Gebruikers account van globale beheerder**: de voor naam, achternaam, gebruikers naam en het wacht woord die u wilt gebruiken voor het nieuwe account van de globale beheerder.
3. Selecteer **maken** om de nieuwe domein-en account gegevens te bevestigen.
4. Meld u aan met uw nieuwe gebruikers naam en wacht woord voor de globale beheerder van Azure AD om [gebruikers toe te voegen en te beheren](#manage-users).

Zie het artikel [een nieuwe Tenant maken in azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)voor meer informatie over het maken van nieuwe tenants in uw Azure Portal, in plaats van via de Partner Center-Portal.

### <a name="remove-a-tenant"></a>Een Tenant verwijderen

Als u een Tenant uit uw partner centrum-account wilt verwijderen, zoekt u de naam op de pagina **tenants** (in **account instellingen**) en selecteert u vervolgens **verwijderen**. U wordt gevraagd om te bevestigen dat u de Tenant wilt verwijderen. Als u dit doet, kunnen gebruikers in die Tenant zich niet aanmelden bij het partner centrum-account en worden de machtigingen die u voor deze gebruikers hebt geconfigureerd, verwijderd.

Wanneer u een Tenant verwijdert, kunnen alle gebruikers die zijn toegevoegd aan het partner centrum-account van die Tenant, zich niet meer aanmelden bij het account.

> [!TIP]
> U kunt een Tenant niet verwijderen als u momenteel bent aangemeld bij een partner centrum met een account in dezelfde Tenant. Als u een Tenant wilt verwijderen, moet u zich aanmelden bij Partner Center als **Manager** voor een andere Tenant die is gekoppeld aan het account. Als er slechts één Tenant is gekoppeld aan het account, kan die Tenant pas worden verwijderd nadat u zich hebt aangemeld met de Microsoft-account waarmee het account is geopend.

## <a name="agreements"></a>Overeenkomsten

In het gedeelte **overeenkomsten** van het partner centrum (onder **account instellingen**) kunt u een lijst bekijken met de publicatie overeenkomsten die u hebt geautoriseerd. Deze overeenkomsten worden vermeld op basis van naam en versie nummer, met inbegrip van de datum waarop deze is geaccepteerd en de naam van de gebruiker die de overeenkomst heeft geaccepteerd.

Als er updates voor overeenkomsten zijn die uw aandacht nodig hebben, kunnen er boven aan deze pagina **acties** worden weer gegeven. Als u een bijgewerkte overeenkomst wilt accepteren, moet u eerst de versie van de gekoppelde overeenkomst lezen en vervolgens **overeenkomst accepteren** selecteren.
