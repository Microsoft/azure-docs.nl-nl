---
title: Probleem bij het installeren van de connector voor de toepassingsproxyagent
description: Problemen oplossen die u mogelijk ondervindt bij het installeren van de agent connector voor toepassings proxy voor Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 0f76f03883746b6f4b87bb817f8adde850ed28b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99253661"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Probleem bij het installeren van de connector voor de toepassingsproxyagent

Microsoft Azure Active Directory Application proxy connector is een intern domein onderdeel dat uitgaande verbindingen gebruikt om verbinding te maken met het eind punt beschik bare Cloud naar het interne domein.

## <a name="general-problem-areas-with-connector-installation"></a>Algemene probleem gebieden met installatie van connector

Wanneer de installatie van een connector mislukt, is de hoofd oorzaak meestal een van de volgende gebieden. **Als u een probleem wilt oplossen, moet u de connector opnieuw opstarten.**

1.  **Connectiviteit** : om een geslaagde installatie te volt ooien, moet de nieuwe connector de eigenschappen van de vertrouwens relatie in de toekomst registreren en instellen. Dit doet u door verbinding te maken met de Azure Active Directory-toepassingsproxy Cloud service.

2.  Het maken van een **vertrouwens relatie** : de nieuwe connector maakt een zelfondertekend certificaat en registreert zich bij de Cloud service.

3.  **Verificatie van de beheerder** : tijdens de installatie moet de gebruiker beheerders referenties opgeven om de installatie van de connector te volt ooien.

> [!NOTE]
> De installatie logboeken van de connector vindt u in de map% TEMP% en biedt aanvullende informatie over wat een installatie fout veroorzaakt.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Controleer de verbinding met de Cloud toepassings proxy-service en de micro soft-aanmeldings pagina

**Doel stelling:** Controleer of de connector computer verbinding kan maken met het eind punt van de toepassings proxy en de aanmeldings pagina van micro soft.

1.  Voer op de connector server een poort test uit met behulp van [Telnet](/windows-server/administration/windows-commands/telnet) of een ander hulp programma voor poort testen om te controleren of de poorten 443 en 80 zijn geopend.

