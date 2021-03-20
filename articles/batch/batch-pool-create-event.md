---
title: Gebeurtenis Azure Batch groep maken
description: Verwijzing voor de gebeurtenis batch-pool Create die wordt verzonden zodra een pool is gemaakt. De inhoud van het logboek bevat algemene informatie over de groep.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: f8b020401443b2a9e80837599d6fd4b2a3001d27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97609000"
---
# <a name="pool-create-event"></a>Gebeurtenis pool maken

 Deze gebeurtenis wordt verzonden zodra een pool is gemaakt. De inhoud van het logboek bevat algemene informatie over de groep. Houd er rekening mee dat als de doel grootte van de groep groter is dan 0 reken knooppunten, de start gebeurtenis voor het wijzigen van de pool direct na deze gebeurtenis wordt gevolgd.

 In het volgende voor beeld ziet u de hoofd tekst van een groep Create gebeurtenis voor een groep die is gemaakt met behulp van de `CloudServiceConfiguration` eigenschap.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de pool.|
|`displayName`|Tekenreeks|De weergave naam van de pool.|
|`vmSize`|Tekenreeks|De grootte van de virtuele machines in de groep. Alle virtuele machines in een groep hebben dezelfde grootte. <br/><br/> Zie [grootten voor Cloud Services](../cloud-services/cloud-services-sizes-specs.md)voor informatie over de beschik bare grootten van virtuele machines voor Cloud Services Pools (Pools die zijn gemaakt met cloudServiceConfiguration). Batch ondersteunt alle Cloud Services VM-grootten behalve `ExtraSmall` .<br/><br/> Zie [grootten voor virtual machines](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux) of [grootten voor virtual machines](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Windows) voor meer informatie over de beschik bare VM-grootten voor Pools met installatie kopieën van de virtual machines Marketplace (Pools die zijn gemaakt met virtualMachineConfiguration). Batch ondersteunt alle Azure VM-groottes met uitzondering van `STANDARD_A0` en die met Premium Storage (de serie `STANDARD_GS`, `STANDARD_DS` en `STANDARD_DSV2`).|
|`imageType`|Tekenreeks|De implementatie methode voor de installatie kopie. Ondersteunde waarden zijn `virtualMachineConfiguration` of `cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Complex type|De Cloud service configuratie voor de groep.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Complex type|De virtuele-machine configuratie voor de groep.|
|[`networkConfiguration`](#bk_netconf)|Complex type|De netwerk configuratie voor de groep.|
|`resizeTimeout`|Tijd|De time-out voor de toewijzing van reken knooppunten aan de groep die is opgegeven voor de laatste bewerking voor het wijzigen van de grootte van de groep.  (De oorspronkelijke grootte wanneer de pool wordt gemaakt, telt als een grootte van het formaat.)|
|`targetDedicatedNodes`|Int32|Het aantal toegewezen reken knooppunten dat voor de pool wordt aangevraagd.|
|`targetLowPriorityNodes`|Int32|Het aantal reken knooppunten met lage prioriteit dat voor de groep wordt aangevraagd.|
|`enableAutoScale`|Booleaanse waarde|Hiermee geeft u op of de pool grootte automatisch in de loop van de tijd wordt aangepast.|
|`enableInterNodeCommunication`|Booleaanse waarde|Hiermee geeft u op of de pool is ingesteld voor directe communicatie tussen knoop punten.|
|`isAutoPool`|Booleaanse waarde|Hiermee geeft u op of de groep is gemaakt via het mechanisme voor de groep van een taak.|
|`taskSlotsPerNode`|Int32|Het maximum aantal taken dat gelijktijdig kan worden uitgevoerd op één reken knooppunt in de pool.|
|`vmFillType`|Tekenreeks|Definieert hoe taken in de batch-service worden gedistribueerd tussen reken knooppunten in de pool. Geldige waarden zijn spread of Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a> cloudServiceConfiguration

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`osFamily`|Tekenreeks|De Azure-gast besturingssysteem familie moet worden geïnstalleerd op de virtuele machines in de groep.<br /><br /> Mogelijke waarden zijn:<br /><br /> **2** : OS-familie 2, gelijk aan Windows Server 2008 R2 SP1.<br /><br /> **3** : OS-familie 3, gelijk aan Windows Server 2012.<br /><br /> **4** : OS-familie 4, gelijk aan Windows Server 2012 R2.<br /><br /> Zie [versies van Azure Guest OS](../cloud-services/cloud-services-guestos-update-matrix.md#releases)voor meer informatie.|
|`targetOSVersion`|Tekenreeks|De versie van het Azure-gast besturingssysteem die moet worden geïnstalleerd op de virtuele machines in de groep.<br /><br /> De standaard waarde is **\*** de meest recente versie van het besturings systeem voor de opgegeven familie.<br /><br /> Zie [versies van Azure Guest OS](../cloud-services/cloud-services-guestos-update-matrix.md#releases)voor andere toegestane waarden.|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a> virtualMachineConfiguration

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Complex type|Hiermee geeft u informatie op over het platform of de Marketplace-installatie kopie die moet worden gebruikt.|
|`nodeAgentId`|Tekenreeks|De SKU van de batch-knooppunt agent die is ingericht op het reken knooppunt.|
|[`windowsConfiguration`](#bk_winconf)|Complex type|Hiermee geeft u de instellingen van het Windows-besturings systeem op de virtuele machine. Deze eigenschap moet niet worden opgegeven als de imageReference verwijst naar een Linux-installatie kopie van het besturings systeem.|

###  <a name="imagereference"></a><a name="bk_imgref"></a> imageReference

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`publisher`|Tekenreeks|De uitgever van de installatie kopie.|
|`offer`|Tekenreeks|De aanbieding van de installatie kopie.|
|`sku`|Tekenreeks|De SKU van de afbeelding.|
|`version`|Tekenreeks|De versie van de installatie kopie.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a> windowsConfiguration

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Booleaans|Hiermee wordt aangegeven of de virtuele machine is ingeschakeld voor automatische updates. Als deze eigenschap niet is opgegeven, is de standaard waarde True.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a> networkConfiguration

|Elementnaam|Type|Opmerkingen|
|------------------|--------------|----------|
|`subnetId`|Tekenreeks|Hiermee geeft u de resource-id op van het subnet waarin de reken knooppunten van de pool worden gemaakt.|