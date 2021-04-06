---
title: Problemen met toepassings proxy oplossen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u fouten in azure AD-toepassingsproxy oplost.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 06/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: cb8fb0e194b4c43b5e247f2ea5d1e38d924591db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257960"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Problemen en foutberichten met Application Proxy oplossen

Bij het oplossen van problemen met toepassings proxy raden we u aan om te beginnen met het controleren van de stroom voor probleem oplossing, problemen met de connector van de [toepassings proxy op te lossen](application-proxy-debug-connectors.md), om te bepalen of toepassings proxy connectors correct zijn Als u nog steeds problemen hebt met het maken van verbinding met de toepassing, volgt u de stroom problemen oplossen in toepassings [problemen met toepassings proxy](application-proxy-debug-apps.md).

Als er fouten optreden bij het openen van een gepubliceerde toepassing of bij het publiceren van toepassingen, controleert u de volgende opties om te zien of Microsoft Azure AD toepassings proxy correct werkt:

* Open de console van Windows-Services. Controleer of de **micro soft Aad Application proxy connector** -service is ingeschakeld en wordt uitgevoerd. Mogelijk wilt u ook de pagina eigenschappen van de toepassings proxy service bekijken, zoals wordt weer gegeven in de volgende afbeelding:  
  ![Micro soft AAD Application proxy connector venster Eigenschappen scherm afbeelding](./media/application-proxy-troubleshoot/connectorproperties.png)
