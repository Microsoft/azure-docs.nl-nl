---
title: De gereedheids test voor het container exemplaar instellen
description: Meer informatie over het configureren van een test om ervoor te zorgen dat containers in Azure Container Instances alleen aanvragen ontvangen wanneer ze klaar zijn
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 3e89086d66f284df35e36dc8f1d68bb09264843f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86169660"
---
# <a name="configure-readiness-probes"></a>Gereedheidstests configureren

Voor container toepassingen die verkeer verwerken, wilt u wellicht controleren of uw container gereed is voor het verwerken van binnenkomende aanvragen. Azure Container Instances ondersteunt gereedheids tests voor het toevoegen van configuraties zodat uw container onder bepaalde omstandigheden niet kan worden geopend. De gereedheids test gedraagt zich als een [Kubernetes Readiness-test](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/). Een container-app moet bijvoorbeeld een grote gegevensset laden tijdens het opstarten, en u wilt niet dat er aanvragen worden ontvangen tijdens deze periode.

In dit artikel wordt uitgelegd hoe u een container groep implementeert die een gereedheids test bevat, zodat een container alleen verkeer ontvangt wanneer de test slaagt.

Azure Container Instances biedt ook ondersteuning voor [beproefde tests](container-instances-liveness-probe.md), die u zo kunt configureren dat een beschadigde container automatisch opnieuw wordt opgestart.

> [!NOTE]
> Op dit moment kunt u geen gereedheids test gebruiken in een container groep die is geïmplementeerd in een virtueel netwerk.

## <a name="yaml-configuration"></a>YAML-configuratie

Maak een voor beeld van een `readiness-probe.yaml` bestand met het volgende code fragment dat een gereedheids test bevat. Dit bestand definieert een container groep die bestaat uit een container met een kleine web-app. De app wordt geïmplementeerd vanuit de open bare `mcr.microsoft.com/azuredocs/aci-helloworld` installatie kopie. Deze container-app wordt ook gedemonstreerd in [een container exemplaar implementeren in azure met behulp van de Azure cli](container-instances-quickstart.md) en andere Quick starts.

```yaml
apiVersion: 2019-12-01
location: eastus
name: readinesstest
properties:
  containers:
  - name: mycontainer
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      command:
        - "/bin/sh"
        - "-c"
        - "node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait"
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      readinessProbe:
        exec:
          command:
          - "cat"
          - "/tmp/ready"
        periodSeconds: 5
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="start-command"></a>Opdracht starten

De implementatie bevat een `command` eigenschap waarmee een begin opdracht wordt gedefinieerd die wordt uitgevoerd wanneer de container voor het eerst wordt gestart. Deze eigenschap accepteert een matrix met teken reeksen. Met deze opdracht wordt een tijd gesimuleerd wanneer de web-app wordt uitgevoerd, maar de container niet gereed is. 

Eerst wordt een shell-sessie gestart en wordt een `node` opdracht uitgevoerd om de web-app te starten. Er wordt ook een opdracht gestart voor een slaap stand van 240 seconden, waarna een bestand wordt gemaakt `ready` in de `/tmp` map:

```console
node /usr/src/app/index.js & (sleep 240; touch /tmp/ready); wait
```

### <a name="readiness-command"></a>Gereedheids opdracht

Dit YAML-bestand bevat een definitie van een `readinessProbe` die ondersteuning biedt voor een `exec` gereedheids opdracht die fungeert als gereedheids controle. In dit voor beeld wordt de gereedheids opdracht getest op het bestaan van het `ready` bestand in de `/tmp` map.

Als het `ready` bestand niet bestaat, wordt de gereedheids opdracht afgesloten met een waarde die niet gelijk is aan nul. de container blijft actief, maar is niet toegankelijk. Wanneer de opdracht is afgesloten met de afsluit code 0, is de container klaar om te worden geopend. 

De `periodSeconds` eigenschap geeft aan dat de gereedheids opdracht elke vijf seconden moet worden uitgevoerd. De gereedheids test wordt uitgevoerd gedurende de levens duur van de container groep.

## <a name="example-deployment"></a>Voorbeeld implementatie

Voer de volgende opdracht uit om een container groep te implementeren met de voor gaande YAML-configuratie:

```azurecli-interactive
az container create --resource-group myResourceGroup --file readiness-probe.yaml
```

## <a name="view-readiness-checks"></a>Gereedheids controles weer geven

In dit voor beeld mislukt de gereedheids opdracht tijdens de eerste 240 seconden wanneer wordt gecontroleerd of het `ready` bestand bestaat. De status code heeft een signaal geretourneerd dat de container niet gereed is.

Deze gebeurtenissen kunnen worden bekeken vanuit de Azure Portal of Azure CLI. De portal toont bijvoorbeeld gebeurtenissen van `Unhealthy` het type worden geactiveerd wanneer de gereedheids opdracht mislukt. 

![De portal heeft een slechte gebeurtenis][portal-unhealthy]

## <a name="verify-container-readiness"></a>De gereedheid van container controleren

Nadat u de container hebt gestart, kunt u controleren of deze in eerste instantie niet toegankelijk is. Na het inrichten krijgt u het IP-adres van de container groep:

```azurecli
az container show --resource-group myResourceGroup --name readinesstest --query "ipAddress.ip" --out tsv
```

Probeer toegang te krijgen tot de site terwijl de gereedheids test mislukt:

```bash
wget <ipAddress>
```

Uitvoer geeft aan dat de site in eerste instantie niet toegankelijk is:
```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response... 
```

Na 240 seconden is de gereedheids opdracht geslaagd, waarmee wordt aangegeven dat de container gereed is. Wanneer u de `wget` opdracht uitvoert, lukt dit nu:

```
$ wget 192.0.2.1
--2019-10-15 16:46:02--  http://192.0.2.1/
Connecting to 192.0.2.1... connected.
HTTP request sent, awaiting response...200 OK
Length: 1663 (1.6K) [text/html]
Saving to: ‘index.html.1’

index.html.1                       100%[===============================================================>]   1.62K  --.-KB/s    in 0s      

2019-10-15 16:49:38 (113 MB/s) - ‘index.html.1’ saved [1663/1663] 
```

Wanneer de container klaar is, kunt u ook toegang krijgen tot de web-app door te bladeren naar het IP-adres via een webbrowser.

> [!NOTE]
> De gereedheids test blijft actief gedurende de levens duur van de container groep. Als de gereedheids opdracht op een later tijdstip mislukt, wordt de container opnieuw ontoegankelijk. 
> 

## <a name="next-steps"></a>Volgende stappen

Een gereedheids test kan nuttig zijn in scenario's waarbij groepen met meerdere containers die bestaan uit afhankelijke containers. Zie [container groups in azure container instances](container-instances-container-groups.md)voor meer informatie over scenario's met meerdere containers.

<!-- IMAGES -->
[portal-unhealthy]: ./media/container-instances-readiness-probe/readiness-probe-failed.png
