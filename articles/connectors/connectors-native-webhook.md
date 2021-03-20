---
title: Wachten en reageren op gebeurtenissen
description: Werk stromen automatiseren die op basis van gebeurtenissen in een service-eind punt activeren, onderbreken en hervatten met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 7c6f3c4e3e4a2a29fe6a02c03043e3dfb81a2010
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89227896"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Geautomatiseerde op gebeurtenis gebaseerde werk stromen maken en uitvoeren met behulp van HTTP-webhooks in Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de ingebouwde http-webhook-connector kunt u geautomatiseerde taken en werk stromen maken die zich abonneren op een service-eind punt, wachten op specifieke gebeurtenissen en uitvoeren op basis van deze gebeurtenissen, in plaats van regel matige controle of *polling* van dat eind punt.

Hier volgen enkele voor beelden van webhook-werk stromen:

* Wacht totdat een item is ontvangen van een [Azure Event hub](https://github.com/logicappsio/EventHubAPI) voordat de uitvoering van een logische app wordt geactiveerd.
* Wacht op een goed keuring voordat u doorgaat met een werk stroom.

In dit artikel wordt beschreven hoe u de webhook-trigger en de webhook-actie gebruikt, zodat uw logische app gebeurtenissen op een service-eind punt kan ontvangen en erop kan reageren.

## <a name="how-do-webhooks-work"></a>Hoe werken webhooks?

Een webhook-trigger is gebaseerd op gebeurtenissen. Dit is niet afhankelijk van het controleren of pollen regel matig voor nieuwe items. Wanneer u een logische app opslaat die begint met een webhook-trigger of wanneer u de logische app van uitgeschakeld naar ingeschakeld, wordt de webhook geactiveerd door een *call back-URL* te *registreren bij het* opgegeven eind punt. De trigger wacht vervolgens op dat service-eind punt om de URL aan te roepen, waardoor de logische app wordt uitgevoerd. Net als bij de [aanvraag trigger](connectors-native-reqres.md)wordt de logische app onmiddellijk geactiveerd wanneer de opgegeven gebeurtenis plaatsvindt. De webhook-trigger *meldt zich* af bij het service-eind punt als u de trigger verwijdert en uw logische app opslaat, of wanneer u de logische app wijzigt van ingeschakeld in uitgeschakeld.

Een webhook-actie is ook op gebeurtenis gebaseerd en *abonneert* zich op het opgegeven eind punt van de service door een *call back-URL* te registreren bij dat eind punt. Met de actie webhook wordt de werk stroom van de logische app onderbroken en wordt gewacht totdat het service-eind punt de URL aanroept voordat de logische app wordt hervat. De webhook-actie meldt *zich* in de volgende gevallen af van het service-eind punt:

* Wanneer de webhook-actie is voltooid
* Als de uitvoering van de logische app wordt geannuleerd tijdens het wachten op een reactie
* Voordat er een time-out optreedt voor de logische app

Zo is de actie [**e-mail bericht verzenden goed keuren**](connectors-create-api-office365-outlook.md) van Office 365 Outlook Connector een voor beeld van een webhook-actie die volgt op dit patroon. U kunt dit patroon uitbreiden in een service met behulp van de webhook-actie.

Raadpleeg de volgende onderwerpen voor meer informatie:

* [Webhooks en abonnementen](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Aangepaste Api's maken die ondersteuning bieden voor een webhook](../logic-apps/logic-apps-create-api-app.md)

Voor informatie over versleuteling, beveiliging en autorisatie voor inkomende oproepen naar uw logische app, zoals [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security), voorheen bekend als Secure Sockets Layer (SSL) of [Azure Active Directory open verificatie (Azure AD OAuth)](../active-directory/develop/index.yml), raadpleegt [u beveiligde toegang en gegevens toegang voor inkomende oproepen op op aanvragen gebaseerde triggers](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests).

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* De URL voor een al geïmplementeerd eind punt of API die ondersteuning biedt voor het abonneren en abonneren van webhooks voor [webhook-triggers in Logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) of [webhook-acties in Logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) , indien van toepassing

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

* De logische app waar u wilt wachten op specifieke gebeurtenissen op het doel eindpunt. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met de http-webhook-trigger. Als u de actie HTTP-webhook wilt gebruiken, start u uw logische app met een wille keurige trigger. In dit voor beeld wordt de HTTP-trigger als eerste stap gebruikt.

## <a name="add-an-http-webhook-trigger"></a>Een HTTP-webhook-trigger toevoegen

Deze ingebouwde trigger roept het eind punt van het abonnement op de doel service aan en registreert een call back-URL met de doel service. De logische app wacht vervolgens tot de doel service een `HTTP POST` aanvraag naar de call back-URL verzendt. Wanneer deze gebeurtenis plaatsvindt, wordt de trigger geactiveerd en worden gegevens in de aanvraag samen met de werk stroom door gegeven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw lege logische app in de ontwerp functie voor logische apps.

1. Voer als filter in het zoekvak van de ontwerp functie in `http webhook` . Selecteer in de lijst **Triggers** de **http-webhook-** trigger.

   ![Trigger voor HTTP-webhook selecteren](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   In dit voor beeld wordt de naam van de trigger gewijzigd `HTTP Webhook trigger` zodat de stap een meer beschrijvende naam heeft. Daarnaast voegt het voor beeld een HTTP-webhook-actie toe en beide namen moeten uniek zijn.

1. Geef de waarden voor de [para meters voor de http-webhook-trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) op die u wilt gebruiken voor de aanroepen van abonneren en het afmelden.

   In dit voor beeld bevat de trigger de methoden, Uri's en bericht teksten die moeten worden gebruikt bij het uitvoeren van de abonnements-en afmeldings bewerkingen.

   ![Para meters voor HTTP-webhook-trigger opgeven](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Abonnement-methode** | Ja | De methode die moet worden gebruikt bij het abonneren op het doel eindpunt |
   | **Abonneren: URI** | Ja | De URL die moet worden gebruikt voor het abonneren op het doel eindpunt |
   | **Abonneren: hoofd tekst** | Nee | Een bericht tekst die moet worden meegenomen in de abonnements aanvraag. Dit voor beeld bevat de call back-URL waarmee de abonnee op unieke wijze wordt geïdentificeerd: de logische app, door gebruik te maken van de `@listCallbackUrl()` expressie voor het ophalen van de call back-URL van de logische app. |
   | **Afmelden-methode** | Nee | De methode die moet worden gebruikt bij het afmelden van het doel eindpunt |
   | **Opzeg ging van de URI** | Nee | De URL die moet worden gebruikt voor het afmelden van het doel eindpunt |
   | **Afmelden: hoofd tekst** | Nee | Een optionele bericht tekst die moet worden meegenomen in de afmeldings aanvraag <p><p>**Opmerking**: deze eigenschap biedt geen ondersteuning voor het gebruik van de `listCallbackUrl()` functie. De trigger neemt echter automatisch de headers op en verzendt deze `x-ms-client-tracking-id` en `x-ms-workflow-operation-name` , die de doel service kan gebruiken om de abonnee uniek te identificeren. |
   ||||

1. Open de lijst **nieuwe para meters toevoegen** om andere trigger eigenschappen toe te voegen.

   ![Meer trigger eigenschappen toevoegen](./media/connectors-native-webhook/http-webhook-trigger-add-properties.png)

   Als u bijvoorbeeld verificatie moet gebruiken, kunt u de eigenschappen **Abonneren-authenticatie** en **Afmelden/authenticatie** toevoegen. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over de beschik bare verificatie typen voor http-webhook.

1. Ga door met het bouwen van de werk stroom van uw logische app met acties die worden uitgevoerd wanneer de trigger wordt geactiveerd.

1. Wanneer u klaar bent, kunt u uw logische app opslaan. Selecteer **Opslaan** op de werkbalk van de ontwerper.

   Als u uw logische app opslaat, wordt het eind punt van de abonnee op de doel service aangeroepen en wordt de call back-URL geregistreerd. De logische app wacht vervolgens tot de doel service een `HTTP POST` aanvraag naar de call back-URL verzendt. Wanneer deze gebeurtenis plaatsvindt, wordt de trigger geactiveerd en worden gegevens in de aanvraag samen met de werk stroom door gegeven. Als deze bewerking is voltooid, wordt de trigger afgemeld bij het eind punt en wordt de resterende werk stroom door de logische app voortgezet.

## <a name="add-an-http-webhook-action"></a>Een HTTP-webhook-actie toevoegen

Met deze ingebouwde actie wordt het eind punt voor abonneren op de doel service aangeroepen en wordt een call back-URL geregistreerd bij de doel service. Uw logische app wordt vervolgens onderbroken en er wordt gewacht tot de doel service een `HTTP POST` aanvraag naar de call back-URL verzendt. Wanneer deze gebeurtenis plaatsvindt, geeft de actie gegevens in de aanvraag door aan de werk stroom. Als de bewerking is voltooid, wordt de actie afgemeld bij het eind punt, waarna de logische app de resterende werk stroom blijft uitvoeren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Open uw logische app in de ontwerp functie voor logische apps.

   In dit voor beeld wordt de HTTP-webhook-trigger als de eerste stap gebruikt.

1. Selecteer **nieuwe stap** onder de stap waarin u de http-webhook-actie wilt toevoegen.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer als filter in het zoekvak van de ontwerp functie in `http webhook` . Selecteer in de lijst **acties** de actie **http-webhook** .

   ![Actie voor HTTP-webhook selecteren](./media/connectors-native-webhook/select-http-webhook-action.png)

   In dit voor beeld wordt de naam van de actie gewijzigd in HTTP-webhook-actie zodat de stap een meer beschrijvende naam heeft.

1. Geef de waarden op voor de para meters van de HTTP-webhook-actie, die vergelijkbaar zijn met de [para meters voor de http-webhook-trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)die u wilt gebruiken voor de aanroepen voor abonneren en het afmelden.

   In dit voor beeld omvat de actie de methoden, Uri's en bericht teksten die moeten worden gebruikt bij het uitvoeren van de abonnements-en afmeldings bewerkingen.

   ![Actie parameters voor HTTP-webhook opgeven](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Abonnement-methode** | Ja | De methode die moet worden gebruikt bij het abonneren op het doel eindpunt |
   | **Abonneren: URI** | Ja | De URL die moet worden gebruikt voor het abonneren op het doel eindpunt |
   | **Abonneren: hoofd tekst** | Nee | Een bericht tekst die moet worden meegenomen in de abonnements aanvraag. Dit voor beeld bevat de call back-URL waarmee de abonnee op unieke wijze wordt geïdentificeerd: de logische app, door gebruik te maken van de `@listCallbackUrl()` expressie voor het ophalen van de call back-URL van de logische app. |
   | **Afmelden-methode** | Nee | De methode die moet worden gebruikt bij het afmelden van het doel eindpunt |
   | **Opzeg ging van de URI** | Nee | De URL die moet worden gebruikt voor het afmelden van het doel eindpunt |
   | **Afmelden: hoofd tekst** | Nee | Een optionele bericht tekst die moet worden meegenomen in de afmeldings aanvraag <p><p>**Opmerking**: deze eigenschap biedt geen ondersteuning voor het gebruik van de `listCallbackUrl()` functie. De actie bevat echter automatisch de headers en verzendt deze `x-ms-client-tracking-id` en `x-ms-workflow-operation-name` , die de doel service kan gebruiken om de abonnee uniek te identificeren. |
   ||||

1. Open de lijst **nieuwe para meters** toevoegen om andere actie-eigenschappen toe te voegen.

   ![Meer actie-eigenschappen toevoegen](./media/connectors-native-webhook/http-webhook-action-add-properties.png)

   Als u bijvoorbeeld verificatie moet gebruiken, kunt u de eigenschappen **Abonneren-authenticatie** en **Afmelden/authenticatie** toevoegen. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie over de beschik bare verificatie typen voor http-webhook.

1. Wanneer u klaar bent, moet u uw logische app opslaan. Selecteer **Opslaan** op de werkbalk van de ontwerper.

   Wanneer deze actie nu wordt uitgevoerd, roept uw logische app het eind punt voor abonneren aan bij de doel service en registreert de call back-URL. Met de logische app wordt de werk stroom onderbroken en wordt gewacht tot de doel service een `HTTP POST` aanvraag naar de call back-URL verzendt. Wanneer deze gebeurtenis plaatsvindt, geeft de actie gegevens in de aanvraag door aan de werk stroom. Als de bewerking is voltooid, wordt de actie afgemeld bij het eind punt, waarna de logische app de resterende werk stroom blijft uitvoeren.

## <a name="trigger-and-action-outputs"></a>Trigger-en actie-uitvoer

Hier vindt u meer informatie over de uitvoer van een HTTP-webhook-trigger of-actie, waarmee deze gegevens worden geretourneerd:

| Naam van eigenschap | Type | Beschrijving |
|---------------|------|-------------|
| koppen | object | De headers van de aanvraag |
| body | object | JSON-object | Het object met de inhoud van de hoofd tekst van de aanvraag |
| statuscode | int | De status code van de aanvraag |
|||

| Statuscode | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Geaccepteerd |
| 400 | Ongeldige aanvraag |
| 401 | Niet geautoriseerd |
| 403 | Verboden |
| 404 | Niet gevonden |
| 500 | Interne serverfout. Er is een onbekende fout opgetreden. |
|||

## <a name="connector-reference"></a>Connector-verwijzing

Zie [http-webhook-para meters](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)voor meer informatie over trigger-en actie parameters, die vergelijkbaar zijn met elkaar.

## <a name="next-steps"></a>Volgende stappen

* [Beveiligde toegang en gegevens toegang voor inkomende oproepen naar activeringen op basis van een aanvraag](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Connectors voor Logic Apps](../connectors/apis-list.md)
