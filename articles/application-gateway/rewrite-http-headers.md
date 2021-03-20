---
title: HTTP-headers herschrijven met Azure-toepassing gateway | Microsoft Docs
description: Dit artikel bevat een overzicht van het herschrijven van HTTP-headers in Azure-toepassing gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: absha
ms.openlocfilehash: 7c5b4f0d5d4b153684683963c56b7506e76d963e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99575650"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>HTTP-headers herschrijven met Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met HTTP-headers kan een client en server aanvullende informatie door geven met een aanvraag of antwoord. Door deze headers te herschrijven, kunt u belang rijke taken uitvoeren, zoals het toevoegen van aan beveiliging gerelateerde header velden zoals HSTS/X-XSS-Protection, het verwijderen van antwoord header velden die gevoelige informatie kunnen onthullen en poort gegevens van X-doorgestuurd-voor kopteksten verwijderen.

In Application Gateway kunt u headers van HTTP-aanvragen en -antwoorden toevoegen, verwijderen of bijwerken terwijl aanvraag- en antwoordpakketten zich verplaatsen tussen de client en back-endpools. En u kunt voorwaarden toevoegen om ervoor te zorgen dat de opgegeven headers alleen worden herschreven wanneer is voldaan aan bepaalde voorwaarden.

Application Gateway biedt ook ondersteuning voor verschillende [Server variabelen](#server-variables) die u helpen bij het opslaan van aanvullende informatie over aanvragen en antwoorden. Dit maakt het eenvoudiger voor u om krachtige regels voor herschrijven te maken.

> [!NOTE]
>
> De ondersteuning voor het opnieuw schrijven van HTTP-headers is alleen beschikbaar voor de [Standard_V2 en WAF_V2 SKU](application-gateway-autoscaling-zone-redundant.md).

![Headers opnieuw schrijven](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Ondersteunde headers

U kunt alle headers in aanvragen en antwoorden opnieuw schrijven, met uitzonde ring van de headers host, verbinding en upgrade. U kunt ook de Application gateway gebruiken om aangepaste kopteksten te maken en deze toe te voegen aan de aanvragen en antwoorden die via deze service worden doorgestuurd.

## <a name="rewrite-conditions"></a>Voor waarden opnieuw schrijven

U kunt herschrijf voorwaarden gebruiken om de inhoud van HTTP (S)-aanvragen en-antwoorden te evalueren en een herschrijf bewerking voor de header alleen uit te voeren als aan een of meer voor waarden wordt voldaan. De toepassings gateway gebruikt deze typen variabelen om de inhoud van HTTP (S)-aanvragen en-antwoorden te evalueren:

- HTTP-headers in de aanvraag.
- HTTP-headers in het antwoord.
- Application Gateway server-variabelen.

U kunt een voor waarde gebruiken om te evalueren of een opgegeven variabele aanwezig is, of een opgegeven variabele overeenkomt met een specifieke waarde, of of een opgegeven variabele overeenkomt met een specifiek patroon. U gebruikt de [perl compatibele reguliere expressies (pcre) bibliotheek](https://www.pcre.org/) voor het instellen van reguliere expressie patroon matching in de voor waarden. Voor meer informatie over de syntaxis van een reguliere expressie raadpleegt u de [hoofd pagina van de perl-reguliere expressies](https://perldoc.perl.org/perlre.html).

## <a name="rewrite-actions"></a>Acties opnieuw schrijven

U gebruikt herschrijf acties om de aanvraag-en antwoord headers op te geven die u wilt herschrijven en de nieuwe waarde voor de kopteksten. U kunt een nieuwe header maken, de waarde van een bestaande koptekst wijzigen of een bestaande header verwijderen. De waarde van een nieuwe koptekst of een bestaande koptekst kan worden ingesteld op deze typen waarden:

- Tekst.
- Aanvraag header. Als u een aanvraag header wilt opgeven, moet u de syntaxis {http_req_ *headernaam*} gebruiken.
- Antwoordheader. Als u een antwoord header wilt opgeven, moet u de syntaxis {http_resp_ *headernaam*} gebruiken.
- Server variabele. Als u een server variabele wilt opgeven, moet u de syntaxis {var_ *serverVariable*} gebruiken.
- Een combi natie van tekst, een aanvraag header, een reactie header en een server variabele.

## <a name="server-variables"></a>Server variabelen

Application Gateway gebruikt Server variabelen om nuttige informatie over de server, de verbinding met de client en de huidige aanvraag op de verbinding op te slaan. Voor beelden van opgeslagen informatie zijn het IP-adres van de client en het type webbrowser. Server variabelen worden dynamisch gewijzigd, bijvoorbeeld wanneer een nieuwe pagina wordt geladen of wanneer een formulier wordt gepost. U kunt deze variabelen gebruiken om herschrijf voorwaarden te evalueren en kopteksten opnieuw te schrijven. Als u de waarde van Server variabelen wilt gebruiken om headers opnieuw te schrijven, moet u deze variabelen opgeven in de syntaxis {var_ *serverVariable*}

Application Gateway ondersteunt deze server variabelen:

| Naam van de variabele | Beschrijving                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Het veld X-doorgestuurde client aanvraag header met de `client_ip` variabele (Zie de uitleg verderop in deze tabel) in de NOTATIE IP1, IP2, IP3, enzovoort. Als het X-doorgestuurde-veld zich niet in de header van de client aanvraag `add_x_forwarded_for_proxy` bevindt, is de variabele gelijk aan de `$client_ip` variabele. Deze variabele is vooral handig wanneer u de X-doorgestuurde header wilt herschrijven, zodat deze door Application Gateway wordt ingesteld, zodat de header alleen het IP-adres zonder de poort gegevens bevat. |
| ciphers_supported          | Een lijst met de code ringen die door de client worden ondersteund.          |
| ciphers_used               | De teken reeks die wordt gebruikt voor een tot stand gebrachte TLS-verbinding. |
| client_ip                  | Het IP-adres van de client van waaruit de toepassings gateway de aanvraag heeft ontvangen. Als er een omgekeerde proxy is vóór de toepassings gateway en de oorspronkelijke client, retourneert *client_ip* het IP-adres van de omgekeerde proxy. |
| client_port                | De client poort.                                                  |
| client_tcp_rtt             | Informatie over de TCP-verbinding van de client. Beschikbaar op systemen die ondersteuning bieden voor de TCP_INFO socket optie. |
| client_user                | Wanneer HTTP-verificatie wordt gebruikt, wordt de gebruikers naam opgegeven voor verificatie. |
| host                       | In deze volg orde van prioriteit: de hostnaam van de aanvraag regel, de hostnaam uit het veld met de aanvraag header van de host of de server naam die overeenkomt met een aanvraag. Voor beeld: in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` is de waarde host *contoso.com* |
| cookie_ *naam*              | De *naam* cookie.                                            |
| http_method                | De methode die wordt gebruikt om de URL-aanvraag te maken. Bijvoorbeeld GET of POST. |
| http_status                | De sessie status. Bijvoorbeeld 200, 400 of 403.                       |
| http_version               | Het aanvraag protocol. Meestal HTTP/1.0, HTTP/1.1 of HTTP/2.0. |
| query_string               | De lijst met variabele/waarde-paren die volgt op '? ' in de aangevraagde URL. Voor beeld: in de aanvraag is `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` QUERY_STRING waarde *id = 123&titel = fabrikam* |
| received_bytes             | De lengte van de aanvraag (inclusief de aanvraag regel, kop en hoofd tekst van de aanvraag). |
| request_query              | De argumenten in de aanvraag regel.                                |
| request_scheme             | Het aanvraag schema: http of https.                            |
| request_uri                | De volledige oorspronkelijke aanvraag-URI (met argumenten). Voor beeld: in de aanvraag is `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` REQUEST_URI waarde */article.aspx? id = 123&titel = fabrikam*   |
| sent_bytes                 | Het aantal bytes dat naar een client is verzonden.                             |
| server_port                | De poort van de server die een aanvraag heeft geaccepteerd.                 |
| ssl_connection_protocol    | Het Protocol van een tot stand gebrachte TLS-verbinding.        |
| ssl_enabled                | Aan als de verbinding in de TLS-modus werkt. Zoniet, een lege teken reeks. |
| uri_path                   | Identificeert de specifieke resource in de host waartoe de webclient toegang wil. Dit is het deel van de aanvraag-URI zonder de argumenten. Voor beeld: in de aanvraag is `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path waarde */article.aspx*  |

## <a name="rewrite-configuration"></a>Configuratie opnieuw schrijven

Als u het opnieuw schrijven van HTTP-headers wilt configureren, moet u deze stappen volt ooien.

1. De objecten maken die vereist zijn voor het herschrijven van HTTP-headers:

   - **Actie voor herschrijven**: Hiermee geeft u de velden aanvraag en aanvraag header op die u wilt herschrijven en de nieuwe waarde voor de kopteksten. U kunt een of meer herschrijf voorwaarden koppelen met een herschrijf actie.

   - **Voor waarde voor herschrijven**: een optionele configuratie. Bij herschrijf voorwaarden wordt de inhoud van de HTTP (S)-aanvragen en-antwoorden geëvalueerd. De actie herschrijven vindt plaats als de HTTP (S)-aanvraag of het antwoord overeenkomt met de voor waarde voor herschrijven.

     Als u meer dan één voor waarde aan een actie koppelt, treedt de actie alleen op wanneer aan alle voor waarden wordt voldaan. Met andere woorden, de bewerking is een logische en-bewerking.

   - **Regel voor herschrijven**: bevat meerdere combi Naties van actie voor opnieuw schrijven/voor waarden herschrijven.

   - **Regel volgorde**: helpt bij het bepalen van de volg orde waarin de regels voor herschrijven worden uitgevoerd. Deze configuratie is handig wanneer u meerdere regels voor herschrijven hebt opgegeven in een herschrijfset. Een regel voor herschrijven met een lagere regel reeks waarde wordt eerst uitgevoerd. Als u dezelfde regel reeks aan twee herschrijf regels toewijst, is de volg orde van uitvoering niet-deterministisch.

   - **Set opnieuw schrijven**: bevat meerdere herschrijf regels die worden gekoppeld aan een regel voor het door sturen van aanvragen.

2. Koppel de herschrijfset (*rewriteRuleSet*) aan een routerings regel. De herschrijf configuratie wordt gekoppeld aan de bron-listener via de routerings regel. Wanneer u een basis regel voor door sturen gebruikt, wordt de configuratie voor het opnieuw schrijven van kopteksten gekoppeld aan een bronhost en is het herschrijven van de globale header. Wanneer u een op pad gebaseerde routerings regel gebruikt, wordt de configuratie voor het herschrijven van kopteksten gedefinieerd op de URL-pad toewijzing. In dat geval geldt dit alleen voor het specifieke pad van een site.
   > [!NOTE]
   > URL herschrijven wijzigen de headers; de URL voor het pad wordt niet gewijzigd.

U kunt meerdere herschrijf sets voor HTTP-headers maken en elke herschrijfset Toep assen op meerdere listeners. Maar u kunt slechts één herschrijfset Toep assen op een specifieke listener.

## <a name="common-scenarios"></a>Algemene scenario's

Hier volgen enkele veelvoorkomende scenario's voor het gebruik van header herschrijven.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Poort gegevens verwijderen van de X-doorgestuurd-voor koptekst

Application Gateway voegt een X-doorgestuurd-voor koptekst in alle aanvragen voordat de aanvragen worden doorgestuurd naar de back-end. Deze header is een door komma's gescheiden lijst met IP-poorten. Er zijn mogelijk scenario's waarin de-back-endservers alleen IP-adressen nodig hebben om de headers te kunnen bevatten. U kunt header herschrijven gebruiken om de poort gegevens te verwijderen uit de X-forward-for-header. Een manier om dit te doen is door de header in te stellen op de add_x_forwarded_for_proxy server variabele:

![Poort verwijderen](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Een omleidings-URL wijzigen

Wanneer een back-end-toepassing een omleidings reactie verzendt, wilt u mogelijk de client omleiden naar een andere URL dan die opgegeven door de back-end-toepassing. Stel dat u dit wilt doen wanneer een app service wordt gehost achter een toepassings gateway en dat de client een omleiding moet doen naar het relatieve pad. (Bijvoorbeeld een omleiding van contoso.azurewebsites.net/path1 naar contoso.azurewebsites.net/path2.)

Omdat App Service een multi tenant-service is, gebruikt de host-header in de aanvraag om de aanvraag naar het juiste eind punt te sturen. App-Services hebben een standaard domein naam van *. azurewebsites.net (contoso.azurewebsites.net) die verschilt van de domein naam van de toepassings gateway (zeg contoso.com). Omdat de oorspronkelijke aanvraag van de client de domein naam van de toepassings gateway (contoso.com) als de hostnaam heeft, wijzigt de toepassings gateway de hostnaam in contoso.azurewebsites.net. Deze wijziging wordt aangebracht zodat de app service de aanvraag naar het juiste eind punt kan routeren.

Wanneer de app-service een omleidings reactie verzendt, gebruikt deze dezelfde hostnaam in de locatie header van de reactie als die in de aanvraag die wordt ontvangen van de toepassings gateway. De client gaat de aanvraag dus rechtstreeks naar contoso.azurewebsites.net/path2 in plaats van de Application Gateway (contoso.com/path2). Het overs laan van de toepassings gateway is niet gewenst.

U kunt dit probleem oplossen door de hostnaam in de locatie header in te stellen op de domein naam van de toepassings gateway.

Dit zijn de stappen voor het vervangen van de hostnaam:

1. Maak een regel voor herschrijven met een voor waarde die evalueert of de locatie header in het antwoord azurewebsites.net bevat. Geef het patroon op `(https?):\/\/.*azurewebsites\.net(.*)$` .
1. Voer een actie uit om de locatie header opnieuw te schrijven, zodat deze de hostnaam van de toepassings gateway heeft. Dit doet u door `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als waarde voor de header in te voeren.

![Locatie header wijzigen](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Beveiligings-HTTP-headers implementeren om beveiligings problemen te voor komen

U kunt verschillende beveiligings problemen oplossen door de vereiste headers in de reactie van de toepassing te implementeren. Deze beveiligings headers zijn onder andere X-XSS-Protection, strikte transport beveiliging en beveiligings beleid voor inhoud. U kunt Application Gateway gebruiken om deze headers voor alle antwoorden in te stellen.

![Beveiligingskop tekst](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Ongewenste kopteksten verwijderen

Mogelijk wilt u kopteksten verwijderen die gevoelige informatie uit een HTTP-antwoord onthullen. U kunt bijvoorbeeld informatie verwijderen, zoals de naam van de back-endserver, het besturings systeem of de details van de bibliotheek. U kunt de Application gateway gebruiken om deze headers te verwijderen:

![Koptekst verwijderen](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Controleren op de aanwezigheid van een header

U kunt een HTTP-aanvraag of reactie header evalueren voor de aanwezigheid van een header-of server variabele. Deze evaluatie is handig als u een header opnieuw wilt schrijven wanneer een bepaalde header aanwezig is.

![Aanwezigheid van een header controleren](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Beperkingen

- Als een antwoord meer dan één header met dezelfde naam heeft en vervolgens de waarde van een van deze headers herschrijft, worden de andere headers in het antwoord verwijderd. Dit kan meestal gebeuren met Set-Cookie-header omdat u in een antwoord meer dan één Set-Cookie header kunt hebben. Een dergelijk scenario is wanneer u een app service gebruikt met een toepassings gateway en op cookies gebaseerde sessie affiniteit hebt geconfigureerd op de toepassings gateway. In dit geval bevat het antwoord twee Set-Cookie headers: één gebruikt door de app service, bijvoorbeeld: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` en een andere voor Application Gateway-affiniteit, bijvoorbeeld `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Het herschrijven van een van de Set-Cookie-headers in dit scenario kan ertoe leiden dat de andere Set-Cookie-header uit het antwoord wordt verwijderd.

- Herschrijven wordt niet ondersteund wanneer de toepassings gateway is geconfigureerd om de aanvragen om te leiden of een aangepaste fout pagina weer te geven.

- Het opnieuw schrijven van de verbinding, bijwerken en host-headers wordt momenteel niet ondersteund.

- Header namen mogen alfanumerieke tekens en specifieke symbolen bevatten zoals gedefinieerd in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Het onderstrepings teken () wordt momenteel niet ondersteund \_ in header namen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het herschrijven van HTTP-headers:

- [HTTP-headers opnieuw schrijven met behulp van de Azure-portal](./rewrite-http-headers-portal.md)
- [HTTP-headers herschrijven met Azure PowerShell](add-http-header-rewrite-rule-powershell.md)