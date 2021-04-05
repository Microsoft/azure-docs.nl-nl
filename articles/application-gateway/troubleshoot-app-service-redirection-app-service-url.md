---
title: Problemen met omleiding naar App Service-URL oplossen
titleSuffix: Azure Application Gateway
description: Dit artikel bevat informatie over het oplossen van het probleem met de omleiding wanneer Azure-toepassing gateway wordt gebruikt met Azure App Service
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 1cc7df755198461643703cac988c8c31f2ac25db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182883"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Problemen met App Service oplossen in Application Gateway

Meer informatie over het vaststellen en oplossen van problemen die kunnen optreden wanneer Azure App Service wordt gebruikt als een back-end-doel met Azure-toepassing gateway.

## <a name="overview"></a>Overzicht

In dit artikel leert u hoe u de volgende problemen kunt oplossen:

* De app service-URL wordt weer gegeven in de browser wanneer er een omleiding wordt gemaakt.
* Het domein app service ARRAffinity cookie is ingesteld op de naam van de app service-host, example.azurewebsites.net in plaats van de oorspronkelijke host.

Wanneer een back-end-toepassing een omleidings reactie verzendt, wilt u mogelijk de client omleiden naar een andere URL dan die opgegeven door de back-end-toepassing. U kunt dit doen wanneer een app service wordt gehost achter een toepassings gateway en moet de client een omleiding naar het relatieve pad omleiden. Een voor beeld is een omleiding van contoso.azurewebsites.net/path1 naar contoso.azurewebsites.net/path2. 

Wanneer de app-service een omleidings reactie verzendt, gebruikt deze dezelfde hostnaam in de locatie header van de reactie als de naam van de aanvraag die wordt ontvangen van de toepassings gateway. De client maakt de aanvraag bijvoorbeeld rechtstreeks naar contoso.azurewebsites.net/path2 in plaats van de Application Gateway contoso.com/path2 te passeren. U wilt de toepassings gateway niet overs Laan.

Dit probleem kan de volgende oorzaken hebben:

- U hebt omleiding geconfigureerd op uw app service. Omleiding kan net zo eenvoudig zijn als het toevoegen van een afsluitende schuine streep aan de aanvraag.
- U hebt Azure Active Directory-verificatie, waardoor de omleiding wordt veroorzaakt.

Wanneer u app-Services achter een toepassings gateway gebruikt, is de domein naam die is gekoppeld aan de Application Gateway (example.com) niet hetzelfde als de domein naam van de app service (zeg example.azurewebsites.net). De domein waarde voor het ARRAffinity-cookie dat door de app service is ingesteld, bevat de domein naam example.azurewebsites.net. Dit is niet gewenst. De oorspronkelijke hostnaam, example.com, moet de waarde voor de domein naam in de cookie zijn.

## <a name="sample-configuration"></a>Voorbeeld configuratie

- HTTP-listener: Basic of multi-site
- Back-end-adres groep: App Service
- HTTP-instellingen: **Kies hostname van back-end-adres** ingeschakeld
- Test: **Kies een hostnaam uit de HTTP-instellingen** ingeschakeld

## <a name="cause"></a>Oorzaak

App Service is een service met meerdere tenants, zodat de host-header in de aanvraag wordt gebruikt om de aanvraag naar het juiste eind punt te sturen. De standaard domeinnaam van App Services, *. azurewebsites.net (zeg, contoso.azurewebsites.net), wijkt af van de domein naam van de toepassings gateway (zeg, contoso.com). 

De oorspronkelijke aanvraag van de client heeft de domein naam van de toepassings gateway, contoso.com, als de hostnaam. U moet de toepassings gateway configureren om de hostnaam in de oorspronkelijke aanvraag te wijzigen in de hostnaam van de app service wanneer de aanvraag wordt doorgestuurd naar de back-end van de app service. Gebruik de optie **hostnaam kiezen op basis van het back-upadres** in de configuratie van de http-instelling van de toepassings gateway. Gebruik de switch **hostname van de back-end-HTTP-instellingen** in de status test configuratie.



