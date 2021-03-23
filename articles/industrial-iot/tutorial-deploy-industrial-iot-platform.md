---
title: Het Azure Industrial IoT-platform implementeren
description: In deze zelf studie leert u hoe u het IIoT-platform implementeert.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787746"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Zelf studie: het Azure Industrial IoT-platform implementeren

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Over de belangrijkste onderdelen van het IIoT-platform
> * Over de verschillende installatie typen
> * Het industrieel IoT-platform implementeren

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement moet worden gemaakt
- [Git](https://git-scm.com/downloads) downloaden
- Voor de registraties van de Azure Active Directory (AAD) die voor de verificatie worden gebruikt, is globale beheerder, toepassings beheerder of Cloud toepassings beheerder rechten vereist om toestemming te geven voor de beheerder van de hele Tenant (zie hieronder voor meer opties)
- De ondersteunde besturings systemen voor implementatie zijn Windows, Linux en Mac
- IoT Edge ondersteunt Windows 10 IoT Enter prise LTSC en Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Belangrijkste onderdelen

- Minimale afhankelijkheden: IoT Hub, Cosmos DB, Service Bus, Event hub, Key Vault, opslag
- Standaard afhankelijkheden: minimale + seingevings service, registratie van AAD-apps, Device Provisioning Service, Time Series Insights, werkmap, Log Analytics, Application Insights
- Micro Services: App Service plan, App Service
- Gebruikers interface (Web-app): App Service plan (gedeeld met micro Services), App Service
- Simulatie: virtuele machine, virtueel netwerk, IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>Installatie typen

- Minimum: minimale afhankelijkheden
- Lokaal: minimum en de standaard afhankelijkheden
- Services: lokale en micro Services
- Simulatie: minimale afhankelijkheden en de simulatie onderdelen
- App: Services en de gebruikers interface
- Alle (standaard): app en simulatie

## <a name="deployment"></a>Implementatie

1. Kloon de opslag plaats vanaf de opdracht prompt of terminal om aan de slag te gaan met de implementatie van het IIoT-platform.

    Git kloon- https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. Start de begeleide implementatie, het script verzamelt de vereiste gegevens, zoals Azure-account,-abonnement, doel resource en groeps-en toepassings naam.

In Windows:
    ```
    .\deploy
    ```

Op Linux of Mac:
    ```
    ./deploy.sh
    ```

3. De micro Services en de gebruikers interface zijn webtoepassingen waarvoor authenticatie is vereist. hiervoor zijn hiervoor drie app-registraties in de AAD vereist. Als de vereiste rechten ontbreken, zijn er twee mogelijke oplossingen:

- Vraag de AAD-beheerder om de beheerder toestemming voor de hele Tenant te verlenen voor de toepassing
- Een AAD-beheerder kan de AAD-toepassingen maken. De map Deploy/scripts bevat het Aad-register.ps1 script om de AAD-registratie los van de implementatie uit te voeren. De uitvoer van het script is een bestand dat de relevante informatie bevat die moet worden gebruikt als onderdeel van de implementatie en moet worden door gegeven aan het deploy.ps1 script in dezelfde map met behulp van het argument-aadConfig.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

Het platform kan worden geïmplementeerd in [Azure Kubernetes service (AKS)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md) voor productie-implementaties waarvoor fase ring, terugdraai actie, schaal baarheid en tolerantie zijn vereist.

Referentie
- [Azure industrieel IoT-platform implementeren](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Alles-in-één implementeren](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Platform implementeren in AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Volgende stappen
Nu u het IIoT-platform hebt geïmplementeerd, kunt u meer informatie over het aanpassen van de configuratie van de onderdelen:

> [!div class="nextstepaction"]
> [De configuratie van de onderdelen aanpassen](tutorial-configure-industrial-iot-components.md)
