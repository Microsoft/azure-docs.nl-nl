---
title: Client bibliotheken en REST Api's voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over Azure Communication Services-client bibliotheken en REST-Api's.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f59ebfdb1942c7eb9be6c2cf09e1ed677c3f22e3
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507673"
---
# <a name="client-libraries-and-rest-apis"></a>Clientbibliotheken en REST API's

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Mogelijkheden van Azure Communication Services zijn conceptueel onderverdeeld in zes gebieden. Sommige gebieden hebben volledig open-source-client bibliotheken. De aanroepende client bibliotheek maakt gebruik van eigen netwerk interfaces en is momenteel gesloten-bron en de chat bibliotheek bevat een afhankelijkheid van gesloten bronnen. Voor beelden en aanvullende technische Details voor client bibliotheken worden gepubliceerd in de [Azure Communication Services github opslag plaats](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Clientbibliotheken

| Assembly               | Protocollen             |Open versus gesloten bron| Naamruimten                          | Functies                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Openen            | Azure. Resource Manager. communicatie | Communicatie services-resources inrichten en beheren             |
| Algemeen                 | REST | Openen               | Azure. Communication. common          | Biedt basis typen voor andere client bibliotheken |
| Beheer         | REST | Openen               | Azure. Communication. Administration  | Beheer van gebruikers, toegangs tokens en telefoon nummers, wijs standaarden toe die compatibel zijn met STUN en zet servers |
| Chat                   | REST met een eigen signaal | Openen met een pakket met gesloten bron signalen    | Azure. Communication. chat            | In realtime tekst gebaseerde chat berichten toevoegen aan uw toepassingen  |
| Sms                    | REST | Openen              | Azure. Communication. SMS             | Sms-berichten verzenden en ontvangen |
| Aanroepen                | Eigen Trans Port | Gesloten |Azure. Communication. Calling         | Gebruik spraak, video, scherm delen en andere realtime gegevens communicatie mogelijkheden          |

Houd er rekening mee dat de Azure Resource Manager, beheer en SMS-client bibliotheken gericht zijn op Service-integratie en dat er in veel gevallen beveiligings problemen optreden als u deze functies integreert in toepassingen van eind gebruikers. De common-en chat-client bibliotheken zijn geschikt voor service-en client toepassingen. De aanroepende client bibliotheek is ontworpen voor client toepassingen. Een client bibliotheek die is gericht op service scenario's is in ontwikkeling.

### <a name="languages-and-publishing-locations"></a>Talen en publicatie locaties

Publicatie locaties voor afzonderlijke client bibliotheek pakketten worden hieronder beschreven. 

| Gebied           | Javascript | .NET | Python | Java SE | iOS | Android | Anders                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Go via GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Algemeen         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | N.v.t.      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases/tag/1.0.0-beta.1)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Beheer | [npm](https://www.npmjs.com/package/@azure/communication-administration)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Administration)    | [PyPi](https://pypi.org/project/azure-communication-administration/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-administration)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| Sms            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Aanroepen        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases/tag/v1.0.0-beta.2)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Referentiedocumentatie     | [docs](https://azure.github.io/azure-sdk-for-js/communication.html)         | [docs](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [docs](http://azure.github.io/azure-sdk-for-java/communication.html)     | [docs](https://docs.microsoft.com/objectivec/communication-services/calling/)      | [docs](https://docs.microsoft.com/java/api/com.azure.communication.calling?view=communication-services-java-android)            | -                              |
## <a name="rest-apis"></a>REST-API’s

Api's voor communicatie services worden naast andere Azure REST Api's in [docs.Microsoft.com](/rest/api/azure/)gedocumenteerd. In deze documentatie wordt uitgelegd hoe u uw HTTP-berichten kunt structureren en hoe u postman kunt gebruiken. Deze documentatie wordt ook aangeboden in Swagger-indeling op [github](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Aanvullende ondersteunings Details

### <a name="ios-and-android-support-details"></a>Details van iOS-en Android-ondersteuning

- Communicatie Services iOS-client bibliotheken doel-iOS-versie 13 +, en Xcode 11 +.
- Android Java-client bibliotheken doel Android-API-niveau 21 + en Android Studio 4.0 +

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

## <a name="api-stability-expectations"></a>API-stabiliteits verwachtingen 

> [!IMPORTANT]
> Deze sectie bevat richt lijnen voor REST-Api's en client bibliotheken die **stabiel** zijn gemarkeerd. Api's die zijn gemarkeerd vóór release, preview of bèta, kunnen **zonder kennisgeving** worden gewijzigd of afgeschaft. Momenteel bevindt Azure Communication Services zich in een **open bare preview-versie** en api's zijn als zodanig gemarkeerd.

In de toekomst kunnen we de versies van de client bibliotheken voor communicatie services buiten gebruik stellen en kunnen we wijzigingen aanbrengen in onze REST Api's en vrijgegeven client bibliotheken. Azure Communication Services volgt *doorgaans* twee ondersteunings beleid voor het buiten gebruik stellen van service versies:

- U wordt ten minste drie jaar hiervan op de hoogte gebracht alvorens code te wijzigen als gevolg van een wijziging in de communicatie Services-Interface. Alle gedocumenteerde REST-Api's en client bibliotheek-Api's hebben over het algemeen een waarschuwing van ten minste drie jaar voordat de interfaces buiten gebruik worden gesteld.
- U wordt ten minste één jaar hiervan op de hoogte gebracht voordat u de assembly's van de client bibliotheek bijwerkt naar de meest recente secundaire versie. Voor deze vereiste updates moeten geen code wijzigingen worden aangebracht omdat ze zich in dezelfde primaire versie bevinden. Dit geldt met name voor de aanroepen-en chat-bibliotheken die realtime-onderdelen bevatten waarvoor regel matig beveiligings-en prestatie-updates nodig zijn. We raden u ten zeerste aan om uw client bibliotheken voor communicatie services te laten bijwerken.

### <a name="api-and-client-library-decommissioning-examples"></a>Voor beelden van het uit bedrijf nemen van de API en de client bibliotheek

**U hebt de V24-versie van de SMS-REST API in uw toepassing geïntegreerd. Azure Communication releases V25.**

U ontvangt een waarschuwing van drie jaar voordat deze Api's werken en worden gedwongen om bij te werken naar V25. Voor deze update is mogelijk een code wijziging vereist.

**U hebt de v 2.02-versie van de aanroepende client bibliotheek in uw toepassing geïntegreerd. Azure Communication releases v 2.05.**

Mogelijk moet u worden bijgewerkt naar de v 2.05-versie van de aanroepende client bibliotheek binnen een periode van 12 maanden na de release van v 2.05. Dit moet een eenvoudige vervanging zijn van het artefact zonder dat er een code moet worden gewijzigd, omdat v 2.05 zich in de hoofd versie van v2 bevindt en geen wijzigingen heeft.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie de volgende overzichten van de client bibliotheek:

- [Overzicht van client bibliotheek aanroepen](../concepts/voice-video-calling/calling-sdk-features.md)
- [Overzicht van de chatten-client bibliotheek](../concepts/chat/sdk-features.md)
- [Overzicht SMS-client bibliotheek](../concepts/telephony-sms/sdk-features.md)

Om aan de slag te gaan met Azure Communication Services:

- [Azure-communicatie resources maken](../quickstarts/create-communication-resource.md)
- [Tokens voor gebruikers toegang](../quickstarts/access-tokens.md) genereren
