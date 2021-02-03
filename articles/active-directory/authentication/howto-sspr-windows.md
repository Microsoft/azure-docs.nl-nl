---
title: Self-service voor wacht woord opnieuw instellen voor Windows-apparaten-Azure Active Directory
description: Meer informatie over het inschakelen van Azure Active Directory self-service voor wachtwoord herstel op het Windows-aanmeldings scherm.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2d910c017d3cc626f737bdab50315aef8d1e77
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491382"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>Schakel Azure Active Directory selfservice voor wachtwoord herstel in op het Windows-aanmeldings scherm

Self-service voor wachtwoord herstel (SSPR) biedt gebruikers in Azure Active Directory (Azure AD) de mogelijkheid om hun wacht woord te wijzigen of opnieuw in te stellen, zonder beheerders-of Help Desk. Normaal gesp roken openen gebruikers een webbrowser op een ander apparaat om toegang te krijgen tot de [SSPR-Portal](https://aka.ms/sspr). Als u de ervaring wilt verbeteren op computers met Windows 7, 8, 8,1 en 10, kunt u gebruikers in staat stellen hun wacht woord opnieuw in te stellen op het Windows-aanmeldings scherm.

![Voor beeld van een Windows 7-en 10-Aanmeldings scherm met een SSPR-koppeling](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> In deze zelf studie ziet u een beheerder hoe u SSPR kunt inschakelen voor Windows-apparaten in een onderneming.
>
> Als uw IT-team de mogelijkheid om SSPR van uw Windows-apparaat te gebruiken niet heeft ingeschakeld of als u problemen ondervindt tijdens het aanmelden, kunt u contact met de Help Desk vinden voor meer informatie.

## <a name="general-limitations"></a>Algemene beperkingen

De volgende beperkingen zijn van toepassing op het gebruik van SSPR in het Windows-aanmeldings scherm:

- Wacht woord opnieuw instellen wordt momenteel niet ondersteund vanuit een Extern bureaublad of via Hyper-V-uitgebreide sessies.
- Sommige externe providers van derden veroorzaken problemen met deze functie.
- Het uitschakelen van UAC via wijziging van de [register sleutel EnableLUA](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) is bekend bij het veroorzaken van problemen.
- Deze functie werkt niet voor netwerken met 802.1 x-netwerk verificatie geïmplementeerd en de optie ' direct voor gebruikers aanmelding uitvoeren '. Voor netwerken met 802.1 x-netwerk verificatie is het aanbevolen om computer verificatie te gebruiken om deze functie in te scha kelen.
- Hybride Azure AD-computers moeten over een netwerk verbinding beschikken om het nieuwe wacht woord te kunnen gebruiken en referenties in de cache op te slaan. Dit betekent dat apparaten op het interne netwerk van de organisatie of op een VPN met netwerk toegang tot een on-premises domein controller moeten zijn.
- Als u een installatie kopie gebruikt voordat u Sysprep uitvoert, moet u ervoor zorgen dat de webcache voor de ingebouwde beheerder is gewist voordat u de stap CopyProfile uitvoert. Meer informatie over deze stap vindt u in de ondersteunings artikelen [verslechte prestaties bij gebruik van aangepast standaard gebruikers profiel](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- De volgende instellingen zijn bekend om te voor komen dat wacht woorden op Windows 10-apparaten kunnen worden gebruikt en opnieuw worden ingesteld:
    - Als Ctrl + Alt + del is vereist voor het beleid in Windows 10, werkt **wacht woord opnieuw instellen** niet.
    - Als er meldingen op het vergrendelings scherm zijn uitgeschakeld, werkt **wacht woord opnieuw instellen** niet.
    - *HideFastUserSwitching* is ingesteld op ingeschakeld of 1
    - *DontDisplayLastUserName* is ingesteld op ingeschakeld of 1
    - De functie voor het inschakelen van het *vergrendelings* scherm is ingesteld op ingeschakeld of 1
    - *EnableLostMode* is ingesteld op het apparaat
    - Explorer.exe is vervangen door een aangepaste shell
- De combi natie van de volgende drie instellingen kan ervoor zorgen dat deze functie niet werkt.
    - Interactieve aanmelding: CTRL + ALT + DEL = disabled is uitgeschakeld
    - *DisableLockScreenAppNotifications* = 1 of ingeschakeld
    - Windows SKU is niet Home of Professional Edition

> [!NOTE]
> Deze beperkingen zijn ook van toepassing op de pincode van Windows hello voor bedrijven reset op het vergrendelings scherm van het apparaat.
>

## <a name="windows-10-password-reset"></a>Windows 10-wacht woord opnieuw instellen

Als u een Windows 10-apparaat wilt configureren voor SSPR op het aanmeldings scherm, controleert u de volgende vereisten en configuratie stappen.

### <a name="windows-10-prerequisites"></a>Windows 10-vereisten

- Een beheerder [moet Azure AD selfservice voor wachtwoord herstel inschakelen vanuit het Azure Portal](tutorial-enable-sspr.md).
- Gebruikers moeten zich registreren voor SSPR voordat ze deze functie gebruiken op [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Niet uniek om SSPR te gebruiken in het Windows-aanmeldings scherm, moeten alle gebruikers de contact gegevens van de verificatie opgeven voordat ze hun wacht woord opnieuw kunnen instellen.
- Vereisten voor netwerk proxy:
    - Poort 443 tot `passwordreset.microsoftonline.com` en `ajax.aspnetcdn.com`
    - Windows 10-apparaten ondersteunen alleen proxy configuratie op computer niveau.
- Voer ten minste Windows 10, versie april 2018 update (v1803) uit en de apparaten moeten zijn:
    - Azure AD-deelname
    - Hybride Azure AD-deelname

### <a name="enable-for-windows-10-using-intune"></a>Inschakelen voor Windows 10 met intune

Het implementeren van de configuratie wijziging om SSPR in te scha kelen vanuit het aanmeldings scherm met intune is de meest flexibele methode. Met Intune kunt u een wijziging van de configuratie implementeren voor een specifieke groep machines die u hebt gedefinieerd. Voor deze methode is een Intune-registratie van het apparaat vereist.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Een beleid voor apparaatconfiguratie maken in Intune

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en selecteer **intune**.
1. Maak een nieuw configuratie profiel voor het apparaat door naar **configuratie** profielen voor apparaten te gaan  >  en vervolgens **+ profiel maken** te selecteren.
   - Kies  voor platform *Windows 10 en hoger*
   - Kies bij **profiel type** de optie *aangepast*
1. Selecteer **maken** en geef een beschrijvende naam op voor het profiel, zoals *Windows 10-aanmeld scherm SSPR*

    Geef eventueel een duidelijke beschrijving van het profiel op en selecteer vervolgens **volgende**.
1. Selecteer **toevoegen** onder *configuratie-instellingen* en geef de volgende oma-URI-instelling op om de koppeling wacht woord opnieuw instellen in te scha kelen:
      - Geef een beschrijvende naam op om uit te leggen wat de instelling doet, zoals het toevoegen van een *SSPR-koppeling*.
      - Geef eventueel een duidelijke beschrijving van de instelling op.
      - **OMA-URI** ingesteld op `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Gegevenstype** ingesteld op **Geheel getal**
      - **Waarde** ingesteld op **1**

    Selecteer **toevoegen** en vervolgens **volgende**.
1. Het beleid kan worden toegewezen aan specifieke gebruikers, apparaten of groepen. Wijs het profiel naar wens aan uw omgeving toe, idea liter eerst naar een test groep apparaten en selecteer vervolgens **volgende**.

    Zie [profielen voor gebruikers en apparaten toewijzen in Microsoft intune](/mem/intune/configuration/device-profile-assign)voor meer informatie.

1. Configureer toepas baarheids regels naar wens voor uw omgeving, zoals het *toewijzen van een profiel als OS Edition Windows 10 Enter prise is* en selecteer vervolgens **volgende**.
1. Controleer uw profiel en selecteer vervolgens **maken**.

### <a name="enable-for-windows-10-using-the-registry"></a>Inschakelen voor Windows 10 met behulp van het REGI ster

Voer de volgende stappen uit om SSPR op het aanmeldings scherm in te scha kelen met behulp van een register sleutel:

1. Meld u aan bij de Windows-PC met beheerders referenties.
1. Druk op **Windows**  +  **R** om het dialoog venster *uitvoeren* te openen en voer **regedit** als Administrator uit.
1. Stel de volgende registersleutel in:

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>Problemen oplossen met Windows 10 wacht woord opnieuw instellen

Als u problemen ondervindt met het gebruik van SSPR in het Windows-aanmeldings scherm, bevat het Azure AD-controle logboek informatie over het IP-adres en *ClientType* waar het wacht woord opnieuw is ingesteld, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

![Voor beeld van een Windows 7-wacht woord opnieuw instellen in het Azure AD-controle logboek](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Wanneer gebruikers hun wacht woord opnieuw instellen vanuit het aanmeldings scherm van een Windows 10-apparaat, wordt een tijdelijke account met beperkte bevoegdheden `defaultuser1` gemaakt. Dit account wordt gebruikt om het proces voor het opnieuw instellen van het wachtwoord te beveiligen.

Het account zelf heeft een wille keurig gegenereerd wacht woord, wordt niet weer gegeven voor het aanmelden van apparaten en wordt automatisch verwijderd nadat de gebruiker het wacht woord opnieuw heeft ingesteld. Er `defaultuser` kunnen meerdere profielen bestaan, maar deze kunnen veilig worden genegeerd.

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7, 8 en 8,1 wacht woord opnieuw instellen

Als u een Windows 7-, 8-of 8,1-apparaat wilt configureren voor SSPR op het aanmeldings scherm, controleert u de volgende vereisten en configuratie stappen.

### <a name="windows-7-8-and-81-prerequisites"></a>Vereisten voor Windows 7, 8 en 8,1

- Een beheerder [moet Azure AD selfservice voor wachtwoord herstel inschakelen vanuit het Azure Portal](tutorial-enable-sspr.md).
- Gebruikers moeten zich registreren voor SSPR voordat ze deze functie gebruiken op [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
    - Niet uniek om SSPR te gebruiken in het Windows-aanmeldings scherm, moeten alle gebruikers de contact gegevens van de verificatie opgeven voordat ze hun wacht woord opnieuw kunnen instellen.
- Vereisten voor netwerk proxy:
    - Poort 443 tot `passwordreset.microsoftonline.com`
- Een patch uitgevoerd voor Windows 7-of Windows 8,1-besturings systeem.
- TLS 1,2 ingeschakeld met behulp van de richt lijnen die zijn gevonden in de [register instellingen van Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings#tls-12).
- Als er meer dan één externe referentie provider op uw computer is ingeschakeld, zien gebruikers meer dan één gebruikers profiel in het aanmeldings scherm.

> [!WARNING]
> TLS 1,2 moet zijn ingeschakeld, niet alleen ingesteld op automatisch onderhandelen.

### <a name="install"></a>Installeren

Voor Windows 7, 8 en 8,1 moet er een kleine component op de computer zijn geïnstalleerd om SSPR in te scha kelen op het aanmeldings scherm. Voer de volgende stappen uit om dit SSPR-onderdeel te installeren:

1. Down load het juiste installatie programma voor de versie van Windows die u wilt inschakelen.

    Het software-installatie programma is beschikbaar in het micro soft Download centrum op [https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Meld u aan bij de computer waarop u wilt installeren en voer het installatie programma uit.
1. Na de installatie wordt u ten zeerste aangeraden opnieuw op te starten.
1. Nadat het systeem opnieuw is opgestart, kiest u in het aanmeldings scherm een gebruiker en selecteert u wacht woord verg eten? om de werk stroom voor het opnieuw instellen van wacht woorden te initiëren.
1. Voltooi de werk stroom volgens de stappen op het scherm om uw wacht woord opnieuw in te stellen.

![Voor beeld van Windows 7 op ' wacht woord verg eten? ' SSPR-stroom](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Installatie op de achtergrond

Het onderdeel SSPR kan zonder vragen worden geïnstalleerd of verwijderd met behulp van de volgende opdrachten:

- Gebruik de opdracht ' msiexec/i SsprWindowsLogon.PROD.msi/qn ' voor een installatie op de achtergrond.
- Gebruik de opdracht "msiexec/x SsprWindowsLogon.PROD.msi/qn" voor het ongedaan maken van de installatie.

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Problemen met het opnieuw instellen van wacht woorden in Windows 7, 8 en 8,1 oplossen

Als u problemen ondervindt met het gebruik van SSPR in het Windows-aanmeldings scherm, worden gebeurtenissen geregistreerd op de computer en in azure AD. Azure AD-gebeurtenissen bevatten informatie over het IP-adres en de ClientType waar het wacht woord opnieuw is ingesteld, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

![Voor beeld van een Windows 7-wacht woord opnieuw instellen in het Azure AD-controle logboek](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Als aanvullende logboek registratie vereist is, kan een register sleutel op de computer worden gewijzigd om uitgebreide logboek registratie in te scha kelen. Uitgebreide logboek registratie inschakelen voor het oplossen van problemen met de volgende register sleutel waarde:

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- Als u uitgebreide logboek registratie wilt inschakelen, maakt `REG_DWORD: "EnableLogging"` u een en stelt u deze in op 1.
- Als u uitgebreide logboek registratie wilt uitschakelen, wijzigt `REG_DWORD: "EnableLogging"` u in 0.

## <a name="what-do-users-see"></a>Wat gebruikers zien

Welke wijzigingen voor de gebruiker worden met SSPR geconfigureerd voor uw Windows-apparaten? Hoe weten ze dat ze hun wachtwoord opnieuw kunnen instellen via het aanmeldingsscherm? In de volgende voor beelden ziet u de extra opties voor een gebruiker om hun wacht woord opnieuw in te stellen met behulp van SSPR:

![Voor beeld van een Windows 7-en 10-Aanmeldings scherm met een SSPR-koppeling](./media/howto-sspr-windows/windows-reset-password.png)

Wanneer gebruikers zich proberen aan te melden, zien ze de koppeling **wacht woord opnieuw instellen** of **verg eten wacht woord** waarmee de self-service voor het opnieuw instellen van wacht woorden wordt geopend in het aanmeldings scherm. Deze functionaliteit maakt het mogelijk dat gebruikers hun wachtwoord opnieuw instellen zonder dat ze een ander apparaat moeten gebruiken om toegang te krijgen tot een webbrowser.

Meer informatie voor gebruikers over het gebruik van deze functie vindt u in [het wacht woord voor uw werk of school opnieuw instellen](../user-help/active-directory-passwords-update-your-own-password.md)

## <a name="next-steps"></a>Volgende stappen

Als u de gebruikers registratie wilt vereenvoudigen, kunt u de [contact gegevens van gebruikers verificatie vooraf invullen voor SSPR](howto-sspr-authenticationdata.md).
