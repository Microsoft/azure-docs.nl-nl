---
title: Een Azure Active Directory de implementatie van toegangs beoordelingen plannen
description: Plannings handleiding voor een geslaagde implementatie van toegangs beoordelingen
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
ms.openlocfilehash: 4122e645b76751e8944704a6405cf5dee09129f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97932432"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Implementatie van Azure Active Directory Access beoordelingen plannen

[Toegangs beoordelingen van Azure Active Directory (Azure AD)](access-reviews-overview.md) helpen uw organisatie het netwerk veiliger te houden door de [levens cyclus](identity-governance-overview.md)van de toegang tot resources te beheren. Met toegangs beoordelingen kunt u het volgende doen:

* Regel matige beoordelingen plannen of ad-hoc beoordelingen uitvoeren om te zien wie toegang heeft tot specifieke bronnen, zoals toepassingen en groepen

* Beoordelingen bijhouden voor inzichten, naleving of beleids redenen

* Delegeer beoordelingen naar specifieke beheerders, zakelijke eigen aars of eind gebruikers die zelf kunnen nadoen wat er nodig is voor voortdurende toegang

* De inzichten gebruiken om efficiënt te bepalen of gebruikers toegang moeten blijven hebben

* Resultaten van de beoordeling automatiseren, zoals het verwijderen van de toegang van gebruikers tot resources

  ![Diagram waarin de toegangs beoordelingen stroom wordt weer gegeven.](./media/deploy-access-review/1-planning-review.png)

Toegangs beoordelingen is een [Azure AD Identity governance](identity-governance-overview.md) mogelijkheid. De andere mogelijkheden zijn het [rechten beheer](entitlement-management-overview.md), het [privileged Identity Management](../privileged-identity-management/pim-configure.md) en de [gebruiks voorwaarden](../conditional-access/terms-of-use.md). Samen helpen organisaties de volgende vier vragen te beantwoorden:

* Welke gebruikers moeten toegang hebben tot welke resources?

* Wat doen gebruikers met die toegang?

* Is er effectief beheer van de organisatie voor het beheren van toegang?

* Kunnen auditors controleren of de beheeropties werken?

Het plannen van de implementatie van toegangs beoordelingen is essentieel om ervoor te zorgen dat u de gewenste beheer strategie voor gebruikers in uw organisatie verzorgt.

### <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voor delen van het inschakelen van toegangs beoordelingen zijn:

* **Beheer samen werking**. Met toegangs beoordelingen kunnen organisaties de toegang tot alle resources beheren die gebruikers nodig hebben. Wanneer hun gebruikers delen en samen werken, kunnen organisaties er zeker van zijn dat de informatie alleen onder geautoriseerde gebruikers staat.

* **Risico's beheren**. Toegangs beoordelingen bieden organisaties een manier om de toegang tot gegevens en toepassingen te controleren, het risico van lekkage van gegevens en verstrijken van gegevens te verlagen. Dit omvat mogelijkheden om regel matig de toegang van externe partners tot bedrijfs bronnen te controleren. 

* **Naleving en** beheer van adressen. Met toegangs beoordelingen kunt u de toegangs levenscyclus bepalen en opnieuw certificeren voor groepen, apps en sites. U kunt tracerings Beoordelingen voor naleving of risico gevoelige toepassingen beheren die specifiek zijn voor uw organisatie. 

* **Kosten verlagen**. Toegangs beoordelingen zijn ingebouwd in de Cloud en zijn systeem eigen met cloud resources zoals groepen, toepassingen en toegangs pakketten. Het gebruik van toegangs beoordelingen is minder kostbaar dan het bouwen van uw eigen hulpprogram ma's of op een andere manier om uw on-premises toolset bij te werken.

### <a name="training-resources"></a>Trainings bronnen

De volgende Video's kunnen nuttig zijn als u meer informatie wilt over toegangs beoordelingen:

