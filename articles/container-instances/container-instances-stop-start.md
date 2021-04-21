---
title: Containergroep handmatig stoppen of starten
description: Meer informatie over het handmatig stoppen of starten van een containergroep in Azure Container Instances.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 43498a7d31f0cb78751d2f318aede8523b7b9345
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786995"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Containers in Azure Container Instances handmatig stoppen of starten

De [beleidsinstelling voor](container-instances-restart-policy.md) opnieuw opstarten van een containergroep bepaalt hoe container-exemplaren standaard worden gestart of gestopt. U kunt de standaardinstelling overschrijven door handmatig een containergroep te stoppen of te starten.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Stoppen

Stop handmatig een containergroep die wordt uitgevoerd, bijvoorbeeld met behulp van de [opdracht az container stop][az-container-stop] of Azure Portal. Voor bepaalde containerworkloads wilt u mogelijk een langlopende containergroep na een bepaalde periode stoppen om kosten te besparen. 

*Wanneer een containergroep de status Gestopt krijgt, wordt deze beëindigd en worden alle containers in de groep gerecycled. De containertoestand wordt niet behouden.*

Wanneer de containers worden gerecycled, worden [de resources](container-instances-container-groups.md#resource-allocation) weer toegewezen en wordt de facturering voor de containergroep gestopt.

De stopactie heeft geen effect als de containergroep al is beëindigd (heeft de status Geslaagd of Mislukt). Een containergroep met run-once containertaken die is uitgevoerd, wordt bijvoorbeeld beëindigd met de status Geslaagd. Pogingen om de groep in die status te stoppen, wijzigen de status niet. 

## <a name="start"></a>Starten

Wanneer een containergroep is gestopt, omdat de containers zelf zijn beëindigd of u de groep handmatig hebt gestopt, kunt u de containers starten. Gebruik bijvoorbeeld de opdracht [az container start][az-container-start] of Azure Portal de containers in de groep handmatig te starten. Als de containerafbeelding voor een container wordt bijgewerkt, wordt er een nieuwe afbeelding uit de container gehaald. 

Het starten van een containergroep begint een nieuwe implementatie met dezelfde containerconfiguratie. Met deze actie kunt u snel een bekende configuratie van een containergroep opnieuw gebruiken die naar verwachting werkt. U hoeft geen nieuwe containergroep te maken om dezelfde workload uit te voeren.

Alle containers in een containergroep worden met deze actie gestart. U kunt geen specifieke container in de groep starten.

Nadat u een containergroep handmatig hebt gestart of opnieuw hebt opgestart, wordt de containergroep uitgevoerd volgens het geconfigureerde beleid voor opnieuw opstarten.
  
## <a name="restart"></a>Opnieuw starten

U kunt een containergroep opnieuw starten terwijl deze wordt uitgevoerd, bijvoorbeeld met behulp van de [opdracht az container restart.][az-container-restart] Met deze actie worden alle containers in de containergroep opnieuw gestart. Als de containerafbeelding voor een container wordt bijgewerkt, wordt er een nieuwe afbeelding binnengehaald. 

Het opnieuw starten van een containergroep is handig als u een implementatieprobleem wilt oplossen. Als een tijdelijke resourcebeperking bijvoorbeeld voorkomt dat uw containers worden uitgevoerd, kan het probleem worden opgelost door de groep opnieuw op te starten.

Met deze actie worden alle containers in een containergroep opnieuw gestart. U kunt een specifieke container in de groep niet opnieuw starten.

Nadat u een containergroep handmatig opnieuw hebt opgestart, wordt de containergroep uitgevoerd volgens het geconfigureerde beleid voor opnieuw opstarten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [beleidsinstellingen voor opnieuw opstarten](container-instances-restart-policy.md) in Azure Container Instances.

Naast het handmatig stoppen en starten van een containergroep [](container-instances-update.md) met de bestaande configuratie, kunt u de instellingen van een containergroep bijwerken die wordt uitgevoerd.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az_container_restart
[az-container-start]: /cli/azure/container#az_container_start
[az-container-stop]: /cli/azure/container#az_container_stop
