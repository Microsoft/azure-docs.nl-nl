---
title: Overwegingen voor de implementatie van Azure AD Multi-Factor Authentication
description: Meer informatie over overwegingen met betrekking tot de implementatie en strategie voor een succes volle implementatie van Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ee81abd7cd0268a7cbd6b16aa6065ec7b54bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861303"
---
# <a name="plan-an-azure-ad-multi-factor-authentication-deployment"></a>Een Azure AD Multi-Factor Authentication-implementatie plannen

Mensen maken verbinding met de resources van de organisatie in steeds complexe scenario's. Mensen maken verbinding met de bedrijfs eigen, persoonlijke en open bare apparaten in en uit het zakelijke netwerk met behulp van Smart telefoons, tablets, Pc's en laptops, vaak op meerdere platforms. In deze altijd verbonden multi-device-en multi-platform wereld is de beveiliging van gebruikers accounts belang rijker dan ooit. Wacht woorden, ongeacht hun complexiteit, die worden gebruikt voor apparaten, netwerken en platformen, zijn niet langer voldoende om de beveiliging van het gebruikers account te garanderen, met name wanneer gebruikers wacht woorden van accounts vaak opnieuw moeten gebruiken. Geavanceerde phishing en andere aanvallen met sociale techniek kunnen ertoe leiden dat gebruikers namen en wacht woorden worden gepost en verkocht op het donkere web.

[Azure AD multi-factor Authentication (MFA)](concept-mfa-howitworks.md) helpt de toegang tot gegevens en toepassingen te beveiligen. Het biedt een extra beveiligingslaag met behulp van een tweede vorm van verificatie. Organisaties kunnen [voorwaardelijke toegang](../conditional-access/overview.md) gebruiken om de oplossing te laten voldoen aan hun specifieke behoeften.

In deze implementatie handleiding wordt uitgelegd hoe u een Azure AD-Multi-Factor Authentication implementeert en test.

