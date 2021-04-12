---
title: Een commercieel Marketplace-account beheren in micro soft Partner Center-Azure Marketplace
description: Meer informatie over het beheren van een commerciële Marketplace-account in micro soft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: c76d9d06425405cf7f43e089cb9c2995e30410ee
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108232"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Uw commerciële Marketplace-account beheren in het partner centrum

**Juiste rollen**

- Eigenaar
- Manager

Zodra u [een partner centrum-account hebt gemaakt](./create-account.md), kunt u het [dash board voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) gebruiken voor het beheren van uw account en aanbiedingen.

## <a name="access-your-account-settings"></a>Toegang tot uw account instellingen

Als u dit nog niet hebt gedaan, moet u (of de beheerder van uw organisatie) toegang hebben tot de [account instellingen](https://partner.microsoft.com/dashboard/account/management) voor uw partner centrum-account.

1. Meld u aan bij het [dash board voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace) in het partner centrum met het account dat u wilt gebruiken. Als u deel uitmaakt van meerdere accounts en u zich hebt aangemeld met een ander account, kunt u [scha kelen tussen accounts](switch-accounts.md).
1. Selecteer in de rechter bovenhoek de optie **instellingen** (tandwiel pictogram) en selecteer vervolgens **account instellingen**.

    [![Scherm opname van het menu account instellingen in het partner centrum. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. Selecteer **wettelijk** onder **account instellingen** . Selecteer vervolgens het tabblad **ontwikkel aars** om details weer te geven die betrekking hebben op uw commerciële Marketplace-account.

    [![Scherm afbeelding van het tabblad ontwikkel aars op de pagina juridisch in account instellingen. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Pagina account instellingen

Wanneer u **instellingen** selecteert en **account instellingen** uitvouwt, is de standaard weergave **juridische informatie**. Deze pagina kan Maxi maal drie tabbladen bevatten, afhankelijk van de Program ma's die u hebt Inge schreven bij: _partner_, _wederverkoper_ en _Developer_.

Het tabblad **partner** , voor partners die zijn Inge SCHREVEN in MPN, omvat:

- Alle juridische Bedrijfs gegevens, zoals de naam en het adres van de geregistreerde rechts persoon van uw bedrijf
- Primaire contact persoon
- Bedrijfs locaties.

Het tabblad **reseller** , voor partners die CSP-bedrijven uitvoeren, omvat:

- Primaire contact gegevens
- Profiel voor klant ondersteuning
- Programma-informatie

Het tabblad **ontwikkelaar** , voor partners die zijn Inge schreven in een ontwikkelaars programma, heeft de volgende informatie:

- **Account Details**: account type en account status
- **Uitgevers-id's**: verkopers-id, gebruikers-id, uitgevers-id, Azure AD-tenants en meer
- **Contact gegevens**: de weergave naam van de uitgever, de contact persoon van de verkoper (naam, e-mail, telefoon nummer en adres) en de fiatteur van het bedrijf (naam, e-mail, telefoon nummer)

### <a name="account-settings---developer-tab"></a>Account instellingen-tabblad ontwikkel aars

In de volgende informatie wordt de informatie op het tabblad ontwikkelaar beschreven.

#### <a name="account-details"></a>Account gegevens

In de sectie _account Details_ van het tabblad _ontwikkelaar_ kunt u basis informatie bekijken, zoals uw **account type** (bedrijf of individu) en de **verificatie status** van uw account. Tijdens uw account verificatie worden in deze instellingen elke vereiste stap weer gegeven, waaronder e-mail verificatie, werkgelegenheids verificatie en zakelijke verificatie.

#### <a name="publisher-ids"></a>Uitgevers-Id's

In de sectie uitgevers-Id's ziet u uw **Symantec-id** (indien van toepassing) **, verkoper-** ID, **gebruikers-id**, **MPN-id** en **Azure AD-tenants**. Deze waarden worden door micro soft toegewezen om uw ontwikkelaars account uniek te identificeren en kunnen niet worden bewerkt.

Als u geen Symantec-ID hebt, kunt u de koppeling selecteren om er een te aanvragen.

### <a name="contact-info"></a>Contactgegevens

In de sectie _contact gegevens_ ziet u de **weergave naam** van de uitgever, de **contact gegevens** van de verkoper (de naam van de contact persoon, het e-mail adres en het telefoon nummer van de verkoper van het bedrijf) en de door het **bedrijf gefiatteurde** persoon (de naam, de e-mail en het telefoon nummer van de persoon met autoriteit voor het goed keuren van beslissingen voor het bedrijf)

U kunt ook de koppeling **bijwerken** selecteren om uw contact gegevens te wijzigen, zoals de weergave naam en het e-mail adres van de uitgever.

### <a name="account-settings-identifiers"></a>Account instellingen-id's

Onder **account instellingen**  >  **organisatie profiel** selecteert u **id's** om de volgende informatie weer te geven:

- **MPN-id's**: alle MPN-id's die aan uw account zijn gekoppeld
- **CSP**: MPN-id's die zijn gekoppeld aan het CSP-programma voor dit account.
- **Uitgever**: verkoper-id's die zijn gekoppeld aan uw account
- **Tracking-guid's**: alle tracerings-guid's die aan uw account zijn gekoppeld

#### <a name="tracking-guids"></a>Tracering van GUID'S

GUID'S (Globally Unique Identifiers) zijn unieke referentie nummers (met 32 hexadecimale cijfers) die kunnen worden gebruikt voor het bijhouden van uw Azure-gebruik.

Als u GUID'S voor bijhouden wilt maken, moet u een GUID-generator gebruiken. Het Azure Storage team heeft een [GUID-Generator formulier](https://aka.ms/StoragePartners) gemaakt dat u een GUID van de juiste indeling stuurt en kan worden hergebruikt in de verschillende tracking systemen.

We raden u aan om voor elk product een unieke GUID te maken voor elk aanbod en distributie kanaal. U kunt ervoor kiezen om een enkele GUID voor de meerdere distributie kanalen van het product te gebruiken als u niet wilt dat rapportage wordt gesplitst.

Als u een product implementeert met behulp van een sjabloon en deze beschikbaar is op zowel Azure Marketplace als op GitHub, kunt u twee afzonderlijke GUID'S maken en registreren:

- Product A in azure Marketplace
- Product A op GitHub

Rapportage wordt uitgevoerd door de partner waarde (micro soft-partner-ID) en de GUID'S. U kunt ook GUID'S op een meer gedetailleerd niveau bijhouden voor elk abonnement binnen uw aanbieding.

Zie de [Veelgestelde vragen over het bijhouden van Azure-klanten met guid's](azure-partner-customer-usage-attribution.md#faq)voor meer informatie.

### <a name="agreements"></a>Overeenkomsten

Op de pagina **overeenkomsten** kunt u een lijst bekijken met de publicatie overeenkomsten die u hebt geautoriseerd. Deze overeenkomsten worden vermeld op basis van naam en versie nummer, met inbegrip van de datum waarop deze is geaccepteerd en de naam van de gebruiker die de overeenkomst heeft geaccepteerd.

Voor toegang tot de pagina overeenkomsten:

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
1. Selecteer in de rechter bovenhoek **instellingen**  >  **account instellingen**.
1. Selecteer onder **account instellingen** de optie **overeenkomsten**.

Als er updates voor overeenkomsten zijn die uw aandacht nodig hebben, kunnen er boven aan deze pagina **acties** worden weer gegeven. Als u een bijgewerkte overeenkomst wilt accepteren, moet u eerst de versie van de gekoppelde overeenkomst lezen en vervolgens **overeenkomst accepteren** selecteren.

## <a name="set-up-a-payout-profile"></a>Een uitbetalings profiel instellen

Een uitbetalings profiel is de Bank rekening waarnaar de opbrengsten worden verzonden vanuit uw verkoop. Deze bank rekening moet zich in hetzelfde land of dezelfde regio bevinden waar u uw partner centrum-account hebt geregistreerd. Zie voor meer informatie over een uitzonderings profiel [stimulering-uitbetalingen en BTW-profielen maken en beheren in partner centrum](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) en [uw uitbetalings account en BTW-formulieren instellen](/partner-center/set-up-your-payout-account).

Uw uitbetalings profiel instellen:

1. Ga naar de [overzichts pagina voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) in partner centrum.
2. Selecteer in de sectie **profiel** naast **profiel voor uitbetaling** de optie **bijwerken**.
3. **Kies een betalings wijze**: Bank account of PayPal.
4. **Betalings gegevens toevoegen**: dit kan onder andere het kiezen van een account type (controleren of sparen), de naam van de account houder, het account nummer en het route nummer, het facturerings adres, het telefoon nummer of het e-mail adres van PayPal opgeven. Zie [PayPal-info](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)voor meer informatie over het gebruik van PayPal als betalings methode voor uw account en om te bepalen of het in uw markt of regio wordt ondersteund.

> [!IMPORTANT]
> Het wijzigen van uw uitbetalings account kan uw betalingen vertragen met Maxi maal één betalings cyclus. Deze vertraging treedt op omdat de account wijziging moet worden gecontroleerd, net zoals bij de eerste instelling van het uitbetalings account. U ontvangt nog steeds voor het volledige bedrag nadat uw account is geverifieerd. alle betalingen die moeten worden uitgevoerd voor de huidige betalings cyclus, worden toegevoegd aan de volgende betaling.  

## <a name="tax-profile"></a>BTW-profiel

Controleer de huidige status van het BTW-profiel en bevestig dat het juiste **entiteits type** en de gegevens van het **BTW-certificaat** worden weer gegeven. Selecteer **bewerken** om de vereiste formulieren bij te werken of te volt ooien.

Als u uw belasting status wilt bepalen, moet u het land of de regio van de woon plaats en het burgerschap opgeven en de juiste BTW-formulieren volt ooien die zijn gekoppeld aan uw land of regio.

Ongeacht het land of de regio waar u woont of burgerschap hebt, moet u Verenigde Staten BTW-formulieren invullen om aanbiedingen via micro soft te verkopen. Partners die voldoen aan bepaalde Verenigde Staten locatie-vereisten, moeten een IRS W-9-formulier invullen. Andere partners buiten de Verenigde Staten moeten een IRS W-8-formulier invullen. U kunt deze formulieren online invullen tijdens het volt ooien van uw BTW-profiel.

Een Verenigde Staten afzonderlijke belastingplichtige-id (of ITIN) is niet vereist voor het ontvangen van betalingen van micro soft of voor het claimen van de voor delen van belasting verdragen.

U kunt uw belasting formulieren elektronisch volt ooien en verzenden in het partner centrum. in de meeste gevallen hoeft u geen formulieren af te drukken en te verzenden.

Verschillende landen en regio's hebben verschillende belasting vereisten. De exacte hoeveelheid die u in BTW moet betalen, is afhankelijk van de landen en regio's waar u uw aanbiedingen verkoopt. Micro soft verdeelt de omzet en maakt gebruik van de belasting namens u in sommige landen en regio's. Deze landen en regio's worden geïdentificeerd in het proces van het aanbieden van uw aanbieding. In andere landen en regio's, afhankelijk van waar u bent Inge schreven, moet u mogelijk verkoop-en gebruiks belasting voor uw verkoop rechtstreeks aan de lokale belasting dienst remitteren. Daarnaast kan de verkoop opbrengst worden belast als bate. We raden u aan om contact op te nemen met de relevante instantie voor uw land of regio, die u het beste kan helpen bij het bepalen van de juiste belasting gegevens voor uw micro soft-verkoop transacties.

Zie voor meer informatie over een BTW-profiel [prikkel toekenning en BTW-profielen maken en beheren in partner centrum](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) en [uw uitbetalings account en BTW-formulieren instellen](/partner-center/set-up-your-payout-account).

### <a name="withholding-rates"></a>Inhoudings tarieven

De gegevens die u in uw belasting formulieren verzendt, bepalen het juiste tarief voor belasting heffing. Het inhoudings bedrag geldt alleen voor verkopen die u in de Verenigde Staten. verkoop van niet-Amerikaanse locaties is niet van toepassing op inhoud. De inhoudings tarieven variëren, maar voor de meeste ontwikkel aars die buiten het Verenigde Staten worden geregistreerd, is de standaard tarief 30%. U hebt de mogelijkheid om dit tarief te verlagen als uw land of regio is overeengekomen met de Verenigde Staten.

### <a name="tax-treaty-benefits"></a>Voor delen van belasting verdrag

Als u zich buiten het Verenigde Staten bevindt, kunt u profiteren van de voor delen van belasting verdragen. Deze voor delen variëren van land/regio tot land/regio en kunnen u de belasting hoeveelheid die micro soft inhoudt, beperken. U kunt voor delen van fiscale verdragen claimen door deel II van het formulier W-8BEN te volt ooien. We raden u aan om te communiceren met de juiste resources in uw land of regio om te bepalen of deze voor delen van toepassing zijn op u.

Meer [informatie over belasting gegevens voor Windows app/Game-ontwikkel aars en Azure Marketplace-uitgevers](/windows/uwp/publish/tax-details-for-paid-apps).

### <a name="payout-hold-status"></a>Status uitbetalings blokkering

Micro soft verzendt standaard betalingen op maand basis. U kunt eventueel ook uw uitbetalingen in de wacht zetten, waardoor het verzenden van betalingen naar uw account wordt voor komen. Als u ervoor kiest om uw uitbetalingen in de wacht te zetten, blijven we de omzet die u behaalt vastleggen en de details in de **samen vatting** van de betaling opgeven. Er worden echter geen betalingen naar uw account verzonden totdat u de blok kering verwijdert.

**Uw betalingen in de wacht zetten**:

1. Ga naar **account instellingen**. 
1. Vouw in het linkerdeel venster van links **uitbetaling en belasting** uit en selecteer **uitbetaling en BTW-profielen**.
1. Selecteer het programma waarvoor u betalingen wilt houden en schakel vervolgens het selectie vakje **mijn betaling bewaren** in.

U kunt de status van uw uitbetalings blokkering op elk gewenst moment wijzigen, maar houd er rekening mee dat uw beslissing van invloed is op de volgende maandelijkse uitbetaling. Als u bijvoorbeeld de uitbetaling van april wilt bewaren, moet u ervoor zorgen dat u vóór het einde van maart de status van uw uitbetalings blokkering hebt ingesteld **op aan.**

Nadat u de status van de uitbetalings blokkering hebt ingesteld op **aan**, worden alle uitbetalingen in de wacht stand **gezet** totdat u de schuif regelaar weer inschakelt. Wanneer u dit doet, wordt u opgenomen in de volgende maandelijkse uitbetalings cyclus (op voor waarde dat er aan de toepasselijke betalings drempels is voldaan). Als u bijvoorbeeld uw uitbetalingen in wacht hebt, maar graag een toekenning wilt genereren die in juni is gegenereerd, moet u ervoor zorgen dat de status van de uitbetalings blokkering wordt **uitgeschakeld** voor het einde van mei.

> [!NOTE]
> De status selectie van uw **uitbetalings blokkering** geldt voor **alle** opbrengst bronnen die worden betaald via micro soft Partner Center, waaronder Azure Marketplace, Microsoft AppSource, Microsoft Store, reclame, enzovoort.) U kunt voor elke opbrengst bron niet verschillende wacht statussen selecteren.

## <a name="devices"></a>Apparaten

De instellingen voor Apparaatbeheer zijn alleen van toepassing op UWP-publicatie (Universal Windows platform). [Meer informatie](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Een facturerings profiel maken

Als u een [Dynamics 365 voor klant betrokkenheid publiceert & Power apps](./partner-center-portal/create-new-customer-engagement-offer.md) of [Dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md) -aanbieding, moet u uw *facturerings profiel* volt ooien.

Het factuur adres is vooraf ingevuld op basis van uw rechts persoon en u kunt dit adres later bijwerken. De velden BTW-ID en BTW-nummer zijn vereist voor sommige landen en optioneel voor anderen. De land/regio naam en bedrijfs naam kunnen niet worden bewerkt.

1. Ga naar **account instellingen**.
1. Vouw vervolgens in het linkerdeel venster **organisatie profiel** en selecteer **facturerings profiel** in.


## <a name="multi-user-account-management"></a>Beheer van meerdere gebruikers accounts

Het partner centrum gebruikt [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) voor toegang tot en beheer van meerdere gebruikers accounts. De Azure AD van uw organisatie wordt automatisch gekoppeld aan uw partner centrum-account als onderdeel van het inschrijvings proces.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers toevoegen en beheren](add-manage-users.md)
