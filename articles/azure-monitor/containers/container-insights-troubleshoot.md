---
title: Problemen met Azure Monitor voor containers oplossen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u problemen met Azure Monitor voor containers kunt oplossen en oplossen.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 5727702ff973523ce7ab6400c1c7748e0584acbf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609925"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>Problemen met Azure Monitor voor containers oplossen

Wanneer u de bewaking van uw Azure Kubernetes service-cluster (AKS) met Azure Monitor voor containers configureert, kunt u een probleem tegen komen bij het verzamelen of rapporteren van gegevens. In dit artikel vindt u informatie over enkele veelvoorkomende problemen en stappen voor probleem oplossing.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Verificatiefout tijdens onboarding of updatebewerking

Wanneer u Azure Monitor voor containers inschakelt of een cluster bijwerkt ter ondersteuning van het verzamelen van metrische gegevens, wordt mogelijk een fout bericht weer gegeven met de volgende strekking: *de client <identiteit van de gebruiker> ' with object id ' <de objectId> van de gebruiker heeft geen machtiging om de actie ' micro soft. Authorization/roleAssignments/write ' over te voeren*

Tijdens het voorbereidings-of bijwerk proces wordt het verlenen van de roltoewijzing voor **bewakings gegevens** voor de cluster bron geprobeerd. De gebruiker die het proces initieert om Azure Monitor voor containers in te scha kelen of de update om het verzamelen van gegevens te ondersteunen, moet toegang hebben tot de machtiging **micro soft. Authorization/roleAssignments/write** voor het bron bereik van het AKS-cluster. Alleen leden van de ingebouwde rollen van de **eigenaar** en de beheerder van de **gebruikers toegang** krijgen toegang tot deze machtiging. Als voor uw beveiligings beleid gedetailleerde machtigingen voor niveau zijn vereist, raden we u aan om [aangepaste rollen](../../role-based-access-control/custom-roles.md) weer te geven en toe te wijzen aan de gebruikers die deze nodig hebben.

U kunt deze rol ook hand matig verlenen via de Azure Portal door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik in Azure Portal in de linkerbovenhoek op **Alle services**. Typ **Kubernetes** in de lijst met resources. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Azure Kubernetes**.
3. Selecteer in de lijst met Kubernetes-clusters een in de lijst.
2. Klik in het linkermenu op **toegangs beheer (IAM)**.
3. Selecteer **+ toevoegen** om een roltoewijzing toe te voegen en selecteer de Publisher-rol **bewaking metrieken** en klik onder het **selectie** vakje **AKS** om de resultaten te filteren op alleen de service-principals van de clusters die in het abonnement zijn gedefinieerd. Selecteer de optie in de lijst die specifiek is voor dat cluster.
4. Selecteer **Opslaan** om de rol toe te wijzen.

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>Azure Monitor voor containers is ingeschakeld, maar er wordt geen informatie gerapporteerd

Als Azure Monitor voor containers is ingeschakeld en geconfigureerd, maar u geen status informatie kunt weer geven of als er geen resultaten worden geretourneerd uit een logboek query, kunt u het probleem vaststellen door de volgende stappen uit te voeren:

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

De volgende tabel bevat een overzicht van bekende fouten die kunnen optreden bij het gebruik van Azure Monitor voor containers.

| Foutberichten  | Bewerking |
| ---- | --- |
| Fout bericht `No data for selected filters`  | Het kan enige tijd duren voordat de bewakingsgegevensstroom voor nieuwe clusters tot stand is gebracht. Sta ten minste 10 tot 15 minuten toe dat er gegevens voor uw cluster worden weer gegeven. |
| Fout bericht `Error retrieving data` | Terwijl Azure Kubernetes service-cluster wordt ingesteld voor de status-en prestatie bewaking, wordt er een verbinding tot stand gebracht tussen het cluster en de Azure Log Analytics-werk ruimte. Een Log Analytics-werk ruimte wordt gebruikt om alle bewakings gegevens voor uw cluster op te slaan. Deze fout kan optreden als uw Log Analytics-werk ruimte is verwijderd. Controleer of de werk ruimte is verwijderd. als dat het geval is, moet u de bewaking van het cluster opnieuw inschakelen met Azure Monitor voor containers en een bestaande of een nieuwe werk ruimte maken. Als u het opnieuw wilt inschakelen, moet u de bewaking voor het cluster [uitschakelen](container-insights-optout.md) en de Azure monitor voor containers weer [inschakelen](container-insights-enable-new-cluster.md) . |
| `Error retrieving data` na het toevoegen van Azure Monitor voor containers via AZ AKS cli | Als u controle inschakelt met `az aks cli` , worden Azure monitor voor containers mogelijk niet op de juiste wijze geïmplementeerd. Controleer of de oplossing is geïmplementeerd. Als u wilt controleren, gaat u naar uw Log Analytics-werk ruimte en controleert u of de oplossing beschikbaar is door **oplossingen** te selecteren in het deel venster aan de linkerkant. Als u dit probleem wilt oplossen, moet u de oplossing opnieuw implementeren door de instructies te volgen voor [het implementeren van Azure monitor voor containers](container-insights-onboard.md) |

Om het probleem te kunnen vaststellen, hebben we een script voor het [oplossen van problemen](https://aka.ms/troubleshooting-script)opgesteld.

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>Azure Monitor voor containers van de container-agent in een cluster zijn niet gepland voor niet-Azure Kubernetes-clusters

Azure Monitor voor container agent-replicaset is een afhankelijkheid van de volgende knooppunt selectie vakjes op de worker-knoop punten (of agent) voor de planning:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Als aan uw worker-knoop punten geen knooppunt labels zijn gekoppeld, wordt de replicaset van de agent pas gepland. Raadpleeg [Kubernetes labels selecteren](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) voor instructies over het koppelen van het label.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Prestatie grafieken tonen geen CPU of geheugen van knoop punten en containers op een niet-Azure-cluster

Agentpods voor Azure Monitor voor containers maken gebruik van het cAdvisor-eindpunt naar de knooppuntagent om de metrische prestatiegegevens te verzamelen. Controleer of de container agent op het knoop punt zodanig is geconfigureerd dat deze kan `cAdvisor port: 10255` worden geopend op alle knoop punten in het cluster om metrische gegevens over prestaties te verzamelen.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>Niet-Azure Kubernetes-cluster wordt niet weer gegeven in Azure Monitor voor containers

Als u het niet-Azure Kubernetes-cluster in Azure Monitor voor containers wilt weer geven, moet u lees toegang hebben op de Log Analytics-werk ruimte die dit inzicht ondersteunt en op de **ContainerInsights (*werk ruimte*)** van de container Insights-oplossing.

## <a name="next-steps"></a>Volgende stappen

Als bewaking is ingeschakeld voor het vastleggen van metrische gegevens over de status van de AKS-cluster knooppunten en de peulen, zijn deze metrische gegevens over de status beschikbaar in de Azure Portal. Zie [Azure Kubernetes service Health weer geven](container-insights-analyze.md)voor meer informatie over het gebruik van Azure monitor voor containers.
