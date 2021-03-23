---
title: Een groot aantal servers in de VMware-omgeving beoordelen voor migratie naar Azure met Azure Migrate
description: Hierin wordt beschreven hoe u een groot aantal servers in de VMware-omgeving kunt beoordelen voor migratie naar Azure met behulp van de Azure Migrate-service.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10b8aaeaa25e49140dbf6f31c064c7f823d23e31
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778250"
---
# <a name="assess-large-numbers-of-servers-in-vmware-environment-for-migration-to-azure"></a>Een groot aantal servers in de VMware-omgeving beoordelen voor migratie naar Azure


In dit artikel wordt beschreven hoe u op basis van Azure Migrate het hulp programma detectie en evaluatie grote aantallen (1000 35000) van on-premises servers in VMware-omgeving kunt beoordelen voor migratie naar Azure.

[Azure Migrate](migrate-services-overview.md) biedt een hub aan hulpprogramma's waarmee u apps, infrastructuur en workloads op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogramma's en externe aanbiedingen van onafhankelijke softwareleveranciers (ISV’s). 

In dit artikel leert u het volgende:
> [!div class="checklist"]
> * Plan voor evaluatie op schaal.
> * Configureer Azure-machtigingen en bereid VMware voor evaluatie voor.
> * Maak een Azure Migrate project en maak een evaluatie.
> * Controleer de evaluatie tijdens het plannen van de migratie.


> [!NOTE]
> Als u een haalbaarheids test wilt uitproberen om een aantal servers te beoordelen voordat u op schaal evalueert, volgt u de [reeks zelf](./tutorial-discover-vmware.md) studies

## <a name="plan-for-assessment"></a>Beoordeling plannen

Bij het plannen van de beoordeling van een groot aantal servers in VMware-omgevingen, zijn er een aantal dingen die u moet overwegen:

- **Azure migrate projecten plannen**: Ontdek hoe u Azure migrate projecten implementeert. Als uw data centers zich bijvoorbeeld in verschillende geografische grafieken bevinden, of als u de meta gegevens voor detectie, analyses of migratie wilt opslaan in een andere geografie, hebt u mogelijk meerdere projecten nodig. 
- **Toestellen plannen**: Azure migrate gebruikt een on-premises Azure migrate apparaat, geïmplementeerd als een VMware-VM, om servers voortdurend te detecteren. Het apparaat bewaakt omgevings wijzigingen, zoals het toevoegen van servers, schijven of netwerk adapters. Ook worden er meta gegevens en prestatie gegevens naar Azure verzonden. U moet bepalen hoeveel apparaten er moeten worden geïmplementeerd.
- **Accounts voor detectie plannen**: het Azure migrate-apparaat maakt gebruik van een account met toegang tot vCenter Server om servers te detecteren voor evaluatie en migratie. Als u meer dan 10.000 servers detecteert, moet u meerdere accounts instellen, omdat er geen overlap ping is tussen de servers die zijn gedetecteerd vanaf een van de twee apparaten in een project. 

> [!NOTE]
> Als u meerdere apparaten instelt, zorg er dan voor dat er geen overlap is tussen de servers op de beschik bare vCenter-accounts. Een detectie met een dergelijke overlap is een niet-ondersteund scenario. Als een server wordt gedetecteerd door meer dan één apparaat, resulteert dit in dubbele waarden in detectie en in problemen tijdens het inschakelen van replicatie voor de server met behulp van de Azure Portal in server migratie.

## <a name="planning-limits"></a>Plannings limieten
 
Gebruik de limieten in deze tabel voor de planning.

**Planning** | **Limieten**
--- | --- 
**Azure Migrate projecten** | Evalueer Maxi maal 35.000 servers in een project.
**Azure Migrate-apparaat** | Een apparaat kan Maxi maal 10.000 servers op een vCenter Server detecteren.<br/> Een apparaat kan alleen verbinding maken met één vCenter Server.<br/> Een apparaat kan alleen worden gekoppeld aan één Azure Migrate project.<br/>  Een wille keurig aantal apparaten kan worden gekoppeld aan één Azure Migrate project. <br/><br/> 
**Groep** | U kunt Maxi maal 35.000 servers in één groep toevoegen.
**Azure Migrate beoordeling** | U kunt Maxi maal 35.000 servers in één evaluatie evalueren.

Hieronder volgen enkele voor beelden van implementaties:


