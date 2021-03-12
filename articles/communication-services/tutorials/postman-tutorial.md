---
title: Zelf studie-ondertekenen en aanvragen indienen bij de SMS-API van de ACS met postman
titleSuffix: An Azure Communication Services tutorial
description: Meer informatie over het ondertekenen en aanvragen van ACS met postman om een SMS-bericht te verzenden.
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 03/08/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0d98ae1ef537b06858b8c03df65bbcdd27984c4f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022277"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>Zelf studie: ondertekenen en aanvragen indienen met de Postman
In deze zelf studie wordt postman ingesteld en gebruikt om met behulp van HTTP een aanvraag uit te voeren voor services van Azure Communication Services (ACS). Aan het einde van deze zelf studie hebt u een SMS-bericht verzonden met behulp van ACS en postman. u kunt postman gebruiken om andere Api's binnen ACS te verkennen.

In deze zelf studie gaan we het volgende doen:
> [!div class="checklist"]
> * Postman downloaden
> * Postman instellen voor het ondertekenen van HTTP-aanvragen
> * Een aanvraag indienen voor de SMS-API van de ACS om een bericht te verzenden.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie. Het gratis account biedt $200 aan Azure-tegoed om een combi natie van services uit te proberen.
- Een actieve Communication Services-resource en verbindingsreeks. [Meer informatie over het maken van een communicatie Services-resource](../quickstarts/create-communication-resource.md).
- Een ACS-telefoon nummer waarmee SMS-berichten kunnen worden verzonden, vindt u [een telefoon nummer ophalen](../quickstarts/telephony-sms/get-phone-number.md) om er een te krijgen.

## <a name="downloading-and-installing-postman"></a>Postman downloaden en installeren

Postman is een bureaublad toepassing die API-aanvragen kan maken voor elke HTTP-API. Dit wordt vaak gebruikt voor het testen en verkennen van Api's. De nieuwste desktop versie wordt gedownload [vanaf de website van Postman](https://www.postman.com/downloads/). Postman heeft versies voor Windows, Mac en Linux. down load daarom de versie die geschikt is voor uw besturings systeem. Open de toepassing zodra deze is gedownload. Er wordt een start scherm weer gegeven waarin u wordt gevraagd om u aan te melden of om een postman-account te maken. Het maken van een account is optioneel en kan worden overgeslagen door te klikken op de koppeling overs Laan en naar de app te gaan. Als u een account maakt, worden de instellingen van uw API-aanvraag opgeslagen naar de Postman, waarmee u vervolgens uw aanvragen kunt ophalen op andere computers.

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Het Start scherm van dit bericht toont de mogelijkheid om een account te maken of over te slaan en naar de app te gaan.":::

Zodra u een account hebt gemaakt of er een hebt overgeslagen, ziet u nu het hoofd venster van postman.

## <a name="creating-and-configuring-a-postman-collection"></a>Een postman-verzameling maken en configureren

Met postman kunnen aanvragen op verschillende manieren worden georganiseerd. Voor de doel einden van deze zelf studie. Er wordt een postman-verzameling gemaakt. Als u dit wilt doen, selecteert u de knop verzamelingen aan de linkerkant van de toepassing:

:::image type="content" source="media/postman/collections-tab.png" alt-text="Het hoofd scherm van dit bericht wordt weer gegeven op het tabblad verzamelingen gemarkeerd.":::

Klik nadat u hebt geselecteerd op nieuwe verzameling maken om het proces voor het maken van de verzameling te starten. Er wordt een nieuw tabblad geopend in het middelste gebied van postman. Geef de verzameling de gewenste naam. Hier heet de verzameling ' ACS ':

:::image type="content" source="media/postman/acs-collection.png" alt-text="Postman met een ACS-verzameling geopend en de naam van de verzameling gemarkeerd.":::

Zodra uw verzameling is gemaakt en deze een naam heeft, kunt u deze configureren.

### <a name="adding-collection-variables"></a>Verzamelings variabelen toevoegen

Als u de verificatie wilt afhandelen en aanvragen gemakkelijker wilt maken, worden er twee verzamelings variabelen in de zojuist gemaakte ACS-verzameling opgegeven. Deze variabelen zijn beschikbaar voor alle aanvragen in uw ACS-verzameling. Ga naar het tabblad Variable's van de verzameling om aan de slag te gaan met het maken van variabelen.

:::image type="content" source="media/postman/variable-stab.png" alt-text="Postman met het tabblad variabelen van een ACS-verzameling.":::

