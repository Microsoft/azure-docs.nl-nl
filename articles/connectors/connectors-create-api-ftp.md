---
title: Verbinding maken met de FTP-server
description: Taken en werk stromen automatiseren die bestanden op een FTP-server maken, bewaken en beheren met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999571"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>FTP-bestanden bewaken, maken en beheren met behulp van Azure Logic Apps

Met Azure Logic Apps en de FTP-connector kunt u geautomatiseerde taken en werk stromen maken om bestanden te maken, controleren, verzenden en ontvangen via uw account op een FTP-server, samen met andere acties, bijvoorbeeld:

* Controleren wanneer bestanden worden toegevoegd of gewijzigd.
* Bestanden downloaden, maken, kopiëren, bijwerken, weer geven en verwijderen.
* Bestands inhoud en meta gegevens ophalen.
* Haal archief naar mappen.

U kunt triggers gebruiken die reacties ontvangen van uw FTP-server en de uitvoer beschikbaar maken voor andere acties. U kunt acties uitvoeren in uw Logic apps gebruiken voor het beheren van bestanden op uw FTP-server. U kunt ook andere acties uitvoeren met de uitvoer van FTP-acties. Als u bijvoorbeeld regel matig bestanden van uw FTP-server ontvangt, kunt u e-mail over deze bestanden en hun inhoud verzenden met behulp van de Office 365 Outlook-Connector of de Outlook.com-connector. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Beperkingen

* De FTP-connector ondersteunt alleen expliciete FTP via TLS/SSL (FTPS) en is niet compatibel met impliciete FTPS.

* FTP-acties kunnen standaard bestanden lezen of schrijven die *50 MB of kleiner* zijn. Voor het afhandelen van bestanden die groter zijn dan 50 MB, worden met FTP-bewerkingen [bericht chunks](../logic-apps/logic-apps-handle-large-messages.md)ondersteund. De actie **Bestands inhoud ophalen** maakt impliciet gebruik van Chunking.

* FTP-Triggers bieden geen ondersteuning voor het delen van segmenten. Bij het aanvragen van bestands inhoud selecteren triggers alleen bestanden van 50 MB of kleiner. Als u bestanden groter dan 50 MB wilt ophalen, volgt u dit patroon:

  * Gebruik een FTP-trigger die bestands eigenschappen retourneert, bijvoorbeeld **Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen)**.

  * Volg de trigger met de actie **Bestands inhoud** voor FTP ophalen, die het volledige bestand leest en impliciet gebruikmaakt van Chunking.