2.  Als een van deze poorten niet is geslaagd, controleert u of de firewall of de back-end-proxy toegang heeft tot de vereiste domeinen en poorten Zie, [uw on-premises omgeving voorbereiden](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Open een browser (tabblad afzonderlijk) en ga naar de volgende webpagina:, Controleer of `https://login.microsoftonline.com` u zich kunt aanmelden bij deze pagina.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-certificate"></a>De ondersteuning voor de machine en back-end-onderdelen controleren voor het certificaat van de toepassings proxy

**Doel stelling:** Controleer of de connector computer, back-end proxy en firewall het certificaat kunnen ondersteunen dat door de connector is gemaakt voor toekomstige vertrouwens relaties en of het certificaat geldig is.

>[!NOTE]
>De connector probeert een SHA512 gebruikt-certificaat te maken dat wordt ondersteund door TLS 1.2. Als de computer of de back-end firewall en proxy geen ondersteuning bieden voor TLS 1.2, mislukt de installatie.
>
>

**Controleer de vereiste vereisten:**

1.  Controleren of de computer TLS 1.2 ondersteunt: alle Windows-versies na 2012 R2 moeten TLS 1,2 ondersteunen. Als uw connector computer van een versie van 2012 R2 of eerder is, controleert u of de volgende Kb's zijn geïnstalleerd op de computer: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Neem contact op met uw netwerk beheerder en vraag om te controleren of de back-end-proxy en firewall SHA512 gebruikt niet blok keren voor uitgaand verkeer.

**Het client certificaat verifiëren:**

Controleer de vinger afdruk van het huidige client certificaat. Het certificaat archief kan worden gevonden in `%ProgramData%\microsoft\Microsoft AAD Application Proxy Connector\Config\TrustSettings.xml` .

```
<?xml version="1.0" encoding="utf-8"?>
<ConnectorTrustSettingsFile xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <CloudProxyTrust>
    <Thumbprint>4905CC64B2D81BBED60962ECC5DCF63F643CCD55</Thumbprint>
    <IsInUserStore>false</IsInUserStore>
  </CloudProxyTrust>
</ConnectorTrustSettingsFile>
```

De mogelijke **IsInUserStore** waarden zijn **True** en **False**. Als de waarde **True** is, wordt het automatisch verlengde certificaat opgeslagen in de persoonlijke container in het certificaat archief van de netwerk service. De waarde **False** betekent dat het client certificaat is gemaakt tijdens de installatie of registratie die is gestart door Register-AppProxyConnector opdracht en dat het is opgeslagen in de persoonlijke container in het certificaat archief van de lokale computer.

Als de waarde **True** is, volgt u deze stappen om het certificaat te verifiëren:
1. [PsTools.zip](/sysinternals/downloads/pstools) downloaden
2. Extraheer [PsExec](/sysinternals/downloads/psexec) uit het pakket en voer **PsExec-i-u ' NT Authority\Network service ' uit cmd.exe** uit vanaf een opdracht prompt met verhoogde bevoegdheid.
3. **Certmgr. msc** uitvoeren in de zojuist verschenen opdracht prompt
4. Vouw in de beheer console de persoonlijke container uit en klik op certificaten
5. Zoek het certificaat dat is uitgegeven door **connectorregistrationca.msappproxy.net**

Als de waarde **False** is, volgt u deze stappen om het certificaat te verifiëren:
1. Voer **certlm. msc** uit
2. Vouw in de beheer console de persoonlijke container uit en klik op certificaten
3. Zoek het certificaat dat is uitgegeven door **connectorregistrationca.msappproxy.net**

**Het client certificaat vernieuwen:**

Als een connector gedurende enkele maanden niet is verbonden met de service, zijn de certificaten mogelijk verouderd. Fout bij het vernieuwen van het certificaat leidt naar een verlopen certificaat. Hierdoor werkt de connector service niet meer. De gebeurtenis 1000 wordt vastgelegd in het beheer logboek van de connector:

Het opnieuw registreren van de connector is mislukt: het vertrouwens certificaat van de connector is verlopen. Voer de Power shell-cmdlet uit Register-AppProxyConnector op de computer waarop de connector wordt uitgevoerd om de connector opnieuw te registreren.

In dit geval moet u de connector verwijderen en opnieuw installeren om de registratie te activeren of u kunt de volgende Power shell-opdrachten uitvoeren:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

Zie [een script voor installatie zonder toezicht maken voor de Azure AD-toepassingsproxy-connector voor](./application-proxy-register-connector-powershell.md) meer informatie over de Register-AppProxyConnector opdracht.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Controleren of beheerder wordt gebruikt voor het installeren van de connector

**Doel stelling:** Controleer of de gebruiker die de connector probeert te installeren, een beheerder met de juiste referenties is. Op dit moment moet de gebruiker ten minste een toepassings beheerder zijn om de installatie te kunnen volt ooien.

**Controleren of de referenties juist zijn:**

Verbinding maken met `https://login.microsoftonline.com` dezelfde referenties en deze gebruiken. Zorg ervoor dat de aanmelding is geslaagd. U kunt de gebruikersrol controleren door naar **Azure Active Directory**  - &gt; **gebruikers en groepen**  - &gt; **alle gebruikers** te gaan. 

Selecteer uw gebruikers account en vervolgens ' Directory-rol ' in het resulterende menu. Controleer of de geselecteerde rol ' toepassings beheerder ' is. Als u in deze stappen geen toegang hebt tot een van de pagina's, hebt u niet de vereiste rol.

## <a name="next-steps"></a>Volgende stappen
[Azure AD-toepassingsproxy-connectors begrijpen](application-proxy-connectors.md)
