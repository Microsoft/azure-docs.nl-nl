---
title: Een groot aantal fysieke servers beoordelen voor migratie naar Azure met Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u een groot aantal fysieke servers kunt beoordelen voor migratie naar Azure met behulp van de Azure Migrate-service.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/19/2020
ms.author: hamusa
ms.openlocfilehash: 6009975337f8f4caaf43e4b8b715e42c6ce2ffdc
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494576"
---
# <a name="assess-large-numbers-of-physical-servers-for-migration-to-azure"></a>Een groot aantal fysieke servers beoordelen voor migratie naar Azure

In dit artikel wordt beschreven hoe u een groot aantal on-premises fysieke servers kunt beoordelen voor migratie naar Azure, met behulp van het hulp programma voor het evalueren van Azure Migrate-servers.

[Azure Migrate](migrate-services-overview.md) biedt een hub aan hulpprogramma's waarmee u apps, infrastructuur en workloads op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogramma's en externe aanbiedingen van onafhankelijke softwareleveranciers (ISV’s). 


In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Plan voor evaluatie op schaal.
> * Azure-machtigingen configureren en fysieke servers voorbereiden voor evaluatie.
> * Maak een Azure Migrate project en maak een evaluatie.
> * Controleer de evaluatie tijdens het plannen van de migratie.


> [!NOTE]
> Als u een haalbaarheids test wilt uitproberen om een aantal servers te beoordelen voordat u op schaal controleert, volgt u de [reeks zelf](./tutorial-discover-physical.md)studies.

## <a name="plan-for-assessment"></a>Beoordeling plannen

Bij het plannen van de beoordeling van een groot aantal fysieke servers, zijn er een aantal dingen die u moet nadenken:

- **Azure migrate projecten plannen**: Ontdek hoe u Azure migrate projecten implementeert. Als uw data centers zich bijvoorbeeld in verschillende geografische grafieken bevinden, of als u de meta gegevens voor detectie, analyses of migratie wilt opslaan in een andere geografie, hebt u mogelijk meerdere projecten nodig.
- **Toestellen plannen**: Azure migrate gebruikt een on-premises Azure migrate apparaat, geïmplementeerd op een Windows-computer, om voortdurend servers te detecteren voor evaluatie en migratie. Het apparaat bewaakt omgevings wijzigingen, zoals het toevoegen van Vm's, schijven of netwerk adapters. Ook worden er meta gegevens en prestatie gegevens naar Azure verzonden. U moet bepalen hoeveel apparaten er moeten worden geïmplementeerd.


## <a name="planning-limits"></a>Plannings limieten
 
Gebruik de limieten in deze tabel voor de planning.

**Planning** | **Limieten**
--- | --- 
**Azure Migrate projecten** | Evalueer Maxi maal 35.000 servers in een project.
**Azure Migrate-apparaat** | Een apparaat kan Maxi maal 1000 servers detecteren.<br/> Een apparaat kan alleen worden gekoppeld aan één Azure Migrate project.<br/> Een wille keurig aantal apparaten kan worden gekoppeld aan één Azure Migrate project. <br/><br/> 
**Groep** | U kunt Maxi maal 35.000 servers in één groep toevoegen.
**Azure Migrate beoordeling** | U kunt Maxi maal 35.000 servers in één evaluatie evalueren.


## <a name="other-planning-considerations"></a>Andere overwegingen bij de planning

- Als u de detectie van het apparaat wilt starten, moet u elke fysieke server selecteren. 

## <a name="prepare-for-assessment"></a>Evaluatie voorbereiden

Azure en fysieke servers voorbereiden voor Server evaluatie. 

1. Controleer de [vereisten en beperkingen van de fysieke server ondersteuning](migrate-support-matrix-physical.md).
2. Stel machtigingen in voor uw Azure-account om te communiceren met Azure Migrate.
3. De fysieke servers voorbereiden.

Volg de instructies in [deze zelf studie](./tutorial-discover-physical.md) om deze instellingen te configureren.

## <a name="create-a-project"></a>Een project maken

In overeenstemming met uw plannings vereisten gaat u als volgt te werk:

1. Maak een Azure Migrate-project.
2. Voeg het hulp programma voor het evalueren van Azure Migrate-servers toe aan de projecten.

[Meer informatie](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Een evaluatie maken en bekijken

1. Maak evaluaties voor fysieke servers.
1. Bekijk de evaluaties in de voor bereiding op de migratie planning.

[Meer informatie](tutorial-assess-physical.md) over het maken en beoordelen van evaluaties.
    

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u het volgende:
 
> [!div class="checklist"] 
> * Gepland om Azure Migrate Beoordelingen voor fysieke servers te schalen.
> * Voor bereiding van Azure en fysieke servers voor evaluatie.
> * Maak een Azure Migrate project en voer evaluaties uit.
> * Gereviseerde evaluaties in de voor bereiding voor de migratie.

Nu [leert u hoe](concepts-assessment-calculation.md) beoordelingen worden berekend en hoe u beoordelingen kunt [wijzigen](how-to-modify-assessment.md).