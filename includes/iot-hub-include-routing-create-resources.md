---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76021196"
---
## <a name="create-base-resources"></a>Basisresources maken

Voordat u de berichtroutering kunt configureren, moet u een IoT-hub, een opslagaccount en een Service Bus-wachtrij maken. Deze resources kunnen worden gemaakt met behulp van een van de vier artikelen die beschikbaar zijn voor deel 1 van deze zelfstudie: Azure Portal, een Azure Resource Manager-sjabloon, de Azure CLI of Azure PowerShell.

Gebruik dezelfde resourcegroep en -locatie voor alle resources. Vervolgens kunt u alle resources aan het einde in één stap verwijderen door de resourcegroep te verwijderen.

Hieronder vindt u een overzicht van de stappen die in de volgende secties moeten worden uitgevoerd: 

1. Maak een [resourcegroep](../articles/azure-resource-manager/management/overview.md).

2. Maak een IoT Hub in de S1-laag. Voeg een consumentengroep toe aan uw IoT Hub. De consumentengroep wordt gebruikt door de Azure Stream Analytics bij het ophalen van gegevens.

   > [!NOTE]
   > U moet een IoT Hub in een betaalde laag gebruiken om deze zelfstudie te voltooien. Met de gratis laag kunt u slechts één eindpunt instellen, terwijl voor deze zelfstudie meerdere eindpunten zijn vereist.
   > 

3. Maak een standaard V1-opslagaccount met Standard_LRS replicatie.

4. Maak een Service Bus-naamruimte en -wachtrij.

5. Maak een apparaat-id voor het gesimuleerde apparaat dat berichten naar uw hub verzendt. Sla de sleutel op voor de testfase. (Als u een Resource Manager-sjabloon maakt, doet u dit na het implementeren van de sjabloon.)