---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: caca5f5a05a136248f7453337629fdd2b22f956a
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110336"
---
Ga aan de slag met Azure Communication Services met de SMS SDK van Communication Services C# om SMS-berichten te verzenden.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- De nieuwste versie [.net core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturings systeem.
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een telefoonnummer met sms-functionaliteit. [Een telefoonnummer aanvragen](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controle van vereisten

- Voer in een Terminal-of opdracht venster de `dotnet` opdracht uit om te controleren of de .NET SDK is geïnstalleerd.
- Als u de telefoonnummers wilt weergeven die zijn gekoppeld aan uw Communication Services-resource, meldt u zich aan bij [Azure Portal](https://portal.azure.com/), zoekt u uw Communication Services-resource en opent u het tabblad **telefoonnummers** vanuit het navigatiedeelvenster aan de linkerkant.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `SmsQuickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: **Program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Wijzig uw map in de zojuist gemaakte app-map en gebruik de opdracht `dotnet build` om uw toepassing te compileren.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog steeds in de toepassingsmap, installeert u het Azure Communication Services SMS SDK voor .NET-pakket met behulp van de `dotnet add package` opdracht.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.4
```

Voeg een `using`-instructie toe aan het begin van **Program.cs** om de naamruimte `Azure.Communication` op te nemen.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de SMS SDK voor Azure Communication Services voor C#.

| Naam                                       | Beschrijving                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Deze klasse is vereist voor alle sms-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om sms-berichten te verzenden.                           |
| SmsSendResult               | Deze klasse bevat het resultaat van de SMS-service.                                          |
| SmsSendOptions | Deze klasse biedt opties voor het configureren van leveringsrapporten. Als enable_delivery_report is ingesteld op True, wordt een gebeurtenis verzonden wanneer de levering is geslaagd |

## <a name="authenticate-the-client"></a>De client verifiëren

 Open **Program.cs** in een teksteditor en vervang de hoofdtekst van de `Main`-methode door code om een `SmsClient` te initialiseren met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>Een SMS-bericht van 1:1 verzenden

Als u een SMS-bericht naar één ontvanger wilt verzenden, roept u de `Send` functie of aan `SendAsync` vanuit de SmsClient. Voeg deze code toe aan het einde van de `Main`-methode in **Program.cs**:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>", // Your E.164 formatted from phone number used to send SMS
    to: "<to-phone-number>", // E.164 formatted recipient phone number
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
U moet `<from-phone-number>` vervangen door een telefoonnummer met sms-functionaliteit dat is gekoppeld aan uw Communication Services-resources en `<to-phone-number>` met het telefoonnummer waarnaar u een bericht wilt verzenden.

## <a name="send-a-1n-sms-message-with-options"></a>Een SMS-bericht van 1: N verzenden met opties
Als u een SMS-bericht naar een lijst met ontvangers wilt verzenden, roept u de `Send` functie or aan `SendAsync` vanuit de SmsClient met een lijst met telefoon nummers van de ontvanger. U kunt ook aanvullende para meters door geven om op te geven of het leverings rapport moet worden ingeschakeld en aangepaste tags moet worden ingesteld.

```csharp
Response<IEnumerable<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>", // Your E.164 formatted from phone number used to send SMS
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" }, // E.164 formatted recipient phone numbers
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

De parameter `enableDeliveryReport` is een optionele parameter die u kunt gebruiken voor het configureren van leveringsrapporten. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```console
dotnet run
```

## <a name="sample-code"></a>Voorbeeldcode

U kunt de voorbeeld-app downloaden uit [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS).
