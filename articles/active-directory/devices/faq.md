---
title: Veelgestelde vragen over het beheer van apparaten in Azure Active Directory | Microsoft Docs
description: Veelgestelde vragen over het beheer van apparaten Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb468dba1156087bdeb6086f5bc24b214e576e18
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773297"
---
# <a name="azure-active-directory-device-management-faq"></a>Veelgestelde vragen over het beheer van apparaten Azure Active Directory

## <a name="general-faq"></a>Veelgestelde algemene vragen

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>V: Ik heb het apparaat onlangs geregistreerd. Waarom kan ik het apparaat niet zien onder mijn gebruikers gegevens in de Azure Portal? Of waarom is de eigenaar van het apparaat gemarkeerd als N/A voor Hybrid Azure Active Directory (Azure AD) gekoppelde apparaten?

**A:** Windows 10-apparaten die zijn toegevoegd aan hybride Azure AD worden niet weer gegeven onder **gebruikers apparaten**.
Gebruik de weer gave **alle apparaten** in de Azure Portal. U kunt ook een Power shell [Get-MsolDevice-](/powershell/module/msonline/get-msoldevice) cmdlet gebruiken.

Onder **gebruikers apparaten** worden alleen de volgende apparaten weer gegeven:

- Alle persoonlijke apparaten die geen lid zijn van hybride Azure AD. 
- Alle niet-Windows 10-of Windows Server 2016-apparaten.
- Alle niet-Windows-apparaten. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>V: Hoe kan ik weet ik wat de registratie status van het apparaat van de client is?

**A:** Ga in het Azure Portal naar **alle apparaten**. Zoek naar het apparaat met behulp van de apparaat-ID. Controleer de waarde onder de kolom Type samen voeging. Soms wordt het apparaat opnieuw ingesteld of teruggezet. Het is dus belang rijk om ook de registratie status van het apparaat op het apparaat te controleren:

- Voer uit voor Windows 10-en Windows Server 2016-apparaten of hoger `dsregcmd.exe /status` .
- Voer uit voor eerdere versies van het besturings systeem `%programFiles%\Microsoft Workplace Join\autoworkplace.exe` .

