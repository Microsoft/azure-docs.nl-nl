---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89302125"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>IoT Edge CI/CD controleren met pijp lijnen voor Build en release

Als u een build-taak wilt activeren, kunt u een door voeren naar de opslag plaats van de bron code pushen of hand matig activeren. In deze sectie kunt u hand matig de CI/CD-pijp lijn activeren om te testen dat deze werkt. Controleer vervolgens of de implementatie slaagt.

1. Selecteer in het menu van het linkerdeel venster **pijp lijnen** en open de build-pijp lijn die u aan het begin van dit artikel hebt gemaakt.

2. U kunt een build-taak in uw build-pijp lijn activeren door de knop **pijp lijn uitvoeren** in de rechter bovenhoek te selecteren.

    ![Uw build-pijp lijn hand matig activeren met behulp van de knop pijp lijn uitvoeren](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Controleer de instellingen voor het uitvoeren van de **pijp lijn** . Selecteer vervolgens **uitvoeren**.

    ![Geef runtime-pijplijn opties op en selecteer uitvoeren.](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Selecteer **Agent taak 1** om de voortgang van de uitvoering te bekijken. U kunt de logboeken van de uitvoer van de taak bekijken door de taak te selecteren. 

    ![De logboek uitvoer van de taak controleren](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Als de build-pijp lijn met succes is voltooid, wordt een release-naar- **ontwikkelings** fase geactiveerd. Met de geslaagde **dev** -versie wordt IOT Edge-implementatie gemaakt voor doel apparaten van IOT Edge.

    ![Release to dev](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Klik op **ontwikkel** fase om release logboeken te bekijken.

    ![Release-logboeken](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)