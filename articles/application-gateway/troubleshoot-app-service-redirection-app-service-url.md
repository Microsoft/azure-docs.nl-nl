---
title: Problemen met omleiding naar App Service-URL oplossen
titleSuffix: Azure Application Gateway
description: Dit artikel bevat informatie over het oplossen van het omleidingsprobleem wanneer Azure Application Gateway wordt gebruikt met Azure App Service
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.openlocfilehash: 6aad1cf1269a7c3dc082482c39fdc4a079fc3240
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514883"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Problemen App Service in Application Gateway

Meer informatie over het vaststellen en oplossen van problemen die kunnen Azure App Service wanneer een back-enddoel wordt gebruikt met Azure Application Gateway.

## <a name="overview"></a>Overzicht

In dit artikel leert u hoe u de volgende problemen kunt oplossen:

* De URL van de App Service wordt weergegeven in de browser wanneer er een omleiding is.
* Het ARRAffinity-cookiedomein van de app-service is ingesteld op de hostnaam van de app-service, example.azurewebsites.net, in plaats van de oorspronkelijke host.

Wanneer een back-endtoepassing een omleidingsreactie verzendt, kunt u de client omleiden naar een andere URL dan de URL die is opgegeven door de back-endtoepassing. Mogelijk wilt u dit doen wanneer een app-service wordt gehost achter een toepassingsgateway en de client een omleiding naar het relatieve pad vereist. Een voorbeeld is een omleiding van contoso.azurewebsites.net/path1 naar contoso.azurewebsites.net/path2. 

Wanneer de app-service een omleidingsreactie verzendt, gebruikt deze dezelfde hostnaam in de locatieheader van het antwoord als de naam in de aanvraag die wordt ontvangen van de toepassingsgateway. De client doet de aanvraag bijvoorbeeld rechtstreeks naar contoso.azurewebsites.net/path2 in plaats van via de toepassingsgateway te contoso.com/path2. U wilt de toepassingsgateway niet omzeilen.

Dit probleem kan de volgende hoofdredenen hebben:

- U hebt omleiding geconfigureerd in uw app-service. Omleiding kan net zo eenvoudig zijn als het toevoegen van een schuine streep aan de aanvraag.
- U hebt Azure Active Directory verificatie, waardoor de omleiding wordt veroorzaakt.

Wanneer u app-services achter een toepassingsgateway gebruikt, verschilt de domeinnaam die is gekoppeld aan de toepassingsgateway (example.com) van de domeinnaam van de app-service (bijvoorbeeld example.azurewebsites.net). De domeinwaarde voor de ARRAffinity-cookie die door de app-service is ingesteld, bevat de example.azurewebsites.net domeinnaam, wat niet wenselijk is. De oorspronkelijke hostnaam, example.com, moet de domeinnaamwaarde in de cookie zijn.

## <a name="sample-configuration"></a>Voorbeeldconfiguratie

- HTTP-listener: Basic of multi-site
- Back-endadresgroep: App Service
- HTTP-instellingen: **Kies Hostnaam uit Back-mailadres** ingeschakeld
- Test: **Kies Hostnaam uit HTTP-instellingen** ingeschakeld

## <a name="cause"></a>Oorzaak

App Service is een multitenant-service, dus wordt de hostheader in de aanvraag gebruikt om de aanvraag naar het juiste eindpunt te sturen. De standaarddomeinnaam van App Services, *.azurewebsites.net (bijvoorbeeld contoso.azurewebsites.net), verschilt van de domeinnaam van de toepassingsgateway (bijvoorbeeld contoso.com). 

De oorspronkelijke aanvraag van de client heeft de domeinnaam van de toepassingsgateway, contoso.com, als hostnaam. U moet de toepassingsgateway configureren om de hostnaam in de oorspronkelijke aanvraag te wijzigen in de hostnaam van de App Service wanneer deze de aanvraag doorspoort naar de back-end van de App Service. Gebruik de schakelknop **Hostnaam kiezen uit back-endadres** in de CONFIGURATIE van de HTTP-instelling van de toepassingsgateway. Gebruik de schakelknop **Hostnaam kiezen uit HTTP-instellingen voor back-enden** in de statustestconfiguratie.



