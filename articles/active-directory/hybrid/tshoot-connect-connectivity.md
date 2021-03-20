---
title: 'Azure AD Connect: problemen met Azure AD-connectiviteit oplossen | Microsoft Docs'
description: In dit artikel wordt uitgelegd hoe verbindings problemen met Azure AD Connect worden opgelost.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 56e9820c5e3a750a35b7271b86750df00eb4784e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92677059"
---
# <a name="troubleshoot-azure-ad-connectivity"></a>Problemen met Azure AD-connectiviteit oplossen
In dit artikel wordt uitgelegd hoe connectiviteit tussen Azure AD Connect en Azure AD werkt en hoe u verbindings problemen kunt oplossen. Deze problemen worden hoogstwaarschijnlijk gezien in een omgeving met een proxy server.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Verbindings problemen in de installatie wizard oplossen
Azure AD Connect maakt gebruik van moderne verificatie (met behulp van de ADAL-bibliotheek) voor verificatie. Voor de installatie wizard en de synchronisatie-engine moet machine.config correct worden geconfigureerd, omdat deze twee .NET-toepassingen zijn.

In dit artikel laten we zien hoe fabrikam verbinding maakt met Azure AD via de proxy. De proxy server heeft de naam fabrikamproxy en maakt gebruik van poort 8080.

