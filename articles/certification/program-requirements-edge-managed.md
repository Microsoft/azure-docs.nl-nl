---
title: Door de Edge beheerde certificerings vereisten
description: Vereisten voor het Edge-beheerde certificerings programma
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Managed Certification Requirements
ms.service: certification
ms.openlocfilehash: 744f81d0544a765f60793ece1aa539c6c37e39cd
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969185"
---
# <a name="azure-certification-edge-managed"></a>Beheerde Azure-certificerings Edge 

Dit document bevat een overzicht van de apparaatspecifieke mogelijkheden die worden weer gegeven in de Azure Certified Device Catalog. Een mogelijkheid is een enkel kenmerk van het apparaat dat het apparaat kan beschrijven. 

## <a name="program-purpose"></a>Programma doel

Door de rand beheerde certificering, een incrementele certificering buiten de basis lijn certificering van Azure gecertificeerde apparaten. Edge Managed focuss op de standaarden voor Apparaatbeheer voor apparaten die zijn verbonden met Azure en valideert de IoT Edge runtime compatibiliteit voor module-implementatie en-beheer. (Voorheen werd dit programma geïdentificeerd als het IoT Edge-certificerings programma.) 

Door de rand beheerde certificering valideert IoT Edge runtime compatibiliteit voor module-implementatie en-beheer. Dit programma biedt vertrouwen in het beheer van met Azure verbonden IoT-apparaten.

## <a name="requirements"></a>Vereisten

Voor de Edge Managed certificering is vereist dat alle vereisten van het [Azure Certified Device Baseline Program](.\program-requirements-azure-certified-device.md).

**DPS: het doel van de test is om te controleren of het apparaat wordt geïmplementeerd en ondersteunt IoT Hub Device Provisioning Service met een van de drie Attestation-methoden**

| **Naam**                | AzureReady. DPS                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Ignite (in preview-versie)                                                |
| **Van toepassing op**          | Elk apparaat                                      |
| **Besturingssysteem**                  | Agnostisch                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | AICS valideert de ondersteunings DPS voor Device codes. **1.** De gebruiker moet een van de Attestation-methoden (X. 509, TPM en SAS-sleutel) selecteren. **2.** Afhankelijk van de Attestation-methode moet de gebruiker overeenkomstige actie ondernemen, zoals **een)** upload X. 509-certificaat naar AICS Managed DPS Scope **b)** Implementeer SAS-sleutel of goedkeurings sleutel in het apparaat. **3.** Vervolgens wordt de gebruiker op de knop Connect geklikt om verbinding te maken met AICS beheerde IoT Hub via DPS                                                    |
| **Bronnen**           |                                                      |
| **Aanbevolen door Azure:**     | N.v.t.                                                    |

## <a name="iot-edge"></a>IoT Edge

**Edge-runtime bestaat: het doel van de test is om te controleren of het apparaat IoT Edge runtime bevat ($edgehub en $edgeagent) correct functioneren.**

| **Naam**                | EdgeManaged.EdgeRT                                               |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu verkrijgbaar                                                          |
| **Van toepassing op**          | IoT Edge-apparaat                                                   |
| **Besturingssysteem**                  | [Tier1-en Tier2-besturings systeem](../iot-edge/support.md)                                                     |
| **Validatie type**     | Geautomatiseerd                                                    |
| **Validatie**          | AICS valideert de implementatie functie van de geïnstalleerde IoT Edge RT. **1.** De gebruiker moet een specifiek besturings systeem opgeven (besturings systeem niet in de lijst met Tier1/2 wordt niet geaccepteerd) **2.** AICS genereert de configuratie. yaml en implementeert canonieke [gesimuleerde tijdelijke sensor rand module](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-iot.simulated-temperature-sensor?tab=Overview) **3.** AICS valideert dat docker-compatibel container subsysteem (Moby) op het apparaat **4** is geïnstalleerd. Het test resultaat wordt bepaald op basis van de geslaagde implementatie van de gesimuleerde Temp-sensor rand module en de functionaliteit van docker-compatibel container subsysteem                                                    |
| **Bronnen**           | **a)** [AICS blog](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [certificerings stappen](./overview.md) (alle aanvullende bronnen), **c-** [vereisten](./program-requirements-azure-certified-device.md) |
| **Aanbevolen door Azure:**     | N.v.t.                                                    |

### <a name="capability-template"></a>Sjabloon voor mogelijkheden:

**IoT Edge Easy Setup: het doel van de test is om ervoor te zorgen dat IoT Edge apparaat eenvoudig kan worden ingesteld en gevalideerd IoT Edge runtime vooraf is geïnstalleerd tijdens de validatie van een fysiek apparaat**

| **Naam**                | EdgeManaged.PhysicalDevice                                             |
| ----------------------- | ------------------------------------------------------------ |
| **Beschik baarheid doel** | Nu beschikbaar (momenteel geblokkeerd door COVID-19)                                            |
| **Van toepassing op**          | IoT Edge-apparaat                                                   |
| **Besturingssysteem**                  | [Tier1-en Tier2-besturings systeem](../iot-edge/support.md)                                                     |
| **Validatie type**     | Hand matig/Lab gecontroleerd                                                    |
| **Validatie**          | OEM moet het fysieke apparaat naar IoT-beheer (HCL) verzenden. Er wordt door HCL hand matige validatie uitgevoerd op het fysieke apparaat om het volgende te controleren: **1.** EdgeRT maakt gebruik van Moby subsysteem (toegestane herdistributies versie). Geen docker **2.** Kies de module nieuwste rand om de geschiktheid voor het implementeren van Edge te valideren.                                                     |
| **Bronnen**           | **a)** [AICS blog](https://azure.microsoft.com/en-in/blog/expanding-azure-iot-certification-service-to-support-azure-iot-edge-device-certification/), **b)** [vereisten voor](./program-requirements-azure-certified-device.md) [certificering](./overview.md) , **c** |
| **Aanbevolen door Azure:**     | N.v.t.                                                    |