* Als u een on-premises FTP-server hebt, kunt u overwegen om een [ISE (Integration service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) te maken of [Azure app service hybride verbindingen](../app-service/app-service-hybrid-connections.md)te gebruiken, waarmee u toegang kunt krijgen tot on-premises gegevens bronnen zonder een on-premises gegevens gateway te gebruiken.

## <a name="how-ftp-triggers-work"></a>Hoe FTP-triggers werken

FTP-triggers worden uitgevoerd door het FTP-bestands systeem te pollen en te zoeken naar een bestand dat sinds de laatste poll is gewijzigd. Met sommige hulpprogram ma's kunt u de tijds tempel behouden wanneer de bestanden worden gewijzigd. In deze gevallen moet u deze functie uitschakelen zodat de trigger kan werken. Hier volgen enkele algemene instellingen:

| SFTP-client | Bewerking |
|-------------|--------|
| WinSCP | Ga naar **Opties**  >  **voor keuren**  >  **overdracht**  >  **bewerken**  >  **behouden tijds tempel**  >  **uitschakelen** |
| FileZilla | Ga naar de **overdrachts**  >  **tijds tempels van overgebrachte bestanden**  >  **uitschakelen** |
|||

Wanneer een trigger een nieuw bestand vindt, controleert de trigger of het nieuwe bestand is voltooid en niet gedeeltelijk is geschreven. Een bestand kan bijvoorbeeld wijzigingen in voortgang hebben wanneer de trigger de bestands server controleert. Om te voor komen dat een gedeeltelijk geschreven bestand wordt geretourneerd, wordt door de trigger de tijds tempel voor het bestand met recente wijzigingen weer gegeven, maar wordt dat bestand niet direct geretourneerd. De trigger retourneert het bestand alleen wanneer de server opnieuw wordt gecontroleerd. Dit gedrag kan soms een vertraging veroorzaken die twee maal het polling interval van de trigger is.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Het adres en de account referenties van de FTP-hostserver

  De FTP-connector vereist dat de FTP-server toegankelijk is vanaf internet en in de *passieve* modus kan worden uitgevoerd. Met uw referenties kunt u een verbinding maken met uw logische app en toegang krijgen tot uw FTP-account.

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot uw FTP-account. [Maak een lege logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)om te beginnen met een FTP-trigger. Als u een FTP-actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="connect-to-ftp"></a>Verbinding maken met FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps.

1. Voer als filter toe voor lege logische apps in het zoekvak `ftp` . Selecteer de gewenste trigger in de lijst met **Triggers** .

   -of-

   Voor bestaande Logic apps, onder de laatste stap waar u een actie wilt toevoegen, selecteert u **nieuwe stap** en vervolgens **een actie toevoegen**. Voer in het zoekvak `ftp` als uw filter in. Selecteer in de lijst **acties** de gewenste actie.

   Als u een actie tussen stappen wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Geef uw verbindings gegevens op en selecteer **maken**.

1. Geef de gegevens voor de geselecteerde trigger of actie op en blijf door gaan met het bouwen van de werk stroom van uw logische app.

## <a name="examples"></a>Voorbeelden

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>FTP-trigger toevoegen

De trigger **Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen)** start een werk stroom voor logische apps wanneer de trigger detecteert dat een bestand wordt toegevoegd of gewijzigd op een FTP-server. U kunt bijvoorbeeld een voor waarde toevoegen waarmee de inhoud van het bestand wordt gecontroleerd en beslist of deze inhoud wordt opgehaald, op basis van de vraag of die inhoud voldoet aan een opgegeven voor waarde. Ten slotte kunt u een actie toevoegen waarmee de inhoud van het bestand wordt opgehaald en die inhoud in een andere map op de SFTP-server plaatsen.

U kunt deze trigger bijvoorbeeld gebruiken om een FTP-map te bewaken voor nieuwe bestanden die klant orders beschrijven. U kunt vervolgens een FTP-actie gebruiken, zoals **meta gegevens ophalen** om de eigenschappen voor dat nieuwe bestand op te halen, en vervolgens **Bestands inhoud ophalen** gebruiken om de inhoud van dat bestand op te halen voor verdere verwerking en deze order op te slaan in een Data Base voor orders.

