---
title: 'Zelfstudie: Virtuele Linux-machines maken en beheren met de Azure CLI'
description: In deze zelfstudie leert u hoe u Azure CLI gebruikt voor het maken en beheren van virtuele Linux-machines in Azure
services: virtual-machines
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77213fe83a56a33f4d10658f85465349949be792
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555598"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>Zelfstudie: Virtuele Linux-machines maken en beheren met de Azure CLI

Virtuele machines in Azure bieden een volledig geconfigureerde en flexibele computeromgeving. Deze zelfstudie bevat informatie over basisconcepten voor het implementeren van virtuele Azure-machines, zoals het selecteren van een VM-grootte, het selecteren van een VM-installatiekopie en het implementeren van een virtuele machine. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een virtuele machine maken en verbinding maken met een virtuele machine
> * VM-installatiekopieën selecteren en gebruiken
> * Specifieke VM-grootten weergeven en gebruiken
> * De grootte van een virtuele machine wijzigen
> * De status van een virtuele machine weergeven en begrijpen

In deze zelfstudie wordt gebruikgemaakt van de CLI in de [Azure Cloud Shell](../../cloud-shell/overview.md), die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Cloud Shell wilt openen, selecteert u **Probeer het** bovenaan een willekeurig codeblok.

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). 

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voordat een virtuele machine wordt gemaakt, moet een resourcegroep worden gemaakt. In dit voorbeeld wordt een resourcegroep met de naam *myResourceGroupVM* gemaakt in de regio *VS - Oost*. 

```azurecli-interactive
az group create --name myResourceGroupVM --location eastus
```

De resourcegroep wordt opgegeven tijdens het maken of wijzigen van een virtuele machine, zoals in deze zelfstudie te zien is.

## <a name="create-virtual-machine"></a>Virtuele machine maken

Maak een virtuele machine met de opdracht [az vm create](/cli/azure/vm). 

Wanneer u een virtuele machine maakt, zijn er diverse opties beschikbaar zoals installatiekopie besturingssysteem, schijfgrootte en beheerdersreferenties. In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt waarop Ubuntu Server loopt. Een gebruikersaccount met de naam *azureuser* wordt gemaakt op de virtuele machine en SSH-sleutels worden gegenereerd als deze niet bestaan op de standaardlocatie van de sleutel ( *~/.ssh*):

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Het maken van de virtuele machine kan een paar minuten duren. Wanneer de virtuele machine is gemaakt, biedt de Azure CLI informatie over de virtuele machine. Noteer het `publicIpAddress`; dit adres kan worden gebruikt voor toegang tot de virtuele machine... 

