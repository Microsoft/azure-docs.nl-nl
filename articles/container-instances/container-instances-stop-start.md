---
title: De container groep hand matig stoppen of starten
description: Meer informatie over het hand matig stoppen of starten van een container groep in Azure Container Instances.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 1801dad463d478c754e621dad0ae9406899ae7e3
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198112"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Containers in Azure Container Instances handmatig stoppen of starten

De beleids instelling voor het [opnieuw opstarten](container-instances-restart-policy.md) van een container groep bepaalt hoe de container instanties standaard worden gestart of gestopt. U kunt de standaard instelling onderdrukken door een container groep hand matig te stoppen of te starten.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Stoppen

Een actieve container groep hand matig stoppen: u kunt bijvoorbeeld de opdracht [AZ container stop][az-container-stop] of Azure Portal gebruiken. Voor bepaalde container werkbelastingen wilt u mogelijk een langlopende container groep na een gedefinieerde periode stoppen om de kosten op te slaan. 

*Wanneer een container groep de status gestopt krijgt, wordt deze beëindigd en worden alle containers in de groep gerecycled. De container status wordt niet behouden.*

Wanneer de containers worden gerecycled, worden de toewijzingen van de [resources](container-instances-container-groups.md#resource-allocation) ongedaan gemaakt en worden facturerings stops voor de container groep.

De actie stoppen heeft geen effect als de container groep al is beëindigd (de status is geslaagd of mislukt). Bijvoorbeeld een container groep met taken voor het uitvoeren als-container die met succes is beëindigd met de status geslaagd. Als u probeert de groep in die status te stoppen, wijzigt u de status niet. 

## <a name="start"></a>Starten

Wanneer een container groep is gestopt, omdat de containers zijn beëindigd of u de groep hand matig hebt gestopt, kunt u de containers starten. Gebruik bijvoorbeeld de opdracht [AZ container start][az-container-start] of Azure Portal om de containers in de groep hand matig te starten. Als de container installatie kopie voor een container wordt bijgewerkt, wordt een nieuwe installatie kopie opgehaald. 

Als u een container groep start, wordt er een nieuwe implementatie met dezelfde container configuratie gestart. Deze actie kan u helpen om snel een configuratie van een bekende container groep te hergebruiken die naar verwachting werkt. U hoeft geen nieuwe container groep te maken om dezelfde werk belasting uit te voeren.

Alle containers in een container groep worden gestart door deze actie. U kunt een specifieke container niet starten in de groep.

Nadat u een container groep hand matig hebt gestart of opnieuw hebt opgestart, wordt de container groep uitgevoerd op basis van het geconfigureerde beleid voor opnieuw opstarten.
  
## <a name="restart"></a>Opnieuw starten

U kunt een container groep opnieuw starten terwijl deze actief is, bijvoorbeeld door de opdracht [AZ container restart][az-container-restart] te gebruiken. Met deze actie worden alle containers in de container groep opnieuw opgestart. Als de container installatie kopie voor een container wordt bijgewerkt, wordt een nieuwe installatie kopie opgehaald. 

Het opnieuw starten van een container groep is handig wanneer u een implementatie probleem wilt oplossen. Als een tijdelijke bron beperking bijvoorbeeld verhindert dat uw containers worden uitgevoerd, kan het probleem worden opgelost door de groep opnieuw op te starten.

Alle containers in een container groep worden opnieuw gestart door deze actie. U kunt een bepaalde container in de groep niet opnieuw starten.

Nadat u een container groep hand matig opnieuw hebt opgestart, wordt de container groep uitgevoerd op basis van het geconfigureerde beleid voor opnieuw opstarten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [opnieuw opstarten van beleids instellingen](container-instances-restart-policy.md) in azure container instances.

Naast het hand matig stoppen en starten van een container groep met de bestaande configuratie, kunt u [de instellingen](container-instances-update.md) van een actieve container groep bijwerken.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az-container-restart
[az-container-start]: /cli/azure/container#az-container-start
[az-container-stop]: /cli/azure/container#az-container-stop
