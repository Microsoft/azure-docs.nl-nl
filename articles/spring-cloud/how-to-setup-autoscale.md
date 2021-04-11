---
title: Automatische schaalaanpassing instellen voor microservicetoepassingen
description: In dit artikel wordt beschreven hoe u instellingen voor automatisch schalen instelt voor uw toepassingen met behulp van de Microsoft Azure-portal of de Azure CLI.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: eaa4a219a1020639c930f8a07bfcaa1c45d0cef8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878092"
---
# <a name="set-up-autoscale-for-microservice-applications"></a>Automatische schaalaanpassing instellen voor microservicetoepassingen

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Automatisch schalen is een ingebouwde functie van Azure Spring Cloud die ervoor zorgt dat microservicetoepassingen ook optimaal presteren wanneer de vraag verandert. Dit kan door het wijzigen van het aantal virtuele CPU's, de hoeveelheid geheugen en het aantal app-instanties. In dit artikel wordt beschreven hoe u instellingen voor automatisch schalen instelt voor uw toepassingen met behulp van de Microsoft Azure-portal of de Azure CLI.

## <a name="prerequisites"></a>Vereisten

Als u deze procedures wilt volgen, hebt u het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
* Een geïmplementeerd Azure Spring Cloud-service-exemplaar. Volg de [quickstart voor het implementeren van een app via Azure CLI](./spring-cloud-quickstart.md) om aan de slag te gaan.
* Er is al minstens één toepassing gemaakt in uw service-exemplaar.

## <a name="navigate-to-the-autoscale-page-in-the-azure-portal"></a>Naar de pagina Automatische schaalaanpassing navigeren in de Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Ga naar de pagina **Overzicht** van Azure Spring Cloud.
3. Selecteer dezelfde resourcegroep die uw service bevat.
4. Selecteer het tabblad **Apps** onder **Instellingen** in het menu aan de linkerkant van het navigatievenster.
5. Selecteer de toepassing waarvoor u automatische schaalaanpassing wilt instellen. Selecteer in dit voorbeeld de toepassing met de naam **demo**. Vervolgens ziet u de **Overzichtspagina** van de toepassing.
6. Ga naar het tabblad **Scale out** onder **Instellingen** in het menu aan de linkerkant van het navigatievenster.
7. Selecteer de implementatie waarvoor u automatische schaalaanpassing wilt instellen. U ziet nu de opties voor automatische schaalaanpassing die worden weergegeven in de volgende sectie.


![Opties voor automatische schaalaanpassing](./media/spring-cloud-autoscale/autoscale-menu.png)

## <a name="set-up-autoscale-settings-for-your-application-in-the-azure-portal"></a>Instellingen voor automatische schaalaanpassing instellen voor uw toepassing in de Azure-portal

Er zijn twee opties voor het beheer van automatische schaalaanpassing op basis van vraag:

* Handmatig schalen: Er wordt een vast aantal exemplaren onderhouden. In de Standard-laag kunt u uitschalen naar maximaal 500 exemplaren. Met deze waarde wordt het aantal afzonderlijke actieve exemplaren van de microservicetoepassing gewijzigd.
* Aangepaste automatische schaalaanpassing: Hiermee kunt u de schaal aanpassen volgens een willekeurige planning, op basis van bepaalde metrische gegevens

Geef in de Azure-portal aan hoe u wilt schalen.  In de volgende afbeelding ziet u de optie **Aangepaste automatische schaalaanpassing** en de bijbehorende instellingen.

![Aangepaste automatische schaalaanpassing](./media/spring-cloud-autoscale/custom-autoscale.png)

## <a name="set-up-autoscale-settings-for-your-application-in-azure-cli"></a>Instellingen voor automatische schaalaanpassing instellen voor uw toepassing in de Azure CLI
U kunt de modus voor automatische schaalaanpassing ook instellen met behulp van de Azure CLI.  Met de volgende opdrachten maakt u een instelling en een regel voor automatische schaalaanpassing.

* Instelling voor automatische schaalaanpassing maken
  ```
  az monitor autoscale create -g demo-rg --resource /subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourcegroups/demo-rg/providers/Microsoft.AppPlatform/Spring/autoscale/apps/demo/deployments/default --name demo-setting --min-count 1 --max-count 5 --count 1
  ```
* Regel voor automatische schaalaanpassing maken
  ```
  az monitor autoscale rule create -g demo-rg --autoscale-name demo-setting --scale out 1 --cooldown 1 --condition "tomcat.global.request.total.count > 100 avg 1m where AppName == demo and Deployment == default"
  ```

## <a name="upgrade-to-the-standard-tier"></a>Upgraden naar de prijscategorie Standard

Als u zich in de Basic-laag bevindt en bent beperkt door een of meer van deze limieten, kunt u een upgrade uitvoeren naar de Standard-laag. Ga hiervoor naar het menu **Prijscategorie** door eerst de kolom *Standard* te selecteren en vervolgens op de knop **Upgraden** te klikken.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van automatische schaalaanpassing in Microsoft Azure](../azure-monitor/autoscale/autoscale-overview.md)
* [Automatische schaalaanpassing controleren met Azure CLI](/cli/azure/monitor/autoscale)
