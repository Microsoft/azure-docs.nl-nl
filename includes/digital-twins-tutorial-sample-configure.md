---
author: baanders
description: inclusief bestand voor Azure Digital Twins-zelfstudies, het voorbeeldproject configureren
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 67a2799a93141ad84f458642d8499a58784cc19c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463767"
---
## <a name="configure-the-sample-project"></a>Het voorbeeldproject configureren

Stel vervolgens een voorbeeldclienttoepassing in die gaat communiceren met uw instantie van Azure Digital Twins.

Ga op uw computer naar een bestand dat u eerder hebt gedownload van [*Azure Digital Twins-end-to-endvoorbeelden*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (en pak het uit als u dat nog niet hebt gedaan).

Ga in de map naar _AdtSampleApp_. Open _**AdtE2ESample.sln**_ in Visual Studio 2019. 

Selecteer in Visual Studio _SampleClientApp > **bestand appSettings.json**_ om het te openen in het bewerkingsvenster. Dit bestand fungeert als een vooraf ingesteld JSON-bestand met de benodigde configuratievariabelen om het project uit te voeren.

In de hoofd tekst van het bestand wijzigt `instanceUrl` u de URL van uw Azure Digital apparaatdubbels-exemplaar *hostName* (door **_https://_** toe te voegen vóór de *hostnaam*, zoals hieronder wordt weer gegeven).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Sla het bestand op en sluit het. 

Configureer vervolgens het bestand *appsettings.json* dat moet worden gekopieerd naar de uitvoermap wanneer u de *SampleClientApp* compileert. U doet dit door met de rechter muisknop *op* het bestandappsettings.jste selecteren en **Eigenschappen** te kiezen. Zoek in de **Eigenschappen** controle naar de eigenschap *kopiëren naar uitvoer Directory* . Wijzig de waarde die u wilt **kopiëren indien nieuwer** als deze nog niet is ingesteld.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Fragment van een Visual Studio-venster waarin het deelvenster Solution Explorer wordt weergegeven en appsettings.json is gemarkeerd, en het deelvenster Eigenschappen waarin de eigenschap Naar uitvoermap kopiëren is ingesteld op Kopiëren indien nieuwer" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Houd het project _**AdtE2ESample**_ geopend in Visual Studio om dit in de rest van de zelfstudie te gebruiken.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
