---
title: Azure MFA server gebruiken met AD FS 2,0-Azure Active Directory
description: Dit is de Azure Multi-Factor Authentication-pagina waarop wordt beschreven hoe u met Azure MFA en AD FS 2.0 aan de slag kunt gaan.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13cfed6bf378ff9dacc4b6446cdfc246a87949bb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838788"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Azure Multi-Factor Authentication-server configureren om met AD FS 2.0 te werken

Dit artikel is voor organisaties die zijn gefedereerd met Azure Active Directory en resources die on-premises of in de cloud zijn, willen beveiligen. Beveilig uw resources met de Azure Multi-Factor Authentication-server en configureer de service zodanig dat deze met AD FS werkt en verificatie in twee stappen wordt geactiveerd voor waardevolle eindpunten.

In deze documentatie wordt beschreven hoe u de Azure Multi-Factor Authentication-server gebruikt met AD FS 2.0. Voor meer informatie over AD FS raadpleegt u [Uw cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server met AD FS in Windows Server 2012 R2](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Als u op de cloud gebaseerde MFA gebruikt, raadpleegt u [cloud resources beveiligen met Azure AD multi-factor Authentication en AD FS](howto-mfa-adfs.md).
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

## <a name="secure-ad-fs-20-with-a-proxy"></a>AD FS 2.0 beveiligen met een proxy

Als u AD FS 2.0 wilt beveiligen met een proxy, installeert u de Multi-Factor Authentication-server op de ADFS-proxyserver.

### <a name="configure-iis-authentication"></a>IIS-verificatie configureren

1. Klik op de Azure Multi-Factor Authentication-server in het menu links op het pictogram **IIS-verificatie**.
2. Klik op het tabblad **op formulier gebaseerd** .
3. Klik op **Add**.

   ![Venster voor IIS-verificatie van MFA-server](./media/howto-mfaserver-adfs-2/setup1.png)

4. Als u de gebruikers naam, het wacht woord en de domein variabelen automatisch wilt detecteren, voert u de aanmeldings-URL (bijvoorbeeld `https://sso.contoso.com/adfs/ls` ) in het dialoog venster Form-Based website automatisch configureren in en klikt u op **OK**.
5. Schakel het selectievakje **Overeenkomende Azure Multi-Factor Authentication-gebruiker** vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan verificatie in twee stappen onderworpen zijn. Als een groot aantal gebruikers nog niet is geïmporteerd in de Azure Multi-Factor Authentication-server en/of vrijgesteld zal zijn van verificatie in twee stappen, laat u het vakje uitgeschakeld.
6. Als de pagina variabelen niet automatisch kunnen worden gedetecteerd, klikt u op **hand matig opgeven...** in het dialoogvenster Formulier-gebaseerde website automatisch configureren.
7. Voer in het dialoog venster Form-Based website toevoegen de URL naar de AD FS aanmeldings pagina in het veld indienings-URL in (zoals `https://sso.contoso.com/adfs/ls` ) en voer een toepassings naam in (optioneel). De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
8. Stel de indeling van de aanvraag in op **POST of GET**.
9. Voer de gebruikersnaamvariabele (ctl00$ContentPlaceHolder1$UsernameTextBox) en wachtwoordvariabele (ctl00$ContentPlaceHolder1$PasswordTextBox) in. Als uw formulier-gebaseerde aanmeldingspagina een tekstvak voor een domein bevat, voert u de domeinvariabele in. Ga in een webbrowser naar de aanmeldingspagina, klik met de rechtermuisknop op de pagina en selecteer **Bron weergeven** om de namen van de invoervakken op de aanmeldingspagina te vinden.
10. Schakel het selectievakje **Overeenkomende Azure Multi-Factor Authentication-gebruiker** vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan verificatie in twee stappen onderworpen zijn. Als een groot aantal gebruikers nog niet is geïmporteerd in de Azure Multi-Factor Authentication-server en/of vrijgesteld zal zijn van verificatie in twee stappen, laat u het vakje uitgeschakeld.

    ![Formulier-gebaseerde website toevoegen aan MFA-server](./media/howto-mfaserver-adfs-2/manual.png)

11. Klik op **Geavanceerd...** om de geavanceerde instellingen te controleren. U kunt onder meer de volgende instellingen configureren:

    - Een aangepast bestand voor de weigeringspagina selecteren
    - Succesvolle verificaties voor de website in de cache opslaan met behulp van cookies
    - Selecteren hoe u de primaire referenties wilt verifiëren

12. Aangezien de AD FS-proxyserver waarschijnlijk niet aan het domein zal worden gekoppeld, kunt u LDAP gebruiken om verbinding te maken met de domeincontroller voor het importeren van gebruikers en pre-authenticatie. Klik in het dialoogvenster Geavanceerde op formulier-gebaseerde website op het tabblad **Primaire authenticatie** en selecteer **LDAP-binding** voor het authenticatietype Pre-authenticatie.
13. Als u klaar bent, klikt u op **OK** om terug te gaan naar het dialoogvenster Formulier-gebaseerde website.
14. Klik op **OK** om het dialoogvenster te sluiten.
15. Zodra de URL- en paginavariabelen zijn gedetecteerd of ingevoerd, worden de websitegegevens weergegeven in het paneel Op formulier gebaseerd.
16. Klik op het tabblad **systeem eigen module** en selecteer de server, de website waaronder de AD FS-proxy wordt uitgevoerd (zoals ' standaard website '), of de AD FS proxy toepassing (zoals ' ls ' onder ' ADFS ') om de IIS-plug-in op het gewenste niveau in te scha kelen.
17. Klik op het vak **IIS-verificatie** inschakelen aan de bovenkant van het scherm.

De IIS-authenticatie is nu ingeschakeld.

### <a name="configure-directory-integration"></a>Adreslijstintegratie configureren

U hebt IIS-authenticatie ingeschakeld, maar om de pre-authenticatie voor uw Active Directory (AD) via LDAP uit te voeren, moet u de LDAP-verbinding configureren met de domeincontroller.

1. Kik op het pictogram **Adreslijstintegratie**.
2. Schakel op het tabblad instellingen het keuze rondje **specifieke LDAP-configuratie gebruiken** in.

   ![LDAP-instellingen voor specifieke LDAP-instellingen configureren](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Klik op **Bewerken**.
4. Vul in de velden van het dialoogvenster LDAP-configuratie bewerken de benodigde informatie in om verbinding te maken met de AD-domeincontroller. Beschrijvingen van de velden zijn ook beschikbaar in het Help-bestand voor de Azure Multi-Factor Authentication-server.
5. Test de LDAP-verbinding door te klikken op de knop **testen** .

   ![LDAP-configuratie testen in MFA-server](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Als de LDAP-verbindingstest is geslaagd, klikt u op **OK**.

### <a name="configure-company-settings"></a>Instellingen van het bedrijf configureren

1. Klik vervolgens op het pictogram **bedrijfs instellingen** en selecteer het tabblad **gebruikers naam omzetting** .
2. Selecteer het keuze rondje **unieke LDAP-id gebruiken voor overeenkomende gebruikers namen** .
3. Als gebruikers hun gebruikers naam invoeren in de indeling domein\gebruikersnaam, moet de server het domein van de gebruikers naam kunnen verwijderen bij het maken van de LDAP-query. Dat kan worden gedaan met behulp van een registerinstelling.
4. Open de Register-editor en ga op een 64-bits-server naar HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor. Als u op een 32-bits-server, moet u de "Wow6432Node" uit het pad halen. Maak een DWORD-register sleutel met de naam UsernameCxz_stripPrefixDomain en stel de waarde in op 1. De AD FS-proxy wordt nu beveiligd met Azure Multi-Factor Authentication.

Zorg ervoor dat gebruikers uit Active Directory in de server zijn geïmporteerd. Zie de [sectie betrouw bare ip's](#trusted-ips) als u interne IP-adressen wilt toestaan zodat verificatie in twee stappen niet is vereist bij het aanmelden bij de website vanaf die locaties.

![REGI ster-editor voor het configureren van bedrijfs instellingen](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 Direct zonder een proxy

U kunt AD FS beveiligen wanneer de AD FS-proxy niet wordt gebruikt. Installeer de Multi-Factor Authentication-server op de ADFS-server en configureer de server door de volgende stappen uit te voeren:

1. Klik in de Azure-Multi-Factor Authentication-server op het pictogram **IIS-verificatie** in het menu links.
2. Klik op het tabblad **http** .
3. Klik op **Add**.
4. Voer in het dialoog venster basis-URL toevoegen in het veld basis-URL de URL in voor de AD FS website waar de HTTP-verificatie wordt uitgevoerd (zoals `https://sso.domain.com/adfs/ls/auth/integrated` ). Voer dan een toepassingsnaam in (optioneel). De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
5. Pas, indien gewenst, de tijd voor Time-out voor inactiviteit en voor Maximale sessie aan.
6. Schakel het selectievakje **Overeenkomende Azure Multi-Factor Authentication-gebruiker** vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan verificatie in twee stappen onderworpen zijn. Als een groot aantal gebruikers nog niet is geïmporteerd in de Azure Multi-Factor Authentication-server en/of vrijgesteld zal zijn van verificatie in twee stappen, laat u het vakje uitgeschakeld.
7. Schakel, indien gewenst, het selectievakje Cookie gebruiken om voltooide authenticaties in de cache op te slaan in.

   ![AD FS 2.0 Direct zonder een proxy](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Klik op **OK**.
9. Klik op het tabblad **systeem eigen module** en selecteer de server, de website (zoals ' standaard website ') of de AD FS toepassing (zoals ' ls ' onder ' ADFS ') om de IIS-plug-in op het gewenste niveau in te scha kelen.
10. Klik op het vak **IIS-verificatie** inschakelen aan de bovenkant van het scherm.

AD FS wordt nu beveiligd met Azure Multi-Factor Authentication.

Zorg ervoor dat gebruikers uit Active Directory in de server zijn geïmporteerd. Zie de sectie betrouw bare Ip's als u interne IP-adressen wilt toestaan zodat verificatie in twee stappen niet is vereist bij het aanmelden bij de website vanaf die locaties.

## <a name="trusted-ips"></a>Goedgekeurde IP-adressen

De goedgekeurde IP-adressen bieden gebruikers de mogelijkheid om Azure Multi-Factor Authentication over te slaan voor websiteverzoeken die afkomstig zijn van bepaalde IP-adressen of subnetten. Als u bijvoorbeeld gebruikers wilt uitsluiten van de verificatie in twee stappen wanneer ze zich aanmelden vanaf het kantoor. Hiervoor geeft u het subnet van de werkplek op als een goedgekeurd IP-adres.

### <a name="to-configure-trusted-ips"></a>Goedgekeurde IP-adressen configureren

1. Klik in de sectie IIS-verificatie op het tabblad **vertrouwde IP-adressen** .
2. Klik op de knop **Toevoegen...** te klikken.
3. Wanneer het dialoogvenster Goedgekeurd IP-adres toevoegen wordt weergegeven, selecteert u het keuzerondje **Eén IP-adres**, **IP-bereik** of **Subnet**.
4. Voer het IP-adres, het bereik van de IP-adressen of het subnet in dat moet worden toegestaan. Als u een subnet wilt invoeren, selecteert u het juiste netmasker en klikt u op de knop **OK** .

![Vertrouwde IP-adressen configureren voor MFA-server](./media/howto-mfaserver-adfs-2/trusted.png)
