---
title: Bewaken met webtests met meerdere stappen-Azure-toepassing inzichten
description: Webtests met meerdere stappen instellen om uw webtoepassingen te bewaken met Azure-toepassing Insights
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 1d3597eaf54c40fb1f986d822af0dd6b8c8a7b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719845"
---
# <a name="multi-step-web-tests"></a>Webtests met meerdere stappen

U kunt een vastgelegde reeks Url's en interacties bewaken met een website via webtests met meerdere stappen. Dit artikel begeleidt u stapsgewijs door het proces van het maken van een webtest met meerdere stappen met Visual Studio Enter prise.

> [!NOTE]
> Webtests met meerdere stappen zijn afhankelijk van bestanden van Visual Studio Web testen. Het werd [aangekondigd](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) dat Visual Studio 2019 de laatste versie is met de webtest-functionaliteit. Het is belang rijk om te begrijpen dat er geen nieuwe functies worden toegevoegd, maar dat de webtest-functionaliteit in Visual Studio 2019 nog steeds wordt ondersteund en wordt ondersteund tijdens de levens cyclus van het product. Het Azure Monitor-product team heeft [hier](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)vragen gesteld over de toekomst van Beschik baarheid van meerdere stappen.  
> </br>
> Webtests met meerdere stappen **worden niet ondersteund** in de [Azure Government](../../azure-government/index.yml) Cloud.


## <a name="pre-requisites"></a>Vereisten

* Visual Studio 2017 Enter prise of hoger.
* Visual Studio Web Performance en load test tools.

Om de vereiste voor de test hulpprogramma's te vinden. Start de afzonderlijke onderdelen van **Visual Studio Installer**  >    >  **fouten opsporen en testen** op  >  **webprestaties en laad Programma's**.

