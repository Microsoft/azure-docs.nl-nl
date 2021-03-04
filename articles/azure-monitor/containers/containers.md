---
title: Container bewakings oplossing in Azure Monitor | Microsoft Docs
description: Met de container bewakings oplossing in Azure Monitor kunt u uw docker-en Windows-container-hosts op één locatie weer geven en beheren.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/06/2020
ms.openlocfilehash: 59f81103e72be6c0f1b629981fdb53c8467000a9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037418"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Container bewakings oplossing in Azure Monitor

![Containers-symbool](./media/containers/containers-symbol.png)

In dit artikel wordt beschreven hoe u de container bewakings oplossing kunt instellen en gebruiken in Azure Monitor, waarmee u uw docker-en Windows-container-hosts op één locatie kunt weer geven en beheren. Docker is een systeem voor software virtualisatie dat wordt gebruikt voor het maken van containers waarmee de implementatie van software naar hun IT-infra structuur wordt geautomatiseerd.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

De oplossing laat zien welke containers worden uitgevoerd, welke container installatie kopie ze uitvoeren en waar containers worden uitgevoerd. U kunt gedetailleerde controle-informatie weer geven met opdrachten die worden gebruikt met containers. En u kunt problemen met containers oplossen door gecentraliseerde logboeken weer te geven en ernaar te zoeken zonder dat u op afstand docker-of Windows-hosts hoeft weer te geven. U kunt containers vinden die ruis kunnen veroorzaken en overtollige resources op een host gebruiken. En u kunt gecentraliseerde gegevens over CPU, geheugen, opslag en netwerk gebruik en prestaties voor containers weer geven. Op computers waarop Windows wordt uitgevoerd, kunt u Logboeken centraliseren en vergelijken van Windows Server-, Hyper-V-en docker-containers. De oplossing ondersteunt de volgende container-Orchestrator:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Als u containers hebt geïmplementeerd in [Azure service Fabric](../../service-fabric/service-fabric-overview.md), raden we u aan om zowel de [service Fabric oplossing](../../service-fabric/service-fabric-diagnostics-oms-setup.md) als deze oplossing in te scha kelen voor de bewaking van cluster gebeurtenissen. Voordat u de Service Fabric-oplossing inschakelt, raadpleegt u [de service Fabric-oplossing](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) om te begrijpen wat deze biedt en hoe u deze kunt gebruiken.

Zie de [Azure Kubernetes-service bewaken](../containers/container-insights-overview.md)als u geïnteresseerd bent in het bewaken van de prestaties van uw workloads die zijn geïmplementeerd in Kubernetes-omgevingen die worden gehost op Azure Kubernetes service (AKS). De container bewakings oplossing biedt geen ondersteuning voor het bewaken van het platform.  

In het volgende diagram ziet u de relaties tussen verschillende container-hosts en-agents met Azure Monitor.