Op het tabblad verzameling maakt u twee variabelen:
- sleutel: deze variabele moet een van uw sleutels zijn op de sleutel pagina van de Azure Communication Services in de Azure Portal. Bijvoorbeeld `oW...A==`.
- eind punt-deze variabele moet uw Azure Communication Services-eind punt zijn van de sleutel pagina. **Zorg ervoor dat u de afsluitende slash verwijdert**. Bijvoorbeeld `https://contoso.communication.azure.com`.

Voer deze waarden in in de kolom begin waarde van het scherm variabelen. Zodra u de invoer hebt ingevoerd, klikt u op de knop alles behouden boven de tabel aan de rechter kant. Wanneer het scherm van de Postman correct is geconfigureerd, moet er ongeveer als volgt uitzien:

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="Postman met de variabelen van een ACS-verzameling correct ingesteld.":::

Lees [de documentatie van Postman voor](https://learning.postman.com/docs/sending-requests/variables)meer informatie over variabelen.

### <a name="creating-a-pre-request-script"></a>Een pre-Request-script maken

De volgende stap is het maken van een pre-Request-script in postman. Een script dat voorafgaat aan aanvragen, is een script dat vóór elke aanvraag in postman wordt uitgevoerd en kan namens u aanvragen voor het wijzigen of aanpassen van de para meters. We gebruiken deze om onze HTTP-aanvragen te ondertekenen zodat ze kunnen worden geautoriseerd door ACS-Services. Meer informatie over de vereisten voor ondertekening vindt u [in onze hand leiding over verificatie](https://docs.microsoft.com/rest/api/communication/authentication).

We maken dit script in de verzameling zodanig dat deze wordt uitgevoerd op elke aanvraag in de verzameling. Hiertoe klikt u in het tabblad verzameling op het tabblad ' pre-Request-script '.

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="Postman met het pre-Request-script van een ACS-verzameling Sub-Tab geselecteerd.":::

Op dit subtabblad kunt u een pre-aanvraag script maken door het in het onderstaande tekst gebied in te voeren. Het kan gemakkelijker zijn om dit te schrijven binnen een volledige code-editor, zoals [Visual Studio code](https://code.visualstudio.com/) voordat u deze in de voltooide vorm plakt. In deze zelf studie gaan we verder met elk deel van het script. U kunt door gaan naar het einde als u het zojuist wilt kopiëren naar een postman en aan de slag wilt gaan. Laten we beginnen met het schrijven van het script.

### <a name="writing-the-pre-request-script"></a>Het script voor voorafgaande aanvragen schrijven

Het eerste wat we gaan doen, is het maken van een UTC-teken reeks (Coordinated Universal Time) en het toevoegen hiervan aan de HTTP-header date. We slaan deze teken reeks ook op in een variabele om deze later te gebruiken bij het ondertekenen:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

Vervolgens wordt de hoofd tekst van de aanvraag gehasht met SHA 256 en geplaatst in de `x-ms-content-sha256` koptekst. Postman bevat enkele [standaard bibliotheken](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries) voor hashing en het ondertekenen van wereld wijde, zodat we deze niet hoeven te installeren of vereisen:

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

We gaan nu onze eerder opgegeven eindpunt variabele gebruiken om de waarde voor de HTTP-host-header te onderscheiden. We moeten dit doen als de host-header pas is ingesteld nadat dit script is verwerkt:

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

Als u deze informatie hebt gemaakt, kunt u nu de teken reeks maken die we ondertekenen voor de HTTP-aanvraag. dit bestaat uit verschillende eerder gemaakte waarden:

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

Ten slotte moeten we deze teken reeks ondertekenen met onze ACS-sleutel en vervolgens toevoegen aan de aanvraag in de `Authorization` header:

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>Het laatste script dat vooraf is aangevraagd

Het laatste script dat voorafgaat aan de aanvraag moet er ongeveer als volgt uitzien:

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

Voer dit laatste script in of plak dit in het tekst gebied op het tabblad script voor voorafgaande aanvragen:

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Postman met het pre-Request-script van een ACS-verzameling dat is ingevoerd.":::

Zodra u het bestand hebt ingevoerd, drukt u op CTRL + S of drukt u op de knop Opslaan. Hierdoor wordt het script op de verzameling opgeslagen. 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="De knop script voor het vooraf aanvragen van een bericht opslaan.":::

## <a name="creating-a-request-in-postman"></a>Een aanvraag in postman maken

Nu alles is ingesteld, kunt u een ACS-aanvraag maken binnen een bericht. Als u aan de slag wilt gaan, klikt u op het plus teken (+) naast de ACS-verzameling:

:::image type="content" source="media/postman/create-request.png" alt-text="De plus knop van postman.":::

Hiermee maakt u een nieuw tabblad voor onze aanvraag binnen het bericht. Bij het maken moeten we het configureren. Er wordt een aanvraag gedaan voor de SMS Send API. Raadpleeg de [documentatie voor deze API voor hulp](https://docs.microsoft.com/rest/api/communication/sms/send). Laten we het verzoek van de Postman configureren.

Begin met instellen, het aanvraag type aan `POST` en invoeren `{{endpoint}}/sms?api-version=2021-03-07` in het veld aanvraag-URL. Deze URL maakt gebruik van onze eerder gemaakte `endpoint` variabele om deze automatisch te verzenden naar uw ACS-resource.

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="Een postman-aanvraag, waarbij het type is ingesteld op POST en de URL correct is ingesteld.":::

Selecteer nu het tabblad hoofd tekst van de aanvraag en wijzig vervolgens het keuze rondje onder aan RAW. Aan de rechter kant ziet u een vervolg keuzelijst ' tekst ', wijzigt u deze in JSON:

:::image type="content" source="media/postman/postman-json.png" alt-text="De hoofd tekst van de aanvraag instellen op RAW en JSON":::

Hiermee wordt de aanvraag geconfigureerd voor het verzenden en ontvangen van informatie in een JSON-indeling.

In het tekst gebied hieronder moet u een aanvraag tekst invoeren. deze moet de volgende indeling hebben:

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

Voor de waarde ' van ' moet u [een telefoon nummer](../quickstarts/telephony-sms/get-phone-number.md) in de ACS-Portal vinden, zoals eerder is vermeld. Voer de waarde in zonder spaties en voorafgegaan door uw land code. Bijvoorbeeld: `+15555551234`. Uw "bericht" kan alles zijn wat u wilt verzenden, maar dit `Hello from ACS` is een goed voor beeld. De "to"-waarde moet een telefoon zijn waartoe u toegang hebt. deze kan SMS-berichten ontvangen. Het is een goed idee om uw eigen mobiele apparaten te gebruiken.

Zodra de aanvraag is ingevoerd, moet deze worden opgeslagen in de ACS-verzameling die u eerder hebt gemaakt. Dit zorgt ervoor dat de variabelen en het script dat eerder is gemaakt, worden opgehaald. Hiertoe klikt u op de knop ' opslaan ' in de rechter bovenhoek van het aanvraag gebied.

:::image type="content" source="media/postman/postman-save.png" alt-text="De knop Opslaan voor een postman-aanvraag.":::

Er wordt dan een dialoog venster weer gegeven waarin u wordt gevraagd, wat u de aanvraag wilt aanroepen en waar u het wilt opslaan. U kunt een naam opgeven wat u graag wilt, maar zorg ervoor dat u uw ACS-verzameling selecteert in de onderste helft van het dialoog venster:

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="Het dialoog venster postman aanvraag opslaan met de ACS-verzameling geselecteerd.":::

## <a name="sending-a-request"></a>Een aanvraag verzenden

Nu alles is ingesteld, moet u de aanvraag kunnen verzenden en een SMS-bericht ontvangen op uw telefoon. Als u dit wilt doen, moet u ervoor zorgen dat uw gemaakte aanvraag is geselecteerd en drukt u op de knop verzenden aan de rechter kant:

:::image type="content" source="media/postman/postman-send.png" alt-text="Een postman-aanvraag, met de knop verzenden gemarkeerd.":::

Als alles goed is geworden, ziet u nu het antwoord van ACS. dit moet 202-status code zijn:

:::image type="content" source="media/postman/postman-202.png" alt-text="Een postman-aanvraag is verzonden met een 202-status code.":::

De mobiele telefoon, die eigenaar is van het nummer dat u hebt ingevoerd in de waarde ' to ', moet ook een SMS-bericht hebben ontvangen. U hebt nu een werkend postman ingesteld, waarmee kan worden gecommuniceerd met ACS-Services en SMS-berichten te verzenden.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ACS Api's verkennen](https://docs.microsoft.com/rest/api/communication/) 
>  [Meer informatie over verificatie](https://docs.microsoft.com/rest/api/communication/authentication) 
>  Meer [informatie over verzen](https://learning.postman.com/) ding

U kunt ook het volgende doen:

- [Chat aan uw app toevoegen](../quickstarts/chat/get-started.md)
- [Tokens voor gebruikers toegang maken](../quickstarts/access-tokens.md)
- [Meer leren over architectuur van client en server](../concepts/client-and-server-architecture.md)
- [Meer leren over verificatie](../concepts/authentication.md)