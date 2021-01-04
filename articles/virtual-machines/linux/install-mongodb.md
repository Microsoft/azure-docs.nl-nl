---
title: MongoDB installeren op een Linux-VM met de Azure CLI
description: Meer informatie over het installeren en configureren van MongoDB op een virtuele Linux-machine iusing de Azure CLI
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: e3bc8ed2745e06096e05f17319a8f7896f87f80f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97702035"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>MongoDB installeren en configureren op een virtuele Linux-machine

[MongoDb](https://www.mongodb.org) is een populaire open-source NoSQL-data base met hoge prestaties. Dit artikel laat u zien hoe u MongoDB kunt installeren en configureren op een virtuele Linux-machine met de Azure CLI. Voor beelden worden weer gegeven met de volgende informatie:

* [Een basis MongoDB-exemplaar hand matig installeren en configureren](#manually-install-and-configure-mongodb-on-a-vm)
* [Een basis MongoDB-exemplaar maken met een resource manager-sjabloon](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Een complex MongoDB Shard-cluster met replica sets maken met behulp van een resource manager-sjabloon](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>MongoDB hand matig installeren en configureren op een VM
MongoDB [bieden installatie-instructies](https://docs.mongodb.com/manual/administration/install-on-linux/) voor Linux distributies, waaronder Red Hat/CENTOS, SuSE, Ubuntu en Debian. In het volgende voor beeld wordt een *CentOS* -VM gemaakt. Als u deze omgeving wilt maken, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voor beeld wordt een virtuele machine met de naam *myVM* gemaakt met de naam *azureuser* met behulp van open bare SSH-sleutel verificatie

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH naar de virtuele machine met uw eigen gebruikers naam en de `publicIpAddress` weer gegeven in de uitvoer van de vorige stap:

```bash
ssh azureuser@<publicIpAddress>
```

Als u de installatie bronnen voor MongoDB wilt toevoegen, maakt u als volgt een **yum** opslagplaats bestand:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Open het MongoDB opslag plaats-bestand om het te bewerken, zoals met `vi` of `nano` . Voeg de volgende regels toe:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Installeer MongoDB met behulp van **yum** als volgt:

```bash
sudo yum install -y mongodb-org
```

SELinux wordt standaard afgedwongen op CentOS-installatie kopieën die voor komen dat u toegang krijgt tot MongoDB. Installeer hulpprogram ma's voor beleids beheer en configureer SELinux zodat MongoDB als volgt kan worden gebruikt voor de standaard TCP-poort 27017:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Start de MongoDB-service als volgt:

```bash
sudo service mongod start
```

Controleer de MongoDB-installatie door verbinding te maken via de lokale `mongo` client:

```bash
mongo
```

Test nu het MongoDB-exemplaar door sommige gegevens toe te voegen en vervolgens te zoeken:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Configureer MongoDB indien gewenst om automatisch te starten tijdens het opnieuw opstarten van het systeem:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Een basis MongoDB-exemplaar maken op CentOS met behulp van een sjabloon
U kunt een basis-MongoDB-exemplaar maken op één CentOS-VM met behulp van de volgende Azure Quick Start-sjabloon van GitHub. Deze sjabloon maakt gebruik van de aangepaste script extensie voor Linux om een **yum** -opslag plaats toe te voegen aan de zojuist gemaakte CENTOS-VM en vervolgens MongoDb te installeren.

* [Basis-MongoDB-exemplaar op CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Als u deze omgeving wilt maken, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index). Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Implementeer vervolgens de sjabloon MongoDB met [AZ Deployment Group Create](/cli/azure/deployment/group). Voer desgevraagd uw eigen unieke waarden in voor *newStorageAccountName*, *dnsNameForPublicIP* en gebruikers naam en wacht woord:

```azurecli
az deployment group create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Meld u aan bij de virtuele machine met behulp van het open bare DNS-adres van uw virtuele machine. U kunt het open bare DNS-adres weer geven met [AZ VM show](/cli/azure/vm):

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

SSH naar uw virtuele machine met uw eigen gebruikers naam en open bare DNS-adres:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Controleer de MongoDB-installatie door als volgt verbinding te maken via de lokale `mongo` client:

```bash
mongo
```

Test nu het exemplaar door gegevens toe te voegen en op de volgende manier te zoeken:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Een complex MongoDB Shard-cluster op CentOS maken met behulp van een sjabloon
U kunt een complex MongoDB Shard-cluster maken met behulp van de volgende Azure Quick Start-sjabloon van GitHub. Deze sjabloon volgt de [Best practices voor MongoDb Shard-cluster](https://docs.mongodb.com/manual/core/sharded-cluster-components/) om redundantie en hoge Beschik baarheid te bieden. Met de sjabloon maakt u twee Shards, met drie knoop punten in elke replicaset. Er wordt ook een replicaset van een configuratie server met drie knoop punten gemaakt, plus twee **mongos** -router servers om consistentie van toepassingen te bieden vanuit de Shards.

* [MongoDB sharding-cluster op CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Voor het implementeren van dit complexe MongoDB Shard-cluster zijn meer dan 20 kernen vereist. Dit is meestal het standaard aantal kernen per regio voor een abonnement. Open een ondersteunings aanvraag voor Azure om het aantal kernen te verhogen.

Als u deze omgeving wilt maken, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index). Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Implementeer vervolgens de sjabloon MongoDB met [AZ Deployment Group Create](/cli/azure/deployment/group). Definieer uw eigen resource namen en grootten, zoals voor *mongoAdminUsername*, *sizeOfDataDiskInGB* en *configNodeVmSize*:

```azurecli
az deployment group create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Deze implementatie kan een uur duren om alle VM-exemplaren te implementeren en te configureren. De `--no-wait` vlag wordt aan het einde van de voor gaande opdracht gebruikt om de besturings element te retour neren naar de opdracht prompt zodra de sjabloon implementatie is geaccepteerd door het Azure-platform. U kunt vervolgens de implementatie status weer geven met [AZ Deployment Group show](/cli/azure/deployment/group). In het volgende voor beeld wordt de status weer gegeven van de *myMongoDBCluster* -implementatie in de resource groep *myResourceGroup* :

```azurecli
az deployment group show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Volgende stappen
In deze voor beelden maakt u lokaal verbinding met het MongoDB-exemplaar van de virtuele machine. Als u verbinding wilt maken met het MongoDB-exemplaar van een andere VM of een ander netwerk, controleert u of de juiste regels voor de [netwerk beveiligings groep zijn gemaakt](nsg-quickstart.md).

In deze voor beelden wordt de kern MongoDB-omgeving voor ontwikkelings doeleinden geïmplementeerd. Pas de vereiste beveiligings configuratie opties voor uw omgeving toe. Zie [MongoDb Security docs](https://docs.mongodb.com/manual/security/)(Engelstalig) voor meer informatie.

Zie [Azure Resource Manager-overzicht](../../azure-resource-manager/management/overview.md)voor meer informatie over het maken van sjablonen.

De Azure Resource Manager sjablonen gebruiken de aangepaste script extensie om scripts op uw Vm's te downloaden en uit te voeren. Zie [de aangepaste script extensie van Azure gebruiken met Linux virtual machines](../extensions/custom-script-linux.md)voor meer informatie.
