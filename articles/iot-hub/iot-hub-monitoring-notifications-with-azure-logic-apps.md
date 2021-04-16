---
title: Externe IoT-bewaking en meldingen met Azure Logic Apps | Microsoft Docs
description: Gebruik Azure Logic Apps voor IoT-temperatuurbewaking op uw IoT-hub en verzend automatisch e-mailmeldingen naar uw postvak voor gedetecteerde afwijkingen.
author: robinsh
keywords: IOT-bewaking, ioT-meldingen, ioT-temperatuurbewaking
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: 74724357dea9cd6c8c89a11a9eeb3d1b2933b790
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564932"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Externe IoT-bewaking en meldingen met Azure Logic Apps verbinding maken met uw IoT-hub en postvak

![End-to-end-diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) kunt u werkstromen beheren in on-premises en cloudservices, een of meer ondernemingen en in verschillende protocollen. Een logische app begint met een trigger, die vervolgens wordt gevolgd door een of meer acties die kunnen worden gesequentieerd met behulp van ingebouwde besturingselementen, zoals voorwaarden en iterators. Deze flexibiliteit maakt Logic Apps ideale IoT-oplossing voor IoT-bewakingsscenario's. De aankomst van telemetriegegevens van een apparaat op een IoT Hub-eindpunt kan bijvoorbeeld werkstromen van logische apps initiëren om de gegevens in een Azure Storage-blob te warehouseen, e-mailwaarschuwingen te verzenden om te waarschuwen voor gegevensafwijkingen, een onderhoudstechnicus te plannen om naar een storing te gaan, en meer.

In dit artikel leert u hoe u een logische app maakt die uw IoT-hub verbindt met uw postvak voor temperatuurbewaking en meldingen. De clientcode die op uw apparaat wordt uitgevoerd, stelt een toepassings-eigenschap in, , voor elk telemetriebericht dat naar uw `temperatureAlert` IoT-hub wordt gestuurd. Wanneer de clientcode een temperatuur van meer dan 30 C detecteert, stelt deze eigenschap in op . Anders wordt de eigenschap `true` op `false` .

Berichten die bij uw IoT-hub aankomen, zien er ongeveer als volgt uit, met de telemetriegegevens in de hoofdtekst en de eigenschap in de toepassingseigenschappen (systeemeigenschappen worden niet `temperatureAlert` weergegeven):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Zie Create and read IoT Hub messages (Berichten maken IoT Hub lezen) voor meer informatie [over IoT Hub berichtindeling.](iot-hub-devguide-messages-construct.md)

