---
title: Apparaatconnectiviteit bewaken met de Azure IoT Central Explorer
description: Controleer apparaatberichten en bekijk wijzigingen in de apparaattweeling via IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 30a823b7e78145224929b427e9e37522a7e29f09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871287"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Apparaatconnectiviteit bewaken met Azure CLI

*Dit onderwerp is van toepassing op apparaatontwikkelaars en oplossingsontwikkelaars.*

Gebruik de Azure CLI IoT-extensie om te zien welke berichten uw apparaten naar de IoT Central en om wijzigingen in de apparaat dubbel te bekijken. U kunt dit hulpprogramma gebruiken om problemen met de connectiviteit van apparaten op te sporen en te observeren, en problemen te diagnosticeren met apparaatberichten die de cloud niet bereiken of apparaten die niet reageren op dubbele wijzigingen.

[Ga naar de naslaginformatie over Azure CLI-extensies voor meer informatie](/cli/azure/iot/central)

## <a name="prerequisites"></a>Vereisten

+ Azure CLI is geïnstalleerd en is versie 2.7.0 of hoger. Controleer de versie van uw Azure CLI door uit te `az --version` gaan. Meer informatie over installeren en bijwerken vanuit [de Azure CLI-documenten](/cli/azure/install-azure-cli)
+ Een werk- of schoolaccount in Azure, toegevoegd als een gebruiker in een IoT Central toepassing.

## <a name="install-the-iot-central-extension"></a>De extensie IoT Central installeren

Voer de volgende opdracht uit vanaf de opdrachtregel om deze te installeren:

```azurecli
az extension add --name azure-iot
```

Controleer de versie van de extensie door het volgende uit te doen:

```azurecli
az --version
```

U ziet dat de extensie azure-iot 0.9.9 of hoger is. Als dat niet het volgende is, voer dan het volgende uit:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>De extensie gebruiken

In de volgende secties worden algemene opdrachten en opties beschreven die u kunt gebruiken wanneer u uit te `az iot central` voeren. Als u de volledige set opdrachten en opties wilt weergeven, geeft u door aan of een `--help` `az iot central` van de subopdrachten.

### <a name="login"></a>Aanmelden

Begin door u aan te melden bij de Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>De toepassings-id van uw IoT Central app
Kopieer **in Beheer/Toepassingsinstellingen** de **toepassings-id**. U gebruikt deze waarde in latere stappen.

### <a name="monitor-messages"></a>Berichten bewaken
Controleer de berichten die worden verzonden naar uw IoT Central app vanaf uw apparaten. De uitvoer bevat alle headers en aantekeningen.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Apparaateigenschappen weergeven
Bekijk de huidige apparaateigenschappen voor lezen en lezen/schrijven voor een bepaald apparaat.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Volgende stappen

Als u een apparaatontwikkelaar bent, is een voorgestelde volgende stap om meer te lezen over [Apparaatconnectiviteit in Azure IoT Central](./concepts-get-connected.md).