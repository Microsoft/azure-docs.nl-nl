---
title: Problemen met Azure AD-toepassingsproxy CORS begrijpen en oplossen
description: Biedt een uitleg over CORS in azure AD-toepassingsproxy en hoe u CORS-problemen kunt identificeren en oplossen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/23/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: b57fc7e3af99819c9b27b6bc796e501d1db02818
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259165"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Problemen met CORS voor een Azure Active Directory-toepassingsproxy begrijpen en oplossen

[Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) kan soms uitdagingen opleveren voor de apps en api's die u via de Azure Active Directory-toepassingsproxy publiceert. In dit artikel worden de problemen met Azure AD-toepassingsproxy CORS en oplossingen besproken.

Browser beveiliging voor komt doorgaans dat een webpagina AJAX-aanvragen naar een ander domein brengt. Deze beperking wordt *hetzelfde-Origin-beleid* genoemd en voor komt dat een schadelijke site gevoelige gegevens van een andere site leest. Soms wilt u echter dat andere sites uw web-API kunnen aanroepen. CORS is een W3C-standaard waarmee een server hetzelfde-Origin-beleid kan versoepelen en enkele cross-Origin-aanvragen kan afwijzen.

## <a name="understand-and-identify-cors-issues"></a>CORS-problemen begrijpen en identificeren

