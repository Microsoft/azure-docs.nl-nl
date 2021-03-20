---
title: Een groot aantal virtuele Hyper-V-machines evalueren voor migratie naar Azure met Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u een groot aantal virtuele Hyper-V-machines kunt beoordelen voor migratie naar Azure met behulp van de Azure Migrate-service.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 92c275ee3f8e00e71b80e448c9adb94f0b6d21dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753719"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Een groot aantal virtuele Hyper-V-machines beoordelen voor migratie naar Azure

In dit artikel wordt beschreven hoe u een groot aantal on-premises virtuele Hyper-V-machines kunt beoordelen voor migratie naar Azure, met behulp van het hulp programma Azure Migrate server Assessment.

[Azure Migrate](migrate-services-overview.md) biedt een hub aan hulpprogramma's waarmee u apps, infrastructuur en workloads op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogramma's en externe aanbiedingen van onafhankelijke softwareleveranciers (ISV’s). 


In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Plan voor evaluatie op schaal.
> * Azure-machtigingen configureren en Hyper-V voorbereiden voor evaluatie.
> * Maak een Azure Migrate project en maak een evaluatie.
> * Controleer de evaluatie tijdens het plannen van de migratie.


> [!NOTE]
> Als u een haalbaarheids test wilt uitproberen om een aantal Vm's te beoordelen voordat u op schaal controleert, volgt u de [reeks zelf](./tutorial-discover-hyper-v.md) studies

## <a name="plan-for-assessment"></a>Beoordeling plannen

Bij het plannen van de beoordeling van een groot aantal virtuele Hyper-V-machines, kunt u een aantal dingen nadenken:

- **Azure migrate projecten plannen**: Ontdek hoe u Azure migrate projecten implementeert. Als uw data centers zich bijvoorbeeld in verschillende geografische grafieken bevinden, of als u de meta gegevens voor detectie, analyses of migratie wilt opslaan in een andere geografie, hebt u mogelijk meerdere projecten nodig.
- **Toestellen plannen**: Azure migrate gebruikt een on-premises Azure migrate-apparaat, geïmplementeerd als een Hyper-V-VM, om voortdurend vm's te detecteren voor evaluatie en migratie. Het apparaat bewaakt omgevings wijzigingen, zoals het toevoegen van Vm's, schijven of netwerk adapters. Ook worden er meta gegevens en prestatie gegevens naar Azure verzonden. U moet bepalen hoeveel apparaten er moeten worden geïmplementeerd.


## <a name="planning-limits"></a>Plannings limieten
 
Gebruik de limieten in deze tabel voor de planning.

**Planning** | **Limieten**
--- | --- 
**Azure Migrate projecten** | Evalueer Maxi maal 35.000 Vm's in een project.
**Azure Migrate-apparaat** | Een apparaat kan Maxi maal 5000 Vm's detecteren.<br/> Een apparaat kan verbinding maken met Maxi maal 300 Hyper-V-hosts.<br/> Een apparaat kan alleen worden gekoppeld aan één Azure Migrate project.<br/> Een wille keurig aantal apparaten kan worden gekoppeld aan één Azure Migrate project. <br/><br/> 
**Groep** | U kunt Maxi maal 35.000 Vm's toevoegen aan één groep.
**Azure Migrate beoordeling** | U kunt Maxi maal 35.000 Vm's in één evaluatie evalueren.



## <a name="other-planning-considerations"></a>Andere overwegingen bij de planning

- Als u de detectie van het apparaat wilt starten, moet u elke Hyper-V-host selecteren. 
- Als u een omgeving met meerdere tenants hebt, kunt u momenteel alleen Vm's detecteren die horen bij een specifieke Tenant. 

## <a name="prepare-for-assessment"></a>Evaluatie voorbereiden

Azure en Hyper-V voorbereiden voor de evaluatie van de server. 

1. Controleer de [vereisten en beperkingen voor Hyper-V-ondersteuning](migrate-support-matrix-hyper-v.md).
2. Stel machtigingen in voor uw Azure-account om te communiceren met Azure Migrate
3. Hyper-V-hosts en virtuele machines voorbereiden

Volg de instructies in [deze zelf studie](./tutorial-discover-hyper-v.md) om deze instellingen te configureren.

## <a name="create-a-project"></a>Een project maken

In overeenstemming met uw plannings vereisten gaat u als volgt te werk:

1. Een Azure Migrate projecten maken.
2. Voeg het hulp programma voor het evalueren van Azure Migrate-servers toe aan de projecten.

[Meer informatie](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Een evaluatie maken en bekijken

1. Maak evaluaties voor virtuele Hyper-V-machines.
1. Bekijk de evaluaties in de voor bereiding op de migratie planning.

[Meer informatie](tutorial-assess-hyper-v.md) over het maken en beoordelen van evaluaties.
    

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u het volgende:
 
> [!div class="checklist"] 
> * Gepland om Azure Migrate Beoordelingen voor virtuele Hyper-V-machines te schalen
> * Azure en Hyper-V zijn voor bereid voor evaluatie
> * Een Azure Migrate project gemaakt en evaluaties uitgevoerd
> * Gereviseerde evaluaties in de voor bereiding voor de migratie.

Ontdek nu [hoe](concepts-assessment-calculation.md) beoordelingen worden berekend en hoe u [beoordelingen wijzigt](how-to-modify-assessment.md)