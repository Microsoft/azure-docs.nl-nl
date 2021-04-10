---
title: Veilige toegangs procedures voor beheerders in azure AD | Microsoft Docs
description: Zorg ervoor dat de beheerders-en beheerders accounts van uw organisatie veilig zijn. Voor systeem architecten en IT-professionals die Azure AD, Azure en micro soft Online Services configureren.
services: active-directory
keywords: ''
author: rolyon
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f2fe29ede2bf0f92049d1ae82bae87326057a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594290"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD

De beveiliging van bedrijfs assets is afhankelijk van de integriteit van de bevoegde accounts die uw IT-systemen beheren. Cyber aanvallers gebruiken aanmeldings diefstal aanvallen naar beheerders accounts en andere privileged Access om toegang te krijgen tot gevoelige gegevens.

Voor Cloud Services zijn de gezamenlijke verantwoordelijkheden van de Cloud serviceprovider en de klant van toepassing. Zie het [micro soft Security Intelligence-rapport](https://www.microsoft.com/security/operations/security-intelligence-report)voor meer informatie over de nieuwste bedreigingen voor eind punten en de Cloud. In dit artikel vindt u informatie over het ontwikkelen van een route kaart voor het sluiten van de hiaten tussen uw huidige plannen en de hier beschreven richt lijnen.

> [!NOTE]
> Micro soft wordt op de hoogte gebracht van de hoogste mate van vertrouwen, transparantie, normen conform en naleving van de regelgeving. Meer informatie over de manier waarop het micro soft Global incident response team de gevolgen van aanvallen op Cloud Services verkleint en hoe beveiliging is ingebouwd in micro soft-producten en Cloud Services in micro [soft vertrouwens centrum: beveiligings](https://www.microsoft.com/trustcenter/security) -en micro soft-nalevings doelen in [micro soft vertrouwens centrum-naleving](https://www.microsoft.com/trustcenter/compliance).

Normaal gesp roken is de organisatie beveiliging gericht op de ingangs-en uitgangs punten van een netwerk als de beveiligings verbinding. SaaS-apps en persoonlijke apparaten op internet hebben echter deze aanpak minder effectief. In azure AD vervangen we de netwerk beveiligings verbinding met verificatie in de id-laag van uw organisatie, met gebruikers die zijn toegewezen aan geprivilegieerde beheerders rollen in het besturings element. Hun toegang moet worden beveiligd, of de omgeving zich op locatie, in de Cloud of in een hybride bevindt.

Het beveiligen van bevoegde toegang vereist wijzigingen in:

* Processen, beheer procedures en kennis beheer
* Technische onderdelen, zoals beveiligingen van de host, account bescherming en identiteits beheer

Beveilig uw bevoorrechte toegang op een manier die wordt beheerd en gerapporteerd in de micro soft-services die u begeleidt. Als u on-premises beheerders accounts hebt, raadpleegt u de richt lijnen voor on-premises en hybride privileged Access in Active Directory op het [beveiligen van bevoegde toegang](/windows-server/identity/securing-privileged-access/securing-privileged-access).

> [!NOTE]
> De richt lijnen in dit artikel zijn voornamelijk bedoeld voor functies van Azure Active Directory die zijn opgenomen in Azure Active Directory Premium plan P1 en P2. Azure Active Directory Premium P2 is opgenomen in EMS E5 Suite en Microsoft 365 E5 Suite. In deze richt lijnen wordt ervan uitgegaan dat uw organisatie al Azure AD Premium P2-licenties heeft gekocht voor uw gebruikers. Als u deze licenties niet hebt, is het mogelijk dat bepaalde instructies niet van toepassing zijn op uw organisatie. In dit artikel is de term globale beheerder ook hetzelfde als ' bedrijfs beheerder ' of ' Tenant beheerder '.

## <a name="develop-a-roadmap"></a>Een route kaart ontwikkelen

Micro soft raadt u aan om een plan te ontwikkelen en te volgen om bevoegde toegang te beveiligen tegen Cyber aanvallers. U kunt uw plan altijd aanpassen aan uw bestaande mogelijkheden en specifieke vereisten binnen uw organisatie. Elke fase van het plan moet de kosten en moeilijkheid verhogen voor aanvallers om toegang te krijgen tot uw on-premises, Cloud-en hybride activa. Micro soft raadt de volgende vier plan stadia aan. Plan eerst de meest efficiënte en snelle implementaties. Dit artikel kan uw gids zijn, op basis van de ervaringen van micro soft met het incident en de implementatie van een cyber-aanval. De tijd lijnen voor deze route kaart zijn benaderingen.

![Fasen van het schema met tijd lijnen](./media/security-planning/roadmap-timeline.png)

* Fase 1 (24-48 uur): essentiële items die wij u het beste meteen doen

* Fase 2 (2-4 weken): de meest gebruikte aanvals technieken beperken

* Fase 3 (1-3 maanden): Maak inzicht in en bouw volledig beheer van beheer activiteiten

* Fase 4 (zes maanden en langer): door gaan met het bouwen van verdedigings elementen voor een verdere beveiliging van uw beveiligings platform

Dit raam werk schema is ontworpen om het gebruik van micro soft-technologieën die u mogelijk al hebt geïmplementeerd, te maximaliseren. Overweeg om te koppelen aan alle beveiligings Programma's van andere leveranciers die u al hebt geïmplementeerd of overweegt om te implementeren.

## <a name="stage-1-critical-items-to-do-right-now"></a>Fase 1: kritieke items nu moeten worden uitgevoerd

![Fase 1 essentiële items eerst uitvoeren](./media/security-planning/stage-one.png)

Fase 1 van het schema is gericht op kritieke taken die snel en gemakkelijk kunnen worden geïmplementeerd. U wordt aangeraden deze enkele items direct binnen de eerste 24-48 uur uit te voeren om een basis niveau van beveiligde privileged Access te garanderen. Deze fase van de beveiligde route kaart voor uitgebreide toegang bevat de volgende acties:

### <a name="general-preparation"></a>Algemene voor bereiding

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management inschakelen

U wordt aangeraden Azure AD Privileged Identity Management (PIM) in te scha kelen in uw Azure AD-productie omgeving. Nadat u PIM hebt ingeschakeld, ontvangt u meldings-e-mail berichten voor de gewijzigde rol van de toegang. Meldingen bieden een vroegtijdige waarschuwing wanneer extra gebruikers worden toegevoegd aan rollen met een hoge bevoegdheid.

Azure AD Privileged Identity Management is opgenomen in Azure AD Premium P2 of EMS E5. Meld u aan voor de [Enterprise Mobility + Security gratis proef versie van 90 dagen](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)om de toegang tot toepassingen en bronnen on-premises en in de cloud te beveiligen. Azure AD Privileged Identity Management en Azure AD Identity Protection beveiligings activiteiten controleren met behulp van Azure AD Reporting, auditing en waarschuwingen.

Nadat u Azure AD Privileged Identity Management hebt ingeschakeld:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een account dat een globale beheerder van uw Azure AD-productie organisatie is.

2. Als u de Azure AD-organisatie wilt selecteren waar u Privileged Identity Management wilt gebruiken, selecteert u uw gebruikers naam in de rechter bovenhoek van de Azure Portal.

3. Selecteer in het menu Azure Portal **alle services** en filter de lijst voor **Azure AD privileged Identity Management**.

4. Open Privileged Identity Management in de lijst **alle services** en vastmaken aan uw dash board.

Zorg ervoor dat de eerste persoon die PIM in uw organisatie gebruikt, is toegewezen aan de beheerders rollen **beveiligings beheerder** en **geprivilegieerde rol** . Alleen bevoegde beheerdersrol kunnen de toewijzingen van Azure AD-Directory functies van gebruikers beheren. De wizard PIM-beveiliging leidt u door de eerste detectie-en toewijzings ervaring. U kunt de wizard afsluiten zonder verdere wijzigingen aan te brengen.

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Accounts identificeren en categoriseren die zich in rollen met een hoge bevoegdheden bevinden

Nadat u Azure AD Privileged Identity Management hebt ingeschakeld, bekijkt u de gebruikers die zich in de volgende Azure AD-rollen bevinden:

* Globale beheerder
* Beheerder voor bevoorrechte rollen
* Exchange-beheerder
* SharePoint-beheerder

Als u geen Azure AD Privileged Identity Management hebt in uw organisatie, kunt u de [Power shell-API](/powershell/module/azuread/get-azureaddirectoryrolemember)gebruiken. Begin met de rol van globale beheerder omdat een globale beheerder dezelfde machtigingen heeft voor alle Cloud Services waarvoor uw organisatie een abonnement heeft. Deze machtigingen worden verleend, ongeacht waar ze zijn toegewezen: in het Microsoft 365-beheer centrum, de Azure Portal of de Azure AD-module voor micro soft power shell.

Verwijder alle accounts die niet meer nodig zijn voor deze rollen. Vervolgens categoriseert u de overige accounts die zijn toegewezen aan beheerders rollen:

* Toegewezen aan gebruikers met beheerders rechten, maar worden ook gebruikt voor niet-administratieve doel einden (bijvoorbeeld persoonlijke e-mail)
* Toegewezen aan gebruikers met beheerders rechten en alleen voor administratieve doel einden worden gebruikt
* Gedeeld door meerdere gebruikers
* Voor scenario's voor nood gevallen
* Voor geautomatiseerde scripts
* Voor externe gebruikers

#### <a name="define-at-least-two-emergency-access-accounts"></a>Ten minste twee accounts voor toegang in nood gevallen definiëren

Het is mogelijk dat een gebruiker per ongeluk uit hun rol wordt uitgesloten. Als er bijvoorbeeld een federatieve on-premises ID-provider niet beschikbaar is, kunnen gebruikers zich niet aanmelden of een bestaand beheerders account activeren. U kunt voor bereidingen treffen voor onbedoelde toegang door twee of meer accounts voor nood toegang op te slaan.

Met accounts voor toegang in nood gevallen kunt u de toegang in een Azure AD-organisatie beperken. Deze accounts zijn zeer privileged en worden niet toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot nood gevallen waarin normale beheerders accounts niet kunnen worden gebruikt. Zorg ervoor dat u het gebruik van het nood-account alleen beheert en beperkt tot die tijd waarvoor het nodig is.

Controleer de accounts die zijn toegewezen of in aanmerking komen voor de rol globale beheerder. Als u geen alleen-Cloud accounts met behulp \* van het. onmicrosoft.com-domein (voor ' Breek glazen ' nood toegang) ziet, maakt u deze. Zie [Beheer accounts voor nood toegang beheren in azure AD](security-emergency-access.md)voor meer informatie.

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Multi-factor Authentication inschakelen en alle andere niet-gefedereerde niet-federatieve beheerders accounts van één gebruiker registreren

Azure AD Multi-Factor Authentication (MFA) vereisen bij het aanmelden voor alle afzonderlijke gebruikers die permanent zijn toegewezen aan een of meer van de Azure AD-beheerders rollen: globale beheerder, beheerder van beschermde rol, Exchange-beheerder en share point-beheerder. Gebruik de hand leiding om [multi-factor Authentication (MFA) in te scha kelen voor uw beheerders accounts](../authentication/howto-mfa-userstates.md) en ervoor te zorgen dat alle gebruikers zijn geregistreerd op [https://aka.ms/mfasetup](https://aka.ms/mfasetup) . Meer informatie vindt u onder stap 2 en stap 3 van de hand leiding [beveiliging beveiligen tot gegevens en services in Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>Fase 2: veelgebruikte aanvallen beperken

![Fase 2 veelgebruikte aanvallen beperken](./media/security-planning/stage-two.png)

Fase 2 van het schema is gericht op het beperken van de meest gebruikte aanvals technieken met referentie diefstal en-misbruik en kan in ongeveer 2-4 weken worden geïmplementeerd. Deze fase van de beveiligde route kaart voor verhoogde toegang bevat de volgende acties.

### <a name="general-preparation"></a>Algemene voor bereiding

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Een inventarisatie uitvoeren van services, eigen aren en beheerders

De toename van ' uw eigen apparaat meenemen ' en het werken met een thuis beleid en de groei van draadloze connectiviteit maken het essentieel om te controleren wie verbinding maakt met uw netwerk. Een beveiligings controle kan apparaten, toepassingen en Program ma's op uw netwerk onthullen die niet worden ondersteund door uw organisatie en die een hoog risico vormen. Zie overzicht van Azure- [beveiligings beheer en-controle](../../security/fundamentals/management-monitoring-overview.md)voor meer informatie. Zorg ervoor dat u alle volgende taken opneemt in uw inventaris proces.

* Identificeer de gebruikers met beheerders rollen en de services die ze kunnen beheren.
* Gebruik Azure AD PIM om erachter te komen welke gebruikers in uw organisatie beheerders toegang hebben tot Azure AD.
* Naast de functies die in azure AD zijn gedefinieerd, wordt Microsoft 365 geleverd met een set beheerders rollen die u aan gebruikers in uw organisatie kunt toewijzen. Elke beheerdersrol wordt toegewezen aan algemene bedrijfs functies en geeft personen in uw organisatie machtigingen om specifieke taken uit te voeren in het [Microsoft 365-beheer centrum](https://admin.microsoft.com). Gebruik het Microsoft 365-beheer centrum om erachter te komen welke gebruikers in uw organisatie beheerders toegang hebben tot Microsoft 365, zoals via rollen die niet worden beheerd in azure AD. Zie [over Microsoft 365 beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) en [beveiligings procedures voor Office 365](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)voor meer informatie.
* Maak de inventarisatie in services die uw organisatie afhankelijk maakt, zoals Azure, intune of Dynamics 365.
* Zorg ervoor dat uw accounts worden gebruikt voor beheer doeleinden:

  * Werk aan hen gekoppelde e-mail adressen
  * Zijn geregistreerd voor Azure AD Multi-Factor Authentication of gebruikmaken van MFA on-premises
* Vraag gebruikers om hun zakelijke rechtvaardiging voor beheerders toegang.
* Verwijder beheerders toegang voor de personen en services die deze niet nodig hebben.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Micro soft-accounts in beheerders rollen identificeren die moeten worden overgeschakeld naar werk-of school accounts

Als uw eerste globale beheerders hun bestaande Microsoft-account referenties opnieuw gebruiken bij het gebruik van Azure AD, moet u de micro soft-accounts vervangen door afzonderlijke Cloud-of gesynchroniseerde accounts.

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Zorg ervoor dat afzonderlijke gebruikers accounts en e-mail berichten worden doorgestuurd voor globale beheerders accounts

Persoonlijke e-mail accounts worden regel matig gephishd door Cyber aanvallers, een risico dat persoonlijke e-mail adressen niet worden toegestaan voor accounts voor globale beheerders. Maak voor elke gebruiker met beheerders bevoegdheden speciale accounts om Internet Risico's te onderscheiden van beheerders bevoegdheden.

* Zorg ervoor dat u afzonderlijke accounts maakt voor gebruikers om algemene beheerders taken uit te voeren.
* Zorg ervoor dat uw globale beheerders niet per ongeluk e-mail berichten openen of Program ma's uitvoeren met hun beheerders accounts.
* Zorg ervoor dat deze accounts hun e-mail adres hebben doorgestuurd naar een werk postvak.
* Globale beheerders (en andere geprivilegieerde groepen)-accounts moeten alleen Cloud accounts zijn zonder binding met on-premises Active Directory.

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Controleren of de wacht woorden van beheerders accounts onlangs zijn gewijzigd

Zorg ervoor dat alle gebruikers zijn aangemeld bij hun beheerders accounts en wijzig hun wacht woord ten minste eenmaal in de afgelopen 90 dagen. Controleer ook of de wacht woorden van alle gedeelde accounts recent zijn gewijzigd.

#### <a name="turn-on-password-hash-synchronization"></a>Wachtwoord hash synchronisatie inschakelen

Azure AD Connect synchroniseert een hash van de hash van het wacht woord van een gebruiker van on-premises Active Directory naar een Azure AD-organisatie in de Cloud. U kunt wachtwoord hash-synchronisatie gebruiken als back-up als u een Federatie met Active Directory Federation Services (AD FS) gebruikt. Deze back-up kan handig zijn als uw on-premises Active Directory of AD FS servers tijdelijk niet beschikbaar zijn.

Met wachtwoord hash-synchronisatie kunnen gebruikers zich aanmelden bij een service met hetzelfde wacht woord dat ze gebruiken om zich aan te melden bij hun on-premises Active Directory-exemplaar. Met de synchronisatie van wacht woord-hashes kan identiteits beveiliging beveiligings referenties detecteren door wacht woord-hashes te vergelijken met wacht woorden waarvan bekend is dat ze worden aangetast. Zie [wachtwoord hash synchronisatie implementeren met Azure AD Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie.

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>Multi-factor Authentication vereisen voor gebruikers in geprivilegieerde rollen en belichte gebruikers

Azure AD raadt u aan om multi-factor Authentication (MFA) te vereisen voor al uw gebruikers. Houd er rekening mee dat gebruikers die belang rijke gevolgen zouden hebben, als hun account is aangetast (bijvoorbeeld financiële functionarissen). MFA vermindert het risico van een aanval vanwege een wacht woord dat is aangetast.

Inschakelen:

* [MFA met beleids regels voor voorwaardelijke toegang](../authentication/howto-mfa-getstarted.md) voor alle gebruikers in uw organisatie.

Als u Windows hello voor bedrijven gebruikt, kan aan de MFA-vereiste worden voldaan door gebruik te maken van de aanmeld ervaring van Windows hello. Zie [Windows hello](/windows/uwp/security/microsoft-passport)voor meer informatie.

#### <a name="configure-identity-protection"></a>Identiteits beveiliging configureren

Azure AD Identity Protection is een op algoritme gebaseerd hulp programma voor bewaking en rapportage dat mogelijke beveiligings problemen detecteert die van invloed zijn op de identiteiten van uw organisatie. U kunt automatische reacties op gedetecteerde verdachte activiteiten configureren en de juiste actie ondernemen om ze op te lossen. Zie [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md) voor meer informatie.

#### <a name="obtain-your-microsoft-365-secure-score-if-using-microsoft-365"></a>Uw Microsoft 365 beveiligde score verkrijgen (als u Microsoft 365 gebruikt)

Met een beveiligde Score worden uw instellingen en activiteiten gecontroleerd op de Microsoft 365 services die u gebruikt en vergelijkt deze met een basis lijn die door micro soft is ingesteld. U krijgt een score op basis van de manier waarop u de beveiligings procedures afstemt. Iedereen met de beheerders machtigingen voor een Microsoft 365 Business Standard-of ENTER prise-abonnement kan toegang krijgen tot de beveiligde Score op [https://securescore.office.com](https://securescore.office.com/) .

#### <a name="review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365"></a>Lees de Microsoft 365 beveiligings-en nalevings richtlijnen (als u Microsoft 365 gebruikt)

Het [plan voor beveiliging en naleving](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) geeft een overzicht van de benadering van een Office 365-klant om Office 365 te configureren en andere EMS-mogelijkheden in te scha kelen. Lees vervolgens stap 3-6 van de beveiliging van de [toegang tot gegevens en services in Microsoft 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) en de hand leiding voor het [bewaken van de beveiliging en naleving in Microsoft 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-microsoft-365-activity-monitoring-if-using-microsoft-365"></a>Bewaking van Microsoft 365 activiteiten configureren (als u Microsoft 365 gebruikt)

Bewaak uw organisatie voor gebruikers die Microsoft 365 gebruiken om mede werkers te identificeren die een beheerders account hebben, maar die mogelijk geen Microsoft 365 toegang nodig hebben omdat ze zich niet aanmelden bij deze portals. Zie [activiteiten rapporten in het Microsoft 365-beheer centrum](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)voor meer informatie.

#### <a name="establish-incidentemergency-response-plan-owners"></a>Eigen aren van incidenten/nood-antwoord plannen vastleggen

Het opzetten van een geslaagde reactie op incidenten vereist veel planning en bronnen. U moet voortdurend controleren op Cyber aanvallen en prioriteiten instellen voor het verwerken van incidenten. Verzamel, analyseer en meld incident gegevens om relaties te bouwen en communicatie met andere interne groepen te maken en eigen aars te plannen. Zie [micro soft Security Response Center](https://technet.microsoft.com/security/dn440717)(Engelstalig) voor meer informatie.

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>On-premises geprivilegieerde beheerders accounts beveiligen als dat nog niet is gebeurd

Als uw Azure Active Directory-organisatie is gesynchroniseerd met on-premises Active Directory, volgt u de instructies in de hand leiding voor [beveiligde toegang](/windows-server/identity/securing-privileged-access/securing-privileged-access): deze fase omvat:

* Afzonderlijke beheerders accounts maken voor gebruikers die on-premises beheer taken moeten uitvoeren
* Werk stations met verhoogde toegang implementeren voor Active Directory beheerders
* Unieke lokale beheerders wachtwoorden voor werk stations en servers maken

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die de toegang tot Azure beheren

#### <a name="complete-an-inventory-of-subscriptions"></a>Een inventarisatie van abonnementen volt ooien

Gebruik de Enter prise Portal en de Azure Portal om de abonnementen in uw organisatie te identificeren die als host optreden voor productie toepassingen.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Micro soft-accounts uit beheerders rollen verwijderen

Micro soft-accounts uit andere Program ma's, zoals Xbox, Live en Outlook, mogen niet worden gebruikt als beheerders account voor de abonnementen van uw organisatie. Beheer status verwijderen uit alle micro soft-accounts en vervangen door Azure AD (bijvoorbeeld chris@contoso.com ) werk-of school accounts. Voor beheerders doeleinden is afhankelijk van accounts die zijn geverifieerd in azure AD en niet in andere services.

#### <a name="monitor-azure-activity"></a>Azure-activiteit bewaken

Het Azure-activiteitenlogboek bevat een geschiedenis van gebeurtenissen op abonnementsniveau in Azure. Het biedt informatie over wie de resources heeft gemaakt, bijgewerkt en verwijderd, en wanneer deze gebeurtenissen zich hebben voorgedaan. Zie [controleren en meldingen ontvangen over belang rijke acties in uw Azure-abonnement](../../azure-monitor/alerts/alerts-activity-log.md)voor meer informatie.

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Aanvullende stappen voor organisaties die toegang tot andere Cloud-apps beheren via Azure AD

#### <a name="configure-conditional-access-policies"></a>Beleid voor voorwaardelijke toegang configureren

Bereid het beleid voor voorwaardelijke toegang voor op on-premises en in de Cloud gehoste toepassingen. Als u apparaten aan de werk plek hebt toegevoegd, kunt u meer informatie krijgen [over het instellen van on-premises voorwaardelijke toegang met behulp van Azure Active Directory apparaatregistratie](../../active-directory-b2c/overview.md).

## <a name="stage-3-take-control-of-admin-activity"></a>Fase 3: beheer activiteiten overnemen

![Fase 3: beheer activiteiten overnemen](./media/security-planning/stage-three.png)

Fase 3 bouwt voort op de oplossingen van fase 2 en moet in ongeveer 1-3 maanden worden geïmplementeerd. Deze fase van de beveiligd-privileged Access-schema bevat de volgende onderdelen.

### <a name="general-preparation"></a>Algemene voor bereiding

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Een toegangs beoordeling van gebruikers in beheerders rollen volt ooien

Meer zakelijke gebruikers krijgen uitgebreide toegang via Cloud Services, wat kan leiden tot onbeheerde toegang. Gebruikers kunnen tegenwoordig globale beheerders worden voor Microsoft 365, beheerders van Azure-abonnementen of beheerders toegang hebben tot Vm's of via SaaS-apps.

Uw organisatie moet alle werk nemers de normale zakelijke trans acties laten afhandelen als niet-gemachtigde gebruikers en vervolgens beheerders rechten alleen verlenen als dat nodig is. Volledige toegangs Beoordelingen voor het identificeren en bevestigen van de gebruikers die in aanmerking komen voor het activeren van Administrator bevoegdheden.

U wordt aangeraden dat u:

1. Bepaal welke gebruikers Azure AD-beheerders zijn, schakel just-in-time-beheerders toegang en op rollen gebaseerde beveiligings controles in.
2. Converteer gebruikers die geen duidelijke uitschakeling hebben over de toegang met beheerders rechten voor een andere rol (als er geen geschikte rol is, verwijdert u deze).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Implementatie van sterkere verificatie voor alle gebruikers voortzetten

Vereisen dat Maxi maal beschik bare gebruikers moderne, sterke authenticatie hebben zoals Azure AD MFA of Windows hello. Voor beelden van Maxi maal beschik bare gebruikers zijn:

* Managers van C-Suite
* Managers op hoog niveau
* Kritiek IT-en beveiligings personeel

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Speciale werk stations gebruiken voor beheer voor Azure AD

Kwaadwillende personen kunnen proberen om geautoriseerde accounts te richten, zodat ze de integriteit en authenticiteit van gegevens kunnen verstoren. Ze gebruiken vaak schadelijke code die de programma logica wijzigt of de beheerder die een referentie instuurt. Privileged Access Workstations (PAW's) beschikken over een speciaal besturingssysteem voor gevoelige taken dat is beveiligd tegen aanvallen via internet en dreigingsvectoren. Het scheiden van deze gevoelige taken en accounts van de werk stations en apparaten van dagelijks gebruik biedt een zware beveiliging van:

* Phishing-aanvallen
* Problemen met toepassingen en besturings systemen
* Imitatie aanvallen
* Referentie diefstal aanvallen, zoals toetsaanslag registratie, Pass-the-hash en Pass-the-ticket

Door privileged Access workstations te implementeren, kunt u het risico dat beheerders hun referenties invoeren, beperken in een bureaublad omgeving die niet is gehard. Zie [privileged Access workstations](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)(Engelstalig) voor meer informatie.

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Bestudeer het National Institute of Standards and Technology aanbevelingen voor het afhandelen van incidenten

Het National Institute of Standards and Technology (NIST) biedt richt lijnen voor de verwerking van incidenten, met name voor het analyseren van gegevens met betrekking tot incidenten en het bepalen van de juiste reactie op elk incident. Zie voor meer informatie [de hand leiding (het NIST) computer security incident handling (SP 800-61, revisie 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Privileged Identity Management (PIM) implementeren voor JIT naar extra beheerders rollen

Gebruik [Azure AD privileged Identity Management](../privileged-identity-management/pim-configure.md) mogelijkheid voor Azure Active Directory. Beperkte activering van geprivilegieerde rollen werkt door het volgende in te scha kelen:

* Beheerders bevoegdheden activeren om een specifieke taak uit te voeren
* MFA afdwingen tijdens het activerings proces
* Waarschuwingen gebruiken om beheerders op de hoogte te stellen van out-of-band-wijzigingen
* Gebruikers in staat stellen om hun bevoorrechte toegang te beperken gedurende een vooraf geconfigureerde tijd
* Beveiligings beheerders toestaan om:

  * Alle bevoorrechte identiteiten detecteren
  * Controle rapporten weer geven
  * Toegangs beoordelingen maken voor het identificeren van elke gebruiker die in aanmerking komt voor het activeren van Administrator bevoegdheden

Als u Azure AD Privileged Identity Management al gebruikt, past u de tijds perioden aan voor tijdgebonden bevoegdheden (bijvoorbeeld onderhouds Vensters).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>De bloot stelling aan aanmeldings protocollen op basis van wacht woorden bepalen (als u Exchange Online gebruikt)

We raden u aan elke mogelijke gebruiker te identificeren die kan leiden tot een onherstelbare organisatie als hun referenties zijn aangetast. Voor deze gebruikers wordt gebruikgemaakt van sterke verificatie vereisten en het gebruik van voorwaardelijke toegang van Azure AD om ervoor te zorgen dat ze zich niet kunnen aanmelden bij hun e-mail adres met behulp van gebruikers naam en wacht woord. U kunt [verouderde verificatie blok keren met behulp van voorwaardelijke toegang](../conditional-access/block-legacy-authentication.md)en u kunt [basis verificatie blok keren](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) via Exchange Online.

#### <a name="complete-a-roles-review-assessment-for-microsoft-365-roles-if-using-microsoft-365"></a>Beoordeling van functies volt ooien evaluatie voor Microsoft 365 rollen (indien gebruikt Microsoft 365)

Bepaal of alle beheerders gebruikers de juiste rollen hebben (verwijderen en opnieuw toewijzen volgens deze evaluatie).

#### <a name="review-the-security-incident-management-approach-used-in-microsoft-365-and-compare-with-your-own-organization"></a>Bekijk de aanpak voor het beheer van beveiligings incidenten in Microsoft 365 en vergelijkt u met uw eigen organisatie

U kunt dit rapport downloaden vanuit het [beveiligings incident beheer in Microsoft 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Blijf on-premises geprivilegieerde beheerders accounts beveiligen

Als uw Azure Active Directory is verbonden met on-premises Active Directory, volgt u de richt lijnen in de [beveiligings schema voor bevoegde toegang](/windows-server/identity/securing-privileged-access/securing-privileged-access): fase 2. In deze fase gaat u als volgt te werk:

* Privileged Access workstations implementeren voor alle beheerders
* MFA vereisen
* Net genoeg beheerder gebruiken voor het onderhoud van domein controllers, waardoor de kwets baarheid van domeinen wordt verlaagd
* Geavanceerde bedreigings evaluatie voor detectie van aanvallen implementeren

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die de toegang tot Azure beheren

#### <a name="establish-integrated-monitoring"></a>Geïntegreerde bewaking tot stand brengen

De [Azure Security Center](../../security-center/security-center-introduction.md):

* Biedt geïntegreerde beveiligings bewaking en beleids beheer in uw Azure-abonnementen
* Helpt bedreigingen te detecteren die anders niet worden opgemerkt
* Werkt met een brede reeks beveiligings oplossingen

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Uw geprivilegieerde accounts inventariseren in gehoste Virtual Machines

Normaal gesp roken hoeft u geen gebruikers onbeperkte machtigingen te geven voor al uw Azure-abonnementen of-resources. Gebruik Azure AD-beheerders rollen om alleen de toegang toe te kennen die uw gebruikers nodig hebben om hun taken uit te voeren. U kunt Azure AD-beheerders rollen gebruiken om één beheerder toe te staan alleen Vm's in een abonnement te beheren, terwijl een andere SQL-data bases binnen hetzelfde abonnement kan beheren. Zie [Wat is Azure Role-based Access Control](../../active-directory-b2c/overview.md)voor meer informatie.

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>PIM implementeren voor Azure AD-beheerders rollen

Gebruik privileged Identity Management met Azure AD-beheerders rollen voor het beheren, beheren en bewaken van de toegang tot Azure-resources. Met PIM beschermt u de belichtings tijd van bevoegdheden en verhoogt u uw zicht baarheid in het gebruik via rapporten en waarschuwingen. Zie [Wat is Azure AD privileged Identity Management](../privileged-identity-management/pim-configure.md)voor meer informatie.

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure-logboek integratie gebruiken om relevante Azure-logboeken te verzenden naar uw SIEM-systemen

Met Azure-logboek integratie kunt u onbewerkte logboeken van uw Azure-resources integreren in de bestaande Security Information and Event Management-systemen (SIEM) van uw organisatie. [Azure-logboek integratie](/previous-versions/azure/security/fundamentals/azure-log-integration-overview) verzamelt Windows-gebeurtenissen van Windows Logboeken-Logboeken en Azure-resources vanaf:

* Activiteiten logboeken van Azure
* Azure Security Center-waarschuwingen
* Azure-resourcelogboeken

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Aanvullende stappen voor organisaties die toegang tot andere Cloud-apps beheren via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Gebruikers inrichten voor verbonden Apps implementeren

Met Azure AD kunt u het maken en onderhouden van gebruikers identiteiten in Cloud-apps, zoals Dropbox, Sales Force en ServiceNow automatiseren. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure AD automatiseren](../app-provisioning/user-provisioning.md)voor meer informatie.

#### <a name="integrate-information-protection"></a>Gegevens beveiliging integreren

Met Microsoft Cloud App Security kunt u bestanden onderzoeken en beleid instellen op basis van Azure Information Protection classificatie labels, waardoor u meer inzicht krijgt in en controle hebt over uw Cloud gegevens. Bestanden in de Cloud scannen en classificeren en Azure Information Protection-labels Toep assen. Zie [Azure Information Protection-integratie](/cloud-app-security/azip-integration)voor meer informatie.

#### <a name="configure-conditional-access"></a>Voorwaardelijke toegang configureren

Configureer voorwaardelijke toegang op basis van een groep, locatie en toepassings gevoeligheid voor [SaaS-apps](https://azure.microsoft.com/overview/what-is-saas/) en Azure AD Connected apps. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Activiteit in verbonden Cloud-apps bewaken

U kunt het beste [Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) gebruiken om ervoor te zorgen dat de gebruikers toegang ook wordt beveiligd in verbonden toepassingen. Deze functie beveiligt de bedrijfs toegang tot Cloud-apps en beveiligt uw beheerders accounts, zodat u het volgende kunt doen:

* Zicht baarheid en beheer voor Cloud-apps uitbreiden
* Beleids regels maken voor toegang, activiteiten en het delen van gegevens
* Automatisch Risk ante activiteiten, abnormaal gedrag en bedreigingen identificeren
* Lekkage van gegevens voor komen
* Risico's en automatische bedreigings preventie en beleids afdwinging minimaliseren

De Cloud App Security SIEM-Agent integreert Cloud App Security met uw SIEM-server om gecentraliseerde bewaking van Microsoft 365 waarschuwingen en activiteiten mogelijk te maken. Het wordt uitgevoerd op uw server en er worden waarschuwingen en activiteiten opgehaald van Cloud App Security en deze worden naar de SIEM-server gestreamd. Zie [Siem-integratie](/cloud-app-security/siem)voor meer informatie.

## <a name="stage-4-continue-building-defenses"></a>Fase 4: door gaan met het bouwen van verdediging

![Fase 4: een actieve beveiligings postuur](./media/security-planning/stage-four.png)

Fase 4 van het schema moet worden geïmplementeerd op zes maanden en verder. Voltooi uw plan om uw bevoorrechte toegangs beveiligingen te versterken tegen mogelijke aanvallen die tegenwoordig bekend zijn. Voor de beveiligings Risico's van morgen raden wij aan de beveiliging te bekijken als een doorlopend proces om de kosten te verhogen en het slagings percentage van aanvallers gericht op uw omgeving te verminderen.

Het beveiligen van bevoegde toegang is belang rijk voor het instellen van beveiligings garanties voor uw bedrijfs middelen. Het moet echter deel uitmaken van een volledig beveiligings programma dat permanente beveiligings garanties biedt. Dit programma moet elementen bevatten zoals:

* Beleid
* Operations
* Gegevens beveiliging
* Servers
* Toepassingen
* Pc's
* Apparaten
* Cloud infrastructuur

We raden u aan de volgende procedures uit te voeren wanneer u accounts voor bevoegde toegang beheert:

* Zorg ervoor dat beheerders hun dagelijkse bedrijfs activiteiten doen als niet-gemachtigde gebruikers
* Ken alleen uitgebreide toegang toe wanneer dit nodig is en verwijder deze later (just-in-time)
* Controle activiteiten logboeken met betrekking tot bevoegde accounts houden

Zie voor meer informatie over het bouwen van een volledig beveiligings schema [micro soft Cloud IT-architectuur bronnen](https://almbok.com/office365/microsoft_cloud_it_architecture_resources). Neem contact op met uw micro soft-vertegenwoordiger of Zie [essentiële Cyber-verdediging bouwen om uw bedrijf te beschermen](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)als u wilt deel nemen aan micro soft-Services om u te helpen bij het implementeren van uw plan.

Deze laatste voortdurende fase van het beveiligde privileged Access-schema bevat de volgende onderdelen.

### <a name="general-preparation"></a>Algemene voor bereiding

#### <a name="review-admin-roles-in-azure-ad"></a>Beheerders rollen controleren in azure AD

Bepaal of de huidige ingebouwde Azure AD-beheerders rollen nog steeds up-to-date zijn en zorg ervoor dat gebruikers alleen de rollen hebben die ze nodig hebben. Met Azure AD kunt u afzonderlijke beheerders toewijzen om verschillende functies te leveren. Zie [beheerders rollen toewijzen in azure Active Directory](permissions-reference.md)voor meer informatie.

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Gebruikers met een beheer van aan Azure AD gekoppelde apparaten bekijken

Zie [Configure hybrid Azure Active Directory joind devices](../devices/hybrid-azuread-join-plan.md)(Engelstalig) voor meer informatie.

#### <a name="review-members-of-built-in-microsoft-365-admin-roles"></a>Leden van [ingebouwde Microsoft 365 beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) controleren
Sla deze stap over als u geen gebruik maakt van Microsoft 365.
‎
#### <a name="validate-incident-response-plan"></a>Reactie plan voor incidenten valideren

Om uw abonnement te verbeteren, raadt micro soft u aan regel matig te controleren of uw plan op de verwachte wijze werkt:

* Door loop uw bestaande weg kaart om te zien wat er is gemist
* Op basis van de analyse van postmortem kunt u bestaande procedures herzien of nieuwe prak tijken definiëren
* Zorg ervoor dat uw bijgewerkte reactie plan voor incidenten en procedures worden gedistribueerd in uw hele organisatie


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die de toegang tot Azure beheren 

Bepaal of u [het eigendom van een Azure-abonnement naar een ander account moet overdragen](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Glas aflichten": wat u in een nood geval moet doen

![Accounts voor toegang tot nood gevallen](./media/security-planning/emergency.jpeg)

1. Meld belang rijke managers en beveiligings ambtenaren met informatie over het incident.

2. Bekijk de Playbook van uw aanval.

3. Open de combi natie van gebruikers naam en wacht woord voor afbreek glas om u aan te melden bij Azure AD.

4. Krijg hulp van micro soft door [een ondersteunings aanvraag voor Azure te openen](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Bekijk de [aanmeldings rapporten van Azure AD](../reports-monitoring/overview-reports.md). Het kan enige tijd duren tussen een gebeurtenis die optreedt en wanneer deze in het rapport wordt opgenomen.

6. Als uw on-premises infra structuur Federated en uw AD FS-server niet beschikbaar zijn voor hybride omgevingen, kunt u tijdelijk overschakelen van Federated Authentication naar het gebruik van wacht woord-hash-synchronisatie. Met deze switch wordt de domein Federatie teruggezet naar beheerde verificatie totdat de AD FS-server beschikbaar is.

7. Controleer e-mail op bevoegde accounts.

8. Zorg ervoor dat u back-ups van relevante logboeken opslaat voor mogelijke forensische en juridisch onderzoek.

Voor meer informatie over hoe Microsoft Office 365 beveiligings incidenten afhandelt, raadpleegt u [Security Incident Management in Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-answers-for-securing-privileged-access"></a>Veelgestelde vragen: antwoorden voor het beveiligen van bevoegde toegang  

**V:** Wat moet ik doen als ik nog geen beveiligde toegangs onderdelen heb geïmplementeerd?

**Antwoord:** Definieer ten minste twee account voor een afbreek punt, wijs MFA toe aan uw bevoegde beheerders accounts en scheid gebruikers accounts van globale beheerders accounts.

**V:** Wat is het belangrijkste probleem dat eerst moet worden opgelost na een schending?

**Antwoord:** Zorg ervoor dat u de krach tigste verificatie voor Maxi maal beschik bare individuen nodig hebt.

**V:** Wat gebeurt er als onze geprivilegieerde beheerders zijn gedeactiveerd?

**Antwoord:** Maak een algemeen beheerders account dat altijd up-to-date blijft.

**V:** Wat gebeurt er als er slechts één globale beheerder is en deze niet kan worden bereikt?

**Antwoord:** Gebruik een van uw afbreek glazen om direct privileged Access te verkrijgen.

**V:** Hoe kan ik beheerders in mijn organisatie beveiligen?

**Antwoord:** Laat beheerders altijd hun dagelijkse bedrijfs activiteiten doen als standaard gebruikers zonder privileged.

**V:** Wat zijn de aanbevolen procedures voor het maken van beheerders accounts in azure AD?

**Antwoord:** Reserveer uitgebreide toegang voor specifieke beheerders taken.

**V:** Welke hulpprogram ma's bestaan er voor het verminderen van permanente beheerders toegang?

**Antwoord:** Privileged Identity Management (PIM) en Azure AD-beheerders rollen.

**V:** Wat is de positie van micro soft bij het synchroniseren van beheerders accounts met Azure AD?

**Antwoord:** De beheerders accounts op laag 0 worden alleen gebruikt voor on-premises AD-accounts. Dergelijke accounts worden doorgaans niet gesynchroniseerd met Azure AD in de Cloud. Beheerders accounts op laag 0 zijn accounts, groepen en andere assets die directe of indirecte administratieve controle hebben over de on-premises Active Directory forest, domeinen, domein controllers en assets.

**V:** Hoe kunnen beheerders ervoor zorgen dat er geen wille keurige beheerders toegang worden toegewezen in de portal?

**Antwoord:** Gebruik niet-privilegede accounts voor alle gebruikers en de meeste beheerders. Begin met het ontwikkelen van een footprint van de organisatie om te bepalen welke beheerders accounts privileged moeten zijn. En controleren op nieuwe gebruikers met beheerders rechten.

## <a name="next-steps"></a>Volgende stappen

* [Micro soft vertrouwens centrum voor product beveiliging](https://www.microsoft.com/trustcenter/security) – beveiligings functies van micro soft-Cloud producten en-services

* [Micro soft vertrouwens centrum-naleving](https://www.microsoft.com/trustcenter/compliance/complianceofferings) : de uitgebreide set nalevings aanbiedingen van micro soft voor Cloud Services

* [Richt lijnen voor het uitvoeren van een risico analyse](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -vereisten voor beveiliging en naleving beheren voor micro soft-Cloud Services

### <a name="other-microsoft-online-services"></a>Andere online services van micro soft

* [Microsoft intune beveiliging](https://www.microsoft.com/trustcenter/security/intune-security) : intune biedt mogelijkheden voor Mobile Device Management, Mobile Application Management en PC-beheer vanuit de Cloud.

* [Micro soft dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) : Dynamics 365 is de micro soft-Cloud oplossing voor het samen delen van CRM-en Enter prise resource planning (ERP)-mogelijkheden.
