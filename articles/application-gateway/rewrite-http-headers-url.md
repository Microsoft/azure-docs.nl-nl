---
title: HTTP-headers en URL's herschrijven met Azure Application Gateway | Microsoft Docs
description: Dit artikel biedt een overzicht van het herschrijven van HTTP-headers en URL's in Azure Application Gateway
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: azhussai
ms.openlocfilehash: b7cf7c98e71da215eb30dcab556a88d6d2701591
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789443"
---
# <a name="rewrite-http-headers-and-url-with-application-gateway"></a>HTTP-headers en URL's herschrijven met Application Gateway

Application Gateway kunt u geselecteerde inhoud van aanvragen en antwoorden herschrijven. Met deze functie kunt u URL's, queryreeksparameters en aanvraag- en antwoordheaders wijzigen. Ook kunt u voorwaarden toevoegen om ervoor te zorgen dat de URL of de opgegeven headers alleen worden herschreven wanneer aan bepaalde voorwaarden wordt voldaan. Deze voorwaarden zijn gebaseerd op de informatie in de aanvraag en het antwoord.

>[!NOTE]
>Functies voor het herschrijven van HTTP-headers en URL's zijn alleen beschikbaar voor de [Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md)

## <a name="rewrite-types-supported"></a>Ondersteunde typen herschrijven

### <a name="request-and-response-headers"></a>Aanvraag- en reactieheaders

Met HTTP-headers kunnen een client en server aanvullende informatie doorgeven met een aanvraag of antwoord. Door deze headers te herschrijven, kunt u belangrijke taken uitvoeren, zoals het toevoegen van beveiligingsgerelateerde headervelden zoals HSTS/ X-XSS-Protection, het verwijderen van antwoordheadervelden die gevoelige informatie kunnen weergeven en het verwijderen van poortgegevens uit X-Forwarded-For-headers.

In Application Gateway kunt u headers van HTTP-aanvragen en -antwoorden toevoegen, verwijderen of bijwerken terwijl aanvraag- en antwoordpakketten zich verplaatsen tussen de client en back-endpools.

Zie hier voor meer informatie over het herschrijven van aanvraag- en antwoordheaders met Application Gateway [Azure Portal.](rewrite-url-portal.md)

![afbeelding](./media/rewrite-http-headers-url/header-rewrite-overview.png)


**Ondersteunde headers**

U kunt alle headers in aanvragen en antwoorden opnieuw schrijven, met uitzondering van de verbindings- en upgradeheaders. U kunt de toepassingsgateway ook gebruiken om aangepaste headers te maken en deze toe te voegen aan de aanvragen en antwoorden die er doorheen worden gerouteerd.

### <a name="url-path-and-query-string"></a>URL-pad en queryreeks

Met de mogelijkheid voor het herschrijven van APPLICATION GATEWAY kunt u het volgende doen:

* De hostnaam, het pad en de queryreeks van de aanvraag-URL herschrijven 

* Kies ervoor om de URL van alle aanvragen op een listener te herschrijven of alleen de aanvragen die overeenkomen met een of meer van de voorwaarden die u hebt ingesteld. Deze voorwaarden zijn gebaseerd op de aanvraag- en antwoordeigenschappen (aanvraag-, header-, antwoordheader- en servervariabelen).

* Kies ervoor om de aanvraag door te sturen (selecteer de back-endpool) op basis van de oorspronkelijke URL of de herschreven URL

Zie hier voor meer informatie over het herschrijven van URL'Application Gateway met [Azure Portal.](rewrite-url-portal.md)

![Diagram met een beschrijving van het proces voor het herschrijven van een URL met Application Gateway.](./media/rewrite-http-headers-url/url-rewrite-overview.png)

## <a name="rewrite-actions"></a>Acties herschrijven

U gebruikt herschrijfacties om de URL, aanvraagheaders of antwoordheaders op te geven die u wilt herschrijven en de nieuwe waarde waarin u ze wilt herschrijven. De waarde van een URL of een nieuwe of bestaande header kan worden ingesteld op deze typen waarden:

