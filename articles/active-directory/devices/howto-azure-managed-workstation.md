---
title: Door Azure beheerde werk stations implementeren-Azure Active Directory
description: Meer informatie over het implementeren van beveiligde, door Azure beheerde werk stations om het risico van schending te verminderen vanwege een onjuiste configuratie of inbreuk.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a56cd23494f65b1c74e44868496855c6e4a32bf7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95974082"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Een beveiligd, door Azure beheerd werk station implementeren

Nu u bekend bent met [beveiligde werk stations](concept-azure-managed-workstation.md), is het tijd om het implementatie proces te starten. Met deze richt lijnen gebruikt u gedefinieerde profielen om een werk station te maken dat veiliger is dan het begin.

![Implementatie van een beveiligd werk station](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Selecteer een profiel voordat u de oplossing implementeert. U kunt meerdere profielen tegelijk in een implementatie gebruiken en deze toewijzen aan Tags of groepen.

> [!NOTE]
> Pas een van de profielen toe die nodig zijn voor uw vereisten. U kunt overstappen op een ander profiel door het toe te wijzen in Microsoft Intune.

| Profiel | Beperkt | Verbeterd | Hoog | Gespecialiseerd | Beveiligd | Geïsoleerd |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Gebruiker in azure AD | Ja | Ja | Ja | Ja | Ja | Ja |
| Door intune beheerde | Ja | Ja | Ja | Ja | Ja | Ja |
| Apparaat-Azure AD geregistreerd | Yes |  |  |  |  | |   |
| Apparaat-Azure AD toegevoegd |   | Ja | Ja | Ja | Ja | Ja |
| Basis lijn voor beveiliging van intune toegepast |   | Yes <br> Intensievere | Yes <br> (HighSecurity) | Yes <br> (NCSC) | Yes <br> Gesteld | NA |
| Hardware voldoet aan de veilige Windows 10-standaarden |   | Ja | Ja | Ja | Ja | Ja |
| Micro soft Defender ATP is ingeschakeld |   | Ja  | Ja | Ja | Ja | Ja |
| Beheer rechten verwijderen |   |   | Ja  | Ja | Ja | Ja |
| Implementatie met micro soft auto pilot |   |   | Ja  | Ja | Ja | Ja |
| Apps die alleen door intune zijn geïnstalleerd |   |   |   | Ja | Ja |Ja |
| Url's die zijn beperkt tot goedgekeurde lijst |   |   |   | Ja | Ja |Ja |
| Internet geblokkeerd (inkomend/uitgaand) |   |   |   |  |  |Yes |

> [!NOTE]
> Op de **apparaten** voor beveiligde werk stations worden toegewezen aan profielen en beleid. Gebruikers worden niet rechtstreeks op deze beleids regels toegepast, waardoor het delen van apparaten (gedeelde apparaten) van kracht wordt. Als een beveiligd werk station niet wordt gedeeld in uw implementatie, of als het individuele gebruikers beleid nodig is, kan de toewijzing van de gebruikers beleids profielen worden toegewezen aan de gebruiker en het apparaat. 

## <a name="license-requirements"></a>Licentievereisten

Voor de concepten die in deze hand leiding worden behandeld, wordt ervan uitgegaan dat u Microsoft 365 Enterprise E5 of een vergelijk bare SKU hebt. Enkele van de aanbevelingen in deze hand leiding kunnen worden geïmplementeerd met lagere Sku's. Zie [Microsoft 365 Enterprise Licensing](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)voor meer informatie.

U kunt het inrichten van licenties automatiseren door op [groepen gebaseerde licentie verlening](../enterprise-users/licensing-groups-assign.md) te nemen voor uw gebruikers.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory configuratie

Azure Active Directory (Azure AD) beheert gebruikers, groepen en apparaten voor uw beheerders werkstations. Identiteits Services en-functies inschakelen met een [Administrator-account](../roles/permissions-reference.md).

Wanneer u het beveiligde werk station Administrator-account maakt, wordt het account beschikbaar gesteld aan uw huidige werk station. Zorg ervoor dat u een bekend veilig apparaat gebruikt om deze eerste configuratie en alle globale configuratie uit te voeren. Als u de bloot stelling van aanvallen voor de eerste keer wilt beperken, kunt u overwegen de [richt lijnen te volgen om malware-infecties te voor komen](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Multi-factor Authentication vereisen, ten minste voor uw beheerders. Zie op [cloud gebaseerde MFA implementeren](../authentication/howto-mfa-getstarted.md) voor implementatie richtlijnen.

### <a name="azure-ad-users-and-groups"></a>Azure AD-gebruikers en -groepen

1. Blader in het Azure Portal naar **Azure Active Directory**  >  **gebruikers**  >  **nieuwe gebruiker**.
1. Maak de apparaat-beheerder door de stappen in de [zelf studie gebruikers maken](/Intune/quickstart-create-user)te volgen.
1. Voer het volgende in:

   * **Naam** : Beveilig de beheerder van het werk station
   * **Gebruikers naam** - `secure-ws-admin@identityitpro.com`
   * **Directory-functie**  -  **Beperkte beheerder** en selecteer de rol **intune-beheerder** .

1. Selecteer **Maken**.

Vervolgens maakt u twee groepen: werk Station gebruikers en Workstation apparaten.

Blader in het Azure Portal naar **Azure Active Directory**  >  **groepen**  >  **nieuwe groep**.

1. Voor de werk Station-gebruikers groep wilt u mogelijk [licentie verlening op basis](../enterprise-users/licensing-groups-assign.md) van een groep configureren om het inrichten van licenties voor gebruikers te automatiseren.
1. Voer voor de groep werk Station gebruikers het volgende in:

   * **Groeps type** -beveiliging
   * **Groeps naam** -beveiligde werk Station gebruikers
   * **Type lidmaatschap** : toegewezen

1. Uw beveiligde werk station-beheerders gebruiker toevoegen: `secure-ws-admin@identityitpro.com`
1. U kunt andere gebruikers toevoegen die beveiligde werk stations gaan beheren.
1. Selecteer **Maken**.
1. Voer voor de groep werk station apparaten het volgende in:

   * **Groeps type** -beveiliging
   * **Groeps naam** -beveiligde werk stations
   * **Type lidmaatschap** : toegewezen

1. Selecteer **Maken**.

### <a name="azure-ad-device-configuration"></a>Configuratie van Azure AD-apparaten

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Opgeven wie apparaten mag toevoegen aan Azure AD

Configureer de instelling van uw apparaten in Active Directory zodat uw administratieve beveiligings groep apparaten kan toevoegen aan uw domein. Als u deze instelling wilt configureren vanuit de Azure Portal:

1. Ga naar **Azure Active Directory** > **Apparaten** > **Apparaatinstellingen**.
1. Kies **geselecteerd** onder **gebruikers kunnen apparaten toevoegen aan Azure AD** en selecteer vervolgens de groep beveiligde werk Station-gebruikers.

#### <a name="removal-of-local-admin-rights"></a>Verwijderen van lokale beheerders rechten

Deze methode vereist dat gebruikers van het VIP-, DevOps-en secure-level werk station geen beheerders rechten hebben op hun computers. Als u deze instelling wilt configureren vanuit de Azure Portal:

1. Ga naar **Azure Active Directory** > **Apparaten** > **Apparaatinstellingen**.
1. Selecteer **geen** onder **aanvullende lokale beheerders op apparaten die zijn toegevoegd aan Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Multi-factor Authentication vereisen voor het toevoegen van apparaten

Om het proces van het toevoegen van apparaten aan Azure AD verder te verbeteren:

1. Ga naar **Azure Active Directory** > **Apparaten** > **Apparaatinstellingen**.
1. Selecteer **Ja** onder **vereisen multi-factor Authentication om lid te worden van apparaten**.
1. Selecteer **Opslaan**.

#### <a name="configure-mobile-device-management"></a>Mobile Device Management configureren

Vanuit de Azure-portal:

1. Blader naar **Azure Active Directory**  >  **Mobility (MDM en mam)**  >  **Microsoft intune**.
1. Wijzig de instelling voor het **MDM-gebruikers bereik** in **alle**.
1. Selecteer **Opslaan**.

Met deze stappen kunt u apparaten beheren met intune. Zie voor meer informatie de [Snelstartgids voor intune: automatische inschrijving instellen voor Windows 10-apparaten](/Intune/quickstart-setup-auto-enrollment). In een toekomstige stap maakt u een intune-configuratie en nalevings beleid.

#### <a name="azure-ad-conditional-access"></a>Voorwaardelijke toegang van Azure AD

Voorwaardelijke toegang van Azure AD kan helpen om geprivilegieerde beheer taken te beperken tot compatibele apparaten. Vooraf gedefinieerde leden van de groep **beveiligde werk Station-gebruikers** zijn vereist om multi-factor Authentication uit te voeren wanneer ze zich aanmelden bij Cloud toepassingen. Een best practice is het uitsluiten van toegangs accounts voor nood gevallen van het beleid. Zie [accounts voor nood toegang beheren in azure AD](../roles/security-emergency-access.md)voor meer informatie.

## <a name="intune-configuration"></a>InTune-configuratie

### <a name="configure-enrollment-status"></a>Inschrijvings status configureren

Het is belang rijk om ervoor te zorgen dat uw beveiligde werk station een vertrouwd schoon apparaat is. Wanneer u nieuwe apparaten aanschaft, kunt u instellen dat de fabrieks instellingen [van Windows 10 Pro in de S-modus](/Windows/deployment/Windows-10-pro-in-s-mode)worden ingesteld, waardoor de bloot stelling aan beveiligings problemen tijdens het beheer van de toeleverings keten wordt beperkt. Nadat u een apparaat van uw leverancier hebt ontvangen, kunt u auto pilot gebruiken om de modus van S te wijzigen. De volgende richt lijnen bevatten informatie over het Toep assen van het transformatie proces.

Om ervoor te zorgen dat apparaten volledig worden geconfigureerd voordat ze worden gebruikt, biedt intune een manier om het **apparaat te blok keren totdat alle apps en profielen zijn geïnstalleerd**.

Van de **Azure Portal**:

1. Ga naar **Microsoft intune** apparaatregistratie de standaard instellingen voor de inschrijving van de  >  **Device enrollment**  >  **Windows-inschrijvings**  >  **status pagina**  >  **Default**  >  **Settings**.
1. Stel de voortgang van de installatie van het **app-profiel weer geven** in op **Ja**.
1. Het **gebruik van het blok apparaat instellen totdat alle apps en profielen** op **Ja** zijn geïnstalleerd.

### <a name="create-an-autopilot-deployment-profile"></a>Een Autopilot-implementatieprofiel maken

Nadat u een apparaatgroep hebt gemaakt, moet u een implementatie profiel maken om de auto pilot-apparaten te configureren.

In intune in de Azure Portal:

1. Selecteer **registratie** van  >  **Windows-inschrijvings**  >  **profielen** voor apparaatregistratie  >  **maken**.
1. Voer het volgende in:

   * Naam: **profiel voor beveiligde werk station implementeren**.
   * Beschrijving: **implementatie van beveiligde werk stations**.
   * Stel **Alle doelapparaten converteren naar Autopilot** in op **Ja**. Deze instelling zorgt ervoor dat alle apparaten in de lijst worden ingeschreven bij de Autopilot-implementatieservice. Het kan 48 uur duren voordat de registratie is verwerkt.

1. Selecteer **Volgende**.

   * Voor **implementatie modus** kiest u **zelf implementeren (preview)**. Apparaten met dit profiel zijn gekoppeld aan de gebruiker die het apparaat inschrijft. Er zijn gebruikersreferenties vereist om het apparaat in te kunnen schrijven. Het is belang rijk om te weten dat wanneer u een apparaat implementeert in de modus **zelf implementeren** , u laptops in een gedeeld model kunt implementeren. Er vindt geen gebruikers toewijzing plaats totdat het apparaat voor de eerste keer wordt toegewezen aan een gebruiker. Als gevolg hiervan worden alle gebruikers beleidsregels, zoals BitLocker, pas ingeschakeld als een gebruikers toewijzing is voltooid. Zie [selected profiles](/intune/device-profile-assign)(Engelstalig) voor meer informatie over het aanmelden bij een beveiligd apparaat.
   * In het vak **toevoegen aan Azure AD** moet het **lid van Azure AD zijn opgenomen** en grijs worden weer gegeven.
   * Selecteer uw taal (regio), type **standaard** gebruikers account. 

1. Selecteer **Next**.

   * Selecteer een scope-tag als u deze vooraf hebt geconfigureerd.

1. Selecteer **Next**.
1. Kies **toewijzingen**  >  **toewijzen aan**  >  **geselecteerde groepen**. Kies **werk stations beveiligen** in **groepen selecteren die u wilt toevoegen**.
1. Selecteer **Next**.
1. Selecteer **Maken** om het profiel te maken. Het Autopilot-implementatieprofiel is nu klaar om te worden toegewezen aan apparaten.

Registratie van apparaten in auto pilot biedt een andere gebruikers ervaring op basis van apparaattype en rol. In het voor beeld van de implementatie illustreren we een model waarin de beveiligde apparaten bulksgewijs worden geïmplementeerd en kunnen worden gedeeld, maar wanneer het apparaat voor de eerste keer wordt gebruikt, wordt het aan een gebruiker toegewezen. Zie [intune auto pilot Device Enrollment](/intune/device-enrollment)(Engelstalig) voor meer informatie.

### <a name="configure-windows-update"></a>Windows Update configureren

Het up-to-date houden van Windows 10 is een van de belangrijkste dingen die u kunt doen. Als u Windows met een veilige status wilt behouden, implementeert u een update ring om het tempo te beheren dat updates op werk stations worden toegepast. 

In deze richt lijnen wordt geadviseerd om een nieuwe update-ring te maken en de volgende standaard instellingen te wijzigen:

In Azure Portal:

1. Ga naar **Microsoft intune**-  >  **software-updates**  >  **Windows 10-update ringen**.
1. Voer het volgende in:

   * Naam: door **Azure beheerde werk station-updates**
   * Onderhouds kanaal- **Windows Insider-snel**
   * Uitstel van kwaliteits updates (dagen)- **3**
   * Uitstel periode voor onderdelen updates (dagen)- **3**
   * Gedrag van automatische updates- **automatisch installeren en opnieuw opstarten zonder besturings element voor eind gebruiker**
   * Blok keren dat de gebruiker Windows-updates onderbreekt- **blok keren**
   * Vereisen dat de gebruiker opnieuw wordt opgestart buiten de werk uren- **vereist**
   * Gebruiker mag opnieuw worden opgestart (opnieuw opgestart)- **vereist**
   * Overgangs gebruikers om opnieuw op te starten na het automatisch opnieuw opstarten (dagen)- **3**
   * Herinnering voor ingeschakelde opnieuw starten (dagen)- **3**
   * Deadline instellen voor opnieuw opstarten in behandeling (dagen)- **3**

1. Selecteer **Maken**.
1. Voeg op het tabblad **toewijzingen** de groep **beveiligde werk stations** toe.

Zie [Policy CSP-update](/windows/client-management/mdm/policy-csp-update)voor meer informatie over Windows Update-beleid.

### <a name="windows-defender-atp-intune-integration"></a>Integratie van Windows Defender ATP intune

Windows Defender ATP en Microsoft Intune samen werken om inbreuk op de beveiliging te voor komen. Ze kunnen ook de impact van inbreuken beperken. ATP-functies bieden realtime detectie van bedreigingen en het inschakelen van uitgebreide controle en logboek registratie van de eind punt apparaten.

Als u de integratie van Windows Defender ATP en intune wilt configureren, gaat u naar de Azure Portal.

1. Blader naar **Microsoft intune**  >  **apparaatcompatibiliteit**  >  **Windows Defender ATP**.
1. Selecteer in stap 1 onder **Windows Defender ATP configureren** **de optie Windows Defender atp verbinden met Microsoft intune in Windows Defender Security Center**.
1. In het Windows Defender Beveiligingscentrum:

   1. Selecteer **Instellingen** > **Geavanceerde functies**.
   1. Kies **aan** **Microsoft intune-verbinding**.
   1. Selecteer **Voorkeuren opslaan**.

1. Nadat een verbinding tot stand is gebracht, keert u terug naar intune en selecteert u bovenaan **vernieuwen** .
1. Stel **Windows-apparaten met versie 10.0.15063 en hoger verbinden met Windows Defender ATP** in op **Aan**.
1. Selecteer **Opslaan**.

Zie [Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)(Engelstalig) voor meer informatie.

### <a name="finish-workstation-profile-hardening"></a>Beveiliging van het workstation profiel volt ooien

Down load en voer het juiste script uit om de beveiliging van de oplossing te volt ooien. Zoek de Download koppelingen voor het gewenste **profiel niveau**:

| Profiel | Download locatie | Bestands |
| --- | --- | --- |
| Lage beveiliging | N.v.t. | N.v.t. |
| Verbeterde beveiliging | https://aka.ms/securedworkstationgit | Uitgebreid-werk station-Windows10-(1809). ps1 |
| Hoge beveiliging | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809). ps1 |
| Gespecialiseerd | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline.ps1 |
| Gespecialiseerde naleving * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10 (1803). ps1 |
| Beveiligd | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809) -SecurityBaseline.ps1 |

