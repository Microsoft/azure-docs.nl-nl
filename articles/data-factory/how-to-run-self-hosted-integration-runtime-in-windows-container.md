---
title: Een Self-Hosted Integration Runtime uitvoeren in een Windows-container
description: Meer informatie over het uitvoeren van Self-Hosted Integration Runtime in een Windows-container.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 2423d6bd29d893f9a27749dcc2b6d2af8a12e941
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478128"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Een Self-Hosted Integration Runtime uitvoeren in een Windows-container

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt uitgelegd hoe u een Self-Hosted Integration Runtime in een Windows-container.
Azure Data Factory de officiële windows-containerondersteuning van Self-Hosted Integration Runtime. U kunt de broncode van de docker-build downloaden en het bouwproces en het lopende proces combineren in uw eigen pijplijn voor continue levering. 

## <a name="prerequisites"></a>Vereisten 
- [Vereisten voor Windows-containers](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker versie 2.3 en hoger 
- Self-Hosted Integration Runtime versie 5.2.7713.1 en hoger 
## <a name="get-started"></a>Aan de slag 
1.  Docker installeren en Windows-container inschakelen 
2.  De broncode vanuit https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container downloaden
3.  Download de nieuwste versie van SHIR in de map 'SHIR' 
4.  Open de map in de shell: 
```console
cd "yourFolderPath"
```

5.  Bouw de Windows Docker-afbeelding: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Voer de Docker-container uit: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true -e HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY is verplicht voor deze opdracht. NODE_NAME zijn ENABLE_HA en HA_PORT optioneel. Als u de waarde niet in stelt, gebruikt de opdracht standaardwaarden. De standaardwaarde van ENABLE_HA is false en HA_PORT is 8060.

## <a name="container-health-check"></a>Statuscontrole van container 
Na de opstartperiode van 120 seconden wordt de statuscontrole periodiek om de 30 seconden uitgevoerd. De status van de IR wordt aan de container-engine verstrekt. 

## <a name="limitations"></a>Beperkingen
Op dit moment bieden we geen ondersteuning voor onderstaande functies bij het uitvoeren Self-Hosted Integration Runtime in een Windows-container:
- HTTP-proxy 
- Versleutelde communicatie tussen knooppunt en TLS/SSL-certificaat 
- Back-up genereren en importeren 
- Daemon-service 
- Automatisch bijwerken 

### <a name="next-steps"></a>Volgende stappen
- Bekijk [integratieruntimeconcepten in Azure Data Factory](./concepts-integration-runtime.md).
- Meer informatie over het [maken van een zelf-hostende Integration Runtime in de Azure Portal](./create-self-hosted-integration-runtime.md).