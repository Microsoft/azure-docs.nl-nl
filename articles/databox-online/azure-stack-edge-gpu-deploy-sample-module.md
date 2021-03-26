---
title: De GPU-module implementeren op uw Azure Stack Edge Pro GPU-apparaat | Microsoft Docs
description: Hierin wordt beschreven hoe u reken kracht kunt inschakelen en uw Azure Stack Edge Pro-apparaat kunt instellen met behulp van de lokale gebruikers interface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: af44912edf3ce98ceb71bd34388543f7652c2181
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568457"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>Een IoT-module met GPU-functionaliteit implementeren op Azure Stack Edge Pro GPU-apparaat

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

In dit artikel wordt beschreven hoe u een IoT Edge module met GPU-functionaliteit implementeert op uw Azure Stack Edge Pro GPU-apparaat. 

In dit artikel leert u het volgende:
  - Bereid Azure Stack Edge Pro voor op het uitvoeren van een GPU-module.
  - Down load en installeer voorbeeld code uit een Git-opslag plaats.
  - Bouw de oplossing en Genereer een implementatie manifest.
  - Implementeer de oplossing op Azure Stack Edge Pro-apparaat.
  - De module-uitvoer bewaken.


## <a name="about-sample-module"></a>Over de voorbeeld module

De GPU-voorbeeld module in dit artikel bevat PyTorch-en tensor flow-benchmark code voor CPU-voor-GPU.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, controleert u of u over het volgende beschikt:

- U hebt toegang tot een op GPU ingeschakelde apparaat met 1 knoop punt Azure Stack Edge Pro. Dit apparaat wordt geactiveerd met een resource in Azure. Zie [het apparaat activeren](azure-stack-edge-gpu-deploy-activate.md).
- U hebt Compute geconfigureerd op dit apparaat. Volg de stappen in [zelf studie: Configure Compute op uw Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-configure-compute.md).
- Een Azure Container Registry (ACR). Ga naar de Blade **toegangs sleutels** en noteer de aanmeldings server, de gebruikers naam en het wacht woord van ACR. Ga voor meer informatie naar [Quick Start: een persoonlijk container register maken met behulp van de Azure Portal](../container-registry/container-registry-get-started-portal.md#create-a-container-registry).
- De volgende ontwikkel bronnen op een Windows-client:
    - [Azure CLI 2,0 of hoger](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Mogelijk moet u een account maken om de software te downloaden en te installeren.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IOT Edge-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).    
    - [Python-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - PIP voor het installeren van Python-pakketten (doorgaans opgenomen in uw python-installatie)

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

1. Ga naar de [intelligente Edge-patronen van Azure in azure-voor beelden](https://github.com/azure-samples/azure-intelligent-edge-patterns). Kloon of down load het zip-bestand voor code. 

    ![Zip-bestand downloaden](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Pak de bestanden uit vanuit de zip. U kunt de voor beelden ook klonen.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Module maken en implementeren

1. Open de map **GpuReferenceModules** in Visual Studio code.

    ![Open GPUReferenceModules in VS code](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. Open de *deployment.template.jsop* en Identificeer de para meters die verwijzen naar het container register. In het volgende bestand worden CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD en CONTAINER_REGISTRY_NAME gebruikt.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Maak een nieuw bestand. Vul de waarden in voor de container register parameters (gebruik de in de vorige stap geïdentificeerde) als volgt: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    Een voor beeld *. env* -bestand wordt hieronder weer gegeven:
    
    ![Een. env-bestand maken en opslaan](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Sla het bestand op als *. env* in de map **SampleSolution** .

5. Als u zich wilt aanmelden bij docker, geeft u de volgende opdracht op in de Visual Studio code Integrated Terminal. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Ga naar de sectie **toegangs sleutels** van het container register in de Azure Portal. Kopieer en gebruik de register naam, het wacht woord en de aanmeldings server.

    ![Toegangs sleutels in het container register](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    Nadat de referenties zijn opgegeven, slaagt de aanmelding.

    ![Geslaagde aanmelding](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Push uw installatie kopie naar uw Azure container Registry. In de VS code Explorer selecteert u en klikt u met de rechter muisknop op de **deployment.template.jsin** het bestand en selecteert u vervolgens **Build and push IOT Edge Solution**. 

    ![IoT Edge-oplossingen bouwen en pushen](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Als de extensie python en python niet is geïnstalleerd, worden deze geïnstalleerd wanneer u de oplossing bouwt en pusht. Dit resulteert echter in langere build-tijden. 

    Zodra deze stap is voltooid, ziet u de module in het container register.

    ![Module in container Registry](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Als u een implementatie manifest wilt maken, klikt u met de rechter muisknop op de **deployment.template.jsop** en selecteert u **IOT Edge implementatie manifest genereren**. 

    ![IoT Edge-implementatiemanifest genereren](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    De melding informeert u over het pad waar het implementatie manifest is gegenereerd. Het manifest is het `deployment.amd64.json` bestand dat in de map **config** is gegenereerd. 

8. Selecteer de map **deployment.amd64.js** in het bestand **config** en kies vervolgens **implementatie maken voor één apparaat**. Gebruik niet de **deployment.template.jsvoor** het bestand. 

    ![Implementatie voor één apparaat maken](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    In het venster **uitvoer** ziet u een bericht dat de implementatie is geslaagd.

    ![Implementatie is voltooid in uitvoer](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>De module bewaken  

1. Voer in het opdrachtenpalet van VS Code de opdracht **Azure IoT Hub: Select IoT Hub** uit.

2. Kies het abonnement en de IoT-hub met het IoT Edge-apparaat dat u wilt configureren. In dit geval selecteert u het abonnement dat is gebruikt om het Azure Stack Edge Pro-apparaat te implementeren en selecteert u het IoT Edge apparaat dat u hebt gemaakt voor uw Azure Stack Edge Pro-apparaat. Dit gebeurt wanneer u Compute configureert via de Azure Portal in de eerdere stappen.

3. Vouw in de VS code Explorer de sectie Azure IoT Hub uit. Onder **apparaten** wordt het IOT edge apparaat weer geven dat overeenkomt met uw Azure stack Edge Pro-apparaat. 

    1. Selecteer dat apparaat, klik met de rechter muisknop en selecteer **controle van ingebouwd gebeurtenis begin punt starten**.
  
        ![Bewaking starten](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Ga naar **apparaten > modules** en u ziet dat uw **GPU-module** wordt uitgevoerd.

        ![Module in IoT Hub](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. De VS code-terminal moet ook de IoT Hub gebeurtenissen weer geven als de bewakings uitvoer voor uw Azure Stack Edge Pro-apparaat.

        ![Bewakings uitvoer](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        U kunt zien dat de tijd die nodig is om dezelfde set bewerkingen uit te voeren (5000 herhalingen van vorm transformatie) door GPU veel minder is dan voor CPU.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u [GPU kunt configureren voor het gebruik van een module](./azure-stack-edge-gpu-configure-gpu-modules.md).