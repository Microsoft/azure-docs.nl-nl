---
title: Controle lijst voor de pre-certificering van IoT Edge module aanbiedingen in azure Marketplace
description: Meer informatie over de specifieke certificerings vereisten voor het publiceren van IoT Edge module aanbiedingen in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 31c19f62f0328fca05562eaa2f19b7a79c0f3e15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105562695"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Controle lijst voor de pre-certificering voor IoT Edge-modules

> [!NOTE]
> De meeste aanbevolen uitgevers nemen deze controle lijst door en valideren de functionaliteit van de module voordat ze worden verzonden voor certificering. Hiermee wordt het certificerings proces versneld door de nood zaak van wijzigingen te verminderen en opnieuw in te dienen.

## <a name="validation-of-image"></a>Validatie van de installatie kopie

Zodra de installatie kopie van de rand module gereed is voor verzen ding, voert u deze stappen uit om ervoor te zorgen dat de installatie kopie werkt zoals micro soft verwacht.

### <a name="steps-to-perform-in-the-azure-portal"></a>Stappen voor het uitvoeren van de Azure Portal

1. Open de [Azure Portal](https://partner.microsoft.com/).
1. Maak een resource groep.
1. Een IoT Hub maken.
1. Maak een IoT Edge-apparaat.
1. Kopieer de connection string en sla deze op in Klad blok.
1. Selecteer de set **modules op edge-apparaat gemaakt**.
1. Voeg de ACR-details toe waarin de meest recente versie van de installatie kopie zich bevindt.
1. Selecteer **IOT Edge module toevoegen** en geef het volgende op:
    - De afbeeldings-URI in module-instelling
    - De omgevings variabele (hetzelfde als wat is toegevoegd in het partner centrum)
    - De container maken opties (hetzelfde als wat is toegevoegd in het partner centrum)
    - De dubbele instelling van de module (hetzelfde als wat is toegevoegd in het partner centrum)
1. Routes toevoegen (hetzelfde als wat is toegevoegd in het partner centrum).
1. Selecteer **Controleren + maken**.

Edge-modules worden geïmplementeerd op het apparaat Edge dat is gemaakt in Azure.

### <a name="steps-to-perform-on-the-device"></a>Stappen om uit te voeren op het apparaat

#### <a name="device-details"></a>Apparaatgegevens

Het certificerings team gebruikt de volgende hardware om installatie kopieën op verschillende architecturen te valideren:

- Voor x64-installatie kopieën, een Azure VM met configuratie grootte als Standard D2s V3 met Ubuntu Server 18.04/Ubuntu Server 16,04.
- Voor Azure Resource Manager 32-installatie kopieën, een Raspberry Pi 3-model B.
- Voor Azure Resource Manager 64 installatie kopieën, een NVIDIA Jetson Nano 4 GB.

#### <a name="steps"></a>Stappen

1. Controleer of de apparaten/VM die zijn gemaakt toegankelijk zijn via Putty.
1. Down load [IOT Edge runtime](../iot-edge/how-to-install-iot-edge.md) op het apparaat.
1. Werk de connection string die u in stap 5 hebt gekopieerd, bij naar het bestand config. yaml.
1. Start de module Edge opnieuw met `sudo systemctl restart iotedge` .
1. Controleer of de module is geïmplementeerd op het apparaat `sudo iotedge list` . deze moet de status wordt uitgevoerd hebben.
1. Zorg ervoor dat er `sudo iotedge logs “Module Name“ -f` geen fouten zijn opgetreden in de logboeken van de module die is geïmplementeerd. Als er bekende fouten zijn, beschrijft u dit in de Partner Center- **opmerkingen voor de revisor voordat u** de aanbieding verzendt.

## <a name="metadata-validation"></a>Validatie van meta gegevens

Controleer het volgende:

- De nieuwste tag wordt vermeld in het partner centrum en de Azure Container Registry.
- Minimale hardwarevereisten worden toegevoegd in de beschrijving van de aanbieding.
- De gebruikers naam en het wacht woord van het Azure container Registry worden bijgewerkt en toegevoegd in het partner centrum.
- Nauw keurigheid van de gewenste **dubbele eigenschap,** *indien van toepassing*.
- Nauw keurigheid van de gewenste **omgevings variabelen** , *indien van toepassing*.
- Nauw keurigheid van de gewenste **Opties voor maken** , *indien van toepassing*.
- Connection string voor lead beheer is aanwezig.
- Privacybeleid aanwezig
- Gebruiksvoorwaarden aanwezig
- Ondersteunde IoT Edge apparaat-koppeling toevoegen vanuit [Azure IOT-apparaatinstantie](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 

## <a name="next-steps"></a>Volgende stappen

- [Modules implementeren vanuit de commerciële Marketplace](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [De Edge-module publiceren in het partner centrum](./partner-center-portal/azure-iot-edge-module-creation.md)
- [IoT Edge module implementeren](../iot-edge/quickstart-linux.md)