![Scherm afbeelding van de gebruikers interface van Visual Studio Installer waarbij afzonderlijke onderdelen zijn geselecteerd met een selectie vakje naast het item voor de hulpprogram ma's voor webprestaties en laad Programma's](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Voor webtests met meerdere stappen gelden aanvullende kosten. Raadpleeg de [officiële prijs handleiding](https://azure.microsoft.com/pricing/details/application-insights/)voor meer informatie.

## <a name="record-a-multi-step-web-test"></a>Een webtest met meerdere stappen opnemen 

> [!WARNING]
> Het gebruik van de meervoudige-stap recorder wordt niet meer aanbevolen. De recorder is ontwikkeld voor statische HTML-pagina's met eenvoudige interacties en biedt geen functionele ervaring voor moderne webpagina's.

Raadpleeg de [officiële documentatie voor Visual studio 2019](/visualstudio/test/how-to-create-a-web-service-test)voor hulp bij het maken van Visual Studio-webtests.

## <a name="upload-the-web-test"></a>De webtest uploaden

1. Selecteer in de Portal Application Insights in het deel venster Beschik baarheid de optie test test  >  **type**  >  **multi-step web test** maken.

2. Stel de test locaties, frequentie en waarschuwings parameters in.

### <a name="frequency--location"></a>Frequentie & locatie

|Instelling| Uitleg
|----|----|----|
|**Test frequentie**| Hiermee stelt u in hoe vaak de test wordt uitgevoerd vanaf elke test locatie. Met een standaardfrequentie van vijf minuten en vijf testlocaties wordt uw site gemiddeld per minuut getest.|
|**Testlocaties**| Zijn de locaties waar onze servers webaanvragen verzenden naar uw URL. Het **minimum aantal aanbevolen test locaties is vijf** om ervoor te zorgen dat u problemen in uw website kunt onderscheiden van netwerk problemen. U kunt maximaal 16 locaties selecteren.

### <a name="success-criteria"></a>Criteria voor geslaagde pogingen

|Instelling| Uitleg
|----|----|----|
| **Time-out testen** |Deze waarde verlagen om te worden gewaarschuwd over trage reacties. De test wordt als mislukt beschouwd als er binnen deze periode geen reactie van uw site is ontvangen. Als u **Parse onafhankelijke aanvragen** hebt geselecteerd, moeten alle afbeeldingen, stijlbestanden, scripts en andere afhankelijke resources binnen deze periode worden ontvangen.|
| **HTTP-antwoord** | De geretourneerde status code die als geslaagd is geteld. 200 is de code die aangeeft dat er een normale webpagina is geretourneerd.|
| **Inhouds overeenkomst** | Een teken reeks, bijvoorbeeld ' Welkom! ' Er wordt getest of er in elke respons een exacte (hoofdlettergevoelige) overeenkomst wordt gevonden. Het moet een eenvoudige tekenreeks zijn, zonder jokertekens. Als uw pagina-inhoud wordt gewijzigd, moet u deze tekenreeks mogelijk ook bijwerken. **Alleen Engelse tekens worden ondersteund met inhouds overeenkomsten** |

### <a name="alerts"></a>Waarschuwingen

|Instelling| Uitleg
|----|----|----|
|**Bijna realtime (preview-versie)** | We raden u aan bijna realtime waarschuwingen te gebruiken. Het configureren van dit type waarschuwing wordt uitgevoerd nadat de beschikbaarheids test is gemaakt.  |
|**Drempel waarde voor waarschuwings locatie**|We raden aan dat er mini maal 3/5 locaties zijn. De optimale relatie tussen de drempel waarde van de waarschuwings locatie en het aantal test locaties is drempel waarde voor **waarschuwings locaties**  =  **aantal test locaties-2, met een minimum van vijf test locaties.**|

## <a name="configuration"></a>Configuratie

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Tijd en wille keurige getallen koppelen aan uw test

Stel dat u een hulpprogramma test dat tijdsafhankelijke gegevens ontvangt van een externe feed (bijvoorbeeld een feed met aandelenkoersen). Wanneer u uw webtest opneemt, moet u specifieke tijden gebruiken, maar u stelt deze in als testparameters: StartTime en EndTime.

![Mijn scherm afbeelding van de fantastische Stock-app](./media/availability-multistep/app-insights-72webtest-parameters.png)

Wanneer u de test uitvoert, moet EndTime altijd de huidige tijd zijn. StartTime moet een kwartier in het verleden liggen.

De invoeg toepassing datum tijd web test biedt de mogelijkheid om para meters-tijden te verwerken.

1. Voeg een webtestinvoegtoepassing toe voor elke gewenste variabele parameterwaarde. Kies in de werkbalk van de webtest de optie **Webtestinvoegtoepassing toevoegen**.
    
    ![Invoeg toepassing voor web-test toevoegen](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    In dit voorbeeld gebruiken we twee exemplaren van de invoegtoepassing Date Time. Een exemplaar is voor "15 minuten geleden" en een ander voor “nu”.

2. Open de eigenschappen van elke invoegtoepassing. Geef de invoegtoepassing een naam en stel deze zodanig in dat de huidige tijd wordt gebruikt. Stel voor een van de toepassingen Minuten toevoegen in op -15.

    ![Context parameters](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Gebruik in de webtestparameters {{plug-in name}} om te verwijzen naar de naam van de invoegtoepassing.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Upload uw test nu naar de portal. Telkens wanneer de test wordt uitgevoerd, worden er dynamische waarden gebruikt.

### <a name="dealing-with-sign-in"></a>Omgaan met aanmelden

Als uw gebruikers zich aanmelden bij uw app, hebt u verschillende functies om de aanmelding te simuleren, zodat u pagina’s na het aanmelden kunt testen. Welke aanpak u gebruikt, hangt af van het type beveiliging van de app.

In alle gevallne moet u een account maken in uw toepassing voor testdoeleinden. Beperk indien mogelijk de machtigingen voor dit testaccount, zodat webtests echte gebruikers niet beïnvloeden.

**Eenvoudige gebruikers naam en wacht woord** Registreer een webtest op de gebruikelijke manier. Verwijder eerst de cookies.

**SAML-verificatie**

|Naam van eigenschap| Description|
|----|-----|
| Doel groep-URI | De doel groep-URI voor het SAML-token.  Dit is de URI voor de Access Control Service (ACS), met inbegrip van de ACS-naam ruimte en de hostnaam. |
| Certificaat wachtwoord | Het wacht woord voor het client certificaat waarmee toegang wordt verleend aan de Inge sloten persoonlijke sleutel. |
| Client certificaat  | De waarde van het client certificaat met de persoonlijke sleutel in base64-gecodeerde indeling. |
| Naam-id | De naam-id voor het token |
| Niet na | De time span waarvoor het token geldig is.  De standaard waarde is 5 minuten. |
| Niet voor | De time span waarvoor een token in het verleden geldig is (om tijd scheef te maken).  De standaard waarde is (negatief) 5 minuten. |
| Naam van de doel context parameter | De context parameter die de gegenereerde bevestiging ontvangt. |


**Client geheim** Als uw app een aanmeldings route heeft die betrekking heeft op een client geheim, gebruikt u deze route. Azure Active Directory (AAD) is een voorbeeld van een service die aanmelden met een clientgeheim bevat. In AAD is het klantgeheim de App Key.

Hier is een voorbeeldwebtest van een Azure web-app met een App Key:

![Voorbeeld scherm afbeelding](./media/availability-multistep/client-secret.png)

Token van AAD krijgen met klantgeheim (AppKey).
Bearer token uit antwoord halen.
API oproepen met bearer token in de autorisatie-header.
Zorg ervoor dat de webtest een daad werkelijke client is, dat wil zeggen een eigen app in AAD, en gebruik de client sleutel voor clientId + app. Uw service onder test heeft ook een eigen app in AAD: de appID-URI van deze app wordt weer gegeven in de webtest in het veld Resource.

### <a name="open-authentication"></a>Open verificatie
Een voorbeeld van open verificatie is het aanmelden met uw Microsoft- of Google-account. Veel apps die OAuth gebruiken, bieden een alternatief met clientgeheim, zodat uw eerste tactiek moet zijn deze mogelijkheid te onderzoeken.

Als uw test moet aanmelden met OAuth, is de algemene benadering:

Gebruik een hulpprogramma zoals Fiddler om het verkeer tussen de webbrowser, de verificatiesite en uw app te onderzoeken.
Voer twee of meer aanmeldingen uit via verschillende apparaten en browsers, of met lange periodes ertussen (zodat de tokens verlopen).
Vergelijk de verschillende sessies om te bepalen welk token is doorgegeven door de verificatiesite en daarna, na het aanmelden, wordt doorgegeven aan uw appserver.
Neem een webtest op met Visual Studio.
Maak parameters van de tokens. Stel de parameter in wanneer er een token wordt geretourneerd van de verificator en gebruik deze in de query voor de site. (Visual Studio probeert de testparameters toe te voegen, maar voegt de parameters voor de tokens niet correct toe.)

## <a name="troubleshooting"></a>Problemen oplossen

Speciaal [artikel voor probleem oplossing](troubleshoot-availability.md).

## <a name="next-steps"></a>Volgende stappen

* [Beschikbaarheids waarschuwingen](availability-alerts.md)
* [Webtests voor URL-ping](monitor-web-app-availability.md)