Eerst moeten we controleren of [**machine.config**](how-to-connect-install-prerequisites.md#connectivity) juist is geconfigureerd en **Microsoft Azure AD de synchronisatie service** eenmaal opnieuw is opgestart nadat het machine.config bestand is bijgewerkt.
![Scherm afbeelding toont een deel van het configuratie bestand van de machine dot.](./media/tshoot-connect-connectivity/machineconfig.png)

> [!NOTE]
> In sommige niet-micro soft-blogs wordt gedocumenteerd dat er in plaats daarvan wijzigingen in miiserver.exe.config moeten worden aangebracht. Dit bestand wordt echter bij elke upgrade overschreven, dus zelfs als het tijdens de eerste installatie werkt, stopt het systeem bij de eerste upgrade. Daarom is het raadzaam om machine.config in plaats daarvan bij te werken.
>
>

De proxy server moet ook de vereiste Url's hebben geopend. De officiële lijst wordt beschreven in [Office 365 url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)adresbereiken.

Van deze Url's is de volgende tabel mini maal de absoluut bare waarde om te kunnen verbinden met Azure AD. Deze lijst bevat geen optionele functies, zoals wacht woord terugschrijven of Azure AD Connect Health. Dit wordt hier beschreven om te helpen bij het oplossen van problemen met de initiële configuratie.

| URL | Poort | Beschrijving |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Wordt gebruikt voor het downloaden van CRL-lijsten. |
| \*. verisign.com |HTTP/80 |Wordt gebruikt voor het downloaden van CRL-lijsten. |
| \*. entrust.net |HTTP/80 |Wordt gebruikt voor het downloaden van CRL-lijsten voor MFA. |
| \*.windows.net |HTTPS/443 |Wordt gebruikt om u aan te melden bij Azure AD. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |Gebruikt voor MFA. |
| \*.microsoftonline.com |HTTPS/443 |Wordt gebruikt voor het configureren van uw Azure AD-Directory en het importeren/exporteren van gegevens. |
| \*. crl3.digicert.com |HTTP/80 |Wordt gebruikt om certificaten te controleren. |
| \*. crl4.digicert.com |HTTP/80 |Wordt gebruikt om certificaten te controleren. |
| \*. ocsp.digicert.com |HTTP/80 |Wordt gebruikt om certificaten te controleren. |
| \*. www.d-trust.net |HTTP/80 |Wordt gebruikt om certificaten te controleren. |
| \*. root-c3-ca2-2009.ocsp.d-trust.net |HTTP/80 |Wordt gebruikt om certificaten te controleren. |
| \*. crl.microsoft.com |HTTP/80 |Wordt gebruikt om certificaten te controleren. |
| \*. oneocsp.microsoft.com |HTTP/80 |Wordt gebruikt om certificaten te controleren. |
| \*. ocsp.msocsp.com |HTTP/80 |Wordt gebruikt om certificaten te controleren. |

## <a name="errors-in-the-wizard"></a>Fouten in de wizard
De installatie wizard gebruikt twee verschillende beveiligings contexten. Op de pagina **verbinding maken met Azure AD** wordt de momenteel aangemelde gebruiker gebruikt. Op het **configureren** van de pagina wordt het wijzigen van het [account waarmee de service wordt uitgevoerd voor de synchronisatie-engine](reference-connect-accounts-permissions.md#adsync-service-account). Als er een probleem is, lijkt het waarschijnlijk dat u de pagina **verbinding maken met Azure AD** in de wizard hebt, omdat de proxy configuratie globaal is.

De volgende problemen zijn de meest voorkomende fouten die optreden in de installatie wizard.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>De installatie wizard is niet juist geconfigureerd
Deze fout wordt weer gegeven wanneer de wizard zelf de proxy niet kan bereiken.
![Scherm opname toont een fout: kan de referenties niet valideren.](./media/tshoot-connect-connectivity/nomachineconfig.png)

* Als u deze fout ziet, controleert u of de [machine.config](how-to-connect-install-prerequisites.md#connectivity) juist is geconfigureerd.
* Als dat het goed is, volgt u de stappen in [Controleer de connectiviteit](#verify-proxy-connectivity) van de proxy om te zien of het probleem zich buiten de wizard bevindt.

### <a name="a-microsoft-account-is-used"></a>Een Microsoft-account wordt gebruikt
Als u een **Microsoft-account** gebruikt in plaats van een **school-of organisatie** account, ziet u een algemene fout.
![Er wordt een micro soft-account gebruikt](./media/tshoot-connect-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Het MFA-eind punt kan niet worden bereikt
Deze fout wordt weer gegeven als het eind punt **https://secure.aadcdn.microsoftonline-p.com** niet kan worden bereikt en voor uw globale beheerder MFA is ingeschakeld.
![nomachineconfig](./media/tshoot-connect-connectivity/nomicrosoftonlinep.png)

* Als u deze fout ziet, controleert u of het eind punt **Secure.aadcdn.microsoftonline-p.com** is toegevoegd aan de proxy.

### <a name="the-password-cannot-be-verified"></a>Het wacht woord kan niet worden geverifieerd
Als de installatie wizard is geslaagd om verbinding te maken met Azure AD, maar het wacht woord zelf niet kan worden geverifieerd, ziet u deze fout: ![ ongeldig wacht woord.](./media/tshoot-connect-connectivity/badpassword.png)

* Is het wacht woord een tijdelijk wacht woord en moet het worden gewijzigd? Is het eigenlijk het juiste wacht woord? Probeer u aan te melden bij `https://login.microsoftonline.com` (op een andere computer dan de Azure AD Connect-server) en controleer of het account bruikbaar is.

### <a name="verify-proxy-connectivity"></a>Proxy connectiviteit controleren
Als u wilt controleren of de Azure AD Connect server daad werkelijk verbinding heeft met de proxy en Internet, gebruikt u een Power shell om te zien of de proxy webaanvragen toestaat of niet. Voer uit in een Power shell-prompt `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc` . (Technisch de eerste aanroep is `https://login.microsoftonline.com` en deze URI werkt ook, maar de andere URI is sneller om te reageren.)

PowerShell maakt gebruik van de configuratie in machine.config om contact op te nemen met de proxy. De instellingen in winhttp/netsh zouden niet van invloed mogen zijn op deze cmdlets.

Als de proxy correct is geconfigureerd, krijgt u de status geslaagd: ![ scherm opname waarin de status geslaagd wordt weer gegeven wanneer de proxy op de juiste wijze is geconfigureerd.](./media/tshoot-connect-connectivity/invokewebrequest200.png)

Als u **geen verbinding kunt maken met de externe server**, probeert Power shell een directe aanroep uit te voeren zonder de proxy of DNS te gebruiken onjuist geconfigureerd. Controleer of het **machine.config** -bestand juist is geconfigureerd.
![unabletoconnect](./media/tshoot-connect-connectivity/invokewebrequestunable.png)

Als de proxy niet juist is geconfigureerd, wordt een fout bericht weer geven: ![ proxy200 ](./media/tshoot-connect-connectivity/invokewebrequest403.png)
 ![ proxy407](./media/tshoot-connect-connectivity/invokewebrequest407.png)

| Fout | Fout tekst | Opmerking |
| --- | --- | --- |
| 403 |Verboden |De proxy is niet geopend voor de aangevraagde URL. Ga naar de proxy configuratie en controleer of de [url's](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) zijn geopend. |
| 407 |Proxy verificatie vereist |De proxy server vereist een aanmelding en er is geen opgegeven. Als voor uw proxy server verificatie is vereist, moet u ervoor zorgen dat deze instelling is geconfigureerd in de machine.config. Zorg er ook voor dat u domein accounts gebruikt voor de gebruiker die de wizard uitvoert en voor het service account. |

### <a name="proxy-idle-timeout-setting"></a>Instelling time-out proxy inactief
Wanneer Azure AD Connect een export aanvraag naar Azure AD verzendt, kan het tot vijf minuten duren voordat de aanvraag is verwerkt voordat een antwoord wordt gegenereerd. Dit kan vooral gebeuren als er sprake is van een aantal groeps objecten met grote groepslid maatschappen die zijn opgenomen in dezelfde export aanvraag. Zorg ervoor dat de time-out voor de proxy is ingesteld op meer dan 5 minuten. Anders kan een probleem met de Azure AD Connect-server onregelmatige connectiviteits problemen met Azure AD worden waargenomen.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Het communicatie patroon tussen Azure AD Connect en Azure AD
Als u al deze voor gaande stappen hebt gevolgd en nog steeds geen verbinding kunt maken, kunt u op dit moment zoeken naar netwerk Logboeken. In deze sectie wordt een normaal en geslaagd connectiviteits patroon gedocumenteerd. Er wordt ook gemeen schappelijke rode haring vermeld die kunnen worden genegeerd wanneer u de netwerk logboeken leest.

* Er zijn aanroepen naar `https://dc.services.visualstudio.com` . Het is niet vereist om deze URL te openen in de proxy om de installatie te volt ooien en deze aanroepen kunnen worden genegeerd.
* U ziet dat de DNS-omzetting een lijst met de werkelijke hosts bevat die zich bevinden in de nsatc.net van de DNS-naam ruimte en andere naam ruimten die niet onder microsoftonline.com. Er zijn echter geen aanvragen voor webservices op de daad werkelijke server namen en u hoeft deze Url's niet aan de proxy toe te voegen.
* De eind punten adminwebservice en provisioningapi zijn detectie-eind punten en worden gebruikt om het werkelijke eind punt te vinden dat moet worden gebruikt. Deze eind punten verschillen afhankelijk van uw regio.

### <a name="reference-proxy-logs"></a>Naslag informatie over proxy logboeken
Hier volgt een dump van een echt proxy logboek en de pagina van de wizard installatie van waar het is gemaakt (dubbele vermeldingen voor hetzelfde eind punt zijn verwijderd). Deze sectie kan worden gebruikt als referentie voor uw eigen proxy en netwerk Logboeken. De werkelijke eind punten zijn mogelijk anders in uw omgeving (met name de Url's *cursief*).

**Verbinding maken met Azure AD**

| Tijd | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*. microsoftonline.com:443 |

**Configureerer**

| Tijd | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*. microsoftonline.com:443 |

**Initiële synchronisatie**

| Tijd | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-Anchor*. microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-Anchor*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Verificatiefouten
In deze sectie worden fouten behandeld die kunnen worden geretourneerd via ADAL (de verificatie bibliotheek die wordt gebruikt door Azure AD Connect) en Power shell. De fout die wordt uitgelegd, zou u kunnen helpen bij het begrijpen van de volgende stappen.

### <a name="invalid-grant"></a>Ongeldige toekenning
Ongeldige gebruikers naam of wacht woord. Zie [het wacht woord kan niet worden geverifieerd](#the-password-cannot-be-verified)voor meer informatie.

### <a name="unknown-user-type"></a>Onbekend gebruikers type
Uw Azure AD-adres lijst kan niet worden gevonden of opgelost. Kunt u zich aanmelden met een gebruikers naam in een niet-geverifieerd domein?

### <a name="user-realm-discovery-failed"></a>Detectie van gebruikers realm is mislukt
Problemen met netwerk-of proxy configuratie. Het netwerk is niet bereikbaar. Zie [verbindings problemen oplossen in de installatie wizard](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Gebruikers wachtwoord verlopen
Uw referenties zijn verlopen. Wijzig uw wacht woord.

### <a name="authorization-failure"></a>Autorisatie fout
De gebruiker is niet gemachtigd om actie uit te voeren in azure AD.

### <a name="authentication-canceled"></a>Verificatie geannuleerd
De poging tot multi-factor Authentication (MFA) is geannuleerd.

<div id="connect-msolservice-failed">
<!--
  Empty div just to act as an alias for the "Connect To MS Online Failed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="connect-to-ms-online-failed"></a>Verbinding maken met MS online is mislukt
De verificatie is voltooid, maar er is een verificatie probleem opgetreden in azure AD Power shell.

<div id="get-msoluserrole-failed">
<!--
  Empty div just to act as an alias for the "Azure AD Global Admin Role Needed" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="azure-ad-global-admin-role-needed"></a>Globale beheerdersrol van Azure AD vereist
De verificatie van de gebruiker is voltooid. Aan de gebruiker is echter geen globale beheerdersrol toegewezen. Zo [kunt u de rol van globale beheerder toewijzen](../roles/permissions-reference.md) aan de gebruiker.

<div id="privileged-identity-management">
<!--
  Empty div just to act as an alias for the "Privileged Identity Management Enabled" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="privileged-identity-management-enabled"></a>Privileged Identity Management ingeschakeld
De verificatie is voltooid. Privileged Identity Management is ingeschakeld en u bent momenteel geen globale beheerder. Zie [privileged Identity Management](../privileged-identity-management/pim-getting-started.md)voor meer informatie.

<div id="get-msolcompanyinformation-failed">
<!--
  Empty div just to act as an alias for the "Company Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="company-information-unavailable"></a>Bedrijfs gegevens niet beschikbaar
De verificatie is voltooid. Kan geen bedrijfs gegevens ophalen uit Azure AD.

<div id="get-msoldomain-failed">
<!--
  Empty div just to act as an alias for the "Domain Information Unavailable" header
  because we used the mentioned id in the code to jump to this section.
-->
</div>

### <a name="domain-information-unavailable"></a>Domein gegevens niet beschikbaar
De verificatie is voltooid. Kan geen domein gegevens ophalen uit Azure AD.

### <a name="unspecified-authentication-failure"></a>Niet-opgegeven verificatie fout
Wordt weer gegeven als onverwachte fout in de installatie wizard. Kan zich voordoen als u een **micro soft-account** wilt gebruiken in plaats van een **school-of organisatie account**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Stappen voor probleem oplossing voor eerdere versies.
Met releases die beginnen met Build Number 1.1.105.0 (uitgebracht op 2016 februari), is de aanmeld hulp buiten gebruik gesteld. Deze sectie en de configuratie moeten niet meer nodig zijn, maar blijven als referentie.

Voor de tijdelijke assistent voor eenmalige aanmelding moet WinHTTP worden geconfigureerd. Deze configuratie kan worden uitgevoerd met [**netsh**](how-to-connect-install-prerequisites.md#connectivity).
![Scherm afbeelding toont een opdracht prompt venster met het hulp programma Netsh voor het instellen van een proxy.](./media/tshoot-connect-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>De aanmeld hulp is niet juist geconfigureerd
Deze fout wordt weer gegeven wanneer de aanmeld hulp de proxy niet kan bereiken of de proxy de aanvraag niet toestaat.
![Scherm opname toont een fout: kan de referenties niet valideren, Controleer de netwerk verbinding en de firewall-of proxy-instellingen.](./media/tshoot-connect-connectivity/nonetsh.png)

* Als u deze fout ziet, bekijkt u de proxy configuratie in [netsh](how-to-connect-install-prerequisites.md#connectivity) en controleert u of deze juist is.
  ![Scherm afbeelding toont een opdracht prompt venster met het hulp programma Netsh om de proxy configuratie weer te geven.](./media/tshoot-connect-connectivity/netshshow.png)
* Als dat het goed is, volgt u de stappen in [Controleer de connectiviteit](#verify-proxy-connectivity) van de proxy om te zien of het probleem zich buiten de wizard bevindt.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
