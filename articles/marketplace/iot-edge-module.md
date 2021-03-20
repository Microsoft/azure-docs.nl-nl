---
title: Azure Marketplace IoT Edge-module aanbiedingen
description: Meer informatie over het publiceren van IoT Edge module-aanbiedingen in azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/18/2020
ms.openlocfilehash: 90267f2b75b7b9c1e77a45d7e3faa4b0bf6dd63a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91708540"
---
# <a name="iot-edge-modules"></a>IoT Edge-modules

Het [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) -platform wordt ondersteund door Microsoft Azure.  Met dit platform kunnen gebruikers Cloud werkbelastingen implementeren die rechtstreeks op IoT-apparaten worden uitgevoerd.  Een IoT Edge module kan offline workloads uitvoeren en gegevens analyse lokaal doen. Met dit type aanbieding kunt u band breedte besparen, lokale en gevoelige gegevens beveiligen en een reactie tijd van lage latentie bieden.  U hebt nu de mogelijkheid om te profiteren van deze vooraf ontwikkelde werk belastingen. Tot nu toe zijn er slechts enkele oplossingen van de eerste partij beschikbaar.  U moest de tijd en resources investeren in het bouwen van uw eigen aangepaste IoT-oplossingen.

Met [IOT Edge modules in de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)hebben we nu één doel voor uitgevers om hun oplossingen beschikbaar te maken en te verkopen aan de IOT-doel groep. IoT-ontwikkel aars kunnen uiteindelijk mogelijkheden vinden en kopen om de ontwikkeling van oplossingen te versnellen.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Belangrijkste voor delen van IoT Edge modules in azure Marketplace

| **Voor uitgevers**    | **Voor klanten (IoT-ontwikkel aars)**  |
| :------------------- | :-------------------|
| Bereik miljoenen ontwikkel aars die IoT Edge-oplossingen willen bouwen en implementeren.  | Stel een IoT Edge oplossing met het vertrouwen van het gebruik van beveiligde en geteste onderdelen. |
| Publiceer één keer en voer uit op alle IoT Edge hardware die containers ondersteunt. | Verminder de tijd tot de markt door 1ste en van derden IoT Edge modules te zoeken en te implementeren voor specifieke behoeften. |
| Geld verdienen met flexibele facturerings opties <ul> <li> Maak en til uw eigen licentie (BYOL). </li> </ul> | Maak aankopen met uw favoriete facturerings modellen. <ul> <li> Maak en til uw eigen licentie (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Wat is een IoT Edge module?

Met Azure IoT Edge kunt u bedrijfs logica op de rand in de vorm van modules implementeren en beheren. Azure IoT Edge modules zijn de kleinste reken eenheden die worden beheerd door IoT Edge en kunnen micro soft-Services (zoals Azure Stream Analytics), services van derden of uw eigen oplossing-specifieke code bevatten. Zie [Azure IOT Edge modules begrijpen](../iot-edge/iot-edge-modules.md)voor meer informatie over IOT Edge-modules.

**Wat is het verschil tussen een type container aanbieding en een IoT Edge module-aanbod type?**

Het IoT Edge-module aanbod type is een specifiek type container dat wordt uitgevoerd op een IoT Edge-apparaat. Het wordt geleverd met de standaard configuratie-instellingen die in de IoT Edge context worden uitgevoerd en maakt optioneel gebruik van de IoT Edge module SDK om te worden geïntegreerd met de IoT Edge runtime.

## <a name="publishing-your-iot-edge-module"></a>Uw IoT Edge-module publiceren

**De online winkel rechts selecteren**

IoT Edge modules worden alleen gepubliceerd op de Azure Marketplace. AppSource is niet van toepassing. Zie [uw publicatie optie bepalen](determine-your-listing-type.md)voor meer informatie over de verschillen tussen online winkels.

**Factureringsopties**

De Marketplace ondersteunt momenteel **gratis** en **BYOL-facturerings opties (uw eigen licentie)** voor IOT Edge modules.

### <a name="publishing-options"></a>Publicatie opties

In alle gevallen moeten IoT Edge modules de optie voor het publiceren van **Transact** selecteren.  Zie [een publicatie optie kiezen](determine-your-listing-type.md) voor meer informatie over publicatie opties.  

## <a name="eligibility-criteria"></a>Geschiktheids criteria

Alle voor waarden van de Microsoft Azure Marketplace overeenkomsten en beleids regels zijn van toepassing op IoT Edge module aanbiedingen.  Daarnaast zijn er vereisten en technische vereisten voor IoT Edge modules.  

### <a name="prerequisites"></a>Vereisten

Als u een IoT Edge module naar Azure Marketplace wilt publiceren, moet u aan de volgende vereisten voldoen:

- Toegang tot het partner centrum. Zie [een commercieel Marketplace-account maken in het partner centrum](partner-center-portal/create-account.md)voor meer informatie.
- Host uw IoT Edge-module in een Azure Container Registry.
- De meta gegevens van uw IoT Edge-module gereed maken, zoals (niet-limitatieve lijst):
    - Een titel
    - Een beschrijving (in HTML-indeling)
    - Een logo afbeelding (in grootten van 48 x 48 (optioneel), 90 x 90 (optioneel) en van 216 x 216 tot 350 x 350 px, allemaal in PNG-indeling)
    - Een gebruiks voorwaarden en privacybeleid
    - Standaard module configuratie (route, dubbele gewenste eigenschappen, createOptions, omgevings variabelen)
    - Documentatie
    - Contactpersonen voor ondersteuning

### <a name="technical-requirements"></a>Technische vereisten

De belangrijkste technische vereisten voor een IoT Edge module, zodat IT kan worden gecertificeerd en gepubliceerd op de Azure Marketplace, worden beschreven in de [technische activa van uw IOT Edge module voorbereiden](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="next-steps"></a>Volgende stappen

- Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
- [Maak een IOT Edge module-aanbieding in het](./partner-center-portal/azure-iot-edge-module-creation.md) partner centrum.