* Tekst
* Aanvraagheader. Als u een aanvraagheader wilt opgeven, moet u de syntaxis {http_req_ *headerName*} gebruiken
* Antwoordheader. Als u een antwoordheader wilt opgeven, moet u de syntaxis {http_resp_ *headerName*} gebruiken
* Servervariabele. Als u een servervariabele wilt opgeven, moet u de syntaxis {var_ *serverVariable*}. Zie de lijst met ondersteunde servervariabelen
* Een combinatie van tekst, een aanvraagheader, een antwoordheader en een servervariabele. 

## <a name="rewrite-conditions"></a>Voorwaarden herschrijven

U kunt voorwaarden voor herschrijven, een optionele configuratie, gebruiken om de inhoud van HTTP(S)-aanvragen en -antwoorden te evalueren en alleen een herschrijven uit te voeren wanneer aan een of meer voorwaarden wordt voldaan. De toepassingsgateway gebruikt deze typen variabelen om de inhoud van aanvragen en antwoorden te evalueren:

* HTTP-headers in de aanvraag
* HTTP-headers in het antwoord
* Application Gateway servervariabelen configureren

U kunt een voorwaarde gebruiken om te evalueren of een opgegeven variabele aanwezig is, of een opgegeven variabele overeenkomt met een specifieke waarde of dat een opgegeven variabele overeenkomt met een specifiek patroon. 


### <a name="pattern-matching"></a>Patroonherkenning 

