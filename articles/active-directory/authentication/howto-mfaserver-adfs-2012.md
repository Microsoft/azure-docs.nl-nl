---
title: Azure MFA-server met AD FS in Windows Server-Azure Active Directory
description: In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Multi-Factor Authentication en AD FS in Windows Server 2012 R2 en 2016.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fd1bf4f9c463973d70f5289f7a82f372d0156cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742542"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Azure Multi-Factor Authentication-server configureren om met AD FS in Windows Server te werken

Als u gebruikmaakt van Active Directory Federation Services (AD FS) en u cloud- of on-premises resources wilt beveiligen, kunt u Azure Multi-Factor Authentication Server configureren voor gebruik met AD FS. Deze configuratie activeert verificatie in twee stappen voor waardevolle eindpunten.

In dit artikel wordt besproken hoe u de Azure Multi-Factor Authentication-server gebruikt met AD FS in Windows Server 2012 R2 of Windows Server 2016. Lees over het [beveiligen van cloudresources en on-premises resources met behulp van de Azure Multi-Factor Authentication-server met AD FS 2.0](howto-mfaserver-adfs-2.md) voor meer informatie.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Als u op de cloud gebaseerde MFA gebruikt, raadpleegt u [cloud resources beveiligen met Azure AD multi-factor Authentication en AD FS](howto-mfa-adfs.md).
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Windows Server AD FS beveiligen met Azure Multi-Factor Authentication-server

Bij de installatie van de Azure Multi-Factor Authentication-server hebt u de volgende opties:

* De Azure Multi-Factor Authentication-server lokaal op dezelfde server installeren als AD FS
* De Azure Multi-Factor Authentication-adapter lokaal installeren op de AD FS-server en de Multi-Factor Authentication-server op een andere computer installeren

Houd rekening met de volgende informatie voordat u begint:

* U hoeft geen Azure Multi-Factor Authentication-server te installeren op uw AD FS-server. U moet echter de Multi-Factor Authentication-adapter voor AD FS installeren op een Windows Server 2012 R2 of Windows Server 2016 met AD FS. U kunt de server op een andere computer installeren als u de AD FS-adapter afzonderlijk installeert op uw federatieve AD FS-server. Zie de volgende procedures voor informatie over hoe u de adapter afzonderlijk kunt installeren.
* Als uw organisatie verificatiemethoden voor sms-berichten of mobiele apps gebruikt, bevatten de tekenreeksen die in Bedrijfsinstellingen zijn gedefinieerd een tijdelijke aanduiding, <$*toepassingsnaam*$>. In MFA Server v7.1 kunt u de naam van een toepassing opgeven die wordt gebruikt in plaats van deze tijdelijke aanduiding. In v7.0 of ouder wordt deze tijdelijke aanduiding niet automatisch vervangen wanneer u de AD FS-adapter gebruikt. Voor deze oudere versies wordt aangeraden de tijdelijke aanduiding te verwijderen uit de relevante tekenreeksen als u AD FS beveiligt.
* Het account waarmee u zich aanmeldt, moet gebruikersrechten hebben voor het maken van beveiligingsgroepen in de Active Directory-service.
* De wizard Multi-Factor Authentication AD FS-adapter installeren maakt een beveiligingsgroep met de naam PhoneFactor Admins in uw exemplaar van Active Directory. Vervolgens wordt het AD FS-serviceaccount van uw federatieve service toegevoegd aan deze groep. Controleer of de groep PhoneFactor Admins inderdaad is gemaakt op uw domeincontroller en of het AD FS-serviceaccount lid is van deze groep. Voeg, indien nodig, het AD FS-serviceaccount handmatig toe aan de groep PhoneFactor Admins in uw domeincontroller.
* Zie [De gebruikersportal implementeren voor de Azure Multi-Factor Authentication-server](howto-mfaserver-deploy-userportal.md) voor informatie over het installeren van de webservice-SDK met de gebruikersportal.

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>De Azure Multi-Factor Authentication-server lokaal op de AD FS-server installeren

