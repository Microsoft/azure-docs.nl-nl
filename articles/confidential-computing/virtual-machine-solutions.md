---
title: Azure-oplossingen voor vertrouwelijke Computing op virtuele machines
description: Meer informatie over Azure-oplossingen voor vertrouwelijke Computing op virtuele machines.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 8621dc8cfc10ab44ecb358a40fdae1a1b2081734
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566580"
---
# <a name="solutions-on-azure-virtual-machines"></a>Oplossingen op virtuele machines van Azure

Dit artikel bevat informatie over het implementeren van Azure vertrouwelijk computing virtual machines (Vm's) met Intel-processors die worden ondersteund door de [Intel software Guard extension](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>VM-grootten van Azure vertrouwelijk computing

Azure vertrouwelijk computing virtual machines zijn ontworpen om de vertrouwelijkheid en integriteit van uw gegevens en code te beschermen tijdens de verwerking in de Cloud 

[DCsv2-serie](../virtual-machines/dcv2-series.md) Vm's zijn de nieuwste en meest recente familie voor vertrouwelijke computing-grootte. Deze Vm's ondersteunen een groter aantal implementatie mogelijkheden, hebben twee maal de enclave-pagina cache (EPC) en een grotere selectie van grootten vergeleken met onze DC-Series Vm's. De vm's van de [DC-serie](../virtual-machines/sizes-previous-gen.md#preview-dc-series) zijn momenteel beschikbaar als preview-versie en worden afgeschaft en zijn niet opgenomen in de algemene Beschik baarheid.

Begin met het implementeren van een DCsv2-Series-VM via de micro soft Commercial Marketplace door de [Snelstartgids zelf](quick-create-marketplace.md)te volgen.

### <a name="current-available-sizes-and-regions"></a>Huidige beschik bare grootten en regio's

Voer de volgende opdracht uit in de [Azure-cli](/cli/azure/install-azure-cli-windows)om een lijst op te halen met alle algemeen beschik bare generatie-VM-grootten in de beschik bare regio's en beschikbaarheids zones:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Voer de volgende opdracht uit voor een gedetailleerde weer gave van de bovenstaande grootten:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Vereisten voor speciale host
Het implementeren van een **Standard_DC8_v2** grootte van een virtuele machine in de DCSv2-Series VM-familie neemt de volledige host in beslag en wordt niet gedeeld met andere tenants of abonnementen. Deze VM-SKU-serie biedt de isolatie die u mogelijk nodig hebt om te voldoen aan nalevings-en beveiligings voorschriften die normaal gesp roken worden voldaan door een speciale host-service te hebben. Wanneer u **Standard_DC8_v2** SKU kiest, wijst de fysieke hostserver alle beschik bare hardwarebronnen, inclusief EPC-geheugen, toe aan uw virtuele machine. Houd er rekening mee dat deze functionaliteit bestaat door het ontwerpen van de infra structuur en dat alle functies van de **Standard_DC8_v2** worden ondersteund. Deze implementatie is niet hetzelfde als de [Azure dedicated host](../virtual-machines/dedicated-hosts.md) -service die wordt verschaft door andere Azure VM-families.


## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

Volg een zelf studie over het uitvoeren van een Snelstartgids voor het implementeren van een DCsv2-Series virtuele machine in minder dan 10 minuten. 

- **Azure-abonnement** : als u een exemplaar van een vertrouwelijk computing-VM wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of een andere aanschaf optie gebruiken. Als u een [gratis Azure-account](https://azure.microsoft.com/free/)gebruikt, hebt u geen quota voor de juiste hoeveelheid Azure-reken kernen.

- **Prijzen en regionale Beschik baarheid** : Zoek de prijzen voor DCsv2-Series vm's op de [pagina met prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Controleer of de beschik [bare producten per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) in azure-regio's.


- **Quotum voor kernen** : u moet mogelijk het quotum voor kernen in uw Azure-abonnement verhogen van de standaard waarde. Uw abonnement kan ook het aantal kernen beperken dat u kunt implementeren in bepaalde VM-grootte families, met inbegrip van de DCsv2-serie. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../azure-portal/supportability/per-vm-quota-requests.md) . Opmerking: de standaard limieten kunnen variëren, afhankelijk van de categorie van uw abonnement.

  > [!NOTE]
  > Neem contact op met de ondersteuning van Azure als er grootschalige capaciteits behoeften zijn. Azure-quota zijn krediet limieten, geen capaciteits garanties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor kernen die u gebruikt.
  
- **Grootte wijzigen** : vanwege hun gespecialiseerde hardware kunt u alleen de grootte van vertrouwelijke computing-instanties binnen dezelfde omvang aanpassen. U kunt bijvoorbeeld alleen het formaat van een virtuele machine uit de DCsv2-serie wijzigen van de grootte van een DCsv2-serie naar een andere. Het wijzigen van het formaat van een niet-vertrouwelijke computer grootte naar een vertrouwelijk computer formaat wordt niet ondersteund.  

- **Afbeelding** : om Intel-software Guard-extensie (Intel SGX) ondersteuning te bieden voor vertrouwelijke Compute-instanties, moeten alle implementaties worden uitgevoerd op installatie kopieën van de tweede generatie. Azure vertrouwelijk computing ondersteunt werk belastingen die worden uitgevoerd op Ubuntu 18,04 gen 2, Ubuntu 16,04 gen 2, Windows Server 2019 Gen2 en Windows Server 2016 gen 2. Meer informatie over [ondersteuning voor virtuele machines van de tweede generatie op Azure](../virtual-machines/generation-2.md) voor meer info over ondersteunde en niet-ondersteunde scenario's. 

- **Opslag** : Azure vertrouwelijk computing gegevens schijven van virtuele machines en onze tijdelijke OS-schijven bevinden zich op NVMe-schijven. Instanties ondersteunen alleen Premium-SSD en Standard-SSD schijven, niet Ultra-SSD of Standard-HDD. De grootte van de virtuele machine **DC8_v2** biedt geen ondersteuning voor Premium Storage. 

- **Schijf versleuteling** : vertrouwelijke reken instanties ondersteunen op dit moment geen schijf versleuteling. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Overwegingen voor hoge Beschik baarheid en herstel na nood gevallen

Wanneer u virtuele machines in azure gebruikt, bent u verantwoordelijk voor het implementeren van een oplossing voor hoge Beschik baarheid en herstel na nood gevallen om uitval tijd te voor komen. 

Azure vertrouwelijk computing ondersteunt momenteel geen zone-redundantie via Beschikbaarheidszones. Gebruik [beschikbaarheids sets](../virtual-machines/availability-set-overview.md)voor de hoogst mogelijke Beschik baarheid en redundantie voor vertrouwelijke computing. Vanwege de beperkingen van de hardware kunnen beschikbaarheids sets voor vertrouwelijke computing instanties slechts een maximum van 10 update domeinen hebben. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>De sjabloon implementatie met Azure Resource Manager (ARM)

Azure Resource Manager is de implementatie- en beheersservice voor Azure. Er wordt een Management-laag geboden waarmee u resources in uw Azure-abonnement kunt maken, bijwerken en verwijderen. U kunt beheer functies, zoals toegangs beheer, vergren delingen en tags, gebruiken om uw resources na de implementatie te beveiligen en te organiseren.

Zie [Sjabloonimlementatie Overview](../azure-resource-manager/templates/overview.md)(Engelstalig) voor meer informatie over arm-sjablonen.

Als u een DCsv2-Series-VM in een ARM-sjabloon wilt implementeren, gebruikt u de resource van de [virtuele machine](../virtual-machines/windows/template-description.md). Zorg ervoor dat u de juiste eigenschappen voor **vmSize** en voor uw **imageReference** opgeeft.

### <a name="vm-size"></a>VM-grootte

Geef een van de volgende grootten op in uw ARM-sjabloon in de virtuele-machine bron. Deze teken reeks wordt in **Eigenschappen** als **vmSize** geplaatst.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Installatie kopie van Gen2-besturings systeem

Onder **Eigenschappen** moet u ook verwijzen naar een installatie kopie onder **storageProfile**. Gebruik *slechts één* van de volgende installatie kopieën voor uw **imageReference**.

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
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Volgende stappen 

In dit artikel hebt u geleerd over de kwalificaties en configuraties die nodig zijn bij het maken van een virtuele machine met vertrouwelijke computing. U kunt nu de Microsoft Azure Marketplace gebruiken om een DCsv2-Series virtuele machine te implementeren.

> [!div class="nextstepaction"]
> [Een DCsv2-Series virtuele machine implementeren in azure Marketplace](quick-create-marketplace.md)