**vCenter-Server** | **Servers op server** | **Aanbeveling** | **Actie**
---|---|---|---
Eén | < 10.000 | Eén Azure Migrate-project.<br/> Eén apparaat.<br/> Een vCenter-account voor detectie. | Apparaat instellen, verbinding maken met vCenter Server met een account.
Eén | > 10.000 | Eén Azure Migrate-project.<br/> Meerdere apparaten.<br/> Meerdere vCenter-accounts. | Stel het apparaat in voor elke 10.000-servers.<br/><br/> Stel vCenter-accounts in en Splits de inventaris om de toegang voor een account tot minder dan 10.000 servers te beperken.<br/> Elk apparaat verbinden met een vCenter-Server met een account.<br/> U kunt afhankelijkheden analyseren tussen servers die worden gedetecteerd met verschillende apparaten. <br/> <br/> Zorg ervoor dat er geen overlap is tussen de servers op de beschik bare vCenter-accounts. Een detectie met een dergelijke overlap is een niet-ondersteund scenario. Als een server wordt gedetecteerd door meer dan één apparaat, resulteert dit in een duplicaat van de detectie en in problemen tijdens het inschakelen van replicatie voor de server met behulp van de Azure Portal in server migratie.
Meerdere | < 10.000 |  Eén Azure Migrate-project.<br/> Meerdere apparaten.<br/> Een vCenter-account voor detectie. | Toestellen instellen, verbinding maken met vCenter Server met een account.<br/> U kunt afhankelijkheden analyseren tussen servers die worden gedetecteerd met verschillende apparaten.
Meerdere | > 10.000 | Eén Azure Migrate-project.<br/> Meerdere apparaten.<br/> Meerdere vCenter-accounts. | Als vCenter Server detectie < 10.000-servers, moet u voor elke vCenter Server een apparaat instellen.<br/><br/> Als vCenter Server detectie > 10.000-servers, moet u voor elke 10.000-servers een apparaat instellen.<br/> Stel vCenter-accounts in en Splits de inventaris om de toegang voor een account tot minder dan 10.000 servers te beperken.<br/> Elk apparaat verbinden met een vCenter-Server met een account.<br/> U kunt afhankelijkheden analyseren tussen servers die worden gedetecteerd met verschillende apparaten. <br/><br/> Zorg ervoor dat er geen overlap is tussen de servers op de beschik bare vCenter-accounts. Een detectie met een dergelijke overlap is een niet-ondersteund scenario. Als een server wordt gedetecteerd door meer dan één apparaat, resulteert dit in een duplicaat van de detectie en in problemen tijdens het inschakelen van replicatie voor de server met behulp van de Azure Portal in server migratie.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Detectie plannen in een omgeving met meerdere tenants

Als u van plan bent een omgeving met meerdere tenants te plannen, kunt u de detectie op de vCenter Server bereiken.

- U kunt het apparaat detectie bereik instellen op een vCenter Server Data Centers, clusters of map met clusters, hosts of de map hosts of afzonderlijke servers.
- Als uw omgeving wordt gedeeld door tenants en u elke Tenant afzonderlijk wilt detecteren, kunt u de toegang tot het vCenter-account beperken dat het apparaat voor detectie gebruikt. 
    - U kunt het bereik van VM-mappen beperken als de tenants hosts delen. Azure Migrate kan geen servers detecteren als het vCenter-account toegang heeft verleend op het niveau van de vCenter-VM-map. Als u uw detectie op basis van VM-mappen wilt beperken, kunt u dit doen door ervoor te zorgen dat het vCenter-account alleen-lezen toegang heeft dat is toegewezen op server niveau. [Meer informatie](set-discovery-scope.md).

## <a name="prepare-for-assessment"></a>Evaluatie voorbereiden

Azure en VMware voorbereiden voor het hulp programma voor detectie en evaluatie:

1. Controleer de [vereisten en beperkingen voor VMware-ondersteuning](migrate-support-matrix-vmware.md).
2. Stel machtigingen in voor uw Azure-account om te communiceren met Azure Migrate.
3. VMware voorbereiden voor evaluatie.

Volg de instructies in [deze zelf studie](./tutorial-discover-vmware.md) om deze instellingen te configureren.


## <a name="create-a-project"></a>Een project maken

In overeenstemming met uw plannings vereisten gaat u als volgt te werk:

1. Een Azure Migrate projecten maken.
2. Voeg het hulp programma voor Azure Migrate detectie en evaluatie toe aan de projecten.

[Meer informatie](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Een evaluatie maken en bekijken

1. Maak evaluaties voor servers in de VMware-omgeving.
1. Bekijk de evaluaties in de voor bereiding op de migratie planning.


Volg de instructies in [deze zelf studie](./tutorial-assess-vmware-azure-vm.md) om deze instellingen te configureren.
    

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u het volgende:
 
> [!div class="checklist"] 
> * Gepland om Azure Migrate beoordelingen te schalen voor servers in VMware-omgeving
> * Voor bereiding van Azure en VMware voor evaluatie
> * Een Azure Migrate project gemaakt en evaluaties uitgevoerd
> * Gereviseerde evaluaties in de voor bereiding voor de migratie.

Nu [leert u hoe](concepts-assessment-calculation.md) beoordelingen worden berekend en hoe u beoordelingen kunt [wijzigen](how-to-modify-assessment.md).