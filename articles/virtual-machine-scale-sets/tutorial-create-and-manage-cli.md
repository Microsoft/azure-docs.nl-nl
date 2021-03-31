---
title: 'Zelfstudie: Een virtuele-machineschaalset maken en beheren - Azure CLI'
description: Ontdek hoe u met Azure CLI een schaalset voor virtuele Azure-machines maakt. Leer daarnaast hoe u enkele veelvoorkomende beheertaken uitvoert, zoals het starten en stoppen van een exemplaar of het wijzigen van de capaciteit van de schaalset.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 0f94823b958ae5f95789dd4ef9a62057bdf764a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517458"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Zelfstudie: Een virtuele-machineschaalset maken en beheren met Azure CLI
Met een virtuele-machineschaalset kunt u een reeks identieke virtuele machines met automatisch schalen implementeren en beheren. Gedurende de levenscyclus van een schaalset voor virtuele machines moet u mogelijk een of meer beheertaken uitvoeren. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een schaalset voor virtuele machines maken en hiermee verbinding maken
> * VM-installatiekopieën selecteren en gebruiken
> * Specifieke grootten van VM-exemplaren weergeven en gebruiken
> * Een schaalset handmatig schalen
> * Algemene beheertaken voor schaalsets uitvoeren

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.29 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 


## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. U kunt pas een schaalset voor virtuele machines maken als er al een resourcegroep is gemaakt. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt in de regio *eastus*. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

De naam van de resourcegroep moet worden opgegeven als u ergens in deze zelfstudie een schaalset maakt of wijzigt.


## <a name="create-a-scale-set"></a>Een schaalset maken
U maakt een schaalset voor virtuele machines met [az vmss create](/cli/azure/vmss). Het volgende voorbeeld wordt een schaalset gemaakt met de naam *myScaleSet*, en worden SSH-sleutels gegenereerd als deze nog niet bestaan:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Het duurt enkele minuten om alle schaalsetresources en VM-exemplaren te maken en te configureren. Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt.


## <a name="view-the-vm-instances-in-a-scale-set"></a>De VM-exemplaren in een schaalset bekijken
Als u een lijst met VM-exemplaren wilt weergeven die worden uitgevoerd in een schaalset, gebruikt u [az vmss list-instances](/cli/azure/vmss) als volgt:

```azurecli-interactive
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

In de volgende voorbeelduitvoer ziet u dat de schaalset twee VM-exemplaren bevat:

```output
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  c059be0c-37a2-497a-b111-41272641533c
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUP  ec19e7a7-a4cd-4b24-9670-438f4876c1f9
```


De eerste kolom in de uitvoer bevat een *InstanceId*. Als u extra informatie wilt weergeven over een bepaald VM-exemplaar, voegt u de parameter `--instance-id` toe aan [az vmss get-instance-view](/cli/azure/vmss). In het volgende voorbeeld wordt informatie opgevraagd over het VM-exemplaar *1*:

```azurecli-interactive
az vmss get-instance-view \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --instance-id 1
```


## <a name="list-connection-information"></a>Verbindingsgegevens opvragen
Er wordt een openbaar IP-adres toegewezen aan de load balancer die verkeer routeert naar de afzonderlijke VM-exemplaren. Standaard worden er regels voor NAT (Network Address Translation) toegevoegd aan de load balancer van Azure waarmee verkeer van externe verbindingen wordt doorgestuurd naar elke VM op een opgegeven poort. Om verbinding te maken met de VM-exemplaren in een schaalset, maakt u een externe verbinding met een toegewezen openbare IP-adres en poortnummer.

Gebruik [az vmss list-instance-connection-info](/cli/azure/vmss) om de adressen en poorten op te vragen waarmee VM-exemplaren in een schaalset moeten worden verbonden:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

In de volgende voorbeelduitvoer ziet u de exemplaarnaam, het openbare IP-adres van de load balancer en het poortnummer waarnaar de NAT-regels verkeer doorsturen:

```output
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

