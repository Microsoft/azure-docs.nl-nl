---
title: Marketplace-aankoopplangegevens zoeken en gebruiken met behulp van de CLI
description: Informatie over het gebruik van de Azure CLI om url's voor afbeeldingen en aankoopplanparameters te vinden, zoals de uitgever, aanbieding, SKU en versie, voor marketplace-VM-afbeeldingen.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.collection: linux
ms.custom: contperf-fy21q3-portal, devx-track-azurecli
ms.openlocfilehash: be0535a49b47c45cad49abd1bf720b6347a660b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484197"
---
# <a name="find-azure-marketplace-image-information-using-the-azure-cli"></a>Informatie over Azure Marketplace-afbeelding zoeken met behulp van de Azure CLI

In dit onderwerp wordt beschreven hoe u de Azure CLI gebruikt om VM-afbeeldingen te zoeken in de Azure Marketplace. Gebruik deze informatie om een Marketplace-afbeelding op te geven wanneer u een VM programmatisch maakt met de CLI, Resource Manager sjablonen of andere hulpprogramma's.

U kunt ook door beschikbare afbeeldingen en aanbiedingen bladeren met behulp van [de Azure Marketplace](https://azuremarketplace.microsoft.com/) of  [Azure PowerShell](../windows/cli-ps-findimage.md). 

## <a name="terminology"></a>Terminologie

Een Marketplace-afbeelding in Azure heeft de volgende kenmerken:

* **Uitgever:** de organisatie die de afbeelding heeft gemaakt. Voorbeelden: Canonical, MicrosoftWindowsServer
* **Aanbieding:** de naam van een groep gerelateerde afbeeldingen die door een uitgever is gemaakt. Voorbeelden: UbuntuServer, WindowsServer
* **SKU:** een exemplaar van een aanbieding, zoals een grote release van een distributie. Voorbeelden: 18.04-LTS, 2019-Datacenter
* **Versie:** het versienummer van een SKU voor een afbeelding. 

Deze waarden kunnen afzonderlijk of als een *afbeeldings-URN* worden doorgegeven, waarbij de waarden worden gecombineerd, gescheiden door de dubbele punt (:). Bijvoorbeeld: *Uitgever*:*Aanbieding:**SKU*:*versie*. U kunt het versienummer in de URN vervangen door om de nieuwste versie van de `latest` afbeelding te gebruiken. 

Als de uitgever van de afbeelding aanvullende licentie- en aankoopvoorwaarden biedt, moet u deze accepteren voordat u de afbeelding kunt gebruiken.  Zie De informatie over het [aankoopplan controleren voor meer informatie.](#check-the-purchase-plan-information)



## <a name="list-popular-images"></a>Populaire afbeeldingen op een lijst zetten

Voer de [opdracht az vm image list](/cli/azure/vm/image) uit, zonder de optie , om een lijst met populaire VM-afbeeldingen weer te geven `--all` in de Azure Marketplace. Voer bijvoorbeeld de volgende opdracht uit om een lijst met populaire afbeeldingen in de cache weer te geven in tabelindeling:

```azurecli
az vm image list --output table
```

De uitvoer bevat de URN van de afbeelding. U kunt ook de *UrnAlias* gebruiken. Dit is een verkorte versie die is gemaakt voor populaire afbeeldingen zoals *UbuntuLTS.*

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

Als u een specifieke VM-afbeelding wilt zoeken in Marketplace, gebruikt u `az vm image list` de opdracht met de optie `--all` . Deze versie van de opdracht neemt enige tijd in beslag en kan lange uitvoer retourneren, zodat u de lijst meestal filtert op `--publisher` of een andere parameter. 

Met de volgende opdracht worden bijvoorbeeld alle Debian-aanbiedingen weergegeven (vergeet niet dat zonder de schakelknop alleen wordt gezocht in de lokale `--all` cache met algemene afbeeldingen):

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


## <a name="look-at-all-available-images"></a>Alle beschikbare afbeeldingen bekijken
 
Een andere manier om een afbeelding op een locatie te vinden, is door de opdrachten [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image)en [az vm image list-skus](/cli/azure/vm/image) opeenvolgend uit te voeren. Met deze opdrachten bepaalt u deze waarden:

1. Vermeld de uitgevers van afbeeldingen voor een locatie. In dit voorbeeld kijken we naar de regio *VS -* west.
    
    ```azurecli
    az vm image list-publishers --location westus --output table
    ```

1. Geef de aanbiedingen voor een bepaalde uitgever weer. In dit voorbeeld voegen we *Canonical toe* als uitgever.
    
    ```azurecli
    az vm image list-offers --location westus --publisher Canonical --output table
    ```

1. Geef de SKU's voor een bepaalde aanbieding weer. In dit voorbeeld voegen we *UbuntuServer toe* als de aanbieding.
    ```azurecli
    az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
    ```

1. Voor een bepaalde uitgever, aanbieding en SKU toont u alle versies van de afbeelding. In dit voorbeeld voegen we *18.04-LTS toe* als de SKU.

    ```azurecli
    az vm image list \
        --location westus \
        --publisher Canonical \  
        --offer UbuntuServer \    
        --sku 18.04-LTS \
        --all --output table
    ```

Geef deze waarde van de kolom URN door met de parameter wanneer u een `--image` VM maakt met [de opdracht az vm create.](/cli/azure/vm) U kunt ook het versienummer in de URN vervangen door 'nieuwste', om gewoon de nieuwste versie van de afbeelding te gebruiken. 

Als u een VM met een Resource Manager implementeert, stelt u de afbeeldingsparameters afzonderlijk in de `imageReference` eigenschappen in. Zie de [sjabloonverwijzing](/azure/templates/microsoft.compute/virtualmachines).


## <a name="check-the-purchase-plan-information"></a>De informatie over het aankoopplan controleren

Sommige VM-afbeeldingen in de Azure Marketplace extra licentie- en aankoopvoorwaarden die u moet accepteren voordat u ze programmatisch kunt implementeren.  

Als u een VM van een dergelijke afbeelding wilt implementeren, moet u de voorwaarden van de afbeelding accepteren wanneer u deze voor de eerste keer gebruikt, eenmaal per abonnement. U moet ook parameters voor het *aankoopplan opgeven* om een VM vanuit die afbeelding te implementeren

Als u de aankoopplangegevens van een afbeelding wilt weergeven, moet u de [opdracht az vm image show](/cli/azure/image) uitvoeren met de URN van de afbeelding. Als de `plan` eigenschap in de uitvoer niet `null` is, heeft de afbeelding voorwaarden die u moet accepteren vóór de programmatische implementatie.

De canonieke Ubuntu Server 18.04 LTS-installatier heeft bijvoorbeeld geen aanvullende voorwaarden, omdat de `plan` informatie `null` is:

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

Als u een vergelijkbare opdracht voor de afbeelding RabbitMQ Certified by Bitnami gebruikt, ziet u de volgende `plan` eigenschappen: `name` , en `product` `publisher` . (Sommige afbeeldingen hebben ook een `promotion code` eigenschap.) 

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

Als u deze afbeelding wilt implementeren, moet u de voorwaarden accepteren en de parameters voor het aankoopplan opgeven wanneer u een VM implementeert met behulp van die afbeelding.

## <a name="accept-the-terms"></a>De gebruiksvoorwaarden accepteren

Als u de licentievoorwaarden wilt bekijken en accepteren, gebruikt [u de opdracht az vm image accept-terms.](/cli/azure/vm/image/terms) Wanneer u de voorwaarden accepteert, kunt u programmatische implementatie inschakelen in uw abonnement. U hoeft de voorwaarden slechts eenmaal per abonnement voor de afbeelding te accepteren. Bijvoorbeeld:

```azurecli
az vm image terms show --urn bitnami:rabbitmq:rabbitmq:latest
``` 

De uitvoer bevat een `licenseTextLink` voor de licentievoorwaarden en geeft aan dat de waarde van `accepted` `true` is:

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

Als u de voorwaarden wilt accepteren, typt u:

```azurecli
az vm image terms accept --urn bitnami:rabbitmq:rabbitmq:latest
``` 

## <a name="deploy-a-new-vm-using-the-image-parameters"></a>Een nieuwe VM implementeren met behulp van de afbeeldingsparameters

Met informatie over de afbeelding kunt u deze implementeren met behulp van de `az vm create` opdracht . 

Als u een installatie afbeelding wilt implementeren die geen plangegevens bevat, zoals de meest recente Ubuntu Server 18.04-installatie afbeelding van Canonical, geeft u de URN door voor `--image` :

```azurecli-interactive
az group create --name myURNVM --location westus
az vm create \
   --resource-group myURNVM \
   --name myVM \
   --admin-username azureuser \
   --generate-ssh-keys \
   --image Canonical:UbuntuServer:18.04-LTS:latest 
```


Voor een afbeelding met aankoopplanparameters, zoals de afbeelding RabbitMQ Certified by Bitnami, geeft u de URN voor door en geeft u ook de parameters voor `--image` het aankoopplan op:

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

Als u een bericht krijgt over het accepteren van de voorwaarden van de afbeelding, bekijkt u de sectie [De voorwaarden accepteren.](#accept-the-terms) Zorg ervoor dat de uitvoer van `az vm image accept-terms` de waarde `"accepted": true,` retourneert die laat zien dat u de voorwaarden van de afbeelding hebt geaccepteerd.


## <a name="using-an-existing-vhd-with-purchase-plan-information"></a>Een bestaande VHD gebruiken met informatie over het aankoopplan

Als u een bestaande VHD hebt van een VM die is gemaakt met behulp van een betaalde Azure Marketplace-afbeelding, moet u mogelijk de aankoopplangegevens verstrekken wanneer u een nieuwe VM maakt op basis van die VHD. 

Als u nog steeds de oorspronkelijke VM hebt of een andere VM die is gemaakt met behulp van dezelfde Marketplace-afbeelding, kunt u de plannaam, uitgever en productinformatie hieruit halen met [az vm get-instance-view.](/cli/azure/vm#az_vm_get_instance_view) In dit voorbeeld wordt een VM met de *naam myVM* in de resourcegroep *myResourceGroup* en worden vervolgens de aankoopplangegevens weergegeven.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Als u de plangegevens niet hebt ontvangen voordat de oorspronkelijke VM werd verwijderd, kunt u een [ondersteuningsaanvraag indienen.](https://ms.portal.azure.com/#create/Microsoft.Support) Ze hebben de VM-naam, de abonnements-id en het tijdstempel van de verwijderbewerking nodig.

Zodra u de plangegevens hebt, kunt u de nieuwe VM maken met behulp van `--attach-os-disk` de parameter om de VHD op te geven.

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
Zie Virtuele Linux-machines maken en beheren met de Azure CLI om snel een virtuele machine te maken met behulp van de [afbeeldingsgegevens.](tutorial-manage-vm.md)
