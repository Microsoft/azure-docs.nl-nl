---
title: Een implementatie Azure Active Directory toegangsbeoordelingen plannen
description: Planningshandleiding voor een geslaagde implementatie van toegangsbeoordelingen
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af783d7ff8be36c63af871ab4f2d214ca9f9405
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532584"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Implementatie Azure Active Directory toegangsbeoordelingen plannen

[Azure Active Directory (Azure AD) Access Reviews](access-reviews-overview.md) helpt uw organisatie het netwerk beter te beveiligen door de levenscyclus van [de toegang tot resources te beheren.](identity-governance-overview.md) Met toegangsbeoordelingen kunt u het volgende doen:

* Regelmatige beoordelingen plannen of ad-hocbeoordelingen uitvoeren om te zien wie toegang heeft tot specifieke resources, zoals toepassingen en groepen

* Beoordelingen bijhouden om redenen van inzichten, naleving of beleid

* Beoordelingen delegeren aan specifieke beheerders, bedrijfseigenaren of eindgebruikers die zelf kunnen bevestigen dat ze nog steeds toegang nodig hebben

* Gebruik de inzichten om efficiënt te bepalen of gebruikers toegang moeten blijven hebben

* Controleresultaten automatiseren, zoals het verwijderen van de toegang van gebruikers tot resources

  ![Diagram met de stroom voor toegangsbeoordelingen.](./media/deploy-access-review/1-planning-review.png)

Toegangsbeoordelingen is [een Azure AD Identity Governance](identity-governance-overview.md) mogelijkheid. De andere mogelijkheden zijn [Rechtenbeheer,](entitlement-management-overview.md) [Privileged Identity Management](../privileged-identity-management/pim-configure.md) en [Gebruiksvoorwaarden.](../conditional-access/terms-of-use.md) Samen helpen ze organisaties bij het beantwoorden van deze vier vragen:

* Welke gebruikers moeten toegang hebben tot welke resources?

* Wat doen gebruikers met die toegang?

* Is er effectief organisatiebeheer voor het beheren van toegang?

* Kunnen auditors controleren of de beheeropties werken?

Het plannen van uw implementatie van toegangsbeoordelingen is essentieel om ervoor te zorgen dat u de gewenste governancestrategie voor gebruikers in uw organisatie bereikt.

### <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voordelen van het inschakelen van toegangsbeoordelingen zijn:

* **Samenwerking controleren.** Met toegangsbeoordelingen kunnen organisaties de toegang beheren tot alle resources die de gebruikers nodig hebben. Wanneer hun gebruikers delen en samenwerken, kunnen organisaties er zeker van zijn dat de informatie alleen onder geautoriseerde gebruikers valt.

* **Beheer risico**. Toegangsbeoordelingen bieden organisaties een manier om de toegang tot gegevens en toepassingen te controleren, waardoor het risico op gegevenslekken en lekkage van gegevens wordt verlaagd. Dit omvat mogelijkheden om regelmatig de toegang van externe partners tot bedrijfsresources te controleren. 

* **Adres naleving en governance.** Met Toegangsbeoordelingen kunt u de toegangslevenscyclus voor groepen, apps en sites beheren en opnieuw certificeren. U kunt controle houden over beoordelingen voor naleving of risicogevoelige toepassingen die specifiek zijn voor uw organisatie. 

* **Kosten verlagen.** Toegangsbeoordelingen zijn ingebouwd in de cloud en werken systeemeigen met cloudbronnen zoals groepen, toepassingen en toegangspakketten. Het gebruik van toegangsbeoordelingen is goedkoper dan het bouwen van uw eigen hulpprogramma's of een andere upgrade van uw on-premises toolset.

### <a name="training-resources"></a>Trainingsbronnen

De volgende video's kunnen nuttig zijn wanneer u meer te weten komt over toegangsbeoordelingen:

* [Wat zijn toegangsbeoordelingen in Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Toegangsbeoordelingen maken in Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Toegangsbeoordelingen inschakelen in Azure AD](https://youtu.be/X1SL2uubx9M)

* [Toegang controleren met behulp van Mijn toegang](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenties

U hebt een geldige Azure AD Premium (P2) nodig voor elke persoon, met andere dan globale beheerders of gebruikersbeheerders, die toegangsbeoordelingen maken of uitvoeren. Zie Licentievereisten voor [toegangsbeoordelingen voor meer informatie.](access-reviews-overview.md)

Mogelijk hebt u ook andere identity governance-functies nodig, zoals [Rechtenlevenscyclusbeheer](entitlement-management-overview.md) of Privileged Identity Managements. In dat geval hebt u mogelijk ook gerelateerde licenties nodig. Zie prijzen Azure Active Directory [voor meer informatie.](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="plan-the-access-reviews-deployment-project"></a>Het implementatieproject Voor toegangsbeoordelingen plannen

Houd er rekening mee dat uw organisatie de strategie moet bepalen voor het implementeren van toegangsbeoordelingen in uw omgeving.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden betrekken

Wanneer technologieprojecten mislukken, doen ze dit meestal vanwege niet-overeenkomende verwachtingen over impact, resultaten en verantwoordelijkheden. Om deze valkuilen te voorkomen, moet u ervoor zorgen dat [u de](../fundamentals/active-directory-deployment-plans.md) juiste belanghebbenden in de arm hebt en dat de projectrollen duidelijk zijn.

Voor toegangsbeoordelingen moet u waarschijnlijk vertegenwoordigers van de volgende teams binnen uw organisatie opnemen:

* **IT-beheer** beheert uw IT-infrastructuur en beheert uw cloudinvesteringen en SaaS-apps (Software as a Service). Dit team gaat het volgende doen:

   * Bevoorrechte toegang tot infrastructuur en apps controleren, Microsoft 365 en Azure AD.

   * Plan en voer toegangsbeoordelingen uit voor groepen die worden gebruikt voor het onderhouden van uitzonderingslijsten of IT-proefprojecten, om up-to-date toegangslijsten te onderhouden.

   * Zorg ervoor dat programmatische (met scripts) toegang tot resources via service-principals wordt beheerd en gecontroleerd.

* **Ontwikkelteams** bouwen en onderhouden toepassingen voor uw organisatie. Dit team gaat het volgende doen:

   * Bepalen wie toegang heeft tot en onderdelen kan beheren in SaaS-, PaaS- en IaaS-resources waar de ontwikkelde oplossingen deel van uit maken.

   * Groepen beheren die toegang hebben tot toepassingen en hulpprogramma's voor het ontwikkelen van interne toepassingen.

   * Bevoorrechte identiteiten vereisen die toegang hebben tot productiesoftware of oplossingen die worden gehost voor uw klanten

* **Bedrijfseenheden beheren** projecten en eigen toepassingen. Dit team gaat het volgende doen: 

   * Toegang tot groepen en toepassingen voor interne en externe gebruikers controleren en goedkeuren of weigeren.

   * Plan en voer beoordelingen uit om de voortdurende toegang voor werknemers en externe identiteiten, zoals zakelijke partners, te bevestigen.

* **Bedrijfsgovernance** zorgt ervoor dat de organisatie het interne beleid volgt en voldoet aan de regelgeving. Dit team gaat het volgende doen:

   * Nieuwe toegangsbeoordelingen aanvragen of plannen.

   * Evalueer processen en procedures voor het controleren van toegang, waaronder documentatie en het bewaren van records voor naleving.

   * Bekijk de resultaten van eerdere beoordelingen voor de meest kritieke resources.

> [!NOTE]
> Voor beoordelingen waarvoor handmatige evaluaties zijn vereist, moet u ervoor zorgen dat u voldoende revisoren en beoordelingscycli hebt die voldoen aan uw beleids- en nalevingsbehoeften. Als beoordelingscycli te vaak voorkomen of als er te weinig revisoren zijn, gaat de kwaliteit verloren en hebben te veel of te weinig mensen toegang. 

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is essentieel voor het succes van een nieuw bedrijfsproces. Proactief communiceren met gebruikers over hoe en wanneer hun ervaring zal veranderen en hoe ze ondersteuning kunnen krijgen als ze problemen ervaren. 

#### <a name="communicate-changes-in-accountability"></a>Wijzigingen in aansprakelijkheid communiceren

Toegangsbeoordelingen bieden ondersteuning voor het verschuiven van de verantwoordelijkheid voor het beoordelen en handelen bij voortdurende toegang tot bedrijfseigenaren. Het ontkoppelen van toegangsbeslissingen van IT zorgt voor nauwkeurigere toegangsbeslissingen. Dit is een culturele verandering in de verantwoordelijkheid en verantwoordelijkheid van de resource-eigenaar. Communiceer deze wijziging proactief en zorg ervoor dat resource-eigenaren worden getraind en de inzichten kunnen gebruiken om goede beslissingen te nemen.

Het is duidelijk dat IT de controle wil houden over alle infrastructuurgerelateerde toegangsbeslissingen en bevoorrechte roltoewijzingen. 

#### <a name="customize-email-communication"></a>E-mailcommunicatie aanpassen

Wanneer u een beoordeling inplant, benoemt u gebruikers die deze beoordeling zullen uitvoeren. Deze revisoren ontvangen vervolgens een e-mailmelding over nieuwe beoordelingen die aan hen zijn toegewezen, evenals herinneringen voordat een beoordeling die aan hen is toegewezen, verloopt.

Beheerders kunnen ervoor kiezen om deze melding halverwege te verzenden voordat de beoordeling verloopt of een dag voordat deze verloopt. 

De e-mail die naar revisoren wordt verzonden, kan worden aangepast met een aangepast kort bericht waarin hen wordt aangemoedigd om actie te ondernemen voor de beoordeling. U wordt aangeraden de aanvullende tekst te gebruiken voor het volgende:

* Neem een persoonlijk bericht op naar revisoren, zodat ze begrijpen dat het wordt verzonden door uw compliance- of IT-afdeling.

* Neem een hyperlink of verwijzing op naar interne informatie over wat de verwachtingen van de beoordeling zijn en aanvullende naslaginformatie of trainingsmateriaal.

* Voeg een koppeling toe naar instructies [voor het uitvoeren van een zelfbeoordeling van toegang.](review-your-access.md) 

  ![E-mail revisor](./media/deploy-access-review/2-plan-reviewer-email.png)

Wanneer u Beoordeling starten selecteert, worden revisoren omgeleid naar [de myAccess-portal](https://myapplications.microsoft.com/) voor groeps- en toepassingstoegangsbeoordelingen. De portal biedt een overzicht van alle gebruikers die toegang hebben tot de resource die ze beoordelen, en systeemaanbevelingen op basis van de laatste aanmeldings- en toegangsgegevens.

### <a name="plan-a-pilot"></a>Een pilot plannen

We raden klanten aan om in eerste instantie toegangsbeoordelingen te piloten met een kleine groep en niet-kritieke resources te gebruiken. Piloting kan u helpen processen en communicatie zo nodig aan te passen en de mogelijkheid van gebruikers en revisoren om te voldoen aan beveiligings- en nalevingsvereisten te vergroten.

In uw testfase raden we het volgende aan:

* Begin met beoordelingen waarbij de resultaten niet automatisch worden toegepast en u de implicaties kunt bepalen.

* Zorg ervoor dat alle gebruikers geldige e-mailadressen hebben die worden vermeld in Azure AD en dat ze e-mailcommunicatie ontvangen om de juiste actie te ondernemen. 

* Documenter alle toegang die is verwijderd als onderdeel van de test voor het geval u deze snel moet herstellen.

* Controleer auditlogboeken om er zeker van te zijn dat alles op de juiste wijze wordt gecontroleerd.

Zie best practices [voor een pilot voor meer informatie.](../fundamentals/active-directory-deployment-plans.md)

## <a name="introduction-to-access-reviews"></a>Inleiding tot toegangsbeoordelingen

In deze sectie maakt u kennis met concepten van toegangsbeoordeling die u moet kennen voordat u uw beoordelingen plant.

### <a name="what-resource-types-can-be-reviewed"></a>Welke resourcetypen kunnen worden gecontroleerd?

Zodra u de resources van uw organisatie integreert met Azure AD (zoals gebruikers, toepassingen en groepen), kunnen ze worden beheerd en gecontroleerd. 

Typische controledoelen zijn:

* [Toepassingen die zijn geïntegreerd met Azure AD voor](../manage-apps/what-is-application-management.md) een een aanmelding (zoals SaaS, Line-Of-Business).

* [Groepslidmaatschap](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (gesynchroniseerd met Azure AD of gemaakt in Azure AD of Microsoft 365, waaronder Microsoft Teams).

* [Toegangspakket](./entitlement-management-overview.md) dat resources (groepen, apps en sites) groept in één pakket om de toegang te beheren.

* [Azure AD-rollen en Azure-resourcerollen](../privileged-identity-management/pim-resource-roles-assign-roles.md) zoals gedefinieerd in Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Wie maakt en beheert toegangsbeoordelingen?

De beheerdersrol die is vereist voor het maken, beheren of lezen van een toegangsbeoordeling, is afhankelijk van het type resource dat wordt gecontroleerd. De volgende tabel geeft de rollen aan die vereist zijn voor elk resourcetype:

| Resourcetype| Toegangsbeoordelingen maken en beheren (makers)| Resultaten van toegangsbeoordeling lezen |
| - | - | -|
| Groep of toepassing| Hoofdbeheerder <p>Gebruikersbeheerder| Makers en beveiligingsbeheerder |
| Bevoorrechte rollen in Azure AD| Hoofdbeheerder <p>Beheerder voor bevoorrechte rollen| makers <p>Beveiligingslezer<p>Beveiligingsbeheer |
| Bevoorrechte rollen in Azure (resources)| Hoofdbeheerder<p>Gebruikersbeheerder<p>Resource-eigenaar| makers |
| Toegangspakket| Hoofdbeheerder<p>Maker van toegangspakket| Alleen globale beheerder |


Zie Machtigingen voor beheerdersrol in Azure Active Directory voor [meer Azure Active Directory.](../roles/permissions-reference.md)

### <a name="who-will-review-the-access-to-the-resource"></a>Wie beoordeelt de toegang tot de resource?

De maker van de toegangsbeoordeling bepaalt op het moment van maken wie de beoordeling gaat uitvoeren. Deze instelling kan niet worden gewijzigd zodra de beoordeling is gestart. Revisoren worden vertegenwoordigd door drie persona's:

* Resource-eigenaren, die de bedrijfseigenaars van de resource zijn.

* Een set afzonderlijk geselecteerde gemachtigden, zoals geselecteerd door de beheerder van toegangsbeoordelingen.

* Eindgebruikers die elk zelf bevestigen dat ze toegang nodig hebben.

Bij het maken van een toegangsbeoordeling kunnen beheerders een of meer revisoren kiezen. Alle revisoren kunnen een beoordeling starten en uitvoeren, gebruikers kiezen voor verdere toegang tot een resource of ze verwijderen. 

### <a name="components-of-an-access-review"></a>Onderdelen van een toegangsbeoordeling

Voordat u uw toegangsbeoordelingen implementeert, moet u de typen beoordelingen plannen die relevant zijn voor uw organisatie. Als u dit wilt doen, moet u zakelijke beslissingen nemen over wat u wilt beoordelen en welke acties u moet ondernemen op basis van deze beoordelingen.

Als u een toegangsbeoordelingsbeleid wilt maken, moet u de volgende informatie hebben.

* Wat zijn de resources die moeten worden beoordeeld?

* Waarvan de toegang wordt gecontroleerd.

* Hoe vaak moet de beoordeling plaatsvinden?

* Wie voert de beoordeling uit?

   * Hoe worden ze hiervan op de hoogte gesteld?

   * Wat zijn de tijdlijnen die moeten worden afgedwongen voor beoordeling?

* Welke automatische acties moeten worden afgedwongen op basis van de beoordeling?

   * Wat gebeurt er als de revisor niet op tijd reageert?

* Welke handmatige acties worden als resultaat genomen op basis van de beoordeling?

* Welke communicatie moet worden verzonden op basis van ondernomen acties?


**Voorbeeld van een toegangsbeoordelingsplan**

| Onderdeel| Waarde |
| - | - |
| **Resources die moeten worden beoordeeld**| Toegang tot Microsoft Dynamics |
| **Frequentie controleren**| Maandelijks |
| **Wie beoordeling uitvoert**| Programmamanagers voor Dynamics-bedrijfsgroep |
| **Melding**| E-mail 24 uur vóór beoordeling naar alias Dynamics-Pms<p>Aangepaste berichten doorgeven aan revisoren om hun inkopen te beveiligen |
| **Tijdlijn**| 48 uur vanaf melding |
|**Automatische acties**| Verwijder toegang tot een account zonder interactieve aanmelding binnen 90 dagen door de gebruiker te verwijderen uit de dynamics-access van de beveiligingsgroep. <p>*Voer acties uit als deze niet binnen de tijdlijn worden gecontroleerd.* |
| **Handmatige acties**| Revisoren kunnen indien gewenst goedkeuring voor verwijderingen uitvoeren vóór geautomatiseerde actie. |
| **Communicatie**| Interne (lid)gebruikers verzenden die een e-mailbericht hebben verwijderd waarin wordt uitgelegd dat ze zijn verwijderd en hoe ze weer toegang kunnen krijgen. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Acties automatiseren op basis van toegangsbeoordelingen

U kunt ervoor kiezen om het verwijderen van toegang geautomatiseerd in te stellen door de optie Resultaten automatisch toepassen op resource in te stellen op Inschakelen.

  ![Toegangsbeoordelingen plannen](./media/deploy-access-review/3-automate-actions-settings.png)

Zodra de beoordeling is voltooid en is beëindigd, worden gebruikers die niet zijn goedgekeurd door de revisor automatisch verwijderd uit de resource of blijven ze toegang behouden. Dit kan betekenen dat hun groepslidmaatschap, hun toepassingstoewijzing of het inroepen van hun recht om naar een bevoorrechte rol te verhogen, moet worden opgeheven.

Aanbevelingen doen

De aanbevelingen worden weergegeven aan revisoren als onderdeel van de ervaring van de revisor en geven de laatste aanmelding van een persoon aan bij de tenant of de laatste toegang tot een toepassing. Deze informatie helpt revisoren de juiste toegangsbeslissing te nemen. Als u Aanbevelingen nemen selecteert, volgt u de aanbevelingen van de toegangsbeoordeling. Aan het einde van een toegangsbeoordeling past het systeem deze aanbevelingen automatisch toe voor gebruikers waar revisoren niet op hebben gereageerd.

Aanbevelingen zijn gebaseerd op de criteria in de toegangsbeoordeling. Als u de beoordeling bijvoorbeeld configureert om de toegang te verwijderen zonder interactieve aanmelding voor 90 dagen, wordt aanbevolen dat alle gebruikers die aan die criteria voldoen, worden verwijderd. Microsoft werkt voortdurend aan het verbeteren van aanbevelingen. 

### <a name="review-guest-user-access"></a>Toegang van gastgebruikers controleren

Gebruik Toegangsbeoordelingen om de identiteiten van samenwerkingspartners van externe organisaties te controleren en op te schonen. Configuratie van een beoordeling per partner kan voldoen aan nalevingsvereisten.

Externe identiteiten kunnen toegang krijgen tot bedrijfsbronnen via een van de volgende acties:

* Toegevoegd aan een groep 

* Uitgenodigd voor Teams 

* Toegewezen aan een bedrijfstoepassing of toegangspakket

* Een bevoorrechte rol toegewezen in Azure AD of in een Azure-abonnement

Zie [het voorbeeldscript](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Het script laat zien waar externe identiteiten die zijn uitgenodigd voor de tenant, worden gebruikt. U kunt het groepslidmaatschap van de externe gebruiker, roltoewijzingen en toepassingstoewijzingen in Azure AD zien. Het script geeft geen toewijzingen buiten Azure AD weer, bijvoorbeeld directe rechtentoewijzing aan Sharepoint-resources, zonder het gebruik van groepen.

Wanneer u een toegangsbeoordeling maakt voor groepen of toepassingen, kunt u ervoor kiezen om de revisor zich te laten focussen op Iedereen met toegang of Alleen gastgebruikers. Door alleen gastgebruikers te selecteren, krijgen revisoren een gerichte lijst met externe identiteiten van Azure AD B2B die toegang hebben tot de resource.

 ![Gastgebruikers controleren](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Dit omvat GEEN externe leden die een userType van lid hebben. Dit geldt ook niet voor gebruikers die buiten Azure AD B2B-samenwerking zijn uitgenodigd, bijvoorbeeld gebruikers die rechtstreeks via SharePoint toegang hebben tot gedeelde inhoud.

## <a name="plan-access-reviews-for-access-packages"></a>Toegangsbeoordelingen voor toegangspakketten plannen

[Met toegangspakketten](entitlement-management-overview.md) kunt u uw governance- en toegangsbeoordelingsstrategie aanzienlijk vereenvoudigen. Een toegangspakket is een bundel van alle resources met de toegang die een gebruiker nodig heeft om aan een project te werken of de taak uit te voeren. U kunt bijvoorbeeld een toegangspakket maken dat alle toepassingen bevat die ontwikkelaars in uw organisatie nodig hebben, of alle toepassingen waarvoor externe gebruikers toegang moeten hebben. Een beheerder of gedelegeerde toegangspakketbeheerder groepert vervolgens de resources (groepen of apps) en de rollen die de gebruikers nodig hebben voor deze resources.

Wanneer u een toegangspakket [maakt,](entitlement-management-access-package-create.md)kunt u een of meer toegangsbeleidsregels maken waarmee voorwaarden worden ingesteld waarvoor gebruikers een toegangspakket kunnen aanvragen, hoe het goedkeuringsproces eruit ziet en hoe vaak een persoon opnieuw toegang moet aanvragen. Toegangsbeoordelingen worden geconfigureerd tijdens het maken of bewerken van een toegangspakketbeleid.

Open het tabblad Levenscyclus om omlaag te schuiven naar Toegangsbeoordelingen.

 ![Schermopname van het 'Beleid bewerken' op het tabblad Levenscyclus.](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Toegangsbeoordelingen voor groepen plannen

Naast toegangspakketten is het controleren van groepslidmaatschap de meest efficiënte manier om de toegang te controleren. We raden u aan om toegang tot resources toe te voegen via beveiligingsgroepen [of Microsoft 365](../fundamentals/active-directory-manage-groups.md)groepen en dat gebruikers worden toegevoegd aan deze groepen om toegang te krijgen.

Aan één groep kan toegang worden verleend tot alle juiste resources. U kunt de groepstoegang toewijzen aan afzonderlijke resources of aan een toegangspakket dat toepassingen en andere resources groeperen. Met deze methode kunt u de toegang tot de groep controleren in plaats van de toegang van een individu tot elke toepassing. 

Groepslidmaatschap kan worden gecontroleerd door: 

* Beheerders

* Groepseigenaren

* Geselecteerde gebruikers, gedelegeerde beoordelingsfunctie wanneer de beoordeling wordt gemaakt

* Leden van de groep, die zichzelf bevestigen

### <a name="group-ownership"></a>Groepseigendom

Het is raadzaam dat groepseigenaren het lidmaatschap beoordelen, omdat ze het beste kunnen weten wie toegang nodig heeft. Het eigendom van groepen verschilt met het type groep:

Groepen die worden gemaakt in Microsoft 365 en Azure AD hebben een of meer goed gedefinieerde eigenaren. In de meeste gevallen zijn deze eigenaren perfecte revisoren voor hun eigen groepen, omdat ze weten wie toegang moet hebben. 

Microsoft Teams gebruikt bijvoorbeeld Microsoft 365 Groepen als het onderliggende autorisatiemodel om gebruikers toegang te verlenen tot resources in SharePoint, Exchange, OneNote of Microsoft 365 services. De maker van het team wordt automatisch een eigenaar en moet verantwoordelijk zijn voor het bevestigen van het lidmaatschap van die groep. 

Groepen die handmatig zijn gemaakt in de Azure AD-portal of via scripts via Microsoft Graph mogelijk niet noodzakelijkerwijs eigenaars hebben gedefinieerd. We raden u aan deze te definiëren via de Azure AD-portal in de sectie 'Eigenaren' van de groep of via Graph.

Groepen die worden gesynchroniseerd vanuit on-premises Active Directory kunnen geen eigenaar hebben in Azure AD. Wanneer u een toegangsbeoordeling voor hen maakt, moet u personen selecteren die het meest geschikt zijn om te beslissen over het lidmaatschap ervan.

> [!NOTE]
> Het is raadzaam bedrijfsbeleid te definiëren dat bepaalt hoe groepen worden gemaakt om ervoor te zorgen dat het eigendom en de verantwoordelijkheid voor groepen duidelijk zijn voor een regelmatige beoordeling van het lidmaatschap. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Lidmaatschap van uitsluitingsgroepen controleren in beleid voor voorwaardelijke toegang 

Er zijn momenten waarop beleid voor voorwaardelijke toegang dat is ontworpen om uw netwerk veilig te houden, niet op alle gebruikers van toepassing mag zijn. Een beleid voor voorwaardelijke toegang waarmee gebruikers zich alleen kunnen aanmelden in het bedrijfsnetwerk, is bijvoorbeeld mogelijk niet van toepassing op het verkoopteam, dat intensief wordt gebruikt. In dat geval worden de leden van het verkoopteam in een groep gezet en wordt die groep uitgesloten van het beleid voor voorwaardelijke toegang. 

Controleer een dergelijk groepslidmaatschap regelmatig, omdat de uitsluiting een potentieel risico vormt als de verkeerde leden worden uitgesloten van de vereiste.

U kunt [Azure AD-toegangsbeoordelingen gebruiken om gebruikers te beheren die zijn uitgesloten van beleid voor voorwaardelijke toegang.](conditional-access-exclusion.md)

### <a name="review-external-users-group-memberships"></a>Groepslidmaatschap van externe gebruiker controleren

Als u handmatig werk en bijbehorende mogelijke fouten wilt minimaliseren, kunt u dynamische groepen [gebruiken om](../enterprise-users/groups-create-rule.md) groepslidmaatschap toe te wijzen op basis van de kenmerken van een gebruiker. Mogelijk wilt u een of meer dynamische groepen voor externe gebruikers maken. De interne sponsor kan fungeren als revisor voor lidmaatschap van de groep. 

Opmerking: Externe gebruikers die uit een groep worden verwijderd als gevolg van een toegangsbeoordeling, worden niet verwijderd uit de tenant. 

Ze kunnen handmatig worden verwijderd uit een tenant of via een script.

### <a name="review-access-to-on-premises-groups"></a>Toegang tot on-premises groepen controleren

Toegangsbeoordelingen kunnen het groepslidmaatschap niet wijzigen van groepen die u on-premises synchroniseert met [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Dit komt doordat de autoriteitsbron on-premises is.

U kunt toegangsbeoordelingen nog steeds gebruiken om regelmatige beoordelingen van on-premises groepen te plannen en te onderhouden. Revisoren ondernemen vervolgens actie in de on-premises groep. Deze strategie behoudt Toegangsbeoordelingen als het hulpprogramma voor alle beoordelingen.

U kunt de resultaten van een toegangsbeoordeling op on-premises groepen gebruiken en deze verder verwerken, door:

* Het CSV-rapport downloaden uit de toegangsbeoordeling en handmatig actie ondernemen.

* Gebruik Microsoft Graph programmatisch toegang te krijgen tot resultaten en beslissingen in voltooide toegangsbeoordelingen.

Als u bijvoorbeeld toegang wilt krijgen tot resultaten voor een door Windows AD beheerde groep, gebruikt u dit [PowerShell-voorbeeldscript.](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises) Het script bevat een overzicht van de vereiste Graph-aanroepen en exporteert de Windows AD-PowerShell om de wijzigingen uit te voeren.

## <a name="plan-access-reviews-for-applications"></a>Toegangsbeoordelingen voor toepassingen plannen 

Wanneer u de toegang tot een toepassing beoordeelt, controleert u de toegang voor werknemers en externe identiteiten tot de informatie en gegevens in de toepassing. Kies ervoor om een toepassing te controleren wanneer u wilt weten wie toegang heeft tot een specifieke toepassing, in plaats van een toegangspakket of een groep. 

In de volgende scenario's raden we u aan om beoordelingen voor toepassingen te plannen:

* Gebruikers krijgen directe toegang tot de toepassing (buiten een groep of toegangspakket).

* De toepassing maakt kritieke of gevoelige informatie beschikbaar.

* De toepassing heeft specifieke nalevingsvereisten waaraan u moet voldoen.

* U vermoedt ongepaste toegang.

Als u toegangsbeoordelingen voor een toepassing wilt maken, stelt u Gebruikerstoewijzing vereist in. eigenschap ja. Als deze is ingesteld op Nee, hebben alle gebruikers in uw directory, met inbegrip van externe identiteiten, toegang tot de toepassing en kunt u de toegang tot de toepassing niet controleren. 

 ![app-toewijzingen plannen](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Vervolgens moet [u de gebruikers en groepen toewijzen](../manage-apps/assign-user-or-group-access-portal.md) die u toegang wilt geven. 

### <a name="reviewers-for-an-application"></a>Revisoren voor een toepassing

Toegangsbeoordelingen kunnen zijn voor de leden van een groep of voor gebruikers die zijn toegewezen aan een toepassing. Toepassingen in Azure AD hebben niet noodzakelijkerwijs een eigenaar. Daarom is het niet mogelijk om de eigenaar van de toepassing te selecteren als revisor. U kunt een beoordeling verder inspreken om alleen gastgebruikers te controleren die zijn toegewezen aan de toepassing, in plaats van alle toegang te controleren.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Planbeoordeling van Azure AD- en Azure-resourcerollen

[Privileged Identity Management (PIM) vereenvoudigt](../privileged-identity-management/pim-configure.md) de manier waarop ondernemingen bevoegde toegang tot resources in Azure AD beheren. Hierdoor blijft de lijst met bevoorrechte rollen, zowel in [Azure AD-](../roles/permissions-reference.md) als [Azure-resources,](../../role-based-access-control/built-in-roles.md) veel kleiner en wordt de algehele beveiliging van de directory verhoogd.

Met toegangsbeoordelingen kunnen revisoren bevestigen of gebruikers nog steeds een rol moeten hebben. Net als toegangsbeoordelingen voor toegangspakketten, worden beoordelingen voor Azure AD-rollen en Azure-resources geïntegreerd in de gebruikerservaring van de PIM-beheerder. U wordt aangeraden de volgende roltoewijzingen regelmatig te controleren:

* Hoofdbeheerder

* Gebruikersbeheerder

* Bevoorrechte verificatiebeheerder

* Beheerder van voorwaardelijke toegang

* Beveiligingsbeheer

* Alle Microsoft 365 en Dynamics Service Administration-rollen

Rollen die hier zijn geselecteerd, zijn de permanente en in aanmerking komende rol. 

Selecteer in de sectie Revisoren een of meer personen om alle gebruikers te controleren. U kunt er ook voor kiezen om de leden hun eigen toegang te laten beoordelen.

 ![Beleid bewerken](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Toegangsbeoordelingen implementeren

Nadat u een strategie en een plan hebt voorbereid om de toegang te beoordelen voor resources die zijn geïntegreerd met Azure AD, implementeert en beheert u beoordelingen met behulp van de onderstaande resources.

### <a name="review-access-packages"></a>Toegangspakketten controleren

Om het risico op verouderde toegang te verminderen, kunnen beheerders periodieke beoordelingen inschakelen van gebruikers die actieve toewijzingen hebben aan een toegangspakket. Volg de instructies in de onderstaande koppeling:

| Artikelen met procedures| Description |
| - | - |
| [Toegangsbeoordelingen maken](entitlement-management-access-reviews-create.md)| Beoordelingen van toegangspakket inschakelen. |
| [Toegangsbeoordelingen uitvoeren](entitlement-management-access-reviews-review-access.md)| Voer toegangsbeoordelingen uit voor andere gebruikers die zijn toegewezen aan een toegangspakket. |
| [Toegewezen toegangspakket(en) voor zelfbeoordeling](entitlement-management-access-reviews-self-review.md)| Zelfbeoordeling van toegewezen toegangspakketten |


> [!NOTE]
> Eindgebruikers die zelf een beoordeling geven en zeggen dat ze geen toegang meer nodig hebben, worden niet onmiddellijk verwijderd uit het toegangspakket. Ze worden verwijderd uit het toegangspakket wanneer de beoordeling wordt beëindigd of als een beheerder de beoordeling stopt.

### <a name="review-groups-and-apps"></a>Groepen en apps controleren

Toegangsbehoeften voor groepen en toepassingen voor werknemers en gasten veranderen waarschijnlijk in de tijd. Beheerders kunnen toegangsbeoordelingen maken voor groepsleden of toegang tot toepassingen om het risico te beperken dat gepaard gaat met verouderde toegangstoewijzingen. Volg de instructies in de onderstaande koppeling:

| Artikelen met procedures| Description |
| - | - |
| [Toegangsbeoordelingen maken](create-access-review.md)| Maak een of meer toegangsbeoordelingen voor groepsleden of toegang tot toepassingen. |
| [Toegangsbeoordelingen uitvoeren](perform-access-review.md)| Toegangsbeoordeling uitvoeren voor leden van een groep of gebruikers met toegang tot een toepassing. |
| [Uw toegang zelf beoordelen](review-your-access.md)| Leden beoordelen hun eigen toegang tot een groep of toepassing |
| [Toegangsbeoordeling voltooien](complete-access-review.md)| Een toegangsbeoordeling weergeven en de resultaten toepassen |
| [Actie ondernemen voor on-premises groepen](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| PowerShell-voorbeeldscript voor toegangsbeoordelingen voor on-premises groepen. |


### <a name="review-azure-ad-roles"></a>Azure AD-rollen controleren

Als u het risico wilt beperken dat gepaard gaat met verouderde roltoewijzingen, moet u regelmatig de toegang tot bevoorrechte Azure AD-rollen controleren.

![Schermopname van de lijst Lidmaatschap controleren met Azure A D-rollen.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Volg de instructies via de onderstaande links:

| Artikelen met procedures | Description |
| - | - |
 [Toegangsbeoordelingen maken](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Toegangsbeoordelingen maken voor bevoegde Azure AD-rollen in PIM |
| [Uw toegang zelf beoordelen](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Als u bent toegewezen aan een beheerdersrol, moet u de toegang tot uw rol goedkeuren of weigeren |
| [Een toegangsbeoordeling voltooien](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Een toegangsbeoordeling weergeven en de resultaten toepassen |


### <a name="review-azure-resource-roles"></a>Azure-resourcerollen controleren

Als u het risico wilt verminderen dat gepaard gaat met verouderde roltoewijzingen, moet u regelmatig de toegang tot bevoorrechte Azure-resourcerollen controleren. 

![Azure AD-rollen controleren](./media/deploy-access-review/9-review-azure-roles-picker.png)

Volg de instructies via de onderstaande links:

| Artikelen met procedures| Description |
| - | -|
| [Toegangsbeoordelingen maken](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Toegangsbeoordelingen maken voor bevoorrechte Azure-resourcerollen in PIM |
| [Uw toegang zelf beoordelen](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Als u bent toegewezen aan een beheerdersrol, moet u de toegang tot uw rol goedkeuren of weigeren |
| [Een toegangsbeoordeling voltooien](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Een toegangsbeoordeling weergeven en de resultaten toepassen |


## <a name="use-the-access-reviews-api"></a>De Toegangsbeoordelingen-API gebruiken

Zie graph API methods and role and application permission authorization checks [(Grafiek-API-methoden en](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) autorisatiecontroles voor rol- en [toepassingsmachtigingen)](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) voor interactie met en beheer van controleerbare resources. De methoden voor toegangsbeoordelingen in Microsoft Graph API zijn beschikbaar voor zowel toepassings- als gebruikerscontexten. Wanneer u scripts uitvoert in de toepassingscontext, moet het account dat wordt gebruikt om de API uit te voeren (het service-principe) de machtiging AccessReview.Read.All krijgen om toegangsbeoordelingen op te vragen.

Populaire taken voor toegangsbeoordelingen die moeten worden automatiseren met Graph API toegangsbeoordelingen zijn:

* Een toegangsbeoordeling maken en starten

* Handmatig een toegangsbeoordeling beëindigen vóór het geplande einde

* Alle uitvoerende toegangsbeoordelingen en hun status weergeven

* Bekijk de geschiedenis van een beoordelingsreeks en de beslissingen en acties die in elke beoordeling worden genomen.

* Beslissingen verzamelen van een toegangsbeoordeling

* Verzamel beslissingen van voltooide beoordelingen waarbij de revisor een andere beslissing heeft genomen dan wat het systeem heeft aanbevolen.

Wanneer u nieuwe Graph API voor automatisering maakt, raden we u aan de [Graph Explorer te gebruiken.](https://developer.microsoft.com/en-us/graph/graph-explorer) U kunt uw Graph-query's bouwen en verkennen voordat u ze in scripts en code kunt plaatsen. Dit kan u helpen om uw query snel te itereren, zodat u precies de resultaten krijgt die u zoekt, zonder de code van uw script te wijzigen.

## <a name="monitor-access-reviews"></a>Toegangsbeoordelingen bewaken

Activiteiten van toegangsbeoordelingen worden vastgelegd en beschikbaar in de [auditlogboeken van Azure AD.](../reports-monitoring/concept-audit-logs.md) U kunt de controlegegevens filteren op de categorie, het activiteitstype en het datumbereik. Hier is een voorbeeldquery:

| Categorie| Beleid |
| - | - |
| Type activiteit| Toegangsbeoordeling maken |
| | Toegangsbeoordeling bijwerken |
| | Toegangsbeoordeling beëindigd |
| | Toegangsbeoordeling verwijderen |
| | Beslissing goedkeuren |
| | Beslissing weigeren |
| | Beslissing over opnieuw instellen |
| | Beslissing toepassen |
| Datumbereik| zeven dagen |


Voor geavanceerdere query's en analyse van toegangsbeoordelingen en om wijzigingen en voltooiing van beoordelingen bij te houden, raden we u aan uw Azure AD-auditlogboeken te exporteren naar [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) of Azure Event Hub. Wanneer u deze opgeslagen in Azure Log Analytics, kunt u de [krachtige analysetaal gebruiken](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) en uw eigen dashboards bouwen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de onderstaande gerelateerde technologieën.

* [Wat is Azure AD-rechtenbeheer?](entitlement-management-overview.md)

* [Wat is Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)