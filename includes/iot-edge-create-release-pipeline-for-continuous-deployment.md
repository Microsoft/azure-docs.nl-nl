---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 8009d98ddbfa778cf5f357248ecd943b810e06e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803424"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Een release pijplijn maken voor continue implementatie

In deze sectie maakt u een release pijplijn die is geconfigureerd om automatisch te worden uitgevoerd wanneer uw build-pijp lijn artefacten overneemt en de implementatie Logboeken in azure-pijp lijnen worden weer gegeven.

Maak een nieuwe pijp lijn en voeg een nieuwe fase toe:

1. Kies op het tabblad **releases** onder **pijp lijnen** de optie **+ nieuwe pijp lijn**. Als u al release pijplijnen hebt, kiest u de knop **+ Nieuw** en selecteert u **+ nieuwe release pijplijn**.  

    ![Een release pijplijn toevoegen met behulp van de knop + nieuwe pijp lijn](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. Wanneer u wordt gevraagd om een sjabloon te selecteren, kiest u om te beginnen met een **lege taak**.

    ![Beginnen met een lege taak voor uw release pijplijn](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. Uw nieuwe release pijplijn wordt geïnitialiseerd met één fase, de naam **fase 1**. Wijzig de naam fase 1 in **dev** en behandel dit als een continue implementatie pijplijn voor uw ontwikkel omgeving. Doorgaans hebben doorlopende implementatie pijplijnen meerdere fasen, waaronder **ontwikkeling**, **fase ring** en **productie**. U kunt verschillende namen gebruiken en meer maken op basis van uw DevOps-prak tijken. Sluit het venster met fase details zodra de naam ervan is gewijzigd.

   U kunt ook de naam van de release pijplijn wijzigen door de tekst ' nieuwe release pijplijn ' bovenaan te selecteren.

4. Koppel de release aan de constructie-artefacten die worden gepubliceerd door de build-pijp lijn. Klik op **toevoegen** in het gebied artefacten.

   ![Klik op toevoegen in het gebied artefacten van de interface](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. Selecteer op de **pagina een artefact toevoegen** de optie **Build** als **bron type**. Kies het project en de build-pijp lijn die u hebt gemaakt. Als u wilt, kunt u de **bron alias** wijzigen in een duidelijkere beschrijving. Selecteer vervolgens **toevoegen**.

   ![Selecteer op de pagina een artefact toevoegen de optie toevoegen om het artefact te maken](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Open de artefact triggers en selecteer de schakel optie om de continue implementatie trigger in te scha kelen. Er wordt nu een nieuwe release gemaakt wanneer een nieuwe build beschikbaar is.

   ![De artefact Triggers openen en scha kelen om de continue implementatie trigger in te scha kelen](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. De **ontwikkelings** fase is vooraf geconfigureerd met één taak en nul taken. Selecteer in het menu pijp lijn **taken** en kies vervolgens de **ontwikkelings** fase. Selecteer de **Agent taak** en wijzig de **weergave naam** in **QA**. U kunt details over de agent taak configureren, maar de implementatie taak is ingevoelig voor een platform, zodat u elke **agent specificatie** in de gekozen **agent groep** kunt gebruiken.

   ![De taken voor uw ontwikkelings fase weer geven op het tabblad taken](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. Selecteer op de QA-taak het plus teken ( **+** ) om twee taken toe te voegen. Zoek en Voeg twee keer **Azure IOT Edge** toe.

9. Selecteer de eerste **Azure IOT Edge** taak en configureer deze met de volgende waarden:

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | De weergave naam wordt automatisch bijgewerkt wanneer het actie veld wordt gewijzigd. |
    | Bewerking | Selecteer `Generate deployment manifest`. |
    | .template.jsvoor bestand | Geef het pad op: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . Het pad wordt gepubliceerd vanuit een build-pijp lijn. |
    | Standaard platform | Selecteer het juiste besturings systeem voor uw modules op basis van uw doel-IoT Edge apparaat. |
    | Uitvoerpad| Plaats het pad `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Dit pad is het uiteindelijke manifest bestand voor de implementatie van IoT Edge. |

    Deze configuraties helpt bij het vervangen van de installatie kopie-Url's van de module in het `deployment.template.json` bestand. Het **genereren van het implementatie manifest** helpt ook de variabelen te vervangen door de exacte waarde die u in het bestand hebt gedefinieerd `deployment.template.json` . In VS/VS-code geeft u de daad werkelijke waarde op in een `.env` bestand. In azure-pijp lijnen stelt u de waarde in op het tabblad **release pijplijn variabelen** . Ga naar het tabblad **variabelen** en configureer de naam en waarde als volgt:

    * **ACR_ADDRESS**: de waarde van de Azure container Registry- **aanmeldings server** . U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal.
    * **ACR_PASSWORD**: uw Azure container Registry wacht woord.
    * **ACR_USER**: de gebruikers naam van uw Azure container Registry.

    Als u andere variabelen in uw project hebt, kunt u de naam en waarde op dit tabblad opgeven. Het **manifest voor het genereren van implementaties** kan alleen de variabelen herkennen die in de `${VARIABLE}` smaak zijn. Zorg ervoor dat u deze versie in uw `*.template.json` bestanden gebruikt.
    
    ```json-interactive
    "registryCredentials": {
      "<ACR name>": { // Your Azure Container Registry **Registry name** value
        "username": "${ACR_USER}",
        "password": "${ACR_PASSWORD}",
        "address": "${ACR_ADDRESS}"
      }
    }
    ```
    
    ![Configureer de variabelen voor uw release pijplijn op het tabblad variabelen](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. Selecteer de tweede **Azure IOT Edge** taak en configureer deze met de volgende waarden:

    | Parameter | Beschrijving |
    | --- | --- |
    | Weergavenaam | De weergave naam wordt automatisch bijgewerkt wanneer het actie veld wordt gewijzigd. |
    | Bewerking | Selecteer `Deploy to IoT Edge devices`. |
    | Implementatie bestand | Plaats het pad `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Dit pad is het bestand IoT Edge implementatie manifest bestand. |
    | Azure-abonnement | Selecteer het abonnement dat uw IoT Hub bevat.|
    | IoT Hub naam | Selecteer uw IoT-hub.|
    | Kies één of meerdere apparaten | Kies of u de release pijplijn wilt implementeren op een of meer apparaten. Als u op één apparaat implementeert, voert u de **IOT edge apparaat-id** in. Als u implementeert op meerdere apparaten, geeft u de **voor waarde voor** het apparaat doel op. De doel voorwaarde is een filter dat overeenkomt met een set IoT Edge apparaten in IoT Hub. Als u Device Tags als voor waarde wilt gebruiken, moet u de labels van de bijbehorende apparaten bijwerken met IoT Hub apparaat twee. Werk de **IOT Edge implementatie-id** en **IOT Edge implementatie prioriteit** bij in de geavanceerde instellingen. Zie [inzicht IOT Edge automatische implementaties](../articles/iot-edge/module-deployment-monitoring.md)voor meer informatie over het maken van een implementatie voor meerdere apparaten. |
    | Apparaat-ID of doel voorwaarde | Geef, afhankelijk van de vorige selectie, een apparaat-ID of [doel voorwaarde](../articles/iot-edge/module-deployment-monitoring.md#target-condition) op om op meerdere apparaten te implementeren. |
    | Geavanceerd | Geef voor de IoT Edge implementatie-ID op `$(System.TeamProject)-$(Release.EnvironmentName)` . Met deze variabele wordt de naam van het project en de release toegewezen aan uw IoT Edge-implementatie-ID. |
    

    Als uw taak betrekking heeft op het gebruik van een installatie kopie die zich in een privé-docker-vertrouwd REGI ster bevindt dat niet zichtbaar is voor de open bare Cloud, kunt u de omgevings variabele **SKIP_MODULE_IMAGE_VALIDATION** instellen op `true` om installatie kopieën over te slaan. 

    ![Azure IoT Edge-taken toevoegen voor uw ontwikkel fase](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. Selecteer **Opslaan** om uw wijzigingen op te slaan in de nieuwe release pijplijn. Ga terug naar de pijplijn weergave door het tabblad **pijp lijn** te selecteren in het menu.