\* Gespecialiseerde naleving is een script dat de gespecialiseerde configuratie afdwingt die is opgenomen in de NCSC Windows10 Security Baseline Baseline.

Nadat het script is uitgevoerd, kunt u in intune updates Toep assen op profielen en beleid. De scripts voor uitgebreide en veilige profielen maken beleids regels en profielen voor u, maar u moet het beleid toewijzen aan uw apparaatgroep voor **beveiligde werk stations** .

* Hier vindt u de intune-configuratie profielen voor apparaten die zijn gemaakt door de scripts: **Azure Portal**  >  **Microsoft intune**  >  **configuratie**  >  **profielen** voor apparaten.
* Hier vindt u het intune-nalevings beleid voor apparaten dat is gemaakt door de scripts: **Azure Portal**  >  **Microsoft intune**  >  **nalevings**  >  **beleid** voor apparaten.

Als u de wijzigingen wilt bekijken die door de scripts zijn aangebracht, kunt u de profielen exporteren. Op deze manier kunt u extra beveiliging bepalen die nodig is, zoals beschreven in de SECCON- [documentatie](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Voer het intune-export script `DeviceConfiguration_Export.ps1` uit vanuit de [apparaatconfiguratie GiuHub-opslag plaats](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) om alle huidige intune-profielen te exporteren.

## <a name="additional-configurations-and-hardening-to-consider"></a>Aanvullende configuraties en beveiliging om rekening mee te houden

Door de volgende instructies te volgen, hebt u een beveiligd werk station geïmplementeerd. U moet echter ook rekening houden met extra besturings elementen. Bijvoorbeeld:

* toegang tot alternatieve browsers beperken
* uitgaande HTTP toestaan
* selecteren van websites blok keren
* machtigingen instellen voor het uitvoeren van aangepaste Power shell-scripts

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Regels instellen in de Firewall Configuration service provider (CSP)

U kunt wijzigingen aanbrengen in het beheer van zowel binnenkomende als uitgaande regels als nodig is voor uw toegestane en geblokkeerde eind punten. Als u doorgaat met de beveiliging van het beveiligde werk station, kunt u de beperking versoepelen waardoor al het binnenkomende en uitgaande verkeer wordt geweigerd. U kunt toegestane uitgaande sites toevoegen aan gemeen schappelijke en vertrouwde websites. Zie de [Firewall Configuration-service](/Windows/client-management/mdm/firewall-csp)voor meer informatie.

Beperkend URL-verkeer beheer omvat:

* Alle inkomend verkeer dat is ingesteld in het script voor het beveiligde werk station, wordt geweigerd.
* Alle uitgaand verkeer weigeren behalve de geselecteerde Azure-en micro soft-Services, inclusief Azure Cloud Shell en de mogelijkheid om Azure-wacht woord opnieuw in te stellen.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Als u meer granulatie wilt bieden voor uw blokkerings regels, kunt u verwijzen naar het Spamhaus-project dat [de lijst met domein blokken (dubbele)](https://www.spamhaus.org/dbl/)onderhoudt: een goede resource die moet worden gebruikt als een geavanceerde set regels voor het blok keren van sites.

### <a name="manage-local-applications"></a>Lokale toepassingen beheren

Het beveiligde werk station wordt verplaatst naar een echt beveiligde status wanneer lokale toepassingen worden verwijderd, inclusief productiviteits toepassingen. Hier kunt u Chrome toevoegen als de standaard browser en intune gebruiken om de mogelijkheid van een gebruiker om de browser en de invoeg toepassingen te wijzigen te beperken.

#### <a name="deploy-applications-using-intune"></a>Toepassingen implementeren met intune

In sommige gevallen zijn toepassingen als de Google Chrome-browser vereist op het beveiligde werk station. Het volgende voor beeld bevat instructies voor het installeren van Chrome op apparaten in de beveiligings groep **beveiligde werk stations**.

1. Down load de Offline Installer [Chrome bundel voor Windows 64 bits](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Pak de bestanden uit en noteer de locatie van het `GoogleChromeStandaloneEnterprise64.msi` bestand.
1. Ga in het **Azure Portal** naar **Microsoft intune**  >  apps voor **client-apps**  >  **Apps**  >  **toevoegen**.
1. Kies onder **app-type** de optie **line-of-Business**.
1. Onder **app-pakket bestand** selecteert u het `GoogleChromeStandaloneEnterprise64.msi` bestand van de uitgepakte locatie en selecteert u **OK**.
1. Geef onder **app-gegevens** een beschrijving en een uitgever op. Selecteer **OK**.
1. Selecteer **Toevoegen**.
1. Selecteer op het tabblad **toewijzingen** de optie **beschikbaar voor Inge schreven apparaten** onder **toewijzings type**.
1. Voeg onder **opgenomen groepen** de groep **beveiligde werk stations** toe.
1. Selecteer **OK** en selecteer vervolgens **Opslaan**.

Zie voor meer informatie over het configureren van Chrome-instellingen [Chrome browser beheren met Microsoft intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>De bedrijfs portal configureren voor aangepaste apps

In een beveiligde modus is de installatie van de toepassing beperkt tot de intune-bedrijfs portal. Voor de installatie van de portal is echter toegang tot Microsoft Store vereist. In uw beveiligde oplossing kunt u de bedrijfs portal beschikbaar maken voor alle apparaten via een offline modus.

Een door intune beheerde kopie van de [bedrijfsportal](/Intune/store-apps-company-portal-app) biedt u toegang op aanvraag tot extra hulpprogram ma's die u naar gebruikers van de beveiligde werk stations kunt pushen.

Mogelijk moet u Windows 32-bits Apps of andere apps installeren waarvan de implementatie speciale voor bereidingen vereist. In dergelijke gevallen kunt u het [micro soft Win32 content prep-hulp programma](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) een kant-en-klaar `.intunewin` indelings bestand bieden voor installatie.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Voorwaardelijke toegang alleen toestaan dat beveiligde werk stations toegang krijgen Azure Portal

Azure AD biedt de mogelijkheid om te beheren en te beperken wie en wat toegang heeft tot uw Azure-cloud Beheer Portal. Als u [voorwaardelijke toegang](../conditional-access/overview.md) inschakelt, kunt u ervoor zorgen dat alleen uw beveiligde werk station bronnen kan beheren of wijzigen. Het is essentieel dat bij het implementeren van deze functie, als de functionaliteit voor [nood toegang](../roles/security-emergency-access.md) alleen kan of moet worden gebruikt voor extreme gevallen en het account dat wordt beheerd via beleid.

> [!NOTE]
> U moet een gebruikers groep maken en uw nood gebruiker toevoegen waarmee het beleid voor voorwaardelijke toegang kan worden omzeild. In ons voor beeld hebben we een beveiligings groep met de naam **Emergency BreakGlass**

1. Blader naar het **Azure Portal**  >  **Microsoft intune**  >  **voorwaardelijke toegang-beleid**  >  **Nieuw beleid**.
1. Geef een **naam** op voor het beleid.
1. **Gebruikers en groepen** selecteren  >  **gebruikers en groepen selecteren** 
1. Selecteer **Include**  >  **Directory-functies** insluiten > Kies de rollen > globale beheerder, beheerder van beschermde rol, bevoegde verificatie beheerder, beveiligings beheerder, nalevings beheerder, beheerder van voorwaardelijke toegang, toepassings beheerder, Cloud toepassings beheerder, intune-service beheerder
1. Selecteer **uitsluiten** > Kies **gebruikers en groepen** > selecteer **uitgesloten gebruikers selecteren** > Selecteer uw groep met **nood BreakGlass** .
1. Selecteer **Cloud-apps of-acties** > **alle Cloud-apps** te selecteren
1. Selecteer **voor waarden** > Selecteer **device platforms** > kies configure **Ja** > Select **device platforms selecteren** **Windows**
1. Selecteer **toegangs beheer** > Selecteer **toegang verlenen** **Ja** > Kies **vereisen dat het apparaat moet worden gemarkeerd als compatibel**. 
1. Selecteer **beleid inschakelen**  >  **op**
 
Met deze beleids instelling zorgt u ervoor dat uw beheerders een compatibel Windows-apparaat moeten gebruiken, dat is ingesteld door intune en WDATP. 

Organisaties moeten ook overwegen om verouderde verificatie protocollen in hun omgevingen te blok keren. Er zijn meerdere manieren om deze taak uit te voeren. voor meer informatie over het blok keren van verouderde verificatie protocollen raadpleegt u het artikel [: verouderde verificatie naar Azure AD blok keren met voorwaardelijke toegang](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Power shell gebruiken om aangepaste instellingen te maken

U kunt ook Power shell gebruiken om de mogelijkheden voor het beheer van hosts uit te breiden. Met dit voorbeeld script wordt een standaard achtergrond ingesteld op de host. Het is een mogelijkheid die ook beschikbaar is via de Azure Portal en profielen. Het voorbeeld script dient alleen ter illustratie van de Power shell-functionaliteit.

Mogelijk moet u bepaalde aangepaste besturings elementen en instellingen op uw beveiligde werk stations instellen. In dit voor beeld wordt de achtergrond van het werk station gewijzigd door gebruik te maken van de mogelijkheid van Power shell om het apparaat eenvoudig te identificeren als een geschikt, beveiligd werk station.

Met het [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) script uit het micro soft scripting Center kan Windows deze [gratis, algemene achtergrond afbeelding](https://i.imgur.com/OAJ28zO.png) laden bij het opstarten.

1. Down load het script naar een lokaal apparaat.
1. Werk de customerXXXX en de download locatie van de achtergrond afbeelding bij. In ons voor beeld vervangen we customerXXXX naar achtergronden.
1. Blader naar het **Azure Portal**  >  **Microsoft intune**  >  **Device configuration**  >  **Power shell-scripts**  >  **toevoegen**.
1. Geef een **naam** op voor het script en geef de locatie van het **script** op.
1. Selecteer **Configureren**.
   1. Stel **Dit script uitvoeren met de aanmeldings referenties** in op **Ja**.
   1. Selecteer **OK**.
1. Selecteer **Maken**.
1. Selecteer **toewijzingen**  >  **groepen selecteren**.
   1. De beveiligings groep **beveiligen werk stations** toevoegen.
   1. Selecteer **Opslaan**.

## <a name="enroll-and-validate-your-first-device"></a>Uw eerste apparaat inschrijven en valideren

1. Als u uw apparaat wilt inschrijven, hebt u de volgende gegevens nodig:
   * **Serie nummer** : gevonden op het chassis van het apparaat.
   * **Windows-product-id** : gevonden onder **systeem**  >  **info** in het menu Windows-instellingen.
   * U kunt [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) uitvoeren om een CSV-hash-bestand op te halen met alle vereiste gegevens voor de registratie van het apparaat.
   
     Voer uit `Get-WindowsAutoPilotInfo – outputfile device1.csv` om de informatie uit te voeren als een CSV-bestand dat u in intune kunt importeren.

     > [!NOTE]
     > Voor het script zijn verhoogde rechten vereist. Deze wordt uitgevoerd als een externe hand tekening. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`Met deze opdracht kan het script correct worden uitgevoerd.

   * U kunt deze informatie verzamelen door u aan te melden bij een apparaat met Windows 10 versie 1809 of hoger. De wederverkoper van uw hardware kan deze informatie ook verstrekken.
1. Ga in het **Azure Portal** naar **Microsoft intune**  >  **apparaatregistratie**  >  **Windows registratie**  >  **apparaten-Windows auto pilot-apparaten beheren**.
1. Selecteer **importeren** en kies uw CSV-bestand.
1. Voeg het apparaat toe aan de beveiligings groep **beveiligde werk stations** .
1. Op het Windows 10-apparaat dat u wilt configureren, gaat u naar **Windows-instellingen**  >  **Update & beveiligings**  >  **herstel**.
   1. Kies **aan de slag** onder **deze PC opnieuw instellen**.
   1. Volg de aanwijzingen om het apparaat opnieuw in te stellen en opnieuw te configureren met het profiel en nalevings beleid dat is geconfigureerd.

Nadat u het apparaat hebt geconfigureerd, voltooit u een controle en controleert u de configuratie. Controleer of het eerste apparaat juist is geconfigureerd voordat u doorgaat met de implementatie.

## <a name="assign-devices"></a>Apparaten toewijzen

Als u apparaten en gebruikers wilt toewijzen, moet u de [geselecteerde profielen](/intune/device-profile-assign) toewijzen aan uw beveiligings groep. Alle nieuwe gebruikers die machtigingen nodig hebben voor de service, moeten ook worden toegevoegd aan de beveiligings groep.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Sentinel en Windows Defender ATP gebruiken om beveiligings incidenten te bewaken en erop te reageren

Het bewaken van de implementatie van een beveiligd werk station kan worden uitgevoerd door [Sentinel] in te scha kelen en te gebruiken voor het [beheren van dreigingen en beveiligings problemen](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) . de richt lijnen bieden geen uitgebreide dreigingen van bedreigingen, maar bieden goede gang bare inspanningen om potentiële beveiligings incidenten te bewaken en hierop te reageren.

We gaan **Azure-Sentinel** gebruiken voor: 

* Gegevens verzamelen uit intune, Azure Portal en Azure AD voor het controleren van gebruikers en apparaten
* Verdachte activiteit van de configuratie van gebruikers en apparaten onderzoeken
* En de mogelijkheid om beveiligings onderzoeken te verkennen met behulp van WDATP

Voor Sentinel-bewaking moeten connectors met uw gegevens bronnen, zoals Azure AD, worden ingesteld.

1. Ga in de **Azure Portal** naar **Azure Sentinel (preview)** > Selecteer **toevoegen**
1. Selecteer **een nieuwe werk ruimte maken** in het **Sentinel Kies een werk ruimte die u wilt toevoegen aan Azure** .
1. Voer het volgende in:
   * **Log Analytics-werk ruimte** -' beveiliging van werk station controleren '
   * **Abonnement** : Selecteer uw actieve abonnement
   * **Resource groep** : Selecteer de * * nieuwe maken * * > beveiligde werk station RG > **OK**
   * **Locatie** : Selecteer de locatie die geografisch het meest geschikt is voor uw implementatie
   * **Prijs categorie** : Selecteer **Per GB (2018)**
1. Selecteer **OK**.

Nu gaan we de beschik bare beveiligde gegevens bronnen voor werk stations koppelen aan de bewaking.

1. Ga in het **Azure Portal** naar **Azure Sentinel Workspace** > Selecteer werk ruimte bewaking van **beveiligde werk stations**
1. **Gegevens connectors** selecteren
1. Kies **Azure Active Directory** > de pagina Connector openen > nadat u de vereiste hebt bekeken. Ga verder met de configuratie en selecteer **verbinding maken** voor zowel logboek registratie van Azure AD als Azure AD-audit Logboeken.
1. Kies **Azure Activity** > pagina connector > openen nadat u de vereiste hebt bekeken. Ga door met het configureren van Azure-activiteiten logboeken > Selecteer uw abonnement > Selecteer **verbinding maken**

Wanneer gegevens door Sentinel worden verzameld, kunt u activiteiten observeren door **Azure Portal** te selecteren, gaat u naar **overzicht van Azure Sentinel** 

**Windows Defender ATP (WDATP)** wordt gebruikt om het volgende te doen:

* Doorlopend observeren en bewaken beveiligings problemen en onjuiste configuraties
* Gebruik WDATP om de voor delen van dynamische bedreigingen in de natuur te bepalen
* Schijf correlatie van beveiligings problemen met waarschuwingen voor eind punten en antwoorden (EDR)
* Het dash board gebruiken om beveiligings problemen op computer niveau te identificeren tijdens onderzoek
* Herstel bewerkingen naar intune pushen

Uw [Defender ATP-dash board](https://securitycenter.windows.com/machines)configureren. Met behulp van de richt lijnen in het [dash board & voor risico beheer](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights)

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Toepassings activiteiten bewaken met micro soft Monitoring Agent (MMA)
Vanaf het gespecialiseerde werk station is app-kluis ingeschakeld voor het bewaken van toepassings activiteiten op een werk station. Als u de bewaking wilt integreren in uw Log Analytics-werk ruimte, moet u een MMA-agent en-configuratie volgen. 

> [!NOTE]
> Het gespecialiseerde Profiel van het werk station bevat het AppLocker-bewakings beleid. De implementatie van het beleid is vereist voor de bewaking van toepassings activiteiten op een client

De MMA-agent implementeren met het Power shell-script van intune

1. Down load het installatie [script naar een lokaal apparaat](https://aka.ms/securedworkstationgit).
1. De para meters, **$WorkSpaceID** en **$WorkSpaceKey** bijwerken
1. Blader naar het **Azure Portal**  >  **Microsoft intune**  >  **Device configuration**  >  **Power shell-scripts**  >  **toevoegen**.
1. Geef een **naam** op voor het script en geef de locatie van het **script** op.
1. Selecteer **Configureren**.
   1. Stel **Dit script uitvoeren met de aanmeldings referenties** in op **Ja**.
   1. Selecteer **OK**.
1. Selecteer **Maken**.
1. Selecteer **toewijzingen**  >  **groepen selecteren**.
   1. De beveiligings groep **beveiligen werk stations** toevoegen.
   1. Selecteer **Opslaan**.

Vervolgens moet u Log Analytics instellen om de nieuwe logboeken te ontvangen
1. Ga in het **Azure Portal** naar **Log Analytics werk ruimte** > Selecteer controle van beveiligd werk station
1. Selecteer **Geavanceerde instellingen**  >  **gegevens** van  >  **Windows-gebeurtenis logboeken**
1. In **gebeurtenissen verzamelen uit de volgende gebeurtenis logboeken** 
1. Voer het volgende in:
   * ' Micro soft-Windows-AppLocker/EXE en DLL ' > **gegevens** selectie opheffen
   * ' Micro soft-Windows-AppLocker/MSI en script ' **> selectie** opheffen
   * ' Micro soft-Windows-AppLocker/verpakte app-implementatie ' > **selectie** opheffen
   * ' Micro soft-Windows-AppLocker/verpakte app-Execution ' > **selectie** opheffen
1. Selecteer **Opslaan**.

Toepassings Logboeken is beschikbaar in uw geselecteerde Log Analytics-werk ruimte.

## <a name="monitoring"></a>Bewaking

* Meer informatie over het [detecteren van bedreigingen met Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md)
* [Incidenten onderzoeken met Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
* [Automatische bedreigings reacties instellen in azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
* Meer informatie over hoe u uw [blootstellings Score](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score) kunt controleren
* [Beveiligings aanbeveling](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation) controleren
* Beveiligings [herstel](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) beheren
* [Eindpunt detectie en-antwoorden](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response) beheren
* Bewaak profielen met [intune-profiel bewaking](/intune/device-profile-monitor).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Microsoft intune](/intune/index).
* Meer informatie over [Azure AD](../index.yml).
* Werken met [micro soft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* [Azure-Sentinel](../../sentinel/index.yml) ontdekken