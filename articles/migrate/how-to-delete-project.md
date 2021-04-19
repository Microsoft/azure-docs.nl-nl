---
title: Een Azure Migrate-project verwijderen
description: In dit artikel leert u hoe u een project Azure Migrate verwijderen met behulp van de Azure Portal.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: how-to
ms.date: 10/22/2019
ms.openlocfilehash: 8c94bb23f5d514fef5cdacb855657efdf5219631
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714736"
---
# <a name="delete-an-azure-migrate-project"></a>Een Azure Migrate-project verwijderen

In dit artikel wordt beschreven hoe u een [Azure Migrate](./migrate-services-overview.md) verwijderen.


## <a name="before-you-start"></a>Voordat u begint

Voordat u een project verwijdert:

- Wanneer u een project verwijdert, worden de metagegevens van het project en de gedetecteerde machine verwijderd.
- Als u een Log Analytics-werkruimte hebt gekoppeld aan het hulpprogramma Serverevaluatie voor afhankelijkheidsanalyse, moet u beslissen of u de werkruimte wilt verwijderen. 
    - De werkruimte wordt niet automatisch verwijderd. Verwijder deze handmatig.
    - Controleer waarvoor een werkruimte wordt gebruikt voordat u deze verwijdert. Dezelfde Log Analytics-werkruimte kan worden gebruikt voor meerdere scenario's.
    - Voordat u het project verwijdert, vindt u een koppeling naar de werkruimte in **Azure Migrate - Servers** Azure Migrate -  >  **Serverevaluatie**, onder **OMS-werkruimte**.
    - Als u een werkruimte wilt verwijderen na het verwijderen van een project, gaat u naar de werkruimte in de relevante resourcegroep en volgt u [deze instructies.](../azure-monitor/logs/delete-workspace.md)


## <a name="delete-a-project"></a>Een project verwijderen


1. Open in Azure Portal de resourcegroep waarin het project is gemaakt.
2. Selecteer verborgen typen tonen op de **pagina van de resourcegroep.**
3. Selecteer het project en de bijbehorende resources die u wilt verwijderen.
    - Het resourcetype voor Azure Migrate projecten is **Microsoft.Migrate/migrateprojects.**
    - Bekijk in de volgende sectie de resources die zijn gemaakt voor detectie, evaluatie en migratie in een Azure Migrate project.
    - Als de resourcegroep alleen het Azure Migrate bevat, kunt u de hele resourcegroep verwijderen.
    - Als u een project uit de vorige versie van Azure Migrate wilt verwijderen, zijn de stappen hetzelfde. Het resourcetype voor deze projecten is **Migratieproject.**


## <a name="created-resources"></a>Gemaakte resources

Deze tabellen geven een overzicht van de resources die zijn gemaakt voor detectie, evaluatie en migratie in Azure Migrate project.

> [!NOTE]
> Verwijder de sleutelkluis voorzichtig, omdat deze mogelijk beveiligingssleutels bevat.

### <a name="projects-with-public-endpoint-connectivity"></a>Projecten met openbare eindpuntconnectiviteit

#### <a name="vmwarephysical-server"></a>VMware/fysieke server

**Resource** | **Type**
--- | ---
"Appliancename"kv | Key Vault
'Appliancename'-site | Microsoft.OffAzure/VMwareSites
'ProjectName' | Microsoft.Migrate/migrateprojects
ProjectName | Microsoft.Migrate/assessmentProjects
"ProjectName"rsvault | Recovery Services-kluis
"ProjectName"-MigrateVault-* | Recovery Services-kluis
migrateappligwsa* | Storage-account
migrateapplilsa* | Storage-account
migrateapplicsa* | Storage-account
migrateapplikv* | Key Vault
migrateapplisbns* | Service Bus-naamruimte

#### <a name="hyper-v-vm"></a>Hyper-V VM

**Resource** | **Type**
--- | ---
'ProjectName' | Microsoft.Migrate/migrateprojects
ProjectName | Microsoft.Migrate/assessmentProjects
HyperV*kv | Key Vault
HyperV*Site | Microsoft.OffAzure/HyperVSites
"ProjectName"-MigrateVault-* | Recovery Services-kluis

<br/>
De volgende tabellen geven een overzicht van de resources die door Azure Migrate voor het ontdekken, beoordelen en migreren van servers via een particulier netwerk met behulp van [Azure Private Link.](./how-to-use-azure-migrate-with-private-endpoints.md)

### <a name="projects-with-private-endpoint-connectivity"></a>Projecten met connectiviteit van privé-eindpunten

#### <a name="vmware-vms---agentless-migrations"></a>VMware-VM's - migraties zonder agent

**Type** | **Resource** | **Privé-eindpunt <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | 'ProjectName' | "ProjectName" \* pe 
Detectiesite (hoofdsite) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Project 'ApplianceName' \* \* pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/VMwareSites | "ApplianceName"*site | NA
Recovery Services-kluis | "ApplianceName"*vault | NA
Storage-account | "ApplianceName"*usa | "ApplianceName" \* usa \* pe
Recovery Services-kluis | "ProjectName"-MigrateVault-* | NA
Storage-account | migrateappligwsa* | NA
Storage-account | migrateapplilsa* | NA
Key Vault | migrateapplikv* | NA
Service Bus-naamruimte | migrateapplisbns* | NA

#### <a name="hyper-v-vms"></a>Virtuele Hyper-V-machines 

**Type** | **Resource** | **Privé-eindpunt <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | 'ProjectName' | "ProjectName" \* pe 
Detectiesite (hoofdsite) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Project 'ApplianceName' \* \* pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/HyperVSites | "ApplianceName"*site | NA
Recovery Services-kluis | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe

#### <a name="physical-servers--aws-vms--gcp-vms"></a>Fysieke servers/AWS-VM's/GCP-VM's 

**Type** | **Resource** | **Privé-eindpunt <br/>** |
--- | --- | ---
Microsoft.Migrate/migrateprojects | 'ProjectName' | "ProjectName" \* pe 
Detectiesite (hoofdsite) | "ProjectName"*mastersite | "ProjectName" \* mastersite \* pe 
Microsoft.Migrate/assessmentProjects | "ApplianceName"*project | Project 'ApplianceName' \* \* pe 
Key Vault | "ProjectName"*kv | "ProjectName" \* kv \* pe
Microsoft.OffAzure/serversites | "ApplianceName"*site | NA
Recovery Services-kluis | "ProjectName"-MigrateVault-* | "ProjectName"-MigrateVault-*pe


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het toevoegen van extra [hulpprogramma's](how-to-assess.md) voor evaluatie [en](how-to-migrate.md) migratie. 
