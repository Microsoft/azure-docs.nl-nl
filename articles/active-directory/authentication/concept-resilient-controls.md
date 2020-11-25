---
title: Een robuuste strategie voor toegangs beheer maken-Azure AD
description: Dit document bevat richt lijnen voor strategieën die een organisatie moet nemen om flexibiliteit te bieden om het risico van vergren deling tijdens onvoorziene onderbrekingen te verminderen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b5b83d75df734c667c365f20fad2e1f62f997d7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994278"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Maak een flexibele toegangs beheer strategie met Azure Active Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Omdat micro soft moet reageren op veranderende markt omstandigheden, mag het niet worden geïnterpreteerd als een toezeg ging voor het deel van micro soft, en micro soft kan de nauw keurigheid van de gegevens die na de publicatie datum worden gepresenteerd, niet garanderen.

Organisaties die afhankelijk zijn van één toegangs beheer, zoals multi-factor Authentication (MFA) of één netwerk locatie, zijn gevoelig voor toegang tot hun apps en bronnen als één toegangs beheer niet beschikbaar is of niet goed is geconfigureerd. Een natuur ramp kan bijvoorbeeld leiden tot de niet-beschik baarheid van grote segmenten van een telecommunicatie infrastructuur of bedrijfs netwerken. Een dergelijke onderbreking kan verhinderen dat eind gebruikers en beheerders zich kunnen aanmelden.

Dit document bevat richt lijnen voor strategieën die een organisatie moet nemen om flexibiliteit te bieden om het risico van vergren deling te beperken tijdens onvoorziene onderbrekingen met de volgende scenario's:

 1. Organisaties kunnen hun tolerantie verhogen om het risico van vergren deling **vóór een onderbreking** te verminderen door beperkings strategieën of nood plannen te implementeren.
 2. Organisaties kunnen de apps en resources die ze kiezen **tijdens een onderbreking** blijven gebruiken door problemen met de risico beperking en nood plannen te ondervinden.
 3. Organisaties moeten ervoor zorgen dat ze gegevens, zoals Logboeken, bewaren  **na een onderbreking** en voordat ze terugvallen op eventuele nood herstel.
 4. Organisaties die preventie strategieën of alternatieve abonnementen niet hebben geïmplementeerd, kunnen **nood herstel opties** implementeren om de onderbreking te verstoren.

## <a name="key-guidance"></a>Belangrijkste richt lijnen

Er zijn vier belang rijke Takeaways in dit document:

* Voorkom dat de beheerder wordt vergrendeld door middel van toegangs accounts voor nood gevallen.
* Implementeer MFA met behulp van voorwaardelijke toegang (CA) in plaats van MFA per gebruiker.
* De gebruikers vergrendeling beperken met behulp van meerdere besturings elementen voor voorwaardelijke toegang (CA).
* De gebruikers vergrendeling beperken door meerdere verificatie methoden of gelijkwaardige voor elke gebruiker in te richten.

## <a name="before-a-disruption"></a>Vóór een onderbreking

Het beperken van een daad werkelijke onderbreking moet de primaire focus van een organisatie zijn voor het oplossen van problemen met toegangs beheer die zich kunnen voordoen. Het beperken van problemen omvat het plannen van een echt evenement plus het implementeren van strategieën om ervoor te zorgen dat toegangs beheer en-bewerkingen niet worden beïnvloed tijdens onderbrekingen.

### <a name="why-do-you-need-resilient-access-control"></a>Waarom hebt u het toegangs beheer flexibeler?

 Identiteit is het beheer vlak van gebruikers die toegang hebben tot apps en bronnen. Uw identiteits systeem bepaalt welke gebruikers en onder welke voor waarden, zoals toegangs controle of verificatie vereisten, gebruikers toegang krijgen tot de toepassingen. Wanneer een of meer vereisten voor verificatie of toegangs beheer niet beschikbaar zijn voor gebruikers om te verifiëren vanwege onvoorziene omstandigheden, kunnen organisaties een of beide van de volgende problemen ondervinden:

* **Beheerder vergren delen:** Beheerders kunnen de Tenant of services niet beheren.
* **Gebruikers vergrendeling:** Gebruikers hebben geen toegang tot apps of resources.

