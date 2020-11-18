---
title: LDAP-verificatie en Azure MFA-server-Azure Active Directory
description: De implementatie van LDAP-verificatie en Azure Multi-Factor Authentication-server.
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
ms.openlocfilehash: 6658eb6f74474d4394310bd39e897c50fc742a95
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838430"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-verificatie en Azure Multi-Factor Authentication-server

Standaard is de Azure Multi-Factor Authentication-server geconfigureerd om de gebruikers van Active Directory te importeren of te synchroniseren. Deze kan echter worden geconfigureerd om verbinding te maken met verschillende LDAP-adreslijsten, zoals een ADAM-adreslijst of een specifieke Active Directory-domeincontroller. Wanneer er via LDAP verbinding wordt gemaakt met een directory, kan de Azure-Multi-Factor Authentication-server fungeren als een LDAP-proxy om verificaties uit te voeren. Daarnaast kunt u de LDAP-binding gebruiken als een RADIUS-doel voor het uitvoeren van pre-verificaties van gebruikers met IIS-verificatie, of voor het uitvoeren van primaire verificaties in de Azure MFA-gebruikersportal.

Als u Azure Multi-Factor Authentication wilt gebruiken als een LDAP-proxy, plaatst u de Azure-Multi-Factor Authentication-server tussen de LDAP-client (bijvoorbeeld VPN-apparaat, toepassing) en de LDAP-adreslijst server. De Azure Multi-Factor Authentication-server moet worden geconfigureerd om te kunnen communiceren met zowel de clientservers als de LDAP-directory. In deze configuratie accepteert de Azure Multi-Factor Authentication-server LDAP-aanvragen van clientservers en toepassingen, en stuurt deze door naar de doel-LDAP-adreslijstserver om de primaire referenties te valideren. Als de LDAP-adres lijst de primaire referenties valideert, voert Azure Multi-Factor Authentication een tweede identiteits verificatie uit en stuurt deze een antwoord terug naar de LDAP-client. De volledige verificatie slaagt alleen als de LDAP-serververificatie en de verificatie in de tweede stap zijn gelukt.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

## <a name="configure-ldap-authentication"></a>LDAP-verificatie configureren

Als u LDAP-verificatie wilt configureren, moet u de Azure Multi-Factor Authentication-server op een Windows-server installeren. Gebruik de volgende procedure:

### <a name="add-an-ldap-client"></a>Een LDAP-client toevoegen

