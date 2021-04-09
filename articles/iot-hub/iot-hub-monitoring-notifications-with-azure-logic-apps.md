---
title: IoT externe bewaking en meldingen met Azure Logic Apps | Microsoft Docs
description: Gebruik Azure Logic Apps voor IoT-temperatuur bewaking op uw IoT-hub en verzend automatisch e-mail meldingen naar uw postvak om eventuele afwijkingen op te sporen.
author: robinsh
keywords: IOT-bewaking, IOT-meldingen, IOT-temperatuur bewaking
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: cd14ff0688f4230aeedac748ca4b32609bdd2938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92490319"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT-externe bewaking en meldingen met Azure Logic Apps verbinding maken met uw IoT hub en Postvak

![End-to-end-diagram](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic apps](../logic-apps/index.yml) kan u helpen bij het organiseren van werk stromen over on-premises en Cloud Services, een of meer ondernemingen en verschillende protocollen. Een logische app begint met een trigger, gevolgd door een of meer acties die kunnen worden geordend met behulp van ingebouwde besturings elementen, zoals voor waarden en herhalingen. Deze flexibiliteit maakt het Logic Apps van een ideale IoT-oplossing voor IoT-bewakings scenario's. Bijvoorbeeld, de aankomst van telemetriegegevens van een apparaat op een IoT Hub-eind punt kan werk stromen voor logische apps initiëren voor het magazijn van de gegevens in een Azure Storage blob, e-mail waarschuwingen verzenden naar waarschuwing over gegevens afwijkingen, een technicus naar een bezoek brengen als een apparaat een fout meldt, enzovoort.

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u een logische app maakt die uw IoT-hub en uw postvak verbindt voor temperatuur bewaking en meldingen.

De client code die wordt uitgevoerd op uw apparaat stelt een toepassings eigenschap `temperatureAlert` in, op elke telemetrie-bericht naar uw IOT-hub. Wanneer de client code een Tempe ratuur van meer dan 30 C detecteert, wordt deze eigenschap ingesteld op. als dat `true` niet het geval is, wordt de eigenschap ingesteld op `false` .

Berichten die op uw IoT-hub arriveren, zien er ongeveer als volgt uit, met de telemetriegegevens in de hoofd tekst en de `temperatureAlert` eigenschap in de toepassings eigenschappen (systeem eigenschappen worden niet weer gegeven):

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

Zie [IOT hub berichten maken en lezen](iot-hub-devguide-messages-construct.md)voor meer informatie over de indeling van IOT hub berichten.

In dit onderwerp stelt u route ring in op uw IoT-hub om berichten te verzenden waarin de `temperatureAlert` eigenschap `true` naar een service bus eind punt gaat. Vervolgens stelt u een logische app in die de berichten activeert die binnenkomen op het Service Bus-eind punt en ontvangt u een e-mail melding.

## <a name="what-you-do"></a>Wat u doet

