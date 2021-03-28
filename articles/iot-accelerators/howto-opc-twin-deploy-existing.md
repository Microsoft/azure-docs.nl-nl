---
title: Een OPC-dubbele module implementeren in een bestaand Azure-project | Microsoft Docs
description: In dit artikel wordt beschreven hoe u OPC twee kunt implementeren in een bestaand project. U kunt ook leren hoe u implementatie fouten oplost.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4535818898459f1eb183026629bc338524c1efd9
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646178"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>OPC van twee tot een bestaand project implementeren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

De OPC-dubbele module wordt uitgevoerd op IoT Edge en biedt verschillende Edge-services aan de OPC-dubbele en register Services.

De OPC-dubbele micro Service vereenvoudigt de communicatie tussen de fabrieks operatoren en de OPC UA-server apparaten op de fabriek via een OPC-dubbele IoT Edge-module. De micro service biedt OPC UA-Services (bladeren, lezen, schrijven en uitvoeren) via de REST API. 

De OPC UA Device Registry micro service biedt toegang tot geregistreerde OPC UA-toepassingen en hun eind punten. Opera tors en beheerders kunnen nieuwe OPC UA-toepassingen registreren en de registratie ervan ongedaan maken, met inbegrip van hun eind punten. Naast het beheer van toepassingen en eind punten catalogeert de register service ook geregistreerde OPC-dubbele IoT Edge modules. Met de service-API kunt u de functionaliteit van de Edge-module beheren, bijvoorbeeld het starten of stoppen van de server detectie (Scan Services) of het activeren van nieuwe apparaatdubbels die toegankelijk zijn via de OPC-dubbele micro service.

De kern van de module is de identiteit van de Super Visor. De Super Visor beheert eind punt dubbele, die overeenkomt met OPC UA-server eindpunten die worden geactiveerd met behulp van de bijbehorende OPC UA Registry API. Dit eind punt apparaatdubbels Vertaal OPC UA JSON ontvangen van de OPC-dubbele micro service die in de Cloud wordt uitgevoerd in OPC UA binaire berichten, die via een stateful veilig kanaal naar het beheerde eind punt worden verzonden. De Super Visor biedt ook detectie services die detectie gebeurtenissen voor apparaten verzenden naar de OPC UA Device-voorbereidings service voor verwerking, waarbij deze gebeurtenissen resulteren in updates van het OPC UA-REGI ster.  In dit artikel wordt beschreven hoe u de OPC-dubbele module implementeert in een bestaand project.

> [!NOTE]
> Zie de GitHub- [opslag plaats](https://github.com/Azure/azure-iiot-opc-twin-module)voor meer informatie over de implementatie details en instructies.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat Power shell-en [AzureRM Power](/powershell/azure/azurerm/install-azurerm-ps) shell-extensies zijn geïnstalleerd. Als u dit nog niet hebt gedaan, kloont u deze GitHub-opslag plaats. Voer de volgende opdrachten uit in PowerShell:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Industriële IoT-services implementeren in azure

1. Voer in uw Power shell-sessie de volgende handelingen uit:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Volg de aanwijzingen om een naam toe te wijzen aan de resource groep van de implementatie en een naam aan de website.   Het script implementeert de micro Services en hun Azure platform-afhankelijkheden in de resource groep in uw Azure-abonnement.  Met het script wordt ook een toepassing geregistreerd in de Tenant van uw Azure Active Directory (AAD) om verificatie op basis van OAUTH te ondersteunen.  De implementatie kan enkele minuten duren.  Een voor beeld van wat u ziet zodra de oplossing is geïmplementeerd:

   ![Industrieel IoT OPC-dubbele implementatie voor bestaand project](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   De uitvoer bevat de URL van het open bare eind punt. 

3. Zodra het script is voltooid, selecteert u of u het bestand wilt opslaan `.env` .  U hebt het `.env` omgevings bestand nodig als u verbinding wilt maken met het Cloud-eind punt met behulp van hulpprogram ma's zoals de console of modules implementeren voor ontwikkeling en fout opsporing.

## <a name="troubleshooting-deployment-failures"></a>Implementatie fouten oplossen

### <a name="resource-group-name"></a>Naam van de resourcegroep

Zorg ervoor dat u een korte en eenvoudige naam voor de resource groep gebruikt.  De naam wordt ook gebruikt om resources te noemen, omdat deze moeten voldoen aan de vereisten voor resource naamgeving.  

### <a name="website-name-already-in-use"></a>De naam van de website wordt al gebruikt

Het is mogelijk dat de naam van de website al in gebruik is.  Als u deze fout uitvoert, moet u een andere toepassings naam gebruiken.

### <a name="azure-active-directory-aad-registration"></a>Registratie van Azure Active Directory (AAD)

Het implementatie script probeert twee AAD-toepassingen in Azure Active Directory te registreren.  Afhankelijk van uw rechten voor de geselecteerde AAD-Tenant kan de implementatie mislukken. Er zijn twee opties:

* Als u een AAD-Tenant hebt gekozen uit een lijst met tenants, start u het script opnieuw en kiest u een ander account in de lijst.
* U kunt ook een persoonlijke AAD-Tenant in een ander abonnement implementeren, het script opnieuw starten en selecteren om het te gebruiken.

> [!WARNING]
> NOOIT door gaan zonder verificatie.  Als u ervoor kiest om dit te doen, heeft iedereen toegang tot uw OPC-twee eind punten via internet niet-geverifieerd.   U kunt altijd de [optie ' lokale implementatie](howto-opc-twin-deploy-dependencies.md) ' kiezen om de banden te starten.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Een alles-in-één industriële IoT-Services-demo implementeren

In plaats van alleen de services en afhankelijkheden kunt u ook een alles-in-één-demo implementeren.  De alles-in-één-demo bevat drie OPC UA-servers, de OPC-dubbele module, alle micro Services en een voor beeld-webtoepassing.  Het is bedoeld voor demonstratie doeleinden.

1. Zorg ervoor dat u beschikt over een kloon van de opslag plaats (zie hierboven). Open een Power shell-prompt in de hoofdmap van de opslag plaats en voer het volgende uit:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Volg de aanwijzingen om een nieuwe naam toe te wijzen aan de resource groep en een naam aan de website.  Zodra het script is geïmplementeerd, wordt de URL van het eind punt van de webtoepassing weer gegeven.

## <a name="deployment-script-options"></a>Opties voor implementatie script

Het script heeft de volgende para meters:

```powershell
-type
```

Het type implementatie (VM, lokaal, demo)

```powershell
-resourceGroupName
```

Dit kan de naam van een bestaande of een nieuwe resource groep zijn.

```powershell
-subscriptionId
```

Optioneel, het abonnement-ID waar resources worden geïmplementeerd.

```powershell
-subscriptionName
```

Of de naam van het abonnement.

```powershell
-resourceGroupLocation
```

Optioneel, een locatie van een resource groep. Als deze is opgegeven, wordt geprobeerd een nieuwe resource groep te maken op deze locatie.

```powershell
-aadApplicationName
```

Een naam voor de AAD-toepassing die u wilt registreren.

```powershell
-tenantId
```

AAD-Tenant die moet worden gebruikt.

```powershell
-credentials
```

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC twee kunt implementeren in een bestaand project, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Beveiligde communicatie van de OPC UA-client en de OPC UA PLC](howto-opc-vault-secure.md)