**A:** Raadpleeg de volgende artikelen voor informatie over het oplossen van problemen:
- [Problemen met apparaten oplossen met behulp van de dsregcmd-opdracht](troubleshoot-device-dsregcmd.md)
- [Problemen oplossen met hybride Azure Active Directory die zijn toegevoegd aan Windows 10-en Windows Server 2016-apparaten](troubleshoot-hybrid-join-windows-current.md)
- [Problemen oplossen met hybride Azure Active Directory-gekoppelde, downlevel apparaten](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>V: Ik zie de record apparaat onder de gebruikers gegevens in de Azure Portal. En ik zie de status geregistreerd op het apparaat. Ben ik correct ingesteld voor het gebruik van voorwaardelijke toegang?

**A:** De status van het lidmaatschap van apparaten, weer gegeven door **DeviceID**, moet overeenkomen met de status op Azure AD en voldoen aan de evaluatie criteria voor voorwaardelijke toegang. Zie voor meer informatie [beheerde apparaten vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>V: Waarom zien mijn gebruikers een fout bericht met de melding dat het apparaat is verwijderd door uw organisatie of omdat uw organisatie het apparaat heeft uitgeschakeld op hun Windows 10-apparaten?

**A:** Op Windows 10-apparaten die zijn gekoppeld aan of zijn geregistreerd bij Azure AD, worden gebruikers een [primair vernieuwings token (PRT)](concept-primary-refresh-token.md) uitgegeven waarmee eenmalige aanmelding mogelijk is. De geldigheid van de PRT is gebaseerd op de geldigheid van het apparaat zelf. Gebruikers zien dit bericht als het apparaat wordt verwijderd of uitgeschakeld in azure AD zonder dat de actie wordt gestart vanaf het apparaat zelf. Een apparaat kan in azure AD een van de volgende scenario's worden verwijderd of uitgeschakeld: 

- Gebruiker het apparaat uitschakelt vanuit de portal mijn apps. 
- Een beheerder (of gebruiker) verwijdert het apparaat uit het Azure Portal of schakelt het in met behulp van Power shell
- Alleen lid worden van hybride Azure AD: een beheerder verwijdert het bereik van de organisatie-eenheid uit de synchronisatie, waardoor de apparaten uit Azure AD worden verwijderd
- Upgrade uitvoeren van Azure AD Connect naar versie 1.4. xx. x. Meer [informatie over Azure AD Connect 1.4. xx. x en het weer geven van apparaten](../hybrid/reference-connect-device-disappearance.md).


Hieronder ziet u hoe deze acties kunnen worden verholpen.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>V: Ik heb mijn apparaat uitgeschakeld of verwijderd uit het Azure Portal of met behulp van Windows Power shell. Maar de lokale status op het apparaat geeft aan dat deze nog steeds is geregistreerd. Wat moet ik doen?

**A:** Deze bewerking is standaard. In dit geval heeft het apparaat geen toegang tot resources in de Cloud. Beheerders kunnen deze actie uitvoeren voor verouderde, verloren of gestolen apparaten om onbevoegde toegang te voor komen. Als deze actie per ongeluk is uitgevoerd, moet u het apparaat opnieuw inschakelen of registreren, zoals hieronder wordt beschreven

- Als het apparaat is uitgeschakeld in azure AD, kan een beheerder met voldoende machtigingen het inschakelen via de Azure AD-Portal  
  > [!NOTE]
  > Als u apparaten synchroniseert met Azure AD Connect, worden aan hybride Azure AD gekoppelde apparaten automatisch opnieuw ingeschakeld tijdens de volgende synchronisatie cyclus. Als u dus een hybride Azure AD-apparaat wilt uitschakelen, moet u dit uitschakelen in uw on-premises AD

 - Als het apparaat wordt verwijderd in azure AD, moet u het apparaat opnieuw registreren. Als u zich opnieuw wilt registreren, moet u een hand matige actie uitvoeren op het apparaat. Hieronder vindt u instructies voor het opnieuw registreren op basis van de Apparaatstatus. 

      Voer de volgende stappen uit om hybride Azure AD gekoppelde Windows 10-en Windows Server 2016/2019-apparaten opnieuw te registreren:

      1. Open de opdracht prompt als beheerder.
      1. Voer `dsregcmd.exe /debug /leave` in.
      1. Meld u af en meld u aan om de geplande taak te activeren waarmee het apparaat opnieuw wordt geregistreerd bij Azure AD. 

      Voer de volgende stappen uit voor versies van het oudere Windows-besturings systeem die zijn toegevoegd aan hybride Azure AD:

      1. Open de opdracht prompt als beheerder.
      1. Voer `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` in.
      1. Voer `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` in.

      Voer de volgende stappen uit voor Windows 10-apparaten die lid zijn van Azure AD:

      1. Open de opdracht prompt als beheerder
      1. Enter `dsregcmd /forcerecovery` (Opmerking: u moet een beheerder zijn om deze actie uit te voeren).
      1. Klik op aanmelden in het dialoog venster dat wordt geopend en ga door met het aanmeldings proces.
      1. Meld u af en meld u opnieuw aan om het herstel te volt ooien.

      Voer de volgende stappen uit voor Azure AD-geregistreerde Windows 10-apparaten:

      1. Ga naar **instellingen**  >  **accounts**  >  **toegang tot werk of school**. 
      1. Selecteer het account en selecteer **verbinding verbreken**.
      1. Klik op + verbinden en registreer het apparaat opnieuw door het aanmeldings proces te door lopen.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>V: Waarom zie ik dubbele apparaten in het Azure Portal?

**Één**

- Voor Windows 10 en Windows Server 2016 kunnen dubbele vermeldingen worden veroorzaakt door herhaalde pogingen om het apparaat te ontkoppelen en opnieuw samen te voegen. 
- Elke Windows-gebruiker die een **werk-of school account toevoegt** , maakt een nieuwe apparaat-record met dezelfde apparaatnaam.
- Voor eerdere versies van Windows-besturings systemen die zijn toegevoegd aan een on-premises Azure Directory-domein, maakt automatische registratie een nieuwe apparaat-record met dezelfde apparaatnaam voor elke domein gebruiker die zich bij het apparaat aanmeldt. 
- Een aan Azure AD gekoppelde computer die wordt gewist, opnieuw wordt geïnstalleerd en opnieuw wordt samengevoegd met dezelfde naam, wordt weer gegeven als een andere record met dezelfde apparaatnaam.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>V: registratie van Windows 10-apparaten in azure AD support Tpm's in de FIPS-modus?

**A:** Registratie van Windows 10-apparaten wordt alleen ondersteund voor FIPS-compatibele TPM 2,0 en niet ondersteund voor TPM 1,2. Als uw apparaten FIPS-compatibele TPM 1,2 hebben, moet u ze uitschakelen voordat u verdergaat met Azure AD-deelname of hybride Azure AD-deelname. Micro soft biedt geen hulpprogram ma's voor het uitschakelen van de FIPS-modus voor Tpm's omdat deze afhankelijk is van de TPM-fabrikant. Neem contact op met uw OEM voor ondersteuning. 

---

**V: Waarom kan een gebruiker nog steeds toegang krijgen tot bronnen vanaf een apparaat dat ik heb uitgeschakeld in de Azure Portal?**

**A:** Het duurt Maxi maal een uur voordat een intrekking wordt toegepast vanaf het moment dat het Azure AD-apparaat is gemarkeerd als uitgeschakeld.

>[!NOTE] 
>Voor geregistreerde apparaten wordt u aangeraden het apparaat te wissen om ervoor te zorgen dat gebruikers geen toegang hebben tot de resources. Zie [Wat is apparaat-inschrijving?](/mem/intune/user-help/use-managed-devices-to-get-work-done)voor meer informatie. 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>V: Waarom zijn er apparaten gemarkeerd als ' in behandeling ' in de kolom geregistreerd in het Azure Portal?

**A**: in behandeling geeft aan dat het apparaat niet is geregistreerd. Deze status geeft aan dat een apparaat is gesynchroniseerd met Azure AD Connect vanuit een on-premises AD en gereed is voor apparaatregistratie. Voor deze apparaten is het JOINTYPE ingesteld op ' Hybrid Azure AD joind '. Meer informatie over het [plannen van uw hybride Azure Active Directory deelname-implementatie](hybrid-azuread-join-plan.md).

>[!NOTE]
>Een apparaat kan ook overschakelen van een geregistreerde status naar in behandeling
>* Als een apparaat eerst uit Azure AD wordt verwijderd en opnieuw wordt gesynchroniseerd vanuit een on-premises AD.
>* Als een apparaat wordt verwijderd uit een synchronisatie bereik op Azure AD Connect en weer is toegevoegd.
>
>In beide gevallen moet u het apparaat hand matig opnieuw registreren op elk van deze apparaten. Als u wilt controleren of het apparaat eerder is geregistreerd, kunt u [problemen oplossen met behulp van de dsregcmd-opdracht](troubleshoot-device-dsregcmd.md).

---

### <a name="q-i-cannot-add-more-than-3-azure-ad-user-accounts-under-the-same-user-session-on-a-windows-10-device-why"></a>V: Ik kan niet meer dan 3 Azure AD-gebruikers accounts toevoegen onder dezelfde gebruikers sessie op een Windows 10-apparaat, waarom?

**A**: Azure AD heeft ondersteuning toegevoegd voor meerdere Azure AD-accounts in Windows 10 1803 release. Windows 10 beperkt het aantal Azure AD-accounts op een apparaat echter tot 3 om de grootte van token aanvragen te beperken en betrouw bare eenmalige aanmelding (SSO) in te scha kelen. Zodra 3 accounts zijn toegevoegd, zien gebruikers een fout voor volgende accounts. De aanvullende probleem informatie op het scherm fout geeft het volgende bericht weer waarin wordt aangegeven dat de bewerking voor het toevoegen van een account is geblokkeerd, omdat de account limiet is bereikt. 

---
## <a name="azure-ad-join-faq"></a>Veelgestelde vragen over Azure AD-deelname

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>V: Hoe kan ik een lid van een Azure AD-apparaat lokaal op het apparaat ontkoppelen?

**A:** Zorg ervoor dat u een offline lokale Administrator-account hebt of een off line domein hebt gemaakt voor zuivere Azure AD-apparaten. U kunt zich niet aanmelden met Azure AD-gebruikers referenties. Ga vervolgens naar **instellingen**  >  **accounts**  >  **toegang tot werk of school**. Selecteer uw account en selecteer **verbinding verbreken**. Volg de aanwijzingen en geef de referenties van de lokale beheerder op wanneer u hierom wordt gevraagd. Start het apparaat opnieuw op om het ontkoppelings proces te volt ooien.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>V: kunnen mijn gebruikers zich aanmelden bij Azure AD gekoppelde apparaten die worden verwijderd of uitgeschakeld in azure AD?

**A:** Klikt. Windows heeft een gebruikers naam en wacht woord in de cache waarmee gebruikers die zich eerder hebben aangemeld, snel toegang hebben tot het bureau blad, zelfs zonder netwerk verbinding. 

Wanneer een apparaat wordt verwijderd of uitgeschakeld in azure AD, is het niet bekend bij het Windows-apparaat. Gebruikers die zich hebben aangemeld, blijven toegang tot het bureau blad met de gebruikers naam en het wacht woord in de cache. Maar wanneer het apparaat wordt verwijderd of uitgeschakeld, hebben gebruikers geen toegang tot bronnen die worden beveiligd door voorwaardelijke toegang op basis van apparaten. 

Gebruikers die zich niet hebben aangemeld, hebben geen toegang tot het apparaat. Er zijn geen gebruikers naam en wacht woord in de cache ingeschakeld. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>V: kan een uitgeschakelde of verwijderde gebruiker aanmelden bij een aan Azure AD gekoppelde apparaten

**A:** Ja, maar alleen gedurende een beperkte periode. Wanneer een gebruiker wordt verwijderd of uitgeschakeld in azure AD, is dit niet onmiddellijk bekend bij het Windows-apparaat. Gebruikers die zich hebben aangemeld, hebben toegang tot het bureau blad met de gebruikers naam en het wacht woord in de cache. 

Normaal gesp roken is het apparaat in minder dan vier uur op de hoogte van de gebruikers status. Vervolgens blokkeert Windows deze gebruikers toegang tot het bureau blad. Wanneer de gebruiker wordt verwijderd of uitgeschakeld in azure AD, worden alle tokens ingetrokken. Ze hebben geen toegang tot resources. 

Verwijderde of uitgeschakelde gebruikers die zich niet eerder hebben aangemeld, hebben geen toegang tot een apparaat. Er zijn geen gebruikers naam en wacht woord in de cache ingeschakeld. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>V: Waarom hebben mijn gebruikers problemen op apparaten die aan Azure AD zijn toegevoegd na het wijzigen van de UPN?

**A:** Op dit moment worden UPN-wijzigingen niet volledig ondersteund op apparaten die zijn toegevoegd aan Azure AD. De verificatie met Azure AD mislukt dus nadat de UPN is gewijzigd. Als gevolg hiervan hebben gebruikers problemen met eenmalige aanmelding en voorwaardelijke toegang op hun apparaten. Op dit moment moeten gebruikers zich aanmelden bij Windows via de Tegel "andere gebruiker" met behulp van hun nieuwe UPN om dit probleem op te lossen. Er wordt momenteel gewerkt aan het adresseren van dit probleem. Gebruikers die zich aanmelden met Windows hello voor bedrijven, hebben echter niet te maken met dit probleem. 

UPN-wijzigingen worden ondersteund met update voor Windows 10 2004. Gebruikers op apparaten met deze update hebben geen problemen nadat ze hun Upn's hebben gewijzigd

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>V: mijn gebruikers kunnen geen printers zoeken op apparaten die zijn toegevoegd aan Azure AD. Hoe kan ik afdrukken vanaf deze apparaten inschakelen?

**A:** Zie [Windows Server hybride-Cloud afdrukken implementeren met pre-authenticatie](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)voor het implementeren van printers voor apparaten die zijn toegevoegd aan Azure AD. U hebt een on-premises Windows-Server nodig voor het implementeren van hybride Cloud afdrukken. Op dit moment is de Cloud afdruk service niet beschikbaar. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>V: Hoe kan ik verbinding maken met een extern Azure AD-apparaat dat is toegevoegd?

**A:** Zie [verbinding maken met een PC die lid is van extern Azure Active Directory](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>V: Waarom zien mijn gebruikers *dat ik hier geen toegang kan krijgen*?

**A:** Hebt u bepaalde regels voor voorwaardelijke toegang geconfigureerd om een specifieke Apparaatstatus te vereisen? Als het apparaat niet voldoet aan de criteria, worden gebruikers geblokkeerd en zien ze dat bericht. Controleer de beleids regels voor voorwaardelijke toegang. Zorg ervoor dat het apparaat voldoet aan de criteria om het bericht te vermijden.

---

### <a name="q-why-dont-some-of-my-users-get-azure-ad-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>V: waarom krijgen sommige van mijn gebruikers geen vragen over Azure AD-Multi-Factor Authentication op gekoppelde apparaten van Azure AD?

**A:** Een gebruiker kan een apparaat toevoegen aan of registreren bij Azure AD met behulp van Multi-Factor Authentication. Het apparaat wordt dan zelf een betrouw bare tweede factor voor die gebruiker. Wanneer dezelfde gebruiker zich aanmeldt bij het apparaat en een toepassing opent, beschouwt Azure AD het apparaat als een tweede factor. Hiermee kan die gebruiker naadloos toegang krijgen tot toepassingen zonder extra Multi-Factor Authentication prompts. 

Dit gedrag:

- Is van toepassing op aan Azure AD gekoppelde en Azure AD geregistreerde apparaten, maar niet voor hybride Azure AD-apparaten die zijn toegevoegd.
- Is niet van toepassing op een andere gebruiker die zich aanmeldt bij dat apparaat. Zodat alle andere gebruikers die toegang hebben tot dat apparaat, een Multi-Factor Authentication uitdaging krijgen. Vervolgens hebben ze toegang tot toepassingen waarvoor Multi-Factor Authentication nodig is.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>V: Waarom krijg ik een *gebruikers naam of wacht woord onjuist* voor een apparaat dat ik net heb toegevoegd aan Azure AD?

**A:** Veelvoorkomende redenen voor dit scenario zijn als volgt:

- De gebruikers referenties zijn niet meer geldig.
- Uw computer kan niet communiceren met Azure Active Directory. Controleer of er problemen zijn met de netwerk verbinding.
- Voor federatieve aanmeldingen moet uw Federatie server ondersteuning bieden voor WS-Trust-eind punten die zijn ingeschakeld en toegankelijk zijn. 
- U hebt Pass-Through-verificatie ingeschakeld. Daarom moet uw tijdelijke wacht woord worden gewijzigd wanneer u zich aanmeldt.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>V: Waarom krijg ik de vraag *... Er is een fout opgetreden.* dialoog venster wanneer ik Azure AD wil toevoegen aan mijn PC?

**A:** Deze fout treedt op wanneer u Azure Active Directory inschrijving met intune instelt. Zorg ervoor dat de juiste intune-licentie is toegewezen aan de gebruiker die een Azure AD-lid probeert te maken. Zie [registratie instellen voor Windows-apparaten](/intune/windows-enroll)voor meer informatie.  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>V: Waarom is mijn poging tot Azure AD lid van een computer mislukt, maar ik heb geen fout informatie ontvangen?

**A:** Een waarschijnlijke oorzaak is dat u zich bij het apparaat hebt aangemeld met het lokale ingebouwde Administrator-account. Maak een ander lokaal account voordat u Azure Active Directory toevoegen gebruikt om de installatie te volt ooien. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What zijn de MS-organisatie-P2P-Access-certificaten aanwezig op onze Windows 10-apparaten?

**A:** De MS-organisatie-P2P-Access-certificaten worden door Azure AD aan zowel Azure AD toegevoegd als aan hybride Azure AD gekoppelde apparaten verleend. Deze certificaten worden gebruikt om vertrouwen in te scha kelen tussen apparaten in dezelfde Tenant voor scenario's voor extern bureau blad. Er wordt één certificaat uitgegeven aan het apparaat en het wordt aan de gebruiker uitgegeven. Het certificaat van het apparaat is aanwezig in `Local Computer\Personal\Certificates` en is één dag geldig. Dit certificaat wordt vernieuwd (door een nieuw certificaat uit te geven) als het apparaat nog steeds actief is in azure AD. Het gebruikers certificaat is aanwezig in `Current User\Personal\Certificates` en dit certificaat is ook voor één dag geldig, maar het wordt op aanvraag verleend wanneer een gebruiker een extern bureau blad-sessie probeert te maken met een ander Azure AD-apparaat. Het wordt niet vernieuwd op de verval datum. Beide certificaten worden uitgegeven met behulp van het MS-organisatie-P2P-Access-certificaat dat aanwezig is in de `Local Computer\AAD Token Issuer\Certificates` . Dit certificaat wordt door Azure AD verleend tijdens het registreren van het apparaat. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why worden er meerdere verlopen certificaten weer gegeven die zijn uitgegeven door MS-organisatie-P2P-Access op onze Windows 10-apparaten? Hoe kan ik deze verwijderen?

**A:** Er is een probleem vastgesteld in Windows 10 versie 1709 en lager waar verlopen MS-organisatie-P2P-Access-certificaten in het archief van de computer blijven bestaan vanwege cryptografische problemen. Uw gebruikers kunnen problemen met de netwerk verbinding ondervinden als u VPN-clients gebruikt (bijvoorbeeld Cisco AnyConnect) die het grote aantal verlopen certificaten niet kunnen verwerken. Dit probleem is opgelost in Windows 10 1803-release om dergelijke verlopen MS-organisatie-P2P-Access-certificaten automatisch te verwijderen. U kunt dit probleem oplossen door uw apparaten bij te werken naar Windows 10 1803. Als u niet kunt bijwerken, kunt u deze certificaten verwijderen zonder dat dit nadelige gevolgen hebben.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Veelgestelde vragen over hybride Azure AD-deelname

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>V: Hoe kan ik een lidmaatschap van een hybride Azure AD-apparaat lokaal op het apparaat ontkoppelen?

**A:** Voor hybride Azure AD gekoppelde apparaten schakelt u automatische registratie uit. Vervolgens wordt het apparaat niet opnieuw geregistreerd door de geplande taak. Open vervolgens een opdracht prompt als beheerder en voer in `dsregcmd.exe /debug /leave` . Of voer deze opdracht uit als een script op meerdere apparaten, zodat deze bulksgewijs kan worden samengevoegd.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>V: waar vind ik informatie over het oplossen van problemen met het opsporen van fouten in hybride deelname aan Azure AD?

**A:** Raadpleeg de volgende artikelen voor informatie over het oplossen van problemen:

- [Problemen oplossen met hybride Azure Active Directory die zijn toegevoegd aan Windows 10-en Windows Server 2016-apparaten](troubleshoot-hybrid-join-windows-current.md)
- [Problemen oplossen met hybride Azure Active Directory-gekoppelde, downlevel apparaten](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>V: Waarom zie ik een duplicaat van een Azure AD-geregistreerde record voor mijn Windows 10 Hybrid Azure AD aangesloten apparaat in de lijst met Azure AD-apparaten?

**A:** Wanneer uw gebruikers hun accounts toevoegen aan apps op een apparaat dat lid is van een domein, kan het worden gevraagd **om een account toe te voegen aan Windows?** Als ze **Ja** op de prompt invoeren, wordt het apparaat geregistreerd bij Azure AD. Het vertrouwens type is gemarkeerd als Azure AD geregistreerd. Nadat u hybride Azure AD join hebt ingeschakeld in uw organisatie, wordt het apparaat ook toegevoegd aan hybride Azure AD. Vervolgens worden twee Apparaatstatus weer gegeven voor hetzelfde apparaat. 

In de meeste gevallen heeft hybride Azure AD join prioriteit boven de geregistreerde Azure AD-status, wat betekent dat uw apparaat wordt beschouwd als hybride Azure AD die is gekoppeld aan een evaluatie van de verificatie en voorwaardelijke toegang. Soms kan deze dubbele status echter leiden tot een niet-deterministische evaluatie van het apparaat en toegangs problemen veroorzaken. U wordt ten zeerste geadviseerd om een upgrade uit te voeren naar Windows 10 versie 1803 en hoger, waarbij de geregistreerde Azure AD-status automatisch wordt opgeschoond. Informatie over het voor [komen of opschonen van deze dubbele status op de Windows 10-computer](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>V: Waarom hebben mijn gebruikers problemen ondervinden op Windows 10 Hybrid Azure AD gekoppelde apparaten na het wijzigen van de UPN?

**A:** Momenteel worden UPN-wijzigingen niet volledig ondersteund met hybride Azure AD gekoppelde apparaten. Hoewel gebruikers zich kunnen aanmelden bij het apparaat en toegang hebben tot hun lokale toepassingen, mislukt de verificatie met Azure AD nadat een UPN is gewijzigd. Als gevolg hiervan hebben gebruikers problemen met eenmalige aanmelding en voorwaardelijke toegang op hun apparaten. Op dit moment moet u het apparaat uit Azure AD ontkoppelen (Voer ' dsregcmd/leave ' uit met verhoogde bevoegdheden) en opnieuw samen voegen (treedt automatisch op) om het probleem op te lossen. Er wordt momenteel gewerkt aan het adresseren van dit probleem. Gebruikers die zich aanmelden met Windows hello voor bedrijven, hebben echter niet te maken met dit probleem. 

UPN-wijzigingen worden ondersteund met update voor Windows 10 2004. Gebruikers op apparaten met deze update hebben geen problemen nadat ze hun Upn's hebben gewijzigd

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>V: Windows 10 Hybrid Azure AD gekoppelde apparaten vereisen een gezichts lijn voor de domein controller om toegang te krijgen tot bronnen in de Cloud?

**A:** Nee, behalve wanneer het wacht woord van de gebruiker wordt gewijzigd. Nadat de Azure AD-deelname van Windows 10 is voltooid en de gebruiker ten minste eenmaal is aangemeld, is er geen regel voor het apparaat nodig om toegang te krijgen tot Cloud bronnen voor de domein controller. Met Windows 10 kunt u eenmalige aanmelding voor Azure AD-toepassingen vanaf elke locatie met een Internet verbinding krijgen, behalve wanneer een wacht woord wordt gewijzigd. Gebruikers die zich aanmelden met Windows hello voor bedrijven, blijven eenmalige aanmelding voor Azure AD-toepassingen ontvangen, zelfs nadat een wacht woord is gewijzigd, zelfs als ze niet over een regel beschikken voor hun domein controller. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>V: wat gebeurt er als een gebruiker het wacht woord wijzigt en probeert zich aan te melden bij hun aangesloten Windows 10 Hybrid Azure AD-apparaat buiten het bedrijfs netwerk?

**A:** Als een wacht woord buiten het bedrijfs netwerk wordt gewijzigd (bijvoorbeeld met behulp van Azure AD SSPR), zal de gebruiker zich niet aanmelden met het nieuwe wacht woord. On-premises Active Directory is de primaire instantie voor hybride Azure AD-gekoppelde apparaten. Wanneer een apparaat geen regel voor de detectie van de domein controller heeft, kan het nieuwe wacht woord niet worden gevalideerd. Daarom moet de gebruiker verbinding maken met de domein controller (via VPN of in het bedrijfs netwerk) voordat ze zich met het nieuwe wacht woord kunnen aanmelden bij het apparaat. Als dat niet het geval is, kunnen ze zich alleen aanmelden met hun oude wacht woord, omdat de aanmeldings mogelijkheden in de cache in Windows zijn opgeslagen. Het oude wacht woord wordt echter ongeldig gemaakt door Azure AD tijdens token aanvragen en daarom wordt voor komen dat door eenmalige aanmelding en op apparaten gebaseerd beleid voor voorwaardelijke toegang mislukt. Dit probleem treedt niet op als u Windows hello voor bedrijven gebruikt. 

---

## <a name="azure-ad-register-faq"></a>Veelgestelde vragen over Azure AD-REGI ster

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>V: Hoe kan ik een Azure AD-status geregistreerd voor een apparaat lokaal te verwijderen?

**Één** 
- Voor Windows 10 geregistreerde Azure ad-apparaten gaat u naar **instellingen**  >  **accounts**  >  **toegang tot werk of school**. Selecteer uw account en selecteer **verbinding verbreken**. Apparaatregistratie is per gebruikers profiel in Windows 10.
- Voor IOS en Android kunt u het apparaat voor de Microsoft Authenticator toepassings **instellingen** gebruiken en de registratie  >   **van apparaat** opheffen selecteren.
- Voor macOS kunt u de Microsoft Intune bedrijfsportal toepassing gebruiken om de inschrijving van het apparaat bij beheer ongedaan te maken en alle registraties te verwijderen. 

Voor Windows 10-apparaten kan dit proces worden geautomatiseerd met het [hulp programma voor het verwijderen van Workplace join (WPJ)](https://download.microsoft.com/download/8/e/f/8ef13ae0-6aa8-48a2-8697-5b1711134730/WPJCleanUp.zip)

> [!NOTE]
> Met dit hulp programma worden alle SSO-accounts op het apparaat verwijderd. Na deze bewerking worden alle toepassingen de SSO-status kwijt geraakt en wordt het apparaat niet Inge schreven bij beheer hulpprogramma's (MDM) en wordt de registratie van de Cloud ongedaan gemaakt. De volgende keer dat een toepassing zich probeert aan te melden, wordt gebruikers gevraagd het account opnieuw toe te voegen.

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>V: hoe kan ik voor komen dat gebruikers extra werk accounts toevoegen (geregistreerd voor Azure AD) op mijn bedrijfs Windows 10-apparaten?

**A:** Schakel het volgende REGI ster in om ervoor te zorgen dat uw gebruikers geen extra werk accounts meer kunnen toevoegen aan uw bedrijfs domein, lid worden van Azure AD of aan hybride Azure AD gekoppelde Windows 10-apparaten. Dit beleid kan ook worden gebruikt om computers die lid zijn van een domein te blok keren om per ongeluk Azure AD te ontvangen die is geregistreerd met hetzelfde gebruikers account. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>V: kan ik Android-of iOS BYOD-apparaten registreren?

**A:** Ja, maar alleen bij de registratie service voor Azure-apparaten en voor hybride klanten. Het wordt niet ondersteund door de on-premises Device Registration service in Active Directory Federation Services (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>V: hoe kan ik een macOS-apparaat registreren?

**A:** Voer de volgende stappen uit:

1.    [Een nalevings beleid maken](/intune/compliance-policy-create-mac-os)
1.    [Beleid voor voorwaardelijke toegang voor macOS-apparaten definiëren](../conditional-access/overview.md) 

**Opmerkingen**

- De gebruikers die deel uitmaken van het beleid voor voorwaardelijke toegang, hebben een [ondersteunde versie van Office nodig voor macOS](../conditional-access/concept-conditional-access-conditions.md) om toegang te krijgen tot bronnen. 
- Tijdens de eerste keer dat u toegang probeert te krijgen, wordt uw gebruikers gevraagd het apparaat te registreren via de bedrijfs portal.

---
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [bij Azure AD geregistreerde apparaten](concept-azure-ad-register.md)
- Meer informatie over [aan Azure AD gekoppelde apparaten](concept-azure-ad-join.md)
- Meer informatie over [aan hybride Azure AD-gekoppelde apparaten](concept-azure-ad-join-hybrid.md)
