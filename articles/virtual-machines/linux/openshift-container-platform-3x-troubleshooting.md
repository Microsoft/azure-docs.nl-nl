---
title: Problemen met de implementatie van open Shift container platform 3,11 in azure oplossen
description: Problemen met de implementatie van open Shift container platform 3,11 in azure oplossen.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: b6dfb4cb697e7d66c7db323df9786ff7ad45e1d7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672324"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Problemen met de implementatie van open Shift container platform 3,11 in azure oplossen

Als het open Shift-cluster niet goed kan worden geïmplementeerd, biedt de Azure Portal de fout uitvoer. Het kan lastig zijn om de uitvoer te lezen, waardoor het moeilijk is om het probleem te identificeren. Scan deze uitvoer snel voor afsluit code 3, 4 of 5. Hieronder vindt u informatie over deze drie afsluit codes:

- Afsluit code 3: de gebruikers naam en het wacht woord of de organisatie-ID/activerings sleutel van het Red Hat-abonnement is onjuist
- Afsluit code 4: de ID van uw Red Hat-groep is onjuist of er zijn geen rechten beschikbaar
- Afsluit code 5: kan docker-volume met smalle pool niet inrichten

Voor alle andere afsluit codes maakt u via SSH verbinding met de host om de logboek bestanden weer te geven.

**OpenShift Container Platform 3.11**

SSH naar de ansible Playbook-host. Voor de sjabloon of de Marketplace-aanbieding gebruikt u de bastion-host. Vanuit de Bastion kunt u SSHen naar alle andere knoop punten in het cluster (Master, infra structuur, CNS, compute). U moet een hoofdmap hebben om de logboek bestanden weer te geven. De hoofdmap is standaard uitgeschakeld voor SSH-toegang. gebruik geen basis voor SSH-naar andere knoop punten.

**OKD**

SSH naar de ansible Playbook-host. Voor de OKD-sjabloon (versie 3,9 en eerder) gebruikt u de host van het hoofd-0. Voor de OKD-sjabloon (versie 3,10 en hoger) gebruikt u de bastion-host. Vanaf de ansible Playbook-host kunt u SSHen naar alle andere knoop punten in het cluster (Master, infra structuur, CNS, compute). Als u de logboek bestanden wilt weer geven, moet u root (sudo su-) zijn. De hoofdmap is standaard uitgeschakeld voor SSH-toegang. gebruik geen basis voor SSH-naar andere knoop punten.

## <a name="log-files"></a>Logboekbestanden

De logboek bestanden (stderr en stdout) voor de host-voorbereidings scripts bevinden zich `/var/lib/waagent/custom-script/download/0` op alle hosts. Als er een fout is opgetreden tijdens de voor bereiding van de host, bekijkt u deze logboek bestanden om de fout te bepalen.

Als de voorbereidings scripts met succes worden uitgevoerd, moeten de logboek bestanden in de `/var/lib/waagent/custom-script/download/1` map van de ansible Playbook-host worden onderzocht. Als de fout is opgetreden tijdens de werkelijke installatie van open Shift, wordt de fout weer gegeven in het stdout-bestand. Gebruik deze informatie om contact op te nemen met de ondersteuning voor verdere ondersteuning.

Voorbeelduitvoer

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

De meest voorkomende fouten tijdens de installatie zijn:

1. Persoonlijke sleutel heeft wachtwoordzin
2. Sleutel kluis geheim met persoonlijke sleutel is niet correct gemaakt
3. De referenties van de Service-Principal zijn onjuist ingevoerd
4. De service-principal heeft geen toegang tot de resource groep.

### <a name="private-key-has-a-passphrase"></a>Persoonlijke sleutel heeft een wachtwoordzin

Er wordt een fout weer geven met de melding dat de machtiging voor SSH is geweigerd. SSH naar de ansible Playbook-host om te controleren op een wachtwoordzin voor de persoonlijke sleutel.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Sleutel kluis geheim met persoonlijke sleutel is niet correct gemaakt

De persoonlijke sleutel wordt gekopieerd naar de ansible Playbook-host ~/.ssh/id_rsa. Bevestig dat dit bestand juist is. Test door een SSH-sessie te openen op een van de cluster knooppunten van de ansible Playbook-host.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>De referenties van de Service-Principal zijn onjuist ingevoerd

Wanneer u de invoer voor de sjabloon of Marketplace-aanbieding opgeeft, zijn de onjuiste gegevens verstrekt. Zorg ervoor dat u de juiste appId (clientId) en het wacht woord (clientSecret) voor de Service-Principal gebruikt. Controleer door de volgende Azure cli-opdracht uit te geven.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>De service-principal heeft geen toegang tot de resource groep.

Als de Azure-Cloud provider is ingeschakeld, moet de Service-Principal toegang hebben tot de resource groep. Controleer door de volgende Azure cli-opdracht uit te geven.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Aanvullende hulpprogramma’s

Voor sommige fouten kunt u ook de volgende opdrachten gebruiken om meer informatie te krijgen:

1. systemctl-status \<service>
2. journalctl-XE
