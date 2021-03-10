---
title: Vertrouwelijke computing virtual machines op Azure
description: Meer informatie over Intel SGX-hardware voor het inschakelen van uw werk belastingen voor uw vertrouwelijke computer gebruik.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: 554260b2a2760380d3bb2d91ee25b4a03bf2f1ae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551365"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Overzicht van virtuele machines (Vm's) van Azure vertrouwelijk computing


Azure is de eerste cloudprovider die confidential computing biedt in een gevirtualiseerde omgeving. We hebben virtuele machines ontwikkeld die als een abstractielaag tussen de hardware en uw toepassing fungeren. U kunt workloads uitvoeren op schaal en met de opties voor redundantie en beschikbaarheid.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Virtuele machines met Intel SGX

In virtuele machines met Azure Confidential Computing wordt een deel van de hardware van de CPU gereserveerd voor een deel van de code en gegevens in uw toepassing. Dit beperkte gedeelte is het enclave. 

![VM-model](media/overview/hardware-backed-enclave.png)

De infrastructuur van Azure Confidential Computing bestaat momenteel uit een speciale SKU van virtuele machines (VM's). Deze VM's worden uitgevoerd op Intel-processors met Software Guard Extension (Intel SGX). [Intel SGX-](https://intel.com/sgx) is het onderdeel dat de verhoogde beveiliging mogelijk maakt die in confidential computing wordt toegepast. 

Nu biedt Azure de [DCsv2-serie](../virtual-machines/dcv2-series.md) die is gebouwd op Intel SGX-technologie voor het maken van een hardware-enclave. U kunt veilige enclavetoepassingen bouwen om uit te voeren op VM's uit de DCsv2-serie, om uw toepassingsgegevens en -code in gebruik te beveiligen. 

[Meer](virtual-machine-solutions.md) informatie over het implementeren van Azure vertrouwelijk computing virtuele machines met op hardware gebaseerde vertrouwde enclaves.

## <a name="enclaves"></a>Enclaves

Enclaves zijn beveiligde delen van de processor en het geheugen van de hardware. Gegevens of code in een enclave kunnen niet worden weergegeven, zelfs niet met een foutopsporingsprogramma. Als niet-vertrouwde code probeert de inhoud in een enclavegeheugen te wijzigen, wordt de omgeving uitgeschakeld en worden de bewerkingen geweigerd.

Eigenlijk kunt u een enclave beschouwen als een soort beveiligde doos. U plaatst versleutelde code en gegevens in de doos. Vanaf de buitenkant ziet u niets. U geeft de enclave een sleutel waarmee de gegevens kunnen worden ontsleuteld, waarna de gegevens worden verwerkt en opnieuw worden versleuteld voordat ze vanuit de enclave worden verzonden.

Elke enclave heeft een ingestelde grootte van de versleutelde pagina cache (EPC) die de hoeveelheid geheugen bepaalt die elke enclave kan bevatten. Grotere virtuele DCsv2-machines hebben meer EPC-geheugen. Lees de pagina met [DCsv2-specificaties](../virtual-machines/dcv2-series.md) voor de maximale grootte van EPC per VM.



### <a name="developing-applications-to-run-inside-enclaves"></a>Toepassingen ontwikkelen om te worden uitgevoerd in enclaves
Bij het ontwikkelen van toepassingen kunt u [softwarehulpprogramma's](application-development.md) gebruiken om delen van uw code en gegevens in de enclave af te schermen. Met deze hulpprogramma's kunt u ervoor zorgen dat uw code en gegevens niet kunnen worden weergegeven of gewijzigd door iemand buiten de vertrouwde omgeving. 

## <a name="next-steps"></a>Volgende stappen
- [Lees de aanbevolen procedures](virtual-machine-solutions.md) voor het implementeren van oplossingen op virtuele machines van Azure voor vertrouwelijke computing.
- [Een DCsv2-Series virtuele machine implementeren](quick-create-portal.md)
- [Een enclave-toepassing ontwikkelen](application-development.md) met behulp van de OE SDK