Twee Url's hebben dezelfde oorsprong als ze identieke schema's, hosts en poorten ([RFC 6454](https://tools.ietf.org/html/rfc6454)) hebben, zoals:

-   http: \/ /contoso.com/foo.html
-   http: \/ /contoso.com/bar.html

De volgende Url's hebben verschillende oorsprongen dan de vorige twee:

-   http: \/ /contoso.net-ander domein
-   http: \/ /contoso.com:9000/foo.html-andere poort
-   https: \/ /contoso.com/foo.html-ander schema
-   http: \/ /www.contoso.com/foo.html-ander subdomein

Met hetzelfde basis beleid voor komt u dat apps toegang hebben tot resources van andere oorsprong, tenzij ze de juiste toegangs beheer headers gebruiken. Als de CORS-headers ontbreken of onjuist zijn, mislukken de cross-Origin-aanvragen. 

U kunt CORS-problemen identificeren met behulp van hulpprogram ma's voor fout opsporing in de browser:

1. Start de browser en ga naar de web-app.
1. Druk op **F12** om de foutopsporingsconsole weer te geven.
1. Probeer de transactie te reproduceren, en bekijk het consolebericht. Een CORS-schending produceert een consolefout over de oorsprong.

Selecteer in de volgende scherm afbeelding de knop **Probeer het opnieuw** met een CORS-fout bericht dat https:/corswebclient-contoso.msappproxy.net niet is \/ gevonden in de header Access-Control-Allow-Origin.

![CORS-probleem](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS-uitdagingen met toepassings proxy

In het volgende voor beeld ziet u een typisch Azure AD-toepassingsproxy CORS-scenario. De interne server fungeert als host voor een **CORSWebService** Web API-controller en een **CORSWebClient** die **CORSWebService** aanroept. Er is een AJAX-aanvraag van **CORSWebClient** naar **CORSWebService**.

![On-premises same-Origin-aanvraag](./media/application-proxy-understand-cors-issues/image1.png)

De CORSWebClient-app werkt wanneer u deze on-premises host, maar kan niet worden geladen of fouten bij het publiceren via Azure AD-toepassingsproxy. Als u de CORSWebClient-en CORSWebService-apps afzonderlijk als verschillende apps hebt gepubliceerd via toepassings proxy, worden de twee apps gehost op verschillende domeinen. Een AJAX-aanvraag van CORSWebClient naar CORSWebService is een cross-Origin-aanvraag en mislukt.

![CORS-aanvraag toepassings proxy](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Oplossingen voor problemen met CORS voor toepassings proxy

U kunt het voor gaande CORS-probleem op verschillende manieren oplossen.

### <a name="option-1-set-up-a-custom-domain"></a>Optie 1: een aangepast domein instellen

Gebruik een aangepast Azure AD-toepassingsproxy- [domein](./application-proxy-configure-custom-domain.md) om te publiceren vanuit dezelfde oorsprong, zonder dat u wijzigingen hoeft aan te brengen in de namen van apps, code of kopteksten. 

### <a name="option-2-publish-the-parent-directory"></a>Optie 2: de bovenliggende map publiceren

Publiceer de bovenliggende map van beide apps. Deze oplossing werkt bijzonder goed als u slechts twee apps op de webserver hebt. In plaats van elke app afzonderlijk te publiceren, kunt u de gemeen schappelijke bovenliggende map publiceren, wat resulteert in dezelfde oorsprong.

In de volgende voor beelden ziet u de Azure AD-toepassingsproxy-pagina van de portal voor de CORSWebClient-app.  Wanneer de **interne URL** is ingesteld op *contoso.com/CORSWebClient*, kan de app geen geslaagde aanvragen naar de *contoso.com/CORSWebService* -map maken, omdat ze cross-Origin zijn. 

![App afzonderlijk publiceren](./media/application-proxy-understand-cors-issues/image4.png)

Stel in plaats daarvan de **interne URL** in voor het publiceren van de bovenliggende map, met inbegrip van de *CORSWebClient* -en *CORSWebService* -directory's:

![Bovenliggende map publiceren](./media/application-proxy-understand-cors-issues/image5.png)

Met de resulterende app-Url's wordt het CORS-probleem effectief opgelost:

- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebService
- https: \/ /corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Optie 3: HTTP-headers bijwerken

Voeg een aangepaste HTTP-antwoord header toe aan de webservice zodat deze overeenkomt met de oorspronkelijke aanvraag. Voor websites die in Internet Information Services (IIS) worden uitgevoerd, gebruikt u IIS-beheer om de header te wijzigen:

![Aangepaste reactie header toevoegen in IIS-beheer](./media/application-proxy-understand-cors-issues/image6.png)

Voor deze aanpassing zijn geen code wijzigingen vereist. U kunt deze controleren in de Fiddler-traceringen:

**De koptekst toevoeging plaatsen**\
HTTP/1.1 200 OK \
Cache-Control: no-cache \
Pragma: no-cache \
Content-type: Text/Plain; charset = UTF-8 \
Expires:-1 \
Variëren: Accept-Encoding \
Server: micro soft-IIS/8.5 micro soft-HTTPAPI/2.0 \
**Access-Control-Allow-Origin: https \: //corswebclient-contoso.msappproxy.net**\
X-AspNet-version: 4.0.30319 \
X-aangedreven-op: ASP.NET \
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Optie 4: de App wijzigen

U kunt de App wijzigen zodat CORS wordt ondersteund door de header Access-Control-Allow-Origin toe te voegen, met de juiste waarden. De manier om de header toe te voegen, is afhankelijk van de code taal van de app. Het wijzigen van de code is de minst aanbevolen optie, omdat hiervoor de meeste inspanning nodig is.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Optie 5: de levens duur van het toegangs token verlengen

Enkele CORS-problemen kunnen niet worden opgelost, bijvoorbeeld wanneer uw app omleidt naar *login.microsoftonline.com* om te verifiëren en het toegangs token verloopt. De CORS-aanroep mislukt dan. Een tijdelijke oplossing voor dit scenario is om de levensduur van het toegangstoken te verlengen, zodat het niet verloopt tijdens de sessie van een gebruiker. Zie [Configureer bare token levensduur in azure AD](../develop/active-directory-configurable-token-lifetimes.md)voor meer informatie over hoe u dit doet.

## <a name="see-also"></a>Zie ook
- [Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Plan een implementatie voor de Azure AD-toepassingsproxy](application-proxy-deployment-plan.md) 
- [Externe toegang tot on-premises toepassingen via Azure Active Directory-toepassingsproxy](application-proxy.md)