* Open Logboeken en zoek naar connector gebeurtenissen van toepassings proxy in **toepassingen en services**  >  **micro soft**  >  **AadApplicationProxy**-  >  **connector**-  >  **beheerder** Logboeken.
* Als dat nodig is, kunt u meer gedetailleerde logboeken vinden door [de Application proxy Connector-sessie Logboeken in te scha kelen](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>De pagina wordt niet correct weer gegeven
Er zijn mogelijk problemen met de rendering van uw toepassing of werken onjuist zonder specifieke fout berichten te ontvangen. Dit kan gebeuren als u het pad naar het artikel hebt gepubliceerd, maar de toepassing vereist inhoud die buiten dat pad bestaat.

Als u bijvoorbeeld het pad publiceert `https://yourapp/app` terwijl de toepassing installatie kopieën aanroept `https://yourapp/media` , worden deze niet weer gegeven. Zorg ervoor dat u de toepassing publiceert met het pad naar het hoogste niveau dat u nodig hebt om alle relevante inhoud op te nemen. In dit voor beeld zou het zijn `http://yourapp/` .

## <a name="connector-errors"></a>Connector fouten

Als de registratie mislukt tijdens de installatie van de wizard connector, zijn er twee manieren om de reden voor de fout weer te geven. Kijk in het gebeurtenis logboek onder **toepassingen en services Logs\Microsoft\AadApplicationProxy\Connector\Admin** of voer de volgende Windows Power shell-opdracht uit:

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

Wanneer u de connector fout in het gebeurtenis logboek vindt, gebruikt u deze tabel met veelvoorkomende fouten om het probleem op te lossen:

| Fout | Aanbevolen stappen |
| ----- | ----------------- |
| Registratie van connector is mislukt: Controleer of u de toepassings proxy hebt ingeschakeld in de Azure Beheerportal en of u de gebruikers naam en het wacht woord van uw Active Directory correct hebt ingevoerd. Fout: er zijn een of meer fouten opgetreden. | Als u het registratie venster hebt gesloten zonder u aan te melden bij Azure AD, voert u de wizard connector opnieuw uit en registreert u de connector. <br><br> Als het registratie venster wordt geopend en vervolgens onmiddellijk wordt gesloten zonder dat u zich hoeft aan te melden, wordt deze fout waarschijnlijk weer gegeven. Deze fout treedt op wanneer het systeem een netwerk fout bevat. Zorg ervoor dat het mogelijk is om vanuit een browser verbinding te maken met een open bare website en dat de poorten zijn geopend zoals is opgegeven in de vereisten voor de [toepassings proxy](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Er wordt een duidelijke fout weer gegeven in het registratie venster. Kan niet door gaan | Als u deze fout ziet en het venster wordt gesloten, hebt u de verkeerde gebruikers naam of het juiste wacht woord opgegeven. Probeer het opnieuw. |
| Registratie van connector is mislukt: Controleer of u de toepassings proxy hebt ingeschakeld in de Azure Beheerportal en of u de gebruikers naam en het wacht woord van uw Active Directory correct hebt ingevoerd. Fout: ' AADSTS50059: geen Tenant gegevens gevonden in de aanvraag of impliciet door de verstrekte referenties en de zoek opdracht op de Service Principal URI is mislukt. | U probeert u aan te melden met een micro soft-account en niet een domein dat deel uitmaakt van de organisatie-ID van de map waartoe u toegang wilt krijgen. Zorg ervoor dat de beheerder deel uitmaakt van dezelfde domein naam als het Tenant domein, bijvoorbeeld als het Azure AD-domein contoso.com is, de beheerder moet zijn admin@contoso.com . |
| Kan het huidige uitvoerings beleid voor het uitvoeren van Power shell-scripts niet ophalen. | Als de installatie van de connector mislukt, controleert u of het Power shell-uitvoerings beleid niet is uitgeschakeld. <br><br>1. Open de groepsbeleid editor.<br>2. Ga naar **computer configuratie**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **Windows Power shell** en dubbel klik op **script uitvoering inschakelen**.<br>3. het uitvoerings beleid kan worden ingesteld op **niet geconfigureerd** of **ingeschakeld**. Als deze optie is **ingeschakeld**, moet u ervoor zorgen dat het uitvoerings beleid onder opties is ingesteld op het **toestaan van lokale scripts en externe ondertekende scripts** of voor het **toestaan van alle scripts**. |
| Connector kan de configuratie niet downloaden. | Het client certificaat van de connector, dat wordt gebruikt voor authenticatie, is verlopen. Dit kan ook gebeuren als de connector achter een proxy is geïnstalleerd. In dit geval heeft de connector geen toegang tot internet en kan deze geen toepassingen leveren aan externe gebruikers. Vernieuw de vertrouwens relatie hand matig met de `Register-AppProxyConnector` cmdlet in Windows Power shell. Als uw connector zich achter een proxy bevindt, moet u Internet toegang verlenen tot de connector-accounts ' netwerk services ' en ' lokaal systeem '. Dit kan worden bereikt door hen toegang te verlenen tot de proxy of door deze in te stellen om de proxy over te slaan. |
| Registratie van connector is mislukt: Zorg ervoor dat u een toepassings beheerder bent van uw Active Directory om de connector te registreren. Fout: de registratie aanvraag is geweigerd. | De alias waarmee u zich wilt aanmelden, is geen beheerder van dit domein. De connector is altijd geïnstalleerd voor de directory die eigenaar is van het domein van de gebruiker. Zorg ervoor dat het beheerders account waarmee u zich probeert aan te melden, ten minste machtigingen voor de toepassings beheerder heeft voor de Azure AD-Tenant. |
| De connector kan geen verbinding maken met de service vanwege netwerk problemen. De connector heeft geprobeerd toegang te krijgen tot de volgende URL. | De connector kan geen verbinding maken met de Cloud service van de toepassings proxy. Dit kan gebeuren als u een firewall regel hebt die de verbinding blokkeert. Zorg ervoor dat u toegang hebt tot de juiste poorten en URL'S die worden vermeld in de vereisten voor de [toepassings proxy](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Kerberos-fouten

In deze tabel worden de meest voorkomende fouten beschreven die afkomstig zijn van Kerberos Setup en configuratie, en worden suggesties voor het oplossen van problemen opgenomen.

| Fout | Aanbevolen stappen |
| ----- | ----------------- |
| Kan het huidige uitvoerings beleid voor het uitvoeren van Power shell-scripts niet ophalen. | Als de installatie van de connector mislukt, controleert u of het Power shell-uitvoerings beleid niet is uitgeschakeld.<br><br>1. Open de groepsbeleid editor.<br>2. Ga naar **computer configuratie**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **Windows Power shell** en dubbel klik op **script uitvoering inschakelen**.<br>3. het uitvoerings beleid kan worden ingesteld op **niet geconfigureerd** of **ingeschakeld**. Als deze optie is **ingeschakeld**, moet u ervoor zorgen dat het uitvoerings beleid onder opties is ingesteld op het **toestaan van lokale scripts en externe ondertekende scripts** of voor het **toestaan van alle scripts**. |
| 12008-Azure AD heeft het maximum aantal toegestane Kerberos-verificatie pogingen voor de back-endserver overschreden. | Deze fout kan duiden op een onjuiste configuratie tussen Azure AD en de back-end-toepassings server, of een probleem in de tijd-en datum configuratie op beide computers. De back-endserver heeft het Kerberos-ticket geweigerd dat is gemaakt door Azure AD. Controleer of Azure AD en de back-end-toepassings server correct zijn geconfigureerd. Zorg ervoor dat de configuratie van de tijd en de datum op de Azure AD-en de back-end-toepassings server worden gesynchroniseerd. |
| 13016-Azure AD kan een Kerberos-ticket namens de gebruiker niet ophalen omdat er geen UPN is in het rand token of in de cookie voor toegang. | Er is een probleem met de STS-configuratie. Herstel de UPN-claim configuratie in de STS. |
| 13019-Azure AD kan een Kerberos-ticket namens de gebruiker niet ophalen vanwege de volgende algemene API-fout. | Deze gebeurtenis kan duiden op een onjuiste configuratie tussen Azure AD en de domein controller Server, of een probleem in de tijd-en datum configuratie op beide computers. De domein controller heeft het Kerberos-ticket geweigerd dat is gemaakt door Azure AD. Controleer of Azure AD en de back-end-toepassings server correct zijn geconfigureerd, met name de SPN-configuratie. Zorg ervoor dat het domein van Azure AD lid is van hetzelfde domein als de domein controller om ervoor te zorgen dat de domein controller een vertrouwens relatie met Azure AD tot stand brengt. Zorg ervoor dat de configuratie van de tijd en de datum op de Azure AD en de domein controller worden gesynchroniseerd. |
| 13020-Azure AD kan een Kerberos-ticket namens de gebruiker niet ophalen omdat de SPN van de back-endserver niet is gedefinieerd. | Deze gebeurtenis kan duiden op een onjuiste configuratie tussen Azure AD en de domein controller Server, of een probleem in de tijd-en datum configuratie op beide computers. De domein controller heeft het Kerberos-ticket geweigerd dat is gemaakt door Azure AD. Controleer of Azure AD en de back-end-toepassings server correct zijn geconfigureerd, met name de SPN-configuratie. Zorg ervoor dat het domein van Azure AD lid is van hetzelfde domein als de domein controller om ervoor te zorgen dat de domein controller een vertrouwens relatie met Azure AD tot stand brengt. Zorg ervoor dat de configuratie van de tijd en de datum op de Azure AD en de domein controller worden gesynchroniseerd. |
| 13022-Azure AD kan de gebruiker niet verifiëren omdat de back-endserver reageert op Kerberos-verificatie pogingen met een HTTP 401-fout. | Deze gebeurtenis kan duiden op een onjuiste configuratie tussen Azure AD en de back-end-toepassings server, of een probleem in de tijd-en datum configuratie op beide computers. De back-endserver heeft het Kerberos-ticket geweigerd dat is gemaakt door Azure AD. Controleer of Azure AD en de back-end-toepassings server correct zijn geconfigureerd. Zorg ervoor dat de configuratie van de tijd en de datum op de Azure AD-en de back-end-toepassings server worden gesynchroniseerd. Zie [problemen met Kerberos-beperkte overdracht configureren voor toepassings proxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)voor meer informatie.  |

## <a name="end-user-errors"></a>Eind gebruikers fouten

In deze lijst worden fouten behandeld die uw eind gebruikers kunnen tegen komen wanneer ze proberen toegang te krijgen tot de app. 

| Fout | Aanbevolen stappen |
| ----- | ----------------- |
| De website kan de pagina niet weer geven. | De gebruiker krijgt deze fout mogelijk bij het openen van de app die u hebt gepubliceerd als de toepassing een IWA-toepassing is. De gedefinieerde SPN voor deze toepassing is mogelijk onjuist. Zorg ervoor dat de SPN die is geconfigureerd voor deze toepassing juist is voor IWA-apps. |
| De website kan de pagina niet weer geven. | De gebruiker krijgt deze fout mogelijk bij het openen van de app die u hebt gepubliceerd als de toepassing een OWA-toepassing is. Dit kan een van de volgende oorzaken hebben:<br><li>De gedefinieerde SPN voor deze toepassing is onjuist. Zorg ervoor dat de SPN die is geconfigureerd voor deze toepassing juist is.</li><li>De gebruiker die toegang probeerde te krijgen tot de toepassing, gebruikt een Microsoft-account in plaats van de juiste zakelijke account om zich aan te melden, of de gebruiker is een gast gebruiker. Zorg ervoor dat de gebruiker zich aanmeldt met het bedrijfs account dat overeenkomt met het domein van de gepubliceerde toepassing. Gebruikers van micro soft-accounts en gast hebben geen toegang tot IWA-toepassingen.</li><li>De gebruiker die de toepassing probeerde te openen, is niet op de juiste wijze gedefinieerd voor deze toepassing aan de on-premises kant. Zorg ervoor dat deze gebruiker de juiste machtigingen heeft, zoals gedefinieerd voor deze back-end-toepassing op de on-premises computer. |
| Deze zakelijke app is niet toegankelijk. U bent niet gemachtigd om toegang te krijgen tot deze toepassing. Autorisatie is mislukt. Zorg ervoor dat u de gebruiker toegang tot deze toepassing toewijst. | Uw gebruiker krijgt deze fout mogelijk bij het openen van de app die u hebt gepubliceerd als ze micro soft-accounts gebruiken in plaats van hun bedrijfs account om zich aan te melden. Gast gebruikers kunnen deze fout ook ontvangen. Gebruikers en gasten van een micro soft-account hebben geen toegang tot IWA-toepassingen. Zorg ervoor dat de gebruiker zich aanmeldt met het bedrijfs account dat overeenkomt met het domein van de gepubliceerde toepassing.<br><br>U hebt de gebruiker mogelijk niet toegewezen voor deze toepassing. Ga naar het tabblad **toepassing** en wijs deze gebruiker of gebruikers groep toe aan deze toepassing onder **gebruikers en groepen**. |
| Deze zakelijke app kan momenteel niet worden geopend. Probeer het later opnieuw... Time-out van de connector. | Uw gebruiker krijgt deze fout mogelijk bij het openen van de app die u hebt gepubliceerd als deze niet op de juiste wijze is gedefinieerd voor deze toepassing. Zorg ervoor dat uw gebruikers over de juiste machtigingen beschikken die zijn gedefinieerd voor deze back-end-toepassing op de on-premises computer. |
| Deze zakelijke app is niet toegankelijk. U bent niet gemachtigd om toegang te krijgen tot deze toepassing. Autorisatie is mislukt. Zorg ervoor dat de gebruiker een licentie voor Azure Active Directory Premium heeft. | De gebruiker krijgt deze fout mogelijk bij het openen van de app die u hebt gepubliceerd als deze niet expliciet is toegewezen aan een Premium-licentie door de beheerder van de abonnee. Ga naar het tabblad Active Directory **licenties** van de abonnee en controleer of aan deze gebruiker of gebruikers groep een Premium-licentie is toegewezen. |
| Een server met de opgegeven hostnaam is niet gevonden. | De gebruiker krijgt deze fout mogelijk bij het openen van de app die u hebt gepubliceerd als het aangepaste domein van de toepassing niet juist is geconfigureerd. Zorg ervoor dat u een certificaat voor het domein hebt geüpload en de DNS-record correct hebt geconfigureerd door de stappen te volgen in [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md) |
|Verboden: deze zakelijke app is niet toegankelijk of de gebruiker kan niet worden geautoriseerd. Zorg ervoor dat de gebruiker is gedefinieerd in uw on-premises AD en dat de gebruiker toegang heeft tot de app in uw on-premises AD. | Dit kan een probleem zijn met de toegang tot autorisatie-informatie, Zie [sommige toepassingen en api's hebben toegang tot autorisatie gegevens voor account objecten nodig]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information). Voeg in een kort gezegd het account van de app proxy connector machine toe aan de groep ' Windows-autorisatie toegangsgroep ' Builtin domein groep om op te lossen. |

## <a name="my-error-wasnt-listed-here"></a>Mijn fout is hier niet vermeld

Als u een fout of probleem ondervindt met Azure AD-toepassingsproxy dat niet wordt vermeld in deze probleemoplossings handleiding, willen we het graag hierover horen. Stuur een e-mail naar ons [feedback team](mailto:aadapfeedback@microsoft.com) met de details van de fout die u hebt aangetroffen.

## <a name="see-also"></a>Zie ook
* [Toepassings proxy inschakelen voor Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Toepassingen publiceren met toepassingsproxy (Engelstalig artikel)](application-proxy-add-on-premises-application.md)
* [Eenmalige aanmelding inschakelen](application-proxy-configure-single-sign-on-with-kcd.md)
* [Voorwaardelijke toegang inschakelen](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
