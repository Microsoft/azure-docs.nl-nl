---
title: Connectiviteit van apparaten bewaken met behulp van Azure IoT Central Explorer
description: Controleer de apparaatgegevens en Bekijk de dubbele wijzigingen van het apparaat via de IoT Central Explorer-CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 62981686c7aadc713c4abc78075be8613fe0af45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199303"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Apparaatconnectiviteit bewaken met Azure CLI

*Dit onderwerp is van toepassing op ontwikkel aars van apparaten en oplossingen bouwers.*

Gebruik de Azure CLI IoT-extensie om berichten te bekijken die uw apparaten naar IoT Central verzenden en wijzigingen in het apparaat te observeren. U kunt dit hulp programma gebruiken om de connectiviteit van apparaten op te sporen en te controleren op problemen met de toegang tot de Cloud of apparaten die niet reageren op twee wijzigingen.

[Bezoek de naslag informatie voor Azure CLI-extensies voor meer informatie](/cli/azure/ext/azure-iot/iot/central)

## <a name="prerequisites"></a>Vereisten

+ Azure CLI is geïnstalleerd en versie 2.7.0 of hoger. Controleer de versie van uw Azure CLI door uit te voeren `az --version` . Meer informatie over het installeren en bijwerken van de [Azure cli-documenten](/cli/azure/install-azure-cli)
+ Een werk-of school account in azure, toegevoegd als een gebruiker in een IoT Central-toepassing.

## <a name="install-the-iot-central-extension"></a>De IoT Central-extensie installeren

Voer de volgende opdracht uit vanaf de opdracht regel die u wilt installeren:

```azurecli
az extension add --name azure-iot
```

Controleer de versie van de uitbrei ding door het volgende uit te voeren:

```azurecli
az --version
```

U ziet dat de Azure IOT-uitbrei ding 0.9.9 of hoger is. Als dat niet het geval is, voert u de volgende handelingen uit:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>De uitbrei ding gebruiken

In de volgende secties worden veelvoorkomende opdrachten en opties beschreven die u kunt gebruiken wanneer u uitvoert `az iot central` . Als u de volledige set met opdrachten en opties wilt weer geven, geeft `--help` u een `az iot central` of meer van de bijbehorende subopdrachten door.

### <a name="login"></a>Aanmelden

Meld u aan bij de Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>De toepassings-ID van uw IoT Central-app ophalen
Kopieer de **toepassings-id** in **beheer/toepassings instellingen**. U gebruikt deze waarde in latere stappen.

### <a name="monitor-messages"></a>Berichten bewaken
Controleer de berichten die naar uw IoT Central-app worden verzonden vanaf uw apparaten. De uitvoer bevat alle kopteksten en aantekeningen.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Apparaateigenschappen weergeven
De huidige eigenschappen voor lezen en lezen/schrijven weer geven voor een bepaald apparaat.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u de volgende stap lezen over de [connectiviteit van apparaten in Azure IOT Central](./concepts-get-connected.md).