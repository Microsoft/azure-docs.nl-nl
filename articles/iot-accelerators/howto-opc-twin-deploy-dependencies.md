---
title: OPC-dubbele Cloud afhankelijkheden implementeren in azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de OPC dubbele Azure-afhankelijkheden implementeert die nodig zijn om lokale ontwikkeling en fout opsporing uit te voeren.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 42024fc506de7befed7c44ebcc410756b6f43a35
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646184"
---
# <a name="deploying-dependencies-for-local-development"></a>Afhankelijkheden implementeren voor lokale ontwikkeling

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

In dit artikel wordt uitgelegd hoe u alleen de services van het Azure-platform implementeert die nodig zijn om lokale ontwikkeling en fout opsporing uit te voeren.   Aan het einde hebt u een resource groep geïmplementeerd die alles bevat wat u nodig hebt voor lokale ontwikkeling en fout opsporing.

## <a name="deploy-azure-platform-services"></a>Azure-platform services implementeren

1. Zorg ervoor dat Power shell-en [AzureRM Power](/powershell/azure/azurerm/install-azurerm-ps) shell-extensies zijn geïnstalleerd.  Open een opdracht prompt of Terminal en voer het volgende uit:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Volg de aanwijzingen om een naam toe te wijzen aan de resource groep voor uw implementatie.  Het script implementeert alleen de afhankelijkheden van deze resource groep in uw Azure-abonnement, maar niet de micro Services.  Met het script wordt ook een toepassing geregistreerd in azure AD.  Dit is nodig om verificatie op basis van OAUTH te ondersteunen.  De implementatie kan enkele minuten duren.

3. Zodra het script is voltooid, kunt u selecteren om het. env-bestand op te slaan.  Het. env-omgevings bestand is het configuratie bestand van alle services en hulpprogram ma's die u wilt uitvoeren op uw ontwikkel computer.  

## <a name="troubleshooting-deployment-failures"></a>Implementatie fouten oplossen

### <a name="resource-group-name"></a>Naam van de resourcegroep

Zorg ervoor dat u een korte en eenvoudige naam voor de resource groep gebruikt.  De naam wordt ook gebruikt om resources te noemen, omdat deze moeten voldoen aan de vereisten voor resource naamgeving.  

### <a name="azure-active-directory-ad-registration"></a>Registratie van Azure Active Directory (AD)

Het implementatie script probeert Azure AD-toepassingen te registreren in azure AD.  Afhankelijk van uw rechten voor de geselecteerde Azure AD-Tenant kan dit mislukken. Er zijn drie opties:

1. Als u een Azure AD-Tenant uit een lijst met tenants hebt gekozen, start u het script opnieuw en kiest u een ander in de lijst.
2. U kunt ook een persoonlijke Azure AD-Tenant implementeren, het script opnieuw starten en selecteren om het te gebruiken.
3. Door gaan zonder verificatie.  Omdat u uw micro services lokaal uitvoert, is dit acceptabel, maar worden productie omgevingen niet nagebootst.  

## <a name="next-steps"></a>Volgende stappen

Nu u OPC dubbele Services hebt geïmplementeerd voor een bestaand project, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van OPC-dubbele modules](howto-opc-twin-deploy-modules.md)