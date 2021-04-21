---
title: Een commerciële marketplace-account beheren in Microsoft Partner Center - Azure Marketplace
description: Meer informatie over het beheren van een commerciële marketplace-account in Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: 6b721e7acb7907743c0696aff6c11ad59f5ceba9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812566"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Uw commerciële marketplace-account beheren in Partner Center

**Juiste rollen**

- Eigenaar
- Manager

Zodra u een account [voor Partner Center hebt gemaakt,](./create-account.md)kunt u het dashboard van de commerciële [marketplace gebruiken om](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) uw account en aanbiedingen te beheren.

## <a name="access-your-account-settings"></a>Toegang tot uw accountinstellingen

Als u dit nog niet hebt gedaan, moet u (of de beheerder van uw organisatie) toegang hebben tot de [accountinstellingen](https://partner.microsoft.com/dashboard/account/management) voor uw Partner Center account.

1. Meld u aan bij [het dashboard van](https://partner.microsoft.com/dashboard/commercial-marketplace) de commerciële marketplace in Partner Center met het account dat u wilt openen. Als u deel uitmaakt van meerdere accounts en zich hebt aangemeld met een ander account, kunt u [van account wisselen.](switch-accounts.md)
1. Selecteer in de rechterbovenbalk **Instellingen** (tandwielpictogram) en selecteer vervolgens **Accountinstellingen.**

    [![Schermopname van het menu Accountinstellingen in Partner Center. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. Selecteer **onder Accountinstellingen** de **optie Juridisch.** Selecteer vervolgens het tabblad **Ontwikkelaar** om details weer te geven met betrekking tot uw commerciële marketplace-account.

    [![Schermopname van het tabblad Ontwikkelaar op de juridische pagina in Accountinstellingen. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Pagina Accountinstellingen

Wanneer u Instellingen **selecteert** en **Accountinstellingen uitv vouwt,** is de standaardweergave **Juridische gegevens.** Deze pagina kan maximaal drie tabbladen hebben, afhankelijk van de programma's die u hebt geregistreerd bij: _Partner,_ _Reseller_ en _Developer._

Het **tabblad Partner** voor partners die zijn ingeschreven bij MPN bevat:

- Alle juridische bedrijfsgegevens, zoals geregistreerde juridische naam en adres voor uw bedrijf
- Primaire contactpersoon
- Bedrijfslocaties.

Het **tabblad Reseller,** voor partners die CSP-activiteiten doen, bevat:

- Primaire contactgegevens
- Klantondersteuningsprofiel
- Programma-informatie

Het **tabblad** Ontwikkelaar, voor partners die zijn ingeschreven bij een ontwikkelaarsprogramma, heeft de volgende informatie:

- **Accountdetails:** Accounttype en Accountstatus
- **Uitgevers-id's:** verkoper-id, gebruikers-id, uitgevers-id, Azure AD-tenants en meer
- **Contactgegevens:** Weergavenaam van uitgever, verkopercontact (naam, e-mailadres, telefoonnummer en adres) en Fiattur van het bedrijf (naam, e-mailadres, telefoonnummer)

### <a name="account-settings---developer-tab"></a>Accountinstellingen - tabblad Ontwikkelaar

De volgende informatie beschrijft de informatie op het tabblad Ontwikkelaar.

#### <a name="account-details"></a>Accountdetails

In de _sectie Accountgegevens_ van _het_ tabblad Ontwikkelaar ziet u basisgegevens, zoals uw **accounttype** (bedrijf of individu) en de **verificatiestatus** van uw account. Tijdens het verificatieproces van uw account geven deze instellingen elke vereiste stap weer, met inbegrip van e-mailverificatie, verificatie van dienstverbanden en bedrijfsverificatie.

#### <a name="publisher-ids"></a>Uitgevers-ID's

In de sectie Uitgevers-id's ziet u uw **Symantec-id** (indien van toepassing), **verkoper-id,** gebruikers-id,  **MPN-id** en Azure **AD-tenants.** Deze waarden worden door Microsoft toegewezen om uw ontwikkelaarsaccount uniek te identificeren en kunnen niet worden bewerkt.

Als u geen Symantec-id hebt, kunt u de koppeling selecteren om er een aan te vragen.

### <a name="contact-info"></a>Contactgegevens

In  de sectie Contactgegevens ziet u de weergavenaam van de **uitgever,** de contactgegevens van de verkoper **(de** contactgegevens van de contactpersoon voor de contactpersoon, het e-mailadres, het telefoonnummer en het adres van de verkoper van het bedrijf) en de fiattelaar **van** het bedrijf (de naam, het e-mailadres en het telefoonnummer van de persoon met de bevoegdheid om beslissingen voor het bedrijf goed te keuren).

U kunt ook de koppeling **Bijwerken selecteren** om uw contactgegevens te wijzigen, zoals de weergavenaam en het e-mailadres van de uitgever.

### <a name="account-settings-identifiers"></a>Accountinstellingen-id's

Selecteer **onder Accountinstellingen**  >  **Organisatieprofiel** de optie **Id's** om de volgende informatie weer te geven:

- **MPN-ID's:** alle MPN-ID's die zijn gekoppeld aan uw account
- **CSP:** MPN-ID's die zijn gekoppeld aan het CSP-programma voor dit account.
- **Uitgever:** verkoper-ID's die zijn gekoppeld aan uw account
- **Tracerings-GUID's:** alle tracerings-GUID's die zijn gekoppeld aan uw account

#### <a name="tracking-guids"></a>GUID's bijhouden

Globally Unique Identifiers (GUID's) zijn unieke referentienummers (met 32 hexadecimale cijfers) die kunnen worden gebruikt voor het bijhouden van uw Azure-gebruik.

Als u GUID's wilt maken voor tracering, moet u een GUID-generator gebruiken. Het Azure Storage heeft een formulier voor [de GUID-generator](https://aka.ms/StoragePartners) gemaakt dat u een GUID met de juiste indeling e-mailt en die opnieuw kan worden gebruikt in de verschillende traceringssystemen.

U wordt aangeraden voor elk product een unieke GUID te maken voor elke aanbieding en elk distributiekanaal. U kunt ervoor kiezen om één GUID te gebruiken voor meerdere distributiekanalen van het product als u niet wilt dat rapportage wordt gesplitst.

Als u een product implementeert met behulp van een sjabloon en het beschikbaar is op zowel de Azure Marketplace als op GitHub, kunt u twee afzonderlijke GUID's maken en registreren:

- Product A in Azure Marketplace
- Product A op GitHub

Rapportage wordt uitgevoerd door de partnerwaarde (Microsoft partner-id) en de GUID's. U kunt GUID's ook volgen op een gedetailleerder niveau dat is afgestemd op elk plan binnen uw aanbieding.

Zie Tracking Azure [customer usage with GUIDs FAQ (Het gebruik van Azure-klanten bijhouden met GUID's) voor meer informatie.](azure-partner-customer-usage-attribution.md#faq)

### <a name="agreements"></a>Overeenkomsten

Op **de** pagina Overeenkomsten kunt u een lijst weergeven met de publicatieovereenkomsten die u hebt geautoriseerd. Deze overeenkomsten worden vermeld op basis van de naam en het versienummer, met inbegrip van de datum waarop deze is geaccepteerd en de naam van de gebruiker die de overeenkomst heeft geaccepteerd.

Voor toegang tot de pagina Overeenkomsten:

1. Meld u aan [bij Partner Center](https://partner.microsoft.com/dashboard/home).
1. Selecteer in de rechterboven selecteer **Instellingen**  >  **Accountinstellingen.**
1. Selecteer overeenkomsten onder **Accountinstellingen.** 

**Acties die** nodig zijn, kunnen boven aan deze pagina worden weergegeven als er overeenkomstupdates zijn die uw aandacht nodig hebben. Als u een bijgewerkte overeenkomst wilt accepteren, leest u eerst de gekoppelde overeenkomstversie en selecteert **u vervolgens Overeenkomst accepteren.**

## <a name="set-up-a-payout-profile"></a>Een uitbetalingsprofiel instellen

Een uitbetalingsprofiel is de bankrekening waaraan de omzet van uw verkoop wordt verzonden. Dit bankaccount moet zich in hetzelfde land of dezelfde regio hebben als waar u uw Partner Center hebt geregistreerd. Zie Uitbetalings- en belastingprofielen voor incentives maken en beheren [in](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) Partner Center en Uw uitbetalingsaccount en belastingformulieren instellen voor meer informatie over een [uitbetalingsprofiel.](/partner-center/set-up-your-payout-account)

Uw uitbetalingsprofiel instellen:

1. Ga naar de [overzichtspagina van de](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) commerciële marketplace in Partner Center.
2. Selecteer in **de sectie** Profiel, naast **Uitbetalingsprofiel,** de optie **Bijwerken.**
3. **Kies een betalingswijze:** bankrekening of PayPal.
4. **Betalingsgegevens toevoegen:** dit kan bestaan uit het kiezen van een accounttype (controle of besparingen), het invoeren van de naam van de accounthouder, het accountnummer en het routeringsnummer, het factureringsadres, het telefoonnummer of het e-mailadres van PayPal. Zie PayPal info voor meer informatie over het gebruik van PayPal als betalingswijze voor uw account en om erachter te komen of deze wordt ondersteund in uw markt of [regio.](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)

> [!IMPORTANT]
> Het wijzigen van uw uitbetalingsaccount kan uw betalingen met maximaal één betalingscyclus vertragen. Deze vertraging treedt op omdat we de accountwijziging moeten controleren, net zoals bij het instellen van het uitbetalingsaccount. U wordt nog steeds betaald voor het volledige bedrag nadat uw account is geverifieerd; alle betalingen die voor de huidige betalingscyclus moeten worden betaald, worden toegevoegd aan de volgende.  

## <a name="tax-profile"></a>Belastingprofiel

Controleer de status van uw huidige belastingprofiel en controleer of het **juiste entiteitstype en** de gegevens van het **belastingcertificaat** worden weergegeven. Selecteer **Bewerken om** alle vereiste formulieren bij te werken of in te vullen.

Als u uw belastingstatus wilt vaststellen, moet u uw land of regio opgeven voor herkomst en herkomst en de juiste belastingformulieren invullen die zijn gekoppeld aan uw land of regio.

Ongeacht uw land of regio waar u werkt, moet u uw belastingformulieren Verenigde Staten om aanbiedingen via Microsoft te verkopen. Partners die voldoen aan bepaalde Verenigde Staten vereisten voor de lokale gegevens, moeten een IRS W-9-formulier invullen. Andere partners buiten de Verenigde Staten moeten een IRS W-8-formulier invullen. U kunt deze formulieren online invullen wanneer u uw belastingprofiel voltooit.

Een Verenigde Staten Individual Identification Number (of ITIN) is niet vereist om betalingen van Microsoft te ontvangen of belastingvoordelen te claimen.

U kunt uw belastingformulieren elektronisch invullen en verzenden in Partner Center; In de meeste gevallen hoeft u geen formulieren af te drukken en te e-mailen.

Verschillende landen en regio's hebben verschillende belastingvereisten. Het exacte bedrag dat u moet betalen in belastingen is afhankelijk van de landen en regio's waar u uw aanbiedingen verkoopt. Microsoft wijt verkoop toe en gebruikt namens u belasting in sommige landen en regio's. Deze landen en regio's worden geïdentificeerd tijdens het aanbieden van uw aanbieding. In andere landen en regio's moet u, afhankelijk van waar u bent geregistreerd, mogelijk de omzet en de belasting voor uw verkoop rechtstreeks aan de lokale belastingsinstantie betalen. Bovendien kan de omzet die u ontvangt, als inkomsten worden belast. We raden u ten zeerste aan contact op te nemen met de relevante instantie voor uw land of regio die u kan helpen bij het bepalen van de juiste belastinggegevens voor uw Microsoft-verkooptransacties.

Zie Uitbetalings- en belastingprofielen voor incentives maken en beheren [in](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) Partner Center en Uw uitbetalingsaccount en belastingformulieren instellen voor meer informatie over [een belastingprofiel.](/partner-center/set-up-your-payout-account)

### <a name="withholding-rates"></a>Holding-tarieven

De gegevens die u in uw belastingformulieren inzendt, bepalen het juiste belastingtarief. Het holdingtarief is alleen van toepassing op verkopen die u in de Verenigde Staten; verkoop in niet-Amerikaanse locaties is niet onderhevig aan holding. De holding-tarieven variëren, maar voor de meeste ontwikkelaars die zich buiten de Verenigde Staten registreren, is het standaardpercentage 30%. U hebt de mogelijkheid om dit tarief te verlagen als uw land of regio akkoord is gegaan met een btw-tarief met de Verenigde Staten.

### <a name="tax-treaty-benefits"></a>Voordelen van belastingvoordelen

Als u zich buiten de Verenigde Staten, kunt u mogelijk profiteren van belastingvoordelen. Deze voordelen variëren per land/regio en u kunt de belasting die Microsoft int verlagen. U kunt belastingvoordelen claimen door deel II van het formulier W-8BEN in te vullen. U wordt aangeraden te communiceren met de juiste resources in uw land of regio om te bepalen of deze voordelen op u van toepassing zijn.

[Meer informatie over belastinggegevens voor windows-app-/gameontwikkelaars en Azure Marketplace uitgevers.](/windows/uwp/publish/tax-details-for-paid-apps)

### <a name="payout-hold-status"></a>Wachtstatus uitbetaling

Standaard verzendt Microsoft betalingen op maandelijkse basis. U kunt uw uitbetalingen optioneel echter in de wacht zetten, waardoor het verzenden van betalingen naar uw account wordt voorkomen. Als u ervoor kiest om uw uitbetalingen in de wacht te zetten, blijven we eventuele inkomsten die u verdient registreren en geven we de details op in uw **uitbetalingsoverzicht.** We sturen echter geen betalingen naar uw account totdat u de wachtstand hebt verwijderd.

**Uw betalingen in de wacht plaatsen:**

1. Ga naar **Accountinstellingen.** 
1. Vouw in het **linkernavigatiebalkje Uitbetaling en belasting uit** en selecteer **Uitbetalings- en belastingprofielen.**
1. Selecteer het programma waarvoor u betalingen wilt houden en schakel vervolgens het selectievakje **Mijn betaling** in de wacht houden in.

U kunt de status van uw uitbetalings hold op elk moment wijzigen, maar houd er rekening mee dat uw beslissing invloed heeft op de volgende maandelijkse uitbetaling. Als u bijvoorbeeld de uitbetaling van april wilt behouden, moet u  vóór eind maart de status van uw uitbetalings hold instellen op Aan.

Nadat u de status van de uitbetalings hold hebt ingesteld op **Aan**, worden alle uitbetalingen in de wacht gezet totdat u de schuifregelaar weer op **Uit zet.** Wanneer u dit doet, wordt u opgenomen tijdens de volgende maandelijkse uitbetalingscyclus (mits aan de toepasselijke betalingsdrempels is voldaan). Als u bijvoorbeeld uw uitbetalingen in de wacht hebt gezet, maar een uitbetaling wilt genereren in juni,  moet u de status van de uitbetalingsstatus vóór eind mei in- of uitschakelen.

> [!NOTE]
> Uw selectie van **de** wachtstatus voor uitbetalingen is van toepassing op alle inkomstenbronnen die worden betaald via Microsoft Partner Center, waaronder Azure Marketplace, Microsoft AppSource, Microsoft Store, reclame, etc.).  U kunt geen verschillende wachtstatussen selecteren voor elke bron van omzet.

## <a name="devices"></a>Apparaten

De instellingen voor apparaatbeheer zijn alleen van toepassing op UWP-publicatie (Universal Windows Platform). [Meer informatie](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Een factureringsprofiel maken

Als u een [Dynamics 365 for Customer Engagement & Power Apps-](dynamics-365-customer-engage-offer-setup.md) of [Dynamics 365 for Operations-aanbieding](./partner-center-portal/create-new-operations-offer.md) publiceert, moet u uw *factureringsprofiel voltooien.*

Het factureringsadres wordt vooraf ingevuld vanuit uw juridische entiteit en u kunt dit adres later bijwerken. De velden BTW en BTW-id zijn vereist voor sommige landen en zijn optioneel voor andere landen. De naam van het land/de regio en de bedrijfsnaam kunnen niet worden bewerkt.

1. Ga naar **Accountinstellingen.**
1. Vouw vervolgens in de linkernavigatie **organisatieprofiel uit** en selecteer **Factureringsprofiel.**


## <a name="multi-user-account-management"></a>Accountbeheer voor meerdere gebruikers

Partner Center maakt gebruik [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) voor accounttoegang en -beheer voor meerdere gebruikers. De Azure AD van uw organisatie wordt automatisch gekoppeld aan uw Partner Center-account als onderdeel van het inschrijvingsproces.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers toevoegen en beheren](add-manage-users.md)
