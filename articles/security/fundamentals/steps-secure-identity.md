---
title: Uw Azure AD-identiteits infrastructuur beveiligen
titleSuffix: Azure Active Directory
description: In dit document wordt een overzicht van belang rijke acties beschreven die beheerders moeten implementeren om hun organisatie te helpen beveiligen met Azure AD-mogelijkheden
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: 4703a76ff0a0b55a438f9f0d164cd7f2d3242a02
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842019"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Vijf stappen voor het beveiligen van uw identiteits infrastructuur

Als u dit document leest, bent u op de hoogte van de significantie van de beveiliging. Waarschijnlijk hebt u de verantwoordelijkheid voor het beveiligen van uw organisatie. Als u anderen van het belang van beveiliging wilt overzien, kunt u ze verzenden om het meest recente [micro soft Security Intelligence-rapport](https://www.microsoft.com/security/business/security-intelligence-report)te lezen.

Dit document helpt u een veiliger postuur te krijgen met behulp van de mogelijkheden van Azure Active Directory met behulp van een controle lijst van vijf stappen voor het inoculate van uw organisatie tegen Cyber aanvallen.

Deze controle lijst helpt u bij het snel implementeren van kritieke aanbevolen acties om uw organisatie onmiddellijk te beschermen door te verklaren hoe u:

* Versterk uw referenties.
* Verminder de aanvals surface area.
* Het antwoord op bedreigingen automatiseren.
* Gebruik Cloud Intelligence.
* Self-service voor eind gebruikers inschakelen.

Zorg ervoor dat u bijhoudt welke functies en stappen zijn voltooid tijdens het lezen van deze controle lijst.

> [!NOTE]
> Veel van de aanbevelingen in dit document gelden alleen voor toepassingen die zijn geconfigureerd om Azure Active Directory als id-provider te gebruiken. Het configureren van apps voor één Sign-On garandeert dat referentie beleid, detectie van bedreigingen, controle, logboek registratie en andere functies aan deze toepassingen worden toegevoegd. [Azure AD-toepassings beheer](../../active-directory/manage-apps/what-is-application-management.md) is de basis waarop al deze aanbevelingen zijn gebaseerd.

De aanbevelingen in dit document zijn afgestemd op de [identiteits veilige Score](../../active-directory/fundamentals/identity-secure-score.md), een geautomatiseerde evaluatie van de identiteits beveiligings configuratie van uw Azure AD-Tenant. Organisaties kunnen de pagina identiteits beveiliging in de Azure AD-Portal gebruiken om hiaten in hun huidige beveiligings configuratie te vinden om te controleren of ze de huidige [Aanbevolen procedures](identity-management-best-practices.md) van micro soft voor beveiliging volgen. Wanneer u elke aanbeveling op de pagina beveiligde Score implementeert, wordt uw score verhoogd en kunt u uw voortgang volgen, plus de hulp bij het vergelijken van uw implementatie met andere vergelijk bare organisaties of uw branche.

![Identiteits veilige Score](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Voor veel van de functies die hier worden beschreven, is een Azure AD Premium-abonnement vereist, terwijl sommige gratis zijn. Raadpleeg onze [Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory/) en de [controle lijst voor Azure AD-implementatie](../../active-directory/fundamentals/active-directory-deployment-checklist-p2.md) voor meer informatie.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Voordat u begint: beschermde accounts beveiligen met MFA

Voordat u aan deze controle lijst begint, moet u ervoor zorgen dat u niet meer in het gedrang komt wanneer u deze controle lijst leest. U moet eerst uw bevoorrechte accounts beveiligen.

Aanvallers die het beheer van geprivilegieerde accounts krijgen, kunnen grote schade toebrengen, dus het is essentieel om deze accounts eerst te beveiligen. [Azure ad multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) inschakelen en vereisen voor alle beheerders in uw organisatie met behulp van [Azure AD-beveiligings standaards](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) of [voorwaardelijke toegang](../../active-directory/conditional-access/plan-conditional-access.md). Als u MFA nog niet hebt geïmplementeerd, doet u dat nu. Dat is belang rijk.

Alle sets? Laten we aan de slag met de controle lijst.

## <a name="step-1---strengthen-your-credentials"></a>Stap 1: uw referenties versterken

De meeste beveiligings schendingen van een onderneming zijn afkomstig van een account met een van de verschillende methoden, zoals wachtwoord spray, het opnieuw afspelen of het afwijzen van een aanval. Meer informatie over deze aanvallen vindt u in deze video (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Zorg ervoor dat uw organisatie gebruikmaakt van sterke verificatie

Gezien de frequentie van wacht woorden die worden geschat, vergoedd, gestolen met malware of hergebruikt, is het essentieel om het wacht woord te herstellen met een vorm van sterke referenties. meer informatie over [Azure AD-multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

Als u het basis niveau van identiteits beveiliging eenvoudig wilt inschakelen, kunt u de activering met één klik gebruiken met de [standaard instellingen van Azure AD](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md). Met de standaard instellingen voor beveiliging wordt Azure AD MFA afgedwongen voor alle gebruikers in een Tenant en worden aanmeldingen van verouderde protocollen Tenant-breed geblokkeerd.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Start verbieden doorgaans aangevallen wacht woorden en schakel de traditionele complexiteit en de verval regels uit.

Veel organisaties gebruiken de traditionele complexiteit (voor het vereisen van speciale tekens, cijfers, hoofd letters en kleine letters) en regels voor het verlopen van wacht woorden. [Het onderzoek van micro soft](https://aka.ms/passwordguidance) heeft deze beleids regels weer gegeven, zodat gebruikers wacht woorden kunnen kiezen die gemakkelijker te raden zijn.

De functie [dynamisch uitgesloten wacht woord](../../active-directory/authentication/concept-password-ban-bad.md) van Azure AD maakt gebruik van het huidige gedrag van aanvallers om te voor komen dat gebruikers wacht woorden instellen die gemakkelijk kunnen worden geraden. Deze mogelijkheid is altijd ingeschakeld wanneer gebruikers in de cloud worden gemaakt, maar zijn nu ook beschikbaar voor hybride organisaties wanneer ze [Azure AD-wachtwoord beveiliging implementeren voor Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md). Azure AD-wachtwoord beveiliging blokkeert gebruikers bij het kiezen van deze algemene wacht woorden en kan worden uitgebreid om wacht woorden te blok keren met aangepaste tref woorden die u opgeeft. U kunt bijvoorbeeld voor komen dat uw gebruikers wacht woorden kiezen met de product namen van uw bedrijf of een team van een lokale sport.

Micro soft raadt aan het volgende moderne wachtwoord beleid te nemen op basis van de [richt lijnen voor NIST](https://pages.nist.gov/800-63-3/sp800-63b.html):

1. Vereisen dat wacht woorden ten minste 8 tekens bevatten. Langer is niet noodzakelijkerwijs beter, omdat gebruikers voorspel bare wacht woorden kunnen kiezen, wacht woorden kunnen opslaan in bestanden of ze naar beneden kunnen schrijven.
2. De verval regels uitschakelen, waarmee gebruikers eenvoudig wacht woorden kunnen raden zoals **Spring2019!**
3. Schakel de vereisten voor het samen stellen van tekens uit en voorkom dat gebruikers veelvoorkomende wacht woorden kiezen, omdat gebruikers ervoor zorgen dat ze voorspel bare teken vervangingen in wacht woorden kiezen.

U kunt [Power shell gebruiken om te voor komen dat wacht woorden verlopen](../../active-directory/authentication/concept-sspr-policy.md) voor gebruikers als u rechtstreeks identiteiten maakt in azure AD. Hybride organisaties moeten deze beleids regels implementeren met behulp van [instellingen voor groeps beleid](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) van het domein of [Windows Power shell](/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Beveiligen tegen gelekte referenties en tolerantie toevoegen aan storingen

Als uw organisatie gebruikmaakt van een hybride identiteits oplossing met Pass-Through-verificatie of Federatie, moet u de wachtwoord hash-synchronisatie inschakelen om de volgende twee redenen:

* Het rapport [gebruikers met gelekte referenties](../../active-directory/identity-protection/overview-identity-protection.md) in het Azure AD-beheer waarschuwt u voor gebruikers naam-en wachtwoord paren, die zijn weer gegeven op het ' donkere Web '. Een ongelooflijke hoeveelheid wacht woorden wordt gelekt via phishing, malware en het opnieuw gebruiken van wacht woorden op sites van derden die later zijn geschonden. Micro soft vindt veel van deze gelekte referenties en vertelt u, in dit rapport, als ze overeenkomen met referenties in uw organisatie, maar alleen als u de [synchronisatie van wacht woord-hashes inschakelt](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).
* In het geval van een on-premises storing (bijvoorbeeld bij een Ransomware-aanval) kunt u overschakelen naar het gebruik van [Cloud verificatie met behulp van wachtwoord-hash-synchronisatie](../../active-directory/hybrid/choose-ad-authn.md). Met deze methode voor back-upauthenticatie kunt u de toegang tot apps die zijn geconfigureerd voor authenticatie met Azure Active Directory, inclusief Microsoft 365 blijven gebruiken. In dit geval hoeft de IT-afdeling geen persoonlijke e-mail accounts te maken om gegevens te delen totdat de on-premises storing is opgelost.

Meer informatie over hoe [wachtwoord-hash-synchronisatie](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) werkt.

> [!NOTE]
> Als u wacht woord-hash-synchronisatie inschakelt en Azure AD Domain Services gebruikt 256, worden de hashes en optioneel NTLM-hashes (RC4, no zout) ook versleuteld en gesynchroniseerd met Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>AD FS extranet-smartcard vergrendeling implementeren

Organisaties, waarmee toepassingen worden geconfigureerd om rechtstreeks naar Azure AD te worden geprofiteerd van [Azure AD Smart-vergren deling](../../active-directory/authentication/concept-sspr-howitworks.md). Als u AD FS gebruikt in Windows Server 2012R2, implementeert u AD FS [beveiliging voor vergren delen](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)van het extranet. Als u AD FS gebruikt in Windows Server 2016, moet u [extranet slimme vergren deling](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)implementeren. AD FS Smart-extranet vergrendeling beschermt tegen beveiligings aanvallen, die gericht zijn op AD FS, terwijl wordt voor komen dat gebruikers in Active Directory worden vergrendeld.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Profiteer van intrinsiek veilig, gemakkelijker te gebruiken referenties

Met [Windows hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)kunt u wacht woorden vervangen door sterke twee ledige verificatie op pc's en mobiele apparaten. Deze verificatie bestaat uit een nieuw type gebruikers referentie dat veilig is gekoppeld aan een apparaat en een biometrische of pincode gebruikt.

## <a name="step-2---reduce-your-attack-surface"></a>Stap 2: Verminder de kwets baarheid voor aanvallen

Gezien de begrootings situatie van het wacht woord, is het minimaliseren van het kwets baarheid in uw organisatie essentieel. Door het gebruik van oudere, minder veilige protocollen te elimineren, toegangs punten te beperken en meer controle over administratieve toegang tot resources te bieden, kunt u de aanval verminderen surface area.

### <a name="block-legacy-authentication"></a>Verouderde verificatie blokkeren

Apps die gebruikmaken van hun eigen verouderde methoden om te verifiëren met Azure AD en toegang hebben tot Bedrijfs gegevens, vormt een ander risico voor organisaties. Voor beelden van apps die gebruikmaken van verouderde verificatie zijn POP3-, IMAP4-en SMTP-clients. Verouderde verificatie-apps verifiëren namens de gebruiker en voor komt dat Azure AD geavanceerde beveiligings evaluaties uitvoert. De alternatieve, moderne verificatie vermindert het beveiligings risico, omdat het multi-factor Authentication en voorwaardelijke toegang ondersteunt. We raden u aan om de volgende drie acties uit te voeren:

1. [Verouderde verificatie blok keren als u AD FS gebruikt](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Stel [share point online en Exchange Online in voor gebruik van moderne verificatie](../../active-directory/conditional-access/block-legacy-authentication.md).
3. Als u Azure AD Premium hebt, gebruikt u beleid voor voorwaardelijke toegang om [verouderde verificatie te blok keren](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md), anders de [standaard instellingen voor Azure AD-beveiliging](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)te gebruiken.

### <a name="block-invalid-authentication-entry-points"></a>Ongeldige invoer punten voor verificatie blok keren

Als u de mentale schending wilt gebruiken, moet u de invloed van de gebruikers referenties verminderen wanneer deze zich voordoen. Voor elke app in uw omgeving moet u rekening houden met de geldige use-cases: welke groepen, welke netwerken, welke apparaten en andere elementen worden geautoriseerd, en vervolgens de rest blok keren. Met [voorwaardelijke toegang van Azure AD](../../active-directory/conditional-access/overview.md)kunt u bepalen hoe geautoriseerde gebruikers toegang hebben tot hun apps en resources op basis van specifieke voor waarden die u definieert.

### <a name="restrict-user-consent-operations"></a>Bewerkingen voor gebruikers toestemming beperken

Het is belang rijk om inzicht te krijgen in de verschillende ervaringen van de [Azure AD-toepassings instemming](../../active-directory/develop/application-consent-experience.md), de [typen machtigingen en toestemming](../../active-directory/develop/v2-permissions-and-consent.md)en de implicaties van de beveiligings postuur van uw organisatie. Standaard kunnen alle gebruikers in azure AD toepassingen verlenen die gebruikmaken van het micro soft-identiteits platform voor toegang tot de gegevens van uw organisatie. Hoewel gebruikers in staat stellen om op zichzelf toestemming te geven, kunnen ze eenvoudig nuttige toepassingen verwerven die worden geïntegreerd met Microsoft 365, Azure en andere services. het kan een risico vormen wanneer het niet wordt gebruikt en zorgvuldig wordt gecontroleerd.

Micro soft raadt aan om de toestemming van de gebruiker te beperken om uw surface area te beperken en dit risico te verminderen. U kunt ook [App-toestemming beleid (preview)](../../active-directory/manage-apps/configure-user-consent.md) gebruiken om de toestemming van de eind gebruiker te beperken tot alleen geverifieerde uitgevers en alleen voor de machtigingen die u selecteert. Als de toestemming van de eind gebruiker beperkt is, worden de voorafgaande toestemming toekenningen nog steeds toegepast, maar moeten alle toekomstige toestemmings bewerkingen worden uitgevoerd door een beheerder. Voor beperkte gevallen kunnen gebruikers toestemming van de beheerder aanvragen via een geïntegreerde [aanvraag werk stroom](../../active-directory/manage-apps/configure-admin-consent-workflow.md) voor het beheer van toestemming of via uw eigen ondersteunings processen. Gebruik onze [aanbevelingen](../../active-directory/manage-apps/manage-consent-requests.md) voor het plannen van deze wijziging in uw organisatie voordat u de toestemming van de eind gebruiker beperkt. Als u wilt dat alle gebruikers toegang hebben, kunt u overwegen om toestemming te geven namens [alle gebruikers](../../active-directory/develop/v2-admin-consent.md), en ervoor te zorgen dat gebruikers die nog niet afzonderlijk zijn gemachtigd, toegang hebben tot de app. Als u niet wilt dat deze toepassingen beschikbaar zijn voor alle gebruikers in alle scenario's, gebruikt u [toepassings toewijzing](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) en voorwaardelijke toegang om de gebruikers toegang tot [specifieke apps](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md)te beperken.

Zorg ervoor dat gebruikers goed keuring van de beheerder kunnen aanvragen voor nieuwe toepassingen om de wrijving van de gebruiker te verminderen, het ondersteunings volume te minimaliseren en te voor komen dat gebruikers zich aanmelden voor toepassingen met niet-Azure AD-referenties. Wanneer u uw toestemming bewerkingen hebt geregeld, moeten beheerders de app en de machtigingen regel matig controleren.


### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management implementeren

Een andere impact van ' veronderstelde schending ' is de nood zaak om de kans dat een aangetast account kan worden gebruikt met een geprivilegieerde rol te minimaliseren.

[Azure AD privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) helpt u bij het minimaliseren van de account bevoegdheden door het volgende te helpen:

* Gebruikers identificeren en beheren die zijn toegewezen aan beheerders rollen.
* Begrijp ongebruikte of overmatige bevoegdheden die u moet verwijderen.
* Stel regels op om ervoor te zorgen dat bevoorrechte rollen worden beveiligd door multi-factor Authentication.
* Stel regels op om ervoor te zorgen dat bevoorrechte rollen alleen lang genoeg worden verleend om de bevoegde taak uit te voeren.

Schakel Azure AD PIM in en bekijk vervolgens de gebruikers aan wie beheerders rollen zijn toegewezen en Verwijder overbodige accounts in die rollen. Voor gebruikers met een resterende bevoegdheid kunt u ze permanent verplaatsen naar in aanmerking komende. Tot slot stelt u de juiste beleids regels in om ervoor te zorgen dat ze toegang moeten krijgen tot deze geprivilegieerde rollen. ze kunnen dit zo veilig doen, met het benodigde wijzigings besturings element.

Als onderdeel van de implementatie van uw privileged account, volgt u de [Best Practice voor het maken van ten minste twee nood accounts](../../active-directory/roles/security-planning.md) om ervoor te zorgen dat u nog steeds toegang hebt tot Azure AD als u uw mede werkers vergrendelt.

## <a name="step-3---automate-threat-response"></a>Stap 3-reactie van dreigingen automatiseren

Azure Active Directory heeft veel mogelijkheden die automatisch aanvallen onderscheppen, om de latentie tussen detectie en reactie te verwijderen. U kunt de kosten en risico's verminderen, wanneer u de tijd die criminelen gebruiken, verlaagt om zichzelf in te sluiten in uw omgeving. Dit zijn de concrete stappen die u kunt nemen.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Beveiligings beleid voor gebruikers Risico's implementeren met behulp van Azure AD Identity Protection

Gebruikers risico duidt op de kans dat de identiteit van een gebruiker is aangetast en wordt berekend op basis van de [gebruikers risico detecties](../../active-directory/identity-protection/overview-identity-protection.md) die zijn gekoppeld aan de identiteit van een gebruiker. Een beleid voor gebruikers Risico's is een beleid voor voorwaardelijke toegang waarmee het risico niveau wordt geëvalueerd voor een specifieke gebruiker of groep. Op basis van laag, gemiddeld, hoog risico niveau kan een beleid worden geconfigureerd om de toegang te blok keren of om een beveiligd wachtwoord wijziging te vereisen met multi-factor Authentication. De aanbeveling van micro soft is om een veilige wachtwoord wijziging te vereisen voor gebruikers met een hoog risico.

![Scherm afbeelding toont gebruikers die zijn gemarkeerd voor risico, waarbij een gebruiker is geselecteerd.](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Beleid voor aanmeldings Risico's implementeren met behulp van Azure AD Identity Protection

Aanmeldings risico is de kans dat iemand anders dan de account eigenaar probeert zich aan te melden met behulp van de identiteit. Een [beleid voor aanmeldings Risico's](../../active-directory/identity-protection/overview-identity-protection.md) is een beleid voor voorwaardelijke toegang waarmee het risico niveau wordt geëvalueerd voor een specifieke gebruiker of groep. Op basis van het risico niveau (hoog/gemiddeld/laag) kan een beleid worden geconfigureerd voor het blok keren van de toegang of het afdwingen van multi-factor Authentication. Zorg ervoor dat u multi-factor Authentication afdwingt op gemiddeld of boven risico aanmeldingen.

![Meld u aan bij anonieme Ip's](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Stap 4: Cloud Intelligence gebruiken

Controle en logboek registratie van gebeurtenissen met betrekking tot beveiliging en gerelateerde waarschuwingen zijn essentiële onderdelen van een efficiënte beveiligings strategie. Beveiligings logboeken en rapporten bieden een elektronische record van verdachte activiteiten en helpen u bij het detecteren van patronen die kunnen wijzen op geslaagde, externe indringing van het netwerk en interne aanvallen. U kunt controle gebruiken om gebruikers activiteiten te bewaken, naleving van regelgeving te documenteren, forensische analyse uit te voeren en meer. Waarschuwingen bieden meldingen van beveiligings gebeurtenissen.

### <a name="monitor-azure-ad"></a>Azure AD bewaken

Microsoft Azure Services en-functies bieden u Configureer bare opties voor beveiligings controle en logboek registratie waarmee u hiaten in uw beveiligings beleid en-mechanismen kunt identificeren en deze hiaten kunt aanpakken om inbreuken te voor komen. U kunt [Azure-logboek registratie en-controle](log-audit.md) gebruiken en rapporten met [controle activiteiten gebruiken in de Azure Active Directory Portal](../../active-directory/reports-monitoring/concept-audit-logs.md).

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Azure AD Connect Health in hybride omgevingen bewaken

[Bewaking AD FS met Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) biedt u meer inzicht in potentiële problemen en zicht baarheid van aanvallen op uw AD FS-infra structuur. Azure AD Connect Health biedt waarschuwingen met details, oplossings stappen en koppelingen naar gerelateerde documentatie. gebruiks analyse voor diverse metrische gegevens die betrekking hebben op verificatie verkeer; prestaties bewaken en rapporteren.

![Azure AD Connect Health (Engelstalig)](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD Identity Protection gebeurtenissen bewaken

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) is een hulp programma voor melding, bewaking en rapportage dat u kunt gebruiken om mogelijke beveiligings problemen te detecteren die van invloed zijn op de identiteiten van uw organisatie. Er worden risico detecties gedetecteerd, zoals gelekte referenties, onmogelijk reizen en aanmeldingen vanaf geïnfecteerde apparaten, anonieme IP-adressen, IP-adressen die zijn gekoppeld aan de verdachte activiteit en onbekende locaties. Schakel meldings waarschuwingen in om e-mail te ontvangen van gebruikers die risico lopen en/of een wekelijks overzichts-e-mail.

Azure AD Identity Protection biedt twee belang rijke rapporten die u dagelijks moet bewaken:
1. Met Risk ante aanmeldings rapporten worden aanmeldings activiteiten voor gebruikers gesurfacet die u moet onderzoeken; de rechtmatige eigenaar heeft de aanmelding mogelijk niet uitgevoerd.
2. Risk ante gebruikers rapporten ondervinden gebruikers accounts die mogelijk zijn aangetast, zoals gelekte referenties of de gebruiker die zich heeft aangemeld vanaf verschillende locaties waardoor een reis gebeurtenis wordt veroorzaakt.

![Scherm afbeelding toont het deel venster identiteits beveiliging van Azure A D met gebruikers en hun risico niveaus.](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Apps en toegestuurde machtigingen controleren

Gebruikers kunnen zich leiden tot het navigeren naar een gemanipuleerde website of apps die toegang krijgen tot hun profiel gegevens en gebruikers gegevens, zoals hun e-mail adres. Een kwaadwillende actor kan gebruikmaken van de toegestuurde machtigingen die zijn ontvangen om de inhoud van hun postvak te versleutelen en een Ransom te vragen om uw postvak gegevens opnieuw te verkrijgen. Beheerders moeten de machtigingen van gebruikers [controleren en controleren](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) of de mogelijkheid van gebruikers om toestemming te geven, uitschakelen.

Naast het controleren van de machtigingen die door gebruikers worden gegeven, kunt u [Risk ante of ongewenste OAuth-toepassingen vinden](/cloud-app-security/investigate-risky-oauth) in Premium-omgevingen.

## <a name="step-5---enable-end-user-self-service"></a>Stap 5-selfservice voor eind gebruikers inschakelen

Zo veel mogelijk wilt u de beveiliging met productiviteit balanceren. Op dezelfde manier om uw traject te benaderen met de uitgangspunt die u in de lange periode een basis voor beveiliging instelt, kunt u de wrijving van uw organisatie verwijderen door uw gebruikers te voorzien van de resterende Vigilant.

### <a name="implement-self-service-password-reset"></a>Self-service voor wachtwoord herstel implementeren

De selfservice voor [wachtwoord herstel (SSPR)](../../active-directory/authentication/tutorial-enable-sspr.md) van Azure AD biedt een eenvoudige manier om gebruikers in staat te stellen hun wacht woorden of accounts opnieuw in te stellen of te ontgrendelen zonder tussen komst van de Help Desk of beheerder. Het systeem bevat gedetailleerde rapporten die bijhouden wanneer gebruikers hun wacht woord opnieuw hebben ingesteld, evenals meldingen om u te waarschuwen voor misbruik of misbruik.

### <a name="implement-self-service-group-and-application-access"></a>Selfservice groep en toegang tot toepassingen implementeren

Azure AD biedt u de mogelijkheid om niet-beheerders toegang tot resources te beheren, met behulp van beveiligings groepen, Microsoft 365 groepen, toepassings rollen en toegangs pakket catalogi.  Met [groeps beheer met self-service](../../active-directory/enterprise-users/groups-self-service-management.md) kunnen groeps eigenaren hun eigen groepen beheren, zonder dat er een administratieve rol moet worden toegewezen. Gebruikers kunnen ook Microsoft 365 groepen maken en beheren zonder dat ze afhankelijk zijn van beheerders om hun aanvragen af te handelen, en ongebruikte groepen worden automatisch verlopen.  Het [beheer van rechten van Azure AD](../../active-directory/governance/entitlement-management-overview.md) maakt verdere overdracht en zicht baarheid mogelijk, met uitgebreide werk stromen voor toegangs aanvragen en automatische verval datum.  U kunt delegeren aan niet-beheerders de mogelijkheid om hun eigen toegangs pakketten te configureren voor groepen, teams, toepassingen en share point online-sites waarvan ze eigenaar zijn, met aangepaste beleids regels voor wie de toegang moet goed keuren, waaronder het configureren van managers van werk nemers en sponsors als fiatteurs.

### <a name="implement-azure-ad-access-reviews"></a>Azure AD-toegangs beoordelingen implementeren

Met [Azure AD-toegangs beoordelingen](../../active-directory/governance/access-reviews-overview.md)kunt u toegangs pakket-en groepslid maatschappen, toegang tot bedrijfs toepassingen en geprivilegieerde roltoewijzingen beheren om ervoor te zorgen dat u een beveiligings norm houdt.  Regel matige toezicht door de gebruikers zelf, resource-eigen aren en andere revisoren zorgen ervoor dat gebruikers geen toegang behouden gedurende lange tijd wanneer ze deze niet meer nodig hebben.

## <a name="summary"></a>Samenvatting

Er zijn veel aspecten van een veilige identiteits infrastructuur, maar deze controle lijst voor vijf stappen helpt u bij het snel bereiken van een veiligere en veilige identiteits infrastructuur:

* Versterk uw referenties.
* Verminder de aanvals surface area.
* Het antwoord op bedreigingen automatiseren.
* Gebruik Cloud Intelligence.
* Schakel meer voorspel bare en volledige beveiliging van eind gebruikers met zelf ondersteuning in.

We stellen het op prijs dat u de identiteits beveiliging maakt en hopelijk dit document een handig overzicht is van een veiligere postuur voor uw organisatie.

## <a name="next-steps"></a>Volgende stappen

Als u hulp nodig hebt bij het plannen en implementeren van de aanbevelingen, raadpleegt u de [Azure AD-project implementatie plannen](../../active-directory/fundamentals/active-directory-deployment-plans.md) voor hulp.

Als u zeker weet dat u al deze stappen hebt voltooid, gebruikt u de [beveiligde Score](../../active-directory/fundamentals/identity-secure-score.md)van micro soft, waarmee u op de hoogte blijft van de [nieuwste aanbevolen procedures](identity-management-best-practices.md) en beveiligings Risico's.