* [Wat zijn toegangs beoordelingen in azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Toegangs beoordelingen maken in azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Toegangs beoordelingen inschakelen in azure AD](https://youtu.be/X1SL2uubx9M)

* [Toegang controleren met behulp van mijn toegang](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenties

U hebt een geldige Azure AD Premium (P2)-licentie nodig voor elke persoon, met uitzonde ring van globale beheerders of gebruikers beheerders, die toegangs beoordelingen zullen maken of uitvoeren. Zie de [licentie vereisten voor toegangs beoordelingen](access-reviews-overview.md)voor meer informatie.

Mogelijk hebt u ook andere Identity governance-functies nodig, zoals het beheer van de rechten van de [levens cyclus](entitlement-management-overview.md) of privileged Identity Management. In dat geval hebt u mogelijk ook gerelateerde licenties nodig. Zie [Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie.

## <a name="plan-the-access-reviews-deployment-project"></a>Het implementatie project voor toegangs beoordelingen plannen

Bedenk wat uw organisatie nodig heeft om de strategie voor het implementeren van toegangs beoordelingen in uw omgeving te bepalen.

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans als gevolg van niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](../fundamentals/active-directory-deployment-plans.md) en dat de project rollen duidelijk zijn.

Voor toegangs beoordelingen neemt u waarschijnlijk vertegenwoordigers uit de volgende teams binnen uw organisatie:

* **IT-beheer** beheert uw IT-infra structuur en beheert uw Cloud investeringen en SaaS-apps (Software as a Service). Dit team doet het volgende:

   * Bekijk de bevoegde toegang tot de infra structuur en apps, waaronder Microsoft 365 en Azure AD.

   * Toegangs beoordelingen plannen en uitvoeren voor groepen die worden gebruikt voor het onderhouden van uitzonderings lijsten of IT-pilot projecten, om actuele toegangs lijsten te onderhouden.

   * Zorg ervoor dat programmatisch (scripts) toegang tot bronnen via service-principals wordt geregeld en gecontroleerd.

* **Ontwikkel teams** maken en onderhouden van toepassingen voor uw organisatie. Dit team doet het volgende:

   * Bepaal wie onderdelen kan openen en beheren in SaaS-, PaaS-en IaaS-resources waaruit de ontwikkelde oplossingen bestaan.

   * Beheer groepen die toegang hebben tot toepassingen en hulpprogram ma's voor het ontwikkelen van interne toepassingen.

   * Bevoegde identiteiten vereisen die toegang hebben tot productie software of-oplossingen die worden gehost voor uw klanten

* **Bedrijfs eenheden** beheren projecten en eigen toepassingen. Dit team doet het volgende: 

   * Toegang tot groepen en toepassingen voor interne en externe gebruikers controleren en goed keuren of weigeren.

   * Plan en voer beoordelingen uit om voortdurende toegang te bevestigen voor werk nemers en externe identiteiten, zoals zaken partners.

* **Corporate governance** zorgt ervoor dat de organisatie volgt op het interne beleid en voldoet aan de voor Schriften. Dit team doet het volgende:

   * Nieuwe toegangs beoordelingen aanvragen of plannen.

   * Beoordeling van processen en procedures voor het beoordelen van toegang, waaronder documentatie en record behoud van naleving.

   * Bekijk de resultaten van eerdere beoordelingen voor de meeste kritieke resources.

> [!NOTE]
> Voor recensies waarvoor hand matige evaluaties zijn vereist, moet u zorgen voor voldoende revisoren en controle cycli die voldoen aan uw beleid en nalevings behoeften. Als controle cycli te frequent zijn, of als er te weinig revisoren zijn, kan de kwaliteit verloren gaan en kunnen er te veel mensen toegang hebben. 

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is van cruciaal belang voor het slagen van een nieuw bedrijfs proces. Proactief communiceren met gebruikers hoe en wanneer hun ervaring wordt gewijzigd, en hoe ze ondersteuning kunnen krijgen als ze problemen ondervinden. 

#### <a name="communicate-changes-in-accountability"></a>Wijzigingen in de verantwoordelijkheid door geven

Toegangs beoordelingen ondersteunen de verwerkings verantwoordelijkheid voor het beoordelen van de toegang tot zakelijke eigen aren. Het loskoppelen van de toegangs beslissingen van de IT-drive krijgt nauw keurigere toegangs beslissingen. Dit is een cultuur wijziging in de verantwoordelijkheden en verantwoordelijkheid van de resource-eigenaar. Communiceer deze wijziging proactief en zorg ervoor dat de resource-eigen aren getraind zijn en de inzichten kunnen gebruiken om goede beslissingen te nemen.

HET is duidelijk dat u de controle houdt over alle toegangs beslissingen met betrekking tot de infra structuur en geprivilegieerde roltoewijzingen. 

#### <a name="customize-email-communication"></a>E-mail communicatie aanpassen

Wanneer u een beoordeling plant, wijst u gebruikers aan die deze beoordeling gaan uitvoeren. Deze revisoren ontvangen een e-mail melding van nieuwe beoordelingen die eraan zijn toegewezen, evenals herinneringen voordat een aan hen toegewezen beoordeling verloopt.

Beheerders kunnen ervoor kiezen deze melding halverwege te verzenden voordat de controle verloopt of een dag voordat deze verloopt. 

Het e-mail bericht dat wordt verzonden naar revisoren kan worden aangepast met een aangepast kort bericht dat hen adviseert om op de beoordeling te reageren. U wordt aangeraden de extra tekst te gebruiken voor het volgende:

* Voeg een persoonlijk bericht toe aan revisoren, zodat ze weten dat het is verzonden door uw naleving of IT-afdeling.

* Neem een Hyper Link of verwijzing op naar interne informatie over wat de verwachtingen van de beoordeling zijn en aanvullende referentie-of trainings materiaal.

* Neem een koppeling op naar instructies voor [het uitvoeren van een zelf beoordeling van de toegang.](review-your-access.md) 

  ![E-mail van revisor](./media/deploy-access-review/2-plan-reviewer-email.png)

Wanneer u start controle selecteert, worden revisoren omgeleid naar de [myAccess-Portal](https://myapplications.microsoft.com/) voor toegangs Beoordelingen voor groepen en toepassingen. De portal geeft een overzicht van alle gebruikers die toegang hebben tot de resource die ze controleren en systeem aanbevelingen op basis van de laatste aanmelding en toegang tot gegevens.

### <a name="plan-a-pilot"></a>Een pilot plannen

We raden klanten aan om toegangs beoordelingen eerst te testen met een kleine groep en niet-essentiële resources te richten. Piloten kan u helpen bij het aanpassen van processen en communicatie, en verhoogt de mogelijkheden van gebruikers en revisoren om te voldoen aan de vereisten voor beveiliging en naleving.

In uw pilot kunt u het volgende doen:

* Begin met beoordelingen waarbij de resultaten niet automatisch worden toegepast en u de implicaties kunt beheren.

* Zorg ervoor dat alle gebruikers geldige e-mail adressen hebben die worden vermeld in azure AD en dat ze e-mail communicatie ontvangen om de juiste actie te ondernemen. 

* Documenteer alle toegang die wordt verwijderd als onderdeel van de pilot voor het geval u deze snel moet herstellen.

* Controleer de controle Logboeken om te controleren of alle gebeurtenissen goed worden gecontroleerd.

Zie [Aanbevolen procedures voor een pilot](../fundamentals/active-directory-deployment-plans.md)voor meer informatie.

## <a name="introduction-to-access-reviews"></a>Inleiding tot toegangs beoordelingen

In deze sectie vindt u de toegangs beoordelings concepten die u moet weten voordat u uw beoordelingen plant.

### <a name="what-resource-types-can-be-reviewed"></a>Welke resource typen kunnen worden gecontroleerd?

Wanneer u de resources van uw organisatie hebt geïntegreerd met Azure AD (zoals gebruikers, toepassingen en groepen), kunnen ze worden beheerd en gecontroleerd. 

Typische doelen voor controle zijn onder andere:

* [Toepassingen die zijn geïntegreerd met Azure AD voor eenmalige aanmelding](../manage-apps/what-is-application-management.md) (zoals SaaS, line-of-Business).

* Groepslid [maatschap](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) (gesynchroniseerd met Azure AD of gemaakt in azure ad of Microsoft 365, inclusief micro soft teams).

* [Toegangs pakket](./entitlement-management-overview.md) waarmee resources (groepen, apps en sites) in één pakket worden gegroepeerd om de toegang te beheren.

* [Azure AD-rollen en Azure-resource rollen](../privileged-identity-management/pim-resource-roles-assign-roles.md) zoals gedefinieerd in privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Wie maakt en beheert toegangs beoordelingen?

De administratieve rol die vereist is voor het maken, beheren of lezen van een toegangs beoordeling is afhankelijk van het type resource dat wordt gecontroleerd. De volgende tabel geeft een lijst van de functies die vereist zijn voor elk resource type:

| Resourcetype| Toegangs beoordelingen maken en beheren (makers)| Resultaten van de toegangs beoordeling lezen |
| - | - | -|
| Groep of toepassing| Hoofdbeheerder <p>Gebruikersbeheerder| Makers en beveiligings beheerder |
| Geprivilegieerde rollen in azure AD| Hoofdbeheerder <p>Beheerder voor bevoorrechte rollen| makers <p>Beveiligingslezer<p>Beveiligingsbeheer |
| Geprivilegieerde rollen in azure (resources)| Hoofdbeheerder<p>Gebruikersbeheerder<p>Resource-eigenaar| makers |
| Toegangs pakket| Hoofdbeheerder<p>Maker van het toegangs pakket| Alleen globale beheerder |


Zie [machtigingen voor beheerdersrol in azure Active Directory](../roles/permissions-reference.md)voor meer informatie.

### <a name="who-will-review-the-access-to-the-resource"></a>Wie gaat de toegang tot de resource bekijken?

De maker van de toegangs beoordeling beslist op het moment dat de beoordeling wordt uitgevoerd. Deze instelling kan niet worden gewijzigd nadat de controle is gestart. Revisoren worden vertegenwoordigd door drie personen:

* Resource-eigen aren, die de zakelijke eigen aren zijn van de resource.

* Een set afzonderlijke geselecteerde gemachtigden, zoals geselecteerd door de beheerder van de toegangs Beoordelingen.

* Eind gebruikers die elke selfservice zullen afgeven voor de nood zaak van voortdurende toegang.

Bij het maken van een toegangs beoordeling kunnen beheerders een of meer revisoren kiezen. Alle revisoren kunnen een beoordeling starten en uitvoeren, gebruikers kiezen voor een voortdurende toegang tot een bron of het verwijderen ervan. 

### <a name="components-of-an-access-review"></a>Onderdelen van een toegangs beoordeling

Voordat u uw toegangs beoordelingen implementeert, moet u de typen beoordelingen plannen die relevant zijn voor uw organisatie. Hiervoor moet u zakelijke beslissingen nemen over wat u wilt controleren en welke acties u moet ondernemen op basis van deze Beoordelingen.

Als u een toegangs beoordelings beleid wilt maken, moet u de volgende informatie hebben.

* Wat is de resource (s) die u wilt controleren?

* Waarvan de toegang wordt gecontroleerd.

* Hoe vaak moet de beoordeling optreden?

* Wie gaat de beoordeling uitvoeren?

   * Hoe wordt de melding weer gegeven om te worden gecontroleerd?

   * Wat zijn de tijd lijnen die moeten worden afgedwongen voor beoordeling?

* Welke automatische acties moeten worden afgedwongen op basis van de beoordeling?

   * Wat gebeurt er als de revisor niet tijdig reageert?

* Welke hand matige acties worden uitgevoerd op basis van de beoordeling?

* Welke communicatie moet er worden verzonden op basis van acties die worden uitgevoerd?


**Voor beeld van toegangs beoordelings plan**

| Onderdeel| Waarde |
| - | - |
| **Te controleren resources**| Toegang tot micro soft Dynamics |
| **Beoordelings frequentie**| Maandelijks |
| **Wie beoordeling uitvoert**| Dynamics bedrijfs groep programma managers |
| **Melding**| 24 uur per e-mail verzenden naar alias Dynamics-Pms<p>Intrekken van een aangepast bericht aan revisoren voor het beveiligen van hun aankoop |
| **Tijdlijn**| 48 uur vanaf melding |
|**Automatische acties**| Verwijder de toegang van een account zonder interactieve aanmelding binnen 90 dagen door de gebruiker te verwijderen uit de beveiligings groep Dynamics-toegang. <p>*Acties uitvoeren als deze niet binnen de tijd lijn worden gecontroleerd.* |
| **Hand matige acties**| Revisoren kunnen, indien gewenst, de goed keuring van verwijderingen uitvoeren vóór een automatische actie. |
| **Communicatie**| Interne (lid) gebruikers verzenden die een e-mail verwijderen waarin wordt uitgelegd dat ze zijn verwijderd en hoe ze opnieuw toegang krijgen. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Acties automatiseren op basis van toegangs beoordelingen

U kunt de functie voor het automatisch verwijderen van toegang instellen door de optie automatisch Toep assen op resource in te scha kelen.

  ![Toegangs beoordelingen plannen](./media/deploy-access-review/3-automate-actions-settings.png)

Zodra de controle is voltooid en is beëindigd, worden gebruikers die niet zijn goedgekeurd door de revisor, automatisch verwijderd uit de resource, of blijven ze behouden met voortdurende toegang. Dit kan betekenen dat het groepslid maatschap, de toewijzing van de toepassing of het intrekken van het recht om te verhogen naar een geprivilegieerde rol wordt verwijderd.

Aanbevelingen doen

De aanbevelingen worden weer gegeven als onderdeel van de ervaring van de revisor en geven de laatste aanmelding van een persoon aan de Tenant of de laatste toegang tot een toepassing aan. Deze informatie helpt revisoren de juiste toegangs beslissing te nemen. Als u aanbevelingen volgen selecteert, volgt u de aanbevelingen voor de toegangs beoordeling. Aan het einde van een toegangs beoordeling worden deze aanbevelingen automatisch toegepast op gebruikers die niet hebben gereageerd op.

Aanbevelingen zijn gebaseerd op de criteria in de toegangs beoordeling. Als u bijvoorbeeld de beoordeling configureert om de toegang te verwijderen zonder interactieve aanmelding gedurende 90 dagen, wordt aangeraden alle gebruikers die aan deze criteria voldoen, te worden verwijderd. Micro soft werkt voortdurend aan het verbeteren van aanbevelingen. 

### <a name="review-guest-user-access"></a>Toegang tot gast gebruikers controleren

Gebruik toegangs beoordelingen om identiteiten van samenwerkings partners van externe organisaties te controleren en op te schonen. De configuratie van een beoordeling per partner kan voldoen aan de nalevings vereisten.

Externe identiteiten kunnen toegang tot bedrijfs bronnen krijgen via een van de volgende acties:

* Toegevoegd aan een groep 

* Uitgenodigd voor teams 

* Toegewezen aan een bedrijfs toepassing of-toegangs pakket

* Er is een geprivilegieerde rol toegewezen in azure AD of in een Azure-abonnement

Zie [voorbeeld script](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). In het script wordt weer gegeven waar externe identiteiten worden gebruikt die worden uitgenodigd voor de Tenant. U kunt het groepslid maatschap, de roltoewijzingen en toepassings toewijzingen van een externe gebruiker in azure AD bekijken. In het script worden geen toewijzingen weer gegeven buiten Azure AD, zoals het toewijzen van directe rechten aan share point-resources, zonder het gebruik van groepen.

Wanneer u een toegangs beoordeling maakt voor groepen of toepassingen, kunt u ervoor kiezen om de revisor te richten op iedereen met toegang of alleen gast gebruikers. Als u alleen gast gebruikers selecteert, krijgen revisors een lijst met gerichte externe identiteiten van Azure AD B2B die toegang hebben tot de resource.

 ![Gast gebruikers controleren](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> Dit geldt niet voor externe leden die een User type hebben. Dit omvat ook geen gebruikers die buiten Azure AD B2B-samen werking worden uitgenodigd, bijvoorbeeld personen die rechtstreeks toegang hebben tot gedeelde inhoud via share point.

## <a name="plan-access-reviews-for-access-packages"></a>Toegangs Beoordelingen voor toegangs pakketten plannen

Met [toegangs pakketten](entitlement-management-overview.md) kan uw governance-en toegangs beoordelings strategie aanzienlijk worden vereenvoudigd. Een toegangs pakket is een bundel van alle resources met de toegang die een gebruiker nodig heeft om te werken aan een project of om de taak uit te voeren. U kunt bijvoorbeeld een toegangs pakket maken dat alle toepassingen bevat die ontwikkel aars in uw organisatie nodig hebben, of voor alle toepassingen waartoe externe gebruikers toegang moeten hebben. Een beheerder of een gedelegeerde Access package manager groepeert vervolgens de resources (groepen of apps) en de rollen die de gebruikers nodig hebben voor deze resources.

Bij [het maken van een toegangs pakket](entitlement-management-access-package-create.md)kunt u een of meer toegangs beleidsregels maken die voor waarden instellen waarvoor gebruikers een toegangs pakket kunnen aanvragen, waar het goedkeurings proces uitziet en hoe vaak een persoon zou moeten worden gevraagd om toegang te krijgen. Toegangs beoordelingen worden geconfigureerd tijdens het maken of bewerken van een toegangs pakket beleid.

Open het tabblad levenscyclus om omlaag te schuiven naar toegangs Beoordelingen.

 ![Scherm afbeelding met het ' beleid bewerken ' op het tabblad levens cyclus.](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Toegangs Beoordelingen voor groepen plannen

Naast de toegangs pakketten is het lidmaatschap van de groep de meest efficiënte manier om toegang te beheren. U wordt aangeraden toegang tot resources toe te wijzen via [beveiligings groepen of Microsoft 365 groepen](../fundamentals/active-directory-manage-groups.md), en die gebruikers worden toegevoegd aan deze groepen om toegang te krijgen.

Aan één groep kan toegang worden verleend tot alle relevante resources. U kunt de groep toegang verlenen tot afzonderlijke resources of een toegangs pakket waarmee toepassingen en andere resources worden gegroepeerd. Met deze methode kunt u de toegang tot de groep controleren in plaats van de toegang van een persoon tot elke toepassing. 

Groepslid maatschap kan worden gecontroleerd door: 

* Beheerders

* Groepseigenaren

* Geselecteerde gebruikers, gedelegeerde beoordelings mogelijkheid wanneer de beoordeling wordt gemaakt

* Leden van de groep, waarbij voor zichzelf wordt verklaard

### <a name="group-ownership"></a>Eigendom van groep

We raden aan dat groeps eigenaren het lidmaatschap bekijken, omdat ze het beste zijn gevestigd om te weten wie toegang nodig heeft. Eigendom van groepen wijkt af van het type groep:

Groepen die zijn gemaakt in Microsoft 365 en Azure AD hebben een of meer goed gedefinieerde eigen aren. In de meeste gevallen maken deze eigen aren perfecte revisoren voor hun eigen groepen, wanneer ze weten wie er toegang moet hebben. 

Micro soft-teams gebruiken bijvoorbeeld Microsoft 365 groepen als het onderliggende autorisatie model om gebruikers toegang te verlenen tot resources in share point, Exchange, OneNote of andere Microsoft 365 Services. De maker van het team wordt automatisch eigenaar en moet verantwoordelijk zijn voor het samen stellen van het lidmaatschap van die groep. 

Groepen die hand matig zijn gemaakt in de Azure AD-portal of via scripting via Microsoft Graph, hebben mogelijk niet noodzakelijkerwijs eigen aars gedefinieerd. We raden u aan deze te definiëren via de Azure AD-Portal in de sectie eigen aren van de groep of via Graph.

Groepen die zijn gesynchroniseerd vanuit on-premises Active Directory kunnen geen eigenaar hebben in azure AD. Wanneer u een toegangs beoordeling wilt maken, moet u personen selecteren die het meest geschikt zijn om te beslissen over het lidmaatschap.

> [!NOTE]
> We raden u aan om het bedrijfs beleid te definiëren dat bepaalt hoe groepen worden gemaakt om de groeps eigendom en de verantwoordelijkheid voor regel matige beoordeling van het lidmaatschap te waarborgen. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Lidmaatschap van uitsluitings groepen controleren in beleid voor voorwaardelijke toegang 

Er zijn momenten waarop beleid voor voorwaardelijke toegang dat is ontworpen om uw netwerk veilig te maken, niet van toepassing is op alle gebruikers. Een voor beeld: een beleid voor voorwaardelijke toegang waarmee gebruikers zich alleen kunnen aanmelden terwijl ze op het bedrijfs netwerk werken, zijn mogelijk niet van toepassing op het verkoop team, dat uitgebreid wordt verplaatst. In dat geval worden de leden van het verkoop team in een groep geplaatst en wordt die groep uitgesloten van het beleid voor voorwaardelijke toegang. 

Een dergelijk groepslid maatschap regel matig controleren, omdat de uitsluiting een potentieel risico vertegenwoordigt als de verkeerde leden van de vereiste zijn uitgesloten.

U kunt [Azure AD-toegangs beoordelingen gebruiken voor het beheren van gebruikers die zijn uitgesloten van het beleid voor voorwaardelijke toegang](conditional-access-exclusion.md).

### <a name="review-external-users-group-memberships"></a>Groepslid maatschappen van externe gebruiker controleren

Als u hand matig werk wilt minimaliseren en mogelijke fouten wilt oplossen, kunt u het beste [dynamische groepen](../enterprise-users/groups-create-rule.md) gebruiken om groepslid maatschap toe te wijzen op basis van de kenmerken van een gebruiker. Mogelijk wilt u een of meer dynamische groepen maken voor externe gebruikers. De interne sponsor kan optreden als revisor voor lidmaatschap van de groep. 

Opmerking: externe gebruikers die uit een groep worden verwijderd als gevolg van een toegangs beoordeling, worden niet verwijderd uit de Tenant. 

Ze kunnen worden verwijderd uit een Tenant hand matig of via een script.

### <a name="review-access-to-on-premises-groups"></a>Toegang tot on-premises groepen controleren

Met toegangs beoordelingen kunt u het groepslid maatschap van groepen die u synchroniseert met [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)niet wijzigen. Dit komt doordat de bron van de autoriteit on-premises is.

U kunt nog steeds toegangs beoordelingen gebruiken om regel matige beoordelingen van on-premises groepen te plannen en te onderhouden. Revisoren zullen vervolgens actie ondernemen in de on-premises groep. Deze strategie houdt toegangs beoordelingen bij voor alle beoordelingen.

U kunt de resultaten van een toegangs beoordeling gebruiken voor on-premises groepen en ze verder verwerken, hetzij door:

* Het CSV-rapport downloaden van de toegangs beoordeling en hand matig actie ondernemen.

* Het gebruik van Microsoft Graph om programmatisch toegang te krijgen tot resultaten en beslissingen in voltooide toegangs Beoordelingen.

Als u bijvoorbeeld toegang wilt krijgen tot resultaten voor een door Windows AD beheerde groep, gebruikt u dit [Power shell-voorbeeld script](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). Het script geeft een overzicht van de vereiste grafiek aanroepen en exporteert de Windows AD-PowerShell-opdrachten om de wijzigingen uit te voeren.

## <a name="plan-access-reviews-for-applications"></a>Toegangs Beoordelingen voor toepassingen plannen 

Wanneer u de toegang tot een toepassing bekijkt, bekijkt u de toegang voor werk nemers en externe identiteiten voor de informatie en gegevens in de toepassing. Kies of u een toepassing wilt beoordelen wanneer u wilt weten wie toegang heeft tot een bepaalde toepassing, in plaats van een toegangs pakket of een groep. 

We raden u aan om beoordelingen te plannen voor toepassingen in de volgende scenario's:

* Gebruikers krijgen directe toegang tot de toepassing (buiten een groep of toegangs pakket).

* De toepassing geeft essentiële of gevoelige informatie weer.

* De toepassing heeft specifieke nalevings vereisten waarvoor u moet worden verklaard.

* U vermoedt ongeoorloofde toegang.

Stel de gebruikers toewijzing in op vereist om toegangs Beoordelingen voor een toepassing te maken? eigenschap op Ja. Als deze instelling is ingesteld op Nee, kunnen alle gebruikers in uw directory, met inbegrip van externe identiteiten, toegang krijgen tot de toepassing en kunt u de toegang tot de toepassing niet controleren. 

 ![app-toewijzingen plannen](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Vervolgens moet u [de gebruikers en groepen toewijzen](../manage-apps/assign-user-or-group-access-portal.md) die u toegang wilt geven. 

### <a name="reviewers-for-an-application"></a>Revisoren voor een toepassing

Toegangs beoordelingen kunnen gelden voor de leden van een groep of voor gebruikers die zijn toegewezen aan een toepassing. Toepassingen in azure AD hebben niet noodzakelijkerwijs een eigenaar, waarom de optie voor het selecteren van de eigenaar van de toepassing als revisor niet mogelijk is. U kunt een beoordeling verder afwerken om alleen gast gebruikers te controleren die aan de toepassing zijn toegewezen, in plaats van alle toegang te controleren.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>De beoordeling van Azure AD-en Azure-resource rollen plannen

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) vereenvoudigt de manier waarop bedrijven bevoorrechte toegang tot resources in azure AD beheren. Hierdoor blijft de lijst met geprivilegieerde rollen, zowel in [Azure AD](../roles/permissions-reference.md) als [Azure-bronnen](../../role-based-access-control/built-in-roles.md) , veel kleiner en wordt de algehele beveiliging van de Directory verhoogd.

Met toegangs beoordelingen kunnen revisoren worden verklaard of gebruikers nog steeds een rol moeten hebben. Net als bij toegangs Beoordelingen voor toegangs pakketten worden Beoordelingen voor Azure AD-rollen en Azure-resources geïntegreerd in de gebruikers ervaring van de PIM-beheerder. We raden u aan om regel matig de volgende roltoewijzingen te controleren:

* Hoofdbeheerder

* Gebruikersbeheerder

* Bevoorrechte verificatiebeheerder

* Beheerder van voorwaardelijke toegang

* Beveiligingsbeheer

* Alle Microsoft 365-en Dynamics-Service beheer rollen

Hier geselecteerde rollen zijn onder andere permanente en in aanmerking komende rol. 

Selecteer in de sectie controleurs een of meer personen om alle gebruikers te bekijken. Of u kunt ervoor kiezen om de leden hun eigen toegang te laten beoordelen.

 ![Beleid bewerken](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Toegangs beoordelingen implementeren

Nadat u een strategie en een plan hebt opgesteld om de toegang te controleren voor resources die zijn geïntegreerd met Azure AD, kunt u beoordelingen implementeren en beheren met behulp van de onderstaande resources.

### <a name="review-access-packages"></a>Toegangs pakketten controleren

Om het risico van verouderde toegang te verminderen, kunnen beheerders periodieke beoordelingen inschakelen van gebruikers die actieve toewijzingen aan een toegangs pakket hebben. Volg de instructies in de onderstaande koppeling:

| Artikelen met procedures| Beschrijving |
| - | - |
| [Toegangs beoordelingen maken](entitlement-management-access-reviews-create.md)| Evaluatie van het toegangs pakket inschakelen. |
| [Toegangs beoordelingen uitvoeren](entitlement-management-access-reviews-review-access.md)| Toegangs beoordelingen uitvoeren voor andere gebruikers die zijn toegewezen aan een toegangs pakket. |
| [Zelf beoordeling toegewezen toegangs pakket (en)](entitlement-management-access-reviews-self-review.md)| Zelf beoordeling van toegewezen toegangs pakket (en) |


> [!NOTE]
> Eind gebruikers die zelf controleren en zeggen dat ze niet langer toegang nodig hebben, worden niet direct uit het toegangs pakket verwijderd. Ze worden verwijderd uit het toegangs pakket wanneer de controle eindigt of als een beheerder de controle stopt.

### <a name="review-groups-and-apps"></a>Groepen en apps controleren

De toegang tot groepen en toepassingen voor werk nemers en gasten is waarschijnlijk in de loop van de tijd gewijzigd. Om het risico te verminderen dat is gekoppeld aan verouderde toegangs toewijzingen, kunnen beheerders toegangs Beoordelingen voor groeps leden of toepassings toegang maken. Volg de instructies in de onderstaande koppeling:

| Artikelen met procedures| Beschrijving |
| - | - |
| [Toegangs beoordelingen maken](create-access-review.md)| Maak een of meer toegangs Beoordelingen voor groeps leden of toegang tot toepassingen. |
| [Toegangs beoordelingen uitvoeren](perform-access-review.md)| Voer een toegangs beoordeling uit voor leden van een groep of gebruikers met toegang tot een toepassing. |
| [Zelf uw toegang controleren](review-your-access.md)| Leden controleren eigen toegang tot een groep of een toepassing |
| [Volledige toegangs beoordeling](complete-access-review.md)| Een toegangs beoordeling bekijken en de resultaten Toep assen |
| [Actie ondernemen voor on-premises groepen](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Voor beeld van een Power shell-script voor toegangs Beoordelingen voor on-premises groepen. |


### <a name="review-azure-ad-roles"></a>Azure AD-rollen controleren

Om het risico te verminderen dat is gekoppeld aan verouderde roltoewijzingen, moet u regel matig de toegang tot de beschermde Azure AD-rollen controleren.

![Scherm opname van de lijst ' lidmaatschap controleren ' van Azure A D-rollen.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Volg de instructies via de onderstaande links:

| Artikelen met procedures | Beschrijving |
| - | - |
 [Toegangs beoordelingen maken](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Toegangs beoordelingen maken voor beschermde Azure AD-rollen in PIM |
| [Zelf uw toegang controleren](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Als u bent toegewezen aan een beheerdersrol, de toegang tot uw rol goedkeurt of weigert |
| [Een toegangs beoordeling volt ooien](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Een toegangs beoordeling bekijken en de resultaten Toep assen |


### <a name="review-azure-resource-roles"></a>Azure-resource rollen controleren

Om het risico te verminderen dat is gekoppeld aan verouderde roltoewijzingen, moet u regel matig de toegang tot de beschermde Azure-resource rollen controleren. 

![Azure AD-rollen controleren](./media/deploy-access-review/9-review-azure-roles-picker.png)

Volg de instructies via de onderstaande links:

| Artikelen met procedures| Beschrijving |
| - | -|
| [Toegangs beoordelingen maken](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Toegangs beoordelingen maken voor beschermde Azure-resource rollen in PIM |
| [Zelf uw toegang controleren](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Als u bent toegewezen aan een beheerdersrol, de toegang tot uw rol goedkeurt of weigert |
| [Een toegangs beoordeling volt ooien](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Een toegangs beoordeling bekijken en de resultaten Toep assen |


## <a name="use-the-access-reviews-api"></a>De Access beoordelingen-API gebruiken

Zie [Graph API-methoden](/graph/api/resources/accessreviews-root?view=graph-rest-beta) en [machtigings verificatie voor rollen en toepassingen](/graph/api/resources/accessreviews-root?view=graph-rest-beta) om te communiceren met en het beheren van opnieuw te bekijken resources. De methoden voor toegangs beoordelingen in de Microsoft Graph-API zijn beschikbaar voor zowel de toepassing als de gebruikers contexten. Bij het uitvoeren van scripts in de toepassings context moet het account dat wordt gebruikt voor het uitvoeren van de API (het Service-principe) de machtiging ' AccessReview. Read. all ' krijgen om toegang te krijgen tot de gegevens van de toegangs Beoordelingen.

Populaire toegang controleert taken om het gebruik van de Graph API voor toegangs beoordelingen te automatiseren:

* Een toegangs beoordeling maken en starten

* Een toegangs beoordeling hand matig beëindigen vóór het geplande einde

* Alle actieve toegangs beoordelingen en hun status weer geven

* Bekijk de geschiedenis van een controle reeks en de beslissingen en acties die tijdens elke beoordeling worden uitgevoerd.

* Beslissingen van een toegangs beoordeling verzamelen

* Verzamel beslissingen van voltooide beoordelingen waarbij de revisor een andere beslissing heeft genomen dan wat het aanbevolen systeem is.

Wanneer u nieuwe Graph API query's voor Automation maakt, raden we u aan de [Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)te gebruiken. U kunt uw grafiek query's bouwen en verkennen voordat u ze in scripts en code plaatst. Zo kunt u snel uw query herhalen zodat u precies de resultaten krijgt die u zoekt, zonder de code van uw script te wijzigen.

## <a name="monitor-access-reviews"></a>Toegangs beoordelingen bewaken

De activiteiten voor toegangs beoordelingen worden vastgelegd en beschikbaar in de [audit logboeken van Azure AD](../reports-monitoring/concept-audit-logs.md). U kunt de controle gegevens filteren op de categorie, het type activiteit en het datum bereik. Hier volgt een voor beeld van een query:

| Categorie| Beleid |
| - | - |
| Type activiteit| Toegangsbeoordeling maken |
| | Toegangs beoordeling bijwerken |
| | De toegangs beoordeling is beëindigd |
| | Toegangsbeoordeling verwijderen |
| | Beslissing goed keuren |
| | Beslissing weigeren |
| | Beslissing opnieuw instellen |
| | Beslissing Toep assen |
| Datumbereik| zeven dagen |


Voor meer geavanceerde query's en analyse van toegangs beoordelingen, en voor het bijhouden van wijzigingen en het volt ooien van beoordelingen, raden we u aan om uw Azure AD-audit logboeken te exporteren naar [Azure log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) of Azure Event hub. Wanneer u deze opslaat in azure Log Analytics, kunt u de [krachtige analyse taal](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) gebruiken en uw eigen Dash boards bouwen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de onderstaande verwante technologieën.

* [Wat is het beheer van rechten van Azure AD?](entitlement-management-overview.md)

* [Wat is Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)