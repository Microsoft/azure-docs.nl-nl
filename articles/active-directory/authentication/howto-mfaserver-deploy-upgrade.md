---
title: Azure MFA-server-Azure Active Directory bijwerken
description: Stappen en richt lijnen voor het bijwerken van de Azure-Multi-Factor Authentication-server naar een nieuwere versie.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbddd2eb52414827561d8896dfc8bc9ff705f41b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584388"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Upgraden naar de nieuwste versie van Azure Multi-Factor Authentication Server

Dit artikel begeleidt u bij het upgraden van Azure Multi-Factor Authentication (MFA) Server v 6.0 of hoger. Als u een upgrade wilt uitvoeren van een oude versie van de Phone factor-agent, raadpleegt u [de Phone factor-agent bijwerken naar Azure multi-factor Authentication-Server](howto-mfaserver-deploy-upgrade-pf.md).

Als u een upgrade uitvoert van v6. x of ouder naar v7. x of hoger, worden alle onderdelen gewijzigd van .NET 2,0 naar .NET 4,5. Voor alle onderdelen is ook micro soft Visual C++ 2015 Redistributable update 1 of hoger vereist. Het installatie programma van de MFA-server installeert zowel de x86-als de x64-versie van deze onderdelen als ze nog niet zijn geïnstalleerd. Als de gebruikers Portal en de webservice voor mobiele apps op verschillende servers worden uitgevoerd, moet u deze pakketten installeren voordat u deze onderdelen bijwerkt. U kunt in het [micro soft Download centrum](https://www.microsoft.com/download/)zoeken naar de meest recente update voor micro soft Visual C++ 2015. 

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

Upgrade stappen in een oogopslag:

* Azure MFA-servers upgraden (onderliggende niveaus, vervolgens primair)
* De exemplaren van de gebruikers Portal bijwerken
* Upgrade van de exemplaren van de AD FS-adapter

## <a name="upgrade-azure-mfa-server"></a>Azure MFA-Server upgraden

1. Volg de instructies in [de Azure-multi-factor Authentication-server downloaden](howto-mfaserver-deploy.md#download-the-mfa-server) om de nieuwste versie van het installatie programma van de Azure MFA-server op te halen.
2. Maak een back-up van het MFA-server gegevensbestand dat zich bevindt in de C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (uitgaande van de standaard installatie locatie) op uw primaire MFA-server.
3. Als u meerdere servers voor hoge Beschik baarheid uitvoert, wijzigt u de client systemen die worden geverifieerd bij de MFA-server, zodat deze geen verkeer meer verzenden naar de servers die worden bijgewerkt. Als u een load balancer gebruikt, verwijdert u een ondergeschikte MFA-server uit de load balancer, voert u de upgrade uit en voegt u de server vervolgens weer toe aan de farm.
4. Voer het nieuwe installatie programma uit op elke MFA-server. Werk eerst onderliggende servers bij omdat ze het oude gegevens bestand kunnen lezen dat door de primaire server wordt gerepliceerd.

   > [!NOTE]
   > Wanneer u een server bijwerkt, moet deze worden verwijderd uit de taak verdeling of het delen van verkeer met andere MFA-servers.
   >
   > U hoeft uw huidige MFA-server niet te verwijderen voordat u het installatie programma uitvoert. Het installatie programma voert een in-place upgrade uit. Het installatiepad wordt vanuit de vorige installatie vanuit het REGI ster opgehaald, zodat het wordt geïnstalleerd op dezelfde locatie (bijvoorbeeld C:\Program Files\Multi-Factor Authentication Server).
  
5. Als u wordt gevraagd een herdistribueerbaar update pakket voor micro soft Visual C++ 2015 te installeren, accepteert u de prompt. Zowel de x86-als de x64-versie van het pakket zijn geïnstalleerd.
6. Als u de Web Service SDK gebruikt, wordt u gevraagd de nieuwe webservice-SDK te installeren. Wanneer u de nieuwe Web Service SDK installeert, moet u ervoor zorgen dat de naam van de virtuele map overeenkomt met de eerder geïnstalleerde virtuele map (bijvoorbeeld MultiFactorAuthWebServiceSdk).
7. Herhaal de stappen op alle onderliggende servers. Stimuleer een van de onderliggende niveaus als de nieuwe primaire server en voer vervolgens een upgrade uit voor de oude primaire.

## <a name="upgrade-the-user-portal"></a>De gebruikers Portal bijwerken

Voltooi de upgrade van uw MFA-servers voordat u overstapt op deze sectie.

1. Maak een back-up van het web.config-bestand dat zich in de virtuele map van de installatie locatie van de gebruikers Portal (bijvoorbeeld C:\inetpub\wwwroot\MultiFactorAuth) bevindt. Als er wijzigingen zijn aangebracht in het standaard thema, maakt u ook een back-up van de map App_Themes \Default. Het is beter om een kopie van de standaardmap te maken en een nieuw thema te maken dan het standaard thema te wijzigen.
2. Als de gebruikers Portal op dezelfde server als de andere MFA-Server onderdelen wordt uitgevoerd, wordt u door de installatie van de MFA-server gevraagd de gebruikers portal bij te werken. Accepteer de prompt en installeer de update voor de gebruikers Portal. Controleer of de naam van de virtuele map overeenkomt met de eerder geïnstalleerde virtuele map (bijvoorbeeld MultiFactorAuth).
3. Als de gebruikers Portal zich op een eigen server bevindt, kopieert u het MultiFactorAuthenticationUserPortalSetup64.msi-bestand van de installatie locatie van een van de MFA-servers en plaatst u het op de webserver van de gebruikers Portal. Voer het installatieprogramma uit.

   Als er een fout optreedt met de melding, is ' micro soft Visual C++ 2015 Redistributable update 1 of hoger vereist, ' down load en installeer het meest recente update pakket in het [micro soft Download centrum](https://www.microsoft.com/download/). Installeer zowel de x86-als de x64-versie.

4. Nadat de bijgewerkte gebruikers Portal software is geïnstalleerd, vergelijkt u de web.config back-up die u in stap 1 hebt gemaakt met het nieuwe web.config bestand. Als er geen nieuwe kenmerken aanwezig zijn in de nieuwe web.config, kopieert u de back-upweb.config naar de virtuele map om de nieuwe te overschrijven. Een andere optie is het kopiëren/plakken van de waarden van appSettings en de URL van de webservice-SDK van het back-upbestand in de nieuwe web.config.

Als u de gebruikers Portal op meerdere servers hebt, herhaalt u de installatie ervan.

## <a name="upgrade-the-mobile-app-web-service"></a>De webservice voor de mobiele app upgraden

> [!NOTE]
> Wanneer u een upgrade uitvoert van een versie van Azure MFA Server ouder dan 8,0 naar 8.0 +, kan de webservice voor de mobiele app na de upgrade worden verwijderd

## <a name="upgrade-the-ad-fs-adapters"></a>De AD FS adapters upgraden

Voltooi de upgrade van uw MFA-servers en gebruikers Portal voordat u doorgaat naar deze sectie.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Als MFA op verschillende servers wordt uitgevoerd dan AD FS

Deze instructies zijn alleen van toepassing als u Multi-Factor Authentication-server los van uw AD FS servers uitvoert. Als beide services worden uitgevoerd op dezelfde servers, slaat u deze sectie over en gaat u naar de installatie stappen. 

1. Sla een kopie op van het MultiFactorAuthenticationAdfsAdapter.config bestand dat in AD FS is geregistreerd of Exporteer de configuratie met behulp van de volgende Power shell-opdracht: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]` . De adapter naam is "WindowsAzureMultiFactorAuthentication" of "AzureMfaServerAuthentication", afhankelijk van de versie die eerder is geïnstalleerd.
2. Kopieer de volgende bestanden van de installatie locatie van de MFA-server naar de AD FS servers:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Bewerk het Register-MultiFactorAuthenticationAdfsAdapter.ps1 script door toe `-ConfigurationFilePath [path]` te voegen aan het einde van de `Register-AdfsAuthenticationProvider` opdracht. Vervang *[pad]* door het volledige pad naar het MultiFactorAuthenticationAdfsAdapter.config bestand of het configuratie bestand dat u in de vorige stap hebt geëxporteerd.

   Controleer de kenmerken in de nieuwe MultiFactorAuthenticationAdfsAdapter.config om te zien of ze overeenkomen met het oude configuratie bestand. Als kenmerken zijn toegevoegd aan of verwijderd uit de nieuwe versie, kopieert u de kenmerk waarden van het oude configuratie bestand naar de nieuwe of wijzigt u het oude configuratie bestand zodat dit overeenkomt.

### <a name="install-new-ad-fs-adapters"></a>Nieuwe AD FS-adapters installeren

> [!IMPORTANT]
> Uw gebruikers hoeven geen verificatie in twee stappen uit te voeren tijdens stap 3-8 van deze sectie. Als u AD FS in meerdere clusters hebt geconfigureerd, kunt u elk cluster in de farm onafhankelijk van de andere clusters verwijderen, bijwerken en herstellen om uitval tijd te voor komen.

1. Verwijder enkele AD FS servers uit de farm. Werk deze servers bij terwijl de andere nog actief zijn.
2. Installeer de nieuwe AD FS adapter op elke server die is verwijderd uit de AD FS-farm. Als de MFA-server op elke AD FS server is geïnstalleerd, kunt u bijwerken via de MFA-Server beheerder UX. Anders kunt u bijwerken door MultiFactorAuthenticationAdfsAdapterSetup64.msi uit te voeren.

   Als er een fout optreedt met de melding, is ' micro soft Visual C++ 2015 Redistributable update 1 of hoger vereist, ' down load en installeer het meest recente update pakket in het [micro soft Download centrum](https://www.microsoft.com/download/). Installeer zowel de x86-als de x64-versie.

3. Ga naar **AD FS**-  >  **verificatie beleid** globaal multi-  >  **Factor Authentication-beleid bewerken**. Schakel **WindowsAzureMultiFactorAuthentication** of **AzureMFAServerAuthentication** uit (afhankelijk van de huidige versie geïnstalleerd).

   Nadat deze stap is voltooid, is verificatie in twee stappen via MFA server pas beschikbaar in dit AD FS cluster als u stap 8 hebt voltooid.

4. Hef de registratie van de oudere versie van de AD FS adapter op door het Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 Power shell-script uit te voeren. Zorg ervoor dat de para meter *-name* ("WindowsAzureMultiFactorAuthentication" of "AzureMFAServerAuthentication") overeenkomt met de naam die in stap 3 wordt weer gegeven. Dit geldt voor alle servers in hetzelfde AD FS cluster, omdat er een centrale configuratie is.
5. Registreer de nieuwe AD FS-adapter door het Register-MultiFactorAuthenticationAdfsAdapter.ps1 Power shell-script uit te voeren. Dit geldt voor alle servers in hetzelfde AD FS cluster, omdat er een centrale configuratie is.
6. Start de AD FS-service opnieuw op elke server die is verwijderd uit de AD FS-farm.
7. Voeg de bijgewerkte servers weer toe aan de AD FS-Farm en verwijder de andere servers uit de farm.
8. Ga naar **AD FS**-  >  **verificatie beleid** globaal multi-  >  **Factor Authentication-beleid bewerken**. Controleer **AzureMfaServerAuthentication**.
9. Herhaal stap 2 voor het bijwerken van de servers die nu zijn verwijderd uit de AD FS Farm en start de AD FS-service op die servers opnieuw.
10. Voeg deze servers weer toe aan de AD FS-farm.

## <a name="next-steps"></a>Volgende stappen

* Bekijk voor beelden van [Geavanceerde scenario's met Azure multi-factor Authentication en vpn's van](howto-mfaserver-nps-vpn.md) derden

* [MFA-server synchroniseren met Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Windows-verificatie configureren](howto-mfaserver-windows.md) voor uw toepassingen
