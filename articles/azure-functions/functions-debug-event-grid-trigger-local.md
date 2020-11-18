---
title: Azure Functions Event Grid lokale fout opsporing
description: Meer informatie over Azure Functions van lokale fouten die zijn geactiveerd door een Event Grid gebeurtenis
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: be05d237d2799404c3fd8b5733464e23eeb49aa3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833058"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Lokaal opsporen van fouten in Azure-functies voor Event Grid Trigger

In dit artikel wordt beschreven hoe u fouten opspoort in een lokale functie die een Azure Event Grid gebeurtenis veroorzaakt door een opslag account. 

## <a name="prerequisites"></a>Vereisten

- Een bestaande functie-app maken of gebruiken
- Een bestaand opslag account maken of gebruiken
- [Ngrok](https://ngrok.com/) downloaden zodat Azure uw lokale functie kan aanroepen

## <a name="create-a-new-function"></a>Een nieuwe functie maken

Open de functie-app in Visual Studio en klik met de rechter muisknop op de naam van het project in de Solution Explorer en vervolgens op **> nieuwe Azure-functie toevoegen**.

Selecteer in het venster *nieuwe Azure-functie* de optie **Event grid trigger** en klik op **OK**.

![Nieuwe functie maken](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Zodra de functie is gemaakt, opent u het code bestand en kopieert u de URL naar de bovenkant van het bestand. Deze locatie wordt gebruikt bij het configureren van de Event Grid trigger.

![Locatie kopiëren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Stel vervolgens een onderbrekings punt in op de regel die begint met `log.LogInformation` .

![Onderbrekings punt instellen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Druk vervolgens **op F5** om een foutopsporingssessie te starten.

## <a name="allow-azure-to-call-your-local-function"></a>Azure toestaan uw lokale functie aan te roepen

Als u een fout wilt opsporen in een functie die wordt opgespoord op uw computer, moet u een manier inschakelen waarop Azure met uw lokale functie vanuit de cloud kan communiceren.

Het hulp programma [ngrok](https://ngrok.com/) biedt een manier om Azure de functie die wordt uitgevoerd op uw computer aan te roepen. Start *ngrok* met de volgende opdracht:

```bash
ngrok http -host-header=localhost 7071
```
Wanneer het hulp programma is ingesteld, moet het opdracht venster er ongeveer uitzien als in de volgende scherm afbeelding:

![Scherm opname van de opdracht prompt na het starten van het hulp programma ' ngrok '.](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopieer de **https** -URL die wordt gegenereerd wanneer *ngrok* wordt uitgevoerd. Deze waarde wordt gebruikt bij het configureren van het gebeurtenis eind punt gebeurtenis raster.

## <a name="add-a-storage-event"></a>Een opslag gebeurtenis toevoegen

Open de Azure Portal en navigeer naar een opslag account en klik op de optie **gebeurtenissen** .

![Gebeurtenis van opslag account toevoegen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Klik in het venster *gebeurtenissen* op de knop **gebeurtenis abonnement** . Klik in het venster *gebeurtenis abonnement* op de vervolg keuzelijst *type eind punt* en selecteer **webhook**.

![Type abonnement selecteren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Zodra het eindpunt type is geconfigureerd, klikt u op **Selecteer een eind punt** om de eindpunt waarde te configureren.

![Type eind punt selecteren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

De *abonnee-eindpunt* waarde bestaat uit drie verschillende waarden. Het voor voegsel is de HTTPS-URL die door *ngrok* wordt gegenereerd. De rest van de URL is afkomstig van de URL die in het functie code bestand is gevonden, waarbij de functie naam aan het einde wordt toegevoegd. Met ingang van de URL van het code bestand van de functie wordt de *ngrok* -URL vervangen `http://localhost:7071` en wordt de functie naam vervangen `{functionname}` .

In de volgende scherm afbeelding ziet u hoe de uiteindelijke URL eruit moet zien:

![Eindpunt selectie](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Zodra u de juiste waarde hebt ingevoerd, klikt u op **selectie bevestigen**.

> [!IMPORTANT]
> Telkens wanneer u *ngrok* start, wordt de HTTPS-URL opnieuw gegenereerd en wordt de waarde gewijzigd. Daarom moet u telkens wanneer u uw functie beschikbaar maakt, een nieuw gebeurtenis abonnement maken op Azure via *ngrok*.

## <a name="upload-a-file"></a>Bestand uploaden

U kunt nu een bestand uploaden naar uw opslag account om een Event Grid gebeurtenis te activeren, zodat uw lokale functie kan worden afgehandeld. 

Open [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) en maak verbinding met het opslag account. 

- **BLOB-containers** uitvouwen 
- Klik met de rechter muisknop en selecteer **BLOB-container maken**.
- De container **test** een naam
- De *test* container selecteren
- Klik op de knop **uploaden**
- Klik op **bestanden uploaden**
- Een bestand selecteren en dit uploaden naar de BLOB-container

## <a name="debug-the-function"></a>Fouten opsporen in de functie

Zodra de Event Grid een nieuw bestand detecteert dat naar de opslag container is geüpload, wordt het breek punt in uw lokale functie bereikt.

![Ngrok starten](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in dit artikel zijn gemaakt, wilt opschonen, verwijdert u de **test** container in uw opslag account.

## <a name="next-steps"></a>Volgende stappen

- [Formaat van geüploade afbeeldingen automatisch wijzigen met Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Event Grid trigger voor Azure Functions](./functions-bindings-event-grid.md)
