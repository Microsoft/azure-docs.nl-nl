---
title: 'Zelfstudie: werken met Azure Storage-wachtrijen in .NET'
description: Een zelfstudie over het gebruik van de Azure Queue-service voor het maken van wachtrijen en het invoegen, ophalen en verwijderen van berichten met behulp van .NET-code.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d661800c53cc0795efde1f411675d17661fb968
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345530"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>Zelfstudie: Werken met Azure Storage-wachtrijen in .NET

Azure Queue Storage implementeert cloudwachtrijen om communicatie tussen onderdelen van een gedistribueerde toepassing mogelijk te maken. Elke wachtrij houdt een lijst bij van berichten die kunnen worden toegevoegd door een afzenderonderdeel en kunnen worden verwerkt door een ontvangeronderdeel. Met een wachtrij kan uw toepassing onmiddellijk worden geschaald om aan de vraag te voldoen. In dit artikel worden de basisstappen beschreven voor het werken met een Azure Storage-wachtrij.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een Azure-opslagaccount maken
> - De app maken
> - De Azure-clientbibliotheken toevoegen
> - Ondersteuning voor asynchrone code toevoegen
> - Een wachtrij maken
> - Berichten in een wachtrij invoegen
> - Bericht uit een wachtrij verwijderen
> - Een lege wachtrij verwijderen
> - Controleren op opdrachtregelargumenten
> - De app bouwen en uitvoeren

## <a name="prerequisites"></a>Vereisten

- Download een gratis kopie van de [Visual Studio Code](https://code.visualstudio.com/download)-editor voor meerdere platforms.
- Download en installeer de [.NET Core SDK](https://dotnet.microsoft.com/download) versie 3.1 of hoger.
- Als u momenteel geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

Maak eerst een Azure-opslagaccount. Zie de quickstart [Een opslagaccount maken](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) voor een stapsgewijze handleiding voor het maken van een opslagaccount. Dit is een afzonderlijke stap die u uitvoert nadat u bij de vereisten een gratis Azure-account hebt gemaakt.

## <a name="create-the-app"></a>De app maken

Maak een .NET Core-toepassing met de naam **QueueApp**. Voor het gemak zal deze app zowel berichten verzenden als ontvangen via de wachtrij.

1. Gebruik in een consolevenster (zoals CMD, PowerShell of Azure CLI) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam **QueueApp**. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Ga naar de zojuist gemaakte map **QueueApp** en bouw de app om te controleren of alles klopt.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   U ziet als het goed is resultaten die vergelijkbaar zijn met de volgende uitvoer:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

<!-- markdownlint-disable MD023 -->

## <a name="add-the-azure-client-libraries"></a>De Azure-clientbibliotheken toevoegen

1. Voeg de Azure Storage-clientbibliotheken toe aan het project met behulp van de opdracht `dotnet add package`.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   Voer de volgende opdracht uit vanuit de projectmap in het consolevenster.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Voer de volgende opdrachten uit vanuit de projectmap in het consolevenster.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>Instructies toevoegen

1. Ga naar de opdrachtregel in de projectmap en typ `code .` om Visual Studio Code in de huidige map te openen. Houd het opdrachtregelvenster geopend. U gaat later meer opdrachten uitvoeren. Als u wordt gevraagd om C#-assets toe te voegen die vereist zijn voor het maken en het opsporen van fouten, klikt u op de knop **Ja**.

1. Open het bronbestand **Program.cs** en voeg de volgende naamruimten toe na de instructie `using System;`. Deze app gebruikt typen uit deze naamruimten om verbinding met Azure Storage te maken en met wachtrijen te werken.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Sla het bestand **Program.cs** op.

## <a name="add-support-for-asynchronous-code"></a>Ondersteuning voor asynchrone code toevoegen

Omdat de app gebruikmaakt van cloudresources, wordt de code asynchroon uitgevoerd.

1. Werk de methode **Algemeen** bij om asynchroon te worden uitgevoerd. Vervang **ongeldig** door een retourwaarde voor een **asynchrone taak**.

   ```csharp
   static async Task Main(string[] args)
   ```

1. Sla het bestand **Program.cs** op.

## <a name="create-a-queue"></a>Een wachtrij maken

Voordat u aanroepen naar Azure API's kunt uitvoeren, moet u uw referenties ophalen uit de Azure-portal.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>De verbindingsreeks aan de app toevoegen

Voeg de verbindingsreeks toe aan de app, zodat deze toegang krijgt tot het opslagaccount.

1. Ga terug naar Visual Studio Code.

1. Vervang in de methode **Main** de code `Console.WriteLine("Hello World!");` door de volgende regel waarmee de verbindingstekenreeks wordt opgehaald uit de omgevingsvariabele.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Voeg de volgende code toe aan **Algemeen** om een wachtrij-object te maken, dat later wordt doorgegeven aan de methoden voor verzenden en ontvangen.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Sla het bestand op.

## <a name="insert-messages-into-the-queue"></a>Berichten in de wachtrij invoegen

Maak een nieuwe methode om een bericht naar de wachtrij te verzenden.

1. Voeg de volgende methode **InsertMessageAsync** toe aan uw klasse **Programma**.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   Met deze methode wordt een wachtrijverwijzing doorgegeven. Er wordt een nieuwe wachtrij gemaakt als deze nog niet bestaat, door [CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync) aan te roepen. Vervolgens wordt de *newMessage* aan de wachtrij toegevoegd door [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) aan te roepen.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Met deze methode wordt een wachtrijverwijzing doorgegeven. Er wordt een nieuwe wachtrij gemaakt als deze nog niet bestaat, door [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) aan te roepen. Vervolgens wordt de *newMessage* aan de wachtrij toegevoegd door [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) aan te roepen.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **Optioneel** Standaard is de maximale time-to-live voor een bericht ingesteld op zeven dagen. U kunt elk willekeurig positief getal opgeven voor de time-to-live van het bericht. Met het volgende codefragment wordt een bericht toegevoegd dat *nooit* verloopt.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

    Als u een bericht wilt toevoegen dat niet verloopt, gebruikt u `Timespan.FromSeconds(-1)` in uw aanroep naar **SendMessageAsync**.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

    Als u een bericht wilt toevoegen dat niet verloopt, gebruikt u `Timespan.FromSeconds(-1)` in uw aanroep naar **AddMessageAsync**.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Sla het bestand op.

Een wachtrijbericht moet een indeling hebben die compatibel is met een XML-aanvraag met UTF-8-codering. Een bericht kan maximaal 64 KB groot zijn. Als een bericht binaire gegevens bevat, moet u het bericht [base64-coderen](/dotnet/api/system.convert.tobase64string).

## <a name="dequeue-messages"></a>Bericht uit een wachtrij verwijderen

Maak een nieuwe methode om een bericht uit de wachtrij op te halen. Zodra het bericht is ontvangen, is het belangrijk dat het uit de wachtrij wordt verwijderd zodat het niet meer dan één keer wordt verwerkt.

1. Voeg een nieuwe methode met de naam **RetrieveNextMessageAsync** toe aan uw klasse **Programma**.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   Met deze methode wordt een bericht uit de wachtrij ontvangen door het aanroepen van [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync), waarbij 1 in de eerste parameter wordt doorgegeven om alleen het volgende bericht in de wachtrij op te halen. Nadat het bericht is ontvangen, verwijdert u het uit de wachtrij door het aanroepen van [DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync).

   Wanneer een bericht wordt verzonden naar de wachtrij met een SDK-versie van vóór V12, wordt het automatisch met base64 gecodeerd. Vanaf V12 is deze functionaliteit verwijderd. Wanneer u een bericht ophaalt met behulp van SDK v12, wordt het niet automatisch met base64 gedecodeerd. U moet de inhoud zelf expliciet decoderen met [base64](/dotnet/api/system.convert.frombase64string).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Met deze methode wordt een bericht uit de wachtrij ontvangen door het aanroepen van [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Nadat het bericht is ontvangen, verwijdert u het uit de wachtrij door het aanroepen van [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Sla het bestand op.

## <a name="delete-an-empty-queue"></a>Een lege wachtrij verwijderen

Het is een best practice om aan het einde van een project te bepalen of u nog steeds de resources nodig hebt die u hebt gemaakt. Resources die actief blijven, kunnen u geld kosten. Als de wachtrij bestaat, maar leeg is, vraagt u de gebruiker of deze moet worden verwijderd.

1. Vouw de methode **RetrieveNextMessageAsync** uit om een prompt op te nemen voor het verwijderen van de lege wachtrij.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Sla het bestand op.

## <a name="check-for-command-line-arguments"></a>Controleren op opdrachtregelargumenten

Als er opdrachtregelargumenten worden doorgegeven aan de app, wordt ervan uitgegaan dat er een bericht wordt toegevoegd aan de wachtrij. Voeg de argumenten samen om een tekenreeks te maken. Voeg deze tekenreeks toe aan de berichtenwachtrij door de methode **InsertMessageAsync** aan te roepen die u eerder hebt toegevoegd.

Als er geen opdrachtregelargumenten zijn, probeert u een ophaalbewerking uit te voeren. Roep de methode **RetrieveNextMessageAsync** aan om het volgende bericht in de wachtrij op te halen.

Wacht ten slotte op invoer van de gebruiker voordat u afsluit met het aanroepen van **Console.ReadLine**.

1. Vouw de methode **Algemeen** uit om te controleren op opdrachtregelargumenten en te wachten op invoer van de gebruiker.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Sla het bestand op.

## <a name="complete-code"></a>Volledige code

Dit is de volledige codelijst voor dit project.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Voer vanaf de opdrachtregel in de projectmap de volgende DotNet-opdracht uit om het project te bouwen.

   ```console
   dotnet build
   ```

1. Nadat het project is gebouwd, voert u de volgende opdracht uit om het eerste bericht toe te voegen aan de wachtrij.

   ```console
   dotnet run First queue message
   ```

   U hoort deze uitvoer te zien:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

1. Voer de app uit zonder opdrachtregelargumenten om het eerste bericht in de wachtrij te ontvangen en te verwijderen.

   ```console
   dotnet run
   ```

1. Ga door met het uitvoeren van de app totdat alle berichten zijn verwijderd. Als u de app nog een keer uitvoert, ontvangt u een bericht dat de wachtrij leeg is en wordt u gevraagd om de wachtrij te verwijderen.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

1. Een wachtrij maken
1. Berichten toevoegen aan en verwijderen uit een wachtrij
1. Een Azure Storage-wachtrij verwijderen

Bekijk de quickstarts voor Azure-wachtrijen voor meer informatie.

> [!div class="nextstepaction"]
> [Quickstart voor wachtrijen - de portal](storage-quickstart-queues-portal.md)

- [Quickstart voor wachtrijen - .NET](storage-quickstart-queues-dotnet.md)
- [Quickstart voor wachtrijen - Java](storage-quickstart-queues-java.md)
- [Quickstart voor wachtrijen - Python](storage-quickstart-queues-python.md)
- [Quickstart voor wachtrijen - JavaScript](storage-quickstart-queues-nodejs.md)