Application Gateway maakt gebruik van reguliere expressies voor patroonmatching in de voorwaarde. U kunt de [PCRE-bibliotheek (Perl Compatible Regular Expressions)](https://www.pcre.org/) gebruiken om een patroon voor reguliere expressies in te stellen dat overeenkomt met de voorwaarden. Zie de hoofdpagina voor Reguliere expressies perl voor meer informatie over de [syntaxis van reguliere expressies.](https://perldoc.perl.org/perlre.html)

### <a name="capturing"></a>Vastleggen

Als u een subtekenreeks wilt vastleggen voor later gebruik, zet u haakjes rond het subpatroon dat overeenkomt met de subtekenreeks in de regex-voorwaardedefinitie. Het eerste paar haakjes slaat de subtekenreeks op in 1, het tweede paar in 2, en meer. U kunt zoveel haakjes gebruiken als u wilt; Perl blijft alleen maar meer genummerde variabelen definiëren om deze vastgelegde tekenreeksen weer te geven. Enkele voorbeelden van [ref](https://docstore.mik.ua/orelly/perl/prog3/ch05_07.htm): 

*  /(\d)(\d)/ # Overeenkomen met twee cijfers, en deze vastleggen in de groepen 1 en 2 

* /(\d+)/ # Overeenkomen met een of meer cijfers, en deze allemaal vastleggen in groep 1 

* /(\d)+/ # Komt een cijfer een of meer keer overeen, en legt de laatste vast in groep 1

Na het maken kunt u er in de actieset naar verwijzen met behulp van de volgende indeling:

* Voor het vastleggen van de aanvraagheader moet u {http_req_headerName_groupNumber} gebruiken. Bijvoorbeeld {http_req_User-Agent_1} of {http_req_User-Agent_2}
* Voor het vastleggen van een antwoordheader moet u {http_resp_headerName_groupNumber} gebruiken. Bijvoorbeeld {http_resp_Location_1} of {http_resp_Location_2}
* Voor een servervariabele moet u {var_serverVariableName_groupNumber}. Bijvoorbeeld {var_uri_path_1} of {var_uri_path_2}

Als u de hele waarde wilt gebruiken, moet u het getal niet noemen. Gebruik gewoon de indeling {http_req_headerName}, enzovoort zonder het groupNumber.

## <a name="server-variables"></a>Servervariabelen

Application Gateway maakt gebruik van servervariabelen voor het opslaan van nuttige informatie over de server, de verbinding met de client en de huidige aanvraag voor de verbinding. Voorbeelden van informatie die is opgeslagen, zijn het IP-adres van de client en het webbrowsertype. Servervariabelen veranderen dynamisch, bijvoorbeeld wanneer een nieuwe pagina wordt geladen of wanneer een formulier wordt geplaatst. U kunt deze variabelen gebruiken om voorwaarden voor herschrijven te evalueren en headers opnieuw te schrijven. Als u de waarde van servervariabelen wilt gebruiken om headers te herschrijven, moet u deze variabelen opgeven in de syntaxis {var_ *serverVariableName*}

Application Gateway ondersteunt de volgende servervariabelen:

|   Naam van de variabele    |                   Beschrijving                                           |
| ------------------------- | ------------------------------------------------------------ |
| add_x_forwarded_for_proxy | Het veld X-Forwarded-For-clientaanvraagheader met de variabele (zie uitleg verderop in deze tabel) toegevoegd in de indeling `client_ip` IP1, IP2, IP3, en meer. Als het veld X-Forwarded-For niet in de header van de clientaanvraag staat, is de `add_x_forwarded_for_proxy` variabele gelijk aan de variabele `$client_ip` .   Deze variabele is met name handig wanneer u de X-Forwarded-For-header wilt herschrijven die is ingesteld door Application Gateway, zodat de header alleen het IP-adres zonder de poortgegevens bevat. |
| ciphers_supported         | Een lijst met de coderingen die door de client worden ondersteund.               |
| ciphers_used              | De reeks coderingen die wordt gebruikt voor een tot stand gebrachte TLS-verbinding. |
| client_ip                 | Het IP-adres van de client van waaruit de toepassingsgateway de aanvraag heeft ontvangen. Als er een omgekeerde proxy is vóór de toepassingsgateway en de oorspronkelijke client, retourneert het `client_ip` IP-adres van de omgekeerde proxy. |
| client_port               | De clientpoort.                                             |
| client_tcp_rtt            | Informatie over de client-TCP-verbinding. Beschikbaar op systemen die de optie TCP_INFO socket ondersteunen. |
| client_user               | Wanneer HTTP-verificatie wordt gebruikt, wordt de gebruikersnaam opgegeven voor verificatie. |
| host                      | In deze volgorde van prioriteit: de hostnaam van de aanvraagregel, de hostnaam uit het veld Hostaanvraagheader of de servernaam die overeenkomt met een aanvraag. Voorbeeld: in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` is de hostwaarde `contoso.com` |
| cookie_ *naam*             | De *naamcookie.*                                           |
| http_method               | De methode die wordt gebruikt om de URL-aanvraag te maken. Bijvoorbeeld GET of POST. |
| http_status               | De sessiestatus. Bijvoorbeeld 200, 400 of 403.           |
| http_version              | Het aanvraagprotocol. Meestal HTTP/1.0, HTTP/1.1 of HTTP/2.0. |
| query_string              | De lijst met variabele/waarde-paren die volgt op de '?' in de aangevraagde URL. Voorbeeld: in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` wordt query_string waarde `id=123&title=fabrikam` |
| received_bytes            | De lengte van de aanvraag (inclusief de aanvraagregel, header en aanvraagtekst). |
| request_query             | De argumenten in de aanvraagregel.                           |
| request_scheme            | Het aanvraagschema: http of https.                           |
| request_uri               | De volledige oorspronkelijke aanvraag-URI (met argumenten). Voorbeeld: in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam*` is request_uri waarde `/article.aspx?id=123&title=fabrikam` |
| sent_bytes                | Het aantal bytes dat naar een client wordt verzonden.                        |
| server_port               | De poort van de server die een aanvraag heeft geaccepteerd.              |
| ssl_connection_protocol   | Het protocol van een tot stand gebrachte TLS-verbinding.               |
| ssl_enabled               | Aan als de verbinding werkt in de TLS-modus. Anders een lege tekenreeks. |
| uri_path                  | Identificeert de specifieke resource in de host die de webclient wil openen. Dit is het deel van de aanvraag-URI zonder de argumenten. Voorbeeld: in de aanvraag `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` wordt uri_path waarde `/article.aspx` |

### <a name="mutual-authentication-server-variables-preview"></a>Servervariabelen voor wederzijdse verificatie (preview)

Application Gateway ondersteunt de volgende servervariabelen voor wederzijdse verificatiescenario's. Gebruik deze servervariabelen op dezelfde manier als hierboven met de andere servervariabelen. 

|   Naam van de variabele    |                   Beschrijving                                           |
| ------------------------- | ------------------------------------------------------------ |
| client_certificate        | Het clientcertificaat in PEM-indeling voor een tot stand gebrachte SSL-verbinding. |
| client_certificate_end_date| De einddatum van het clientcertificaat. |
| client_certificate_fingerprint| De SHA1-vingerafdruk van het clientcertificaat voor een tot stand gebrachte SSL-verbinding. |
| client_certificate_issuer | De DN-tekenreeks van de vergever van het clientcertificaat voor een tot stand gebrachte SSL-verbinding. |
| client_certificate_serial | Het serienummer van het clientcertificaat voor een tot stand gebrachte SSL-verbinding.  |
| client_certificate_start_date| De begindatum van het clientcertificaat. |
| client_certificate_subject| De onderwerp-DN-tekenreeks van het clientcertificaat voor een tot stand gebrachte SSL-verbinding. |
| client_certificate_verification| Het resultaat van de verificatie van het clientcertificaat: *GESLAAGD,* *MISLUKT: <reason>*, of *GEEN* als er geen certificaat aanwezig is. | 

## <a name="rewrite-configuration"></a>Configuratie herschrijven

Als u een herschrijfregel wilt configureren, moet u een regelset voor herschrijven maken en de configuratie van de herschrijfregel toevoegen.

Een regelset voor herschrijven bevat:

* **Routeringsregelkoppeling aanvragen:** De herschrijfconfiguratie is via de routeringsregel gekoppeld aan de bronlistener. Wanneer u een basisregel voor doorsturen gebruikt, wordt de herschrijfconfiguratie gekoppeld aan een bronlistener en wordt de header globaal herschreven. Wanneer u een padgebaseerde routeringsregel gebruikt, wordt de herschrijfconfiguratie gedefinieerd op de URL-padkaart. In dat geval geldt deze alleen voor het specifieke padgebied van een site. U kunt meerdere herschrijfsets maken en elke herschrijfset toepassen op meerdere listeners. Maar u kunt slechts één herschrijfset toepassen op een specifieke listener.

* **Voorwaarde herschrijven:** dit is een optionele configuratie. Voorwaarden voor herschrijven evalueren de inhoud van de HTTP(S)-aanvragen en -antwoorden. De herschrijfactie treedt op als de HTTP(S)-aanvraag of het antwoord overeenkomt met de herschrijfvoorwaarde. Als u meer dan één voorwaarde aan een actie koppelt, vindt de actie alleen plaats wanneer aan alle voorwaarden wordt voldaan. Met andere woorden, de bewerking is een logische AND-bewerking.

* **Type herschrijven:** er zijn drie typen herschrijven beschikbaar:
   * Aanvraagheaders herschrijven 
   * Antwoordheaders herschrijven
   * URL-onderdelen herschrijven
      * **URL-pad:** de waarde waar het pad naar moet worden herschreven. 
      * **URL-queryreeks:** de waarde waarin de queryreeks moet worden herschreven. 
      * **Padkaart opnieuw evalueren:** wordt gebruikt om te bepalen of de URL-padkaart al dan niet opnieuw moet worden geëvalueerd. Als dit niet wordt aangevinkt, wordt het oorspronkelijke URL-pad gebruikt om overeen te komen met het padpatroon in de URL-padkaart. Als deze is ingesteld op true, wordt de URL-padkaart opnieuw geëvalueerd om de overeenkomst met het herschreven pad te controleren. Het inschakelen van deze switch helpt bij het routeren van de aanvraag naar een andere back-endpool na het herschrijven.

## <a name="rewrite-configuration-common-pitfalls"></a>Veelvoorkomende valkuilen bij het herschrijven van de configuratie

* Het inschakelen van Padkaart opnieuw evalueren is niet toegestaan voor basisregels voor doorsturen van aanvragen. Dit is om een oneindige evaluatielus voor een basisrouteringsregel te voorkomen.

* Er moet ten minste één regel voor voorwaardelijk herschrijven of één herschrijfregel zijn waarvoor padkaart opnieuw evalueren niet is ingeschakeld voor padgebaseerde routeringsregels om een oneindige evaluatielus voor een padgebaseerde routeringsregel te voorkomen.

* Binnenkomende aanvragen worden beëindigd met een 500-foutcode voor het geval er dynamisch een lus wordt gemaakt op basis van clientinvoer. De Application Gateway blijven andere aanvragen verwerken zonder dat dit in een dergelijk scenario afloakt.

### <a name="using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku"></a>URL's herschrijven of hostheader herschrijven met Web Application Firewall (WAF_v2 SKU)

Wanneer u het herschrijven van URL's of het herschrijven van de hostheader configureert, vindt de WAF-evaluatie plaats na de wijziging van de aanvraagheader of URL-parameters (post-rewrite). En wanneer u de configuratie voor het herschrijven van de URL of het herschrijven van de hostheader op uw Application Gateway verwijdert, wordt de WAF-evaluatie uitgevoerd vóór het herschrijven van de header (vooraf herschrijven). Deze volgorde zorgt ervoor dat WAF-regels worden toegepast op de laatste aanvraag die wordt ontvangen door uw back-endpool.

Stel dat u de volgende regel voor het herschrijven van headers hebt voor de header . Als de waarde van header gelijk is aan , herschrijft u de `"Accept" : "text/html"` `"Accept"` waarde vervolgens naar `"text/html"` `"image/png"` .

Nu alleen herschrijven van headers is geconfigureerd, wordt de WAF-evaluatie uitgevoerd op `"Accept" : "text/html"` . Maar wanneer u het herschrijven van URL's of het herschrijven van de hostheader configureert, wordt de WAF-evaluatie uitgevoerd op `"Accept" : "image/png"` .

>[!NOTE]
> Herschrijfbewerkingen van URL's veroorzaken naar verwachting een kleine toename in het CPU-gebruik van uw WAF-Application Gateway. Het is raadzaam om de metrische gegevens over [het CPU-gebruik](high-traffic-support.md) voor een korte periode te controleren nadat u de regels voor het herschrijven van URL's op uw WAF-Application Gateway.

### <a name="common-scenarios-for-header-rewrite"></a>Veelvoorkomende scenario's voor het herschrijven van headers

#### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Poortgegevens verwijderen uit de X-Forwarded-For-header

Application Gateway een X-Forwarded-For-header in alle aanvragen in voordat de aanvragen naar de back-end worden doorgestuurd. Deze header is een door komma's gescheiden lijst met IP-poorten. Er kunnen scenario's zijn waarin de back-endservers alleen de headers nodig hebben om IP-adressen te bevatten. U kunt header herschrijven gebruiken om de poortgegevens uit de X-Forwarded-For-header te verwijderen. Een manier om dit te doen, is door de header in te stellen op de add_x_forwarded_for_proxy servervariabele. U kunt ook de variabele client_ip:

![Poort verwijderen](./media/rewrite-http-headers-url/remove-port.png)


#### <a name="modify-a-redirection-url"></a>Een omleidings-URL wijzigen

Wanneer een back-endtoepassing een omleidingsreactie verzendt, wilt u de client mogelijk omleiden naar een andere URL dan de URL die is opgegeven door de back-endtoepassing. U wilt dit bijvoorbeeld doen wanneer een app-service wordt gehost achter een toepassingsgateway en vereist dat de client een omleiding naar het relatieve pad doet. (Bijvoorbeeld een omleiding van contoso.azurewebsites.net/path1 naar contoso.azurewebsites.net/path2.)

Omdat App Service een multitenant-service is, wordt de hostheader in de aanvraag gebruikt om de aanvraag naar het juiste eindpunt te sturen. App Services hebben een standaarddomeinnaam van .azurewebsites.net (bijvoorbeeld contoso.azurewebsites.net) die verschilt van de domeinnaam van de toepassingsgateway \* (bijvoorbeeld contoso.com). Omdat de oorspronkelijke aanvraag van de client de domeinnaam (contoso.com) van de toepassingsgateway als hostnaam heeft, wijzigt de toepassingsgateway de hostnaam in contoso.azurewebsites.net. Deze wijziging wordt zo gewijzigd dat de app-service de aanvraag kan door sturen naar het juiste eindpunt.

Wanneer de app-service een omleidingsreactie verzendt, gebruikt deze dezelfde hostnaam in de locatieheader van het antwoord als in de aanvraag die wordt ontvangen van de toepassingsgateway. De client maakt de aanvraag dus rechtstreeks naar `contoso.azurewebsites.net/path2` in plaats van via de toepassingsgateway ( `contoso.com/path2` ). Het is niet wenselijk om de toepassingsgateway over te nemen.

U kunt dit probleem oplossen door de hostnaam in de locatieheader in te stellen op de domeinnaam van de toepassingsgateway.

Hier volgen de stappen voor het vervangen van de hostnaam:

1. Maak een herschrijfregel met een voorwaarde die evalueert of de locatieheader in het antwoord azurewebsites.net. Voer het patroon `(https?):\/\/.*azurewebsites\.net(.*)$` in.
2. Voer een actie uit om de locatieheader te herschrijven, zodat deze de hostnaam van de toepassingsgateway heeft. U kunt dit doen door in `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` te invoeren als de headerwaarde. U kunt ook de servervariabele gebruiken om de hostnaam in `host` te stellen op de oorspronkelijke aanvraag.

![Locatieheader wijzigen](./media/rewrite-http-headers-url/app-service-redirection.png)

#### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>BEVEILIGINGS-HTTP-headers implementeren om beveiligingsproblemen te voorkomen

U kunt verschillende beveiligingsproblemen oplossen door de benodigde headers in het antwoord van de toepassing te implementeren. Deze beveiligingsheaders omvatten X-XSS-Protection, Strict-Transport-Security en Content-Security-Policy. U kunt Application Gateway om deze headers in te stellen voor alle antwoorden.

![Beveiligingskoptekst](./media/rewrite-http-headers-url/security-header.png)

### <a name="delete-unwanted-headers"></a>Ongewenste headers verwijderen

Mogelijk wilt u headers verwijderen die gevoelige informatie uit een HTTP-antwoord weergeven. U wilt bijvoorbeeld informatie zoals de naam van de back-endserver, het besturingssysteem of de bibliotheekgegevens verwijderen. U kunt de toepassingsgateway gebruiken om deze headers te verwijderen:

![Header verwijderen](./media/rewrite-http-headers-url/remove-headers.png)

#### <a name="check-for-the-presence-of-a-header"></a>Controleren op de aanwezigheid van een header

U kunt een HTTP-aanvraag of antwoordheader evalueren op de aanwezigheid van een header- of servervariabele. Deze evaluatie is handig als u alleen een header wilt herschrijven wanneer een bepaalde header aanwezig is.

![De aanwezigheid van een header controleren](./media/rewrite-http-headers-url/check-presence.png)

### <a name="common-scenarios-for-url-rewrite"></a>Veelvoorkomende scenario's voor het herschrijven van URL's

#### <a name="parameter-based-path-selection"></a>Padselectie op basis van parameters

Als u scenario's wilt uitvoeren waarbij u de back-endpool wilt kiezen op basis van de waarde van een header, een deel van de URL of een querytekenreeks in de aanvraag, kunt u de combinatie van de mogelijkheid voor het herschrijven van URL's en padgebaseerde routering gebruiken. Als u bijvoorbeeld een winkelwebsite hebt en de productcategorie wordt doorgegeven als querytekenreeks in de URL en u de aanvraag wilt door sturen naar de back-end op basis van de querytekenreeks, doet u het volgende:

**Stap 1:**  Een padkaart maken zoals wordt weergegeven in de onderstaande afbeelding

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-1.png" alt-text="Scenario voor het herschrijven van URL's 1-1.":::

**Stap 2 (a):** Maak een herschrijfset met drie regels voor herschrijven: 

* De eerste regel heeft een voorwaarde waarmee de *variabele query_string* wordt gecontroleerd op *category=shoes* en een actie bevat waarmee het **URL-pad** naar */listing1* wordt herschreven en padkaart opnieuw evalueren is ingeschakeld

* De tweede regel heeft een voorwaarde die de *query_string-variabele* controleert op *category=bags* en een actie bevat waarmee het **URL-pad** naar */listing2* wordt herschreven en padkaart opnieuw evalueren is ingeschakeld

* De derde regel heeft een voorwaarde waarmee de *query_string-variabele* wordt gecontroleerd op *category=accessoires* en een actie bevat waarmee het **URL-pad** naar */listing3* wordt herschreven en padtoe kaart opnieuw evalueren is ingeschakeld

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-2.png" alt-text="Scenario 1-2 voor het herschrijven van URL's.":::

 

**Stap 2 (b):** Koppel deze herschrijfset aan het standaardpad van de bovenstaande padgebaseerde regel

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario1-3.png" alt-text="Scenario 1-3 voor het herschrijven van URL's.":::

Als de gebruiker nu contoso.com/listing?category=any aanvraagt, wordt dit overeenkomen met het standaardpad, omdat geen van de padpatronen in de padkaart (/listing1, /listing2, /listing3) overeenkomen. Omdat u de bovenstaande herschrijfset aan dit pad hebt gekoppeld, wordt deze herschrijfset geëvalueerd. Omdat de querytekenreeks niet aan de voorwaarde in een van de drie herschrijfregels in deze herschrijfset komt, vindt er geen herschrijfactie plaats en wordt de aanvraag daarom ongewijzigd gerouteerd naar de back-end die is gekoppeld aan het standaardpad *(GenericList).*

Als de gebruiker een *contoso.com/listing?category=shoes,* wordt opnieuw het standaardpad gebruikt. In dit geval komt de voorwaarde in de eerste regel echter overeen en wordt daarom de actie uitgevoerd die is gekoppeld aan de voorwaarde. Hiermee wordt het URL-pad naar */listing1*  herschreven en wordt het pad-overzicht opnieuw geëvalueerd. Wanneer de path-map opnieuw wordt geëvalueerd, komt de aanvraag nu overeen met het pad dat is gekoppeld aan patroon */listing1* en wordt de aanvraag doorgeleid naar de back-end die is gekoppeld aan dit patroon, die ShoesListBackendPool is.

>[!NOTE]
>Dit scenario kan worden uitgebreid naar elke header- of cookiewaarde, URL-pad, queryreeks of servervariabelen op basis van de gedefinieerde voorwaarden en stelt u in feite in staat om aanvragen te leiden op basis van deze voorwaarden.

#### <a name="rewrite-query-string-parameters-based-on-the-url"></a>Queryreeksparameters herschrijven op basis van de URL

Denk aan een scenario van een winkelwebsite waar de zichtbare koppeling voor de gebruiker eenvoudig en leesbaar moet zijn, maar de back-endserver de queryreeksparameters nodig heeft om de juiste inhoud weer te geven.

In dat geval kunnen Application Gateway parameters uit de URL vastleggen en sleutel-waardeparen van queryreeksen uit de URL toevoegen. Stel bijvoorbeeld dat de gebruiker wil herschrijven naar , dit kan worden bereikt via de volgende configuratie voor `https://www.contoso.com/fashion/shirts` `https://www.contoso.com/buy.aspx?category=fashion&product=shirts` het herschrijven van URL's.

**Voorwaarde:** als de servervariabele `uri_path` gelijk is aan het patroon `/(.+)/(.+)`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-1.png" alt-text="Scenario 2-1 voor het herschrijven van URL's.":::

**Actie:** url-pad instellen op `buy.aspx` en queryreeks op `category={var_uri_path_1}&product={var_uri_path_2}`

:::image type="content" source="./media/rewrite-http-headers-url/url-scenario2-2.png" alt-text="Scenario voor het herschrijven van URL's 2-2.":::

Zie URL herschrijven met een Application Gateway met behulp van [Azure Portal](rewrite-url-portal.md)

### <a name="url-rewrite-vs-url-redirect"></a>URL herschrijven versus URL-omleiding

In het geval van het herschrijven van een URL herschrijft Application Gateway URL opnieuw voordat de aanvraag naar de back-end wordt verzonden. Dit verandert niet wat gebruikers in de browser zien, omdat de wijzigingen verborgen zijn voor de gebruiker.

In het geval van een URL-omleiding verzendt Application Gateway een omleidingsreactie naar de client met de nieuwe URL. Dit betekent dat de client op zijn beurt de aanvraag opnieuw moet indienen bij de nieuwe URL die is opgegeven in de omleiding. De URL die de gebruiker in de browser ziet, wordt bijgewerkt naar de nieuwe URL.

:::image type="content" source="./media/rewrite-http-headers-url/url-rewrite-vs-redirect.png" alt-text="Herschrijven versus omleiden.":::

## <a name="limitations"></a>Beperkingen

- Als een antwoord meer dan één header met dezelfde naam heeft, zal het herschrijven van de waarde van een van deze headers ertoe leiden dat de andere headers in het antwoord worden neergeschreven. Dit kan meestal gebeuren met Set-Cookie-header, omdat u meer dan één Set-Cookie in een antwoord kunt hebben. Een dergelijk scenario is wanneer u een app-service met een toepassingsgateway gebruikt en sessie-affiniteit op basis van cookies hebt geconfigureerd op de toepassingsgateway. In dit geval bevat het antwoord twee Set-Cookie headers: een die wordt gebruikt door de app-service, bijvoorbeeld: en een andere voor affiniteit met de `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` toepassingsgateway, bijvoorbeeld `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/` . Het herschrijven van een van de Set-Cookie headers in dit scenario kan ertoe leiden dat de andere header Set-Cookie uit het antwoord wordt verwijderd.
- Herschrijven wordt niet ondersteund wanneer de toepassingsgateway is geconfigureerd om de aanvragen om te leiden of om een aangepaste foutpagina weer te geven.
- Headernamen kunnen alfanumerieke tekens en specifieke symbolen bevatten, zoals gedefinieerd in [RFC 7230.](https://tools.ietf.org/html/rfc7230#page-27) Het speciale teken onderstrepingsteken ( ) in headernamen wordt momenteel \_ niet ondersteund.
- Verbindings- en upgradeheaders kunnen niet opnieuw worden geschreven

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het herschrijven van HTTP-headers met Application Gateway met Azure Portal](rewrite-http-headers-portal.md)
- [Meer informatie over het herschrijven van URL's met Application Gateway met Azure Portal](rewrite-url-portal.md)