### <a name="administrator-lockout-contingency"></a>Onvoorziene uitsluiting van beheerder

Als u de beheerders toegang tot uw Tenant wilt ontgrendelen, moet u accounts voor nood toegang maken. Deze accounts voor toegang tot nood gevallen, ook wel account voor *afbreek glazen* genoemd, bieden toegang tot het beheren van de Azure AD-configuratie wanneer het normale privileged account Access procedures niet beschikbaar is. Er moeten ten minste twee accounts voor toegang in nood gevallen worden gemaakt na de aanbevelingen voor het [toegangs account]( ../users-groups-roles/directory-emergency-access.md).

### <a name="mitigating-user-lockout"></a>Vergren deling van gebruikers beperken

 Als u het risico van gebruikers vergrendeling wilt beperken, gebruikt u beleid voor voorwaardelijke toegang met meerdere besturings elementen om gebruikers te voorzien van de keuze van de manier waarop ze toegang krijgen tot apps en bronnen. Door een gebruiker de keuze te geven, bijvoorbeeld om u aan te melden met MFA **of** u aan te melden vanaf een beheerd apparaat **of** u aan te melden bij het bedrijfs netwerk, als een van de toegangs beheer instellingen niet beschikbaar is, heeft de gebruiker andere opties om verder te werken.

#### <a name="microsoft-recommendations"></a>Aanbevelingen van micro soft

De volgende toegangs controles opnemen in uw bestaande beleids regels voor voorwaardelijke toegang voor de organisatie:

1. Richt meerdere verificatie methoden in voor elke gebruiker die afhankelijk is van verschillende communicatie kanalen, bijvoorbeeld de Microsoft Authenticator app (op Internet), OATH-token (gegenereerd op het apparaat) en SMS (telephon). Met het volgende Power shell-script kunt u vooraf bepalen welke aanvullende methoden uw gebruikers moeten registreren: [script voor de analyse van Azure AD MFA-verificatie methode](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).
2. Implementeer Windows hello voor bedrijven op Windows 10-apparaten om rechtstreeks aan de vereisten voor MFA te voldoen bij het aanmelden bij het apparaat.
3. Gebruik vertrouwde apparaten via [Azure AD Hybrid join's](../devices/overview.md) of [Microsoft intune beheerde apparaten](/intune/planning-guide). Vertrouwde apparaten verbeteren de gebruikers ervaring omdat het vertrouwde apparaat zelf kan voldoen aan de sterke verificatie vereisten van het beleid zonder een MFA-uitdaging voor de gebruiker. MFA is vervolgens vereist bij het registreren van een nieuw apparaat en bij het openen van apps of bronnen van niet-vertrouwde apparaten.
4. Op risico gebaseerd beleid van Azure AD Identity Protection gebruiken om toegang te voor komen wanneer de gebruiker of het aanmelden een risico vormt voor een vast MFA-beleid.
5. Als u VPN-toegang met Azure AD MFA NPS-extensie beveiligt, kunt u uw VPN-oplossing federeren als een [SAML-app](../manage-apps/view-applications-portal.md) en de app-categorie bepalen, zoals hieronder wordt aanbevolen. 

>[!NOTE]
> Voor beleids regels op basis van Risico's zijn [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) -licenties vereist.

In het volgende voor beeld wordt het beleid beschreven dat u moet maken om een flexibeler toegangs beheer te bieden voor de gebruiker om toegang te krijgen tot hun apps en bronnen. In dit voor beeld hebt u een beveiligings groep **AppUsers** met de doel gebruikers die u toegang wilt verlenen, een groep met de naam **CoreAdmins** met de hoofd beheerders en een groep met de naam **EmergencyAccess** met de accounts voor toegang voor nood gevallen.
Met dit voor beeld van een beleidset worden geselecteerde gebruikers in **AppUsers**, toegang tot geselecteerde apps verleend als ze verbinding maken vanaf een vertrouwd apparaat of sterke verificatie bieden, bijvoorbeeld MFA. Hiermee worden nood accounts en basis beheerders uitgesloten.

**Beleid voor de risico beperking van de CA is ingesteld:**

* Beleid 1: toegang blok keren voor personen buiten de doel groepen
  * Gebruikers en groepen: alle gebruikers bevatten. AppUsers, CoreAdmins en EmergencyAccess uitsluiten
  * Cloud-apps: alle apps toevoegen
  * Voor waarden: (geen)
  * Besturings element verlenen: blok keren
* Beleid 2: toegang verlenen aan AppUsers die MFA of een vertrouwd apparaat vereisen.
  * Gebruikers en groepen: AppUsers bevatten. CoreAdmins en EmergencyAccess uitsluiten
  * Cloud-apps: alle apps toevoegen
  * Voor waarden: (geen)
  * Beheer toekennen: toegang verlenen, multi-factor Authentication vereisen, vereist dat het apparaat compatibel is. Voor meerdere besturings elementen: een van de geselecteerde besturings elementen vereisen.

### <a name="contingencies-for-user-lockout"></a>Onvoorziene gebeurtenissen voor gebruikers vergrendeling

Het is ook mogelijk dat uw organisatie een nood beleid maakt. Als u nood beleid wilt maken, moet u criteria voor de balans tussen bedrijfs continuïteit, operationele kosten, financiële kosten en beveiligings Risico's definiëren. U kunt bijvoorbeeld een beleid voor nood gevallen alleen activeren voor een subset van gebruikers, voor een subset van apps, voor een subset van clients of van een subset van locaties. Bij een nood geval kunnen beheerders en eind gebruikers toegang krijgen tot apps en bronnen tijdens een onderbreking wanneer er geen beperkings methode is geïmplementeerd. Micro soft raadt aan om nood beleid in de [modus alleen rapport](../conditional-access/howto-conditional-access-insights-reporting.md) in te scha kelen wanneer het niet wordt gebruikt, zodat beheerders de mogelijke gevolgen van het beleid kunnen bewaken. deze moeten worden ingeschakeld.

 Als u uw bloot stelling tijdens een onderbreking begrijpt, vermindert u het risico en is dit een belang rijk onderdeel van uw plannings proces. Bepaal eerst de volgende zakelijke vereisten van uw organisatie om uw rampen plan te maken:

1. Uw essentiële bedrijfs-apps van tevoren bepalen: wat zijn de apps waarvoor u toegang moet verlenen, zelfs met een lager risico/beveiligings postuur? Bouw een lijst van deze apps en zorg ervoor dat uw andere belanghebbenden (zakelijk, veiligheid, juridisch en leiderschap) alle ermee instemmen dat als alle toegangs beheer wegloopt, deze apps nog steeds moeten blijven worden uitgevoerd. U zult waarschijnlijk uiteindelijk beginnen met de volgende categorieën:
   * **Categorie 1 essentiële apps** die meer dan een paar minuten niet beschikbaar zijn, bijvoorbeeld apps die rechtstreeks van invloed zijn op de inkomsten van de organisatie.
   * **Categorie 2 belang rijke apps** die het bedrijf binnen een paar uur toegankelijk moet zijn.
   * **Categorie 3 apps met een lage prioriteit** die tot een onderbreking van een paar dagen kunnen leiden.
2. Voor apps in categorie 1 en 2 raadt micro soft u aan om het type toegangs niveau dat u wilt toestaan vooraf te plannen:
   * Wilt u volledige toegang of beperkte sessie toestaan, zoals het beperken van down loads?
   * Wilt u toegang verlenen tot een deel van de app, maar niet voor de hele app?
   * Wilt u toegang tot Information Workers toestaan en beheerders toegang blok keren totdat het toegangs beheer is hersteld?
3. Voor die apps raadt micro soft u ook aan om te plannen welke toegangs mogelijkheden u opzettelijk kunt openen en welke u wilt sluiten:
   * Wilt u alleen browser toegang toestaan en uitgebreide clients blok keren die offline gegevens kunnen opslaan?
   * Wilt u alleen toegang toestaan voor gebruikers binnen het bedrijfs netwerk en buiten gebruikers geblokkeerd blijven?
   * Wilt u de toegang tot bepaalde landen of regio's alleen toestaan tijdens de onderbreking?
   * Wilt u beleid voor het beleid voor nood gevallen, met name voor essentiële apps, laten mislukken of slagen als er geen alternatief toegangs beheer beschikbaar is?

#### <a name="microsoft-recommendations"></a>Aanbevelingen van micro soft

Een beleid voor voorwaardelijke toegang voor nood gevallen is een **back-upbeleid** voor het weglaten van Azure AD MFA, MFA-, op Risico's of op apparaten gebaseerde besturings elementen. Om onverwachte onderbrekingen te minimaliseren wanneer een nood beleid is ingeschakeld, blijft het beleid in de modus alleen-rapporteren wanneer het niet wordt gebruikt. Beheerders kunnen de mogelijke gevolgen van de nood polissen controleren met behulp van de Insights-werkmap voor voorwaardelijke toegang. Als uw organisatie besluit uw rampen plan te activeren, kunnen beheerders het beleid inschakelen en het normale beleid op basis van beheer uitschakelen.

>[!IMPORTANT]
> Door beleid uit te scha kelen waarmee de beveiliging van uw gebruikers wordt afgedwongen, wordt uw beveiligings postuur beperkt terwijl het nood plan aanwezig is.

* Een set terugval beleid configureren als een onderbreking in één referentie type of een mechanisme voor toegangs beheer invloed heeft op de toegang tot uw apps. Configureer een beleid in de status alleen rapport waarvoor lidmaatschap van een domein als een besturings element is vereist als back-up voor een actief beleid waarvoor een MFA-provider van derden is vereist.
* Verminder het risico dat ongeldige actors wacht woorden raden als MFA niet vereist is door de procedures in het technisch document voor [richt lijnen voor wacht](https://aka.ms/passwordguidance) woorden te volgen.
* Implementeer [Azure ad Self-Service wachtwoord herstel (SSPR)](./tutorial-enable-sspr.md) en [Azure AD-wachtwoord beveiliging](./howto-password-ban-bad-on-premises-deploy.md) om ervoor te zorgen dat gebruikers geen gemeen schappelijk wacht woord en voor waarden gebruiken die u wilt verbieden.
* Gebruik beleids regels voor het beperken van de toegang binnen de apps als een bepaald verificatie niveau niet wordt bereikt in plaats van eenvoudigweg terug te vallen op volledige toegang. Bijvoorbeeld:
  * Configureer een back-upbeleid dat de beperkte sessie claim verzendt naar Exchange en share point.
  * Als uw organisatie gebruikmaakt van Microsoft Cloud App Security, overweeg dan terug te vallen op een beleid dat MCAS en vervolgens MCAS alleen-lezen toegang toestaat, maar niet uploads.
* Geef uw beleid een naam om te controleren of het gemakkelijk is te vinden tijdens een onderbreking. Neem de volgende elementen op in de naam van het beleid:
  * Een *Label nummer* voor het beleid.
  * De tekst die moet worden weer gegeven, is dit beleid alleen voor nood gevallen. Bijvoorbeeld: **inschakelen in nood gevallen**
  * De *onderbreking* is van toepassing op. Bijvoorbeeld: **tijdens een MFA-onderbreking**
  * Een *Volg nummer* voor het weer geven van de volg orde waarin u het beleid moet activeren.
  * De *apps* waarop het van toepassing is.
  * De *besturings elementen* die worden toegepast.
  * De *voor waarden* die nodig zijn.
  
Deze naamgevings standaard voor het beleid voor nood gevallen is als volgt: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

Het volgende voor beeld: **een voor beeld van een CA-beleid om de toegang tot essentiële samenwerkings-apps te herstellen**, is een typische zakelijke nood geval. In dit scenario vereist de organisatie MFA doorgaans voor alle Exchange Online-en share point online-toegang, en de onderbreking in dit geval is de MFA-provider voor de klant heeft een storing (of Azure AD MFA, on-premises MFA-provider of MFA van derden). Dit beleid verkleint deze onderbreking doordat specifieke doel gebruikers alleen toegang hebben tot deze apps vanaf vertrouwde Windows-apparaten wanneer ze toegang hebben tot de app vanuit hun vertrouwde bedrijfs netwerk. Hierbij worden ook nood accounts en essentiële beheerders uitgesloten van deze beperkingen. De doel gebruikers krijgen vervolgens toegang tot Exchange Online en share point online, terwijl andere gebruikers nog steeds geen toegang tot de apps hebben vanwege de storing. Dit voor beeld vereist een benoemde netwerk locatie **CorpNetwork** en een **ContingencyAccess** met de doel gebruikers, een groep met de naam **CoreAdmins** met de kern beheerders en een groep met de naam **EmergencyAccess** met de accounts voor toegang voor nood gevallen. De nood gevallen vereist vier beleids regels om de gewenste toegang te bieden. 

**Voor beeld van een CA-beleid voor nood gevallen om de toegang tot essentiële samenwerkings-apps te herstellen:**

* Beleid 1: apparaten die lid zijn van een domein vereisen voor Exchange en share point
  * Naam: EM001-IN nood gevallen: MFA-onderbreking [1/4]-Exchange share point: hybride Azure AD-deelname vereisen
  * Gebruikers en groepen: ContingencyAccess bevatten. CoreAdmins en EmergencyAccess uitsluiten
  * Cloud-apps: Exchange Online en share point online
  * Voor waarden: any
  * Beheer toekennen: lid zijn van domein vereist
  * Status: alleen rapport
* Beleid 2: andere platforms dan Windows blok keren
  * Naam: EM002-inschakelen IN nood gevallen: MFA-onderbreking [2/4]-Exchange share point-toegang blok keren met uitzonde ring van Windows
  * Gebruikers en groepen: alle gebruikers bevatten. CoreAdmins en EmergencyAccess uitsluiten
  * Cloud-apps: Exchange Online en share point online
  * Voor waarden: platform apparaat omvat alle platforms, sluit Windows
  * Besturings element verlenen: blok keren
  * Status: alleen rapport
* Beleid 3: andere netwerken dan CorpNetwork blok keren
  * Naam: EM003-inschakelen IN nood gevallen: MFA-onderbreking [3/4]-Exchange share point-toegang blok keren met uitzonde ring van bedrijfs netwerk
  * Gebruikers en groepen: alle gebruikers bevatten. CoreAdmins en EmergencyAccess uitsluiten
  * Cloud-apps: Exchange Online en share point online
  * Voor waarden: locaties bevatten een wille keurige locatie, CorpNetwork uitsluiten
  * Besturings element verlenen: blok keren
  * Status: alleen rapport
* Beleid 4: EA'S expliciet blok keren
  * Naam: EM004-inschakelen IN nood gevallen: MFA-onderbreking [4/4]-Exchange-Block EAS voor alle gebruikers
  * Gebruikers en groepen: alle gebruikers toevoegen
  * Cloud-apps: Exchange Online toevoegen
  * Voor waarden: client-apps: Exchange Active Sync
  * Besturings element verlenen: blok keren
  * Status: alleen rapport

Volg orde van activering:

1. Sluit ContingencyAccess, CoreAdmins en EmergencyAccess uit van het bestaande MFA-beleid. Controleren of een gebruiker in ContingencyAccess toegang kan krijgen tot share point online en Exchange Online.
2. Beleid inschakelen 1: controleren of gebruikers op apparaten die lid zijn van een domein die zich niet in de uitsluitings groepen bevinden, toegang hebben tot Exchange Online en share point online. Controleren of gebruikers in de groep uitsluitingen toegang hebben tot share point online en Exchange vanaf elk apparaat.
3. Beleid inschakelen 2: Controleer of gebruikers die zich niet in de uitsluitings groep bevinden, share point online en Exchange Online niet kunnen downloaden vanaf hun mobiele apparaten. Controleren of gebruikers in de groep uitsluitingen toegang hebben tot share point en Exchange vanaf elk apparaat (Windows/iOS/Android).
4. Beleid inschakelen 3: Controleer of gebruikers die geen deel uitmaken van de groepen die geen toegang hebben tot share point en Exchange niet van het bedrijfs netwerk hebben, zelfs niet met een computer die lid is van een domein. Controleren of gebruikers in de groep uitsluitingen toegang hebben tot share point en Exchange vanaf elk netwerk.
5. Beleid inschakelen 4: Controleer of alle gebruikers Exchange Online niet kunnen ophalen van de systeem eigen e-mail toepassingen op mobiele apparaten.
6. Schakel het bestaande MFA-beleid voor share point online en Exchange Online uit.

In dit volgende voor beeld wordt een **voor beeld van een CA-beleid voor onvoorziene toestemming voor mobiele toegang tot Sales Force**, een zakelijke app-toegang, hersteld. In dit scenario vereist de klant doorgaans dat de verkoop medewerkers toegang hebben tot Sales Force (geconfigureerd voor eenmalige aanmelding met Azure AD) vanaf mobiele apparaten, zodat ze alleen kunnen worden toegestaan van compatibele apparaten. De onderbreking in dit geval is dat er een probleem is met de evaluatie van de naleving van het apparaat en dat de storing plaatsvindt op een gevoelige tijd wanneer het verkoop team toegang moet hebben tot Sales Force om deals te sluiten. Met deze beleids regels voor nood gevallen kunnen essentiële gebruikers toegang krijgen tot Sales Force vanaf een mobiel apparaat, zodat ze de deals blijven sluiten en het bedrijf niet kan verstoren. In dit voor beeld bevat **SalesforceContingency** alle verkoop medewerkers die de toegang moeten behouden en **SalesAdmins** de benodigde beheerders van Sales Force bevatten.

**Voor beeld B: beleid voor onvoorziene CA**

* Beleid 1: iedereen niet in het SalesContingency-team blok keren
  * Naam: EM001-inschakelen IN nood gevallen: apparaatcompatibiliteit-onderbreking [1/2]-Sales Force-alle gebruikers blok keren, behalve SalesforceContingency
  * Gebruikers en groepen: alle gebruikers bevatten. SalesAdmins en SalesforceContingency uitsluiten
  * Cloud-apps: Sales Force.
  * Voorwaarden: geen
  * Besturings element verlenen: blok keren
  * Status: alleen rapport
* Beleid 2: het verkoop team blok keren vanaf een ander platform dan mobiel (om surface area van een aanval te verminderen)
  * Naam: EM002-inschakelen IN nood gevallen: apparaatcompatibiliteit-onderbreking [2/2]-Sales Force-alle platformen blok keren behalve iOS en Android
  * Gebruikers en groepen: SalesforceContingency bevatten. SalesAdmins uitsluiten
  * Cloud-apps: Sales Force
  * Voor waarden: platform apparaat omvat alle platforms, sluit iOS en Android uit
  * Besturings element verlenen: blok keren
  * Status: alleen rapport

Volg orde van activering:

1. Sluit SalesAdmins en SalesforceContingency uit van het bestaande apparaatcompatibiliteit voor Sales Force. Controleren of een gebruiker in de SalesforceContingency-groep toegang heeft tot Sales Force.
2. Beleid inschakelen 1: gebruikers buiten SalesContingency verifiëren kan geen toegang krijgen tot Sales Force. Controleer of gebruikers in SalesAdmins en SalesforceContingency toegang hebben tot Sales Force.
3. Beleid inschakelen 2: controleren of gebruikers in de groep SalesContingency geen toegang hebben tot Sales Force vanaf hun Windows/Mac-laptops, maar nog steeds toegang hebben tot hun mobiele apparaten. Controleren of SalesAdmin vanaf elk apparaat nog steeds toegang tot Sales Force heeft.
4. Het bestaande nalevings beleid voor apparaten uitschakelen voor Sales Force.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>Onvoorziene gebeurtenissen voor het vergren delen van gebruikers uit on-premises resources (NPS-extensie)

Als u VPN-toegang met Azure AD MFA NPS-extensie beveiligt, kunt u uw VPN-oplossing federeren als een [SAML-app](../manage-apps/view-applications-portal.md) en de app-categorie bepalen, zoals hieronder wordt aanbevolen. 

Als u Azure AD MFA NPS-extensie hebt geïmplementeerd voor het beveiligen van on-premises resources, zoals VPN en Extern bureaublad-gateway, met MFA, moet u vooraf rekening houden als u in een nood geval MFA kunt uitschakelen.

In dit geval kunt u de NPS-uitbrei ding uitschakelen, omdat de NPS-server alleen primaire authenticatie verifieert en niet MFA afdwingt voor de gebruikers.

NPS-uitbrei ding uitschakelen: 
-   Exporteer de HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters register sleutel als back-up. 
-   Verwijder de register waarden voor "AuthorizationDLLs" en "ExtensionDLLs", niet de sleutel para meters. 
-   Start de service Network Policy service opnieuw op om de wijzigingen van kracht te laten worden 
-   Bepaal of de primaire verificatie voor VPN is geslaagd.

Zodra de service is hersteld en u klaar bent om MFA af te dwingen voor uw gebruikers, schakelt u de NPS-extensie in: 
-   De register sleutel importeren uit een back-up HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters 
-   Start de service Network Policy service opnieuw op om de wijzigingen van kracht te laten worden 
-   Bepaal of primaire authenticatie en secundaire verificatie voor VPN geslaagd zijn.
-   Controleer de NPS-server en het VPN-logboek om te bepalen welke gebruikers zich hebben aangemeld tijdens het nood venster.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Wachtwoord hash-synchronisatie implementeren, zelfs als u federatieve bent of Pass-Through-verificatie gebruikt

Gebruikers kunnen zich ook vergren delen als aan de volgende voor waarden wordt voldaan:

- Uw organisatie gebruikt een hybride identiteits oplossing met Pass Through-verificatie of Federatie.
- Uw on-premises identiteits systemen (zoals Active Directory, AD FS of een afhankelijk onderdeel) zijn niet beschikbaar. 
 
Voor meer flexibeler moet uw organisatie de [wachtwoord hash-synchronisatie inschakelen](../hybrid/choose-ad-authn.md), omdat u kunt [overschakelen naar het gebruik van wachtwoord-hash-synchronisatie](../hybrid/plan-connect-user-signin.md) als uw on-premises identiteits systemen niet actief zijn.

#### <a name="microsoft-recommendations"></a>Aanbevelingen van micro soft
 Schakel wacht woord-hash-synchronisatie in met behulp van de Azure AD Connect wizard, ongeacht of uw organisatie gebruikmaakt van Federatie-of Pass-Through-verificatie.

>[!IMPORTANT]
> Het is niet vereist om gebruikers van federatieve naar beheerde verificatie te converteren voor het gebruik van wacht woord-hash-synchronisatie.

## <a name="during-a-disruption"></a>Tijdens een onderbreking

Als u hebt gekozen voor het implementeren van een beperkings plan, kunt u automatisch een storing in één toegangs beheer laten staan. Als u echter hebt gekozen voor het maken van een rampen plan, kunt u de beleids regels voor nood gevallen activeren tijdens de onderbreking van het toegangs beheer:

1. Schakel uw beleids regels voor nood gevallen in waarmee de beoogde gebruikers toegang krijgen tot specifieke apps, vanuit specifieke netwerken.
2. Schakel uw regel matig op beheer gebaseerde beleids regels uit.

### <a name="microsoft-recommendations"></a>Aanbevelingen van micro soft

Afhankelijk van welke oplossingen of onvoorziene gebeurtenissen worden gebruikt tijdens een onderbreking, kan uw organisatie toegang verlenen met alleen wacht woorden. Geen beveiliging is een aanzienlijk beveiligings risico dat zorgvuldig moet worden gewogen. Organisaties moeten:

1. Als onderdeel van uw strategie voor het wijzigen van het besturings element, documenteren elke wijziging en de status van het bedrijf om eventuele onvoorziene gebeurtenissen terug te draaien zodra de toegangs beheer functies volledig operationeel zijn.
2. Stel dat kwaad aardige actors proberen wacht woorden te oogsten door middel van wachtwoord spray of phishing-aanvallen, terwijl u MFA uitschakelt. Het is ook mogelijk dat ongeldige actors al wacht woorden hebben die voorheen geen toegang verlenen tot resources die tijdens dit venster kunnen worden geprobeerd. Voor kritieke gebruikers, zoals leidinggevenden, kunt u dit risico gedeeltelijk beperken door hun wacht woorden opnieuw in te stellen voordat u MFA uitschakelt.
3. Archiveer alle aanmeldings activiteiten om te bepalen wie toegang heeft tot wat er tijdens de tijd MFA is uitgeschakeld.
4. [Sorteren alle risico detecties gerapporteerd](../reports-monitoring/concept-sign-ins.md) tijdens dit venster.

## <a name="after-a-disruption"></a>Na een onderbreking

De wijzigingen die u hebt gemaakt als onderdeel van het geactiveerde rampen plan ongedaan maken nadat de service is hersteld die de onderbreking heeft veroorzaakt. 

1. Het reguliere beleid inschakelen
2. Schakel de beleids regels voor nood gevallen weer in op de modus alleen rapport. 
3. Terugdraaiende wijzigingen die u hebt aangebracht en die tijdens de onderbreking zijn gedocumenteerd.
4. Als u een account voor toegang tot nood gevallen hebt gebruikt, moet u de referenties opnieuw genereren en de nieuwe gegevens van de referenties fysiek beveiligen als onderdeel van de procedures voor de nood toegangs account.
5. Ga door met het [sorteren van alle risico detecties](../reports-monitoring/concept-sign-ins.md) die zijn gerapporteerd na de onderbreking van verdachte activiteiten.
6. Alle vernieuwings tokens die zijn uitgegeven [met Power shell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) intrekken voor een aantal gebruikers. Het intrekken van alle vernieuwings tokens is belang rijk voor geprivilegieerde accounts die worden gebruikt tijdens de onderbreking en het uitvoeren ervan zal afdwingen dat ze opnieuw worden geverifieerd en voldoen aan het beheer van het herstelde beleid.

## <a name="emergency-options"></a>Opties voor nood gevallen

 In het geval van een nood situatie en uw organisatie heeft nog niet eerder een beperking of nood plan geïmplementeerd, volg dan de aanbevelingen in de sectie voorwaardelijke toegang [voor gebruikers vergrendeling](#contingencies-for-user-lockout) als deze al gebruikmaken van beleids regels voor voorwaardelijk toegangs beheer om MFA af te dwingen.
Als uw organisatie verouderd beleid voor MFA per gebruiker gebruikt, kunt u het volgende alternatief overwegen:

1. Als u het uitgaande IP-adres van het bedrijfs netwerk hebt, kunt u ze als betrouw bare Ip's toevoegen om verificatie alleen voor het bedrijfs netwerk mogelijk te maken.
   1. Als u niet beschikt over de inventaris van uitgaande IP-adressen of als u toegang wilt inschakelen binnen en buiten het bedrijfs netwerk, kunt u de gehele IPv4-adres ruimte als vertrouwde Ip's toevoegen door 0.0.0.0/1 en 128.0.0.0/1 op te geven.

>[!IMPORTANT]
 > Als u de vertrouwde IP-adressen uitbreidt om de toegang te blok keren, worden risico detecties die zijn gekoppeld aan IP-adressen (bijvoorbeeld onmogelijk reizen of niet-vertrouwde locaties) niet gegenereerd.

>[!NOTE]
 > Het configureren van [vertrouwde IP-adressen](./howto-mfa-mfasettings.md) voor Azure AD MFA is alleen beschikbaar met [Azure AD Premium-licenties](./concept-mfa-licensing.md).

## <a name="learn-more"></a>Lees meer

* [Documentatie voor Azure AD-verificatie](./howto-mfaserver-iis.md)
* [Beheer accounts voor toegang in nood gevallen beheren in azure AD](../roles/security-emergency-access.md)
* [Benoemde locaties configureren in Azure Active Directory](../reports-monitoring/quickstart-configure-named-locations.md)
  * [Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Hybride Azure Active Directory-apparaten configureren](../devices/hybrid-azuread-join-plan.md)
* [Implementatiehandleiding Windows Hello voor Bedrijven](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Richt lijnen voor wacht woorden-micro soft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Wat zijn voor waarden in Azure Active Directory voorwaardelijke toegang?](../conditional-access/concept-conditional-access-conditions.md)
* [Wat zijn toegangs beheer in Azure Active Directory voorwaardelijke toegang?](../conditional-access/controls.md)
* [Wat is de modus alleen rapport-alleen voor voorwaardelijke toegang?](../conditional-access/concept-conditional-access-report-only.md)