1. Selecteer in de Azure-Multi-Factor Authentication-server het pictogram LDAP-verificatie in het menu links.
2. Schakel het selectie vakje **LDAP-verificatie inschakelen in** .

   ![LDAP-verificatie in MFA-server](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Wijzig op het tabblad clients de TCP-poort en de SSL-poort (TLS) als de Azure Multi-Factor Authentication LDAP-service moet worden gebonden aan niet-standaard poorten om te Luis teren naar LDAP-aanvragen.
4. Als u van plan bent LDAPS van de client naar de Azure Multi-Factor Authentication-server te gebruiken, moet een TLS/SSL-certificaat op dezelfde server als MFA-server worden geïnstalleerd. Klik op **Bladeren** naast het certificaat van SSL (TLS) en selecteer een certificaat dat u voor de beveiligde verbinding wilt gebruiken.
5. Klik op **Add**.
6. Voer in het dialoog venster LDAP-client toevoegen het IP-adres in van het toestel, de server of de toepassing die zich bij de server verifieert en een toepassings naam (optioneel). De naam van de toepassing wordt vermeld in Azure Multi-Factor Authentication-rapporten en kan worden weergegeven in verificatieberichten via sms of mobiele apps.
7. Schakel het selectievakje **Overeenkomende Azure Multi-Factor Authentication-gebruiker** vereisen in als alle gebruikers zijn of moeten worden geïmporteerd in de server en aan verificatie in twee stappen onderworpen zijn. Als een groot aantal gebruikers nog niet is geïmporteerd in de server en/of niet is uitgesloten van verificatie in twee stappen, laat u het vakje uitgeschakeld. Zie het Help-bestand van de MFA-server voor meer informatie over deze functie.

Herhaal deze stappen om extra LDAP-clients toe te voegen.

### <a name="configure-the-ldap-directory-connection"></a>De LDAP-directoryverbinding configureren

Wanneer Azure Multi-Factor Authentication is geconfigureerd om LDAP-verificaties te kunnen ontvangen, moet deze als proxy optreden voor verificaties bij de LDAP-adreslijst. Dat is de reden waarom op het tabblad Doel een enkele niet beschikbare optie voor het gebruik van een LDAP-doel wordt weergegeven.

> [!NOTE]
> Directory-integratie is niet gegarandeerd om te werken met andere mappen dan Active Directory Domain Services.

1. Als u de LDAP-adreslijst verbinding wilt configureren, klikt u op het pictogram **adreslijst integratie** .
2. Schakel op het tabblad instellingen het keuze rondje **specifieke LDAP-configuratie gebruiken** in.
3. Selecteer **bewerken...**
4. Vul in de velden van het dialoogvenster LDAP-configuratie bewerken de benodigde informatie in om verbinding te maken met de LDAP-adreslijst. Beschrijvingen van de velden zijn ook beschikbaar in het Help-bestand voor de Azure Multi-Factor Authentication-server.

    ![LDAP-configuratie van adreslijst integratie](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Test de LDAP-verbinding door te klikken op de knop **testen** .
6. Als de LDAP-verbindings test is geslaagd, klikt u op de knop **OK** .
7. Klik op het tabblad **filters** . De server is vooraf geconfigureerd om containers, beveiligings groepen en gebruikers van Active Directory te laden. Als u verbinding maakt met een andere LDAP-directory, moet u waarschijnlijk de weergegeven filters bewerken. Klik op de koppeling **Help** voor meer informatie over filters.
8. Klik op het tabblad **kenmerken** . De server is vooraf geconfigureerd om kenmerken van Active Directory toe te wijzen.
9. Als u verbinding maakt met een andere LDAP-directory of als u de vooraf geconfigureerde kenmerktoewijzingen wilt wijzigen, klikt u op **Bewerken...**
10. Wijzig in het dialoogvenster Kenmerken bewerken de toewijzingen van de LDAP-kenmerktoewijzingen voor uw adreslijst. Kenmerk namen kunnen worden getypt of geselecteerd door te klikken op de **..** . -knop naast elk veld. Klik op de koppeling **Help** voor meer informatie over kenmerken.
11. Klik op de knop **OK**.
12. Klik op het pictogram **bedrijfs instellingen** en selecteer het tabblad **gebruikers naam omzetting** .
13. Als u een verbinding maakt met Active Directory vanaf een server die is gekoppeld aan een domein, laat u het keuzerondje **Windows-beveiligings-id's (SID's) gebruiken voor overeenkomende gebruikersnamen** ingeschakeld. Selecteer anders het keuze rondje het **kenmerk unieke LDAP-id gebruiken voor overeenkomende gebruikers namen** .

Wanneer het keuzerondje **Het kenmerk Unieke LDAP-id gebruiken voor overeenkomende gebruikersnamen** is geselecteerd, probeert de Azure Multi-Factor Authentication-server elke gebruikersnaam om te zetten in een unieke id in de LDAP-directory. Er wordt een LDAP-zoek opdracht uitgevoerd op de gebruikersnaam kenmerken die zijn gedefinieerd op het tabblad adreslijst integratie-> kenmerken. Wanneer een gebruiker zich verifieert, wordt de gebruikers naam omgezet in de unieke id in de LDAP-adres lijst. De unieke id wordt gebruikt voor het vergelijken van de gebruiker in het Azure Multi-Factor Authentication-gegevens bestand. Hierdoor kunnen niet-hoofdletter gevoelige vergelijkingen en lange en korte gebruikersnaam notaties worden gemaakt.

Nadat u deze stappen hebt voltooid, luistert de MFA-server op de geconfigureerde poorten voor LDAP-toegangs aanvragen van de geconfigureerde clients en fungeert deze als een proxy voor die aanvragen naar de LDAP-adres lijst voor verificatie.

## <a name="configure-ldap-client"></a>LDAP-client configureren

Als u de LDAP-client wilt configureren, gebruikt u de volgende richtlijnen:

* Configureer uw apparaat, server of toepassing als u wilt dat deze via LDAP bij de Azure Multi-Factor Authentication-server wordt geverifieerd alsof het uw LDAP was. Gebruik dezelfde instellingen die u normaal gesproken gebruikt om rechtstreeks verbinding te maken met de LDAP-directory, met uitzondering van de servernaam of het IP-adres omdat deze toebehoren aan de Azure Multi-Factor Authentication-server.
* Configureer de LDAP-time-out tot 30-60 seconden, zodat er tijd is om de referenties van de gebruiker te valideren met de LDAP-adres lijst, de verificatie in de tweede stap uit te voeren, hun reactie te ontvangen en te reageren op de LDAP-toegangs aanvraag.
* Als LDAPS wordt gebruikt, moet het apparaat of de server die de LDAP-query's aanbrengt het TLS/SSL-certificaat vertrouwen dat is geïnstalleerd op de Azure-Multi-Factor Authentication-server.