* Maak een Service Bus naam ruimte en voeg er een Service Bus wachtrij aan toe.
* Voeg een aangepast eind punt en een regel voor door sturen aan uw IoT-hub toe om berichten die een temperatuur waarschuwing bevatten, te routeren naar de Service Bus wachtrij.
* Een logische app maken, configureren en testen om berichten uit uw Service Bus wachtrij te gebruiken en e-mail meldingen naar een gewenste ontvanger te verzenden.

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de zelf studie [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelf studies van het apparaat. bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze voldoen aan de volgende vereisten:

  * Een actief Azure-abonnement.
  * Een Azure IoT hub onder uw abonnement.
  * Een client toepassing die op uw apparaat wordt uitgevoerd en dat telemetrie-berichten verzendt naar uw Azure IoT hub.

## <a name="create-service-bus-namespace-and-queue"></a>Service Bus naam ruimte en wachtrij maken

Maak een Service Bus-naamruimte en -wachtrij. Verderop in dit onderwerp maakt u een regel voor de route ring in uw IoT-hub om berichten te sturen die een temperatuur waarschuwing bevatten voor de Service Bus wachtrij, waar ze worden opgehaald door een logische app en deze activeren om een e-mail melding te verzenden.

### <a name="create-a-service-bus-namespace"></a>Een Service Bus-naamruimte maken

1. Selecteer op de [Azure Portal](https://portal.azure.com/) **+ een resource**-  >  **integratie**  >  **Service Bus** maken.

1. Geef in het deel venster **naam ruimte maken** de volgende informatie op:

   **Naam**: de naam van de service bus-naam ruimte. De naam ruimte moet uniek zijn binnen Azure.

   **Prijs categorie**: Selecteer **basis** in de vervolg keuzelijst. De laag basis is voldoende voor deze zelf studie.

   **Resource groep**: gebruik dezelfde resource groep als uw IOT-hub.

   **Locatie**: gebruik dezelfde locatie als uw IOT-hub.

   ![Een service bus-naam ruimte maken in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Selecteer **Maken**. Wacht tot de implementatie is voltooid voordat u verdergaat met de volgende stap.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Een Service Bus wachtrij toevoegen aan de naam ruimte

1. Open de Service Bus naam ruimte. De eenvoudigste manier om naar de Service Bus naam ruimte te gaan, is door **resource groepen** te selecteren in het deel venster Resource, de resource groep te selecteren en vervolgens de service bus naam ruimte te selecteren in de lijst met resources.

1. Selecteer in het deel venster **Service Bus naam ruimte** de optie **+ wachtrij**.

1. Voer een naam in voor de wachtrij en selecteer vervolgens **maken**. Wanneer de wachtrij is gemaakt, wordt het deel venster **wachtrij maken** gesloten.

   ![Een service bus-wachtrij toevoegen in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Ga terug naar het deel venster **Service Bus naam ruimte** , onder **entiteiten**, selecteer **wacht rijen**. Open de service bus wachtrij in de lijst en selecteer vervolgens **beleid voor gedeelde toegang**  >  **+ toevoegen**.

1. Voer een naam in voor het beleid, Controleer **beheren** en selecteer vervolgens **maken**.

   ![Een service bus-wachtrij beleid toevoegen in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Een aangepast eind punt en een regel voor door sturen toevoegen aan uw IoT-hub

Voeg een aangepast eind punt voor de Service Bus wachtrij toe aan uw IoT-hub en maak een bericht routering regel om berichten te sturen die een temperatuur waarschuwing bevatten voor dat eind punt, waar ze worden opgehaald door uw logische app. De routerings regel gebruikt een routerings query `temperatureAlert = "true"` om berichten door te sturen op basis van de waarde van de `temperatureAlert` eigenschap Application die is ingesteld door de client code die op het apparaat wordt uitgevoerd. Zie [bericht routerings query op basis van bericht eigenschappen](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties)voor meer informatie.

### <a name="add-a-custom-endpoint"></a>Een aangepast eind punt toevoegen

1. Open uw IoT-hub. De eenvoudigste manier om de IoT-hub te bereiken, is door **resource groepen** te selecteren in het deel venster Resource, de resource groep te selecteren en vervolgens uw IOT-hub te selecteren in de lijst met resources.

1. Selecteer **bericht routering** onder **Messa ging**. Selecteer in het deel venster **bericht routering** het tabblad **aangepaste eind punten** en selecteer vervolgens **+ toevoegen**. Selecteer **Service Bus-wachtrij** in de vervolg keuzelijst.

   ![Scherm afbeelding die de service bus-wachtrij optie markeert.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Voer in het deel venster **een service bus-eind punt toevoegen** de volgende gegevens in:

   **Naam van eind punt**: de naam van het eind punt.

   **Service Bus-naam ruimte**: Selecteer de naam ruimte die u hebt gemaakt.

   **Service Bus-wachtrij**: Selecteer de wachtrij die u hebt gemaakt.

   ![Een eind punt toevoegen aan uw IoT-hub in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Selecteer **Maken**. Nadat het eind punt is gemaakt, gaat u verder met de volgende stap.

### <a name="add-a-routing-rule"></a>Een routeringsregel toevoegen

1. Ga terug naar het deel venster **bericht routering** , selecteer het tabblad **routes** en selecteer vervolgens **+ toevoegen**.

1. Voer in het deel venster **een route toevoegen** de volgende gegevens in:

   **Naam**: de naam van de routerings regel.

   **Eind punt**: Selecteer het eind punt dat u hebt gemaakt.

   **Gegevens bron**: Selecteer de **telemetrie-berichten van apparaten**.

   **Routerings query**: invoeren `temperatureAlert = "true"` .

   ![Een regel voor door sturen toevoegen in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Selecteer **Opslaan**. U kunt het deel venster **bericht routering** sluiten.

## <a name="create-and-configure-a-logic-app"></a>Een logische app maken en configureren

In de voor gaande sectie stelt u uw IoT-hub in voor het routeren van berichten met een temperatuur waarschuwing naar uw Service Bus wachtrij. Nu stelt u een logische app in om de Service Bus wachtrij te controleren en een e-mail melding te verzenden wanneer een bericht wordt toegevoegd aan de wachtrij.

### <a name="create-a-logic-app"></a>Een logische app maken

1. Selecteer **een**  >    >  **logische app** voor het integreren van resources maken.

1. Voer de volgende informatie in:

   **Naam**: de naam van de logische app.

   **Resource groep**: gebruik dezelfde resource groep als uw IOT-hub.

   **Locatie**: gebruik dezelfde locatie als uw IOT-hub.

   ![Een logische app maken in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Selecteer **Maken**.

### <a name="configure-the-logic-app-trigger"></a>De trigger van de logische app configureren

1. Open de logische app. De eenvoudigste manier om naar de logische app te gaan, is door **resource groepen** te selecteren in het deel venster Resource, de resource groep te selecteren en vervolgens uw logische app te selecteren in de lijst met resources. Wanneer u de logische app selecteert, wordt de Logic Apps Designer geopend.

1. Schuif in de Logic Apps Designer omlaag naar **sjablonen** en selecteer **lege logische app**.

   ![Beginnen met een lege logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Selecteer het tabblad **alle** en selecteer vervolgens **Service Bus**.

   ![Selecteer Service Bus om de logische app te gaan maken in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Onder **Triggers** selecteert u **Wanneer een of meer berichten binnenkomen in een wachtrij (automatisch volt ooien)**.

   ![Selecteer de trigger voor uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Een service bus-verbinding maken.
   1. Geef een naam op voor de verbinding en selecteer uw Service Bus naam ruimte in de lijst. Het volgende scherm wordt geopend.

      ![Scherm opname van de optie wanneer een of meer berichten binnenkomen in een wachtrij (automatisch volt ooien).](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Selecteer het service bus-beleid (RootManageSharedAccessKey). Selecteer vervolgens  **maken**.

      ![Een service bus-verbinding maken voor uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Selecteer in het laatste scherm bij **wachtrij naam** de wachtrij die u hebt gemaakt in de vervolg keuzelijst. Voer `175` in voor het **maximum aantal berichten**.

      ![Geef het maximum aantal berichten op voor de Service Bus-verbinding in uw logische app](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Selecteer **Opslaan** in het menu boven aan de Logic apps Designer om uw wijzigingen op te slaan.

### <a name="configure-the-logic-app-action"></a>De actie van de logische app configureren

1. Een SMTP-service verbinding maken.

   1. Selecteer **Nieuwe stap**. In **Kies een actie** selecteert u het tabblad **alle** .

   1. Typ `smtp` in het zoekvak, selecteer de **SMTP-** service in het Zoek resultaat en selecteer vervolgens **e-mail verzenden**.

      ![Een SMTP-verbinding maken in uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Voer de SMTP-gegevens voor uw postvak in en selecteer **maken**.

      ![Geef de SMTP-verbindings gegevens in uw logische app op in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      De SMTP-informatie ophalen voor [Hotmail/Outlook. com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en)en [Yahoo mail](https://help.yahoo.com/kb/SLN4075.html).

      > [!NOTE]
      > Mogelijk moet u TLS/SSL uitschakelen om de verbinding tot stand te brengen. Als dit het geval is en u TLS opnieuw wilt inschakelen nadat de verbinding tot stand is gebracht, raadpleegt u de optionele stap aan het eind van deze sectie.

   1. Selecteer in de vervolg keuzelijst **nieuwe para meter toevoegen** in de stap **e-mail verzenden** de optie **van**, **naar**, **onderwerp** en **hoofd tekst**. Klik of tik op een wille keurige plaats op het scherm om het selectie vakje te sluiten.

      ![E-mail velden voor SMTP-verbindingen kiezen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Voer uw e-mail adres **in voor van** en naar, en voor **het** `High temperature detected` **onderwerp** en de **hoofd tekst**. Als het dialoog venster **dynamische inhoud toevoegen van de apps en connectors in deze stroom** wordt geopend, selecteert u **verbergen** om het bestand te sluiten. In deze zelf studie gebruikt u geen dynamische inhoud.

      ![E-mail velden voor SMTP-verbindingen invullen](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Selecteer **Opslaan** om de SMTP-verbinding op te slaan.

1. Beschrijving Als u TLS wilt uitschakelen om verbinding te maken met uw e-mail provider en deze opnieuw wilt inschakelen, voert u de volgende stappen uit:

   1. Selecteer in het deel venster **logische app** onder **ontwikkelingsprogram Ma's** de optie **API-verbindingen**.

   1. Selecteer de SMTP-verbinding in de lijst met API-verbindingen.

   1. Selecteer in het deel venster **SMTP API Connection** onder **Algemeen** de optie **API-verbinding bewerken**.

   1. In het deel venster **API-verbinding bewerken** selecteert u **SSL inschakelen?**, voert u het wacht woord voor uw e-mail account opnieuw in en selecteert u **Opslaan**.

      ![Een SMTP-API-verbinding bewerken in uw logische app in de Azure Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Uw logische app is nu gereed voor het verwerken van temperatuur waarschuwingen uit de Service Bus wachtrij en het verzenden van meldingen naar uw e-mail account.

## <a name="test-the-logic-app"></a>Test de logische app

1. Start de client toepassing op het apparaat.

1. Als u een fysiek apparaat gebruikt, dient u zorgvuldig een verwarmings bron in de buurt te brengen tot de Tempe ratuur meer dan 30 graden C overschrijdt. Als u de online Simulator gebruikt, zal de client code wille keurige telemetriegegevens uitvoeren op berichten van meer dan 30 C.

1. U moet beginnen met het ontvangen van e-mail meldingen die worden verzonden door de logische app.

   > [!NOTE]
   > Uw e-mail serviceprovider moet mogelijk de identiteit van de afzender verifiëren om er zeker van te zijn dat u het e-mail bericht verzendt.

## <a name="next-steps"></a>Volgende stappen

U hebt een logische app gemaakt die uw IoT-hub en uw postvak verbindt voor temperatuur bewaking en meldingen.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]