![Containers diagram](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systeem vereisten en ondersteunde platforms

Voordat u begint, controleert u de volgende gegevens om te controleren of u voldoet aan de vereisten.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Ondersteuning voor container bewakings oplossing voor docker Orchestrator en OS-platform

De volgende tabel bevat een overzicht van de ondersteuning van docker-indeling en besturingssysteem controle van de container inventaris, prestaties en logboeken met Azure Monitor.   

|Docker-indeling | ACS | Linux | Windows | Container<br>Inventaris | Installatiekopie<br>Inventaris | Knooppunt<br>Inventaris | Container<br>Prestaties | Container<br>Gebeurtenis | Gebeurtenis<br>Logboek | Container<br>Logboek |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Service<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat open<br>SHIFT | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>autonome | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-server<br>autonome | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Docker-versies die worden ondersteund in Linux

- Docker 1,11 tot 1,13
- Docker CE en EE v 17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 Linux-distributies die worden ondersteund als container-hosts

- Ubuntu 14,04 LTS en 16,04 LTS
- CoreOS (stabiel)
- Amazon Linux 2016.09.0
- openSUSE 13,2
- openSUSE SCHRIKKEL 42,2
- CentOS 7,2 en 7,3
- SLES 12
- RHEL 7,2 en 7,3
- Red Hat open Shift container platform (OCP) 3,4 en 3,5
- ACS Mesosphere DC/OS 1.7.3 naar 1.8.8
- ACS Kubernetes 1.4.5 tot 1,6
    - Kubernetes-gebeurtenissen, Kubernetes-inventarisatie en container processen worden alleen ondersteund met versie 1.4.1-45 en hoger van de Log Analytics-agent voor Linux
- ACS-docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Ondersteund Windows-besturings systeem

- Windows Server 2016
- Windows 10 jubileum Edition (Professional of ENTER prise)

### <a name="docker-versions-supported-on-windows"></a>Docker-versies die worden ondersteund in Windows

- Docker 1,12 en 1,13
- Docker 17.03.0 en hoger

## <a name="installing-and-configuring-the-solution"></a>De oplossing installeren en configureren

Gebruik de volgende informatie om de oplossing te installeren en configureren.

1. Voeg de container bewakings oplossing toe aan uw Log Analytics-werk ruimte vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) of door gebruik te maken van het proces dat wordt beschreven in [bewakings oplossingen toevoegen van de Oplossingengalerie](../insights/solutions.md).

