---
ms.openlocfilehash: 3f92bae608284c8b619be34a0e08f15e831bf88e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101751111"
---
Vouw bij de stap [Het IoT Edge-implementatiemanifest genereren en implementeren](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) in Visual Studio Code het knooppunt **lva-sample-device** onder **AZURE IOT HUB** uit (in de sectie linksonder). U ziet dat de volgende modules zijn geïmplementeerd:

* De Live Video Analytics-module met de naam `lvaEdge`
* De `rtspsim`-module, die een RTSP-server simuleert en fungeert als de bron van een live-videofeed

  ![Modules](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Bij de bovenstaande stappen wordt ervan uitgegaan dat u gebruikmaakt van de virtuele machine die is gemaakt door het installatie script. Als u in plaats daarvan uw eigen edge-apparaat gebruikt, gaat u naar uw edge-apparaat en voert u de volgende opdrachten uit met **beheerders rechten** om het voor beeld-video bestand dat voor deze Quick Start wordt gebruikt, op te halen en op te slaan:  

```
mkdir /home/lvaedgeuser/samples
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
