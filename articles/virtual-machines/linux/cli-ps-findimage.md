---
title: Informatie over het aankoop plan van Marketplace zoeken en gebruiken met de CLI
description: Meer informatie over het gebruik van de Azure CLI voor het vinden van image URNs-en Purchase Plan-para meters, zoals de uitgever, aanbieding, SKU en versie, voor VM-installatie kopieën van Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 70cb4cc54c6f9a376d3bd38dc8bb6cd3a059a20c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022838"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Informatie over Azure Marketplace-installatie kopieën zoeken met behulp van Azure CLI

In dit onderwerp wordt beschreven hoe u de Azure CLI gebruikt om VM-installatie kopieën te vinden in de Azure Marketplace. Gebruik deze informatie om een Marketplace-installatie kopie op te geven wanneer u via een programma een virtuele machine maakt met de CLI, Resource Manager-sjablonen of andere hulpprogram ma's.

U kunt ook bladeren in beschik bare installatie kopieën en aanbiedingen met behulp van [Azure Marketplace](https://azuremarketplace.microsoft.com/) of  [Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologie

Een Marketplace-installatie kopie in azure heeft de volgende kenmerken:

* **Uitgever**: de organisatie die de installatie kopie heeft gemaakt. Voorbeelden: Canonical, MicrosoftWindowsServer
* **Aanbieding**: de naam van een groep gerelateerde installatie kopieën die door een uitgever zijn gemaakt. Voor beelden: UbuntuServer, WindowsServer
* **SKU**: een exemplaar van een aanbieding, zoals een belang rijke release van een distributie. Voor beelden: 18,04-LTS, 2019-Data Center
* **Versie**: het versie nummer van een afbeeldings-SKU. 

Deze waarden kunnen afzonderlijk of als afbeelding *urn* worden door gegeven, waarbij de waarden worden gecombineerd die worden gescheiden door de dubbele punt (:). Bijvoorbeeld: *Uitgever*:*aanbieding*:*SKU*:*versie*. U kunt het versie nummer in de URN vervangen door `latest` de nieuwste versie van de installatie kopie te gebruiken. 

Als de uitgever van de installatie kopie aanvullende licentie-en aankoop voorwaarden biedt, moet u deze accepteren voordat u de installatie kopie kunt gebruiken.  Zie [de informatie over het aankoop plan controleren](#check-the-purchase-plan-information)voor meer informatie.



## <a name="list-popular-images"></a>Populaire installatie kopieën weer geven

Voer de opdracht [AZ VM Image List](/cli/azure/vm/image) uit zonder de `--all` optie om een lijst met populaire VM-installatie kopieën in azure Marketplace weer te geven. Voer bijvoorbeeld de volgende opdracht uit om een lijst met populaire afbeeldingen in de cache weer te geven in tabel indeling:

```azurecli
az vm image list --output table
```

De uitvoer bevat de afbeelding URN. U kunt ook de *UrnAlias* gebruiken. Dit is een verkorte versie die is gemaakt voor populaire installatie kopieën zoals *UbuntuLTS*.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
debian-10      Debian                  10                  Debian:debian-10:10:latest                                      Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-LVM               RedHat:RHEL:7-LVM:latest                                        RHEL                 latest
SLES           SUSE                    15                  SUSE:SLES:15:latest                                             SLES                 latest
UbuntuServer   Canonical               18.04-LTS           Canonical:UbuntuServer:18.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2019-Datacenter     MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest     Win2019Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

## <a name="find-specific-images"></a>Specifieke installatiekopieën zoeken

Als u een specifieke VM-installatie kopie in Marketplace wilt zoeken, gebruikt u de `az vm image list` opdracht met de `--all` optie. Het duurt enige tijd voordat deze versie van de opdracht is voltooid en kan een langdurige uitvoer retour neren, dus u kunt de lijst doorgaans filteren op `--publisher` of een andere para meter. 

Met de volgende opdracht worden bijvoorbeeld alle Debian-aanbiedingen weer gegeven (Houd er rekening mee dat zonder de `--all` Switch alleen de lokale cache van algemene installatie kopieën wordt doorzocht):

```azurecli
az vm image list --offer Debian --all --output table 
```

Gedeeltelijke uitvoer: 

```output
Offer                                    Publisher                         Sku                                      Urn                                                                                                   Version
---------------------------------------  --------------------------------  ---------------------------------------  ----------------------------------------------------------------------------------------------------  --------------
apache-solr-on-debian                    apps-4-rent                       apache-solr-on-debian                    apps-4-rent:apache-solr-on-debian:apache-solr-on-debian:1.0.0                                         1.0.0
atomized-h-debian10-v1                   atomizedinc1587939464368          hdebian10plan                            atomizedinc1587939464368:atomized-h-debian10-v1:hdebian10plan:1.0.0                                   1.0.0
atomized-h-debian9-v1                    atomizedinc1587939464368          hdebian9plan                             atomizedinc1587939464368:atomized-h-debian9-v1:hdebian9plan:1.0.0                                     1.0.0
atomized-r-debian10-v1                   atomizedinc1587939464368          rdebian10plan                            atomizedinc1587939464368:atomized-r-debian10-v1:rdebian10plan:1.0.0                                   1.0.0
atomized-r-debian9-v1                    atomizedinc1587939464368          rdebian9plan                             atomizedinc1587939464368:atomized-r-debian9-v1:rdebian9plan:1.0.0                                     1.0.0
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.7                         1.0.7
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.8                         1.0.8
cis-debian-linux-10-l1                   center-for-internet-security-inc  cis-debian10-l1                          center-for-internet-security-inc:cis-debian-linux-10-l1:cis-debian10-l1:1.0.9                         1.0.9
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.18                          1.0.18
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.19                          1.0.19
cis-debian-linux-9-l1                    center-for-internet-security-inc  cis-debian9-l1                           center-for-internet-security-inc:cis-debian-linux-9-l1:cis-debian9-l1:1.0.20                          1.0.20
apache-web-server-with-debian-10         cognosys                          apache-web-server-with-debian-10         cognosys:apache-web-server-with-debian-10:apache-web-server-with-debian-10:1.2019.1008                1.2019.1008
docker-ce-with-debian-10                 cognosys                          docker-ce-with-debian-10                 cognosys:docker-ce-with-debian-10:docker-ce-with-debian-10:1.2019.0710                                1.2019.0710
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201602010                                                                       8.0.201602010
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201603020                                                                       8.0.201603020
Debian                                   credativ                          8                                        credativ:Debian:8:8.0.201604050                                                                       8.0.201604050
...
```


## <a name="look-at-all-available-images"></a>Alle beschik bare installatie kopieën bekijken
 
Een andere manier om een installatie kopie te vinden op een locatie is het uitvoeren van de [AZ VM Image List-Publishers](/cli/azure/vm/image), [AZ VM Image List-aanbiedingen](/cli/azure/vm/image)en [AZ VM Image List-sku's-](/cli/azure/vm/image) opdrachten in volg orde. Met deze opdrachten bepaalt u deze waarden:

1. De installatie kopie-uitgevers voor een locatie weer geven. In dit voor beeld bekijken we de regio *VS-West* .
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Geef de aanbiedingen voor een bepaalde uitgever weer. In dit voor beeld voegen we *canoniek* toe als de uitgever.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Geef de SKU's voor een bepaalde aanbieding weer. In dit voor beeld voegen we *UbuntuServer* toe als de aanbieding.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Voor een bepaalde uitgever, aanbieding en SKU, worden alle versies van de installatie kopie weer gegeven. In dit voor beeld voegen we *18,04-LTS* toe als de SKU.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Geef deze waarde van de kolom URN door met de `--image` para meter bij het maken van een virtuele machine met de opdracht [AZ VM Create](/cli/azure/vm) . U kunt ook het versie nummer in de URN vervangen door ' laatste ', zodat u gewoon de meest recente versie van de installatie kopie kunt gebruiken. 

Als u een virtuele machine implementeert met een resource manager-sjabloon, stelt u de installatie kopie parameters afzonderlijk in de `imageReference` Eigenschappen in. Zie de [sjabloonverwijzing](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>De informatie van het aankoop plan controleren

Sommige VM-installatie kopieën in de Azure Marketplace hebben aanvullende licentie-en aankoop voorwaarden die u moet accepteren voordat u ze via een programma kunt implementeren.  

Als u een virtuele machine van een dergelijke installatie kopie wilt implementeren, moet u de voor waarden van de installatie kopie accepteren wanneer u deze voor de eerste keer gebruikt, eenmaal per abonnement. U moet ook de para meters van het *aankoop plan* opgeven om een virtuele machine te implementeren vanuit die installatie kopie

Als u de gegevens van het aankoop plan van een afbeelding wilt weer geven, voert u de opdracht [AZ VM Image Show](/cli/azure/image) uit met de urn van de installatie kopie. Als de `plan` eigenschap in de uitvoer niet is `null` , heeft de installatie kopie de voor waarden die u moet accepteren voordat u een programmatische implementatie uitvoert.

De canonieke Ubuntu Server 18,04 LTS-installatie kopie heeft bijvoorbeeld geen aanvullende voor waarden, omdat de `plan` gegevens er als volgt uitziet `null` :

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Uitvoer:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Als u een vergelijk bare opdracht uitvoert voor de RabbitMQ die door de BitNami-installatie kopie wordt gecertificeerd, worden de volgende eigenschappen weer gegeven `plan` : `name` , `product` en `publisher` . (Sommige installatie kopieën hebben ook een `promotion code` eigenschap.) 

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Uitvoer:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

Als u deze installatie kopie wilt implementeren, moet u de voor waarden accepteren en de para meters van het aankoop plan opgeven wanneer u een virtuele machine implementeert met behulp van die installatie kopie.

## <a name="accept-the-terms"></a>De gebruiksvoorwaarden accepteren

Als u de licentie voorwaarden wilt weer geven en accepteren, gebruikt u de opdracht [AZ VM image Accept-Terms](/cli/azure/vm/image/terms) . Wanneer u de voor waarden accepteert, schakelt u programmatische implementatie in voor uw abonnement. U hoeft slechts één keer per abonnement voor de installatie kopie te accepteren. Bijvoorbeeld:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

De uitvoer bevat een `licenseTextLink` aan de licentie voorwaarden en geeft aan dat de waarde van `accepted` is `true` :

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

Als u de voor waarden wilt accepteren, typt u:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Een nieuwe VM implementeren met de para meters van de installatie kopie

Met informatie over de installatie kopie kunt u deze implementeren met behulp van de `az vm create` opdracht. 

Als u een installatie kopie wilt implementeren die geen plan informatie heeft, zoals de meest recente Ubuntu Server 18,04-installatie kopie van canonieke, geeft u de URN door voor `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Voor een installatie kopie met de para meters van het aankoop plan, zoals de RabbitMQ gecertificeerd door BitNami, geeft u de URN voor en geeft u `--image` ook de para meters van het aankoop plan op:

```azurecli
az group create --name myPurchasePlanRG --location westus

az vm create \
   --resource-group myPurchasePlanRG \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image bitnami:rabbitmq:rabbitmq:latest \
   --plan-name rabbitmq \
   --plan-product rabbitmq \
   --plan-publisher bitnami
```

Als u een bericht ontvangt over het accepteren van de voor waarden van de afbeelding, raadpleegt u [de voor waarden](#accept-the-terms)in de sectie. Zorg ervoor dat de uitvoer van de `az vm image accept-terms` waarde retourneert `"accepted": true,` die aangeeft dat u de voor waarden van de afbeelding hebt geaccepteerd.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Een bestaande VHD gebruiken met informatie over het aankoop plan

Als u een bestaande VHD hebt van een virtuele machine die is gemaakt met een betaalde Azure Marketplace-installatie kopie, moet u mogelijk de informatie van het aankoop plan opgeven wanneer u een nieuwe virtuele machine maakt op basis van die VHD. 

Als u nog steeds de oorspronkelijke virtuele machine hebt, of een andere virtuele machine die is gemaakt met dezelfde Marketplace-installatie kopie, kunt u de naam van het abonnement, de uitgever en de product informatie ophalen via [AZ VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view). In dit voor beeld wordt een virtuele machine met de naam *myVM* in de resource groep *myResourceGroup* opgehaald en worden de gegevens van het aankoop plan weer gegeven.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Als u de informatie over het abonnement niet hebt opgehaald voordat de oorspronkelijke VM werd verwijderd, kunt u een [ondersteunings aanvraag](https://ms.portal.azure.com/#create/Microsoft.Support)indienen. Ze moeten de VM-naam, abonnements-ID en het tijds tempel van de verwijderings bewerking hebben.

Zodra u de plannings gegevens hebt, kunt u de nieuwe VM maken met behulp van de `--attach-os-disk` para meter om de VHD op te geven.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```


## <a name="next-steps"></a>Volgende stappen
Zie [Linux Vm's maken en beheren met de Azure cli](tutorial-manage-vm.md)om snel een virtuele machine te maken met behulp van de installatie kopie-informatie.
