---
title: Azure Analysis Services beheren met Power shell | Microsoft Docs
description: Beschrijft Azure Analysis Services Power shell-cmdlets voor veelvoorkomende beheer taken, zoals het maken van servers, het onderbreken van bewerkingen of het wijzigen van het service niveau.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28f414c5eaaea7b987f2c3694cb8fc73b70838e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92018760"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Azure Analysis Services beheren met PowerShell

In dit artikel worden de Power shell-cmdlets beschreven die worden gebruikt voor het uitvoeren van Azure Analysis Services server-en database beheer taken. 

Server Resource beheer taken zoals het maken of verwijderen van een server, het onderbreken of hervatten van Server bewerkingen of het wijzigen van de service niveau (laag) gebruik Azure Analysis Services-cmdlets. Andere taken voor het beheren van data bases, zoals het toevoegen of verwijderen van Rolgroepen, het verwerken of het partitioneren van cmdlets die zijn opgenomen in dezelfde SqlServer-module als SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Machtigingen

Voor de meeste Power shell-taken moet u beheerders bevoegdheden hebben op de Analysis Services-server die u beheert. Geplande power shell-taken zijn bewerkingen zonder toezicht. De account of Service-Principal waarop de scheduler wordt uitgevoerd, moet beheerders bevoegdheden hebben op de Analysis Services-server. 

Voor Server bewerkingen met Azure PowerShell-cmdlets moet uw account of het account dat scheduler uitvoert ook behoren tot de rol van eigenaar van de resource in [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Resource-en Server bewerkingen 

Module installeren- [AZ. AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentatie- [AZ. AnalysisServices Reference](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Databasebewerkingen

Azure Analysis Services database bewerkingen gebruiken dezelfde SqlServer-module als SQL Server Analysis Services. Niet alle cmdlets worden echter ondersteund voor Azure Analysis Services. 

De SqlServer-module biedt taak-specifieke data base management-cmdlets en de cmdlet voor algemeen Invoke-ASCmd gebruik die een TMSL-query (Tabellair model scripting language) of script accepteert. De volgende cmdlets in de SqlServer-module worden ondersteund voor Azure Analysis Services.

Module installeren- [sqlserver](https://www.powershellgallery.com/packages/SqlServer)   
Documentatie- [sqlserver-referentie](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Ondersteunde cmdlets

|Cmdlet|Beschrijving|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Een lid toevoegen aan een databaserol.| 
|[Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase)|Back-up maken van een Analysis Services-Data Base.|  
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Een lid uit een databaserol verwijderen.|   
|[Invoke-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Voer een TMSL-script uit.|
|[Invoke-ProcessASDatabase](/powershell/module/sqlserver/invoke-processasdatabase)|Een Data Base verwerken.|  
|[Invoke-ProcessPartition](/powershell/module/sqlserver/invoke-processpartition)|Een partitie verwerken.| 
|[Invoke-ProcessTable](/powershell/module/sqlserver/invoke-processtable)|Een tabel verwerken.|  
|[Samen voegen-partitie](/powershell/module/sqlserver/merge-partition)|Een partitie samen voegen.|  
|[Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase)|Een Analysis Services-Data Base herstellen.| 
  

## <a name="related-information"></a>Gerelateerde informatie

* [SQL Server PowerShell](/sql/powershell/sql-server-powershell)      
* [SQL Server Power shell-module downloaden](/sql/ssms/download-sql-server-ps-module)   
* [SSMS downloaden](/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer-module in PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Program meren in tabellair model voor compatibiliteits niveau 1200 en hoger](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)