Hier volgt een voor beeld waarin wordt getoond hoe u de trigger **Wanneer een bestand wordt toegevoegd of gewijzigd (alleen eigenschappen)** kunt gebruiken.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Voer als filter toe voor lege logische apps in het zoekvak `ftp` . Selecteer deze trigger onder de lijst triggers: **Wanneer een gearchiveerd of gewijzigd wordt toegevoegd (alleen eigenschappen)**

   ![Zoek en selecteer de FTP-trigger](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Geef de benodigde gegevens voor de verbinding op en selecteer vervolgens **maken**.

   Deze connector brengt standaard bestanden over in een tekst indeling. Als u bestanden wilt overdragen in binaire indeling, bijvoorbeeld wanneer en wanneer code ring wordt gebruikt, selecteert u **binair Trans Port**.

   ![Verbinding maken met de FTP-server](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Selecteer in het vak **map** het mappictogram zodat er een lijst wordt weer gegeven. Als u de map wilt vinden die u wilt controleren op nieuwe of bewerkte bestanden, selecteert u de pijl-rechts ( **>** ), bladert u naar de map en selecteert u de map.

   ![Zoeken en selecteren welke map u wilt bewaken](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   De geselecteerde map wordt weer gegeven in het vak **map** .

   ![De geselecteerde map wordt weer gegeven in de eigenschap ' folder '](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

Nu de logische app een trigger heeft, voegt u de acties toe die u wilt uitvoeren wanneer de logische app een nieuw of bewerkt bestand vindt. Voor dit voor beeld kunt u een FTP-actie toevoegen die de nieuwe of bijgewerkte inhoud ontvangt.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>FTP-actie toevoegen

Met de actie **meta gegevens van bestand ophalen** worden de eigenschappen opgehaald voor een bestand op uw FTP-server en de actie **Bestands inhoud ophalen** haalt de bestands inhoud op basis van de informatie over dat bestand op uw FTP-server. U kunt bijvoorbeeld de trigger uit het vorige voor beeld toevoegen en deze acties om de inhoud van het bestand op te halen nadat het bestand is toegevoegd of bewerkt.

1. Selecteer **nieuwe stap** onder de trigger of een andere actie.

1. Voer in het zoekvak `ftp` als uw filter in. Selecteer in de lijst acties deze actie: **meta gegevens van bestand ophalen**

   ![Selecteer de actie ' meta gegevens van bestand ophalen '](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Als u al een verbinding met uw FTP-server en account hebt, gaat u naar de volgende stap. Geef anders de benodigde gegevens op voor die verbinding en selecteer vervolgens **maken**.

   ![FTP-server verbinding maken](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Nadat de actie **meta gegevens van bestand ophalen** wordt weer gegeven, klikt u in het vak **bestand** zodat de lijst met dynamische inhoud wordt weer gegeven. U kunt nu eigenschappen selecteren voor de uitvoer van de vorige stappen. Selecteer in de lijst dynamische inhoud onder **Bestands metagegevens ophalen** de **lijst met bestands-id-** eigenschappen, die verwijst naar de verzameling waar het bestand is toegevoegd of bijgewerkt.

   ![Zoek en selecteer de eigenschap lijst met bestands-Id's](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   De **lijst met bestands-id-** eigenschappen wordt nu weer gegeven in het vak **bestand** .

   ![Geselecteerde eigenschap van de lijst met bestands-Id's](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Voeg deze FTP-actie nu toe: **Bestands inhoud ophalen**

   ![Zoek en selecteer de actie bestands inhoud ophalen](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Nadat de actie **Bestands inhoud ophalen** wordt weer gegeven, klikt u in het vak **bestand** zodat de lijst met dynamische inhoud wordt weer gegeven. U kunt nu eigenschappen selecteren voor de uitvoer van de vorige stappen. Selecteer in de lijst dynamische inhoud onder **Get File meta data** de eigenschap **id** , die verwijst naar het bestand dat is toegevoegd of bijgewerkt.

   ![Zoek en selecteer de eigenschap ID](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   De eigenschap **id** wordt nu weer gegeven in het vak **bestand** .

   ![Geselecteerde id-eigenschap](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Sla uw logische app op.

## <a name="test-your-logic-app"></a>Uw logische app testen

Als u wilt controleren of uw werk stroom de verwachte inhoud retourneert, voegt u een andere actie toe waarmee u de inhoud van het geüploade of bijgewerkte bestand verzendt.

1. Voeg onder de actie **Bestands inhoud ophalen** een actie toe waarmee u de inhoud van het bestand kunt verzenden. In dit voor beeld wordt de actie **een E-mail verzenden** toegevoegd voor Office 365 Outlook.

   ![Een actie voor het verzenden van e-mail toevoegen](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Nadat de actie wordt weer gegeven, geeft u de informatie op en voegt u de eigenschappen toe die u wilt testen. Voeg bijvoorbeeld de eigenschap **Bestands inhoud** toe, die wordt weer gegeven in de lijst met dynamische inhoud nadat u in de sectie **Bestands inhoud ophalen** de optie **meer weer geven** hebt geselecteerd.

   ![Informatie opgeven over de e-mailactie](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Sla uw logische app op. Als u de logische app wilt uitvoeren en activeren, selecteert u **uitvoeren** op de werk balk en vervolgens voegt u een bestand toe aan de FTP-map die nu door uw logische app wordt bewaakt.

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/ftpconnector/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)

