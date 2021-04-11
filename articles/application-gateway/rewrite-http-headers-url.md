---
title: HTTP-headers en URL opnieuw schrijven met Azure-toepassing gateway | Microsoft Docs
description: Dit artikel bevat een overzicht van het herschrijven van HTTP-headers en URL in Azure-toepassing gateway
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: 7662ef5c2c3f5ed20069f64781d222ae44e52168
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384836"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>HTTP-headers en URL opnieuw schrijven met Application Gateway

 Met Application Gateway kunt u geselecteerde inhoud van aanvragen en antwoorden opnieuw schrijven. Met deze functie kunt u Url's, query reeks parameters en wijzigings aanvragen en-antwoord headers omzetten. U kunt hiermee ook voor waarden toevoegen om ervoor te zorgen dat de URL of de opgegeven headers alleen worden herschreven wanneer aan bepaalde voor waarden wordt voldaan. Deze voorwaarden zijn gebaseerd op de informatie in de aanvraag en het antwoord.

>[!NOTE]
>De HTTP-header en de functies voor het herschrijven van URL'S zijn alleen beschikbaar voor de [SKU van Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Herschrijf typen die worden ondersteund

### <a name="request-and-response-headers"></a>Aanvraag- en reactieheaders

Met HTTP-headers kan een client en server aanvullende informatie door geven met een aanvraag of antwoord. Door deze headers te herschrijven, kunt u belang rijke taken uitvoeren, zoals het toevoegen van aan beveiliging gerelateerde header velden zoals HSTS/X-XSS-Protection, het verwijderen van antwoord header velden die gevoelige informatie kunnen onthullen en poort gegevens van X-doorgestuurd-voor kopteksten verwijderen.

In Application Gateway kunt u headers van HTTP-aanvragen en -antwoorden toevoegen, verwijderen of bijwerken terwijl aanvraag- en antwoordpakketten zich verplaatsen tussen de client en back-endpools.

Zie [hier](rewrite-url-portal.md)voor meer informatie over het opnieuw schrijven van aanvraag-en reactie headers met Application Gateway met behulp van Azure Portal.

