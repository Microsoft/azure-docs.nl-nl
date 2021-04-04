---
title: Met GPU ingeschakelde container instantie implementeren
description: Meer informatie over het implementeren van Azure container instances voor het uitvoeren van Compute-container-apps met GPU-resources.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 0d645d1fce24d1324e485d74e20bcf492d4444a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93127005"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Container instanties implementeren die GPU-bronnen gebruiken

Als u bepaalde computerintensieve werk belastingen op Azure Container Instances wilt uitvoeren, implementeert u de [container groepen](container-instances-container-groups.md) met *GPU-resources*. De container instanties in de groep hebben toegang tot een of meer NVIDIA Tesla-Gpu's tijdens het uitvoeren van container werkbelastingen, zoals CUDA en diepe leer toepassingen.

In dit artikel wordt beschreven hoe u GPU-resources toevoegt wanneer u een container groep implementeert met behulp van een [yaml-bestand](container-instances-multi-container-yaml.md) of [Resource Manager-sjabloon](container-instances-multi-container-group.md). U kunt ook GPU-resources opgeven wanneer u een container exemplaar implementeert met behulp van de Azure Portal.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er [zijn enkele beperkingen van toepassing](#preview-limitations). Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

In de preview-periode gelden de volgende beperkingen bij het gebruik van GPU-resources in container groepen. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

De ondersteuning wordt gedurende de loop tijd aan extra regio's toegevoegd.

**Ondersteunde typen besturings systeem**: alleen Linux

**Aanvullende beperkingen**: GPU-bronnen kunnen niet worden gebruikt bij het implementeren van een container groep in een [virtueel netwerk](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Over GPU-bronnen

### <a name="count-and-sku"></a>Aantal en SKU

Als u Gpu's wilt gebruiken in een container exemplaar, geeft u een *GPU-resource* op met de volgende gegevens:

* **Count** -het aantal gpu's: **1**, **2** of **4**.
* **SKU** -de GPU-SKU: **K80**, **P100** of **V100**. Elke SKU wordt toegewezen aan de NVIDIA Tesla GPU in een van de volgende Azure GPU-VM-families:

  | SKU | VM-serie |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Wanneer u GPU-resources implementeert, stelt u de CPU-en geheugen resources in die geschikt zijn voor de werk belasting, tot aan de maximum waarden die in de voor gaande tabel worden weer gegeven. Deze waarden zijn momenteel groter dan de CPU-en geheugen bronnen die beschikbaar zijn in container groepen zonder GPU-resources.  

> [!IMPORTANT]
> Standaard [abonnements limieten](container-instances-quotas.md) (quota's) voor GPU-resources verschillen per SKU. De standaard CPU-limieten voor de P100-en V100-Sku's worden in eerste instantie ingesteld op 0. Als u een toename in een beschik bare regio wilt aanvragen, moet u een [ondersteunings aanvraag voor Azure][azure-support]indienen.

### <a name="things-to-know"></a>Dingen die u moet weten

* Het maken van de **implementatie tijd** van een container groep met GPU-bronnen duurt maxi maal **8-10 minuten**. Dit wordt veroorzaakt door de extra tijd voor het inrichten en configureren van een GPU-VM in Azure. 

* **Prijzen** : vergelijkbaar met container groepen zonder GPU-resources, Azure-facturen voor bronnen die worden verbruikt gedurende de *duur* van een container groep met GPU-resources. De duur wordt berekend op basis van de tijd voor het ophalen van de installatie kopie van uw eerste container totdat de container groep wordt beëindigd. Het bevat niet de tijd voor het implementeren van de container groep.

  Zie de [prijs informatie](https://azure.microsoft.com/pricing/details/container-instances/).

* **CUDA-Stuur Programma's** : container instanties met GPU-resources zijn vooraf ingericht met NVIDIA CUDA-Stuur Programma's en container-runtimes, zodat u container installatie kopieën kunt gebruiken die zijn ontwikkeld voor CUDA-workloads.

  We ondersteunen in deze fase alleen CUDA 9,0. U kunt bijvoorbeeld de volgende basis installatie kopieën voor uw docker-bestand gebruiken:
  * [NVIDIA/CUDA: 9.0-base-Ubuntu 16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensor flow/tensor flow: 1.12.0-GPU-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML-voor beeld

Een manier om GPU-resources toe te voegen, is door een container groep te implementeren met behulp van een [yaml-bestand](container-instances-multi-container-yaml.md). Kopieer de volgende YAML naar een nieuw bestand met de naam *GPU-Deploy-ACI. yaml* en sla het bestand op. Met deze YAML maakt u een container groep met de naam *gpucontainergroup* die een container exemplaar met een K80 GPU opgeeft. De instantie voert een voor beeld van een toepassing voor het optellen van CUDA vector. De resource-aanvragen zijn voldoende om de werk belasting uit te voeren.

```YAML
additional_properties: {}
apiVersion: '2019-12-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Implementeer de container groep met de opdracht [AZ container Create][az-container-create] en geef de yaml-bestands naam op voor de `--file` para meter. U moet de naam van een resource groep en een locatie opgeven voor de container groep, zoals *ooster* , die GPU-resources ondersteunt.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Het duurt enkele minuten om de implementatie te voltooien. Vervolgens wordt de container gestart en wordt er een bewerking voor het optellen van CUDA-vector uitgevoerd. Voer de opdracht [AZ container logs][az-container-logs] uit om de logboek uitvoer weer te geven:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Uitvoer:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Voor beeld van Resource Manager-sjabloon

Een andere manier om een container groep te implementeren met GPU-resources is met behulp van een [Resource Manager-sjabloon](container-instances-multi-container-group.md). Begin met het maken van een bestand met `gpudeploy.json` de naam en kopieer vervolgens de volgende JSON naar de toepassing. In dit voor beeld wordt een container exemplaar geïmplementeerd met een V100-GPU die een [tensor flow](https://www.tensorflow.org/) -trainings taak uitvoert op de MNIST-gegevensset. De resource-aanvragen zijn voldoende om de werk belasting uit te voeren.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Implementeer de sjabloon met de opdracht [AZ Deployment Group Create][az-deployment-group-create] . U moet de naam opgeven van een resource groep die is gemaakt in een regio, zoals *ooster* , die GPU-resources ondersteunt.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Het duurt enkele minuten om de implementatie te voltooien. De container start en voert vervolgens de tensor flow-taak uit. Voer de opdracht [AZ container logs][az-container-logs] uit om de logboek uitvoer weer te geven:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Uitvoer:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Resources opschonen

Omdat het gebruik van GPU-resources kostbaar kan zijn, moet u ervoor zorgen dat uw containers niet voor een lange periode onverwacht worden uitgevoerd. Bewaak uw containers in het Azure Portal of Controleer de status van een container groep met de opdracht [AZ container show][az-container-show] . Bijvoorbeeld:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Wanneer u klaar bent met de container instanties die u hebt gemaakt, verwijdert u deze met de volgende opdrachten:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van een container groep met behulp van een [yaml-bestand](container-instances-multi-container-yaml.md) of een [Resource Manager-sjabloon](container-instances-multi-container-group.md).
* Meer informatie over door [GPU geoptimaliseerde VM-grootten](../virtual-machines/sizes-gpu.md) in Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
