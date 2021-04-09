---
title: Linux-installatie kopieën maken zonder een inrichtings agent
description: Maak generaliseerde Linux-installatie kopieën zonder een inrichtings agent in Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: c7ca147f0a5b907ee0c5c66d53a219fe75ab2179
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551705"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>Gegeneraliseerde installatie kopieën maken zonder een inrichtings agent

Microsoft Azure voorziet in inrichtings agenten voor Linux-Vm's in de vorm van de [walinuxagent](https://github.com/Azure/WALinuxAgent) of [Cloud-init](https://github.com/canonical/cloud-init) (aanbevolen). Maar er kan een scenario zijn wanneer u niet een van deze toepassingen voor uw inrichtings agent wilt gebruiken, zoals:

- Uw Linux distributie/-versie biedt geen ondersteuning voor Cloud-init/Linux-agents.
- U moet specifieke VM-eigenschappen instellen, zoals hostname.

> [!NOTE] 
>
> Als u geen eigenschappen hoeft in te stellen of een vorm van inrichting moet worden, kunt u overwegen een gespecialiseerde installatie kopie te maken.

In dit artikel wordt beschreven hoe u een VM-installatie kopie kunt instellen om te voldoen aan de vereisten van het Azure-platform en de hostnaam in te stellen, zonder een inrichtings agent te installeren.

## <a name="networking-and-reporting-ready"></a>Netwerken en rapporten zijn gereed

Als u wilt dat uw Linux-VM communiceert met Azure-onderdelen, moet u een DHCP-client een host-IP ophalen van het virtuele netwerk, evenals DNS-omzetting en route beheer. De meeste distributies worden met deze hulpprogram ma's out-of-the-box verzonden. Hulpprogram ma's die zijn getest op Azure door Linux distributie-leveranciers zijn onder andere `dhclient` , `network-manager` , `systemd-networkd` en anderen.

> [!NOTE]
>
> Het is momenteel niet mogelijk om algemene installatie kopieën te maken zonder een inrichtings agent die virtuele machines met DHCP ondersteunt.

Nadat het netwerk is ingesteld en geconfigureerd, moet u ' rapport Ready '. Hiermee wordt aangegeven dat Azure is ingericht voor de VM.

> [!IMPORTANT]
>
> Als u gereed voor Azure niet rapporteert, wordt uw VM opnieuw opgestart.

## <a name="demosample"></a>Demo/voor beeld

In deze demo ziet u hoe u een bestaande Marketplace-installatie kopie (in dit geval een Debian Buster-VM) kunt maken en de Linux-agent (walinuxagent) verwijdert, maar ook het meest eenvoudige proces maakt om te rapporteren aan Azure dat de VM ' gereed ' is.

### <a name="create-the-resource-group-and-base-vm"></a>Maak de resource groep en de basis-VM:

```bash
$ az group create --location eastus --name demo1
```

De basis-VM maken:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>De agent voor het inrichten van de installatie kopie verwijderen

Zodra de virtuele machine is ingericht, kunt u er SSH van maken en de Linux-agent verwijderen:

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>Vereiste code toevoegen aan de VM

Omdat we de Azure Linux-agent hebben verwijderd, moeten we ook in de virtuele machine een mechanisme bieden om gereed te melden. 

#### <a name="python-script"></a>Python-script

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = '1'
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>Algemene stappen (zonder python)

Als op uw virtuele machine python niet is geïnstalleerd of beschikbaar is, kunt u deze bovenstaande script logica op een programmatische manier reproduceren met de volgende stappen:

1. Haal de `ContainerId` en `InstanceId` op door de reactie te parseren vanuit de WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate` .

2. Maak de volgende XML-gegevens en Injecteer de geparseerde `ContainerId` en `InstanceId` uit de bovenstaande stap:
   ```xml
   <Health>
     <GoalStateIncarnation>1</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. Deze gegevens plaatsen op WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`

### <a name="automating-running-the-code-at-first-boot"></a>Automatische uitvoering van de code bij eerste keer opstarten

In deze demo wordt gebruikgemaakt van systemed, het meest voorkomende init-systeem in moderne Linux-distributies. De eenvoudigste en meest systeem eigen manier om ervoor te zorgen dat dit voorbereidings mechanisme op het juiste moment wordt uitgevoerd, is door een systeem-service-eenheid te maken. U kunt het volgende eenheids bestand toevoegen aan `/etc/systemd/system` (dit voor beeld is de naam van het eenheids bestand `azure-provisioning.service` ):

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

Deze gesystemte service heeft drie dingen voor basis inrichting:

1. Rapporten die gereed zijn voor Azure (om aan te geven dat deze correct zijn gearriveerd).
1. Hiermee wordt de naam van de virtuele machine op basis van de door de gebruiker opgegeven VM-naam gewijzigd door deze gegevens uit [Azure instance metadata service (IMDS)](./instance-metadata-service.md)op te halen. **Opmerking** IMDS biedt ook andere [meta gegevens voor instanties](./instance-metadata-service.md#access-azure-instance-metadata-service), zoals open bare SSH-sleutels, zodat u meer dan de hostnaam kunt instellen.
1. Wordt uitgeschakeld, zodat deze alleen wordt uitgevoerd bij de eerste keer opstarten en niet bij de volgende keer opnieuw opstarten.

Met de eenheid op het bestands systeem voert u de volgende handelingen uit om het bestand in te scha kelen:

```bash
$ sudo systemctl enable azure-provisioning.service
```

De virtuele machine kan nu worden gegeneraliseerd en er moet een installatie kopie op worden gemaakt. 

#### <a name="completing-the-preparation-of-the-image"></a>De voor bereiding van de installatie kopie volt ooien

Op de ontwikkel computer voert u de volgende handelingen uit om het maken van een installatie kopie voor te bereiden vanuit de basis-VM:

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

En maak de installatie kopie van deze VM:

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

Nu kunt u een nieuwe virtuele machine (of meerdere Vm's) maken op basis van de installatie kopie:

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> Het is belang rijk om `--enable-agent` in te stellen op `false` omdat walinuxagent niet aanwezig is op deze virtuele machine die wordt gemaakt op basis van de installatie kopie.

Deze VM moet worden ingericht. Als u zich aanmeldt bij de nieuwe virtuele machine, kunt u de uitvoer van de gesystemeerde rapport service bekijken:

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>Ondersteuning

Als u uw eigen inrichtings code/agent implementeert, bent u eigenaar van de ondersteuning van deze code. micro soft support onderzoekt alleen problemen met betrekking tot de inrichtings interfaces die niet beschikbaar zijn. We voeren voortdurend verbeteringen en wijzigingen uit op dit gebied, dus u moet controleren op wijzigingen in de Cloud-init en Azure Linux-agent voor het inrichten van API-wijzigingen.

## <a name="next-steps"></a>Volgende stappen

Zie [Linux Provisioning](provisioning.md)(Engelstalig) voor meer informatie.