Ga met SSH naar uw eerste VM-exemplaar. Geef met de parameter `-p` uw openbare IP-adres en poortnummer op, zoals is vastgesteld in de voorgaande opdracht:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Zodra u bent aangemeld bij het VM-exemplaar, kunt u indien nodig de configuratie op enkele punten handmatig wijzigen. Dit is nu niet nodig en u kunt de SSH-sessie op de gebruikelijke manier afsluiten:

```console
exit
```


## <a name="understand-vm-instance-images"></a>Installatiekopieën van VM-exemplaren begrijpen
Toen u aan het begin van de zelfstudie een schaalset hebt gemaakt, is een `--image` van *UbuntuLTS* opgegeven voor de VM-exemplaren. Azure Marketplace bevat allerlei installatiekopieën die kunnen worden gebruikt voor het maken van VM-exemplaren. Als u een lijst wilt weergeven van de meest gebruikte installatiekopieën, gebruikt u de opdracht [az vm image list](/cli/azure/vm/image).

```azurecli-interactive
az vm image list --output table
```

In de volgende voorbeelduitvoer ziet u de meest gebruikte VM-installatiekopieën in Azure. De *UrnAlias* kan worden gebruikt om een van deze veelvoorkomende installatiekopieën op te geven bij het maken van een schaalset.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
```

Als u een volledige lijst wilt weergeven, voegt u het argument `--all` toe. De lijst met installatiekopieën kan ook worden gefilterd door `--publisher` of `–-offer`. In het volgende voorbeeld wordt de lijst gefilterd op alle installatiekopieën met een aanbieding die overeenkomt met *CentOS*:

```azurecli-interactive
az vm image list --offer CentOS --all --output table
```

In de volgende verkorte uitvoer ziet u enkele van de beschikbare CentOS 7.3-installatiekopieën:

```output
Offer    Publisher   Sku   Urn                                 Version
-------  ----------  ----  ----------------------------------  -------------
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20161221   7.3.20161221
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170421   7.3.20170421
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170517   7.3.20170517
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170612   7.3.20170612
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170707   7.3.20170707
CentOS   OpenLogic   7.3   OpenLogic:CentOS:7.3:7.3.20170925   7.3.20170925
```

Als u een schaalset wilt implementeren met een specifieke installatiekopie, gebruikt u de waarde in de kolom *Urn*. Wanneer u de installatiekopie opgeeft, kunt u het versienummer van de installatiekopie vervangen door *latest* om de nieuwste versie van de distributie te selecteren. In het volgende voorbeeld wordt het argument `--image` gebruikt om de nieuwste versie van een installatiekopie van CentOS 7.3 op te geven.

> [!IMPORTANT]
> We raden u aan de *nieuwste* installatiekopieversie te gebruiken. Specificeer 'nieuwste' om de nieuwste versie van een installatiekopie te gebruiken die beschikbaar is op de implementatietijd. Zelfs als u 'nieuwste' gebruikt, wordt de VM-installatiekopie niet automatisch bijgewerkt na de implementatietijd, zelfs niet als er een nieuwe versie beschikbaar wordt.

Aangezien het enkele minuten duurt om alle schaalsetresources en VM-exemplaren te maken en te configureren, hoeft u de volgende schaalset niet te implementeren:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetCentOS \
  --image OpenLogic:CentOS:7.3:latest \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="understand-vm-instance-sizes"></a>VM-exemplaargrootten begrijpen
De grootte van een VM-exemplaar, of *SKU*, bepaalt de hoeveelheid rekenresources, zoals CPU, GPU en geheugen die beschikbaar worden gesteld aan het VM-exemplaar. VM-exemplaren in een schaalset moeten de juiste grootte krijgen voor de verwachte werkbelasting.

### <a name="vm-instance-sizes"></a>VM-exemplaargrootten
In de volgende tabel worden enkele veelgebruikte VM-grootten gecategoriseerd naar gebruikssituatie.

| Type                     | Veelgebruikte grootten           |    Beschrijving       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Algemeen doel](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Evenwichtige CPU-geheugenverhouding. Ideaal voor ontwikkelen/testen en in kleine tot middelgrote toepassingen en gegevensoplossingen.  |
| [Geoptimaliseerde rekenkracht](../virtual-machines/sizes-compute.md)   | Fs, F             | Hoge CPU-geheugenverhouding. Goed voor middelgrootte verkeerstoepassingen, netwerkapparatuur en batchprocessen.        |
| [Geoptimaliseerd geheugen](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Hoge geheugen-kernverhouding. Uiterst geschikt voor relationele-databases, middelgrote tot grote caches en analysefuncties in het geheugen.                 |
| [Geoptimaliseerde opslag](../virtual-machines/sizes-storage.md)      | Ls                | Snelle doorvoer van schijfgegevens en IO. Ideaal voor big data-, SQL- en NoSQL-databases.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | Gespecialiseerde VM's bedoeld voor intensieve grafische rendering en videobewerking.       |
| [Hoge prestaties](../virtual-machines/sizes-hpc.md) | H, A8-11          | Onze krachtigste CPU-VM's met optionele netwerkinterfaces (RDMA) voor hoge doorvoer. 

### <a name="find-available-vm-instance-sizes"></a>Beschikbare VM-exemplaargrootten zoeken
Als u een lijst wilt weergeven met de VM-exemplaargrootten die beschikbaar zijn in een bepaalde regio, gebruikt u de opdracht [az vm list-sizes](/cli/azure/vm).

```azurecli-interactive
az vm list-sizes --location eastus --output table
```

De uitvoer is vergelijkbaar met het volgende verkorte voorbeeld, waarin de resources worden weergegeven die zijn toegewezen aan elke VM-grootte:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 4          3584  Standard_DS1_v2                       1           1047552                    7168
                 8          7168  Standard_DS2_v2                       2           1047552                   14336
[...]
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
[...]
                 4          2048  Standard_F1                           1           1047552                   16384
                 8          4096  Standard_F2                           2           1047552                   32768
[...]
                24         57344  Standard_NV6                          6           1047552                   38912
                48        114688  Standard_NV12                        12           1047552                  696320
```

