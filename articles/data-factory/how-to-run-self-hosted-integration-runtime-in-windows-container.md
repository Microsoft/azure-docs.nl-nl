---
title: Self-Hosted Integration Runtime uitvoeren in Windows-container
description: Meer informatie over het uitvoeren van Self-Hosted Integration Runtime in Windows-container.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: e34e1f589442be41e2a4eac3ac893f377675e4b5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379502"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Self-Hosted Integration Runtime uitvoeren in Windows-container

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt uitgelegd hoe u Self-Hosted Integration Runtime uitvoert in Windows-container.
Azure Data Factory leveren de officiële Windows-container ondersteuning van Self-Hosted Integration Runtime. U kunt de docker build-bron code downloaden en het bouwen en uitvoeren van het proces combi neren in uw eigen continue leverings pijplijn. 

## <a name="prerequisites"></a>Vereisten 
- [Windows-container vereisten](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker-versie 2,3 en hoger 
- Self-Hosted Integration Runtime versie 4.11.7512.1 en hoger 
## <a name="get-started"></a>Aan de slag 
1.  Docker installeren en Windows-container inschakelen 
2.  De broncode vanuit https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container downloaden
3.  Down load de meest recente versie SHIR in de map SHIR 
4.  Open uw map in de shell: 
```console
cd "yourFolderPath"
```

5.  De Windows docker-installatie kopie bouwen: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Docker-container uitvoeren: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY is verplicht voor deze opdracht. NODE_NAME, ENABLE_HA en HA_PORT optioneel zijn. Als u de waarde niet instelt, gebruikt de opdracht standaard waarden. De standaard waarde van ENABLE_HA is onwaar en HA_PORT is 8060.

## <a name="container-health-check"></a>Container status controle 
Na 120 seconden een opstart periode wordt de Health Checker elke 30 seconden periodiek uitgevoerd. Hiermee wordt de status van de IR-status aan de container engine verstrekt. 

## <a name="limitations"></a>Beperkingen
De onderstaande functies worden momenteel niet ondersteund voor het uitvoeren van Self-Hosted Integration Runtime in Windows-container:
- HTTP-proxy 
- Versleutelde communicatie tussen knoop punten en TLS/SSL-certificaat 
- Back-up genereren en importeren 
- Daemon-service 
- Automatisch bijwerken 

### <a name="next-steps"></a>Volgende stappen
- Bekijk de [concepten van de integratie-runtime in azure Data Factory](./concepts-integration-runtime.md).
- Meer informatie over [het maken van een zelf-hostende Integration runtime in de Azure Portal](./create-self-hosted-integration-runtime.md).