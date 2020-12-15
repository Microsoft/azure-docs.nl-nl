---
title: 'Azure AD Connect: hybride identiteits overwegingen voor Azure Government Cloud'
description: Speciale overwegingen voor het implementeren van Azure AD Connect met de Azure Government Cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e163ea34948906060996ee952f45ec0cdb467557
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504352"
---
# <a name="hybrid-identity-considerations-for-the-azure-government-cloud"></a>Overwegingen voor hybride identiteit voor de Azure Government Cloud

In dit artikel worden overwegingen beschreven voor het integreren van een hybride omgeving met de Microsoft Azure Government Cloud. Deze informatie wordt verstrekt als referentie voor beheerders en architecten die samen werken met de Azure Government Cloud.

> [!NOTE]
> Als u een micro soft Active Directory-omgeving (on-premises of gehost in een IaaS die deel uitmaakt van dezelfde Cloud instantie) wilt integreren met de Azure Government Cloud, moet u een upgrade uitvoeren naar de nieuwste versie van [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *white list*, een term die micro soft niet meer gebruikt. Wanneer de periode van de software wordt verwijderd, worden deze uit dit artikel verwijderd.

Raadpleeg de [documentatie](/office365/enterprise/office-365-u-s-government-dod-endpoints)voor een volledige lijst van Verenigde Staten overheids-eind punten.

## <a name="azure-ad-pass-through-authentication"></a>Pass-Through-verificatie van Azure AD

In de volgende informatie wordt de implementatie van Pass-Through-verificatie en de Azure Government Cloud beschreven.

### <a name="allow-access-to-urls"></a>Toegang tot URL's toestaan

Voordat u de Pass-Through-verificatie agent implementeert, controleert u of er een firewall bestaat tussen uw servers en Azure AD. Als uw firewall of Proxy Domain Name System (DNS) geblokkeerde of veilige Program ma's toestaat, voegt u de volgende verbindingen toe.

> [!NOTE]
> De volgende richt lijnen zijn ook van toepassing op de installatie van de [Azure AD-toepassingsproxy-connector](../manage-apps/what-is-application-proxy.md) voor Azure Government omgevingen.

|URL |Hoe dat wordt gebruikt|
|-----|-----|
|&#42;. msappproxy.us</br>&#42;.servicebus.usgovcloudapi.net|De agent gebruikt deze Url's om te communiceren met de Azure AD-Cloud service. |
|`mscrl.microsoft.us:80` </br>`crl.microsoft.us:80` </br>`ocsp.msocsp.us:80` </br>`www.microsoft.us:80`| De agent gebruikt deze Url's om certificaten te verifiëren.|
|login.windows.us </br>secure.aadcdn.microsoftonline-p.com </br>&#42;. microsoftonline.us </br>&#42;. microsoftonline-p.us </br>&#42;. msauth.net </br>&#42;. msauthimages.net </br>&#42;. msecnd.net</br>&#42;. msftauth.net </br>&#42;. msftauthimages.net</br>&#42;. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctldl.windowsupdate.us:80| De agent gebruikt deze Url's tijdens het registratie proces.

### <a name="install-the-agent-for-the-azure-government-cloud"></a>De agent voor de Azure Government Cloud installeren

Voer de volgende stappen uit om de agent te installeren voor de Azure Government Cloud:

1. Ga in de opdracht regel terminal naar de map die het uitvoer bare bestand bevat waarmee de agent wordt geïnstalleerd.
1. Voer de volgende opdrachten uit, waarmee wordt aangegeven dat de installatie is voor Azure Government.

   Voor Pass-Through-verificatie:

   ```
   AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
   ```

   Voor toepassings proxy:

   ```
   AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
   ```

## <a name="single-sign-on"></a>Eenmalige aanmelding

### <a name="set-up-your-azure-ad-connect-server"></a>Uw Azure AD Connect-server instellen

Als u Pass-Through-verificatie gebruikt als uw aanmeldings methode, is er geen aanvullende controle van vereisten vereist. Als u wachtwoord hash-synchronisatie gebruikt als uw aanmeldings methode en er een firewall is tussen Azure AD Connect en Azure AD, moet u ervoor zorgen dat:

- U gebruikt Azure AD Connect versie 1.1.644.0 of hoger.
- Als uw firewall of Proxy DNS-geblokkeerde of veilige Program ma's toestaat, voegt u de verbindingen toe aan de &#42;. msappproxy.us-Url's via poort 443.

  Als dat niet het geval is, verleent u toegang tot de IP-bereiken van het Azure-Data Center, die wekelijks worden bijgewerkt. Deze vereiste is alleen van toepassing wanneer u de functie inschakelt. Het is niet vereist voor de werkelijke gebruikers aanmeldingen.

### <a name="roll-out-seamless-single-sign-on"></a>Naadloze single Sign-On implementeren

U kunt met behulp van de volgende instructies de naadloze single Sign-On van Azure AD naar uw gebruikers samen vouwen. U begint door de Azure AD-URL toe te voegen `https://autologon.microsoft.us` aan de intranet zone-instellingen van alle of geselecteerde gebruikers met behulp van Groepsbeleid in Active Directory.

U moet ook de beleids instelling intranet zone inschakelen **updates op status balk via script toestaan via Groepsbeleid**.

## <a name="browser-considerations"></a>Browser overwegingen

### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (alle platformen)

In Mozilla Firefox wordt niet automatisch Kerberos-verificatie gebruikt. Elke gebruiker moet de URL van Azure AD hand matig toevoegen aan hun Firefox-instellingen door de volgende stappen te volgen:

1. Voer Firefox uit en voer **about: config**   in de adres balk in. Sluit alle meldingen die u mogelijk ziet.
1. Zoek naar de voor keuren **netwerk. Negotiate-auth. Trusted-uri's**   . Deze voor keur geeft een lijst van de sites die door Firefox worden vertrouwd voor Kerberos-verificatie.
1. Klik met de rechter muisknop op de naam van de voor keur en selecteer vervolgens **wijzigen**.
1. Voer `https://autologon.microsoft.us` in het vak in.
1. Selecteer **OK**   en open vervolgens de browser opnieuw.

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Micro soft Edge op basis van chroom (alle platformen)

Als u de  `AuthNegotiateDelegateAllowlist`   `AuthServerAllowlist`   beleids instellingen in uw omgeving hebt overschreven, moet u ervoor zorgen dat u de Azure AD-URL hieraan toevoegt `https://autologon.microsoft.us` .

### <a name="google-chrome-all-platforms"></a>Google Chrome (alle platformen)

Als u de  `AuthNegotiateDelegateWhitelist`   `AuthServerWhitelist`   beleids instellingen in uw omgeving hebt overschreven, moet u ervoor zorgen dat u de Azure AD-URL hieraan toevoegt `https://autologon.microsoft.us` .

## <a name="next-steps"></a>Volgende stappen

- [Pass-Through-verificatie](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
- [Single Sign-On](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites)
