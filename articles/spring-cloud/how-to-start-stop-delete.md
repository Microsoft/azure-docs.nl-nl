---
title: Start, stop en verwijder uw Azure Spring Cloud-| Microsoft Docs
description: Uw toepassing starten, stoppen en verwijderen Azure Spring Cloud toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 914325aba3d123fb1b700f0eff8ddb17119c5d12
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863205"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Uw toepassing starten, stoppen en Azure Spring Cloud verwijderen

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

In deze handleiding wordt uitgelegd hoe u de status van een toepassing in Azure Spring Cloud wijzigen met behulp van de Azure Portal of de Azure CLI.

## <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Nadat u een toepassing hebt geïmplementeerd, kunt u deze starten, stoppen en verwijderen met behulp van de Azure Portal.

1. Ga naar uw Azure Spring Cloud service-exemplaar in de Azure Portal.
1. Selecteer het **tabblad Toepassingsdashboard.**
1. Selecteer de toepassing waarvan u de status wilt wijzigen.
1. Selecteer op **de** pagina Overzicht voor die toepassing de optie **Starten/stoppen,** **Opnieuw** opstarten of **Verwijderen.**

## <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

> [!NOTE]
> U kunt optionele parameters gebruiken en standaardinstellingen configureren met de Azure CLI. Lees onze referentiedocumentatie voor meer informatie [over](/cli/azure/spring-cloud)de Azure CLI.  

Installeer eerst de Azure Spring Cloud voor de Azure CLI als volgt:

```azurecli
az extension add --name spring-cloud
```

Selecteer vervolgens een van deze Azure CLI-bewerkingen:

* Ga als volgende te werk om uw toepassing te starten:

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
