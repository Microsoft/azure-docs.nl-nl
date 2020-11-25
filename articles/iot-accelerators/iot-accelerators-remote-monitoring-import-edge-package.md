---
title: Pakket voor het importeren van oplossingen voor externe bewaking-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een IoT Edge-pakket importeert in de oplossings versneller voor externe controle
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012285"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Een IoT Edge-pakket importeren in de verbetering voor de externe bewakingsoplossing

Een implementatie manifest definieert de modules die moeten worden geïmplementeerd op een IoT Edge apparaat. In dit artikel wordt ervan uitgegaan dat een ontwikkelaar in uw organisatie al een implementatie manifest heeft gemaakt. Voor meer informatie over hoe een ontwikkelaar een manifest maakt, raadpleegt u een van de volgende IoT Edge procedures:

- [Azure IoT Edge modules implementeren vanuit de Azure Portal](../iot-edge/how-to-deploy-modules-portal.md)
- [Azure IoT Edge-modules implementeren met Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Azure IoT Edge-modules implementeren vanuit Visual Studio code](../iot-edge/how-to-deploy-modules-vscode.md)

Een ontwikkelaar maakt en test een implementatie manifest in een ontwikkel omgeving. Wanneer u klaar bent, kunt u het manifest importeren in uw oplossings versneller voor externe controle.

## <a name="export-a-manifest"></a>Een manifest exporteren

Gebruik de Azure Portal om het implementatie manifest te exporteren vanuit uw ontwikkel omgeving:

1. Navigeer in het Azure Portal naar de IoT-hub die u gebruikt om uw IoT Edge apparaten te ontwikkelen en te testen. Klik op **IOT Edge** en **IOT Edge implementaties**: ![ IOT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Klik op de implementatie met de implementatie configuratie die u wilt gebruiken. De pagina **Implementatie Details** wordt weer gegeven: ![ Details van IOT Edge-implementatie](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Klik op **downloaden IOT Edge manifest**:  ![ implementatie manifest downloaden](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Sla het JSON-bestand op als een lokaal bestand met de naam **deploymentmanifest.jsop**.

U hebt nu een bestand dat het implementatie manifest bevat. In de volgende sectie importeert u dit manifest als een pakket in de oplossing voor controle op afstand.

## <a name="import-a-package"></a>Een pakket importeren

Volg de onderstaande stappen om een Edge-implementatie manifest te importeren als een pakket in uw oplossing:

1. Ga naar de pagina **pakketten** op de pagina externe bewaking webinterface:  ![ pakketten](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Klik op **+ nieuw pakket**, kies **Edge-manifest** als het pakket type en klik op **Bladeren** om dedeploymentmanifest.jste selecteren **voor** het bestand dat u in de vorige sectie hebt opgeslagen:  ![ manifest selecteren](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Klik op **uploaden** om het pakket toe te voegen aan uw oplossing voor controle op afstand:  ![ geüpload pakket](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

U hebt nu een IoT Edge implementatie manifest geüpload als een pakket. Op de pagina **implementaties** kunt u dit pakket implementeren op uw verbonden IOT edge-apparaten.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u modules kunt implementeren op een IoT Edge apparaat vanuit de oplossing voor externe controle, is de volgende stap meer informatie over [IOT Edge](../iot-edge/about-iot-edge.md).
