---
title: Sleutel-waardeparen ophalen van een bepaald tijdstip
titleSuffix: Azure App Configuration
description: Verouderde sleutel-waardeparen ophalen met behulp van punt-in-time moment opnamen in Azure-app configuratie, waarmee een record met wijzigingen in sleutel waarden wordt bijgehouden.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929894"
---
# <a name="point-in-time-snapshot"></a>Point-in-time-snapshot

Azure-app configuratie houdt een record bij van wijzigingen die zijn aangebracht in sleutel waarden. Deze record bevat een tijd lijn met belang rijke wijzigingen. U kunt de geschiedenis van een wille keurige sleutel waarde opnieuw samen stellen en de bijbehorende waarde op elk moment in de sleutel geschiedenis periode (7 dagen voor gratis laag winkels of 30 dagen voor de Standard-laag) opgeven. Met deze functie kunt u ' time-reizen ' achterwaarts maken en een oude sleutel waarde ophalen. U kunt bijvoorbeeld de configuratie-instellingen herstellen die voor de meest recente implementatie zijn gebruikt om de toepassing terug te zetten naar de vorige configuratie.

## <a name="key-value-retrieval"></a>Sleutel/waarde ophalen

U kunt Azure Portal of CLI gebruiken om vorige sleutel waarden op te halen. Gebruik in azure CLI `az appconfig revision list` om de juiste para meters toe te voegen om de vereiste waarden op te halen.  Geef het Azure-app configuratie-exemplaar op door de naam van de winkel op te geven ( `--name <app-config-store-name>` ) of door een Connection String () te gebruiken `--connection-string <your-connection-string>` . De uitvoer beperken door een bepaald punt in de tijd () op te geven `--datetime` en door het maximum aantal items op te geven dat moet worden geretourneerd ( `--top` ).

Als Azure CLI niet lokaal is geïnstalleerd, kunt u eventueel [Azure Cloud shell](../cloud-shell/overview.md)gebruiken.

Alle vastgelegde wijzigingen in uw sleutel waarden ophalen.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

Alle vastgelegde wijzigingen voor de sleutel `environment` en de labels `test` en worden opgehaald `prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Alle vastgelegde wijzigingen in de hiërarchische sleutel ruimte ophalen `environment:prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Alle vastgelegde wijzigingen voor de sleutel `color` op een specifiek tijdstip ophalen.

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

De laatste 10 vastgelegde wijzigingen in uw sleutel waarden ophalen en alleen de waarden voor `key` , `label` en `last_modified` tijds tempel retour neren.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-web-app maken](./quickstart-aspnet-core-app.md)