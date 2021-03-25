---
title: Client bibliotheken en REST Api's voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de Sdk's en REST-Api's van Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: effd7658bbfe7359e1f99f9452857824c2c45c2f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107887"
---
# <a name="client-libraries-and-rest-apis"></a>Clientbibliotheken en REST API's

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Mogelijkheden van Azure Communication Services zijn conceptueel onderverdeeld in zes gebieden. Sommige gebieden hebben volledige open source-Sdk's. De aanroepende SDK maakt gebruik van eigen netwerk interfaces en is momenteel gesloten-bron en de chat bibliotheek bevat een afhankelijkheid van gesloten bronnen. Voor beelden en aanvullende technische gegevens voor Sdk's worden gepubliceerd in de [Azure Communication Services github opslag plaats](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Clientbibliotheken

| Assembly               | Protocollen             |Open versus gesloten bron| Naamruimten                          | Functies                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Openen            | Azure. Resource Manager. communicatie | Communicatie services-resources inrichten en beheren             |
| Algemeen                 | REST | Openen               | Azure. Communication. common          | Biedt basis typen voor andere Sdk's |
| Identiteit         | REST | Openen               | Azure. Communication. Identity  | Gebruikers beheren, toegangs tokens |
| Telefoonnummers         | REST | Openen               | Azure. Communication. PhoneNumbers  | Telefoon nummers beheren |
| Chat                   | REST met een eigen signaal | Openen met een pakket met gesloten bron signalen    | Azure. Communication. chat            | In realtime tekst gebaseerde chat berichten toevoegen aan uw toepassingen  |
| Sms                    | REST | Openen              | Azure. Communication. SMS             | Sms-berichten verzenden en ontvangen |
| Aanroepen                | Eigen Trans Port | Gesloten |Azure. Communication. Calling         | Gebruik spraak, video, scherm delen en andere realtime gegevens communicatie mogelijkheden          |

Houd er rekening mee dat de Azure Resource Manager-, id-en SMS-Sdk's gericht zijn op Service-integratie, en in veel gevallen kunnen er beveiligings problemen optreden als u deze functies integreert in toepassingen van eind gebruikers. De common-en chat-Sdk's zijn geschikt voor service-en client toepassingen. De aanroepende SDK is ontworpen voor client toepassingen. Een SDK die gericht is op service scenario's is in ontwikkeling.

### <a name="languages-and-publishing-locations"></a>Talen en publicatie locaties

Hieronder vindt u een beschrijving van de publicatie locaties voor afzonderlijke SDK-pakketten.

| Gebied           | Javascript | .NET | Python | Java SE | iOS | Android | Anders                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Go via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Algemeen         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N.v.t.      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identiteit | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Telefoon nummers | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| Sms            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Aanroepen        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Referentiedocumentatie     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](/objectivec/communication-services/calling/)      | [docs](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>REST-API’s

Api's voor communicatie services worden naast andere Azure REST Api's in [docs.Microsoft.com](/rest/api/azure/)gedocumenteerd. In deze documentatie wordt uitgelegd hoe u uw HTTP-berichten kunt structureren en hoe u postman kunt gebruiken. Deze documentatie wordt ook aangeboden in Swagger-indeling op [github](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Aanvullende ondersteunings Details

### <a name="ios-and-android-support-details"></a>Details van iOS-en Android-ondersteuning

- Communicatie Services iOS Sdk's doel-iOS-versie 13 +, en Xcode 11 +.
- Android Java Sdk's target Android-API Level 21 + en Android Studio 4.0 +

### <a name="net-support-details"></a>Details voor .NET-ondersteuning

Met uitzonde ring van het aanroepen van de communicatie Services-pakketten van .NET Standard 2,0, die ondersteuning biedt voor de platforms die hieronder worden weer gegeven.

Ondersteuning via .NET Framework 4.6.1
- Windows 10, 8,1, 8 en 7
- Windows Server 2012 R2, 2012 en 2008 R2 SP1

Ondersteuning via .NET Core 2,0:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1 +
- Max. OS X 10.12 +
- Meerdere Linux-versies/-distributies
- UWP 10.0.16299 (RS3) september 2017
- Unity 2018,1
- Mono 5,4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="calling-sdk-timeouts"></a>SDK-time-outs aanroepen

De volgende time-outs zijn van toepassing op de communicatie services die Sdk's aanroepen:

| Bewerking           | Time-out in seconden |
| -------------- | ---------- |
| Deel nemer opnieuw verbinden/verwijderen | 120 |
| Een nieuwe modale functie toevoegen aan of verwijderen uit een oproep (video starten/stoppen of scherm delen) | 40 |
| Time-out voor overdracht van oproep | 60 |
| time-out voor aanleg van 1:1-aanroepen | 85 |
| Time-out voor groeperen van groeps aanroepen | 85 |
| Time-out voor PSTN-aanroepen | 115 |
| 1:1-aanroep promo veren naar een time-out voor groeps aanroepen | 115 |


## <a name="api-stability-expectations"></a>API-stabiliteits verwachtingen

> [!IMPORTANT]
> Deze sectie bevat richt lijnen voor REST Api's en Sdk's die **stabiel** zijn gemarkeerd. Api's die zijn gemarkeerd vóór release, preview of bèta, kunnen **zonder kennisgeving** worden gewijzigd of afgeschaft.

In de toekomst kunnen we de versies van de Sdk's van Communication Services buiten gebruik stellen en kunnen we wijzigingen aanbrengen in onze REST Api's en vrijgegeven Sdk's. Azure Communication Services volgt *doorgaans* twee ondersteunings beleid voor het buiten gebruik stellen van service versies:

- U wordt ten minste drie jaar hiervan op de hoogte gebracht alvorens code te wijzigen als gevolg van een wijziging in de communicatie Services-Interface. Alle gedocumenteerde REST Api's en SDK-Api's profiteren doorgaans van een waarschuwing van drie jaar voordat de interfaces uit bedrijf worden genomen.
- U wordt ten minste één jaar gewaarschuwd voordat SDK-assembly's worden bijgewerkt naar de meest recente secundaire versie. Voor deze vereiste updates moeten geen code wijzigingen worden aangebracht omdat ze zich in dezelfde primaire versie bevinden. Dit geldt met name voor de aanroepen-en chat-bibliotheken die realtime-onderdelen bevatten waarvoor regel matig beveiligings-en prestatie-updates nodig zijn. Wij raden u ten zeerste aan om uw Sdk's voor Communication Services te laten bijwerken.

### <a name="api-and-sdk-decommissioning-examples"></a>Voor beelden van API en SDK uit bedrijf nemen

**U hebt de V24-versie van de SMS-REST API in uw toepassing geïntegreerd. Azure Communication releases V25.**

U ontvangt een waarschuwing van drie jaar voordat deze Api's werken en worden gedwongen om bij te werken naar V25. Voor deze update is mogelijk een code wijziging vereist.

**U hebt de v 2.02-versie van de aanroepende SDK geïntegreerd in uw toepassing. Azure Communication releases v 2.05.**

Mogelijk moet u worden bijgewerkt naar de v 2.05-versie van de aanroepende SDK binnen een periode van 12 maanden na de release van v 2.05. Dit moet een eenvoudige vervanging zijn van het artefact zonder dat er een code moet worden gewijzigd, omdat v 2.05 zich in de hoofd versie van v2 bevindt en geen wijzigingen heeft.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie raadpleegt u de volgende SDK-overzichten:

- [Overzicht van de SDK](../concepts/voice-video-calling/calling-sdk-features.md)
- [Overzicht van chat-SDK](../concepts/chat/sdk-features.md)
- [SMS-SDK-overzicht](../concepts/telephony-sms/sdk-features.md)

Om aan de slag te gaan met Azure Communication Services:

- [Azure-communicatie resources maken](../quickstarts/create-communication-resource.md)
- [Tokens voor gebruikers toegang](../quickstarts/access-tokens.md) genereren
