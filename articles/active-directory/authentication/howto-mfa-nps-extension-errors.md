---
title: Problemen oplossen met de NPS-extensie voor Azure AD MFA-Azure Active Directory
description: Help-informatie over het oplossen van problemen met de NPS-extensie voor Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: fa3a2366f007ff1481e7c84f049e606586392037
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742949"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-ad-multi-factor-authentication"></a>Fout berichten oplossen vanuit de NPS-extensie voor Azure AD Multi-Factor Authentication

Als u problemen ondervindt met de NPS-extensie voor Azure AD Multi-Factor Authentication, kunt u dit artikel gebruiken om sneller een oplossing te bereiken. NPS-extensie Logboeken vindt u in Logboeken onder **aangepaste weer gaven**  >  **Server rollen**  >  **Services voor netwerk beleid en-toegang** op de server waarop de NPS-extensie is geïnstalleerd.

## <a name="troubleshooting-steps-for-common-errors"></a>Probleemoplossings stappen voor veelvoorkomende fouten

| Foutcode | Stappen voor probleemoplossing |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Neem contact op met de ondersteuning](#contact-microsoft-support)en vermeld de lijst met stappen voor het verzamelen van Logboeken. Geef zoveel informatie op als u wilt weten wat er is gebeurd vóór de fout, waaronder Tenant-ID en user principal name (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Er is mogelijk een probleem met de manier waarop het client certificaat is geïnstalleerd of gekoppeld aan uw Tenant. Volg de instructies in [Troubleshooting the MFA-extensie voor het](howto-mfa-nps-extension.md#troubleshooting) uitvoeren van problemen met client certificaten. |
| **ESTS_TOKEN_ERROR** | Volg de instructies in [Troubleshooting the MFA-extensie voor het](howto-mfa-nps-extension.md#troubleshooting) uitvoeren van problemen met client certificaten en ADAL-tokens. |
| **HTTPS_COMMUNICATION_ERROR** | De NPS-server kan geen reacties ontvangen van Azure AD MFA. Controleer of de firewalls zijn geopend in twee richtingen voor verkeer naar en van https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Op de server waarop de NPS-extensie wordt uitgevoerd, controleert u of u kunt bereiken  `https://adnotifications.windowsazure.com` en `https://login.microsoftonline.com/` . Als deze sites niet worden geladen, kunt u problemen met de connectiviteit op die server oplossen. |
| **NPS-extensie voor Azure AD MFA:** <br> De NPS-extensie voor Azure AD MFA voert alleen secundaire verificatie voor RADIUS-aanvragen uit in de AccessAccept-status. Er is een aanvraag ontvangen voor de gebruikers naam van de gebruiker met de reactie status AccessReject, aanvraag wordt genegeerd. | Deze fout weerspiegelt meestal een verificatie fout in AD of de NPS-server kan geen reacties ontvangen van Azure AD. Controleer of de firewalls zijn geopend voor verkeer naar en van `https://adnotifications.windowsazure.com` en `https://login.microsoftonline.com` met behulp van de poorten 80 en 443. Het is ook belang rijk om te controleren of op het tabblad inbel netwerk toegangs machtigingen de instelling is ingesteld op toegang beheren via NPS-netwerk beleid. Deze fout kan ook worden geactiveerd als aan de gebruiker geen licentie is toegewezen. |
| **REGISTRY_CONFIG_ERROR** | Er ontbreekt een sleutel in het REGI ster voor de toepassing. Dit kan zijn omdat het [Power shell-script](howto-mfa-nps-extension.md#install-the-nps-extension) niet is uitgevoerd na de installatie. Het fout bericht moet de ontbrekende sleutel bevatten. Zorg ervoor dat u beschikt over de sleutel onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Er ontbreekt een verplicht userName\Identifier kenmerk van de RADIUS-aanvraag. Controleren of NPS RADIUS-aanvragen ontvangt | Deze fout weerspiegelt meestal een installatie probleem. De NPS-extensie moet worden geïnstalleerd in NPS-servers die RADIUS-aanvragen kunnen ontvangen. NPS-servers die zijn geïnstalleerd als afhankelijkheden voor services als RDG en RRAS ontvangen geen RADIUS-aanvragen. De NPS-extensie werkt niet wanneer deze wordt geïnstalleerd via dergelijke installaties en er zijn fouten opgetreden omdat de gegevens van de verificatie aanvraag niet kunnen worden gelezen. |
| **REQUEST_MISSING_CODE** | Zorg ervoor dat het protocol voor wachtwoord versleuteling tussen de NPS-en NAS-servers de secundaire verificatie methode ondersteunt die u gebruikt. **Pap** ondersteunt alle verificatie methoden van Azure AD MFA in de Cloud: telefoon oproep, tekst bericht in één richting, melding van mobiele app en verificatie code voor mobiele apps. Ondersteuning voor telefonische en **EAP** -telefoon **gesprekken en mobiele** app-meldingen. |
| **USERNAME_CANONICALIZATION_ERROR** | Controleer of de gebruiker aanwezig is in uw on-premises Active Directory-exemplaar en of de NPS-service machtigingen heeft voor toegang tot de Directory. Als u vertrouwens relaties tussen forests gebruikt, [neemt u contact op met de ondersteuning](#contact-microsoft-support) voor verdere hulp. |

### <a name="alternate-login-id-errors"></a>Alternatieve aanmeldings-ID-fouten

| Foutcode | Foutbericht | Stappen voor probleemoplossing |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Fout: userObjectSid-zoek actie is mislukt | Controleer of de gebruiker aanwezig is in uw on-premises Active Directory-exemplaar. Als u vertrouwens relaties tussen forests gebruikt, [neemt u contact op met de ondersteuning](#contact-microsoft-support) voor verdere hulp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fout: kan de alternatieve LoginId niet opzoeken | Controleer of LDAP_ALTERNATE_LOGINID_ATTRIBUTE is ingesteld op een [geldig Active Directory-kenmerk](/windows/win32/adschema/attributes-all). <br><br> Als LDAP_FORCE_GLOBAL_CATALOG is ingesteld op True, of LDAP_LOOKUP_FORESTS is geconfigureerd met een niet-lege waarde, controleert u of u een globale catalogus hebt geconfigureerd en of het kenmerk AlternateLoginId is toegevoegd. <br><br> Als LDAP_LOOKUP_FORESTS is geconfigureerd met een niet-lege waarde, controleert u of de waarde juist is. Als er meer dan één Forestnaam is, moeten de namen worden gescheiden door punt komma's, geen spaties. <br><br> Als u het probleem niet kunt oplossen met deze stappen, [neemt u contact op met de ondersteuning](#contact-microsoft-support) voor meer hulp. |
| **ALTERNATE_LOGIN_ID_ERROR** | Fout: de alternatieve LoginId-waarde is leeg | Controleer of het kenmerk AlternateLoginId is geconfigureerd voor de gebruiker. |

## <a name="errors-your-users-may-encounter"></a>Fouten die uw gebruikers kunnen tegen komen

| Foutcode | Foutbericht | Stappen voor probleemoplossing |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | De aanroeper-Tenant heeft geen toegangs machtigingen om verificatie voor de gebruiker uit te voeren | Controleer of het domein van de Tenant en het domein van de user principal name (UPN) hetzelfde zijn. Zorg er bijvoorbeeld voor dat u user@contoso.com probeert te verifiëren bij de contoso-Tenant. De UPN vertegenwoordigt een geldige gebruiker voor de Tenant in Azure. |
| **AuthenticationMethodNotConfigured** | De opgegeven verificatie methode is niet geconfigureerd voor de gebruiker | Laat de gebruiker hun verificatie methoden toevoegen of controleren aan de hand van de instructies in [uw instellingen beheren voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | De opgegeven verificatie methode wordt niet ondersteund. | Verzamel alle logboeken die deze fout bevatten en [Neem contact op met de ondersteuning](#contact-microsoft-support). Wanneer u contact opneemt met ondersteuning, geeft u de gebruikers naam en de secundaire verificatie methode op die de fout heeft veroorzaakt. |
| **BecAccessDenied** | MSODS BEC-aanroep heeft toegang geweigerd, waarschijnlijk is de gebruikers naam niet gedefinieerd in de Tenant | De gebruiker is aanwezig in Active Directory on-premises, maar wordt niet gesynchroniseerd met Azure AD via AD Connect. Of de gebruiker ontbreekt voor de Tenant. Voeg de gebruiker toe aan Azure AD en laat ze hun verificatie methoden toevoegen volgens de instructies in [uw instellingen beheren voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** of **StrongAuthenticationServiceInvalidParameter** | Het telefoon nummer heeft een niet-herken bare indeling | Laat de gebruiker hun telefoon nummers voor de verificatie corrigeren. |
| **InvalidSession** | De opgegeven sessie is ongeldig of is mogelijk verlopen | Het volt ooien van de sessie heeft meer dan drie minuten geduurd. Controleer of de gebruiker de verificatie code invoert of reageert op de app-melding binnen drie minuten na het initiëren van de verificatie aanvraag. Als het probleem niet is opgelost, controleert u of er geen netwerk latenties zijn tussen de client, de NAS-server, de NPS-server en het Azure AD MFA-eind punt.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Er is geen standaard authenticatie methode voor de gebruiker geconfigureerd | Laat de gebruiker hun verificatie methoden toevoegen of controleren aan de hand van de instructies in [uw instellingen beheren voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-manage-settings.md). Controleer of de gebruiker een standaard verificatie methode heeft gekozen en die methode voor hun account heeft geconfigureerd. |
| **OathCodePinIncorrect** | Er is een onjuiste code en pincode ingevoerd. | Deze fout wordt niet verwacht in de NPS-extensie. Als uw gebruiker dit ondervindt, [neemt u contact op met de ondersteuning](#contact-microsoft-support) voor hulp bij het oplossen van problemen. |
| **ProofDataNotFound** | Er zijn geen bewijs gegevens geconfigureerd voor de opgegeven verificatie methode. | Laat de gebruiker een andere verificatie methode proberen of Voeg een of meer nieuwe verificatie methoden toe volgens de instructies in [uw instellingen beheren voor verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-manage-settings.md). [Neem contact op met de ondersteuning](#contact-microsoft-support)als de gebruiker deze fout blijft zien nadat u hebt bevestigd dat de verificatie methode juist is ingesteld. |
| **SMSAuthFailedWrongCodePinEntered** | Er is een onjuiste code en pincode ingevoerd. (OneWaySMS) | Deze fout wordt niet verwacht in de NPS-extensie. Als uw gebruiker dit ondervindt, [neemt u contact op met de ondersteuning](#contact-microsoft-support) voor hulp bij het oplossen van problemen. |
| **TenantIsBlocked** | Tenant is geblokkeerd | [Neem contact op](#contact-microsoft-support) met de ondersteuning met de *Tenant-id* van de Azure AD-eigenschappen pagina in het Azure Portal. |
| **UserNotFound** | De opgegeven gebruiker is niet gevonden | De Tenant is niet meer zichtbaar als actief in azure AD. Controleer of uw abonnement actief is en of u de vereiste apps voor de eerste partij hebt. Zorg er ook voor dat de Tenant in het certificaat onderwerp juist is, en dat het certificaat nog geldig is en is geregistreerd bij de Service-Principal. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Berichten die uw gebruikers kunnen tegen komen zonder fouten

Soms ontvangen uw gebruikers berichten van Multi-Factor Authentication omdat de verificatie aanvraag is mislukt. Dit zijn geen fouten in het product van de configuratie, maar zijn opzettelijke waarschuwingen waarin wordt uitgelegd waarom een verificatie aanvraag is geweigerd.

| Foutcode | Foutbericht | Aanbevolen stappen |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Onjuiste code entered\OATH code onjuist | De gebruiker heeft de verkeerde code ingevoerd. Probeer het opnieuw door een nieuwe code aan te vragen of u opnieuw aan te melden. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maximum toegestane code voor nieuwe poging bereikt | De gebruiker heeft de verificatie vraag te vaak mislukt. Afhankelijk van uw instellingen, moeten deze mogelijk nu door een beheerder worden gedeblokkeerd.  |
| **SMSAuthFailedWrongCodeEntered** | Onjuiste code ingevoerd/OTP van SMS-bericht onjuist | De gebruiker heeft de verkeerde code ingevoerd. Probeer het opnieuw door een nieuwe code aan te vragen of u opnieuw aan te melden. |

## <a name="errors-that-require-support"></a>Fouten waarvoor ondersteuning nodig is

Als u een van deze fouten tegen komt, raden wij u aan [contact op](#contact-microsoft-support) te nemen met de ondersteuning voor diagnostische Help. Er zijn geen standaard sets met stappen die deze fouten kunnen oplossen. Als u contact opneemt met de ondersteuning, moet u zoveel mogelijk informatie opnemen over de stappen die naar een fout hebben geleid en uw Tenant gegevens.

| Foutcode | Foutbericht |
| ---------- | ------------- |
| **InvalidParameter** | Aanvraag mag niet null zijn |
| **InvalidParameter** | ObjectId mag niet null of leeg zijn voor ReplicationScope:{0} |
| **InvalidParameter** | De lengte van CompanyName \{ 0} \ is langer dan de Maxi maal toegestane lengte {1} |
| **InvalidParameter** | UserPrincipalName mag niet null of leeg zijn |
| **InvalidParameter** | De gegeven TenantId heeft niet de juiste indeling |
| **InvalidParameter** | Sessie-id mag niet null of leeg zijn |
| **InvalidParameter** | Kan ProofData niet oplossen op basis van aanvraag of Msods. De ProofData is onbekend |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Volgende stappen

### <a name="troubleshoot-user-accounts"></a>Problemen met gebruikers accounts oplossen

Als uw gebruikers problemen ondervinden [met verificatie in twee stappen](../user-help/multi-factor-authentication-end-user-troubleshoot.md), helpen ze zelf problemen op te lossen.

### <a name="health-check-script"></a>Status controle script

Met het [Azure AD MFA-status controle script](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) voor de NPS-extensie wordt een eenvoudige status controle uitgevoerd bij het oplossen van problemen met de NPS-extensie. Voer het script uit en kies optie 3.

### <a name="contact-microsoft-support"></a>Contact opnemen met Microsoft Ondersteuning

Als u meer hulp nodig hebt, neemt u contact op met een ondersteunings medewerker via [ondersteuning voor Azure multi-factor Authentication-Server](https://support.microsoft.com/oas/default.aspx?prid=14947). Wanneer u contact met ons opneemt, is het handig als u zoveel mogelijk informatie over uw probleem kunt opnemen. Informatie die u kunt opgeven bevat de pagina waar u de fout hebt gezien, de specifieke fout code, de specifieke sessie-ID, de ID van de gebruiker die de fout heeft gezien en logboeken voor fout opsporing.

Als u Logboeken voor fout opsporing wilt verzamelen voor ondersteunings diagnostiek, gebruikt u de volgende stappen op de NPS-server:

1. Open de REGI ster-editor en blader naar HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa Stel **VERBOSE_LOG** in op **waar**
2. Open een beheerders opdracht prompt en voer deze opdrachten uit:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reproduceer het probleem

4. Stop de tracering met de volgende opdrachten:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Open de REGI ster-editor en blader naar HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa Stel **VERBOSE_LOG** in op **Onwaar**
6. De inhoud van de map C:\NPS en het gezipte bestand koppelen aan de ondersteunings aanvraag.