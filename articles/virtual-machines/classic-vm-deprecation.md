---
title: Azure-Vm's (klassiek) worden op 1 maart 2023 buiten gebruik gesteld.
description: Dit artikel bevat een globaal overzicht van de buiten gebruiks telling van Vm's die zijn gemaakt met het klassieke implementatie model.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 2fb710bab03d595d6e54bc8dd8fbda38c57123e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668201"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>Migreer uw IaaS-resources naar Azure Resource Manager op 1 maart 2023 

In 2014 hebben we de infra structuur als een service (IaaS) voor [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)geïntroduceerd. We hebben onze mogelijkheden ooit verbeterd. Omdat Azure Resource Manager nu volledige IaaS-mogelijkheden en andere voor uitgangen heeft, hebben we het beheer van virtuele IaaS-machines (Vm's) via [Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) op 28 februari 2020 afgeschaft. Deze functie wordt volledig buiten gebruik gesteld op 1 maart 2023. 

Op dit moment worden er met ongeveer 90 procent van de IaaS Vm's gebruik van Azure Resource Manager. Als u IaaS-resources gebruikt via ASM, start u de migratie nu. Voltooi dit uiterlijk 1 maart 2023 om te profiteren van [Azure Resource Manager](../azure-resource-manager/management/index.yml).

Vm's die zijn gemaakt met het klassieke implementatie model, volgen het [moderne levenscyclus beleid](https://support.microsoft.com/help/30881/modern-lifecycle-policy) voor buiten gebruiks telling.

## <a name="how-does-this-affect-me"></a>Wat betekent dit voor mij? 

- Vanaf 28 februari 2020 kunnen klanten die IaaS Vm's via ASM niet gebruiken in de maand van februari 2020 geen Vm's meer maken (klassiek). 
- Op 1 maart 2023 kunnen klanten geen IaaS-Vm's meer starten door gebruik te maken van ASM. Alle die nog worden uitgevoerd of toegewezen, worden gestopt en de toewijzing ervan ongedaan gemaakt. 
- Op 1 maart 2023 worden abonnementen die niet naar Azure Resource Manager worden gemigreerd, op de hoogte gesteld van tijd lijnen voor het verwijderen van alle resterende Vm's (klassiek).  

Deze buiten gebruiks telling heeft *geen* invloed op de volgende Azure-Services en-functionaliteit: 
- [Azure Cloud Services (klassiek)](../cloud-services/cloud-services-choose-me.md)
- Opslag accounts die *niet* worden gebruikt door virtuele machines (klassiek) 
- Virtuele netwerken die *niet* worden gebruikt door virtuele machines (klassiek) 
- Andere klassieke resources

## <a name="what-resources-are-available-for-this-migration"></a>Welke resources zijn er beschikbaar voor deze migratie?

- [Micro soft Q&A](/answers/topics/azure-virtual-machines-migration.html): micro soft en Community-ondersteuning voor migratie.

- [Azure-migratie ondersteuning](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}): specifiek ondersteunings team voor technische ondersteuning tijdens de migratie. Klanten die geen technische ondersteuning hebben, kunnen [gratis ondersteuning](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D) gebruiken die specifiek is voor deze migratie. 

- [Micro soft Fast track](https://www.microsoft.com/fasttrack): met Fast track kunt u in aanmerking komende klanten helpen bij het plannen van & uitvoering van deze migratie. [Noem uzelf](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0) voor het migratie programma voor domein controller.  

- Als uw bedrijf/organisatie is gekoppeld aan micro soft of werkt met micro soft-vertegenwoordigers (zoals Cloud Solution Architects (CSAs) of Technical Account Manager (TAMs)), kunt u het beste met hen samen werken voor aanvullende bronnen voor migratie.

## <a name="what-actions-should-i-take"></a>Welke acties moet ik ondernemen? 

Begin met het plannen van de migratie naar Azure Resource Manager, vandaag. 

1. Een lijst maken met alle betrokken Vm's: 

   - De Vm's van het type **virtuele machines (klassiek)** in het [deel venster VM](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines) van de Azure Portal zijn alle betrokken vm's in het abonnement. 
   - U kunt ook een query uitvoeren op Azure-resource grafiek door de [Portal](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22) of [Power shell](../governance/resource-graph/concepts/work-with-data.md) te gebruiken om de lijst met alle gemarkeerde vm's (klassiek) en gerelateerde informatie voor de geselecteerde abonnementen weer te geven. 
   - Op 8 februari en 2 september 2020 hebben we e-mail berichten verzonden met het onderwerp ' Start de migratie van uw IaaS-VM naar Azure Resource Manager ' om de eigen aars van het abonnement te plannen. Het e-mail bericht bevat een lijst met alle abonnementen en Vm's (klassieke) Vm's. Gebruik ze om deze lijst samen te stellen. 

1. Meer [informatie](./migration-classic-resource-manager-overview.md) over het migreren van uw [Linux](./migration-classic-resource-manager-plan.md) -en [Windows](./migration-classic-resource-manager-plan.md) -vm's (klassiek) naar Azure Resource Manager. Zie [Veelgestelde vragen over klassieke Azure Resource Manager migratie](./migration-classic-resource-manager-faq.md)voor meer informatie.

1. Het is raadzaam om de planning te starten met het [hulp programma voor migratie van platform ondersteuning](./migration-classic-resource-manager-overview.md) om uw bestaande vm's te migreren met drie eenvoudige stappen: valideren, voorbereiden en door voeren. Het hulp programma is ontworpen voor het migreren van uw virtuele machines binnen een minimum aan zonder uitval tijd. 

   - De eerste stap, validate, heeft geen invloed op uw bestaande implementatie en biedt een lijst van alle niet-ondersteunde scenario's voor migratie. 
   - Door loop de [lijst met tijdelijke oplossingen](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations) om uw implementatie te herstellen en deze gereed te maken voor migratie. 
   - In het ideale geval nadat alle validatie fouten zijn opgelost, moet u geen problemen ondervinden tijdens de stappen voor het voorbereiden en door voeren. Nadat het door voeren is voltooid, wordt uw implementatie Live gemigreerd naar Azure Resource Manager en kan deze vervolgens worden beheerd via nieuwe Api's die worden weer gegeven door Azure Resource Manager. 

   Als het hulp programma voor migratie niet geschikt is voor uw migratie, kunt u [andere compute-aanbiedingen](/azure/architecture/guide/technology-choices/compute-decision-tree) voor de migratie verkennen. Omdat er veel Azure Compute-aanbiedingen zijn, en ze verschillen van elkaar, kunnen we geen door het platform ondersteunde migratie paden aanbieden.  

1. [Neem contact op met de ondersteuning](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})voor technische vragen, problemen en hulp bij het toevoegen van abonnementen aan de acceptatie lijst.

1. Voltooi de migratie zo snel mogelijk om bedrijfs impact te voor komen en te profiteren van de verbeterde prestaties, beveiliging en nieuwe functies van Azure Resource Manager. 
