---
title: Wat zijn de Azure IoT Central-gezondheids zorg oplossingen | Microsoft Docs
description: Meer informatie over het ontwikkelen van gezondheidszorgoplossingen met behulp van Azure IoT Central-toepassingssjablonen.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: e8a72195f0fcacce2c994e8770157b05b65d70ee
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833147"
---
# <a name="what-are-the-iot-central-healthcare-solutions"></a>Wat zijn de IoT Central gezondheids zorg oplossingen?

Meer informatie over het ontwikkelen van gezondheidszorgoplossingen met behulp van Azure IoT Central-toepassingssjablonen.

## <a name="what-is-continuous-patient-monitoring-template"></a>Wat is een sjabloon voor continue patiëntbewaking?

Binnen de IoT-gezondheidszorg is continue patiëntbewaking een van de belangrijkste middelen om de kans op een tweede ziekenhuisopname te voorkomen, chronische ziekten effectiever te beheren en de resultaten van patiënten te verbeteren. Continue patiëntbewaking kan in twee hoofdcategorieën worden onderverdeeld:

1. **Interne patiëntbewaking**: Met behulp van medische wearables en andere ziekenhuisapparaten kunnen zorgteams de vitale functies en medische omstandigheden van patiënten controleren zonder dat een verpleegster een patiënt meerdere keren per dag hoeft te controleren. Dankzij meldingen weten zorgteams wanneer een patiënt onmiddellijk aandacht nodig heeft, zodat ze hun tijd efficiënt kunnen indelen.
1. **Externe patiëntbewaking**: Door het gebruik van medische wearables en patiëntgerapporteerde uitkomsten (PRO's) om patiënten buiten het ziekenhuis in de gaten te houden, kan de kans op een tweede ziekenhuisopname worden verkleind. Gegevens van chronisch zieke patiënten en revaliderende patiënten kunnen worden verzameld om ervoor te zorgen dat patiënten zich houden aan zorgplannen en dat waarschuwingen over de achteruitgaande gezondheid van patiënten bij zorgteams terechtkomen voordat de toestand van patiënten kritiek wordt.

Deze toepassingssjabloon kan worden gebruikt voor het ontwikkelen van oplossingen voor beide categorieën van continue patiëntbewaking. Enkele voordelen:

* Verbind de verschillende soorten medische wearables naadloos met een IoT Central-exemplaar.
* Bewaak en beheer de apparaten om ervoor te zorgen dat ze in orde blijven.
* Maak aangepaste regels rond apparaatgegevens om de juiste waarschuwingen te activeren.
* Exporteer gegevens over de gezondheid van uw patiënten naar de Azure API for FHIR, een compatibele gegevensopslag.
* Exporteer de geaggregeerde inzichten naar bestaande of nieuwe zakelijke toepassingen.

>[!div class="mx-imgBorder"] 
>![CPM-dashboard](media/in-patient-dashboard.png)

## <a name="next-steps"></a>Volgende stappen

Om een oplossing voor continue patiëntbewaking te ontwikkelen, gaat u als volgt te werk:

* [De toepassingssjabloon implementeren](tutorial-continuous-patient-monitoring.md)
* [Een voorbeeldarchitectuur bekijken](concept-continuous-patient-monitoring-architecture.md)