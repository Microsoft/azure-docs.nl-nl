---
title: De afronding van de liveiteit instellen voor het container exemplaar
description: Meer informatie over het configureren van Live-tests om beschadigde containers opnieuw te starten in Azure Container Instances
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: befe9693be1413abf455d915814c53aab20db53c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169694"
---
# <a name="configure-liveness-probes"></a>Activiteitstests configureren

Container toepassingen kunnen gedurende lange tijd worden uitgevoerd, wat leidt tot verbroken statussen die mogelijk moeten worden hersteld door de container opnieuw op te starten. Azure Container Instances ondersteunt beproefde tests zodat u uw containers in uw container groep kunt configureren om opnieuw op te starten als essentiële functionaliteit niet werkt. De test op de duur van de liveiteit gedraagt zich als een Kubernetes-test voor de [liveiteit](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/).

In dit artikel wordt uitgelegd hoe u een container groep implementeert die een afsluitings sonde bevat, waarbij het automatisch opnieuw opstarten van een gesimuleerde beschadigde container wordt gedemonstreerd.

Azure Container Instances biedt ook ondersteuning voor [gereedheids tests](container-instances-readiness-probe.md), die u kunt configureren om ervoor te zorgen dat verkeer alleen een container bereikt wanneer het gereed is voor IT.

> [!NOTE]
> U kunt op dit moment geen beproefde test gebruiken in een container groep die is geïmplementeerd in een virtueel netwerk.

## <a name="yaml-deployment"></a>YAML-implementatie

Maak een `liveness-probe.yaml` bestand met het volgende code fragment. Dit bestand definieert een container groep die bestaat uit een NGNIX-container die uiteindelijk slecht wordt.

```yaml
apiVersion: 2019-12-01
location: eastus
name: livenesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: nginx
      command:
        - "/bin/sh"
        - "-c"
        - "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      livenessProbe:
        exec:
            command:
                - "cat"
                - "/tmp/healthy"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Voer de volgende opdracht uit om deze container groep te implementeren met de bovenstaande YAML-configuratie:

```azurecli-interactive
az container create --resource-group myResourceGroup --name livenesstest -f liveness-probe.yaml
```

### <a name="start-command"></a>Opdracht starten

De implementatie bevat een `command` eigenschap waarmee een begin opdracht wordt gedefinieerd die wordt uitgevoerd wanneer de container voor het eerst wordt gestart. Deze eigenschap accepteert een matrix met teken reeksen. Met deze opdracht wordt de container met een slechte status gesimuleerd.

Eerst wordt een bash-sessie gestart en wordt een bestand gemaakt dat wordt aangeroepen `healthy` in de `/tmp` map. Vervolgens wordt er 30 seconden geslapend voordat het bestand wordt verwijderd. vervolgens wordt er een slaap stand van 10 minuten ingevoerd:

```bash
/bin/sh -c "touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600"
```

### <a name="liveness-command"></a>Opdracht van liveiteit

Deze implementatie definieert een `livenessProbe` die ondersteuning biedt voor een `exec` opdracht van de online werking die als controle op de levens duur fungeert. Als deze opdracht wordt afgesloten met een waarde die niet gelijk is aan nul, wordt de container afgebroken en opnieuw gestart, `healthy` waardoor het bestand niet kan worden gevonden. Als de opdracht met de afsluit code 0 is voltooid, wordt er geen actie ondernomen.

De `periodSeconds` eigenschap geeft aan dat de opdracht voor de liveiteit elke vijf seconden moet worden uitgevoerd.

## <a name="verify-liveness-output"></a>Uitvoer van de liveiteit controleren

In de eerste 30 seconden bestaat het `healthy` bestand dat is gemaakt met de opdracht starten. Wanneer de opdracht van de bewerking voor het `healthy` bestaan van het bestand wordt gecontroleerd, retourneert de status code 0, het signaal is geslaagd, dus wordt het niet opnieuw opgestart.

Na 30 seconden kan de `cat /tmp/healthy` opdracht niet worden uitgevoerd, waardoor er sprake is van een slechte status en het doden van gebeurtenissen.

Deze gebeurtenissen kunnen worden bekeken vanuit de Azure Portal of Azure CLI.

![De portal heeft een slechte gebeurtenis][portal-unhealthy]

Door de gebeurtenissen in de Azure Portal weer te geven, worden gebeurtenissen van `Unhealthy` het type geactiveerd op het moment dat de opdracht voor de bewerking wordt uitgevoerd. De volgende gebeurtenis is van het type `Killing` , waardoor een container wordt verwijderd, zodat het opnieuw opstarten kan worden gestart. Het aantal opnieuw te starten voor de container wordt telkens verhoogd wanneer deze gebeurtenis plaatsvindt.

Opnieuw opstarten is in-place voltooid, zodat resources zoals open bare IP-adressen en knooppunt afhankelijke inhoud behouden blijven.

![Teller voor opnieuw starten van portal][portal-restart]

Als de duur van de bewerking continu wordt uitgevoerd en er te veel opnieuw moet worden opgestart, treedt er een exponentiële uitstel vertraging op.

## <a name="liveness-probes-and-restart-policies"></a>Online tests en beleid voor opnieuw opstarten

Beleid voor opnieuw opstarten vervangt het gedrag voor opnieuw opstarten dat door de Live-tests wordt geactiveerd. Als u bijvoorbeeld een- `restartPolicy = Never` *en* een-test voor de duur instelt, wordt de container groep niet opnieuw opgestart vanwege een mislukte online controle. In plaats daarvan wordt de container groep gevolgd door het beleid voor het opnieuw opstarten van de container groep `Never` .

## <a name="next-steps"></a>Volgende stappen

Voor scenario's op basis van taken kan het nodig zijn om automatisch opnieuw opstarten in te scha kelen als een vereiste functie niet goed werkt. Zie [taken in een container uitvoeren in azure container instances](container-instances-restart-policy.md)voor meer informatie over het uitvoeren van taak containers.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-liveness-probe/unhealthy-killing.png
[portal-restart]: ./media/container-instances-liveness-probe/portal-restart.png
