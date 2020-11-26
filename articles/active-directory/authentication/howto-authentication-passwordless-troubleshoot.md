---
title: Bekende problemen en probleem oplossing voor hybride FIDO2-beveiligings sleutels-Azure Active Directory
description: Meer informatie over bekende problemen en manieren voor het oplossen van problemen met wacht woord hybride FIDO2-beveiligings sleutel aanmelden met behulp van Azure Active Directory (preview-versie)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 690d4761657b8bf6e5ba63ddfbce7163584e64e2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174031"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad-preview"></a>Problemen oplossen voor hybride implementaties van FIDO2-beveiligings sleutels in azure AD (preview)

In dit artikel vindt u informatie over veelgestelde vragen over hybride, aan Azure AD gekoppelde apparaten en aanmelding met een wacht woord voor on-premises bronnen. Met deze functie zonder wacht woord kunt u Azure AD-verificatie op Windows 10-apparaten inschakelen voor hybride Azure AD-apparaten die zijn toegevoegd met FIDO2-beveiligings sleutels. Gebruikers kunnen zich aanmelden bij Windows op hun apparaten met moderne referenties zoals FIDO2-sleutels en toegang hebben tot traditionele Active Directory Domain Services (AD DS) resources met een naadloze SSO-ervaring (eenmalige aanmelding) voor hun on-premises resources.

De volgende scenario's voor gebruikers in een hybride omgeving worden ondersteund:

* Meld u aan bij hybride Azure AD gekoppelde apparaten met behulp van FIDO2-beveiligings sleutels en ontvang SSO-toegang tot on-premises bronnen.
* Meld u aan bij Azure AD gekoppelde apparaten met behulp van FIDO2-beveiligings sleutels en ontvang EENMALIGe toegang tot on-premises bronnen.

Om aan de slag te gaan met FIDO2-beveiligings sleutels en hybride toegang tot on-premises bronnen, raadpleegt u de volgende artikelen:

* [Beveiligings sleutels met een wacht woord](howto-authentication-passwordless-security-key.md)
* [Windows 10 zonder wachtwoord](howto-authentication-passwordless-security-key-windows.md)
* [Wacht woord on-premises](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> FIDO2-beveiligings sleutels zijn een open bare preview-functie van Azure Active Directory. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="known-issues"></a>Bekende problemen

* [Gebruikers kunnen zich niet aanmelden met FIDO2-beveiligings sleutels omdat Windows hello gezicht te snel is en het standaard mechanisme voor aanmelden is](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Gebruikers kunnen FIDO2-beveiligings sleutels niet direct gebruiken nadat ze een hybride, aan Azure AD gekoppelde computer hebben gemaakt](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Gebruikers kunnen geen SSO voor mijn NTLM-netwerk bron ophalen nadat ze zich hebben aangemeld met een FIDO2-beveiligings sleutel en een prompt voor referenties hebben ontvangen](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Gebruikers kunnen zich niet aanmelden met FIDO2-beveiligings sleutels omdat Windows hello gezicht te snel is en het standaard mechanisme voor aanmelden is

Windows hello Face is de beoogde beste ervaring voor een apparaat waarop een gebruiker is inge schreven. FIDO2-beveiligings sleutels zijn bedoeld voor gebruik op gedeelde apparaten of waar de registratie van Windows hello voor bedrijven een barrière vormt.

Als Windows hello gezicht verhindert dat de gebruikers het aanmeldings scenario voor de FIDO2-beveiligings sleutel proberen uit te voeren, kunnen gebruikers zich aanmelden met Hello gezicht uitschakelen door gezichts inschrijving te verwijderen in **instellingen > Sign-In opties**.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Gebruikers kunnen FIDO2-beveiligings sleutels niet direct gebruiken nadat ze een hybride, aan Azure AD gekoppelde computer hebben gemaakt

Nadat het domein is toegevoegd aan een schone installatie van een hybride Azure AD-computer, moet u zich aanmelden met een wacht woord en wachten tot het beleid is gesynchroniseerd voordat u kunt gebruiken om een FIDO2-beveiligings sleutel te gebruiken om u aan te melden.

Dit gedrag is een bekende beperking voor apparaten die lid zijn van een domein en is niet specifiek voor FIDO2-beveiligings sleutels.

Als u de huidige status wilt controleren, gebruikt u de `dsregcmd /status` opdracht. Controleer of *AzureAdJoined* en *DomainJoined* *Ja* bevatten.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Gebruikers kunnen geen SSO voor mijn NTLM-netwerk bron ophalen nadat ze zich hebben aangemeld met een FIDO2-beveiligings sleutel en een prompt voor referenties hebben ontvangen

Zorg ervoor dat er voldoende Dc's zijn geïnstalleerd om tijdig te reageren op uw resource aanvraag. Als u wilt controleren of u een server kunt zien waarop de functie wordt uitgevoerd, controleert u de uitvoer van `nltest /dsgetdc:<dc name> /keylist /kdc`

Als u een domein controller met deze functie kunt zien, is het wacht woord van de gebruiker mogelijk gewijzigd sinds de aanmelding of is er nog een probleem. Verzamel logboeken zoals beschreven in de volgende sectie voor het micro soft-ondersteunings team voor fout opsporing.

## <a name="troubleshoot"></a>Problemen oplossen

Er zijn twee gebieden om problemen op te lossen: [Windows-client problemen](#windows-client-issues)of [implementatie problemen](#deployment-issues).

### <a name="windows-client-issues"></a>Problemen met Windows-client

Voer de volgende stappen uit om gegevens te verzamelen die helpen bij het oplossen van problemen met het aanmelden bij Windows of het openen van on-premises resources van Windows 10-apparaten:

1. Open de app **feedback hub** . Zorg ervoor dat uw naam in de linkerbenedenhoek van de app staat en selecteer vervolgens **een nieuw feedback-item maken**.

    Kies voor het feedback-item type *probleem*.

1. Selecteer de categorie *beveiliging en privacy* en vervolgens de subcategorie *Fido* .
1. Schakel het selectie vakje in om *bijgevoegde bestanden en diagnostische gegevens naar micro soft te verzenden samen met mijn feedback*.
1. Selecteer *mijn problemen opnieuw maken* en vervolgens *vastleggen starten*.
1. Vergrendel en Ontgrendel de computer met de beveiligings sleutel FIDO2. Als het probleem zich voordoet, probeert u met andere referenties te ontgrendelen.
1. Ga terug naar de **feedback hub**, selecteer **Stop vastleggen** en verzend uw feedback.
1. Ga naar de pagina *feedback* en klik vervolgens op het tabblad *mijn feedback* . Selecteer uw laatst verzonden feedback.
1. Selecteer de knop *delen* in de rechter bovenhoek om een koppeling naar de feedback te krijgen. Neem deze koppeling op wanneer u een ondersteunings aanvraag opent of beantwoordt aan de engineer die is toegewezen aan een bestaande ondersteunings aanvraag.

De volgende gebeurtenis logboeken en informatie over register sleutels worden verzameld:

**Registersleutels**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [ \* ]*

**Diagnostische gegevens**

* Live kernel dump
* Informatie van AppX-pakket verzamelen
* Context bestanden UIF

**Gebeurtenislogboeken**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Implementatie problemen

Als u problemen met de implementatie van de Azure AD Kerberos-server wilt oplossen, gebruikt u de nieuwe Power shell-module die is opgenomen in Azure AD Connect.

#### <a name="viewing-the-logs"></a>De logboeken weer geven

De Power shell-cmdlets van de Azure AD Kerberos-server gebruiken dezelfde logboek registratie als de standaard-Azure AD Connect wizard. Voer de volgende stappen uit om informatie of fout Details van de cmdlets weer te geven:

1. Blader op de Azure AD Connect-server naar `C:\ProgramData\AADConnect\` . Deze map is standaard verborgen.
1. Open en Bekijk het meest recente `trace-*.log` bestand dat zich in de map bevindt.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>De Azure AD Kerberos-server objecten weer geven

Voer de volgende stappen uit om de Azure AD Kerberos-server objecten te bekijken en te controleren of ze in goede volg orde zijn:

1. Open Power shell op de Azure AD Connect-server en navigeer naar `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Voer de volgende Power shell-opdrachten uit om de Azure AD Kerberos-server van Azure AD en de on-premises AD DS weer te geven.

    Vervang *Corp.contoso.com* door de naam van uw on-premises AD DS domein.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

Met de opdracht worden de eigenschappen van de Azure AD Kerberos-server vanuit Azure AD en on-premises AD DS uitgevoerd. Controleer de eigenschappen om te controleren of alles in goede staat is. Gebruik de onderstaande tabel om de eigenschappen te controleren.

De eerste set eigenschappen is afkomstig uit de objecten in de on-premises AD DS omgeving. De tweede helft (de eigenschappen die beginnen met * Cloud * *) zijn van het Kerberos-server object in azure AD:

| Eigenschap           | Beschrijving  |
|--------------------|--------------|
| Id                 | De unieke *id* van het AD DS domein controller object. |
| DomainDnsName      | De DNS-domein naam van het AD DS domein. |
| ComputerAccount    | Het object computer account van het object van de Azure AD Kerberos-server (de domein controller). |
| User account        | Het uitgeschakelde gebruikers account-object dat de Azure AD Kerberos server TGT-versleutelings sleutel bevat. De DN-naam van dit account is *CN = krbtgt_AzureAD, CN = Users <domein-DN>* |
| Versie         | De sleutel versie van de TGT-versleutelings sleutel van de Azure AD Kerberos-server. De versie wordt toegewezen wanneer de sleutel wordt gemaakt. De versie wordt vervolgens verhoogd telkens wanneer de sleutel wordt gedraaid. De stappen zijn gebaseerd op meta gegevens van replicatie en zijn waarschijnlijk groter dan één.<br /><br /> De eerste *versie* kan bijvoorbeeld *192272* zijn. De eerste keer dat de sleutel wordt gedraaid, kan de versie worden voortgezet in *212621*.<br /><br /> Het is belang rijk om te controleren of de *versie* van het on-premises object en de *CloudKeyVersion* voor het Cloud object hetzelfde zijn. |
| KeyUpdatedOn       | De datum en tijd waarop de TGT-versleutelings sleutel van de Azure AD Kerberos-server is bijgewerkt of gemaakt. |
| KeyUpdatedFrom     | De domein controller waarop de TGT-versleutelings sleutel van de Azure AD Kerberos-server voor het laatst is bijgewerkt. |
| CloudId            | De *id* van het Azure AD-object. Moet overeenkomen met de bovenstaande *id* . |
| CloudDomainDnsName | De *DomainDnsName* van het Azure AD-object. Moet overeenkomen met de bovenstaande *DomainDnsName* . |
| CloudKeyVersion    | De *versie* van het Azure AD-object. Moet overeenkomen met de bovenstaande *versie* . |
| CloudKeyUpdatedOn  | De *KeyUpdatedOn* van het Azure AD-object. Moet overeenkomen met de bovenstaande *KeyUpdatedOn* . |

## <a name="next-steps"></a>Volgende stappen

Om aan de slag te gaan met FIDO2-beveiligings sleutels en hybride toegang tot on-premises bronnen, raadpleegt u de volgende artikelen:

* [FIDO2-beveiligingssleutels zonder wachtwoord](howto-authentication-passwordless-security-key.md)
* [Windows 10 zonder wachtwoord](howto-authentication-passwordless-security-key-windows.md)
* [On-premises zonder wachtwoord](howto-authentication-passwordless-security-key-on-premises.md)