Als u snel de Azure AD-Multi-Factor Authentication in actie wilt zien en vervolgens meer wilt weten over aanvullende overwegingen voor de implementatie:

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication inschakelen](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Vereisten

Voordat u een implementatie van Azure AD Multi-Factor Authentication start, zijn er vereiste items die moeten worden overwogen.

| Scenario | Vereiste |
| --- | --- |
| **Cloud-only-** identiteits omgeving met moderne verificatie | **Geen aanvullende vereiste taken** |
| Scenario's voor **hybride** identiteit | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) wordt geïmplementeerd en gebruikers identiteiten worden gesynchroniseerd of Federated met de on-premises Active Directory Domain Services met Azure Active Directory. |
| On-premises oudere toepassingen die zijn gepubliceerd voor Cloud toegang | Azure AD- [toepassings proxy](../manage-apps/application-proxy.md) wordt geïmplementeerd. |
| Azure AD MFA gebruiken met RADIUS-verificatie | Er wordt een [Network Policy Server (NPS)](howto-mfa-nps-extension.md) geïmplementeerd. |
| Gebruikers hebben Microsoft Office 2010 of eerder of Apple Mail voor iOS 11 of eerder | Voer een upgrade uit naar [Microsoft Office 2013 of hoger](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) en Apple Mail voor IOS 12 of hoger. Voorwaardelijke toegang wordt niet ondersteund door verouderde verificatie protocollen. |

## <a name="plan-user-rollout"></a>Gebruikers implementatie plannen

Uw abonnement op de MFA-implementatie moet een pilot implementatie bevatten, gevolgd door implementatie golven die binnen uw ondersteunings capaciteit vallen. Begin met uw implementatie door uw beleid voor voorwaardelijke toegang toe te passen op een kleine groep test gebruikers. Nadat u het effect van de test gebruikers, het gebruikte proces en registratie gedrag hebt geëvalueerd, kunt u meer groepen toevoegen aan het beleid of meer gebruikers toevoegen aan de bestaande groepen.

### <a name="user-communications"></a>Gebruikers communicatie

Het is essentieel om gebruikers te informeren over geplande communicatie, over aanstaande wijzigingen, Azure AD MFA-registratie vereisten en alle benodigde gebruikers acties. U wordt aangeraden communicaties uit te voeren in overleg met vertegenwoordigers vanuit uw organisatie, zoals een communicatie, wijzigings beheer of Human Resources-afdelingen.

Micro soft biedt [communicatie sjablonen](https://aka.ms/mfatemplates) en [documentatie voor eind gebruikers](../user-help/security-info-setup-signin.md) om uw communicatie te ontwerpen. U kunt gebruikers verzenden naar [https://myprofile.microsoft.com](https://myprofile.microsoft.com) om rechtstreeks te registreren door de koppelingen voor **beveiligings gegevens** op die pagina te selecteren.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

Azure AD Multi-Factor Authentication wordt geïmplementeerd door beleids regels met voorwaardelijke toegang af te dwingen. Een beleid voor voorwaardelijke toegang kan vereisen dat gebruikers multi-factor Authentication uitvoeren wanneer aan bepaalde criteria wordt voldaan, zoals:

* Alle gebruikers, een specifieke gebruiker, lid van een groep of toegewezen rol
* Specifieke Cloud toepassing waartoe toegang wordt verkregen
* Apparaatplatform
* Status van apparaat
* Netwerk locatie of geografisch geplaatste IP-adres
* Clienttoepassingen
* Aanmeldings risico (vereist identiteits beveiliging)
* Compatibel apparaat
* Hybride Azure AD-apparaat toegevoegd
* Goedgekeurde client toepassing

Gebruik de aanpas bare posters en e-mail sjablonen in [multi-factor Authentication-implementatie materialen](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) voor het implementeren van multi-factor Authentication voor uw organisatie.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Multi-Factor Authentication met voorwaardelijke toegang inschakelen

Met beleid voor voorwaardelijke toegang wordt de registratie afgedwongen, waarbij niet-geregistreerde gebruikers zijn vereist voor het volt ooien van de registratie bij de eerste aanmelding, een belang rijk veiligheids aspect.

[Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) draagt zowel een registratie beleid voor als het beleid voor automatische risico detectie en herstel op het multi-factor Authentication verhaal van Azure AD. Beleids regels kunnen worden gemaakt om wachtwoord wijzigingen af te dwingen wanneer er sprake is van een bedreiging van de identiteit aangetast of MFA vereist wanneer een aanmelding als riskant wordt beschouwd door de volgende [gebeurtenissen](../identity-protection/overview-identity-protection.md):

* Gelekte referenties
* Aanmeldingen vanaf anonieme IP-adressen
* Onmogelijke reis naar ongewone locaties
* Aanmeldingen vanaf onbekende locaties
* Aanmeldingen vanaf geïnfecteerde apparaten
* Aanmeldingen vanaf IP-adressen met verdachte activiteiten

Enkele van de door Azure Active Directory Identity Protection gedetecteerde risico detecties treden in realtime op en sommige vereisen offline verwerking. Beheerders kunnen kiezen om gebruikers te blok keren die Risk ante gedrag tonen en hand matig herstellen, een wachtwoord wijziging vereisen of een multi-factor Authentication vereisen als onderdeel van het beleid voor voorwaardelijke toegang.

## <a name="define-network-locations"></a>Netwerk locaties definiëren

Het is raadzaam dat organisaties voorwaardelijke toegang gebruiken om hun netwerk te definiëren met behulp van [benoemde locaties](../conditional-access/location-condition.md#named-locations). Als uw organisatie identiteits beveiliging gebruikt, overweeg dan het gebruik van beleids regels op basis van Risico's in plaats van benoemde locaties.

### <a name="configuring-a-named-location"></a>Een benoemde locatie configureren

1. Open **Azure Active Directory** in het Azure Portal
2. **Beveiliging** selecteren
3. Kies onder **beheren** de optie **benoemde locaties**
4. **Nieuwe locatie** selecteren
5. Geef in het veld **naam** een zinvolle naam op
6. Selecteren of u de locatie definieert met *IP-bereiken* of *landen/regio's*
   1. Als *IP-bereiken* worden gebruikt
      1. Bepaal of u *als vertrouwde locatie wilt markeren*. Aanmelden van een vertrouwde benoemde locaties verlaagt het aanmeldingsrisico van een gebruiker. Markeer deze locatie alleen als vertrouwd als u weet dat de ingevoerde IP-bereiken tot stand zijn gebracht en geloofwaardig is in uw organisatie.
      2. Geef de IP-bereiken op
   2. Als u *landen/regio's* gebruikt
      1. Vouw de vervolg keuzelijst uit en selecteer de landen of regio's die u voor deze benoemde locatie wilt definiëren.
      2. Bepaal of u *onbekende gebieden wilt toevoegen*. Onbekende gebieden zijn IP-adressen die niet kunnen worden toegewezen aan een land/regio.
7. Selecteer **Maken**

## <a name="plan-authentication-methods"></a>Verificatie methoden plannen

Beheerders kunnen de [verificatie methoden](../authentication/concept-authentication-methods.md) kiezen die ze beschikbaar willen maken voor gebruikers. Het is belang rijk om meer dan één verificatie methode toe te staan, zodat gebruikers een back-upmethode beschikbaar hebben als de primaire methode niet beschikbaar is. De volgende methoden zijn beschikbaar voor beheerders om in te scha kelen:

> [!TIP]
> Micro soft raadt u aan om de Microsoft Authenticator (mobiele app) als primaire methode voor Azure AD Multi-Factor Authentication te gebruiken voor een veiligere en verbeterde gebruikers ervaring. De Microsoft Authenticator-app [voldoet ook aan](https://azure.microsoft.com/resources/microsoft-nist/) het National Institute of Standards and Technology Authenticator Assurance levels. 

### <a name="notification-through-mobile-app"></a>Melding via mobiele app

Er wordt een push melding verzonden naar de Microsoft Authenticator-app op uw mobiele apparaat. De gebruiker bekijkt de melding en selecteert **goed keuren** om de verificatie te volt ooien. Push meldingen via een mobiele app bieden de minst opvallendste optie voor gebruikers. Ze zijn ook de meest betrouw bare en veilige optie omdat ze een gegevens verbinding gebruiken in plaats van telefoon.

> [!NOTE]
> Als uw organisatie mede werkers heeft die in China werken of onderweg zijn, werkt de **melding via de mobiele app** -methode op **Android-apparaten** niet in dat land/deze regio. Alternatieve methoden moeten beschikbaar worden gemaakt voor deze gebruikers.

### <a name="verification-code-from-mobile-app"></a>Verificatie code uit de mobiele app

Een mobiele app, zoals de app Microsoft Authenticator, genereert elke 30 seconden een nieuwe OATH-verificatie code. De gebruiker voert de verificatie code in de aanmeldings interface in. De optie voor de mobiele app kan worden gebruikt, ongeacht of de telefoon een gegevens-of mobiel signaal heeft.

### <a name="call-to-phone"></a>Bellen naar telefoon

Er wordt een automatische spraak oproep naar de gebruiker geplaatst. De gebruiker beantwoordt het gesprek en drukt **#** op het toetsen blok van de telefoon om hun verificatie goed te keuren. Bellen naar telefoon is een fantastische back-upmethode voor meldings-of verificatie code van een mobiele app.

### <a name="text-message-to-phone"></a>SMS-bericht naar telefoon

Er wordt een tekst bericht met een verificatie code naar de gebruiker verzonden. de gebruiker wordt gevraagd de verificatie code in te voeren in de aanmeldings interface.

### <a name="choose-verification-options"></a>Verificatie opties kiezen

1. Blader naar **Azure Active Directory**, **gebruikers** **multi-factor Authentication**.

   ![Toegang tot de Blade Multi-Factor Authentication portal van Azure AD-gebruikers in Azure Portal](media/howto-mfa-getstarted/users-mfa.png)

1. Op het tabblad Nieuw kunt u bladeren naar **Service-instellingen** openen.
1. Schakel onder **verificatie opties** alle vakjes in voor de beschik bare methoden voor gebruikers.

   ![Verificatie methoden configureren op het tabblad Multi-Factor Authentication-Service-instellingen](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klik op **Opslaan**.
1. Sluit het tabblad **Service-instellingen** .

## <a name="plan-registration-policy"></a>Registratie beleid plannen

Beheerders moeten bepalen hoe gebruikers hun methoden registreren. Organisaties moeten [de nieuwe gecombineerde registratie-ervaring](howto-registration-mfa-sspr-combined.md) voor Azure AD MFA en self-service voor wachtwoord herstel (SSPR) inschakelen. Met SSPR kunnen gebruikers hun wacht woord op een veilige manier opnieuw instellen met behulp van dezelfde methoden die ze gebruiken voor multi-factor Authentication. We raden u aan deze gecombineerde registratie uit te voeren omdat het een goede ervaring voor gebruikers is, met de mogelijkheid om één keer voor beide services te registreren. Als u dezelfde methoden voor SSPR en Azure AD MFA inschakelt, kunnen uw gebruikers worden geregistreerd om beide functies te gebruiken.

### <a name="registration-with-identity-protection"></a>Registratie met identiteits beveiliging

Als uw organisatie Azure Active Directory Identity Protection gebruikt, [configureert u het MFA-registratie beleid](../identity-protection/howto-identity-protection-configure-mfa-policy.md) zodat uw gebruikers wordt gevraagd de volgende keer dat ze zich interactief aanmelden in te schrijven.

### <a name="registration-without-identity-protection"></a>Registratie zonder identiteits beveiliging

Als uw organisatie geen licenties heeft die identiteits beveiliging inschakelen, wordt gebruikers gevraagd de volgende keer dat MFA is vereist bij het aanmelden te registreren. Gebruikers zijn mogelijk niet geregistreerd voor MFA als ze geen toepassingen gebruiken die zijn beveiligd met MFA. Het is belang rijk om alle gebruikers te laten registreren, zodat de beschadigde actors het wacht woord van een gebruiker niet kunnen raden en zich namens hen voor MFA registreren, waardoor het account effectief wordt beheerd.

#### <a name="enforcing-registration"></a>Registratie afdwingen

Met behulp van de volgende stappen kan een beleid voor voorwaardelijke toegang ertoe afdwingen dat gebruikers zich registreren voor Multi-Factor Authentication

1. Een groep maken, alle gebruikers toevoegen die momenteel niet zijn geregistreerd.
2. Gebruik voorwaardelijke toegang voor het afdwingen van multi-factor Authentication voor deze groep voor toegang tot alle resources.
3. Evalueer het groepslid maatschap regel matig en verwijder gebruikers die zijn geregistreerd bij de groep.

U kunt geregistreerde en niet-geregistreerde Azure AD MFA-gebruikers identificeren met Power shell-opdrachten die afhankelijk zijn van de [MSOnline Power shell-module](/powershell/azure/active-directory/install-msonlinev1).

#### <a name="identify-registered-users"></a>Geregistreerde gebruikers identificeren

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Niet-geregistreerde gebruikers identificeren

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers converteren van MFA per gebruiker naar MFA op basis van voorwaardelijke toegang

Als uw gebruikers zijn ingeschakeld en Azure AD-Multi-Factor Authentication afgedwongen, kunt u de volgende Power shell gebruiken om de conversie naar voorwaardelijke toegang op basis van Azure AD Multi-Factor Authentication te maken.

Voer deze Power shell uit in een ISE-venster of sla het `.PS1` bestand op om lokaal uit te voeren.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Het gedrag en het Power shell-script hierboven zijn onlangs gewijzigd. Voorheen werd het script bespaard op de MFA-methoden, uitgeschakeld MFA en de methoden hersteld. Dit is nu niet langer nodig om de methoden niet te wissen met het standaard gedrag voor uitschakelen.

## <a name="plan-conditional-access-policies"></a>Beleid voor voorwaardelijke toegang plannen

Voor het plannen van de strategie voor het beleid voor voorwaardelijke toegang, die bepaalt wanneer MFA en andere besturings elementen vereist zijn, raadpleegt u [Common Access policies (algemene beleids regels voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-policy-common.md)).

Het is belang rijk dat u niet per ongeluk uw Azure AD-Tenant kunt vergren delen. U kunt de impact van dit onbedoelde gebrek aan administratieve toegang verminderen door [twee of meer accounts voor toegang tot een nood geval in uw Tenant te maken](../roles/security-emergency-access.md) en ze uit te sluiten van uw beleid voor voorwaardelijke toegang.

### <a name="create-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een globale-beheerdersaccount.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
   ![Een beleid voor voorwaardelijke toegang maken om MFA in te scha kelen voor Azure Portal gebruikers in de test groep](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Geef een beschrijvende naam op voor uw beleid.
1. Onder **gebruikers en groepen**:
   * Schakel op het tabblad **include** het keuze rondje **alle gebruikers** in
   * Schakel op het tabblad **exclude** het selectie vakje voor **gebruikers en groepen** in en kies uw accounts voor toegang voor nood gevallen.
   * Klik op **Gereed**.
1. Onder **Cloud-apps** selecteert u het keuze rondje **alle Cloud-apps** .
   * Optioneel: Kies op het tabblad **uitsluiten** de optie Cloud-apps waarvoor uw organisatie geen MFA vereist.
   * Klik op **Gereed**.
1. Onder **voor waarden** :
   * Optioneel: als Azure Identity Protection is ingeschakeld, kunt u ervoor kiezen om het aanmeldings risico als onderdeel van het beleid te evalueren.
   * Optioneel: als u vertrouwde locaties of benoemde locaties hebt geconfigureerd, kunt u opgeven dat deze locaties moeten worden opgenomen of uitgesloten van het beleid.
1. Zorg ervoor dat onder **Grant** het keuze rondje **toegang verlenen** is geselecteerd.
    * Schakel het selectie vakje voor **multi-factor Authentication vereisen** in.
    * Klik op **Selecteren**.
1. Sla de **sessie** sectie over.
1. Stel de wisselknop **Beleid inschakelen** in op **Aan**.
1. Klik op **Create**.

## <a name="plan-integration-with-on-premises-systems"></a>Integratie met on-premises systemen plannen

Voor sommige verouderde en on-premises toepassingen die niet rechtstreeks voor Azure AD worden geverifieerd, zijn extra stappen vereist voor het gebruik van MFA, waaronder:

* Oudere on-premises toepassingen, die toepassings proxy moeten gebruiken.
* On-premises RADIUS-toepassingen, die MFA-adapter moeten gebruiken met NPS-server.
* On-premises AD FS toepassingen, die MFA-adapters moeten gebruiken met AD FS 2016 of nieuwer.

Toepassingen die rechtstreeks worden geverifieerd met Azure AD en moderne authenticatie hebben (WS-voeder, SAML, OAuth, OpenID Connect Connect) kunnen rechtstreeks gebruikmaken van beleids regels voor voorwaardelijke toegang.

### <a name="use-azure-ad-mfa-with-azure-ad-application-proxy"></a>Azure AD MFA gebruiken met Azure AD-toepassingsproxy

Toepassingen die on-premises zijn opgeslagen, kunnen worden gepubliceerd naar uw Azure AD-Tenant via [azure AD-toepassingsproxy](../manage-apps/application-proxy.md) en kunnen profiteren van Azure ad-multi-factor Authentication als ze zijn geconfigureerd voor het gebruik van pre-authenticatie van Azure AD.

Deze toepassingen zijn onderworpen aan beleid voor voorwaardelijke toegang dat Azure AD-Multi-Factor Authentication afdwingen, net zoals elke andere met Azure AD geïntegreerde toepassing.

Als Azure AD Multi-Factor Authentication wordt afgedwongen voor alle gebruikers aanmeldingen, worden on-premises toepassingen die zijn gepubliceerd met Azure AD-toepassingsproxy beschermd.

### <a name="integrating-azure-ad-multi-factor-authentication-with-network-policy-server"></a>Azure AD-Multi-Factor Authentication integreren met Network Policy Server

Met de uitbrei ding van de Network Policy Server (NPS) voor Azure AD MFA voegt u op de cloud gebaseerde MFA-mogelijkheden toe aan uw verificatie-infra structuur met behulp van uw bestaande servers. Met de NPS-extensie kunt u een telefoon gesprek, SMS-bericht of de verificatie van de mobiele app toevoegen aan uw bestaande verificatie stroom. Voor deze integratie gelden de volgende beperkingen:

* Met het CHAPv2-protocol worden alleen push meldingen en spraak oproepen van de verificator-app ondersteund.
* Beleid voor voorwaardelijke toegang kan niet worden toegepast.

De NPS-uitbrei ding fungeert als een adapter tussen RADIUS-en Cloud-Azure AD MFA om een tweede factor van verificatie te bieden voor het beveiligen van [VPN](howto-mfa-nps-extension-vpn.md), [extern bureaublad-gateway verbindingen](howto-mfa-nps-extension-rdg.md)of andere toepassingen die geschikt zijn voor RADIUS. Gebruikers die zich registreren voor Azure AD MFA in deze omgeving worden gevraagd voor alle verificatie pogingen, het ontbreken van beleid voor voorwaardelijke toegang betekent dat MFA altijd vereist is.

#### <a name="implementing-your-nps-server"></a>De NPS-server implementeren

Als er al een NPS-exemplaar is geïmplementeerd en in gebruik is, kunt [u uw bestaande NPS-infra structuur integreren met Azure AD multi-factor Authentication](howto-mfa-nps-extension.md). Als u NPS voor de eerste keer instelt, raadpleegt u [Network Policy Server (NPS)](/windows-server/networking/technologies/nps/nps-top) voor instructies. Richt lijnen voor probleem oplossing vindt u in het artikel de [fout berichten van de NPS-extensie voor Azure AD multi-factor Authentication oplossen](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>NPS voorbereiden voor gebruikers die niet zijn Inge schreven voor MFA

Kies wat er gebeurt wanneer gebruikers die niet zijn Inge schreven met MFA proberen te verifiëren. Gebruik de register instelling `REQUIRE_USER_MATCH` in het registerpad `HKLM\Software\Microsoft\AzureMFA` om het functie gedrag te bepalen. Deze instelling heeft één configuratie optie.

| Sleutel | Waarde | Standaard |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | WAAR/ONWAAR | Niet ingesteld (gelijk aan TRUE) |

Het doel van deze instelling is om te bepalen wat er moet gebeuren wanneer een gebruiker niet is inge schreven voor MFA. De gevolgen van het wijzigen van deze instelling worden weer gegeven in de volgende tabel.

| Instellingen | Gebruikers MFA-status | Bijwerkingen |
| --- | --- | --- |
| Sleutel bestaat niet | Niet Inge schreven | De MFA-vraag is mislukt |
| Waarde ingesteld op waar/niet ingesteld | Niet Inge schreven | De MFA-vraag is mislukt |
| Sleutel ingesteld op ONWAAR | Niet Inge schreven | Verificatie zonder MFA |
| Sleutel ingesteld op False of True | Geregistreerd | Moet worden geverifieerd met MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integreren met Active Directory Federation Services

Als uw organisatie federatief is met Azure AD, kunt u [Azure AD-multi-factor Authentication gebruiken om AD FS resources](multi-factor-authentication-get-started-adfs.md), zowel on-premises als in de Cloud, te beveiligen. Met Azure AD MFA kunt u wacht woorden verminderen en een veiligere verificatie methode bieden. Vanaf Windows Server 2016 kunt u nu Azure AD MFA configureren voor primaire authenticatie.

In tegens telling tot AD FS in Windows Server 2012 R2, integreert de AD FS 2016 Azure AD MFA-adapter rechtstreeks met Azure AD en is er geen on-premises Azure MFA-server vereist. De Azure AD MFA-adapter is ingebouwd in Windows Server 2016 en er is geen aanvullende installatie nodig.

Wanneer u Azure AD MFA gebruikt met AD FS 2016 en de doel toepassing is onderworpen aan beleid voor voorwaardelijke toegang, zijn er aanvullende overwegingen:

* Voorwaardelijke toegang is beschikbaar wanneer de toepassing een Relying Party is naar Azure AD, Federated met AD FS 2016 of nieuwer.
* Voorwaardelijke toegang is niet beschikbaar wanneer de toepassing een Relying Party is AD FS 2016 of AD FS 2019 en wordt beheerd of Federated met AD FS 2016 of AD FS 2019.
* Voorwaardelijke toegang is ook niet beschikbaar wanneer AD FS 2016 of AD FS 2019 is geconfigureerd voor het gebruik van Azure AD MFA als primaire verificatie methode.

#### <a name="ad-fs-logging"></a>AD FS logboek registratie

Standaard AD FS 2016 en 2019 logboek registratie in het Windows-beveiligings logboek en het AD FS-beheer logboek bevat informatie over verificatie aanvragen en hun geslaagde of mislukte pogingen. Met gebeurtenis logboek gegevens in deze gebeurtenissen wordt aangegeven of Azure AD MFA is gebruikt. Een AD FS controle gebeurtenis-ID 1200 kan bijvoorbeeld het volgende bevatten:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Certificaten vernieuwen en beheren

Op elke AD FS-server, in de lokale computer mijn Store, is er een zelfondertekend Azure AD MFA-certificaat met de titel OU = micro soft AD FS Azure MFA, dat de verval datum van het certificaat bevat. Controleer de geldigheids periode van dit certificaat op elke AD FS-server om de verval datum te bepalen.

Als de geldigheids periode van uw certificaten bijna is verlopen, [genereert en verifieert u een nieuw MFA-certificaat op elke AD FS-server](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

In de volgende richt lijnen vindt u informatie over het beheren van de Azure AD MFA-certificaten op uw AD FS-servers. Wanneer u AD FS configureert met Azure AD MFA, zijn de certificaten die zijn gegenereerd via de `New-AdfsAzureMfaTenantCertificate` Power shell-cmdlet twee jaar geldig. Vernieuw en installeer de verlengde certificaten vóór de verval datum van Ovoid-onderbrekingen in de MFA-service.

## <a name="implement-your-plan"></a>Uw abonnement implementeren

Nu u uw oplossing hebt gepland, kunt u deze implementeren door de volgende stappen uit te voeren:

1. Voldoen aan de vereiste vereisten
   1. [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) implementeren voor hybride scenario's
   1. [Azure AD-toepassingsproxy](../manage-apps/application-proxy.md) implementeren voor alle on-premises apps die zijn gepubliceerd voor Cloud toegang
   1. [NPS](/windows-server/networking/technologies/nps/nps-top) implementeren voor RADIUS-verificatie
   1. Zorg ervoor dat gebruikers zijn overgestapt op ondersteunde versies van Microsoft Office waarvoor moderne verificatie is ingeschakeld
1. Gekozen [verificatie methoden](#choose-verification-options) configureren
1. Definieer uw [benoemde netwerk locaties](../conditional-access/location-condition.md#named-locations)
1. Selecteer groepen om te beginnen met het implementeren van MFA.
1. Uw [beleid voor voorwaardelijke toegang](#create-conditional-access-policy) configureren
1. Configureer uw MFA-registratie beleid
   1. [Gecombineerde MFA en SSPR](howto-registration-mfa-sspr-combined.md)
   1. Met [identiteits beveiliging](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. Gebruikers communicatie verzenden en gebruikers ontvangen om te registreren op [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Bijhouden wie is inge schreven](#identify-non-registered-users)

> [!TIP]
> Government Cloud-gebruikers kunnen zich inschrijven op [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Uw oplossing beheren

Rapporten voor Azure AD MFA

Azure AD Multi-Factor Authentication biedt rapporten via de Azure Portal:

| Rapport | Locatie | Description |
| --- | --- | --- |
| Gebruiks-en fraude waarschuwingen | Aanmeldingen bij Azure AD-> | Bevat informatie over het algehele gebruik, de gebruikers samenvatting en de gebruikers gegevens. en een geschiedenis van fraude waarschuwingen die zijn ingediend tijdens het opgegeven datum bereik. |

## <a name="troubleshoot-mfa-issues"></a>Problemen met MFA oplossen

Vind oplossingen voor veelvoorkomende problemen met Azure AD MFA in het [artikel Troubleshooting Azure ad multi-factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) in het Microsoft ondersteuning Center.

## <a name="next-steps"></a>Volgende stappen

Als u Azure AD-Multi-Factor Authentication in actie wilt zien, voert u de volgende zelf studie uit:

> [!div class="nextstepaction"]
> [Azure AD Multi-Factor Authentication inschakelen](tutorial-enable-azure-mfa.md)