### <a name="create-a-scale-set-with-a-specific-vm-instance-size"></a>Een schaalset met een specifieke VM-exemplaargrootte maken
Toen u aan het begin van de zelfstudie een schaalset hebt gemaakt, werd er een standaard-VM-SKU van *Standard_D1_v2* opgegeven voor de VM-exemplaren. U kunt een andere grootte voor het VM-exemplaar opgeven op basis van de uitvoer van [az vm list-sizes](/cli/azure/vm). In het volgende voorbeeld wordt een schaalset gemaakt met de parameter `--vm-sku` om een VM-exemplaargrootte op te geven van *Standard_F1*. Aangezien het enkele minuten duurt om alle schaalsetresources en VM-exemplaren te maken en te configureren, hoeft u de volgende schaalset niet te implementeren:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSetF1Sku \
  --image UbuntuLTS \
  --vm-sku Standard_F1 \
  --admin-user azureuser \
  --generate-ssh-keys
```


## <a name="change-the-capacity-of-a-scale-set"></a>De capaciteit van een schaalset wijzigen
Toen u aan het begin van de zelfstudie een schaalset hebt gemaakt, zijn er standaard twee VM-exemplaren geïmplementeerd. U kunt de parameter `--instance-count` opgeven met [az vmss create](/cli/azure/vmss) om het aantal exemplaren te wijzigen dat wordt gemaakt met een schaalset. Als u het aantal VM-exemplaren in een bestaande schaalset wilt vergroten of verkleinen, kunt u de capaciteit handmatig wijzigen. De grootte van de schaalset wordt dan aangepast, waarna de load balancer wordt geconfigureerd voor het verdelen van het verkeer.

Als u het aantal VM-exemplaren in de schaalset handmatig wilt vergroten of verkleinen, gebruikt u [az vmss scale](/cli/azure/vmss). In het volgende voorbeeld wordt het aantal VM-exemplaren in de schaalset ingesteld op *3*:

```azurecli-interactive
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 3
```

Het duurt een paar minuten om de capaciteit van de schaalset bij te werken. Als u het aantal instanties wilt weergeven dat zich momenteel in een schaalset bevindt, gebruikt u [az vmss show](/cli/azure/vmss) en voert u een query uit op *sku.capacity*:

```azurecli-interactive
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```


## <a name="common-management-tasks"></a>Veelvoorkomende beheertaken
U kunt nu een schaalset maken, verbindingsgegevens opvragen en verbinding maken met VM-exemplaren. U hebt geleerd hoe u een andere installatiekopie van het besturingssysteem kunt gebruiken voor VM-exemplaren, hoe u een andere VM-grootte selecteert of hoe u handmatig het aantal exemplaren aanpast. Als onderdeel van de dagelijkse beheertaken, kan het nodig zijn om de VM-exemplaren in de schaalset te stoppen, starten of opnieuw op te starten.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>VM-exemplaren in een schaalset stoppen en hun toewijzing ongedaan maken
Gebruik [az vmss stop](/cli/azure/vmss) om een of meer VM-exemplaren in een schaalset te beëindigen. Met de parameter `--instance-ids` kunt u een of meer VM-exemplaren opgeven die u wilt stoppen. Als u geen exemplaar-id opgeeft, worden alle VM-exemplaren in de schaalset gestopt. In het volgende voorbeeld wordt het exemplaar *1* gestopt:

```azurecli-interactive
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

