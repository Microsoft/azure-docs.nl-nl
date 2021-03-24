---
title: Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen | Microsoft Docs
description: Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 46d79de612a17c7b21e7cc51dcf4904209e1625d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877997"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Uw Azure lente-Cloud toepassing starten, stoppen en verwijderen

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

In deze hand leiding wordt uitgelegd hoe u de status van een toepassing in azure lente Cloud kunt wijzigen met behulp van de Azure Portal of de Azure CLI.

## <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Nadat u een toepassing hebt geïmplementeerd, kunt u deze starten, stoppen en verwijderen met behulp van de Azure Portal.

1. Ga naar het Azure veer Cloud service-exemplaar in het Azure Portal.
1. Selecteer het tabblad **toepassings dashboard** .
1. Selecteer de toepassing waarvan u de status wilt wijzigen.
1. Selecteer op de pagina **overzicht** voor die toepassing **starten/stoppen**, **opnieuw starten** of **verwijderen**.

## <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

> [!NOTE]
> U kunt optionele para meters gebruiken en standaard instellingen configureren met de Azure CLI. Lees [onze referentie documentatie](/cli/azure/ext/spring-cloud/spring-cloud)voor meer informatie over de Azure cli.  

Installeer eerst de Azure veer Cloud-extensie voor de Azure CLI als volgt:

```azurecli
az extension add --name spring-cloud
```

Selecteer vervolgens een van de volgende Azure CLI-bewerkingen:

* Uw toepassing starten:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uw toepassing stoppen:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* De toepassing opnieuw starten:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Uw toepassing verwijderen:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
