---
author: baanders
description: inclusief bestand voor Azure Digital Twins-zelfstudies, het voorbeeldproject configureren
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: f5b332415f1abf7d4c1002bdd4f3bfcef12f1267
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023249"
---
## <a name="configure-the-sample-project"></a>Het voorbeeldproject configureren

Stel vervolgens een voorbeeldclienttoepassing in die gaat communiceren met uw instantie van Azure Digital Twins.

Ga op uw computer naar een bestand dat u eerder hebt gedownload van [*Azure Digital Twins-end-to-endvoorbeelden*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (en pak het uit als u dat nog niet hebt gedaan).

Ga in de map naar _AdtSampleApp_. Open _**AdtE2ESample.sln**_ in Visual Studio 2019. 

Selecteer in Visual Studio _SampleClientApp > **bestand appSettings.json**_ om het te openen in het bewerkingsvenster. Dit bestand fungeert als een vooraf ingesteld JSON-bestand met de benodigde configuratievariabelen om het project uit te voeren.

Wijzig `instanceUrl` in de URL van de *hostName* van uw Azure Digital Twins-exemplaar (met *https://* ervoor, zoals hieronder weergegeven).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Sla het bestand op en sluit het. 

Configureer vervolgens het bestand *appsettings.json* dat moet worden gekopieerd naar de uitvoermap wanneer u de *SampleClientApp* compileert. Selecteer met de rechtermuisknop het bestand *appsettings.json* en kies *Eigenschappen*. In het controlevenster *Eigenschappen* wijzigt u de waarde van de eigenschap *Naar uitvoermap kopiëren* in *Kopiëren indien nieuwer*.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Fragment van een Visual Studio-venster waarin het deelvenster Solution Explorer wordt weergegeven en appsettings.json is gemarkeerd, en het deelvenster Eigenschappen waarin de eigenschap Naar uitvoermap kopiëren is ingesteld op Kopiëren indien nieuwer" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Houd het project _**AdtE2ESample**_ geopend in Visual Studio om dit in de rest van de zelfstudie te gebruiken.

