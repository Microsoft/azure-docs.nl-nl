---
title: Naslag informatie over Azure Active Directory algemene bedienings handleiding
description: In deze Naslag Gids voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om algemene bewerkingen te beveiligen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d5a8fe4192c3778e259ed18239a4198398d8807b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836832"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Naslag informatie over Azure Active Directory algemene bedienings handleiding

In deze sectie van de [Naslag Gids voor Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven die u moet uitvoeren om de algemene bewerkingen van Azure Active Directory (Azure AD) te optimaliseren.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de datum van publicatie, maar kunnen worden gewijzigd in de loop van de tijd. Organisaties moeten voortdurend hun operationele procedures evalueren naarmate micro soft-producten en-services zich in de loop van de tijd ontwikkelen.

## <a name="key-operational-processes"></a>Belangrijkste operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigen aren toewijzen aan belang rijke taken

Voor het beheren van Azure Active Directory is de continue uitvoering van belang rijke operationele taken en-processen vereist, die mogelijk geen deel uitmaken van een implementatie project. Het is nog belang rijk dat u deze taken instelt om uw omgeving te optimaliseren. De belangrijkste taken en de aanbevolen eigen aren zijn onder andere:

| Taak | Eigenaar |
| :- | :- |
| Verbeteringen op het gebied van identiteits beveiliging | InfoSec Operations-team |
| Azure AD Connect-servers onderhouden | IAM Operations-team |
| Regel matig IdFix-rapporten uitvoeren en sorteren | IAM Operations-team |
| Sorteren Waarschuwingen Azure AD Connect Health voor synchronisatie en AD FS | IAM Operations-team |
| Als u Azure AD Connect Health niet gebruikt, heeft de klant gelijkwaardig proces en hulpprogram ma's voor het controleren van de aangepaste infra structuur | IAM Operations-team |
| Als u AD FS niet gebruikt, heeft de klant gelijkwaardig proces en hulpprogram ma's voor het controleren van de aangepaste infra structuur | IAM Operations-team |
| Hybride logboeken bewaken: Azure AD-app proxy-connectors | IAM Operations-team |
| Hybride logboeken controleren: Passthrough-verificatie agenten | IAM Operations-team |
| Hybride logboeken controleren: wacht woord terugschrijven service | IAM Operations-team |
| Hybride logboeken controleren: on-premises wachtwoord beveiligings gateway | IAM Operations-team |
| Hybride logboeken controleren: Azure AD MFA NPS-extensie (indien van toepassing) | IAM Operations-team |

Wanneer u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken waarvoor een eigenaar ontbreekt of het eigendom van taken met eigen aars aanpassen die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="owners-recommended-reading"></a>Eigen aren aanbevolen lezen

- [Beheerdersrollen toewijzen in Azure Active Directory](../roles/permissions-reference.md)
- [Governance in Azure](../../governance/index.yml)

## <a name="hybrid-management"></a>Hybride beheer

### <a name="recent-versions-of-on-premises-components"></a>Recente versies van on-premises onderdelen

Als u de meest recente versies van on-premises onderdelen hebt, biedt de klant alle nieuwste beveiligings updates, prestatie verbeteringen en functionaliteit waarmee u de omgeving verder kunt vereenvoudigen. De meeste onderdelen hebben een automatische upgrade-instelling, waarmee het upgrade proces wordt geautomatiseerd.

Deze onderdelen zijn onder andere:

- Azure AD Connect
- Azure AD Application Proxy-connectors
- Pass Through-verificatie agenten voor Azure AD
- Azure AD Connect Health Agents

Tenzij er een procedure is ingesteld, moet u een proces definiëren om deze onderdelen bij te werken en waar mogelijk afhankelijk te zijn van de functie voor automatische upgrades. Als u onderdelen vindt die zes of meer maanden achter zijn, moet u zo snel mogelijk een upgrade uitvoeren.

#### <a name="hybrid-management-recommended-reading"></a>Aanbevolen Lees bewerkingen voor hybride beheer

- [Azure AD Connect: automatische upgrade](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Meer informatie over Azure AD-toepassingsproxy-connectors | Automatische updates](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Basis lijn van Azure AD Connect Health waarschuwing

Organisaties moeten [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) implementeren voor het bewaken en rapporteren van Azure AD Connect en AD FS. Azure AD Connect en AD FS zijn essentiële onderdelen die levenscyclus beheer en-verificatie kunnen verstoren en daarom leiden tot storingen. Azure AD Connect Health helpt bij het bewaken en verkrijgen van inzicht in uw on-premises identiteits infrastructuur, waardoor de betrouw baarheid van uw omgeving wordt gegarandeerd.

![Azure AD Connect Health-architectuur](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Wanneer u de status van uw omgeving bewaken, moet u onmiddellijk waarschuwingen met hoge Ernst aanpakken, gevolgd door waarschuwingen met een lagere ernst.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health aanbevolen lezen

- [De Azure AD Connect Health-agent installeren](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Logboeken voor on-premises agents

Voor sommige services voor identiteits-en toegangs beheer zijn on-premises agents vereist om hybride scenario's in te scha kelen. Voor beelden zijn het opnieuw instellen van wacht woorden, Pass Through-verificatie (PTA), Azure AD-toepassingsproxy en Azure AD MFA-extensie NPS. Het is belang rijk dat de basis lijn van het operations-team en de status van deze onderdelen bewaken door de logboeken van de onderdeel agent te archiveren en te analyseren met behulp van oplossingen als System Center Operations Manager of SIEM. Het is even belang rijk dat uw INFOSEC-bedrijfs team of de Help Desk inzicht krijgt in het oplossen van patronen met fouten.

#### <a name="on-premises-agents-logs-recommended-reading"></a>On-premises agents logboeken aanbevolen lezen

- [Problemen met toepassingsproxy oplossen](../manage-apps/application-proxy-troubleshoot.md)
- [Oplossen van problemen met de selfservice voor wachtwoord herstel-Azure Active Directory](../authentication/troubleshoot-sspr.md)
- [Azure AD-toepassingsproxy-connectors begrijpen](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: problemen met Pass Through-verificatie oplossen](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Problemen met fout codes voor de NPS-extensie van Azure AD MFA oplossen](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Beheer van on-premises agents

Het hand nemen van aanbevolen procedures kan de optimale werking van on-premises agents helpen. Houd rekening met de volgende aanbevolen procedures:

- Meerdere Azure AD-toepassings proxy-connectors per connector groep worden aanbevolen om een naadloze taak verdeling en hoge Beschik baarheid te bieden door afzonderlijke storings punten te vermijden bij het openen van de proxy toepassingen. Als er slechts één connector in een connector groep aanwezig is die toepassingen in productie verwerkt, moet u ten minste twee connectors voor redundantie implementeren.
- Het maken en gebruiken van een app proxy connector-groep voor fout opsporing kan nuttig zijn bij het oplossen van problemen met scenario's en bij het voorbereiden van nieuwe on-premises toepassingen. We raden u ook aan om netwerk hulpprogramma's als Message Analyzer en Fiddler op de connector computers te installeren.
- Meerdere Pass Through-verificatie agenten worden aanbevolen voor een naadloze taak verdeling en hoge Beschik baarheid door Single Point of Failure te vermijden tijdens de verificatie stroom. Zorg ervoor dat u ten minste twee Pass-Through-verificatie agenten implementeert voor redundantie.

#### <a name="on-premises-agents-management-recommended-reading"></a>Beheer van on-premises agents aanbevolen lezen

- [Azure AD-toepassingsproxy-connectors begrijpen](../manage-apps/application-proxy-connectors.md)
- [Pass-Through-verificatie van Azure AD-Quick Start](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Beheer op schaal

### <a name="identity-secure-score"></a>Identity Secure Score

De [beveiligde Score](./identity-secure-score.md) van de identiteit biedt een meet bare meting van de beveiligings postuur van uw organisatie. Het is belang rijk om voortdurend te controleren en gerapporteerde bevindingen te verhelpen en de hoogste score mogelijk te houden. De score helpt bij het volgende:

- Objectief meten van het beveiligingspostuur van uw identiteit
- Plannen van verbeteringen aan de identiteitsbeveiliging
- Evalueren van het succes van uw verbeteringen

![Beveiligingsscore](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Als uw organisatie momenteel geen programma heeft om wijzigingen in identiteits beveiliging te bewaken, wordt u aangeraden een plan te implementeren en eigen aren toe te wijzen voor het bewaken en verbeteren van de actie. Organisaties moeten verbeterings acties met een Score van meer dan 30 zo snel mogelijk oplossen.

### <a name="notifications"></a>Meldingen

Micro soft stuurt e-mail berichten naar beheerders om verschillende wijzigingen in de service te melden, configuratie-updates die nodig zijn en fouten waarvoor beheerders rechten nodig zijn. Het is belang rijk dat klanten de e-mail adressen van meldingen instellen, zodat er meldingen worden verzonden naar de juiste team leden die alle meldingen kunnen erkennen en hierop reageren. We raden u aan meerdere ontvangers toe te voegen aan het [berichten centrum](/office365/admin/manage/message-center) en de meldingen (inclusief Azure AD Connect Health meldingen) te verzenden naar een distributie lijst of een gedeeld postvak. Als u slechts één globaal beheerders account met een e-mail adres hebt, moet u ervoor zorgen dat u ten minste twee accounts met e-mail mogelijkheden configureert.

Er zijn twee ' from '-adressen die worden gebruikt door Azure AD: <o365mc@email2.microsoft.com> , die meldingen in het berichten centrum verzendt; en <azure-noreply@microsoft.com> , waarmee meldingen worden verzonden die betrekking hebben op:

- [Azure AD-toegangs beoordelingen](../governance/access-reviews-overview.md)
- [Azure AD Connect Health (Engelstalig)](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD-identiteitsbeveiliging](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [Certificaat meldingen voor de Enter prise-app verlopen](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Service meldingen voor de Enter prise-app inrichten

Raadpleeg de volgende tabel voor meer informatie over het type meldingen dat wordt verzonden en waar u deze kunt controleren:

| Meldings bron | Wat wordt verzonden | Waar u moet controleren |
|:-|:-|:-|
| Technische contact persoon | Synchronisatie fouten | Azure Portal-Blade-eigenschappen |
| Berichten centrum | Meldingen over incidenten en degradatie van identiteits Services en Microsoft 365 back-end-services | Office-Portal |
| Overzicht van identiteits beveiliging wekelijks | Samen vatting van identiteits beveiliging | Azure AD Identity Protection Blade |
| Azure AD Connect Health (Engelstalig) | Waarschuwings meldingen | Azure Portal-Azure AD Connect Health Blade |
| Meldingen voor zakelijke toepassingen | Meldingen wanneer certificaten bijna verlopen en inrichtings fouten bevatten | Azure Portal-Blade bedrijfs toepassing (elke app heeft een eigen instelling voor het e-mail adres) |

#### <a name="notifications-recommended-reading"></a>Meldingen aanbevolen lezen

- [Het adres, de technische contact persoon en meer van uw organisatie wijzigen](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Operationele surface area

### <a name="ad-fs-lockdown"></a>AD FS Lockdown

Organisaties, waarmee toepassingen worden geconfigureerd om rechtstreeks naar Azure AD te worden geprofiteerd van [Azure AD Smart-vergren deling](../authentication/concept-sspr-howitworks.md). Als u AD FS gebruikt in Windows Server 2012 R2, implementeert u AD FS [beveiliging voor vergren delen](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)van het extranet. Als u AD FS op Windows Server 2016 of hoger gebruikt, moet u [extranet slimme vergren deling](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)implementeren. We raden u aan om extranet vergrendeling in te scha kelen om het risico van beveiligings aanvallen tegen een on-premises Active Directory te bevatten. Als u echter AD FS hebt in Windows 2016 of hoger, moet u ook smartcard vergrendeling inschakelen die helpt bij het oplossen van aanvallen op [wachtwoord](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) niveau.

Als AD FS alleen wordt gebruikt voor Azure AD Federation, zijn er een aantal eind punten die kunnen worden uitgeschakeld om de aanval surface area te minimaliseren. Als AD FS bijvoorbeeld alleen wordt gebruikt voor Azure AD, moet u WS-Trust eind punten uitschakelen, behalve de eind punten die zijn ingeschakeld voor **usernamemixed** en **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Toegang tot computers met on-premises identiteits onderdelen

Organisaties moeten de toegang tot de machines met on-premises hybride onderdelen op dezelfde manier als uw on-premises domein vergren delen. Zo mag een back-upoperator of Hyper-V-beheerder zich niet kunnen aanmelden bij de Azure AD Connect server om regels te wijzigen.

Het Active Directory administratieve laag model is ontworpen om identiteits systemen te beveiligen met behulp van een set buffer zones tussen volledig beheer van de omgeving (laag 0) en de werk station-assets met een hoog risico dat aanvallers vaak misbruiken. ![Diagram van de drie lagen van het lagenmodel](./media/active-directory-ops-guide/active-directory-ops-img18.png)

Het [laag model](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) bestaat uit drie niveaus en bevat alleen administratieve accounts, niet standaard gebruikers accounts.

- **Laag 0**: directe controle over de ondernemings-id's in de omgeving. Laag 0 bevat accounts, groepen en andere activa die directe of indirecte controle hebben over het beheer van Active Directory-forest, -domeinen of -domeincontrollers en alle activa daarvan. De beveiliging van alle activa op laag 0 is even gevoelig omdat ze elkaar in stand houden.
- **Laag 1**: controle over bedrijfsservers en -toepassingen. De activa op laag 1 zijn serverbesturingssystemen, cloudservices en bedrijfstoepassingen. De administratoraccounts op laag 1 hebben controle over het beheer van een groot deel van de bedrijfswaarde die op deze activa wordt gehost. Een veelgebruikt voorbeeld is de rol van de serverbeheerders. Zij onderhouden deze besturingssystemen en kunnen invloed uitoefenen op alle bedrijfsservices.
- **Laag 2**: de controle over werkstations en apparaten van gebruikers. De administratoraccounts op laag 2 hebben controle over het beheer van een groot deel van de bedrijfswaarde die op werkstations en apparaten van gebruikers wordt gehost. Voorbeelden zijn de medewerkers van de helpdesk en computerondersteuning. Zij hebben immers invloed op de integriteit van nagenoeg alle gebruikersgegevens.

Vergrendel de toegang tot on-premises identiteits onderdelen, zoals Azure AD Connect, AD FS en SQL-services op dezelfde manier als voor domein controllers.

## <a name="summary"></a>Samenvatting

Er zijn zeven aspecten van een veilige identiteits infrastructuur. Deze lijst helpt u de acties te vinden die u moet ondernemen om de bewerkingen voor Azure Active Directory (Azure AD) te optimaliseren.

- Eigen aren toewijzen aan belang rijke taken.
- Automatiseer het upgrade proces voor on-premises hybride onderdelen.
- Implementeer Azure AD Connect Health voor het bewaken en rapporteren van Azure AD Connect en AD FS.
- Bewaak de status van on-premises hybride onderdelen door de logboeken van de onderdeel agent te archiveren en te analyseren met behulp van System Center Operations Manager of een SIEM-oplossing.
- Implementeer beveiligings verbeteringen door uw beveiligings postuur te meten met een beveiligde score voor identiteiten.
- AD FS vergren delen.
- Vergrendel de toegang tot computers met on-premises identiteits onderdelen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Azure AD-implementatie plannen](active-directory-deployment-plans.md) voor implementatie details over alle mogelijkheden die u niet hebt geïmplementeerd.