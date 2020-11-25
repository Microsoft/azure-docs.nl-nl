---
title: Veelgestelde vragen over Solution Accelerator voor externe controle-Azure | Microsoft Docs
description: In dit artikel worden de veelgestelde vragen over de Accelerators voor externe controle oplossingen beantwoord.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 64391d7f5a7b1a295be7e404d27e5cbe8c691eb2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995944"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>Veelgestelde vragen over oplossings versneller voor externe controle

Zie ook de algemene [Veelgestelde vragen](iot-accelerators-faq.md).

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Wat kost het om de nieuwe oplossing voor externe controle in te richten?

De nieuwe oplossings versneller biedt twee implementatie opties:

* Een *basis* optie die is ontworpen voor ontwikkel aars die op zoek zijn naar lagere ontwikkelings kosten of klanten die op zoek zijn naar een demonstratie of concept.
* Een *standaard* optie die is ontworpen voor bedrijven die een infra structuur willen implementeren die gereed is voor productie.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Hoe kan ik ervoor zorgen dat mijn kosten actief blijven terwijl ik mijn oplossing ontwikkel?

Naast het leveren van twee gedifferentieerde implementaties, heeft de nieuwe oplossing voor controle op afstand een instelling om alle gesimuleerde apparaten op aanvraag in of uit te scha kelen. Door de simulatie uit te scha kelen, vermindert u de gegevens die in de oplossing zijn opgenomen en dus de totale kosten.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>Wat is het verschil tussen de opties basis en standaard implementatie? Hoe kan ik kiezen tussen de twee implementatie opties?

Elke implementatie optie reageert op verschillende behoeften. De basis implementatie is ontworpen om aan de slag te gaan en een haalbaarheids test en kleine Pilots te ontwikkelen. Het biedt een gestroomlijnde architectuur met de mini maal benodigde bronnen en lagere kosten. De standaard implementatie is ontworpen voor het bouwen en aanpassen van een oplossing die gereed is voor productie, en biedt een implementatie met de benodigde elementen om dat te realiseren. Voor betrouw baarheid en schaal worden toepassings micro Services gebouwd als docker-containers en geïmplementeerd met behulp van een Orchestrator (standaard Kubernetes). De Orchestrator is verantwoordelijk voor implementatie, schaal baarheid en beheer van de toepassing. Kies een optie op basis van uw huidige behoeften. U kunt een van beide gebruiken, of een combi natie van beide, afhankelijk van uw project fase.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>Hoe kan ik een dynamische toewijzing op het dash board configureren?

Zie [Update toewijzings sleutel voor het weer geven van apparaten op een dynamische kaart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)voor meer informatie.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Waar vind ik informatie over de vorige versie van de oplossing voor bewaking op afstand?

De vorige versie van de oplossing voor controle op afstand van externe bewaking heette de IoT Suite vooraf geconfigureerde oplossing voor externe controle. U kunt de gearchiveerde documentatie vinden op [https://docs.microsoft.com/previous-versions/azure/iot-suite/](/previous-versions/azure/iot-suite/) .

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Bekijk de mogelijkheden van de oplossings versneller voor externe controle](quickstart-remote-monitoring-deploy.md)
* [Overzicht van de oplossingsversneller Voorspeld onderhoud](./iot-accelerators-predictive-walkthrough.md)
* [Connected Factory Solution Accelerator implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](../iot-fundamentals/iot-security-ground-up.md)