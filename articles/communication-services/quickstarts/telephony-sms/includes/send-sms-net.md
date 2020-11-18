---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: a084295aec2cafadd07d47e85a0116a89d37c985
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816729"
---
Ga aan de slag met Azure Communication Services door de clientbibliotheek voor C#-sms van Communications Services te gebruiken om sms-berichten te verzenden.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- De nieuwste versie van [.NET Core-clientbibliotheek](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturings systeem.
- Een actieve Communication Services-resource en verbindingsreeks. [Een communicatieresource maken](../../create-communication-resource.md).
- Een telefoonnummer met sms-functionaliteit. [Een telefoonnummer aanvragen](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controle van vereisten

- Voer in een terminal- of opdrachtvenster de opdracht `dotnet` uit om te controleren of de .NET-clientbibliotheek is geïnstalleerd.
- Als u de telefoonnummers wilt weergeven die zijn gekoppeld aan uw Communication Services-resource, meldt u zich aan bij [Azure Portal](https://portal.azure.com/), zoekt u uw Communication Services-resource en opent u het tabblad **telefoonnummers** vanuit het navigatiedeelvenster aan de linkerkant.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `SmsQuickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Wijzig uw directory in de zojuist gemaakte app-map en gebruik de opdracht `dotnet build` om uw toepassing te compileren.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de toepassingsmap en installeer met de opdracht `dotnet add package` de Azure Communications Services sms-clientbibliotheek voor het .NET-pakket.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.3
```

Voeg een `using`-instructie toe aan het begin van **Program.cs** om de naamruimte `Azure.Communication` op te nemen.

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services sms-clientbibliotheek voor C#.

| Naam                                       | Beschrijving                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Deze klasse is vereist voor alle sms-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om sms-berichten te verzenden.                           |
| SendSmsOptions | Deze klasse biedt opties voor het configureren van leveringsrapporten. Als enable_delivery_report is ingesteld op True, wordt een gebeurtenis verzonden wanneer de levering is geslaagd |

## <a name="authenticate-the-client"></a>De client verifiëren

 Open **Program.cs** in een teksteditor en vervang de hoofdtekst van de `Main`-methode door code om een `SmsClient` te initialiseren met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Een sms-bericht verzenden

Een sms-bericht verzenden door de verzendmethode aan te roepen. Voeg deze code toe aan het einde van de `Main`-methode in **Program.cs**:

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

U moet `<leased-phone-number>` vervangen door een telefoonnummer met sms-functionaliteit dat is gekoppeld aan uw Communication Services-resources en `<to-phone-number>` met het telefoonnummer waarnaar u een bericht wilt verzenden.

De parameter `EnableDeliveryReport` is een optionele parameter die u kunt gebruiken voor het configureren van leveringsrapporten. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Voorbeeldcode

U kunt de voorbeeld-app downloaden uit [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS).