```output
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Verbinding maken met de virtuele machine

U kunt nu verbinding maken met de virtuele machine met SSH in de Azure Cloud Shell of vanaf uw lokale computer. Vervang het voorbeeld van een IP-adres door het `publicIpAddress` dat u in de vorige stap hebt genoteerd.

```bash
ssh azureuser@52.174.34.95
```

Nadat u bent aangemeld bij de virtuele machine, kunt u toepassingen gaan installeren en configureren. Wanneer u klaar bent, sluit u de SSH-sessie af zoals gebruikelijk:

```bash
exit
```

## <a name="understand-vm-images"></a>Inzicht in VM-installatiekopieën

Azure Marketplace bevat vele installatiekopieën die kunnen worden gebruikt voor het maken van virtuele machines. In de vorige stappen is een virtuele machine gemaakt met behulp van een Ubuntu-installatiekopie. In deze stap wordt de Azure CLI gebruikt om op de Marketplace te zoeken naar een CentOS-installatiekopie, die vervolgens wordt gebruikt voor het implementeren van een tweede virtuele machine. 

Als u een lijst wilt weergeven van de meest gebruikte installatiekopieën, gebruikt u de opdracht [az vm image list](/cli/azure/vm/image).

```azurecli-interactive 
az vm image list --output table
```

De uitvoer van de opdracht retourneert de meest populaire VM-installatiekopieën in Azure.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

U kunt een volledige lijst zien door het argument `--all` toe te voegen. De lijst met installatiekopieën kan ook worden gefilterd door `--publisher` of `–-offer`. In dit voorbeeld wordt de lijst gefilterd voor alle installatiekopieën met een aanbieding die overeenkomt met *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Gedeeltelijke uitvoer:

```output
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Als u een virtuele machine wilt implementeren met een specifieke installatiekopie, noteert u de waarde in de kolom *Urn*, die bestaat uit de uitgever, aanbieding, SKU en eventueel een versienummer om de installatiekopie te [identificeren](cli-ps-findimage.md#terminology). Bij het opgeven van de installatiekopie kan het versienummer van de installatiekopie worden vervangen door 'nieuwste', waarmee de meest recente versie van de distributie wordt geselecteerd. In dit voorbeeld wordt het argument `--image` gebruikt om de nieuwste versie van een CentOS 6.5-installatiekopie op te geven.  

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Inzicht in VM-grootten

De grootte van een virtuele machine bepaalt de hoeveelheid rekenresources, zoals CPU, GPU en geheugen, die beschikbaar zijn gesteld voor de virtuele machine. Virtuele machines moeten de juiste grootte krijgen voor de verwachte werkbelasting. Als de werkbelasting toeneemt, kan de grootte van een bestaande virtuele machine worden gewijzigd.

### <a name="vm-sizes"></a>VM-grootten

In de volgende tabel zijn grootten gecategoriseerd in use-cases.  

| Type                     | Veelgebruikte grootten           |    Beschrijving       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Algemeen gebruik](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Evenwichtige CPU-geheugenverhouding. Ideaal voor ontwikkelen/testen en in kleine tot middelgrote toepassingen en gegevensoplossingen.  |
| [Geoptimaliseerde rekenkracht](../sizes-compute.md)   | Fsv2          | Hoge CPU-geheugenverhouding. Goed voor middelgrootte verkeerstoepassingen, netwerkapparatuur en batchprocessen.        |
| [Geoptimaliseerd geheugen](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Hoge geheugen-kernverhouding. Uiterst geschikt voor relationele-databases, middelgrote tot grote caches en analysefuncties in het geheugen.                 |
| [Geoptimaliseerde opslag](../sizes-storage.md)      | Lsv2, Ls              | Snelle doorvoer van schijfgegevens en IO. Ideaal voor big data-, SQL- en NoSQL-databases.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Gespecialiseerde VM's bedoeld voor intensieve grafische rendering en videobewerking.       |
| [Hoge prestaties](../sizes-hpc.md) | H        | Onze krachtigste CPU-VM's met optionele netwerkinterfaces (RDMA) voor hoge doorvoer. |


### <a name="find-available-vm-sizes"></a>Beschikbare VM-grootten zoeken

Als u een lijst wilt weergeven met de VM-grootten die beschikbaar zijn in een bepaalde regio, gebruikt u de opdracht [az vm list-sizes](/cli/azure/vm). 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Gedeeltelijke uitvoer:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Een virtuele machine met een specifieke grootte maken

In het vorige voorbeeld over het maken van een virtuele machine, is er geen grootte opgegeven, waardoor de standaardgrootte werd gebruikt. Een VM-grootte kan worden geselecteerd tijdens het maken met behulp van [az vm create](/cli/azure/vm) en het argument `--size`. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>De grootte van een virtuele machine wijzigen

Nadat een virtuele machine is geïmplementeerd, kan de grootte ervan worden gewijzigd om meer of minder resources toe te wijzen. U kunt de huidige grootte van een virtuele machine weergeven met [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Voordat u de grootte van een virtuele machine wijzigt, moet u controleren of de gewenste grootte beschikbaar is in het huidige Azure-cluster. Met de opdracht [az vm list-vm-resize-options](/cli/azure/vm) wordt de lijst met grootten geretourneerd. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```

Als de gewenste grootte beschikbaar is, kan de grootte van de virtuele machine worden gewijzigd terwijl de virtuele machine wordt uitgevoerd. De virtuele machine moet wel opnieuw worden opgestart tijdens de bewerking. Gebruik de opdracht [az vm resize]( /cli/azure/vm) om de grootte te wijzigen.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Als de gewenste grootte niet beschikbaar is in het huidige cluster, moet de toewijzing van de VM ongedaan worden gemaakt voordat de grootte kan worden gewijzigd. Gebruik de opdracht [az vm deallocate]( /cli/azure/vm) om de virtuele machine te stoppen en de toewijzing van de virtuele machine ongedaan te maken. Opmerking: wanneer de virtuele machine weer wordt ingeschakeld, zijn gegevens op de tijdelijke schijf mogelijk verwijderd. Het openbare IP-adres verandert ook, tenzij een statisch IP-adres is gebruikt. 

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Nadat de toewijzing ongedaan is gemaakt, kan de grootte worden gewijzigd. 

```azurecli-interactive
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Als de grootte is gewijzigd, kan de virtuele machine worden gestart.

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Energiestatussen voor de virtuele machine

Een Azure VM kan op een van de vele energiestatussen worden ingesteld. Deze status vertegenwoordigt de huidige status van de virtuele machine vanuit het oogpunt van de hypervisor. 

### <a name="power-states"></a>Energiestatussen

| Energiestatus | Beschrijving
|----|----|
| Starten | Geeft aan dat de virtuele machine wordt gestart. |
| In uitvoering | Geeft aan dat de virtuele machine wordt uitgevoerd. |
| Stoppen | Geeft aan dat de virtuele machine wordt gestopt. | 
| Gestopt | Geeft aan dat de virtuele machine is gestopt. Virtuele machines met de status Gestopt genereren nog steeds rekenkosten.  |
| Vrijgeven | Geeft aan dat de toewijzing van de virtuele machine ongedaan wordt gemaakt. |
| Toewijzing ongedaan gemaakt | Geeft aan dat de virtuele machine is verwijderd uit de hypervisor maar nog steeds beschikbaar is in het vlak van het besturingselement. Virtuele machines met de status Toewijzing ongedaan gemaakt genereren geen rekenkosten. |
| - | Geeft aan dat de Aan-/uitstatus van de virtuele machine onbekend is. |

### <a name="find-the-power-state"></a>De Aan-/uitstatus zoeken

Als u de status van een bepaalde virtuele machine wilt ophalen, gebruikt u de opdracht [az vm get-instance-view](/cli/azure/vm). Zorg ervoor dat u een geldige naam opgeeft voor de virtuele machine en resourcegroep. 

```azurecli-interactive
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Uitvoer:

```output
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

Als u de energiestatus van alle VM's in uw abonnement wilt ophalen, gebruikt u de API [Virtual Machines - List All](/rest/api/compute/virtualmachines/listall), waarbij u de parameter **statusOnly** instelt op *true*.

## <a name="management-tasks"></a>Beheertaken

Tijdens de levenscyclus van een virtuele machine wilt u mogelijk beheertaken uitvoeren, zoals het starten, stoppen of verwijderen van een virtuele machine. Misschien wilt u ook scripts maken voor het automatiseren van terugkerende of complexe taken. Met de Azure CLI kunnen veel algemene beheertaken worden uitgevoerd vanaf de opdrachtregel of in scripts. 

### <a name="get-ip-address"></a>IP-adres ophalen

Met deze opdracht worden het privé- en openbare IP-adres van een virtuele machine geretourneerd.  

```azurecli-interactive
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Virtuele machine stoppen

```azurecli-interactive
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Virtuele machine starten

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Resourcegroep verwijderen

Als u een resourcegroep verwijdert, verwijdert u ook alle resources binnen deze groep, zoals de VM, het virtuele netwerk en de schijf. De parameter `--no-wait` retourneert het besturingselement naar de prompt zonder te wachten totdat de bewerking is voltooid. De parameter `--yes` bevestigt dat u de resources wilt verwijderen, zonder een extra prompt om dit te doen.

```azurecli-interactive
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd over basistaken voor het maken en beheren van een virtuele machine, zoals:

> [!div class="checklist"]
> * Een virtuele machine maken en verbinding maken met een virtuele machine
> * VM-installatiekopieën selecteren en gebruiken
> * Specifieke VM-grootten weergeven en gebruiken
> * De grootte van een virtuele machine wijzigen
> * De status van een virtuele machine weergeven en begrijpen

In de volgende zelfstudie leert u meer over VM-schijven.  

> [!div class="nextstepaction"]
> [VM-schijven maken en beheren](./tutorial-manage-disks.md)