2. Installeer en gebruik docker met een Log Analytics-agent. Op basis van uw besturings systeem en docker Orchestrator kunt u de volgende methoden gebruiken om uw agent te configureren.
   - Voor zelfstandige hosts:
     - Op ondersteunde Linux-besturings systemen installeert en voert u docker en installeert en configureert u vervolgens de [log Analytics-agent voor Linux](../vm/quick-collect-linux-computer.md).  
     - Op CoreOS kunt u de Log Analytics-agent voor Linux niet uitvoeren. In plaats daarvan voert u een container versie van de Log Analytics-agent voor Linux uit. Bekijk de Linux-container hosts, waaronder CoreOS of Azure Government Linux-container hosts, inclusief CoreOS als u met containers in Azure Government Cloud werkt.
     - Installeer op Windows Server 2016 en Windows 10 de docker-engine en de client vervolgens verbinding maken met een agent om informatie te verzamelen en deze naar Azure Monitor te verzenden. Controleer de [installatie en configuratie van Windows-container-hosts](#install-and-configure-windows-container-hosts) als u een Windows-omgeving hebt.
   - Voor de indeling van docker multi-host:
     - Als u een Red Hat open Shift-omgeving hebt, raadpleegt u Configure a Log Analytics agent for Red Hat open SHIFT.
     - Als u een Kubernetes-cluster hebt met behulp van de Azure Container Service:
       - Bekijk [een log Analytics Linux-agent configureren voor Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Bekijk [een log Analytics Windows-agent configureren voor Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Raadpleeg use helm om Log Analytics-agent te implementeren op Linux Kubernetes.
     - Als u een Azure Container Service DC/OS-cluster hebt, kunt u meer informatie vinden op [een Azure container service DC/OS-cluster met Azure monitor bewaken](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms).
     - Als u beschikt over een docker Swarm-omgeving, kunt u meer informatie vinden op een Log Analytics agent configureren voor docker Swarm.
     - Als u een Service Fabric cluster hebt, kunt u meer informatie vinden op [containers controleren met Azure monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Raadpleeg de [docker-engine in Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon) -artikel voor meer informatie over het installeren en configureren van uw docker-engines op computers met Windows.

> [!IMPORTANT]
> Docker moet worden uitgevoerd **voordat** u de [log Analytics-agent voor Linux](../vm/quick-collect-linux-computer.md) installeert op de container-hosts. Als u de agent al hebt geïnstalleerd voordat u docker installeert, moet u de Log Analytics-agent voor Linux opnieuw installeren. Zie de [docker-website](https://www.docker.com)voor meer informatie over docker.

### <a name="install-and-configure-linux-container-hosts"></a>Linux-container-hosts installeren en configureren

Nadat u docker hebt geïnstalleerd, gebruikt u de volgende instellingen voor de container-host om de agent te configureren voor gebruik met docker. Eerst hebt u de Log Analytics werk ruimte-ID en-sleutel nodig die u in de Azure Portal kunt vinden. Klik in uw werk ruimte op **snel starten**  >  **computers** om uw **werk ruimte-id** en **primaire sleutel** weer te geven.  Kopieer en plak beide in uw favoriete editor.

**Voor alle Linux-container hosts, met uitzonde ring van CoreOS:**

- Zie [log Analytics agent Overview](../agents/log-analytics-agent.md)voor meer informatie en de stappen voor het installeren van de log Analytics-agent voor Linux.

**Voor alle Linux-container hosts, waaronder CoreOS:**

Start de container die u wilt bewaken. Wijzig en gebruik het volgende voor beeld:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Voor alle Azure Government Linux-container hosts, waaronder CoreOS:**

Start de container die u wilt bewaken. Wijzig en gebruik het volgende voor beeld:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Overschakelen van het gebruik van een geïnstalleerde Linux-agent naar een in een container**

Als u eerder de rechtstreeks geïnstalleerde agent hebt gebruikt en in plaats daarvan een agent wilt gebruiken die in een container wordt uitgevoerd, moet u eerst de Log Analytics-agent voor Linux verwijderen. Zie [de log Analytics-agent voor Linux](../vm/quick-collect-linux-computer.md) verwijderen voor meer informatie over het verwijderen van de agent.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Een Log Analytics-agent configureren voor docker Swarm

U kunt de Log Analytics-agent uitvoeren als een globale service op docker Swarm. Gebruik de volgende informatie om een Log Analytics Agent-service te maken. U moet uw Log Analytics werk ruimte-ID en primaire sleutel opgeven.

- Voer de volgende handelingen uit op het hoofd knooppunt.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Beveiligde geheimen voor docker Swarm

Gebruik voor docker Swarm de volgende informatie om uw geheime gegevens te maken zodra het geheim voor de werk ruimte-ID en de primaire sleutel is gemaakt.

1. Voer de volgende handelingen uit op het hoofd knooppunt.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Controleer of de geheimen goed zijn gemaakt.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Voer de volgende opdracht uit om de geheimen te koppelen aan de container Log Analytics agent.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Een Log Analytics-agent voor Red Hat open Shift configureren

Er zijn drie manieren om de Log Analytics-agent toe te voegen aan Red Hat open Shift om het verzamelen van gegevens over container bewaking te starten.

* [De log Analytics-agent voor Linux](../vm/quick-collect-linux-computer.md) rechtstreeks op elk open Shift-knoop punt installeren  
* [Log Analytics VM-extensie inschakelen](../vm/quick-collect-azurevm.md) op elk open Shift-knoop punt dat zich bevindt in azure  
* De Log Analytics-agent installeren als een open Shift-daemon-set  

In deze sectie worden de stappen beschreven die nodig zijn om de Log Analytics-agent te installeren als een open Shift-daemon-set.  

1. Meld u aan bij het knoop punt open Shift Master en kopieer het yaml [-bestand OCP-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) van github naar het hoofd knooppunt en wijzig de waarde met uw log Analytics werk ruimte-id en met uw primaire sleutel.
2. Voer de volgende opdrachten uit om een project te maken voor Azure Monitor en het gebruikers account in te stellen.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Voer de volgende handelingen uit om de daemon-set te implementeren:

    `oc create -f ocp-omsagent.yaml`

4. Als u wilt controleren of het geconfigureerde en correct werkt, typt u het volgende:

    `oc describe daemonset omsagent`  

    en de uitvoer moet er als volgt uitzien:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Voer de volgende stappen uit als u geheimen wilt gebruiken om uw Log Analytics werk ruimte-ID en primaire sleutel te beveiligen wanneer u het Log Analytics agent daemon-set yaml-bestand gebruikt.

1. Meld u aan bij het knoop punt openshift Master en kopieer het yaml [-bestand OCP-DS-omsagent. yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) en het script voor het genereren van een geheim [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) uit github.  Met dit script wordt het yaml-bestand geheimen voor Log Analytics werk ruimte-ID en primaire sleutel gegenereerd voor het beveiligen van uw geheime gegevens.  
2. Voer de volgende opdrachten uit om een project te maken voor Azure Monitor en het gebruikers account in te stellen. Het geheime genererende script vraagt om uw Log Analytics werk ruimte-ID `<WSID>` en primaire sleutel `<KEY>` en na voltooiing wordt het bestand OCP-Secret. yaml gemaakt.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Implementeer het geheime bestand door het volgende uit te voeren:

    `oc create -f ocp-secret.yaml`

4. Controleer de implementatie door de volgende handelingen uit te voeren:

    `oc describe secret omsagent-secret`  

    en de uitvoer moet er als volgt uitzien:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. Implementeer het Log Analytics agent daemon-set yaml-bestand door het volgende uit te voeren:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Controleer de implementatie door de volgende handelingen uit te voeren:

    `oc describe ds oms`

    en de uitvoer moet er als volgt uitzien:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Een Log Analytics Linux-agent configureren voor Kubernetes

Voor Kubernetes gebruikt u een script om het geheimen yaml-bestand te genereren voor uw werk ruimte-ID en primaire sleutel om de Log Analytics-agent voor Linux te installeren. Op de [log Analytics pagina docker Kubernetes github](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) zijn bestanden die u kunt gebruiken met of zonder uw geheime gegevens.

- De standaard Log Analytics agent voor Linux Daemonset heeft geen geheime informatie (omsagent. yaml)
- Het yaml-bestand van de Log Analytics-agent voor Linux Daemonset gebruikt geheime informatie (omsagent-DS-geheimen. yaml) met geheime generatie scripts voor het genereren van het bestand geheimen yaml (omsagentsecret. yaml).

U kunt ervoor kiezen om omsagent DaemonSets te maken met of zonder geheimen.

**Standaard OMSagent Daemonset yaml-bestand zonder geheimen**

- Voor het standaard Log Analytics agent daemon yaml-bestand, vervangt `<WSID>` u de en `<KEY>` naar uw WSID en sleutel. Kopieer het bestand naar het hoofd knooppunt en voer de volgende handelingen uit:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Standaard OMSagent Daemonset yaml-bestand met geheimen**

1. Als u Log Analytics agent daemon wilt gebruiken met behulp van geheime gegevens, maakt u eerst de geheimen.
    1. Kopieer het script-en geheim sjabloon bestand en zorg ervoor dat het zich in dezelfde map bevindt.
        - Script voor het genereren van geheimen-secret-gen.sh
        - geheime sjabloon-geheim: sjabloon. yaml
    2. Voer het script uit, zoals in het volgende voor beeld. Het script vraagt om de Log Analytics werk ruimte-ID en primaire sleutel en nadat u ze hebt ingevoerd, maakt het script een geheim yaml-bestand zodat u het kunt uitvoeren.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Maak de geheimen pod door het volgende uit te voeren:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Voer de volgende handelingen uit om te controleren:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        De uitvoer moet er ongeveer als volgt uitzien:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        De uitvoer moet er ongeveer als volgt uitzien:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Uw omsagent-daemon maken-set door uit te voeren ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Controleer of de Log Analytics agent Daemonset wordt uitgevoerd, vergelijkbaar met het volgende:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Voor Kubernetes gebruikt u een script om het geheimen yaml-bestand te genereren voor de werk ruimte-ID en de primaire sleutel voor de Log Analytics-agent voor Linux. Gebruik de volgende voorbeeld informatie met het [omsagent yaml-bestand](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) om uw geheime gegevens te beveiligen.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Een Log Analytics Windows-agent configureren voor Kubernetes

Voor Windows Kubernetes gebruikt u een script om het geheimen yaml-bestand te genereren voor uw werk ruimte-ID en primaire sleutel om de Log Analytics-agent te installeren. Op de [log Analytics pagina docker Kubernetes github](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) zijn bestanden die u kunt gebruiken met uw geheime gegevens.  U moet de Log Analytics agent afzonderlijk installeren voor de hoofd-en agent knooppunten.  

1. Als u Log Analytics agent daemon wilt gebruiken met behulp van geheime gegevens op het hoofd knooppunt, meldt u zich aan en maakt u eerst de geheimen.
    1. Kopieer het script-en geheim sjabloon bestand en zorg ervoor dat het zich in dezelfde map bevindt.
        - Script voor het genereren van geheimen-secret-gen.sh
        - geheime sjabloon-geheim: sjabloon. yaml

    2. Voer het script uit, zoals in het volgende voor beeld. Het script vraagt om de Log Analytics werk ruimte-ID en primaire sleutel en nadat u ze hebt ingevoerd, maakt het script een geheim yaml-bestand zodat u het kunt uitvoeren.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Uw omsagent-daemon maken-set door uit te voeren ```kubectl create -f omsagentsecret.yaml```
    4. Als u wilt controleren, voert u de volgende handelingen uit:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        De uitvoer moet er ongeveer als volgt uitzien:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Uw omsagent-daemon maken-set door uit te voeren ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Controleer of de Log Analytics agent Daemonset wordt uitgevoerd, vergelijkbaar met het volgende:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Volg de stappen in de sectie [Windows-container-hosts installeren en configureren](#install-and-configure-windows-container-hosts)als u de agent wilt installeren op het worker-knoop punt waarop Windows wordt uitgevoerd.

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Helm gebruiken om Log Analytics-agent te implementeren op Linux Kubernetes

Voer de volgende stappen uit om helm te gebruiken om Log Analytics-agent te implementeren in uw Linux Kubernetes-omgeving.

1. Uw omsagent-daemon maken-set door uit te voeren ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. De resultaten ziet er ongeveer als volgt uit:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. U kunt de status van de omsagent controleren door uit te voeren: ```helm status "omsagent"``` en de uitvoer ziet er ongeveer als volgt uit:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    Ga voor meer informatie naar [container Solution helm Chart](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Windows-container-hosts installeren en configureren

Gebruik de informatie in de sectie voor het installeren en configureren van Windows-container-hosts.

#### <a name="preparation-before-installing-windows-agents"></a>Voor bereiding voor het installeren van Windows-agents

Voordat u agents installeert op computers waarop Windows wordt uitgevoerd, moet u de docker-service configureren. Met de configuratie kan de Windows-agent of de Azure Monitor virtuele-machine-extensie de docker TCP-socket gebruiken, zodat de agents extern toegang hebben tot de docker-daemon en gegevens kunnen vastleggen voor bewaking.

##### <a name="to-configure-the-docker-service"></a>De docker-service configureren  

Voer de volgende Power shell-opdrachten uit om TCP-pipe en named pipe voor Windows Server in te scha kelen:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Zie [docker-engine in Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon)voor meer informatie over de configuratie van de docker-daemon die wordt gebruikt met Windows-containers.

#### <a name="install-windows-agents"></a>Windows-agents installeren

Als u Windows-en Hyper-V-container bewaking wilt inschakelen, installeert u micro soft Monitoring Agent (MMA) op Windows-computers die als container worden gehost. Zie [Windows-computers verbinden met Azure monitor](../agents/agent-windows.md)voor computers met Windows in uw on-premises omgeving. Voor virtuele machines die worden uitgevoerd in azure verbindt u deze met Azure Monitor met behulp van de extensie van de [virtuele machine](../vm/quick-collect-azurevm.md).

U kunt Windows-containers bewaken die worden uitgevoerd op Service Fabric. Alleen [virtuele machines die worden uitgevoerd in azure](../vm/quick-collect-azurevm.md) en [computers met Windows in uw on-premises omgeving](../agents/agent-windows.md) worden momenteel ondersteund voor service Fabric.

U kunt controleren of de container monitoring-oplossing correct is ingesteld voor Windows. Zoek naar *ContainerManagement.xxx* om te controleren of de Management Pack correct is gedownload. De bestanden moeten zich in de map C:\Program Files\Microsoft monitoring Agent\Agent\Health service State\Management packs bevindt.

## <a name="solution-components"></a>Oplossingsonderdelen

Ga vanuit het Azure Portal naar de *Oplossingengalerie* en voeg de **container bewakings oplossing** toe. Als u Windows-agents gebruikt, wordt de volgende management pack geïnstalleerd op elke computer met een agent wanneer u deze oplossing toevoegt. Er is geen configuratie of onderhoud vereist voor de management pack.

- *ContainerManagement.xxx* geïnstalleerd in C:\Program Files\Microsoft monitoring Agent\Agent\Health service State\Management packs

## <a name="container-data-collection-details"></a>Details van container gegevens verzameling

De oplossing voor container bewaking verzamelt diverse metrische gegevens over prestaties en registreert informatie uit container hosts en containers met de agents die u inschakelt.

De gegevens worden om de drie minuten verzameld door de volgende agent typen.

- [Log Analytics-agent voor Linux](../vm/quick-collect-linux-computer.md)
- [Windows-agent](../agents/agent-windows.md)
- [VM-extensie Log Analytics](../vm/quick-collect-azurevm.md)

### <a name="container-records"></a>Container records

De volgende tabel bevat voor beelden van records die zijn verzameld door de container bewakings oplossing en de gegevens typen die worden weer gegeven in de zoek resultaten van de logboeken.

| Gegevenstype | Gegevens type in zoeken in Logboeken | Velden |
| --- | --- | --- |
| Prestaties voor hosts en containers | `Perf` | Computer, ObjectName, CounterName &#40;% processor tijd, schijf lezen MB, schijf schrijf bewerkingen, geheugen gebruik MB, ontvangen bytes van het netwerk, verzonden bytes van de CPU, processor gebruik sec, netwerk&#41;, CounterValue, TimeGenerated, CounterPath, hebben |
| Container voorraad | `ContainerInventory` | TimeGenerated, computer, container naam, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, hebben, ContainerID, ImageID |
| Inventaris van container installatie kopie | `ContainerImageInventory` | TimeGenerated, computer, Image, ImageTag, ImageSize, VirtualSize, actief, onderbroken, gestopt, mislukt, hebben, ImageID, TotalContainer |
| Container logboek | `ContainerLog` | TimeGenerated, computer, afbeeldings-ID, container naam, LogEntrySource, LogEntry, hebben, ContainerID |
| Container service-logboek | `ContainerServiceLog`  | TimeGenerated, computer, TimeOfCommand, Image, opdracht, hebben, ContainerID |
| Container-knooppunt inventaris | `ContainerNodeInventory_CL`| TimeGenerated, computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, hebben|
| Kubernetes-inventaris | `KubePodInventory_CL` | TimeGenerated, computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, hebben |
| Container proces | `ContainerProcess_CL` | TimeGenerated, computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, hebben |
| Kubernetes-gebeurtenissen | `KubeEvents_CL` | TimeGenerated, computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, hebben, bericht |

Labels die zijn toegevoegd aan *PodLabel* -gegevens typen zijn uw eigen aangepaste labels. De namen van de toegevoegde PodLabel die in de tabel worden weer gegeven, zijn voor beelden. Dus,,, `PodLabel_deployment_s` `PodLabel_deploymentconfig_s` `PodLabel_docker_registry_s` verschilt van de gegevensverzameling van uw omgeving en lijkt algemeen op elkaar `PodLabel_yourlabel_s` .

## <a name="monitor-containers"></a>Containers bewaken
Nadat u de oplossing hebt ingeschakeld in de Azure Portal, toont de tegel **containers** samenvattings informatie over de container-hosts en de containers die worden uitgevoerd in hosts.

![Tegel containers](./media/containers/containers-title.png)

De tegel toont een overzicht van het aantal containers dat u in de omgeving hebt en of deze zijn mislukt, worden uitgevoerd of gestopt.

### <a name="using-the-containers-dashboard"></a>Het dash board containers gebruiken

Klik op de tegel **containers** . Hier ziet u weer gaven die zijn ingedeeld op:

- **Container gebeurtenissen** -container status en computers met mislukte containers weer geven.
- **Container logboeken** : toont een grafiek van de container logboek bestanden die in de loop van de tijd worden gegenereerd en een lijst met computers met het hoogste aantal logboek bestanden.
- **Kubernetes-gebeurtenissen** : toont een grafiek van Kubernetes-gebeurtenissen die zijn gegenereerd gedurende een bepaalde periode en een lijst van de redenen waarom de gebeurtenissen door peul zijn gegenereerd. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Kubernetes-naam ruimte inventaris** : toont het aantal naam ruimten en het gehele getal en toont de hiërarchie. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Container knooppunt inventaris** : toont het aantal indelings typen dat wordt gebruikt op container knooppunten/-hosts. De computer knooppunten/-hosts worden ook weer gegeven op het aantal containers. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Inventaris van container installatie kopieën** : hier wordt het totale aantal gebruikte container installatie kopieën en het aantal afbeeldings typen weer gegeven. Het aantal installatie kopieën wordt ook vermeld in de afbeeldings code.
- **Status van containers** : toont het totale aantal container knooppunten/hostcomputers waarop containers worden uitgevoerd. Computers worden ook weer gegeven op het aantal actieve hosts.
- **Container proces** : toont een lijn diagram van container processen die gedurende een periode worden uitgevoerd. Containers worden ook weer gegeven door opdracht/proces binnen containers uit te voeren. *Deze gegevensset wordt alleen gebruikt in Linux-omgevingen.*
- **Prestaties van de container CPU** : toont een lijn diagram van het gemiddelde CPU-gebruik gedurende een bepaalde periode voor computer knooppunten/-hosts. Bevat ook een lijst met de computer knooppunten/-hosts op basis van het gemiddelde CPU-gebruik.
- **Prestaties van container geheugen** : toont een lijn diagram van het geheugen gebruik gedurende een bepaalde periode. Geeft ook het geheugen gebruik van de computer weer op basis van de naam van het exemplaar.
- **Computer prestaties** : toont lijn diagrammen van het percentage CPU-prestaties gedurende een bepaalde periode, percentage van het geheugen gebruik gedurende een bepaalde periode en MB aan vrije schijf ruimte na verloop van tijd. U kunt de muis aanwijzer boven een wille keurige lijn in een grafiek plaatsen om meer details weer te geven.

Elk gebied van het dash board is een visuele representatie van een zoek opdracht die wordt uitgevoerd op verzamelde gegevens.

![Scherm opname van een dash board om de verzamelde gegevens weer te geven. ](./media/containers/containers-dash01.png)

![Dash board voor containers](./media/containers/containers-dash02.png)

Klik in het gebied **container status** op het bovenste gedeelte, zoals hieronder wordt weer gegeven.

![Status van containers](./media/containers/containers-status.png)

Log Analytics wordt geopend, waarin informatie over de status van uw containers wordt weer gegeven.

![Log Analytics voor containers](./media/containers/containers-log-search.png)

Hier kunt u de zoek query bewerken om deze te wijzigen om de specifieke informatie te vinden waarin u geïnteresseerd bent. Zie [logboek query's in azure monitor](../logs/log-query-overview.md)voor meer informatie over logboek query's.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Problemen oplossen door een mislukte container te vinden

Log Analytics een container als **mislukt** markeert als deze is afgesloten met een afsluit code die niet gelijk is aan nul. In het gebied **mislukte containers** ziet u een overzicht van de fouten en fouten in de omgeving.

### <a name="to-find-failed-containers"></a>Defecte containers zoeken

1. Klik op het **status gebied container** .  
   ![status van containers](./media/containers/containers-status.png)
2. Log Analytics wordt geopend en de status van uw containers wordt weer gegeven, zoals in het volgende voor beeld.  
   ![status van containers](./media/containers/containers-log-search.png)
3. Vouw de fout regel uit en klik op + om de criteria aan de query toe te voegen. Vervolgens voert u een opmerking uit op de regel samenvatten in de query.
   ![Scherm opname van de regel die moet worden weer gegeven als commentaar.](./media/containers/containers-state-failed-select.png)  
1. Voer de query uit en vouw vervolgens een regel uit in de resultaten om de afbeeldings-ID weer te geven.  
   ![Scherm afbeelding die laat zien hoe de installatie kopie-ID wordt weer gegeven.](./media/containers/containers-state-failed.png)  
1. Typ het volgende in de logboek query. `ContainerImageInventory | where ImageID == <ImageID>` om details weer te geven over de afbeelding, zoals afbeeldings grootte en aantal gestopte en mislukte installatie kopieën.  
   ![mislukte containers](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Query logboeken voor container gegevens

Wanneer u een specifieke fout wilt oplossen, kunt u zien waar deze zich voordoen in uw omgeving. De volgende logboek typen helpen u bij het maken van query's om de gewenste informatie te retour neren.

- **ContainerImageInventory** : gebruik dit type als u wilt zoeken naar informatie die is ingedeeld op afbeelding en om afbeeldings gegevens zoals afbeeldings-id's of-groottes weer te geven.
- **ContainerInventory** : gebruik dit type als u informatie wilt over de container locatie, wat hun namen zijn en welke installatie kopieën ze uitvoeren.
- **ContainerLog** : gebruik dit type als u specifieke informatie en vermeldingen in het fouten logboek wilt zoeken.
- **ContainerNodeInventory_CL**  Gebruik dit type als u wilt dat de informatie over de host of het knoop punt waar de containers zich bevinden. Het biedt u docker-versie, indelings type, opslag en netwerk gegevens.
- **ContainerProcess_CL** Gebruik dit type om snel te zien hoe het proces wordt uitgevoerd in de container.
- **ContainerServiceLog** : gebruik dit type als u informatie wilt vinden over de audit trail voor de docker-daemon, zoals starten, stoppen, verwijderen of pull-opdrachten.
- **KubeEvents_CL**  Gebruik dit type om de Kubernetes-gebeurtenissen te bekijken.
- **KubePodInventory_CL**  Gebruik dit type als u inzicht wilt krijgen in de gegevens van de cluster hiërarchie.

### <a name="to-query-logs-for-container-data"></a>Logboeken voor container gegevens opvragen

* Kies een afbeelding waarvan u weet dat deze onlangs is mislukt en zoek de fouten logboeken hierop. Zoek eerst de naam van een container met de installatie kopie met een **ContainerInventory** zoeken. Zoek bijvoorbeeld naar `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Zoeken naar Ubuntu-containers](./media/containers/search-ubuntu.png)

  Vouw een rij in de resultaten uit om de Details voor die container weer te geven.

## <a name="example-log-queries"></a>Voorbeeld logboek query's

Het is vaak handig om query's te bouwen, te beginnen met een voor beeld of twee en deze vervolgens te wijzigen in uw omgeving. Als uitgangs punt kunt u experimenteren met het gebied met **voorbeeld query's** aan de rechter kant van de oplossings pagina, om u te helpen bij het bouwen van geavanceerdere query's.

![Container query's](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Logboek query's opslaan

Het opslaan van query's is een standaard functie in Azure Monitor. Door ze op te slaan, hebt u die voor toekomstig gebruik nuttig handig voor u.

Nadat u een query hebt gemaakt die u nuttig vindt, slaat u deze op door te klikken op **Favorieten** boven aan de zoek pagina voor Logboeken. U kunt deze later eenvoudig openen vanaf de pagina **mijn dash board** .

## <a name="next-steps"></a>Volgende stappen

[Query logboeken](../logs/log-query-overview.md) om gedetailleerde container gegevens records weer te geven.

