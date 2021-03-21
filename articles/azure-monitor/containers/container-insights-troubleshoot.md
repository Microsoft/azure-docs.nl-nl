---
title: Problemen met container Insights oplossen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u problemen met container Insights kunt oplossen en oplossen.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 60a6e76d43d954b27336b9631c48328aeff0b69b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708302"
---
# <a name="troubleshooting-container-insights"></a>Problemen met container Insights oplossen

Wanneer u de bewaking van uw Azure Kubernetes service (AKS)-cluster met container Insights configureert, kunt u een probleem tegen komen bij het verzamelen of rapporteren van gegevens. In dit artikel vindt u informatie over enkele veelvoorkomende problemen en stappen voor probleem oplossing.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Verificatiefout tijdens onboarding of updatebewerking

Hoewel het mogelijk is om container Insights in te scha kelen of een cluster bij te werken voor de ondersteuning van het verzamelen van metrische gegevens, kan er een fout bericht met de volgende strekking worden weer gegeven: *de client <identiteit van de gebruiker> ' with object id ' <objectId> van de gebruiker heeft geen toestemming om de actie ' micro soft. Authorization/roleAssignments/write ' over te zetten*

Tijdens het voorbereidings-of bijwerk proces wordt het verlenen van de roltoewijzing voor **bewakings gegevens** voor de cluster bron geprobeerd. De gebruiker die het proces initieert om container Insights in te scha kelen of de update om het verzamelen van metrische gegevens te ondersteunen, moet toegang hebben tot de machtiging **micro soft. Authorization/roleAssignments/write** voor het bron bereik van het AKS-cluster. Alleen leden van de ingebouwde rollen van de **eigenaar** en de beheerder van de **gebruikers toegang** krijgen toegang tot deze machtiging. Als voor uw beveiligings beleid gedetailleerde machtigingen voor niveau zijn vereist, raden we u aan om [aangepaste rollen](../../role-based-access-control/custom-roles.md) weer te geven en toe te wijzen aan de gebruikers die deze nodig hebben.

U kunt deze rol ook hand matig verlenen via de Azure Portal door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik in Azure Portal in de linkerbovenhoek op **Alle services**. Typ **Kubernetes** in de lijst met resources. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Azure Kubernetes**.
3. Selecteer in de lijst met Kubernetes-clusters een in de lijst.
2. Klik in het linkermenu op **toegangs beheer (IAM)**.
3. Selecteer **+ toevoegen** om een roltoewijzing toe te voegen en selecteer de Publisher-rol **bewaking metrieken** en klik onder het **selectie** vakje **AKS** om de resultaten te filteren op alleen de service-principals van de clusters die in het abonnement zijn gedefinieerd. Selecteer de optie in de lijst die specifiek is voor dat cluster.
4. Selecteer **Opslaan** om de rol toe te wijzen.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Container Insights is ingeschakeld, maar er wordt geen informatie gerapporteerd

Als container Insights is ingeschakeld en geconfigureerd, maar u geen status informatie kunt weer geven of als er geen resultaten worden geretourneerd door een logboek query, kunt u het probleem vaststellen door de volgende stappen uit te voeren:

1. Controleer de status van de agent door de opdracht uit te voeren:

    `kubectl get ds omsagent --namespace=kube-system`

    De uitvoer moet er ongeveer uitzien als in het volgende voor beeld, wat aangeeft dat het goed is geïmplementeerd:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Als u Windows Server-knoop punten hebt, controleert u de status van de agent door de opdracht uit te voeren:

    `kubectl get ds omsagent-win --namespace=kube-system`

    De uitvoer moet er ongeveer uitzien als in het volgende voor beeld, wat aangeeft dat het goed is geïmplementeerd:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Controleer de implementatie status met Agent versie *06072018* of hoger met behulp van de opdracht:

    `kubectl get deployment omsagent-rs -n=kube-system`

    De uitvoer moet er ongeveer uitzien als in het volgende voor beeld, wat aangeeft dat het goed is geïmplementeerd:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Controleer de status van de pod om te controleren of deze wordt uitgevoerd met behulp van de opdracht: `kubectl get pods --namespace=kube-system`

    De uitvoer moet er ongeveer uitzien als in het volgende voor beeld met de status *wordt uitgevoerd* voor de omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Foutberichten

