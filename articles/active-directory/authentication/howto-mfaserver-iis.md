---
title: IIS-verificatie en Azure MFA-server-Azure Active Directory
description: Leer hoe u IIS-verificatie en Azure Multi-Factor Authentication-server implementeert.
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
ms.openlocfilehash: c522fb950e2489afce44553d233ac3eddf138730
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742167"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Azure Multi-Factor Authentication-server configureren voor IIS-webtoepassingen

Gebruik de IIS-verificatiesectie van de Azure Multi-Factor Authentication-server (MFA) om IIS-verificatie in te schakelen en te configureren voor integratie met Microsoft IIS-webtoepassingen. Met de Azure MFA-server wordt een invoegtoepassing geïnstalleerd voor het filteren van aanvragen die worden ingediend bij de IIS-webserver om Azure Multi-Factor Authentication te kunnen toevoegen. De IIS-invoegtoepassing biedt ondersteuning voor verificatie op basis van formulier en geïntegreerde Windows-verificatie voor HTTP. Goedgekeurde IP-adressen kunnen ook zo worden geconfigureerd dat interne IP-adressen worden vrijgesteld van verificatie met twee factoren.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.
>
> Wanneer u Azure Multi-Factor Authentication op basis van de Cloud gebruikt, is er geen alternatief voor de IIS-invoeg toepassing die is verschaft door de Azure Multi-Factor Authentication-Server (MFA). Gebruik in plaats daarvan Web Application proxy (WAP) met Active Directory Federation Services (AD FS) of de toepassings proxy van Azure Active Directory.

