---
title: Proxy voor Azure Active Directory SMART on FHIR
description: In deze zelfstudie wordt beschreven hoe u een proxy gebruikt om SMART on FHIR-toepassingen in te schakelen met Azure API for FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 04/02/2019
ms.openlocfilehash: 2e13a9fc32964781dda07e5534e5cab79868ddf0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995536"
---
# <a name="tutorial-azure-active-directory-smart-on-fhir-proxy"></a>Zelfstudie: Proxy voor Azure Active Directory SMART on FHIR

[SMART on FHIR](https://docs.smarthealthit.org/) is een set open specificaties voor het integreren van partnertoepassingen met FHIR-servers en systemen voor elektronische patiëntendossiers met FHIR-interfaces. Een van de belangrijkste doelen van de specificaties is om te beschrijven hoe een toepassing verificatie-eindpunten voor een FHIR-server moet detecteren en een verificatiereeks moet starten. 

Verificatie is gebaseerd op OAuth2. Maar omdat SMART on FHIR naamconventies voor parameters gebruikt die niet direct compatibel zijn met Azure Active Directory (Azure AD), biedt Azure API for FHIR een ingebouwde Azure AD SMART on FHIR-proxy waarmee een subset van de SMART on FHIR-startreeksen wordt ingeschakeld. De proxy schakelt specifiek de [EHR-startreeks](https://hl7.org/fhir/smart-app-launch/#ehr-launch-sequence) in.

In deze zelfstudie wordt beschreven hoe u de proxy gebruikt om SMART on FHIR-toepassingen in te schakelen met Azure API for FHIR.

## <a name="prerequisites"></a>Vereisten

- Een exemplaar van Azure API for FHIR
- [.NET Core 2.2](https://dotnet.microsoft.com/download/dotnet-core/2.2)

## <a name="configure-azure-ad-registrations"></a>Azure AD-registraties configureren

Voor SMART on FHIR moet `Audience` een id-URI hebben die gelijk is aan de URI van de FHIR-service. De standaardconfiguratie van Azure API for FHIR maakt gebruik van de `Audience`-waarde `https://azurehealthcareapis.com`. U kunt echter ook een waarde instellen die overeenkomt met de specifieke URL van uw FHIR-service (bijvoorbeeld `https://MYFHIRAPI.azurehealthcareapis.com`). Dit is vereist bij het werken met de SMART on FHIR-proxy.

U hebt ook een registratie van een clienttoepassing nodig. De meeste SMART on FHIR-toepassingen zijn JavaScript-toepassingen met één pagina. Volg daarom de instructies voor het configureren van een [openbare Azure AD-clienttoepassing](register-public-azure-ad-client-app.md).

Nadat u deze stappen hebt voltooid, hebt u het volgende:

- Een FHIR-server waarop de rge-doelgroep is ingesteld op `https://MYFHIRAPI.azurehealthcareapis.com`, waarbij `MYFHIRAPI` de naam is van uw Azure API for FHIR-exemplaar.
- Een registratie van een openbare clienttoepassing. Noteer de toepassings-id van deze clienttoepassing.

## <a name="enable-the-smart-on-fhir-proxy"></a>De SMART on FHIR-proxy inschakelen

Schakel de SMART on FHIR-proxy in de **Verificatie**-instellingen voor uw Azure API for HIR-exemplaar in door het selectievakje **SMART on FHIR proxy** in te schakelen:

![Selecties voor het inschakelen van de SMART on FHIR-proxy](media/tutorial-smart-on-fhir/enable-smart-on-fhir-proxy.png)

## <a name="enable-cors"></a>CORS inschakelen

Omdat de meeste SMART on FHIR-toepassingen JavaScript-apps met één pagina zijn, moet u [cross-origin resource delen (CORS) inschakelen](configure-cross-origin-resource-sharing.md) voor Azure API for FHIR:

![Selecties voor het inschakelen van CORS](media/tutorial-smart-on-fhir/enable-cors.png)

## <a name="configure-the-reply-url"></a>De antwoord-URL configureren

De SMART on FHIR-proxy fungeert als een schakel tussen de SMART on FHIR-app en Azure AD. Het verificatieantwoord (de verificatiecode) moet in plaats van de app zelf naar de SMART on FHIR-proxy worden gestuurd. De proxy stuurt het antwoord vervolgens door naar de app. 

Als gevolg van deze omleiding in twee stappen van de verificatiecode moet u de antwoord-URL (callback) voor uw Azure AD-clienttoepassing instellen op een URL die een combinatie is van de antwoord-URL voor de SMART on FHIR-proxy en de antwoord-URL voor de SMART on FHIR-app. De gecombineerde antwoord-URL heeft de volgende indeling:

```http
https://MYFHIRAPI.azurehealthcareapis.com/AadSmartOnFhirProxy/callback/aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA
```

In dat antwoord is `aHR0cHM6Ly9sb2NhbGhvc3Q6NTAwMS9zYW1wbGVhcHAvaW5kZXguaHRtbA` een met Base64 gecodeerde, URL-veilige versie van de antwoord-URL voor de SMART on FHIR-app. Als de app lokaal wordt uitgevoerd, is `https://localhost:5001/sampleapp/index.html` de antwoord-URL voor het startprogramma van de SMART on FHIR-app. 

U kunt de gecombineerde antwoord-URL genereren met behulp van een script dat er als volgt uitziet:

```PowerShell
$replyUrl = "https://localhost:5001/sampleapp/index.html"
$fhirServerUrl = "https://MYFHIRAPI.azurewebsites.net"
$bytes = [System.Text.Encoding]::UTF8.GetBytes($ReplyUrl)
$encodedText = [Convert]::ToBase64String($bytes)
$encodedText = $encodedText.TrimEnd('=');
$encodedText = $encodedText.Replace('/','_');
$encodedText = $encodedText.Replace('+','-');

$newReplyUrl = $FhirServerUrl.TrimEnd('/') + "/AadSmartOnFhirProxy/callback/" + $encodedText
```

Voeg de antwoord-URL toe aan de openbare clienttoepassing die u eerder hebt gemaakt voor Azure AD:

![De antwoord-URL die is geconfigureerd voor de openbare client](media/tutorial-smart-on-fhir/configure-reply-url.png)

## <a name="get-a-test-patient"></a>Een testpatiënt ontvangen

Als u Azure API for FHIR en de SMART on FHIR-proxy wilt testen, moet u ten minste één patiënt in de database hebben. Als u nog niet met de API hebt gecommuniceerd en u geen gegevens in de database hebt, volgt u de [zelfstudie FHIR API Postman](access-fhir-postman-tutorial.md) om een patiënt te laden. Noteer de id van een specifieke patiënt.

## <a name="download-the-smart-on-fhir-app-launcher"></a>Download het startprogramma voor de SMART on FHIR-app

De open-source [FHIR Server for Azure-opslagplaats](https://github.com/Microsoft/fhir-server) bevat een eenvoudig startprogramma voor de SMART on FHIR-app en een SMART on FHIR-voorbeeldapp. In deze zelfstudie gebruikt u dit SMART on FHIR-startprogramma lokaal om de installatie te testen.

U kunt de GitHub-opslagplaats klonen en naar de toepassing gaan met behulp van de volgende opdrachten:

```PowerShell
git clone https://github.com/Microsoft/fhir-server
cd fhir-server/samples/apps/SmartLauncher
```

De toepassing heeft een aantal configuratie-instellingen nodig die u in `appsettings.json` kunt instellen:

```json
{
    "FhirServerUrl": "https://MYFHIRAPI.azurehealthcareapis.com",
    "ClientId": "APP-ID",
    "DefaultSmartAppUrl": "/sampleapp/launch.html"
}
```

U wordt aangeraden de functie `dotnet user-secrets` te gebruiken:

```PowerShell
dotnet user-secrets set FhirServerUrl https://MYFHIRAPI.azurehealthcareapis.com
dotnet user-secrets set ClientId <APP-ID>
```

Gebruik deze opdracht om de toepassing uit te voeren:

```PowerShell
dotnet run
```

## <a name="test-the-smart-on-fhir-proxy"></a>De SMART on FHIR-proxy testen

Nadat u het startprogramma voor de SMART on FHIR-app hebt gestart, kunt u in uw browser naar `https://localhost:5001` gaan, waar het volgende scherm wordt weergegeven:

![Startprogramma voor de SMART on FHIR-app](media/tutorial-smart-on-fhir/smart-on-fhir-app-launcher.png)

Wanneer u informatie over een **Patiënt**, **Confrontatie** of **Arts** invoert, ziet u dat de **Startcontext** is bijgewerkt. Wanneer u Azure API for FHIR gebruikt, is de startcontext gewoon een JSON-document met informatie over patiënten, artsen en meer. Deze startcontext wordt base64-gecodeerd en doorgegeven aan de SMART on FHIR-app als de queryparameter `launch`. Volgens de specificaties voor SMART on FHIR, is de variabele ondoorzichtig voor de SMART on FHIR-app en wordt deze doorgegeven aan de id-provider. 

De SMART on FHIR-proxy gebruikt deze informatie voor het invullen van velden in het tokenantwoord. De SMART on FHIR app *kan* deze velden gebruiken om te bepalen voor welke patiënten deze gegevens opvraagt en hoe de toepassing op het scherm wordt weergegeven. De SMART on FHIR-proxy ondersteunt de volgende velden:

* `patient`
* `encounter`
* `practitioner`
* `need_patient_banner`
* `smart_style_url`

Deze velden zijn bedoeld om richting te geven aan de app, maar ze bevatten geen beveiligingsgegevens. Een SMART on FHIR-toepassing kan ze negeren.

U ziet dat het startprogramma voor de SMART on FHIR-app de gegevens voor de **Start-URL** onderaan de pagina bijwerkt. Selecteer **Starten** om de voorbeeld-app te starten. U zou iets vergelijkbaars met het volgende voorbeeld moeten zien:

![SMART on FHIR-app](media/tutorial-smart-on-fhir/smart-on-fhir-app.png)

Inspecteer de tokenrespons om te zien hoe de startcontextvelden worden doorgegeven aan de app.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de SMART on FHIR-proxy van Azure Active Directory geconfigureerd. Ga naar de opslagplaats van FHIR-servervoorbeelden op GitHub om het gebruik van SMART on FHIR-toepassingen met Azure API for FHIR en de open-source FHIR-server voor Azure te verkennen:

>[!div class="nextstepaction"]
>[FHIR-servervoorbeelden](https://github.com/Microsoft/fhir-server-samples)
