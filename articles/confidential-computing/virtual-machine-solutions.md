---
title: Azure Confidential Computing-oplossingen op virtuele machines
description: Meer informatie over Azure Confidential Computing-oplossingen op virtuele machines.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 580c53f311bc8ee70e974df2bc4111e6361d06f6
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818958"
---
# <a name="solutions-on-azure-virtual-machines"></a>Oplossingen op virtuele Azure-machines

In dit artikel wordt informatie beschreven over het implementeren van virtuele machines (VM's) met Azure Confidential Computing met Intel-processors met ondersteuning van [Intel Software Guard Extension](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>VM-grootten van Azure Confidential Computing

Virtuele machines met Azure Confidential Computing zijn ontworpen om de vertrouwelijkheid en integriteit van uw gegevens en code te beschermen terwijl deze worden verwerkt in de cloud 

[DCsv2-serie](../virtual-machines/dcv2-series.md) VM's zijn de nieuwste en meest recente confidential computing-groottefamilie. Deze VM's ondersteunen een groter aantal implementatiemogelijkheden, hebben 2x de Enclave Page Cache (EPC) en een grotere selectie grootten in vergelijking met onze DC-Series-VM's. De [VM's](../virtual-machines/sizes-previous-gen.md#preview-dc-series) uit de DC-serie zijn momenteel in preview en worden afgeschaft en niet opgenomen in de algemene beschikbaarheid.

Begin met het implementeren van een DCsv2-Series-VM via de commerciële marketplace van Microsoft door de [quickstart-zelfstudie te volgen.](quick-create-marketplace.md)

### <a name="current-available-sizes-and-regions"></a>Huidige beschikbare grootten en regio's

Voer de volgende opdracht uit in de Azure CLI om een lijst op te halen met alle algemeen beschikbare VM-grootten voor Confidential Compute in beschikbare regio's en [beschikbaarheidszones:](/cli/azure/install-azure-cli-windows)

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Voer de volgende opdracht uit voor een gedetailleerdere weergave van de bovenstaande grootten:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Vereisten voor toegewezen host
Het implementeren **van Standard_DC8_v2** virtuele machinegrootte in de DCSv2-Series-VM-familie neemt de volledige host in beslag en wordt niet gedeeld met andere tenants of abonnementen. Deze VM SKU-familie biedt de isolatie die u mogelijk nodig hebt om te voldoen aan wettelijke vereisten voor naleving en beveiliging waar normaal gesproken aan wordt voldaan door een toegewezen hostservice te hebben. Wanneer u kiest **Standard_DC8_v2** SKU, wijst de fysieke hostserver alle beschikbare hardwarebronnen, inclusief EPC-geheugen, alleen toe aan uw virtuele machine. Houd er rekening mee dat deze functionaliteit bestaat op basis van het ontwerp van de infrastructuur en dat alle functies van de **Standard_DC8_v2** worden ondersteund. Deze implementatie is niet hetzelfde als de [Azure Dedicated Host](../virtual-machines/dedicated-hosts.md) service die wordt geleverd door andere Azure VM-families.


## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

Volg een snelstartzelfstudie om in minder dan 10 minuten DCsv2-Series virtuele machine te implementeren. 

- **Azure-abonnement:** als u een VM-exemplaar met Confidential Computing wilt implementeren, kunt u een betalen per gebruik-abonnement of een andere aankoopoptie overwegen. Als u een gratis [Azure-account gebruikt,](https://azure.microsoft.com/free/)hebt u geen quotum voor de juiste hoeveelheid Azure-rekenkernen.

- **Prijzen en regionale beschikbaarheid:** u vindt de prijzen voor DCsv2-Series VM's op [de pagina met prijzen voor virtuele machines.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) Controleer [de beschikbare producten per regio op](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) beschikbaarheid in Azure-regio's.


- **Kernquotum:** mogelijk moet u het quotum voor kernen in uw Azure-abonnement verhogen van de standaardwaarde. Uw abonnement kan ook het aantal kernen beperken dat u kunt implementeren in bepaalde VM-groottefamilies, waaronder de DCsv2-serie. Als u een quotumverhoging wilt aanvragen, [opent u gratis een online](../azure-portal/supportability/per-vm-quota-requests.md) klantondersteuningsaanvraag. Houd er rekening mee dat de standaardlimieten kunnen variëren, afhankelijk van uw abonnementscategorie.

  > [!NOTE]
  > Neem Ondersteuning voor Azure als u behoefte hebt aan grootschalige capaciteit. Azure-quota zijn tegoedlimieten, geen capaciteitsgaranties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor de kernen die u gebruikt.
  
- **Het formaat van confidential** computing-exemplaren kan alleen worden groter vanwege de gespecialiseerde hardware. U kunt bijvoorbeeld alleen de grootte van een VM uit de DCsv2-serie van de ene DCsv2-serie naar een andere grootte. Het formaat van een niet-confidential computing-grootte naar een confidential computing-grootte wordt niet ondersteund.  

- **Image:** om Intel Software Guard Extension (Intel SGX) ondersteuning te bieden voor confidential compute-exemplaren, moeten alle implementaties worden uitgevoerd op installatieprogramma's van de tweede generatie. Azure Confidential Computing ondersteunt workloads die worden uitgevoerd op Ubuntu 18.04 Gen 2, Ubuntu 20.04 Gen 2, Windows Server 2019 Gen2 en Windows Server 2016 Gen 2. Lees meer [over ondersteuning voor VM's van de tweede generatie in Azure](../virtual-machines/generation-2.md) voor meer informatie over ondersteunde en niet-ondersteunde scenario's. 

- **Opslag:** gegevensschijven van virtuele machines met Azure Confidential Computing en onze kortstondige besturingssysteemschijven staan op NVMe-schijven. Instanties ondersteunen alleen Premium - SSD en Standard - SSD schijven, niet Ultra - SSD of Standard - HDD. De grootte van **DC8_v2** biedt geen ondersteuning voor Premium-opslag. 

- **Schijfversleuteling:** confidential compute-exemplaren bieden op dit moment geen ondersteuning voor schijfversleuteling. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Overwegingen voor hoge beschikbaarheid en herstel na noodherstel

Wanneer u virtuele machines in Azure gebruikt, bent u verantwoordelijk voor het implementeren van een oplossing voor hoge beschikbaarheid en herstel na noodherstel om downtime te voorkomen. 

Azure Confidential Computing biedt op dit moment geen ondersteuning voor zone-redundantie via Beschikbaarheidszones. Gebruik Beschikbaarheidssets voor de hoogste beschikbaarheid en redundantie [voor](../virtual-machines/availability-set-overview.md)confidential computing. Vanwege hardwarebeperkingen kunnen beschikbaarheidssets voor confidential computing-exemplaren maximaal 10 updatedomeinen hebben. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Implementatie met Azure Resource Manager (ARM)-sjabloon

Azure Resource Manager is de implementatie- en beheersservice voor Azure. Het biedt een beheerlaag waarmee u resources in uw Azure-abonnement kunt maken, bijwerken en verwijderen. U kunt beheerfuncties, zoals toegangsbeheer, vergrendelingen en tags, gebruiken om uw resources na de implementatie te beveiligen en te organiseren.

Zie voor meer informatie over [ARM-sjablonen Sjabloonimlementatie overzicht.](../azure-resource-manager/templates/overview.md)

Als u een virtuele DCsv2-Series virtuele machine wilt implementeren in een ARM-sjabloon, gebruikt u de [virtuele-machineresource](../virtual-machines/windows/template-description.md). Zorg ervoor dat u de juiste eigenschappen opgeeft **voor vmSize** en voor **uw imageReference**.

### <a name="vm-size"></a>VM-grootte

Geef een van de volgende grootten op in uw ARM-sjabloon in de resource van de virtuele machine. Deze tekenreeks wordt als **vmSize in eigenschappen** **gezet.**

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2-besturingssysteemafbeelding

Onder **eigenschappen** moet u ook verwijzen naar een afbeelding onder **storageProfile**. Gebruik *slechts een* van de volgende afbeeldingen voor uw **imageReference**.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "20_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "20_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Volgende stappen 

In dit artikel hebt u geleerd over de kwalificaties en configuraties die nodig zijn bij het maken van een virtuele machine met confidential computing. U kunt nu naar de Microsoft Azure Marketplace om een virtuele DCsv2-Series implementeren.

> [!div class="nextstepaction"]
> [Implementeer DCsv2-Series virtuele machine in de Azure Marketplace](quick-create-marketplace.md)