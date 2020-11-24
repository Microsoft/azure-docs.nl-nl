---
title: Een telefoon gesprek doen vanuit Twilio (Java) | Microsoft Docs
description: Meer informatie over het maken van een telefoon gesprek vanaf een webpagina met behulp van Twilio in een Java-toepassing in Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: cad25990d0ba7e27f4a90adc587f0c8a0a2c551a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521067"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Een telefoon gesprek doen met behulp van Twilio in een Java-toepassing in azure
In het volgende voor beeld ziet u hoe u Twilio kunt gebruiken om een aanroep te doen van een webpagina die wordt gehost in Azure. De resulterende toepassing vraagt de gebruiker om telefoon gesprek waarden, zoals wordt weer gegeven in de volgende scherm afbeelding.

![Azure-oproep formulier met Twilio en Java][twilio_java]

U moet de volgende stappen uitvoeren om de code in dit onderwerp te gebruiken:

1. Haal een Twilio-account en-verificatie token op. Als u aan de slag wilt gaan met Twilio, evalueert u de prijzen op [https://www.twilio.com/pricing][twilio_pricing] . U kunt zich aanmelden bij [https://www.twilio.com/try-twilio][try_twilio] . Zie voor informatie over de API van Twilio [https://www.twilio.com/api][twilio_api] .
2. Haal het Twilio JAR op. [https://github.com/twilio/twilio-java][twilio_java_github]U kunt de GitHub-bronnen downloaden en uw eigen jar maken, of een vooraf gebouwde jar (met of zonder afhankelijkheden) downloaden.
   De code in dit onderwerp is geschreven met behulp van het vooraf gemaakte TwilioJava-3.3.8-with-dependencies JAR.
3. Voeg het JAR toe aan uw Java-build-pad.
4. Als u een eclips gebruikt om deze Java-toepassing te maken, neemt u het Twilio JAR op in het toepassings implementatie bestand (WAR) met behulp van de implementatie-assembly functie van eclips. Als u geen gebruik maakt van eclips om deze Java-toepassing te maken, moet u ervoor zorgen dat het Twilio JAR deel uitmaakt van dezelfde Azure-rol als uw Java-toepassing en dat deze wordt toegevoegd aan het pad naar de klasse van uw toepassing.
5. Zorg ervoor dat uw cacerts-basis archief het certificaat van de Equifax Secure Certificate Authority bevat met MD5-vinger afdruk 67: CB: 9D: C0:13:24:8 bis: 82:9B: B2:17:1E: D1:1B: EC: D4 (het serie nummer is 35: de: F4: CF en de SHA1-vinger afdruk is D2:32:09: AD: 23: D3:14:23::................ Dit is het certificaat van de certificerings instantie (CA) voor de [https://api.twilio.com][twilio_api_service] service, die wordt aangeroepen wanneer u Twilio-api's gebruikt. Zie [een certificaat toevoegen aan het certificaat archief van de Java-ca][add_ca_cert]voor meer informatie over het toevoegen van dit CA-certificaat aan de CAcert-opslag van uw JDK.

Daarnaast wordt de informatie over het maken van [een Hallo wereld-toepassing met behulp van de Azure-Toolkit voor eclipse][azure_java_eclipse_hello_world], of met andere technieken voor het hosten van Java-toepassingen in azure, ook nadrukkelijk aanbevolen.

## <a name="create-a-web-form-for-making-a-call"></a>Een webformulier maken voor het aanroepen van een gesprek
De volgende code laat zien hoe u een webformulier maakt om gebruikers gegevens op te halen voor het maken van een aanroep. In dit voor beeld is er een nieuw dynamisch webproject, met de naam **TwilioCloud**, gemaakt en **callform.jsp** is toegevoegd als een JSP-bestand.

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Automated call form</title>
    </head>
    <body>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <br/>
        <form action="makecall.jsp" method="post">
            <table>
                <tr>
                    <td>To:</td>
                    <td><input type="text" size=50 name="callTo" value="" />
                    </td>
                </tr>
                <tr>
                    <td>From:</td>
                    <td><input type="text" size=50 name="callFrom" value="" />
                    </td>
                </tr>
                <tr>
                    <td>Call message:</td>
                    <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
                    </td>
                </tr>
                <tr>
                    <td colspan=2><input type="submit" value="Make this call" />
                    </td>
                </tr>
            </table>
        </form>
        <br/>
    </body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>De code maken om de aanroep uit te voeren
De volgende code, die wordt aangeroepen wanneer de gebruiker het formulier voltooit dat wordt weer gegeven door callform.jsp, maakt het aanroep bericht en genereert de aanroep. In dit voor beeld heeft het JSP-bestand de naam **makecall.jsp** en is het toegevoegd aan het **TwilioCloud** -project. (Gebruik uw Twilio-account en verificatie token in plaats van de waarden voor tijdelijke aanduidingen die zijn toegewezen aan **accountSID** en **authToken** in de onderstaande code.)

```jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
import="java.util.*"
import="com.twilio.*"
import="com.twilio.sdk.*"
import="com.twilio.sdk.resource.factory.*"
import="com.twilio.sdk.resource.instance.*"
pageEncoding="ISO-8859-1" %>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
        <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
<%
try 
{
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Instantiate an instance of the Twilio client.     
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to retrieve the CallFactory.
    Account account = client.getAccount();

    // Display the client endpoint. 
    out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

    // Display the API version.
    String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
    out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

    // Retrieve the values entered by the user.
    String callTo = request.getParameter("callTo");  
    // The Outgoing Caller ID, used for the From parameter,
    // must have previously been verified with Twilio.
    String callFrom = request.getParameter("callFrom");
    String userText = request.getParameter("callText");

    // Replace spaces in the user's text with '%20', 
    // to make the text suitable for a URL.
    userText = userText.replace(" ", "%20");

    // Create a URL using the Twilio message and the user-entered text.
    String Url="https://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=" + userText;

    // Display the message URL.
    out.println("<p>");
    out.println("The URL is " + Url);
    out.println("</p>");

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", callFrom);
    params.put("To", callTo);
    params.put("Url", Url);

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);
    out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
} 
catch (TwilioRestException e) 
{
    out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
catch (Exception e) 
{
    out.println("<p>Exception encountered: " + e.getMessage() + "");
    out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
}
%>
    </body>
</html>
```

Naast het aanroepen van de aanroep, geeft makecall.jsp het Twilio-eind punt, de API-versie en de oproep status weer. Een voor beeld is de volgende scherm afbeelding:

![Azure-aanroep antwoord met Twilio en Java][twilio_java_response]

## <a name="run-the-application"></a>De toepassing uitvoeren
Hieronder vindt u de stappen op hoog niveau om uw toepassing uit te voeren. meer informatie over deze stappen vindt u in [een Hallo wereld-toepassing maken met behulp van de Azure-Toolkit voor eclipse][azure_java_eclipse_hello_world].

1. Exporteer uw TwilioCloud-WAR naar de map Azure **AppRoot** . 
2. Wijzig **Startup. cmd** om uw TWILIOCLOUD-War uit te pakken.
3. Compileer uw toepassing voor de compute-emulator.
4. Start uw implementatie in de compute-emulator.
5. Open een browser en voer uit `http://localhost:8080/TwilioCloud/callform.jsp` .
6. Voer waarden in het formulier in, klik op **deze aanroep maken** en Bekijk de resultaten in makecall.jsp.

Wanneer u klaar bent om te implementeren in azure, compileert u opnieuw voor implementatie naar de Cloud, implementeert u deze naar Azure en voert u http://*your_hosted_name*. cloudapp.net/TwilioCloud/callform.jsp uit in de browser (Vervang uw waarde voor *your_hosted_name*).

## <a name="next-steps"></a>Volgende stappen
Deze code werd verschaft om u de basis functionaliteit te laten zien met behulp van Twilio in java in Azure. Voordat u naar Azure implementeert in productie, wilt u mogelijk meer fout afhandeling of andere functies toevoegen. Bijvoorbeeld:

* In plaats van een webformulier te gebruiken, kunt u Azure Storage-blobs of SQL Database gebruiken om telefoon nummers op te slaan en tekst aan te roepen. Zie [How to use the Blob Storage service from Java][howto_blob_storage_java](Engelstalig) voor meer informatie over het gebruik van Azure Storage-blobs in Java. 
* U kunt **RoleEnvironment. getConfigurationSettings** gebruiken om de Twilio-account-id en het verificatie token op te halen uit de configuratie-instellingen van uw implementatie, in plaats van de waarden in makecall.jsp vast te schrijven. Zie [de Azure service runtime-bibliotheek in JSP gebruiken][azure_runtime_jsp]voor meer informatie over de **RoleEnvironment** -klasse.
* Met de makecall.jsp code wordt een Twilio-URL, [https://twimlets.com/message][twimlet_message_url] ,, toegewezen aan de **URL** -variabele. Deze URL biedt een TwiML-antwoord (Twilio Markup Language) waarmee Twilio wordt geïnformeerd over hoe u kunt door gaan met de aanroep. De TwiML die wordt geretourneerd, kan bijvoorbeeld een **&lt; dicteer &gt;** opdracht bevatten die resulteert in de tekst die wordt gesp roken naar de ontvanger van het gesprek. In plaats van de Twilio-URL te gebruiken, kunt u uw eigen service bouwen om te reageren op de aanvraag van Twilio. Zie [Twilio gebruiken voor spraak-en SMS-mogelijkheden in Java][howto_twilio_voice_sms_java]voor meer informatie. Meer informatie over TwiML vindt u op [https://www.twilio.com/docs/api/twiml][twiml] , en meer informatie over **&lt; dict &gt;** en andere Twilio-werk woorden vindt u op [https://www.twilio.com/docs/api/twiml/say][twilio_say] .
* Lees de Twilio Security-richt lijnen op [https://www.twilio.com/docs/security][twilio_docs_security] .

Zie voor meer informatie over Twilio [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a>Zie ook
* [Twilio gebruiken voor spraak-en SMS-mogelijkheden in Java][howto_twilio_voice_sms_java]
* [Een certificaat toevoegen aan het certificaat archief van de Java-CA][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: /azure/developer/java/sdk/java-sdk-add-certificate-ca-store
[azure_java_eclipse_hello_world]: /java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: /previous-versions/azure/hh690948(v=azure.100)
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg