---
title: Logische apps aanroepen met Azure Functions
description: Logische apps aanroepen of activeren met behulp van Azure Functions en Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 25f761d85ebfd0ac16f182941c5b5c29636066bf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000331"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Logische apps aanroepen of activeren met behulp van Azure Functions en Azure Service Bus

U kunt [Azure functions](../azure-functions/functions-overview.md) gebruiken om een logische app te activeren wanneer u een langlopende listener of taak moet implementeren. U kunt bijvoorbeeld een functie maken die in een [Azure service bus](../service-bus-messaging/service-bus-messaging-overview.md) wachtrij luistert en onmiddellijk een logische app als een push trigger wordt geactiveerd.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een Azure Service Bus naam ruimte. Als u geen naam ruimte hebt, moet u [eerst uw naam ruimte maken](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Een functie-app, een container voor uw functies. Als u geen functie-app hebt, [maakt u eerst uw functie-app](../azure-functions/functions-create-first-azure-function.md)en zorgt u ervoor dat u .net selecteert als runtime stack.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Logische app maken

Voor dit scenario hebt u een functie voor het uitvoeren van elke logische app die u wilt activeren. Maak eerst een logische app die begint met een HTTP-aanvraag trigger. De functie roept dat eind punt op wanneer er een wachtrij bericht wordt ontvangen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en maak een lege logische app.

   Als u geen ervaring hebt met Logic apps, raadpleegt u [Quick Start: uw eerste logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Typ `http request` in het zoekvak. Selecteer in de lijst triggers de trigger **Wanneer een HTTP-aanvraag wordt ontvangen** .

   ![Trigger selecteren](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Met de aanvraag trigger kunt u optioneel een JSON-schema invoeren dat moet worden gebruikt met het bericht in de wachtrij. JSON-schema's helpen de Logic app Designer inzicht te krijgen in de structuur van de invoer gegevens en de uitvoer gemakkelijker te maken voor gebruik in uw werk stroom.

1. Als u een schema wilt opgeven, voert u het schema in het vak **JSON-schema van aanvraag tekst** in, bijvoorbeeld:

   ![JSON-schema opgeven](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Als u geen schema hebt, maar u een voor beeld van een nettolading in JSON-indeling hebt, kunt u een schema genereren op basis van die nettolading.

   1. Selecteer in de trigger voor de aanvraag een **voor beeld-nettolading gebruiken om een schema te genereren**.

   1. Voer onder **ENTER of plak een JSON**-voor beeld-nettolading in en selecteer vervolgens **gereed**.

      ![Voor beeld-nettolading invoeren](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Met deze nettolading wordt dit schema gegenereerd dat wordt weer gegeven in de trigger:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Voeg andere acties toe die u wilt uitvoeren na ontvangst van het bericht in de wachtrij.

   U kunt bijvoorbeeld een e-mail bericht verzenden met de Office 365 Outlook-Connector.

1. Sla de logische app op, die de call back-URL voor de trigger in deze logische app genereert. Later gebruikt u deze call back-URL in de code voor de trigger voor de Azure Service Bus-wachtrij.

   De call back-URL wordt weer gegeven in de eigenschap **http post-URL** .

   ![Gegenereerde call back-URL voor trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>Een functie maken

Maak vervolgens de functie die als trigger fungeert en luistert naar de wachtrij.

1. Open en vouw in de Azure Portal uw functie-app, als deze nog niet is geopend. 

1. Vouw **functies** onder de naam van de functie-app uit. Selecteer in het deel venster **functies** de optie **nieuwe functie**.

   ![Vouw ' functions ' uit en selecteer ' nieuwe functie '](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Selecteer deze sjabloon op basis van het feit of u een nieuwe functie-app hebt gemaakt waarbij u .NET als runtime stack hebt geselecteerd, of dat u een bestaande functie-app gebruikt.

   * Selecteer voor nieuwe functie-apps deze sjabloon: **Service Bus wachtrij trigger**

     ![Sjabloon selecteren voor nieuwe functie-app](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Voor een bestaande functie-app selecteert u deze sjabloon: **Service Bus wachtrij trigger-C#**

     ![Selecteer een sjabloon voor de bestaande functie-app](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Geef in het deel venster **Azure service bus wachtrij activeren** een naam op voor de trigger en stel de **Service Bus verbinding** voor de wachtrij in, die gebruikmaakt van de Azure service bus SDK `OnMessageReceive()` -listener en selecteer **maken**.

1. Schrijf een Basic-functie om het eerder gemaakte logische app-eind punt aan te roepen met behulp van het wachtrij bericht als een trigger. Lees de volgende overwegingen voordat u de functie schrijft:

   * In dit voor beeld wordt het `application/json` inhouds type bericht gebruikt, maar u kunt dit type zo nodig wijzigen.
   
   * Als gevolg van mogelijke gelijktijdig uitgevoerde functies, grote volumes of zware belastingen, moet u voor komen dat u de [httpclient maakt-klasse](/dotnet/api/system.net.http.httpclient) bij de-instructie instantiëren `using` en rechtstreeks httpclient maakt-exemplaren per aanvraag maakt. Zie [HttpClientFactory gebruiken voor het implementeren van flexibele HTTP-aanvragen](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)voor meer informatie.
   
   * Gebruik, indien mogelijk, het exemplaar van HTTP-clients opnieuw. Zie [verbindingen beheren in azure functions](../azure-functions/manage-connections.md)voor meer informatie.

   In dit voor beeld wordt de- [ `Task.Run` methode](/dotnet/api/system.threading.tasks.task.run) in de [asynchrone](/dotnet/csharp/language-reference/keywords/async) modus gebruikt. Zie [asynchrone programmering met async en await](/dotnet/csharp/programming-guide/concepts/async/)voor meer informatie.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. U kunt de functie testen door een wachtrij bericht toe te voegen met een hulp programma zoals de [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   De logische app activeert onmiddellijk nadat de functie het bericht heeft ontvangen.

## <a name="next-steps"></a>Volgende stappen

* [Aanroepen, activeren of nesten van werk stromen met behulp van HTTP-eind punten](../logic-apps/logic-apps-http-endpoint.md)
