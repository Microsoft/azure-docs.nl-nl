---
title: Veelgestelde vragen over Azure Migrate
description: Krijg antwoorden op veelgestelde vragen over de Azure Migrate-service.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: a5db000ad76f23be60d279923a590204c2fcf5e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377377"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: algemene vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Migrate. Als u vragen hebt nadat u dit artikel hebt gelezen, kunt u ze in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum)plaatsen. U kunt ook deze artikelen bekijken:

- Vragen over het [Azure migrate apparaat](common-questions-appliance.md)
- Vragen over de [visualisatie van detectie, beoordeling en afhankelijkheid](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>Wat is Azure Migrate?

Azure Migrate biedt een centrale hub voor het bijhouden van de detectie, evaluatie en migratie van uw on-premises apps en werk belastingen, en persoonlijke en open bare Cloud-Vm's naar Azure. De hub biedt Azure Migrate tools voor evaluatie en migratie en ISV-aanbiedingen van derden. [Meer informatie](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Wat kan ik doen met Azure Migrate?

Gebruik Azure Migrate om on-premises infra structuur, toepassingen en gegevens te detecteren, te evalueren en te migreren naar Azure. Azure Migrate ondersteunt de evaluatie en migratie van on-premises virtuele VMware-machines, Hyper-V-Vm's, fysieke servers, andere gevirtualiseerde Vm's, data bases, Web-apps en virtuele Bureau bladen. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Wat is het verschil tussen Azure Migrate en Azure Site Recovery?

[Azure migrate](migrate-services-overview.md) biedt een gecentraliseerde hub voor evaluatie en migratie naar Azure. 

- Het gebruik van Azure Migrate biedt interoperabiliteit en toekomstige uitbrei ding met Azure Migrate-hulpprogram ma's, andere Azure-Services en hulpprogram ma's van derden.
- De Azure Migrate: hulp programma voor server migratie is bedoeld voor server migratie naar Azure. Deze is geoptimaliseerd voor migratie. U hoeft niet meer te weten te komen over concepten en scenario's die niet rechtstreeks relevant zijn voor migratie. 
- Er zijn geen kosten voor het gebruik van hulpprogram ma's voor de migratie van 180 dagen, vanaf het moment dat de replicatie voor een virtuele machine wordt gestart. Het biedt u de tijd om de migratie te volt ooien. U betaalt alleen voor de opslag-en netwerk resources die worden gebruikt tijdens de replicatie, en voor de reken kosten die tijdens de test migraties worden verbruikt.
- Azure Migrate ondersteunt alle migratie scenario's die door Site Recovery worden ondersteund. Daarnaast biedt Azure Migrate voor VMware-Vm's een migratie optie zonder agent.
- Er worden prioriteiten gegeven voor nieuwe migratie functies voor de Azure Migrate: alleen hulp programma voor server migratie. Deze functies zijn niet gericht op Site Recovery.

[Azure site Recovery](../site-recovery/site-recovery-overview.md) moet alleen worden gebruikt voor herstel na nood gevallen.

Het Azure Migrate: hulp programma voor server migratie maakt gebruik van enige back-end-Site Recovery functionaliteit voor het migreren van een aantal on-premises machines.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Ik heb een project met de vorige klassieke ervaring van Azure Migrate. Hoe kan ik beginnen met het gebruik van de nieuwe versie?

Klassieke Azure Migrate buiten gebruik worden gesteld in februari 2024. Na februari 2024 wordt de klassieke versie van Azure Migrate niet meer ondersteund en worden de meta gegevens van de inventaris in het klassieke project verwijderd. U kunt projecten of onderdelen in de vorige versie niet upgraden naar de nieuwe versie. U moet [een nieuw Azure Migrate-project maken](create-manage-projects.md) en daar [hulpprogramma's voor evaluatie en migratie aan toevoegen](./create-manage-projects.md). Gebruik de zelfstudies om te begrijpen hoe u de beschikbare hulpprogramma’s voor evaluatie en migratie kunt gebruiken. Als u een Log Analytics werk ruimte aan een klassiek project hebt gekoppeld, kunt u deze koppelen aan een project van de huidige versie nadat u het klassieke project hebt verwijderd.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Wat is het verschil tussen Azure Migrate: Server analyse en de kaart Toolkit?

Server evaluatie biedt een evaluatie van de voor bereiding op de migratie en de evaluatie van werk belastingen voor migratie naar Azure. De [micro soft Assessment and planning (kaart) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) helpt bij andere taken, waaronder migratie planning voor nieuwere versies van Windows-besturings systemen voor clients en servers, en het bijhouden van software gebruik. Voor deze scenario's kunt u de kaart Toolkit blijven gebruiken.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Wat is het verschil tussen server evaluatie en het Site Recovery Deployment Planner?

Server evaluatie is een hulp programma voor migratie planning. De Site Recovery Deployment Planner is een hulp programma voor herstel na nood gevallen.

Kies uw hulp programma op basis van wat u wilt doen:

- **On-premises migratie naar Azure plannen**: als u van plan bent om uw on-premises servers naar Azure te migreren, gebruikt u Server evaluatie voor migratie planning. Server evaluatie evalueert on-premises workloads en biedt hulp middelen en hulpprogram ma's waarmee u kunt migreren. Nadat het migratie plan is geïmplementeerd, kunt u hulpprogram ma's zoals Azure Migrate gebruiken: Server migratie om de machines te migreren naar Azure.
- **Herstel na nood geval naar Azure plannen**: als u van plan bent om herstel na nood gevallen van on-premises naar azure met site Recovery in te stellen, gebruikt u de site Recovery Deployment planner. De Deployment Planner biedt een diep gaande Site Recovery-specifieke evaluatie van uw on-premises omgeving voor nood herstel. Het bevat aanbevelingen met betrekking tot herstel na nood gevallen, zoals replicatie en failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Hoe werkt de migratie van servers met Site Recovery?

- Als u Azure Migrate: Server migratie voor het uitvoeren van *een niet* -werkende migratie van on-premises virtuele VMware-machines, is migratie standaard naar Azure migrate en wordt site Recovery niet gebruikt.
- Als u Azure Migrate: Server migratie gebruikt voor het uitvoeren van een *op agents gebaseerde* migratie van VMware-vm's, of als u virtuele Hyper-V-machines of fysieke servers migreert, Azure migrate: Server migratie maakt gebruik van de Azure site Recovery replicatie-engine.

## <a name="which-geographies-are-supported"></a>Welke geografische gebieden worden ondersteund?

Bekijk de ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?

Bepaal het hulp programma dat u nodig hebt en voeg het hulp programma toe aan een Azure Migrate-project. 

Een ISV-hulp programma of-overwerker toevoegen:

1. Ga aan de slag met het verkrijgen van een licentie of Meld u aan voor een gratis proef versie, in overeenstemming met het hulp programma beleid. Licentie verlening voor hulpprogram ma's is in overeenstemming met het ISV-of hulp programma licentie model.
2. In elk hulp programma is er een optie om verbinding te maken met Azure Migrate. Volg de instructies en documentatie van het hulp programma om verbinding te maken met het hulp programma met Azure Migrate.

U kunt uw migratie traject volgen vanuit het Azure Migrate project, in Azure en in andere hulpprogram ma's.

## <a name="how-do-i-delete-a-project"></a>Een project Hoe kan ik verwijderen?

Meer informatie over het [verwijderen van een project](how-to-delete-project.md). 

## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).