1. Download en installeer de Azure Multi-Factor Authentication-server op uw AD FS-server. Lees [Aan de slag met de Azure Multi-Factor Authentication-server](howto-mfaserver-deploy.md) voor informatie over de installatie.
2. Klik in de beheerconsole van de Azure Multi-Factor Authentication-server op het pictogram **AD FS**. Selecteer de opties **Registreren van gebruikers toestaan** en **Toestaan dat gebruikers de methode selecteren**.
3. Selecteer de aanvullende opties die u wilt opgeven voor uw organisatie.
4. Klik op **AD FS adapter installeren**.

   ![De ADFS-adapter installeren via de MFA-Server console](./media/howto-mfaserver-adfs-2012/server.png)

5. Als het Active Directory-venster wordt weergegeven, betekent dit twee dingen. Uw computer is lid van een domein en de Active Directory-configuratie voor de beveiliging van de communicatie tussen de AD FS-adapter en de Multi-Factor Authentication-service is niet voltooid. Klik op **Volgende** om deze configuratie automatisch te voltooien of schakel het selectievakje **Automatische Active Directory-configuratie overslaan en instellingen handmatig configureren** in. Klik op **Volgende**.
6. Als het venster Lokale groep wordt weergegeven, betekent dit twee dingen. Uw computer is geen lid van een domein en de configuratie van de lokale groep voor de beveiliging van de communicatie tussen de AD FS-adapter en de Multi-Factor Authentication-service is niet voltooid. Klik op **Volgende** om deze configuratie automatisch te voltooien of schakel het selectievakje **Automatische lokale groep-configuratie overslaan en instellingen handmatig configureren** in. Klik op **Volgende**.
7. Klik in de installatiewizard op **Volgende**. Azure Multi-Factor Authentication-server maakt de PhoneFactor Admins-groep en voegt de AD FS-serviceaccount toe aan de PhoneFactor Admins-groep.
8. Klik op de pagina **Installatieprogramma uitvoeren** op **Volgende**.
9. Klik in het installatieprogramma voor de Multi-Factor Authentication AD FS-adapter op **Volgende**.
10. Klik op **Sluiten** nadat de installatie is voltooid.
11. Wanneer de adapter is geïnstalleerd, moet u deze registreren bij AD FS. Open Windows PowerShell en voer de volgende opdracht uit:

    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`

12. Bewerk het algemene authenticatiebeleid in AD FS om de zojuist geregistreerde adapter te gebruiken. Ga in de AD FS-beheerconsole naar het knooppunt **Authentication Policies**. Klik in het gedeelte **Multi-factor Authentication** op de koppeling **Edit** naast het gedeelte **Global Settings**. Selecteer in het venster **Edit Global Authentication Policy****Multi-Factor Authentication** als een aanvullende verificatiemethode en klik op **OK**. De adapter wordt geregistreerd als WindowsAzureMultiFactorAuthentication. Start de AD FS-service opnieuw op voordat de registratie van kracht wordt.

![Algemeen authenticatie beleid bewerken](./media/howto-mfaserver-adfs-2012/global.png)

De Multi-Factor Authentication-server is nu ingesteld voor gebruik als een extra verificatieprovider voor gebruik met AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Een zelfstandig exemplaar van de AD FS-adapter installeren met behulp van de webservice-SDK

1. Installeer de webservice-SDK op de server waarop de Multi-Factor Authentication-server wordt uitgevoerd.
2. Kopieer de volgende bestanden uit de map \Program Files\Multi-Factor Authentication Server naar de server waarop u de AD FS-adapter wilt installeren:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Voer het installatiebestand MultiFactorAuthenticationAdfsAdapterSetup64.msi uit.
4. Klik in het installatieprogramma van de AD FS-adapter van Multi-Factor Authentication op **Volgende** om de installatie te starten.
5. Klik op **Sluiten** nadat de installatie is voltooid.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Bewerk het bestand MultiFactorAuthenticationAdfsAdapter.config

Volg deze stappen om het bestand MultiFactorAuthenticationAdfsAdapter.config te bewerken:

1. Stel het knooppunt **UseWebServiceSdk** in op **true**.  
2. Stel de waarde voor **WebServiceSdkUrl** in op de URL van de webservice-SDK voor Multi-Factor Authentication. Bijvoorbeeld: *https: \/ \/ contoso.com/ \<certificatename> /multifactorauthwebservicessdk/pfwssdk.asmx*, waarbij *\<certificatename>* de naam is van uw certificaat.  
3. Bewerk het Register-MultiFactorAuthenticationAdfsAdapter.ps1 script door toe `-ConfigurationFilePath &lt;path&gt;` te voegen aan het einde van de `Register-AdfsAuthenticationProvider` opdracht, waarbij *&lt; pad &gt;* het volledige pad naar het MultiFactorAuthenticationAdfsAdapter.config bestand is.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>De webservice-SDK configureren met een gebruikersnaam en wachtwoord

Er zijn twee opties voor het configureren van de webservice-SDK. De eerste is met een gebruikersnaam en wachtwoord, de tweede is met een clientcertificaat. Volg deze stappen voor de eerste optie of sla dit gedeelte over voor de tweede optie.  

1. Stel de waarde voor **webservicesdkusername in** in op een account dat lid is van de beveiligings groep Phone factor admins. Gebruik de indeling &lt;domein&gt;&#92;&lt;gebruikersnaam&gt;.  
2. Stel de waarde voor **WebServiceSdkPassword** in op het juiste accountwachtwoord.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>De webservice-SDK configureren met een clientcertificaat

Als u geen gebruikersnaam en wachtwoord wilt gebruiken, volgt u deze stappen voor het configureren van de webservice-SDK met een clientcertificaat.

1. Verkrijg een certificaat van een certificeringsinstantie voor de server waarop de webservice-SDK wordt uitgevoerd. Lees hoe u [clientcertificaten kunt verkrijgen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770328(v=ws.10)).  
2. Importeer het clientcertificaat in het persoonlijke certificaatarchief van de lokale computer op de server waarop de webservice-SDK wordt uitgevoerd. Zorg dat het openbare certificaat van de certificeringsinstantie zich bevindt in de certificaatopslag met vertrouwde basiscertificaten.  
3. Exporteer de openbare en persoonlijke sleutels van het clientcertificaat naar een PFX-bestand.  
4. Exporteer de openbare sleutel in Base64-indeling naar een CER-bestand.  
5. Controleer in Serverbeheer of het onderdeel Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication (Verificatie van IIS-clientcertificaattoewijzing) is geïnstalleerd. Als dit niet is geïnstalleerd, kiest u **Functies en onderdelen toevoegen** om dit onderdeel toe te voegen.  
6. Dubbelklik in IIS-beheer op **Configuratie-editor** voor de website die de virtuele map van de webservice-SDK bevat. Het is belangrijk om de website te selecteren, niet de virtuele map.  
7. Ga naar het gedeelte **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.  
8. Stel enabled in op **true**.  
9. Stel oneToOneCertificateMappingsEnabled in op **true**.  
10. Klik op de knop **...** naast oneToOneMappings en vervolgens op de koppeling **Toevoegen**.  
11. Open het CER-bestand dat u eerder hebt geëxporteerd in base 64-indeling. Verwijder *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* en alle regeleinden. Kopieer de resulterende tekenreeks.  
12. Stel het certificaat in op de tekenreeks die u in de vorige stap hebt gekopieerd.  
13. Stel enabled in op **true**.  
14. Stel Gebruikersnaam in op een account dat lid is van de veiligheidsgroep PhoneFactor Admins. Gebruik de indeling &lt;domein&gt;&#92;&lt;gebruikersnaam&gt;.  
15. Stel het wachtwoord in op het juiste accountwachtwoord en sluit vervolgens de Configuratie-editor.  
16. Klik op de koppeling **Toepassen**.  
17. Dubbelklik in de virtuele map van de webservice-SDK op **Verificatie**.  
18. Controleer of ASP.NET-imitatie en Basisverificatie zijn ingesteld op **Ingeschakeld** en of alle overige items zijn ingesteld op **Uitgeschakeld**.  
19. Dubbelklik in de virtuele map van de webservice-SDK op **SSL-instellingen**.  
20. Stel Clientcertificaten in op **Accepteren** en klik daarna op **Toepassen**.  
21. Kopieer het PFX-bestand dat u eerder hebt geëxporteerd naar de server waarop de AD FS-adapter wordt uitgevoerd.  
22. Importeer het PFX-bestand in het persoonlijke certificaatarchief van de lokale computer.  
23. Klik met de rechtermuisknop en selecteer **Persoonlijke sleutels beheren**, en verleen leestoegang tot het account waarmee u zich aanmeldt bij de AD FS-service.  
24. Open het clientcertificaat en kopieer de vingerafdruk van het tabblad **Details**.  
25. Stel in het bestand MultiFactorAuthenticationAdfsAdapter.config **WebServiceSdkCertificateThumbprint** in op de tekenreeks die u in de vorige stap hebt gekopieerd.  

Voer als laatste stap het script \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 uit in PowerShell om de adapter te registreren. De adapter wordt geregistreerd als WindowsAzureMultiFactorAuthentication. Start de AD FS-service opnieuw op voordat de registratie van kracht wordt.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Azure AD-resources beveiligen met behulp van AD FS

Voor de beveiliging van uw cloudresource stelt u een claimregel in die ervoor zorgt dat Active Directory Federation Services de multipleauthn-claim verstuurt wanneer een gebruiker de verificatie in twee stappen voltooit. Deze claim wordt doorgegeven aan Azure AD. Volg deze procedure om de stappen te doorlopen:

1. Open AD FS-beheer.
2. Selecteer aan de linkerkant de **vertrouwens relatie van de relying** Party.
3. Klik met de rechter muisknop op **Microsoft Office 365-identiteits platform** en selecteer **claim regels bewerken...**

   ![Claim regels bewerken in de ADFS-console](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. Klik bij uitgifte transformatie regels op **regel toevoegen.**

   ![Transformatie regels bewerken in de ADFS-console](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. Selecteer in de wizard Transformatieclaimregels toevoegen **Passthrough of Een binnenkomende claim filteren** in de vervolgkeuzelijst en klik op **Volgende**.

   ![Wizard claim regel voor trans formatie toevoegen](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. Geef de regel een naam.
7. Selecteer **Authenticatiemethodereferenties** als het type voor binnenkomende claims.
8. Selecteer **Alle claimwaarden doorgeven**.

    ![Wizard Claimregel voor transformatie toevoegen](./media/howto-mfaserver-adfs-2012/configurewizard.png)

9. Klik op **Voltooien**. Sluit de AD FS-beheerconsole.

## <a name="troubleshooting-logs"></a>Problemen met logboeken oplossen

Gebruik de volgende stappen om aanvullende logboekregistratie in te schakelen voor hulp bij het oplossen van problemen met de AD FS-adapter van de MFA-server.

1. Open in de MFA-serverinterface de sectie AD FS en schakel het selectievakje **Logboekregistratie inschakelen** in.
2. Voer **regedit.exe** op elke AD FS-server uit om registersleutel `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` van de tekenreekswaarde te maken met de waarde `C:\Program Files\Multi-Factor Authentication Server\` (of een andere map naar keuze).  **Opmerking: de backslash aan het eind is belangrijk.**
3. Maak map `C:\Program Files\Multi-Factor Authentication Server\Logs` (of een andere map, zoals in **Stap 2** is aangegeven).
4. Verleen het AD FS-serviceaccount toestemming voor het wijzigen van de toegang voor de map Logboeken.
5. Start de AD FS-service opnieuw.
6. Controleer of bestand `MultiFactorAuthAdfsAdapter.log` in de map Logboeken is gemaakt.

## <a name="related-topics"></a>Verwante onderwerpen

Zie de [Veelgestelde vragen over Azure Multi-Factor Authentication](multi-factor-authentication-faq.md) voor oplossingen voor problemen