De toewijzing van gestopte VM-exemplaren wordt niet ongedaan gemaakt, wat betekent dat er nog steeds compute-kosten in rekening worden gebracht voor deze exemplaren. Als u de toewijzing van de VM-exemplaren ongedaan wilt maken zodat er alleen nog opslagkosten in rekening worden gebracht, gebruikt u [az vmss deallocate](/cli/azure/vmss). In het volgende voorbeeld wordt het exemplaar *1* gestopt en wordt de toewijzing ingetrokken:

```azurecli-interactive
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="start-vm-instances-in-a-scale-set"></a>VM-exemplaren in een schaalset starten
Gebruik [az vmss start](/cli/azure/vmss) om een of meer VM-exemplaren in een schaalset te starten. Met de parameter `--instance-ids` kunt u een of meer VM-exemplaren opgeven die u wilt starten. Als u geen exemplaar-id opgeeft, worden alle VM-exemplaren in de schaalset gestart. In het volgende voorbeeld wordt het exemplaar *1* gestart:

```azurecli-interactive
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```

### <a name="restart-vm-instances-in-a-scale-set"></a>VM-exemplaren in een schaalset opnieuw opstarten
Gebruik [az vmss restart](/cli/azure/vmss) om een of meer VM-exemplaren in een schaalset opnieuw op te starten. Met de parameter `--instance-ids` kunt u een of meer VM-exemplaren opgeven die u opnieuw wilt opstarten. Als u geen exemplaar-id opgeeft, worden alle VM-exemplaren in de schaalset opnieuw opgestart. In het volgende voorbeeld wordt het exemplaar *1* opnieuw opgestart:

```azurecli-interactive
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 1
```


## <a name="clean-up-resources"></a>Resources opschonen
Als u een resourcegroep verwijdert, verwijdert u ook alle resources binnen deze groep, zoals de VM-exemplaren, het virtuele netwerk en schijven. De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u een aantal basistaken voor het maken en beheren van schaalsets kunt uitvoeren met Azure CLI:

> [!div class="checklist"]
> * Een schaalset voor virtuele machines maken en hiermee verbinding maken
> * VM-installatiekopieën selecteren en gebruiken
> * Specifieke VM-grootten weergeven en gebruiken
> * Een schaalset handmatig schalen
> * Algemene beheertaken voor schaalsets uitvoeren

Ga naar de volgende zelfstudie om alles te leren over schijven in een schaalset.

> [!div class="nextstepaction"]
> [Gegevensschijven met schaalsets gebruiken](tutorial-use-disks-cli.md)