![Toepassingsgateway wijzigt hostnaam](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Wanneer de app-service een omleiding doet, wordt de overschrijvende hostnaam contoso.azurewebsites.net in de locatieheader gebruikt in plaats van de oorspronkelijke hostnaam contoso.com, tenzij anders geconfigureerd. Controleer de volgende voorbeeldheaders voor aanvragen en antwoorden.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
In het vorige voorbeeld ziet u dat de antwoordheader de statuscode 301 heeft voor omleiding. De locatieheader heeft de hostnaam van de App Service in plaats van de oorspronkelijke hostnaam `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Oplossing: De locatieheader herschrijven

Stel de hostnaam in de locatieheader in op de domeinnaam van de toepassingsgateway. Hiervoor maakt u een [herschrijfregel](./rewrite-http-headers.md) met een voorwaarde die evalueert of de locatieheader in het antwoord azurewebsites.net. Er moet ook een actie worden ondernomen om de locatieheader te herschrijven, om de hostnaam van de toepassingsgateway te krijgen. Zie instructies voor het herschrijven van de [locatieheader voor meer informatie.](./rewrite-http-headers.md#modify-a-redirection-url)

> [!NOTE]
> De ondersteuning voor het herschrijven van HTTP-headers is alleen beschikbaar voor de [Standard_v2 en WAF_v2 SKU](./application-gateway-autoscaling-zone-redundant.md) van Application Gateway. U wordt [aangeraden te migreren naar v2](./migrate-v1-v2.md) voor herschrijven van [headers](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) en andere geavanceerde mogelijkheden die beschikbaar zijn met v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatieve oplossing: Een aangepaste domeinnaam gebruiken

Het App Service van Custom Domain is een andere oplossing om het verkeer altijd om te leiden naar Application Gateway domeinnaam van de gebruiker ( `www.contoso.com` in ons voorbeeld). Deze configuratie fungeert ook als oplossing voor het cookieprobleem ARR-affiniteit. Standaard wordt het cookiedomein ARRAffinity ingesteld op de standaardhostnaam (example.azurewebsites.net) van de App Service in plaats van de domeinnaam van de Application Gateway. Daarom weigert de browser in dergelijke gevallen de cookie vanwege het verschil in de domeinnamen van de aanvraag en de cookie.

U kunt de opgegeven methode volgen voor problemen met omleiding en het cookiedomein van ARRAffinity. Voor deze methode moet u toegang hebben tot de DNS-zone van uw aangepaste domein.

**Stap 1:** stel een Custom Domain in App Service en controleer het eigendom van het domein door de [CNAME-& TXT DNS-records toe te voegen.](../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id)
De records zien er ongeveer als
-  `www.contoso.com` IN CNAME `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` IN TXT " `<verification id string>` "


**Stap 2:** de CNAME-record in de vorige stap was alleen nodig voor de domeinverificatie. Uiteindelijk hebben we het verkeer nodig om via een Application Gateway. U kunt dus `www.contoso.com` 's CNAME nu wijzigen zodat deze naar Application Gateway FQDN van de Application Gateway wijzen. Als u een FQDN voor uw Application Gateway, gaat u naar de resource van het openbare IP-adres en wijst u er een 'DNS-naamlabel' voor toe. De bijgewerkte CNAME-record moet er nu uitzien als 
-  `www.contoso.com` IN CNAME `contoso.eastus.cloudapp.azure.com`


**Stap 3:** schakel 'Hostnaam kiezen uit back-endadres' uit voor de bijbehorende HTTP-instelling.

Gebruik in PowerShell de schakelknop `-PickHostNameFromBackendAddress` niet in de opdracht `Set-AzApplicationGatewayBackendHttpSettings` .


**Stap 4:** stel een aangepaste statustest met het veld Host in als aangepast of standaarddomein van de App Service om de back-end te bepalen als in orde en operationeel verkeer.

Gebruik in PowerShell niet de schakelknop in de opdracht en gebruik het aangepaste of standaarddomein van de App Service in de `-PickHostNameFromBackendHttpSettings` `Set-AzApplicationGatewayProbeConfig` switch -HostName van de test.

Als u de vorige stappen wilt implementeren met behulp van PowerShell voor een bestaande installatie, gebruikt u het volgende PowerShell-voorbeeldscript. Merk op dat we de schakelopties **-PickHostname** niet hebben gebruikt in de configuratie van de test- en HTTP-instellingen.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Volgende stappen

Als het probleem niet is opgelost met de voorgaande stappen, opent u een [ondersteuningsticket](https://azure.microsoft.com/support/options/).
