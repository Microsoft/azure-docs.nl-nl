---
title: Twilio gebruiken voor spraak en SMS (Java) | Microsoft Docs
description: Meer informatie over het maken van een telefoon gesprek en het verzenden van een SMS-bericht met de Twilio API-service in Azure. Code voorbeelden geschreven in Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: e7ff925534a9cab6523097f84a8ae199e9cb83eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548723"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Twilio gebruiken voor spraak-en SMS-mogelijkheden in Java
In deze hand leiding wordt gedemonstreerd hoe u veelvoorkomende programmeer taken uitvoert met de Twilio API-service in Azure. De besproken scenario's zijn onder andere het maken van een telefoon oproep en het verzenden van een SMS-bericht (Short Message Service). Zie de sectie [volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en SMS in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is een Telephony Web-Service-API waarmee u uw bestaande webtalen en-vaardig heden kunt gebruiken om spraak-en SMS-toepassingen te bouwen. Twilio is een service van derden (geen Azure-functie en geen micro soft-product).

Met **Twilio Voice** kunnen uw toepassingen telefoon gesprekken voeren en ontvangen. Met **TWILIO SMS** kunnen uw toepassingen SMS-berichten maken en ontvangen. Met **Twilio-client** kunnen uw toepassingen spraak communicatie mogelijk maken via bestaande Internet verbindingen, waaronder mobiele verbindingen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Prijzen voor Twilio en speciale aanbiedingen
Informatie over prijzen voor Twilio is beschikbaar op [Twilio-prijzen][twilio_pricing]. Azure-klanten ontvangen een [speciale aanbieding][special_offer]: een gratis tegoed van 1000 teksten of 1000 binnenkomende minuten. Als u zich voor deze aanbieding wilt aanmelden of meer informatie wilt krijgen, gaat u naar [https://ahoy.twilio.com/azure][special_offer] .

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio-API is een resterende API die spraak-en SMS-functionaliteit biedt voor toepassingen. Client bibliotheken zijn beschikbaar in meerdere talen. Zie [TWILIO API libraries][twilio_libraries]voor een lijst.

De belangrijkste aspecten van de Twilio-API zijn Twilio-werk woorden en Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio-werk woorden
De API maakt gebruik van Twilio-werk woorden. de term **&lt; zeg &gt;** geeft bijvoorbeeld Twilio aan audibly een bericht te verzenden bij een aanroep.

Hier volgt een lijst met Twilio-werk woorden.

* **&lt; Dial &gt;**: verbindt de beller met een andere telefoon.
* **&lt; Verzamelen &gt;**: Hiermee worden numerieke cijfers op het telefoon blok verzameld.
* **&lt; Ophangen &gt;**: beëindigt een aanroep.
* **&lt; Afspelen &gt;**: Hiermee wordt een audio bestand afgespeeld.
* **&lt; Wachtrij &gt;**: Voeg het toe aan een wachtrij met bellers.
* **&lt; Pause &gt;**: wacht een opgegeven aantal seconden op de achtergrond.
* **&lt; Record &gt;**: registreert de stem van de beller en retourneert een URL van een bestand dat de opname bevat.
* **&lt; Omleiden &gt;**: overdracht van de besturing van een aanroep of SMS naar de TWIML op een andere URL.
* **&lt; Afwijzen &gt;**: Hiermee wordt een inkomende oproep naar uw Twilio-nummer afgewezen zonder dat u wordt gefactureerd.
* **&lt; Zeg &gt;**: Hiermee wordt tekst geconverteerd naar spraak die tijdens een aanroep wordt gemaakt.
* **&lt; SMS &gt;**: er wordt een SMS-bericht verzonden.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML is een reeks op XML gebaseerde instructies op basis van de Twilio-werk woorden die Twilio over het verwerken van een aanroep of SMS.

Zo zou de volgende TwiML de tekst converteren **Hallo wereld!** naar spraak.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Wanneer uw toepassing de Twilio API aanroept, is een van de API-para meters de URL die het TwiML-antwoord retourneert. Voor ontwikkelings doeleinden kunt u Twilio-Url's gebruiken om de TwiML-reacties te geven die worden gebruikt door uw toepassingen. U kunt ook uw eigen Url's hosten om de TwiML-reacties te maken en een andere optie is het object **TwiMLResponse** te gebruiken.

Zie [TwiML][twiml]voor meer informatie over Twilio-werk woorden, hun kenmerken en TwiML. Zie [TWILIO API][twilio_api](Engelstalig) voor meer informatie over de TWILIO-API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Wanneer u klaar bent om een Twilio-account op te halen, meldt u zich aan bij [try Twilio][try_twilio]. U kunt beginnen met een gratis account en later uw account bijwerken.

Wanneer u zich aanmeldt voor een Twilio-account, ontvangt u een account-ID en een verificatie token. Beide zijn nodig voor het maken van Twilio-API-aanroepen. Zorg ervoor dat uw verificatie token is beveiligd om onbevoegde toegang tot uw account te voor komen. Uw account-ID en verificatie token kunnen respectievelijk worden weer gegeven in de [Twilio-console][twilio_console], in de velden met de naam **account sid** en **verificatie token**.

## <a name="create-a-java-application"></a><a id="create_app"></a>Een Java-toepassing maken
1. Haal het Twilioe JAR op en voeg deze toe aan uw Java-build-pad en uw WAR-implementatie-assembly. [https://github.com/twilio/twilio-java][twilio_java]U kunt de GitHub-bronnen downloaden en uw eigen jar maken, of een vooraf gebouwde jar (met of zonder afhankelijkheden) downloaden.
2. Zorg ervoor dat de JDK van de **cacerts** -opslag bevat het certificaat voor de Equifax Secure Certificate Authority met MD5-vinger afdruk 67: CB: 9D: C0:13:24:8 bis: 82:9b: B2:17:1e: D1:1b: EC: D4 (het serie nummer is 35: de: F4: CF en de SHA1-vinger afdruk is D2:32:09: AD: 23: D3:14:23::................ Dit is het certificaat van de certificerings instantie (CA) voor de [https://api.twilio.com][twilio_api_service] service, die wordt aangeroepen wanneer u Twilio-api's gebruikt.

Gedetailleerde instructies voor het gebruik van de Twilio-client bibliotheek voor Java zijn beschikbaar voor [het maken van een telefoon oproep met behulp van Twilio in een Java-toepassing in azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uw toepassing configureren voor het gebruik van Twilio-bibliotheken
U kunt binnen uw code **import** instructies toevoegen boven aan de bron bestanden voor de Twilio-pakketten of-klassen die u wilt gebruiken in uw toepassing.

Voor Java-bron bestanden:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Bron bestanden voor Java Server-pagina's (JSP):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
```

Afhankelijk van de Twilio-pakketten of-klassen die u wilt gebruiken, kunnen uw **import** instructies afwijken.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Procedure: een uitgaande oproep doen
Hieronder ziet u hoe u een uitgaande oproep maakt met behulp van de **aanroep** klasse. Deze code maakt ook gebruik van een Twilio-site om het TwiML-antwoord (Twilio Markup Language) te retour neren. Vervang uw waarden door **van** en **naar** telefoon nummers en zorg ervoor dat u het telefoon nummer **van** uw Twilio-account controleert voordat u de code uitvoert.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Zie voor meer informatie over de para meters die worden door gegeven aan de methode **call. Creator** [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] .

Zoals vermeld, gebruikt deze code een Twilio-site voor het retour neren van het TwiML-antwoord. U kunt in plaats daarvan uw eigen site gebruiken om het TwiML-antwoord op te geven. Zie [TwiML-antwoorden bieden in een Java-toepassing op Azure](#howto_provide_twiml_responses)voor meer informatie.

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Procedure: een SMS-bericht verzenden
Hieronder ziet u hoe u een SMS-bericht verzendt met behulp van de **bericht** klasse. Het **van** -nummer, **4155992671**, wordt verschaft door Twilio voor proef accounts om SMS-berichten te verzenden. Het **to** -nummer moet worden geverifieerd voor uw Twilio-account voordat u de code uitvoert.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Zie voor meer informatie over de para meters die worden door gegeven aan de methode **Message. Creator** [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms] .

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Procedure: TwiML-reacties van uw eigen website opgeven
Wanneer uw toepassing een aanroep initieert naar de Twilio-API, bijvoorbeeld via de methode **CallCreator. Create** , stuurt Twilio uw aanvraag naar een URL die naar verwachting een TwiML-antwoord retourneert. In het bovenstaande voor beeld wordt gebruikgemaakt van de Twilio-URL [https://twimlets.com/message][twimlet_message_url] . (Hoewel TwiML is ontworpen voor gebruik door webservices, kunt u de TwiML in uw browser weer geven. Klik bijvoorbeeld op [https://twimlets.com/message][twimlet_message_url] om een leeg **&lt; antwoord &gt;** element weer te geven. Klik op een ander voor beeld [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] om een **&lt; &gt; antwoord** element te zien dat een element **&lt; dict &gt;** bevat.)

In plaats van te vertrouwen op de Twilio-URL, kunt u uw eigen URL-site maken die HTTP-antwoorden retourneert. U kunt de site maken in elke taal die HTTP-antwoorden retourneert; in dit onderwerp wordt ervan uitgegaan dat u de URL op een JSP-pagina host.

De volgende JSP-pagina resulteert in een TwiML-antwoord met de tekst **Hallo wereld!** op de aanroep.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

De volgende JSP-pagina resulteert in een TwiML-antwoord met een tekst, bevat verschillende pauzes en geeft informatie over de Twilio-API-versie en de naam van de Azure-rol.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

De para meter **ApiVersion** is beschikbaar in Twilio-spraak aanvragen (geen SMS-aanvragen). Zie en voor een overzicht van de beschik bare aanvraag parameters voor Twilio Voice-en SMS-aanvragen <https://www.twilio.com/docs/api/twiml/twilio_request> <https://www.twilio.com/docs/api/twiml/sms/twilio_request> . De omgevings variabele **rolnaam** is beschikbaar als onderdeel van een Azure-implementatie. (Als u aangepaste omgevings variabelen wilt toevoegen, zodat ze kunnen worden opgehaald uit **System. GETENV**, raadpleegt u de sectie omgevings variabelen in de [configuratie-instellingen voor diverse rollen][misc_role_config_settings].)

Zodra u uw JSP-pagina hebt ingesteld om TwiML-antwoorden te geven, gebruikt u de URL van de JSP-pagina als de URL die wordt door gegeven aan de methode **call. Creator** . Als u bijvoorbeeld een webtoepassing met de naam MyTwiML hebt geïmplementeerd in een door Azure gehoste service en de naam van de JSP-pagina mytwiml.jsp is, kan de URL worden door gegeven aan **call. Creator** , zoals wordt weer gegeven in het volgende:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Een andere optie voor het reageren met TwiML is via de klasse **VoiceResponse** , die beschikbaar is in het pakket **com. twilio. TwiML** .

Zie [hoe u een telefoon oproep maakt met behulp van Twilio in een Java-toepassing in azure][howto_phonecall_java]voor meer informatie over het gebruik van Twilio in azure met Java.

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Procedure: extra Twilio Services gebruiken
Naast de voor beelden die hier worden weer gegeven, biedt Twilio-Api's die u kunt gebruiken om gebruik te maken van extra Twilio-functionaliteit van uw Azure-toepassing. Zie de [TWILIO API-documentatie][twilio_api_documentation]voor meer informatie.

## <a name="next-steps"></a><a id="NextSteps"></a>Volgende stappen
Nu u de basis principes van de Twilio-service hebt geleerd, volgt u deze koppelingen voor meer informatie:

* [Twilio-beveiligings richtlijnen][twilio_security_guidelines]
* [Twilio procedure-en voorbeeld code][twilio_howtos]
* [Zelf studies voor Twilio Quick Start][twilio_quickstarts]
* [Twilio op GitHub][twilio_on_github]
* [Neem contact op met ondersteuning voor Twilio][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: /previous-versions/azure/hh690945(v=azure.100)
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
