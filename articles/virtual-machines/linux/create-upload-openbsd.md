---
title: Een OpenBSD-installatie kopie maken en uploaden
description: Meer informatie over het maken en uploaden van een virtuele harde schijf (VHD) die het OpenBSD-besturings systeem bevat om een virtuele Azure-machine te maken met behulp van Azure CLI
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 3437fcd2d1d521aad237ecf6d3f7937b4835d376
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554595"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Een OpenBSD-schijf kopie maken en uploaden naar Azure
In dit artikel wordt beschreven hoe u een virtuele harde schijf (VHD) die het OpenBSD-besturings systeem bevat maakt en uploadt. Nadat u het hebt geüpload, kunt u dit als uw eigen installatie kopie gebruiken om een virtuele machine (VM) in azure te maken via Azure CLI.


## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u de volgende items hebt:

* **Een Azure-abonnement** : als u geen account hebt, kunt u er een maken in slechts een paar minuten. Als u een MSDN-abonnement hebt, raadpleegt u het [maandelijkse Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Anders leert u hoe u [een gratis proef account maakt](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure cli** : Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-azure-cli) hebt geïnstalleerd en bent aangemeld bij uw Azure-account met [AZ login](/cli/azure/reference-index).
* **Het OpenBSD-besturings systeem is geïnstalleerd in een VHD-bestand** : een ondersteund OpenBSD-besturings systeem ([6,6 versie amd64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) moet zijn geïnstalleerd op een virtuele harde schijf. Er zijn meerdere hulpprogram ma's voor het maken van VHD-bestanden. U kunt bijvoorbeeld een virtualisatie-oplossing zoals Hyper-V gebruiken om het VHD-bestand te maken en het besturings systeem te installeren. Zie [hyper-v installeren en een virtuele machine maken](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))voor instructies over het installeren en gebruiken van Hyper-v.


## <a name="prepare-openbsd-image-for-azure"></a>OpenBSD-installatie kopie voorbereiden voor Azure
Voer de volgende procedures uit op de virtuele machine waarop u het OpenBSD-besturings systeem 6,1 hebt geïnstalleerd en Hyper-V-ondersteuning heeft toegevoegd:

1. Als DHCP niet is ingeschakeld tijdens de installatie, schakelt u de service als volgt in:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Stel als volgt een seriële console in:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Configureer de installatie van het pakket als volgt:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. De `root` gebruiker is standaard uitgeschakeld op virtuele machines in Azure. Gebruikers kunnen opdrachten uitvoeren met verhoogde bevoegdheden met behulp van de `doas` opdracht op OPENBSD VM. DOAS is standaard ingeschakeld. Zie [DOAS. conf](https://man.openbsd.org/doas.conf.5)voor meer informatie. 

5. Installeer en configureer de vereisten voor de Azure-agent als volgt:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. De nieuwste versie van de Azure-agent kan altijd worden gevonden op [github](https://github.com/Azure/WALinuxAgent/releases). Ga als volgt te werk om de agent te installeren:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Nadat u Azure agent hebt geïnstalleerd, is het een goed idee om te controleren of deze als volgt wordt uitgevoerd:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. De inrichting van het systeem ongedaan maken om het uit te schonen en dit geschikt voor herinrichting. Met de volgende opdracht worden ook de laatste ingerichte gebruikers account en de bijbehorende gegevens verwijderd:

    ```sh
    waagent -deprovision+user -force
    ```

U kunt de virtuele machine nu afsluiten.


## <a name="prepare-the-vhd"></a>De VHD voorbereiden
De VHDX-indeling wordt niet ondersteund in azure, alleen **vaste VHD**. U kunt de schijf converteren naar een vaste VHD-indeling met behulp van Hyper-V-beheer of de Power shell [-cmdlet Convert-VHD](/powershell/module/hyper-v/convert-vhd) . Een voor beeld is als volgt.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Opslag resources maken en uploaden
Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Als u uw VHD wilt uploaden, maakt u een opslag account met [AZ Storage account create](/cli/azure/storage/account). Namen van opslag accounts moeten uniek zijn, dus geef uw eigen naam op. In het volgende voor beeld wordt een opslag account gemaakt met de naam *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Als u de toegang tot het opslag account wilt beheren, moet u de opslag sleutel als volgt ophalen met [AZ Storage account Keys List](/cli/azure/storage/account/keys) :

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Als u de Vhd's die u uploaden logisch wilt scheiden, maakt u een container in het opslag account met [AZ storage container Create](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Upload ten slotte uw VHD met [AZ Storage BLOB upload](/cli/azure/storage/blob) als volgt:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Een VM maken op basis van uw VHD
U kunt een virtuele machine maken met een [voorbeeld script](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-vhd) of rechtstreeks met [AZ VM Create](/cli/azure/vm). Als u de OpenBSD-VHD die u hebt geüpload wilt opgeven, gebruikt u de `--image` para meter als volgt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Vraag het IP-adres voor uw OpenBSD-VM met [AZ VM List-IP-adressen](/cli/azure/vm) als volgt:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

U kunt nu als standaard SSHen naar uw OpenBSD-VM:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Volgende stappen
Als u meer wilt weten over Hyper-V-ondersteuning op OpenBSD 6.1, leest u [OpenBSD 6,1](https://www.openbsd.org/61.html) en [hyper links. 4](https://man.openbsd.org/hyperv.4).

Als u een virtuele machine van een beheerde schijf wilt maken, leest u [AZ Disk](/cli/azure/disk).