![Toepassings gateway wijzigt hostnaam](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Wanneer de app-service een omleiding doet, wordt de overschreven hostnaam contoso.azurewebsites.net in de locatie header gebruikt in plaats van de oorspronkelijke hostnaam contoso.com, tenzij anders is geconfigureerd. Controleer de volgende voorbeeld aanvraag en-antwoord headers.
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
In het vorige voor beeld ziet u dat de antwoord header de status code 301 voor omleiding heeft. De locatie header heeft de hostnaam van de app service in plaats van de oorspronkelijke hostnaam `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Oplossing: de locatie header opnieuw schrijven

Stel de hostnaam in de locatie header in op de domein naam van de toepassings gateway. U doet dit door een [regel voor herschrijven](./rewrite-http-headers.md) te maken met een voor waarde die evalueert of de locatie header in het antwoord azurewebsites.net bevat. Ook moet er een actie worden uitgevoerd voor het herschrijven van de locatie header om de hostnaam van de toepassings gateway te hebben. Zie instructies voor [het herschrijven van de locatie header](./rewrite-http-headers.md#modify-a-redirection-url)voor meer informatie.

> [!NOTE]
> De ondersteuning voor het opnieuw schrijven van HTTP-headers is alleen beschikbaar voor de [Standard_v2 en WAF_V2 SKU](./application-gateway-autoscaling-zone-redundant.md) van Application Gateway. Als u v1 SKU gebruikt, raden we u [aan om te migreren van v1 naar v2](./migrate-v1-v2.md). U wilt herschrijven en andere [geavanceerde mogelijkheden](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) gebruiken die beschikbaar zijn met v2 SKU.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Alternatieve oplossing: gebruik een aangepaste domein naam

Als u v1 SKU gebruikt, kunt u de locatie header niet herschrijven. Deze mogelijkheid is alleen beschikbaar voor v2 SKU. Om het omleidings probleem op te lossen, geeft u dezelfde hostnaam door die de toepassings gateway naar de app service ontvangt, in plaats van een host te overschrijven.

De app service doet nu de omleiding (indien van toepassing) op dezelfde oorspronkelijke host-header die verwijst naar de toepassings gateway en niet zijn eigen.

U moet eigenaar zijn van een aangepast domein en dit proces volgen:

- Registreer het domein bij de lijst met aangepaste domeinen van de app service. U moet een CNAME in uw aangepaste domein hebben dat verwijst naar de FQDN van de app-service. Zie [een bestaande aangepaste DNS-naam toewijzen aan Azure app service](../app-service/app-service-web-tutorial-custom-domain.md)voor meer informatie.

    ![Lijst met aangepaste domeinen voor app service](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Uw app service is gereed om de hostnaam te accepteren `www.contoso.com` . Wijzig uw CNAME-vermelding in DNS zodat deze terug naar de FQDN van de toepassings gateway verwijst, bijvoorbeeld `appgw.eastus.cloudapp.azure.com` .

- Zorg ervoor dat uw domein wordt `www.contoso.com` omgezet naar de FQDN van de toepassings gateway wanneer u een DNS-query uitvoert.

- Stel de aangepaste test voor het uitschakelen **van de hostnaam van de back-end-HTTP-instellingen** uit. Schakel in het Azure Portal het selectie vakje in de instellingen testen uit. Gebruik in Power shell niet de schakel optie **-PickHostNameFromBackendHttpSettings** in de opdracht **set-AzApplicationGatewayProbeConfig** . Voer in het veld hostnaam van de test de FQDN-naam van uw app service in, example.azurewebsites.net. De test aanvragen die vanuit de toepassings gateway worden verzonden, hebben deze FQDN in de hostheader.

  > [!NOTE]
  > Voor de volgende stap zorgt u ervoor dat uw aangepaste test niet is gekoppeld aan uw back-end-HTTP-instellingen. Uw HTTP-instellingen hebben nog steeds de optie **hostnaam kiezen van back-end-adres** switch op dit punt ingeschakeld.

- Stel de HTTP-instellingen van uw toepassings gateway in op het uitschakelen **van de hostnaam uit het back-upadres**. Schakel in het Azure Portal het selectie vakje uit. Gebruik in Power shell niet de schakel optie **-PickHostNameFromBackendAddress** in de opdracht **set-AzApplicationGatewayBackendHttpSettings** .

- Koppel de aangepaste test terug naar de HTTP-instellingen van de back-end en controleer of de back-end in orde is.

- De toepassings gateway moet nu dezelfde hostnaam door sturen `www.contoso.com` naar de app service. De omleiding gebeurt op dezelfde hostnaam. Controleer de volgende voorbeeld aanvraag en-antwoord headers.

Als u de voor gaande stappen wilt implementeren met behulp van Power shell voor een bestaande configuratie, gebruikt u het Power shell-voorbeeld script dat volgt. Houd er rekening mee dat de Schakel opties **-PickHostname niet zijn** gebruikt in de configuratie van de test en de HTTP-instellingen.

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

Als de voor gaande stappen het probleem niet hebben opgelost, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/options/).