![IIS-verificatie in MFA-server](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>IIS-verificatie samen gebruiken met Azure Multi-Factor Authentication-server

Als u een IIS-webtoepassing wilt beveiligen die gebruikmaakt van verificatie op basis van een formulier, moet u de Azure Multi-Factor Authentication-server op de IIS-webserver installeren en de server volgens de onderstaande procedure configureren:

1. Klik op de Azure Multi-Factor Authentication-server in het menu links op het pictogram IIS-verificatie.
2. Klik op het tabblad **op formulier gebaseerd** .
3. Klik op **Add**.
4. Als u de gebruikers naam, het wacht woord en de domein variabelen automatisch wilt detecteren, voert u de aanmeldings-URL (bijvoorbeeld `https://localhost/contoso/auth/login.aspx` ) in het dialoog venster Form-Based website automatisch configureren in en klikt u op **OK**.
5. Schakel het selectievakje **Overeenkomende Multi-Factor Authentication-gebruiker vereisen** in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan Multi-Factor Authentication moeten worden onderworpen. Als een groot aantal gebruikers nog niet is geïmporteerd in de server en/of vrijgesteld zal zijn van Multi-Factor Authentication, laat u het vakje uitgeschakeld.
6. Als de paginavariabelen niet automatisch kunnen worden gedetecteerd, klikt u op de **Handmatig opgeven** in het dialoogvenster Formulier-gebaseerde website automatisch configureren.
7. Voer in het dialoogvenster Formulier-gebaseerde website toevoegen in het veld Indienings-URL de URL naar de aanmeldingspagina in en voer een toepassingsnaam in (optioneel). De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
8. Selecteer de juiste aanvraagindeling. Deze is voor de meeste webtoepassingen ingesteld op **POST of GET**.
9. Voer de gebruikersnaamvariabele, de wachtwoordvariabele en de domeinvariabele in (als deze op de aanmeldingspagina worden weergegeven). Ga in een webbrowser naar de aanmeldingspagina, klik met de rechtermuisknop op de pagina en selecteer **Bron weergeven** om de namen van de invoervakken te vinden.
10. Schakel het selectie vakje **overeenkomende Azure multi-factor Authentication-gebruiker vereisen** in als alle gebruikers zijn of moeten worden geïmporteerd in de server en onderworpen zijn aan multi-factor Authentication. Als een groot aantal gebruikers nog niet is geïmporteerd in de server en/of vrijgesteld zal zijn van Multi-Factor Authentication, laat u het vakje uitgeschakeld.
11. Klik op **Geavanceerd** om de geavanceerde instellingen te bekijken, zoals:

    - Een aangepast bestand voor de weigeringspagina selecteren
    - Succesvolle verificaties voor de website voor een bepaalde periode in de cache opslaan met behulp van cookies
    - Selecteer of u de primaire referenties wilt verifiëren aan de hand van een Windows-domein, LDAP-adreslijst. of RADIUS-server.

12. Klik op **OK** om terug te gaan naar het dialoogvenster Formulier-gebaseerde website toevoegen.
13. Klik op **OK**.
14. Zodra de URL- en paginavariabelen zijn gedetecteerd of ingevoerd, worden de websitegegevens weergegeven in het paneel Op formulier gebaseerd.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Geïntegreerde Windows-verificatie met Azure Multi-Factor Authentication-server gebruiken

Als u een IIS-webtoepassing wilt beveiligen die gebruikmaakt van geïntegreerde Windows-verificatie voor HTTP, moet u de Azure MFA-server op de IIS-webserver installeren en de server aan de hand van de onderstaande stappen configureren:

1. Klik op de Azure Multi-Factor Authentication-server in het menu links op het pictogram IIS-verificatie.
2. Klik op het tabblad **http** .
3. Klik op **Add**.
4. Voer in het dialoog venster basis-URL toevoegen in het veld de URL in voor de website waar de HTTP-verificatie wordt uitgevoerd (zoals `http://localhost/owa` ) en geef een toepassings naam op (optioneel). De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
5. Pas de tijd voor time-out voor inactiviteit en voor Maximale sessie aan als de standaardwaarde niet voldoende is.
6. Schakel het selectievakje **Overeenkomende Multi-Factor Authentication-gebruiker vereisen** in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan Multi-Factor Authentication moeten worden onderworpen. Als een groot aantal gebruikers nog niet is geïmporteerd in de server en/of vrijgesteld zal zijn van Multi-Factor Authentication, laat u het vakje uitgeschakeld.
7. Schakel, indien gewenst, het selectievakje **Cookie gebruiken om voltooide authenticaties in de cache op te slaan** in.
8. Klik op **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>IIS-invoegtoepassingen inschakelen voor Azure Multi-Factor Authentication-server

Nadat u de URL's en instellingen voor op formulier gebaseerde of HTTP-verificaties hebt ingesteld, selecteert u de locaties waar de IIS-invoegtoepassingen van Azure Multi-Factor Authentication moeten worden geladen en ingeschakeld in IIS. Gebruik de volgende procedure:

1. Als IIS 6 wordt uitgevoerd, klikt u op het tabblad **ISAPI** . Selecteer de website waaronder de webtoepassing wordt uitgevoerd (bijvoorbeeld de standaard website) om de invoeg toepassing Azure Multi-Factor Authentication ISAPI-filter voor die site in te scha kelen.
2. Als wordt uitgevoerd op IIS 7 of hoger, klikt u op het tabblad **systeem eigen module** . Selecteer de server, websites of toepassingen om de IIS-invoeg toepassing op de gewenste niveaus in te scha kelen.
3. Klik op het vak **IIS-verificatie** inschakelen aan de bovenkant van het scherm. Azure Multi-Factor Authentication is nu de beveiliging van de geselecteerde IIS-toepassing. Zorg ervoor dat gebruikers in de server zijn geïmporteerd.

## <a name="trusted-ips"></a>Goedgekeurde IP-adressen

De goedgekeurde IP-adressen bieden gebruikers de mogelijkheid om Azure Multi-Factor Authentication over te slaan voor websiteverzoeken die afkomstig zijn van bepaalde IP-adressen of subnetten. Zo kunt u gebruikers vrijstellen van Azure Multi-Factor Authentication wanneer zij zich op kantoor aanmelden. Hiervoor geeft u het subnet van de werkplek op als een goedgekeurd IP-adres. Gebruik de volgende procedure als u goedgekeurde IP-adressen wilt configureren:

1. Klik in de sectie IIS-verificatie op het tabblad **vertrouwde IP-adressen** .
2. Klik op **Add**.
3. Wanneer het dialoog venster vertrouwde Ip's toevoegen wordt weer gegeven, selecteert u het keuze rondje **Eén IP**-adres, **IP-bereik** of **subnet** .
4. Voer het IP-adres, het bereik van de IP-adressen of het subnet in dat moet worden toegestaan. Als u een subnet wilt invoeren, selecteert u het geschikte Netmasker en klikt u op **OK**.
