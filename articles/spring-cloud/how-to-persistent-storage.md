---
title: Permanente opslag gebruiken in azure lente-Cloud | Microsoft Docs
description: Permanente opslag gebruiken in azure lente-Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8811a30d2d0d6a31ff722e7f470b7e7bc38c4158
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878106"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Permanente opslag gebruiken in Azure Spring Cloud

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Azure lente Cloud biedt twee soorten opslag voor uw toepassing: permanente en tijdelijke.

Azure lente-Cloud biedt standaard tijdelijke opslag voor elk toepassings exemplaar. De tijdelijke opslag is beperkt tot 5 GB per exemplaar met het standaard koppel pad/tmp.

> [!WARNING]
> Als u een instantie van een toepassing opnieuw opstart, wordt de gekoppelde tijdelijke opslag permanent verwijderd.

Permanente opslag is een bestands share container die wordt beheerd door Azure en per toepassing wordt toegewezen. Gegevens die zijn opgeslagen in permanente opslag worden gedeeld door alle exemplaren van een toepassing. Een Azure lente-Cloud-exemplaar kan Maxi maal 10 toepassingen bevatten waarvoor permanente opslag is ingeschakeld. Aan elke toepassing wordt 50 GB aan permanente opslag toegewezen. Het pad voor de standaard koppeling voor permanente opslag is/persistent.

> [!WARNING]
> Als u de permanente opslag van een toepassing uitschakelt, wordt de toewijzing van alle opslag ongedaan gemaakt en gaan alle opgeslagen gegevens verloren.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>De Azure Portal gebruiken om permanente opslag in te scha kelen

1. Selecteer **alle resources** op de **Start** pagina van uw Azure Portal.

    >![Het pictogram alle resources zoeken](media/portal-all-resources.jpg)

1. Selecteer de Azure lente-Cloud resource waarvoor permanente opslag nodig is. In dit voor beeld wordt de geselecteerde toepassing **upveer** genoemd.

    > ![Selecteer uw toepassing](media/select-service.jpg)

1. Selecteer in de kop **instellingen** de optie **apps**.

1. Uw Azure lente-Cloud Services worden weer gegeven in een tabel.  Selecteer de service waaraan u permanente opslag wilt toevoegen. In dit voor beeld is de **Gateway** service geselecteerd.

    > ![Uw service selecteren](media/select-gateway.jpg)

1. Selecteer op de pagina configuratie van de service **configuratie**

1. Selecteer het tabblad **permanente opslag** en selecteer **inschakelen**.

    > ![Permanente opslag inschakelen](media/enable-persistent-storage.jpg)

Nadat de permanente opslag is ingeschakeld, worden de grootte en het pad op de pagina configuratie weer gegeven.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>De Azure CLI gebruiken om permanente opslag te wijzigen

Installeer, indien nodig, de lente-Cloud extensie voor Azure CLI:

```azurecli
az extension add --name spring-cloud
```
Andere bewerkingen:

* Een app maken waarvoor permanente opslag is ingeschakeld:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Permanente opslag voor een bestaande app inschakelen:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Permanente opslag in een bestaande app uitschakelen:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Als u de permanente opslag van een toepassing uitschakelt, wordt de toewijzing van alle opslag ongedaan gemaakt en blijven alle opgeslagen gegevens definitief verloren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [toepassings-en service quota's](spring-cloud-quotas.md).
* Meer informatie over het [hand matig schalen van uw toepassing](spring-cloud-howto-scale-manual.md).
