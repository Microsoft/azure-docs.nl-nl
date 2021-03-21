---
title: Een Azure Migrate-project verwijderen
description: In dit artikel leest u hoe u een Azure Migrate project kunt verwijderen met behulp van de Azure Portal.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: bfb4db5d3ebf69f9c7f552c175d33a8b817d1562
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100595143"
---
# <a name="delete-an-azure-migrate-project"></a>Een Azure Migrate-project verwijderen

In dit artikel wordt beschreven hoe u een [Azure migrate](./migrate-services-overview.md) project verwijdert.


## <a name="before-you-start"></a>Voordat u begint

Voordat u een project verwijdert:

- Wanneer u een project verwijdert, worden de meta gegevens van het project en de gedetecteerde computer verwijderd.
- Als u een Log Analytics-werk ruimte hebt gekoppeld aan het hulp programma voor Server evaluatie voor afhankelijkheids analyse, moet u beslissen of u de werk ruimte wilt verwijderen. 
    - De werk ruimte wordt niet automatisch verwijderd. Verwijder deze hand matig.
    - Controleer wat er voor een werk ruimte wordt gebruikt voordat u deze verwijdert. Dezelfde Log Analytics-werk ruimte kan voor meerdere scenario's worden gebruikt.
    - Voordat u het project verwijdert, kunt u een koppeling naar de werk ruimte vinden in de evaluatie van **Azure migrate-servers**  >  **Azure migrate-server**, onder **OMS-werk ruimte**.
    - Als u een werk ruimte wilt verwijderen nadat u een project hebt verwijderd, gaat u naar de werk ruimte in de relevante resource groep en volgt u [deze instructies](../azure-monitor/logs/delete-workspace.md).


## <a name="delete-a-project"></a>Een project verwijderen


1. Open in de Azure Portal de resource groep waarin het project is gemaakt.
2. Selecteer op de pagina resource groep de optie **verborgen typen weer geven**.
3. Selecteer het project en de gekoppelde resources die u wilt verwijderen.
    - Het resource type voor Azure Migrate Projects is **micro soft. migrate/migrateprojects**.
    - Bekijk in de volgende sectie de resources die zijn gemaakt voor detectie, beoordeling en migratie in een Azure Migrate project.
    - Als de resource groep alleen het Azure Migrate project bevat, kunt u de hele resource groep verwijderen.
    - Als u een project uit de vorige versie van Azure Migrate wilt verwijderen, zijn de stappen hetzelfde. Het resource type voor deze projecten is het **migratie project**.


## <a name="created-resources"></a>Gemaakte resources

In deze tabellen vindt u een overzicht van de resources die zijn gemaakt voor detectie, beoordeling en migratie in een Azure Migrate project.

> [!NOTE]
> Verwijder de sleutel kluis met een waarschuwing omdat deze beveiligings sleutels kan bevatten.

### <a name="vmwarephysical-server"></a>VMware/fysieke server

**Resource** | **Type**
--- | ---
"Apparaatnaam" KV | Key Vault
De site ' Apparaatnaam ' | Micro soft. OffAzure/VMwareSites
ProjectName | Micro soft. migrate/migrateprojects
Project ProjectName | Micro soft. migrate/assessmentProjects
' ProjectName ' rsvault | Recovery Services-kluis
"ProjectName"-MigrateVault-* | Recovery Services-kluis
migrateappligwsa* | Storage-account
migrateapplilsa* | Storage-account
migrateapplicsa* | Storage-account
migrateapplikv* | Key Vault
migrateapplisbns16041 | Service Bus-naamruimte

### <a name="hyper-v-vm"></a>Hyper-V VM 

**Resource** | **Type**
--- | ---
ProjectName | Micro soft. migrate/migrateprojects
Project ProjectName | Micro soft. migrate/assessmentProjects
Hyper-v * KV | Key Vault
Hyper-v *-site | Micro soft. OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services-kluis


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van extra hulpprogram ma's voor [evaluatie](how-to-assess.md) en [migratie](how-to-migrate.md) . 
