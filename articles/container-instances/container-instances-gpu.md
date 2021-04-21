---
title: Een container-exemplaar met GPU implementeren
description: Meer informatie over het implementeren van Azure-container instances voor het uitvoeren van rekenintensieve container-apps met behulp van GPU-resources.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 6ffe4840d024c1e1f551966d05673c4ba83e1259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764059"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Container-exemplaren implementeren die GPU-resources gebruiken

Als u bepaalde rekenintensieve workloads wilt uitvoeren op Azure Container Instances, implementeert u [uw containergroepen](container-instances-container-groups.md) met *GPU-resources.* De container-exemplaren in de groep hebben toegang tot een of meer NVIDIA Tesla GPU's tijdens het uitvoeren van containerworkloads zoals CUDA en deep learning-toepassingen.

In dit artikel wordt beschreven hoe u GPU-resources toevoegt wanneer u een containergroep implementeert met behulp van een [YAML-bestand](container-instances-multi-container-yaml.md) [of Resource Manager sjabloon](container-instances-multi-container-group.md). U kunt ook GPU-resources opgeven wanneer u een container-exemplaar implementeert met behulp van Azure Portal.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-versie en er gelden [enkele beperkingen.](#preview-limitations) Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="preview-limitations"></a>Preview-beperkingen

In de preview zijn de volgende beperkingen van toepassing bij het gebruik van GPU-resources in containergroepen. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Er wordt na een bepaalde periode ondersteuning toegevoegd voor extra regio's.

**Ondersteunde besturingssysteemtypen:** alleen Linux

**Aanvullende beperkingen:** GPU-resources kunnen niet worden gebruikt bij het implementeren van een containergroep in [een virtueel netwerk.](container-instances-vnet.md)

## <a name="about-gpu-resources"></a>Over GPU-resources

### <a name="count-and-sku"></a>Aantal en SKU

Als u GPU's in een container-instantie wilt gebruiken, geeft u een *GPU-resource op* met de volgende informatie:

* **Aantal:** het aantal GPU's: **1,** **2** of **4**.
* **SKU:** de GPU-SKU: **K80,** **P100** of **V100.** Elke SKU wordt in een van de volgende VM-families met GPU-ondersteuning van Azure aan de NVIDIA Tesla GPU toe te kennen:

  | SKU | VM-familie |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Wanneer u GPU-resources implementeert, stelt u cpu- en geheugenbronnen in die geschikt zijn voor de workload, tot de maximumwaarden die in de voorgaande tabel worden weergegeven. Deze waarden zijn momenteel groter dan de CPU- en geheugenresources die beschikbaar zijn in containergroepen zonder GPU-resources.  

> [!IMPORTANT]
> [Standaardabonnementslimieten](container-instances-quotas.md) (quota) voor GPU-resources verschillen per SKU. De standaard CPU-limieten voor de SKU's P100 en V100 zijn in eerste instantie ingesteld op 0. Als u een verhoging in een beschikbare regio wilt aanvragen, dient u een aanvraag [in ondersteuning voor Azure verzenden.][azure-support]

### <a name="things-to-know"></a>Dingen die u moet weten

* **Implementatietijd:** het maken van een containergroep met **GPU-resources duurt maximaal 8-10 minuten.** Dit komt door de extra tijd voor het inrichten en configureren van een GPU-VM in Azure. 

* **Prijzen:** net als bij containergroepen zonder GPU-resources,  worden in Azure kosten in rekening genomen voor resources die worden gebruikt gedurende de duur van een containergroep met GPU-resources. De duur wordt berekend op basis van de tijd die nodig is om de containerafbeelding op te halen totdat de containergroep wordt beëindigd. Dit omvat niet de tijd die nodig is om de containergroep te implementeren.

  Zie [prijsinformatie.](https://azure.microsoft.com/pricing/details/container-instances/)

* **CUDA-stuurprogramma's:** container instances met GPU-resources zijn vooraf ingericht met NVIDIA CUDA-stuurprogramma's en containerruntimes, zodat u containerafbeeldingen kunt gebruiken die zijn ontwikkeld voor CUDA-workloads.

  In deze fase ondersteunen we alleen CUDA 9.0. U kunt bijvoorbeeld de volgende basisafbeeldingen gebruiken voor uw Docker-bestand:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML-voorbeeld

Eén manier om GPU-resources toe te voegen, is door een containergroep te implementeren met behulp van een [YAML-bestand](container-instances-multi-container-yaml.md). Kopieer de volgende YAML naar een nieuw bestand met de naam *gpu-deploy-aci.yaml* en sla het bestand op. Deze YAML maakt een containergroep met de *naam gpucontainergroup die* een container-exemplaar met een K80 GPU opgeeft. Het exemplaar voert een voorbeeld van een CUDA-toepassing voor vectoroptelling uit. De resourceaanvragen zijn voldoende om de workload uit te voeren.

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

Implementeer de containergroep met de [opdracht az container create,][az-container-create] met de naam van het YAML-bestand voor de `--file` parameter . U moet de naam van een resourcegroep en een locatie voor de containergroep, zoals *eastus,* die GPU-resources ondersteunt, leveren.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Het duurt enkele minuten om de implementatie te voltooien. Vervolgens wordt de container gestart en wordt een optellingsbewerking voor de CUDA-vector uitgevoerd. Voer de [opdracht az container logs][az-container-logs] uit om de logboekuitvoer weer te geven:

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

## <a name="resource-manager-template-example"></a>Resource Manager voorbeeld van een sjabloon

Een andere manier om een containergroep met GPU-resources te implementeren, is met behulp van [een Resource Manager sjabloon](container-instances-multi-container-group.md). Begin met het maken van een bestand met de `gpudeploy.json` naam en kopieer de volgende JSON naar het bestand. In dit voorbeeld wordt een containerin instance geïmplementeerd met een V100 GPU die een [TensorFlow-trainings](https://www.tensorflow.org/) job op de MNIST-gegevensset. De resourceaanvragen zijn voldoende om de workload uit te voeren.

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

Implementeer de sjabloon met de [opdracht az deployment group create.][az-deployment-group-create] U moet de naam van een resourcegroep die is gemaakt in een regio zoals *eastus* die GPU-resources ondersteunt, leveren.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Het duurt enkele minuten om de implementatie te voltooien. Vervolgens wordt de TensorFlow-taak gestart en uitgevoerd door de container. Voer de [opdracht az container logs][az-container-logs] uit om de logboekuitvoer weer te geven:

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

Omdat het gebruik van GPU-resources duur kan zijn, moet u ervoor zorgen dat uw containers gedurende langere perioden niet onverwacht worden uitgevoerd. Controleer uw containers in de Azure Portal of controleer de status van een containergroep met de [opdracht az container show.][az-container-show] Bijvoorbeeld:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Wanneer u klaar bent met het werken met de container-exemplaren die u hebt gemaakt, verwijdert u deze met de volgende opdrachten:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van een containergroep met behulp van een [YAML-bestand](container-instances-multi-container-yaml.md) [of Resource Manager sjabloon](container-instances-multi-container-group.md).
* Meer informatie over voor [GPU geoptimaliseerde VM-grootten](../virtual-machines/sizes-gpu.md) in Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