![afbeelding](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Ondersteunde headers**

U kunt alle headers in aanvragen en antwoorden opnieuw schrijven, met uitzonde ring van de verbinding en kopteksten bijwerken. U kunt ook de Application gateway gebruiken om aangepaste kopteksten te maken en deze toe te voegen aan de aanvragen en antwoorden die via deze service worden doorgestuurd.

### <a name="url-path-and-query-string"></a>URL-pad en query reeks

Met de mogelijkheid tot het herschrijven van URL'S in Application Gateway kunt u het volgende doen:

* De hostnaam, het pad en de query reeks van de aanvraag-URL herschrijven 

* Kies voor het herschrijven van de URL van alle aanvragen op een listener of alleen de aanvragen die overeenkomen met een of meer van de voor waarden die u hebt ingesteld. Deze voor waarden zijn gebaseerd op de aanvraag-en antwoord eigenschappen (aanvraag, koptekst, reactie header en Server variabelen).

* U kunt de aanvraag routeren (Selecteer de back-end-pool) op basis van de oorspronkelijke URL of de herschreven URL

Zie [hier](rewrite-url-portal.md)voor meer informatie over het herschrijven van een URL met Application Gateway met behulp van Azure Portal.

![Diagram met een beschrijving van het proces voor het herschrijven van een URL met Application Gateway.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Acties opnieuw schrijven

U gebruikt herschrijf acties om de URL, aanvraag headers of antwoord headers op te geven die u wilt herschrijven en de nieuwe waarde waarnaar u ze wilt herschrijven. De waarde van een URL of een nieuwe of bestaande koptekst kan worden ingesteld op deze typen waarden:

* Tekst
* Aanvraag header. Als u een aanvraag header wilt opgeven, moet u de syntaxis {http_req_ *headernaam*} gebruiken
* Antwoordheader. Als u een antwoord header wilt opgeven, moet u de syntaxis {http_resp_ *headernaam*} gebruiken
* Server variabele. Als u een server variabele wilt opgeven, moet u de syntaxis {var_ *serverVariable*} gebruiken. De lijst met ondersteunde Server variabelen weer geven
* Een combi natie van tekst, een aanvraag header, een reactie header en een server variabele. 

## <a name="rewrite-conditions"></a>Voor waarden opnieuw schrijven

U kunt herschrijf voorwaarden, een optionele configuratie, gebruiken om de inhoud van HTTP (S)-aanvragen en-antwoorden te evalueren en een herschrijf bewerking alleen uit te voeren als aan een of meer voor waarden wordt voldaan. De toepassings gateway gebruikt deze typen variabelen om de inhoud van aanvragen en antwoorden te evalueren:

* HTTP-headers in de aanvraag
* HTTP-headers in het antwoord
* Application Gateway server-variabelen

U kunt een voor waarde gebruiken om te evalueren of een opgegeven variabele aanwezig is, of een opgegeven variabele overeenkomt met een specifieke waarde, of of een opgegeven variabele overeenkomt met een specifiek patroon. 


### <a name="pattern-matching"></a>Patroon vergelijking 

Application Gateway gebruikt reguliere expressies voor patroon matching in de voor waarde. U kunt de [perl compatibele reguliere expressies (pcre) bibliotheek](https://www.pcre.org/) gebruiken voor het instellen van reguliere expressie patroon matching in de voor waarden. Voor meer informatie over de syntaxis van een reguliere expressie raadpleegt u de [hoofd pagina van de perl-reguliere expressies](https://perldoc.perl.org/perlre.html).

### <a name="capturing"></a>Vastleggen

Als u een subtekenreeks wilt vastleggen voor later gebruik, plaatst u haakjes rond het subpatroon dat overeenkomt met de definitie van de voor waarde regex. Met het eerste paar haakjes worden de subtekenreeksen in 1 opgeslagen, het tweede paar in 2, enzovoort. U kunt zoveel haakjes gebruiken als u wilt. Perl beveiligt het definiëren van meer genummerde variabelen zodat u deze vastgelegde teken reeksen kunt weer geven. Voor beelden van [Ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d)/# komen overeen met twee cijfers, waarbij ze worden vastgelegd in de groepen 1 en 2 

* /(\d +)/# komen overeen met een of meer cijfers, waarbij alles wordt vastgelegd in groep 1 

* /(\d) +/# komen overeen met een cijfer een of meer keren, waarbij de laatste wordt vastgelegd in groep 1

Zodra u deze hebt vastgelegd, kunt u ernaar verwijzen in de Actieset met de volgende indeling:

* Voor het vastleggen van een aanvraag header moet u {http_req_headerName_groupNumber} gebruiken. Bijvoorbeeld: {http_req_User-Agent_1} of {http_req_User-Agent_2}
* Voor een antwoord header Capture moet u {http_resp_headerName_groupNumber} gebruiken. Bijvoorbeeld: {http_resp_Location_1} of {http_resp_Location_2}
* Voor een server variabele moet u {var_serverVariableName_groupNumber} gebruiken. Bijvoorbeeld: {var_uri_path_1} of {var_uri_path_2}

Als u de gehele waarde wilt gebruiken, moet u het nummer niet vermelden. Gebruik de indeling {http_req_headerName}, etc. zonder groupNumber.

## <a name="server-variables"></a>Server variabelen

Application Gateway gebruikt Server variabelen om nuttige informatie over de server, de verbinding met de client en de huidige aanvraag op de verbinding op te slaan. Voor beelden van opgeslagen informatie zijn het IP-adres van de client en het type webbrowser. Server variabelen worden dynamisch gewijzigd, bijvoorbeeld wanneer een nieuwe pagina wordt geladen of wanneer een formulier wordt gepost. U kunt deze variabelen gebruiken om herschrijf voorwaarden te evalueren en kopteksten opnieuw te schrijven. Als u de waarde van Server variabelen wilt gebruiken om headers opnieuw te schrijven, moet u deze variabelen opgeven in de syntaxis {var_ *serverVariableName*}

Application Gateway biedt ondersteuning voor de volgende server variabelen:

|   Naam van de variabele    |                   Beschrijving                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Het veld X-doorgestuurde client aanvraag header met de `client_ip` variabele (Zie de uitleg verderop in deze tabel) in de NOTATIE IP1, IP2, IP3, enzovoort. Als het X-doorgestuurde-veld zich niet in de header van de client aanvraag `add_x_forwarded_for_proxy` bevindt, is de variabele gelijk aan de `$client_ip` variabele.   Deze variabele is vooral handig wanneer u de X-doorgestuurde header wilt herschrijven, zodat deze door Application Gateway wordt ingesteld, zodat de header alleen het IP-adres zonder de poort gegevens bevat. |
| ciphers_supported         | Een lijst met de code ringen die door de client worden ondersteund.               |
| ciphers_used              | De teken reeks die wordt gebruikt voor een tot stand gebrachte TLS-verbinding. |
| client_ip                 | Het IP-adres van de client van waaruit de toepassings gateway de aanvraag heeft ontvangen. Als er sprake is van een omgekeerde proxy voordat de toepassings gateway en de oorspronkelijke client, `client_ip` wordt het IP-adres van de omgekeerde proxy geretourneerd. |
| client_port               | De client poort.                                             |
| client_tcp_rtt            | Informatie over de TCP-verbinding van de client. Beschikbaar op systemen die ondersteuning bieden voor de TCP_INFO socket optie. |
| client_user               | Wanneer HTTP-verificatie wordt gebruikt, wordt de gebruikers naam opgegeven voor verificatie. |
| host                      | In deze volg orde van prioriteit: de hostnaam van de aanvraag regel, de hostnaam uit het veld met de aanvraag header van de host of de server naam die overeenkomt met een aanvraag. Voor beeld: in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` is de waarde host `contoso.com` |
| cookie_ *naam*             | De *naam* cookie.                                           |
| http_method               | De methode die wordt gebruikt om de URL-aanvraag te maken. Bijvoorbeeld GET of POST. |
| http_status               | De sessie status. Bijvoorbeeld 200, 400 of 403.           |
| http_version              | Het aanvraag protocol. Meestal HTTP/1.0, HTTP/1.1 of HTTP/2.0. |
| query_string              | De lijst met variabele/waarde-paren die volgt op '? ' in de aangevraagde URL. Voor beeld: in de aanvraag is `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` QUERY_STRING waarde `id=123&title=fabrikam` |
| received_bytes            | De lengte van de aanvraag (inclusief de aanvraag regel, kop en hoofd tekst van de aanvraag). |
| request_query             | De argumenten in de aanvraag regel.                           |
| request_scheme            | Het aanvraag schema: http of https.                           |
| request_uri               | De volledige oorspronkelijke aanvraag-URI (met argumenten). Voor beeld: in de aanvraag is `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` REQUEST_URI waarde `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Het aantal bytes dat naar een client is verzonden.                        |
| server_port               | De poort van de server die een aanvraag heeft geaccepteerd.              |
| ssl_connection_protocol   | Het Protocol van een tot stand gebrachte TLS-verbinding.               |
| ssl_enabled               | Aan als de verbinding in de TLS-modus werkt. Zoniet, een lege teken reeks. |
| uri_path                  | Identificeert de specifieke resource in de host waartoe de webclient toegang wil. Dit is het deel van de aanvraag-URI zonder de argumenten. Voor beeld: in de aanvraag is `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` uri_path waarde `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Server variabelen voor wederzijdse verificatie (preview-versie)

Application Gateway ondersteunt de volgende server variabelen voor scenario's voor wederzijdse verificatie. Gebruik deze server variabelen op dezelfde manier als hierboven met de andere server variabelen. 

|   Naam van de variabele    |                   Beschrijving                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Het client certificaat in PEM-indeling voor een bestaande SSL-verbinding. |
| client_certificate_end_date| De eind datum van het client certificaat. |
| client_certificate_fingerprint| De SHA1-vinger afdruk van het client certificaat voor een bestaande SSL-verbinding. |
| client_certificate_issuer | De teken reeks ' uitgever-DN ' van het client certificaat voor een bestaande SSL-verbinding. |
| client_certificate_serial | Het serie nummer van het client certificaat voor een bestaande SSL-verbinding.  |
| client_certificate_start_date| De begin datum van het client certificaat. |
| client_certificate_subject| De teken reeks ' onderwerp-DN ' van het client certificaat voor een tot stand gebrachte SSL-verbinding. |
| client_certificate_verification| Het resultaat van de verificatie van het client certificaat: *geslaagd*, *mislukt: <reason>* of *geen* als er geen certificaat aanwezig is. | 

## <a name="rewrite-configuration"></a>Configuratie opnieuw schrijven

Als u een herschrijf regel wilt configureren, moet u een regel voor opnieuw schrijven maken en de herschrijf regel configuratie hierin toevoegen.

Een regel reeks voor herschrijven bevat:

* **Koppeling voor routerings regel aanvragen:** De herschrijf configuratie wordt gekoppeld aan de bron-listener via de routerings regel. Wanneer u een eenvoudige routerings regel gebruikt, wordt de herschrijf configuratie gekoppeld aan een bronhost en is de globale header opnieuw te schrijven. Wanneer u een op pad gebaseerde routerings regel gebruikt, wordt de configuratie herschrijven gedefinieerd op de URL-pad toewijzing. In dat geval geldt dit alleen voor het specifieke pad van een site. U kunt meerdere herschrijf sets maken en elke herschrijfset Toep assen op meerdere listeners. Maar u kunt slechts één herschrijfset Toep assen op een specifieke listener.

* **Voor waarde voor herschrijven**: dit is een optionele configuratie. Bij herschrijf voorwaarden wordt de inhoud van de HTTP (S)-aanvragen en-antwoorden geëvalueerd. De actie herschrijven vindt plaats als de HTTP (S)-aanvraag of het antwoord overeenkomt met de voor waarde voor herschrijven. Als u meer dan één voor waarde aan een actie koppelt, treedt de actie alleen op wanneer aan alle voor waarden wordt voldaan. Met andere woorden, de bewerking is een logische en-bewerking.

* **Herschrijf type**: er zijn drie soorten herschrijf bewerkingen beschikbaar:
   * Aanvraag headers opnieuw schrijven 
   * Antwoord headers worden vervangen.
   * URL voor herschrijven: het herschrijf type van de URL heeft 3 onderdelen
      * **URL-pad**: de waarde waarnaar het pad moet worden geschreven. 
      * **URL-query teken reeks**: de waarde waarnaar de query teken reeks moet worden geschreven. 
      * **Pad-map opnieuw evalueren**: wordt gebruikt om te bepalen of de URL-pad toewijzing opnieuw moet worden geëvalueerd of niet. Als u dit selectie vakje uitschakelt, wordt het oorspronkelijke URL-pad gebruikt om het pad-patroon in de URL-pad toewijzing overeen te laten komen. Als deze optie is ingesteld op True, wordt de toewijzing van het URL-pad opnieuw geëvalueerd om de overeenkomst met het herschreven pad te controleren. Als u deze schakel optie inschakelt, kan de aanvraag worden doorgestuurd naar een andere back-end-groep na herschrijven.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>Herschrijven van URL'S of hostheadernamen met Web Application firewall (WAF_v2 SKU) gebruiken

Wanneer u het herschrijven van URL'S of het opnieuw schrijven van de host-header configureert, wordt de WAF-evaluatie uitgevoerd na de wijziging in de aanvraag header of de URL-para meters (na herschrijven). En wanneer u de configuratie van de herschrijf bewerking van de URL of host-header op uw Application Gateway verwijdert, wordt de WAF-evaluatie uitgevoerd voordat de header opnieuw wordt geschreven (vóór herschrijf bewerking). Deze volg orde zorgt ervoor dat WAF-regels worden toegepast op de laatste aanvraag die wordt ontvangen door de back-end-pool.

Stel dat u de volgende regel voor het herschrijven van kopteksten hebt voor de header `"Accept" : "text/html"` : als de waarde van header `"Accept"` gelijk is aan, moet u `"text/html"` de waarde vervolgens opnieuw schrijven naar `"image/png"` .

Hier wordt alleen de WAF-evaluatie uitgevoerd met alleen herschrijf bewerkingen in de koptekst `"Accept" : "text/html"` . Maar wanneer u de herschrijf bewerking van een URL of host-header opnieuw configureert, wordt de evaluatie van de WAF uitgevoerd op `"Accept" : "image/png"` .

>[!NOTE]
> URL-herschrijf bewerkingen worden verwacht een kleine toename van het CPU-gebruik van uw WAF-Application Gateway te veroorzaken. Het is raadzaam om de [metrische gegevens](high-traffic-support.md) van het CPU-gebruik gedurende korte tijd te controleren nadat u de regels voor het herschrijven van de URL op uw WAF-Application Gateway hebt ingeschakeld.

### <a name="common-scenarios-for-header-rewrite"></a>Algemene scenario's voor herschrijven van koptekst

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Poort gegevens verwijderen van de X-doorgestuurd-voor koptekst

Application Gateway voegt een X-doorgestuurd-voor koptekst in alle aanvragen voordat de aanvragen worden doorgestuurd naar de back-end. Deze header is een door komma's gescheiden lijst met IP-poorten. Er zijn mogelijk scenario's waarin de-back-endservers alleen IP-adressen nodig hebben om de headers te kunnen bevatten. U kunt header herschrijven gebruiken om de poort gegevens te verwijderen uit de X-forward-for-header. Een manier om dit te doen is door de header in te stellen op de add_x_forwarded_for_proxy-server variabele. U kunt ook de variabele client_ip gebruiken:

![Poort verwijderen](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Een omleidings-URL wijzigen

Wanneer een back-end-toepassing een omleidings reactie verzendt, wilt u mogelijk de client omleiden naar een andere URL dan die opgegeven door de back-end-toepassing. Stel dat u dit wilt doen wanneer een app service wordt gehost achter een toepassings gateway en dat de client een omleiding moet doen naar het relatieve pad. (Bijvoorbeeld een omleiding van contoso.azurewebsites.net/path1 naar contoso.azurewebsites.net/path2.)

Omdat App Service een multi tenant-service is, gebruikt de host-header in de aanvraag om de aanvraag naar het juiste eind punt te sturen. App-Services hebben een standaard domein naam van *. azurewebsites.net (contoso.azurewebsites.net) die verschilt van de domein naam van de toepassings gateway (zeg contoso.com). Omdat de oorspronkelijke aanvraag van de client de domein naam van de toepassings gateway (contoso.com) als de hostnaam heeft, wijzigt de toepassings gateway de hostnaam in contoso.azurewebsites.net. Deze wijziging wordt aangebracht zodat de app service de aanvraag naar het juiste eind punt kan routeren.

Wanneer de app-service een omleidings reactie verzendt, gebruikt deze dezelfde hostnaam in de locatie header van de reactie als die in de aanvraag die wordt ontvangen van de toepassings gateway. De client zal de aanvraag dus rechtstreeks laten `contoso.azurewebsites.net/path2` door lopen in plaats van de Application Gateway ( `contoso.com/path2` ). Het overs laan van de toepassings gateway is niet gewenst.

U kunt dit probleem oplossen door de hostnaam in de locatie header in te stellen op de domein naam van de toepassings gateway.

Dit zijn de stappen voor het vervangen van de hostnaam:

1. Maak een regel voor herschrijven met een voor waarde die evalueert of de locatie header in het antwoord azurewebsites.net bevat. Geef het patroon op `(https?):\/\/.*azurewebsites\.net(.*)$` .
2. Voer een actie uit om de locatie header opnieuw te schrijven, zodat deze de hostnaam van de toepassings gateway heeft. Dit doet u door `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als waarde voor de header in te voeren. U kunt ook de server variabele gebruiken `host` om de hostnaam zo in te stellen dat deze overeenkomt met de oorspronkelijke aanvraag.

![Locatie header wijzigen](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Beveiligings-HTTP-headers implementeren om beveiligings problemen te voor komen

U kunt verschillende beveiligings problemen oplossen door de vereiste headers in de reactie van de toepassing te implementeren. Deze beveiligings headers zijn onder andere X-XSS-Protection, strikte transport beveiliging en beveiligings beleid voor inhoud. U kunt Application Gateway gebruiken om deze headers voor alle antwoorden in te stellen.

![Beveiligingskop tekst](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Ongewenste kopteksten verwijderen

Mogelijk wilt u kopteksten verwijderen die gevoelige informatie uit een HTTP-antwoord onthullen. U kunt bijvoorbeeld informatie verwijderen, zoals de naam van de back-endserver, het besturings systeem of de details van de bibliotheek. U kunt de Application gateway gebruiken om deze headers te verwijderen:

![Koptekst verwijderen](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Controleren op de aanwezigheid van een header

U kunt een HTTP-aanvraag of reactie header evalueren voor de aanwezigheid van een header-of server variabele. Deze evaluatie is handig als u een header opnieuw wilt schrijven wanneer een bepaalde header aanwezig is.

![Aanwezigheid van een header controleren](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Algemene scenario's voor het herschrijven van URL'S

#### <a name="parameter-based-path-selection"></a>Op para meter gebaseerd pad selecteren

Als u de back-end-pool wilt gebruiken op basis van de waarde van een koptekst, een deel van de URL of de query teken reeks in de aanvraag, kunt u de combi natie van de functionaliteit voor het herschrijven van URL'S en op pad gebaseerde route ring.  Als u bijvoorbeeld een winkel website hebt en de product categorie wordt door gegeven als query reeks in de URL en u de aanvraag wilt door sturen naar de back-end op basis van de query teken reeks, geldt het volgende:

**Stap 1-4:**  Maak een pad-toewijzing zoals wordt weer gegeven in de onderstaande afbeelding

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="URL-herschrijf scenario 1-1.":::

**Stap 2 (a):** Maak een herschrijvende set met 3 regels voor herschrijven: 

* De eerste regel bevat een voor waarde waarmee de *QUERY_STRING*  variabele voor *Category = schoenen* wordt gecontroleerd en die een actie bevat waarmee het URL-pad naar/*listing1* opnieuw wordt geschreven en dat **Path map opnieuw** is ingeschakeld

* De tweede regel heeft een voor waarde die de *QUERY_STRING*  variabele voor *categorie = tassen* controleert en een actie heeft waarmee het URL-pad naar/*listing2*  opnieuw wordt geschreven en de **pad-map opnieuw geëvalueerd** is ingeschakeld

* De derde regel heeft een voor waarde die de *QUERY_STRING* variabele voor *Category = Accessories* controleert en een actie heeft waarmee het URL-pad naar/*listing3* opnieuw wordt geschreven en dat **Path map opnieuw wordt geëvalueerd**

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="URL-herschrijf scenario 1-2.":::

 

**Stap 2 (b):** Deze herschrijfset koppelen aan het standaardpad van de bovenstaande regel op basis van het pad

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="URL-herschrijf scenario 1-3.":::

Als de gebruiker zich nu aanvraagt, wordt deze met het standaardpad gematcht omdat geen van de paden in de pad- *contoso.com/Listing?Category=any*(/listing1,/listing2,/listing3) overeenkomt. Omdat u de bovenstaande herschrijfset hebt gekoppeld met dit pad, wordt deze herschrijfset geëvalueerd. Omdat de query reeks niet overeenkomt met de voor waarde in een van de drie regels voor herschrijven in deze herschrijfset, wordt er geen herschrijf actie uitgevoerd en daarom wordt de aanvraag niet gewijzigd naar de back-end die is gekoppeld aan het standaardpad ( *GenericList*).

 Als de gebruiker *contoso.com/Listing?Category=Shoes vraagt,* wordt het standaardpad automatisch aangepast. In dit geval komt de voor waarde in de eerste regel echter overeen en daarom wordt de actie die aan de voor waarde is gekoppeld, uitgevoerd, waardoor het URL-pad opnieuw naar/*listing1*  wordt geschreven en de pad-map opnieuw moet worden geëvalueerd. Wanneer het pad-toewijzing opnieuw wordt geëvalueerd, komt de aanvraag nu overeen met het pad dat is gekoppeld aan het patroon */listing1* en wordt de aanvraag doorgestuurd naar de back-end die is gekoppeld aan dit patroon. Dit is ShoesListBackendPool

>[!NOTE]
>Dit scenario kan worden uitgebreid naar elke kop-of cookie waarde, een URL-pad, een query reeks of server variabelen op basis van de gedefinieerde voor waarde, en stelt u in staat om aanvragen te routeren op basis van deze voor waarden.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Query reeks parameters opnieuw schrijven op basis van de URL

Houd rekening met een scenario van een winkel website waar de door de gebruiker zicht bare koppeling eenvoudig en leesbaar moet zijn, maar de back-endserver moet de query teken reeks parameters hebben om de juiste inhoud weer te geven.

In dat geval kunnen Application Gateway para meters uit de URL vastleggen en sleutel-waardeparen van de query reeks toevoegen uit de URL. Stel bijvoorbeeld dat de gebruiker wil herschrijven `https://www.contoso.com/fashion/shirts` naar `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` , en dat deze kan worden bereikt via de volgende URL-herschrijf configuratie.

**Voor waarde** -als server variabele `uri_path` gelijk is aan het patroon `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="URL-herschrijf scenario 2-1.":::

**Actie** : Stel het URL-pad naar `buy.aspx` en de query reeks naar `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="URL-herschrijf scenario 2-2.":::

Voor een stapsgewijze hand leiding voor het uitvoeren van het hierboven beschreven scenario raadpleegt u [URL herschrijven met Application Gateway met behulp van Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL-herschrijf bewerking versus URL-omleiding

In het geval van een URL-herschrijf bewerking, Application Gateway de URL opnieuw schrijven voordat de aanvraag naar de back-end wordt verzonden. Hierdoor wordt niet gewijzigd wat gebruikers in de browser zien, omdat de wijzigingen worden verborgen voor de gebruiker.

In het geval van een URL-omleiding stuurt Application Gateway een omleidings antwoord naar de client met de nieuwe URL. Op zijn beurt moet de client zijn aanvraag opnieuw verzenden naar de nieuwe URL die in de omleiding wordt vermeld. De URL die de gebruiker in de browser ziet, wordt bijgewerkt naar de nieuwe URL

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Write versus redirect.":::

## <a name="limitations"></a>Beperkingen

- Als een antwoord meer dan één header met dezelfde naam heeft en vervolgens de waarde van een van deze headers herschrijft, worden de andere headers in het antwoord verwijderd. Dit kan meestal gebeuren met Set-Cookie-header omdat u in een antwoord meer dan één Set-Cookie header kunt hebben. Een dergelijk scenario is wanneer u een app service gebruikt met een toepassings gateway en op cookies gebaseerde sessie affiniteit hebt geconfigureerd op de toepassings gateway. In dit geval bevat het antwoord twee Set-Cookie headers: één gebruikt door de app service, bijvoorbeeld: `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` en een andere voor Application Gateway-affiniteit, bijvoorbeeld `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Het herschrijven van een van de Set-Cookie-headers in dit scenario kan ertoe leiden dat de andere Set-Cookie-header uit het antwoord wordt verwijderd.
- Herschrijven wordt niet ondersteund wanneer de toepassings gateway is geconfigureerd om de aanvragen om te leiden of een aangepaste fout pagina weer te geven.
- Header namen mogen alfanumerieke tekens en specifieke symbolen bevatten zoals gedefinieerd in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). Het onderstrepings teken (_) wordt momenteel niet ondersteund in header namen.
- De headers voor verbinding en upgrades kunnen niet opnieuw worden geschreven

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het herschrijven van HTTP-headers met Application Gateway met behulp van Azure Portal](rewrite-http-headers-portal.md)
- [Meer informatie over het herschrijven van een URL met Application Gateway met behulp van Azure Portal](rewrite-url-portal.md)
