---
title: Linux VM-installatie kopieën selecteren met Azure CLI
description: Meer informatie over het gebruik van de Azure CLI om de uitgever, aanbieding, SKU en versie te bepalen voor VM-installatie kopieën van Marketplace.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 01/25/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: efa0b91c9c0e43104f36017c3b1a1f3167190d63
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562806"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Linux-VM-installatiekopieën zoeken in de Azure Marketplace met de Azure CLI

In dit onderwerp wordt beschreven hoe u de Azure CLI gebruikt om VM-installatie kopieën te vinden in de Azure Marketplace. Gebruik deze informatie om een Marketplace-installatie kopie op te geven wanneer u via een programma een virtuele machine maakt met de CLI, Resource Manager-sjablonen of andere hulpprogram ma's.

Blader ook naar beschik bare installatie kopieën en aanbiedingen met behulp van de [Azure Marketplace](https://azuremarketplace.microsoft.com/) -winkel, de [Azure Portal](https://portal.azure.com)of  [Azure PowerShell](../windows/cli-ps-findimage.md). 

Zorg ervoor dat u bent aangemeld bij een Azure-account ( `az login` ).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="deploy-from-a-vhd-using-purchase-plan-parameters"></a>Implementeren vanaf een VHD met behulp van de para meters van het aankoop plan

Als u een bestaande VHD hebt die is gemaakt met een betaalde Azure Marketplace-installatie kopie, moet u mogelijk de informatie van het aankoop plan opgeven wanneer u een nieuwe virtuele machine maakt op basis van die VHD. 

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

## <a name="deploy-a-new-vm-using-purchase-plan-parameters"></a>Een nieuwe VM implementeren met behulp van de para meters van het aankoop plan

Als u al informatie over de installatie kopie hebt, kunt u deze implementeren met behulp van de `az vm create` opdracht. In dit voor beeld implementeren we een virtuele machine met de RabbitMQ-installatie kopie gecertificeerd door BitNami:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

Als u een bericht ontvangt over het accepteren van de voor waarden van de afbeelding, raadpleegt u de sectie [akkoord met de voor waarden](#accept-the-terms) verderop in dit artikel.

## <a name="list-popular-images"></a>Populaire installatie kopieën weer geven

Voer de opdracht [AZ VM Image List](/cli/azure/vm/image) uit zonder de `--all` optie om een lijst met populaire VM-installatie kopieën in azure Marketplace weer te geven. Voer bijvoorbeeld de volgende opdracht uit om een lijst met populaire afbeeldingen in de cache weer te geven in tabel indeling:

```azurecli
az vm image list --output table
```

De uitvoer bevat de afbeelding URN (de waarde in de kolom *urn* ). Bij het maken van een virtuele machine met een van deze populaire Marketplace-installatie kopieën kunt u ook de *UrnAlias* opgeven, een Inge kort formulier, zoals *UbuntuLTS*.

```output
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Specifieke installatiekopieën zoeken

Als u een specifieke VM-installatie kopie in Marketplace wilt zoeken, gebruikt u de `az vm image list` opdracht met de `--all` optie. Het duurt enige tijd voordat deze versie van de opdracht is voltooid en kan een langdurige uitvoer retour neren, dus u kunt de lijst doorgaans filteren op `--publisher` of een andere para meter. 

Met de volgende opdracht worden bijvoorbeeld alle Debian-aanbiedingen weer gegeven (Houd er rekening mee dat zonder de `--all` Switch alleen de lokale cache van algemene installatie kopieën wordt doorzocht):

```azurecli
az vm image list --offer Debian --all --output table 

```

Gedeeltelijke uitvoer: 

```output
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Gelijksoortige filters toep assen met de `--location` `--publisher` Opties, en `--sku` . U kunt gedeeltelijke overeenkomsten op een filter uitvoeren, bijvoorbeeld `--offer Deb` om te zoeken naar alle Debian-installatie kopieën.

Als u geen specifieke locatie met de optie opgeeft `--location` , worden de waarden voor de standaard locatie geretourneerd. (Stel een andere standaard locatie in door uit te voeren `az configure --defaults location=<location>` .)

Met de volgende opdracht wordt bijvoorbeeld een lijst weer gegeven met alle Debian 8 Sku's op de locatie Europa-west:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Gedeeltelijke uitvoer:

```output
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Navigeren door de afbeeldingen
 
Een andere manier om een installatie kopie te vinden op een locatie is het uitvoeren van de [AZ VM Image List-Publishers](/cli/azure/vm/image), [AZ VM Image List-aanbiedingen](/cli/azure/vm/image)en [AZ VM Image List-sku's-](/cli/azure/vm/image) opdrachten in volg orde. Met deze opdrachten bepaalt u deze waarden:

1. Geef de uitgevers van installatiekopieën weer.
2. Geef de aanbiedingen voor een bepaalde uitgever weer.
3. Geef de SKU's voor een bepaalde aanbieding weer.

Voor een geselecteerde SKU kunt u vervolgens een versie kiezen die u wilt implementeren.

De volgende opdracht geeft bijvoorbeeld een lijst van de uitgevers van installatie kopieën op de locatie vs-West:

```azurecli
az vm image list-publishers --location westus --output table
```

Gedeeltelijke uitvoer:

```output
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Gebruik deze informatie om aanbiedingen van een specifieke uitgever te vinden. Bijvoorbeeld, voor de *canonieke* uitgever op de locatie vs-West, zoek aanbiedingen door uit te voeren `azure vm image list-offers` . Geef de locatie en de uitgever op, zoals in het volgende voor beeld:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Uitvoer:

```output
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
U ziet dat in de regio vs-West, canonieke de *UbuntuServer* -aanbieding op Azure publiceert. Maar welke SKU's? Als u deze waarden wilt ophalen, moet `azure vm image list-skus` u de locatie, uitgever en aanbieding die u hebt gedetecteerd, uitvoeren en instellen:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Uitvoer:

```output
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Gebruik tot slot de `az vm image list` opdracht om een specifieke versie te vinden van de SKU die u wilt gebruiken, bijvoorbeeld *18,04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Gedeeltelijke uitvoer:

```output
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

U kunt nu precies de installatie kopie kiezen die u wilt gebruiken door rekening te houden met de URN-waarde. Geef deze waarde door aan de `--image` para meter bij het maken van een virtuele machine met de opdracht [AZ VM Create](/cli/azure/vm) . Houd er rekening mee dat u het versie nummer in de URN kunt vervangen door "nieuwste". Deze versie is altijd de nieuwste versie van de installatie kopie. 

Als u een virtuele machine implementeert met een resource manager-sjabloon, stelt u de installatie kopie parameters afzonderlijk in de `imageReference` Eigenschappen in. Zie de [sjabloonverwijzing](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Plan eigenschappen weer geven

Als u de gegevens van het aankoop plan van een afbeelding wilt weer geven, voert u de opdracht [AZ VM Image Show](/cli/azure/image) uit. Als de `plan` eigenschap in de uitvoer niet is `null` , heeft de installatie kopie de voor waarden die u moet accepteren voordat u een programmatische implementatie uitvoert.

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

Als u een vergelijk bare opdracht uitvoert voor de RabbitMQ die door de BitNami-installatie kopie wordt gecertificeerd, worden de volgende eigenschappen weer gegeven `plan` : `name` , `product` en `publisher` . (Sommige installatie kopieën hebben ook een `promotion code` eigenschap.) Raadpleeg de volgende secties om de voor waarden te accepteren en de programmatische implementatie in te scha kelen om deze installatie kopie te implementeren.

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

## <a name="accept-the-terms"></a>De gebruiksvoorwaarden accepteren

Als u de licentie voorwaarden wilt weer geven en accepteren, gebruikt u de opdracht [AZ VM image Accept-Terms](/cli/azure/vm/image?) . Wanneer u de voor waarden accepteert, schakelt u programmatische implementatie in voor uw abonnement. U hoeft slechts één keer per abonnement voor de installatie kopie te accepteren. Bijvoorbeeld:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
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

## <a name="next-steps"></a>Volgende stappen
Zie [Linux Vm's maken en beheren met de Azure cli](tutorial-manage-vm.md)om snel een virtuele machine te maken met behulp van de installatie kopie-informatie.