De volgende tabel bevat een overzicht van bekende fouten die kunnen optreden bij het gebruik van container Insights.

| Foutberichten  | Bewerking |
| ---- | --- |
| Fout bericht `No data for selected filters`  | Het kan enige tijd duren voordat de bewakingsgegevensstroom voor nieuwe clusters tot stand is gebracht. Sta ten minste 10 tot 15 minuten toe dat er gegevens voor uw cluster worden weer gegeven. |
| Fout bericht `Error retrieving data` | Terwijl Azure Kubernetes service-cluster wordt ingesteld voor de status-en prestatie bewaking, wordt er een verbinding tot stand gebracht tussen het cluster en de Azure Log Analytics-werk ruimte. Een Log Analytics-werk ruimte wordt gebruikt om alle bewakings gegevens voor uw cluster op te slaan. Deze fout kan optreden als uw Log Analytics-werk ruimte is verwijderd. Controleer of de werk ruimte is verwijderd. als dat het geval is, moet u de bewaking van het cluster opnieuw inschakelen met container Insights en een bestaande of een nieuwe werk ruimte maken. Als u het opnieuw wilt inschakelen, moet u de bewaking voor het cluster [uitschakelen](container-insights-optout.md) en de container inzichten weer [inschakelen](container-insights-enable-new-cluster.md) . |
| `Error retrieving data` na het toevoegen van container Insights via AZ AKS cli | Wanneer u controle inschakelt met, is het `az aks cli` mogelijk dat container Insights niet goed is geïmplementeerd. Controleer of de oplossing is geïmplementeerd. Als u wilt controleren, gaat u naar uw Log Analytics-werk ruimte en controleert u of de oplossing beschikbaar is door **oplossingen** te selecteren in het deel venster aan de linkerkant. Als u dit probleem wilt oplossen, moet u de oplossing opnieuw implementeren door de instructies te volgen voor [het implementeren van container Insights](container-insights-onboard.md) |

Om het probleem te kunnen vaststellen, hebben we een script voor het [oplossen van problemen](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot)opgesteld.

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>De replicaset van de container Insights-agent is niet gepland voor een niet-Azure Kubernetes-cluster

Container Insights-agent replicaset is een afhankelijkheid van de volgende knooppunt selectie vakjes op de worker-knoop punten (of agent) voor de planning:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Als aan uw worker-knoop punten geen knooppunt labels zijn gekoppeld, wordt de replicaset van de agent pas gepland. Raadpleeg [Kubernetes labels selecteren](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) voor instructies over het koppelen van het label.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Prestatie grafieken tonen geen CPU of geheugen van knoop punten en containers op een niet-Azure-cluster

Container Insights-agent peulen maakt gebruik van het cAdvisor-eind punt op de knooppunt agent om de metrische gegevens over prestaties te verzamelen. Controleer of de container agent op het knoop punt zodanig is geconfigureerd dat deze kan `cAdvisor port: 10255` worden geopend op alle knoop punten in het cluster om metrische gegevens over prestaties te verzamelen.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>Niet-Azure Kubernetes-cluster wordt niet weer gegeven in container Insights

Voor het weer geven van het niet-Azure Kubernetes-cluster in container Insights, lees toegang is vereist in de Log Analytics-werk ruimte die dit inzicht ondersteunt, en op de resource **-ContainerInsights (*werk ruimte*)** van de container Insights-oplossing.

## <a name="next-steps"></a>Volgende stappen

Als bewaking is ingeschakeld voor het vastleggen van metrische gegevens over de status van de AKS-cluster knooppunten en de peulen, zijn deze metrische gegevens over de status beschikbaar in de Azure Portal. Zie [Azure Kubernetes service Health weer geven](container-insights-analyze.md)voor meer informatie over het gebruik van container Insights.