In dit onderwerp stelt u routering in op uw IoT-hub om berichten te verzenden waarin de eigenschap naar een Service Bus `temperatureAlert` `true` is. Vervolgens stelt u een logische app in die wordt triggers voor de berichten die op Service Bus eindpunt aankomen en u een e-mailmelding stuurt.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [zelfstudie Raspberry Pi Online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelfstudies over apparaten. U kunt bijvoorbeeld naar [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) gaan met node.jsof naar een van de quickstarts [Telemetrie](quickstart-send-telemetry-dotnet.md) verzenden. Deze artikelen hebben betrekking op de volgende vereisten:

  * Een actief Azure-abonnement.
  * Een Azure IoT-hub onder uw abonnement.
  * Een clienttoepassing die op uw apparaat wordt uitgevoerd en die telemetrieberichten naar uw Azure IoT-hub verzendt.

## <a name="create-service-bus-namespace-and-queue"></a>Een Service Bus en wachtrij maken

Maak een Service Bus-naamruimte en -wachtrij. Verderop in dit onderwerp maakt u een routeringsregel in uw IoT-hub om berichten met een temperatuurwaarschuwing naar de Service Bus-wachtrij te sturen, waar ze worden opgehaald door een logische app en deze activeren om een e-mailmelding te verzenden.

### <a name="create-a-service-bus-namespace"></a>Een Service Bus-naamruimte maken

1. Selecteer op [Azure Portal](https://portal.azure.com/)de optie **+ Een resource-integratie**  >  **maken**  >  **Service Bus**.

1. Geef in **het deelvenster Naamruimte** maken de volgende informatie op:

   **Naam:** de naam van de Service Bus-naamruimte. De naamruimte moet uniek zijn binnen Azure.

   **Prijscategorie:** selecteer **Basic** in de vervolgkeuzelijst. De basic-laag is voldoende voor deze zelfstudie.

   **Resourcegroep:** gebruik dezelfde resourcegroep die uw IoT-hub gebruikt.

   **Locatie:** gebruik dezelfde locatie die uw IoT-hub gebruikt.

   ![Een Service Bus-naamruimte maken in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecteer **Maken**. Wacht tot de implementatie is voltooid voordat u verder gaat met de volgende stap.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Een Service Bus toevoegen aan de naamruimte

1. Open de Service Bus naamruimte. De eenvoudigste manier om bij de Service Bus-naamruimte te komen, is door **Resourcegroepen** te selecteren in het resourcedeelvenster, uw resourcegroep te selecteren en vervolgens de Service Bus-naamruimte te selecteren in de lijst met resources.

1. Selecteer in **Service Bus deelvenster Naamruimte** + **Wachtrij**.

1. Voer een naam in voor de wachtrij en selecteer **vervolgens Maken.** Wanneer de wachtrij is gemaakt, wordt het **deelvenster** Wachtrij maken gesloten.

   ![Een Service Bus-wachtrij toevoegen in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Selecteer in **Service Bus deelvenster Naamruimte** onder **Entiteiten** de optie **Wachtrijen.** Open de Service Bus in de lijst en selecteer vervolgens **Gedeeld toegangsbeleid**  >  **+ Toevoegen.**

1. Voer een naam in voor het beleid, **schakel Beheren in** en selecteer vervolgens **Maken.**

   ![Een Service Bus-wachtrijbeleid toevoegen in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Een aangepaste eindpunt- en routeringsregel toevoegen aan uw IoT-hub

Voeg een aangepast eindpunt voor de Service Bus-wachtrij toe aan uw IoT-hub en maak een regel voor berichtroutering om berichten met een temperatuurwaarschuwing naar dat eindpunt te sturen, waar ze worden opgehaald door uw logische app. De routeringsregel maakt gebruik van een routeringsquery, , om berichten door te sturen op basis van de waarde van de toepassings-eigenschap die is ingesteld door de clientcode die op het `temperatureAlert = "true"` `temperatureAlert` apparaat wordt uitgevoerd. Zie Query voor berichtroutering op basis van berichteigenschappen [voor meer informatie.](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties)

### <a name="add-a-custom-endpoint"></a>Een aangepast eindpunt toevoegen

1. Open uw IoT-hub. De eenvoudigste manier om naar de IoT-hub te gaan, is door **Resourcegroepen** te selecteren in het resourcedeelvenster, uw resourcegroep te selecteren en vervolgens uw IoT-hub te selecteren in de lijst met resources.

1. Selecteer **onder Berichten** de optie **Berichtroutering.** Selecteer in **het deelvenster** Berichtroutering het tabblad **Aangepaste eindpunten** en selecteer **vervolgens + Toevoegen.** Selecteer Service Bus-wachtrij in de **vervolgkeuzelijst**.

   ![Schermopname met de optie Service Bus-wachtrij.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Voer in **het deelvenster Een Service Bus-eindpunt toevoegen** de volgende gegevens in:

   **Eindpuntnaam:** de naam van het eindpunt.

   **Service Bus-naamruimte:** selecteer de naamruimte die u hebt gemaakt.

   **Service Bus-wachtrij:** selecteer de wachtrij die u hebt gemaakt.

   ![Een eindpunt toevoegen aan uw IoT-hub in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecteer **Maken**. Nadat het eindpunt is gemaakt, gaat u verder met de volgende stap.

### <a name="add-a-routing-rule"></a>Een routeringsregel toevoegen

1. Selecteer in het **deelvenster Berichtroutering** het **tabblad Routes** en selecteer vervolgens **+ Toevoegen.**

1. Voer in **het deelvenster Een route** toevoegen de volgende gegevens in:

   **Naam:** de naam van de regel voor doorsturen.

   **Eindpunt:** selecteer het eindpunt dat u hebt gemaakt.

   **Gegevensbron:** selecteer **Telemetrieberichten voor apparaten.**

   **Routeringsquery:** voer `temperatureAlert = "true"` in.

   ![Voeg een regel voor doorsturen toe aan Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selecteer **Opslaan**. U kunt het deelvenster **Berichtroutering** sluiten.

## <a name="create-and-configure-a-logic-app"></a>Een logische app maken en configureren

In de vorige sectie hebt u uw IoT-hub ingesteld om berichten met een temperatuurwaarschuwing naar uw Service Bus sturen. U stelt nu een logische app in om de Service Bus wachtrij te bewaken en een e-mailmelding te verzenden wanneer er een bericht aan de wachtrij wordt toegevoegd.

### <a name="create-a-logic-app"></a>Een logische app maken

1. Selecteer **Een logische**  >  **resource-integratie-app**  >  **maken.**

1. Voer de volgende informatie in:

   **Naam:** de naam van de logische app.

   **Resourcegroep:** gebruik dezelfde resourcegroep die uw IoT-hub gebruikt.

   **Locatie:** gebruik dezelfde locatie die uw IoT-hub gebruikt.

   ![Een logische app maken in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecteer **Maken**.

### <a name="configure-the-logic-app-trigger"></a>De trigger voor de logische app configureren

1. Open de logische app. De eenvoudigste manier om bij de logische app te komen, is door **Resourcegroepen** te selecteren in het resourcedeelvenster, uw resourcegroep te selecteren en vervolgens uw logische app te selecteren in de lijst met resources. Wanneer u de logische app selecteert, wordt Logic Apps Designer geopend.

1. Schuif in Logic Apps Designer omlaag naar **Sjablonen** en selecteer **Lege logische app.**

   ![Begin met een lege logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecteer het **tabblad** Alle en selecteer vervolgens **Service Bus**.

   ![Selecteer Service Bus om te beginnen met het maken van uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Selecteer **onder Triggers** de optie Wanneer een of meer berichten in een wachtrij binnenkomen **(automatisch voltooien).**

   ![Selecteer de trigger voor uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Maak een Service Bus-verbinding.
   1. Voer een verbindingsnaam in en selecteer Service Bus naamruimte in de lijst. Het volgende scherm wordt geopend.

      ![Schermopname met de optie Wanneer een of meer berichten in een wachtrij binnenkomen (automatisch voltooien).](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecteer het Service Bus-beleid (RootManageSharedAccessKey). Selecteer vervolgens **Maken.**

      ![Maak een Service Bus-verbinding voor uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Selecteer in het laatste scherm bij **Wachtrijnaam** de wachtrij die u hebt gemaakt in de vervolgkeuzelijst. Voer `175` in bij Maximum aantal **berichten.**

      ![Geef het maximum aantal berichten op voor de Service Bus-verbinding in uw logische app](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selecteer **Opslaan** in het menu bovenaan de Logic Apps Designer om uw wijzigingen op te slaan.

### <a name="configure-the-logic-app-action"></a>De actie van de logische app configureren

1. Maak een SMTP-serviceverbinding.

   1. Selecteer **Nieuwe stap**. Selecteer **in Kies een actie** het **tabblad** Alle.

   1. Typ `smtp` in het zoekvak, selecteer de **SMTP-service** in het zoekresultaat en selecteer **vervolgens E-mail verzenden.**

      ![Een SMTP-verbinding maken in uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Voer de SMTP-gegevens voor uw postvak in en selecteer **vervolgens Maken.**

      ![Voer smtp-verbindingsgegevens in uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Haal de SMTP-informatie [op voor Hotmail/Outlook.com,](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970) [Gmail](https://support.google.com/a/answer/176600?hl=en)en [Yahoo Mail.](https://help.yahoo.com/kb/SLN4075.html)

      > [!NOTE]
      > Mogelijk moet u TLS/SSL uitschakelen om de verbinding tot stand te brengen. Als dit het geval is en u TLS opnieuw wilt inschakelen nadat de verbinding tot stand is gebracht, bekijkt u de optionele stap aan het einde van deze sectie.

   1. Selecteer in **de vervolgkeuzekeuze selecteren** in de vervolgkeuze vervolgkeuzekeuze voor nieuwe parameter toevoegen in de stap **E-mail** verzenden de optie **Van**, **Naar**, **Onderwerp** en **Tekst.** Klik of tik ergens op het scherm om het selectievak te sluiten.

      ![E-mailvelden voor SMTP-verbinding kiezen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Voer uw e-mailadres in **voor Van** **en Naar** en `High temperature detected` voor **Onderwerp** en **Hoofd.** Als het dialoogvenster Dynamische inhoud toevoegen uit de apps en **connectors die in** deze stroom worden gebruikt wordt geopend, selecteert u Verbergen **om** deze te sluiten. In deze zelfstudie gebruikt u geen dynamische inhoud.

      ![Invullen van e-mailvelden voor SMTP-verbinding](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selecteer **Opslaan om** de SMTP-verbinding op te slaan.

1. (Optioneel) Als u TLS moest uitschakelen om verbinding te maken met uw e-mailprovider en deze opnieuw wilt inschakelen, volgt u deze stappen:

   1. Selecteer in **het deelvenster Logische app** onder **Ontwikkelprogramma's** de **optie API-verbindingen.**

   1. Selecteer de SMTP-verbinding in de lijst met API-verbindingen.

   1. Selecteer in **het deelvenster SMTP API-verbinding** onder **Algemeen** de optie **API-verbinding bewerken.**

   1. Selecteer in **het deelvenster API-verbinding** bewerken de optie **SSL inschakelen?**, voer het wachtwoord voor uw e-mailaccount opnieuw in en selecteer **Opslaan.**

      ![Bewerk de SMTP-API-verbinding in uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Uw logische app is nu gereed voor het verwerken van temperatuurwaarschuwingen vanuit Service Bus wachtrij en het verzenden van meldingen naar uw e-mailaccount.

## <a name="test-the-logic-app"></a>Test de logische app

1. Start de clienttoepassing op uw apparaat.

1. Als u een fysiek apparaat gebruikt, brengt u zorgvuldig een warmtebron in de buurt van de warmtesensor totdat de temperatuur hoger is dan 30 graden C. Als u de onlinesimulator gebruikt, zal de clientcode willekeurig telemetrieberichten uitvoeren die groter zijn dan 30 C.

1. U moet e-mailmeldingen ontvangen die door de logische app zijn verzonden.

   > [!NOTE]
   > Uw e-mailserviceprovider moet mogelijk de identiteit van de afzender controleren om te controleren of u de e-mail verzendt.

## <a name="next-steps"></a>Volgende stappen

U hebt een logische app gemaakt die uw IoT-hub en uw postvak verbindt voor temperatuurbewaking en meldingen.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]