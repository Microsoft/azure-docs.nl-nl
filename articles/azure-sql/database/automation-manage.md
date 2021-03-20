---
title: Data bases beheren met Azure Automation
description: Meer informatie over hoe de Azure Automation-Service kan worden gebruikt om Azure SQL Database op schaal te beheren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: faf5b1108e28b352a0b8622feed8bdd99264ff16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327575"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Data bases in Azure SQL Database beheren met Azure Automation

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In deze hand leiding vindt u informatie over de Azure Automation-Service en hoe u deze kunt gebruiken voor het vereenvoudigen van het beheer van data bases in Azure SQL Database.

## <a name="about-azure-automation"></a>Over Azure Automation

[Azure Automation](https://azure.microsoft.com/services/automation/) is een Azure-service voor het vereenvoudigen van het beheer van Clouds met behulp van proces automatisering. Het gebruik van Azure Automation, langlopende, hand matige, fout gevoelige en regel matig herhaalde taken kunnen automatisch worden uitgevoerd om de betrouw baarheid, efficiëntie en tijd voor uw organisatie te verg Roten. Voor informatie over aan de slag gaat u naar [Azure Automation intro](../../automation/automation-intro.md)

Azure Automation biedt een engine voor het uitvoeren van werk stromen met hoge betrouw baarheid en hoge Beschik baarheid, die wordt geschaald om te voldoen aan uw behoeften naarmate uw organisatie groeit. In Azure Automation kunnen processen hand matig worden gestart door systemen van derden of op geplande intervallen zodat taken precies worden uitgevoerd wanneer dit nodig is.

Verlaag operationele overhead en maak IT/DevOps-personeel meer werk dat bedrijfs waarde kan toevoegen door te verplaatsen van uw cloud Beheer taken zodat deze automatisch door Azure Automation worden uitgevoerd.

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Hoe Azure Automation u kan helpen bij het beheren van uw data bases

Met Azure Automation kunt u data bases beheren in Azure SQL Database met behulp van [Power shell-cmdlets](/powershell/module/servicemanagement/azure.service/#sql) die beschikbaar zijn in de [Azure PowerShell-hulpprogram ma's](/powershell/azure/). Azure Automation heeft deze Azure SQL Database Power shell-cmdlets die beschikbaar zijn in het vak, zodat u al uw SQL Database beheer taken binnen de service kunt uitvoeren. U kunt deze cmdlets ook in Azure Automation koppelen met de cmdlets voor andere Azure-Services, om complexe taken te automatiseren in Azure-Services en op systemen van derden.

Azure Automation heeft ook de mogelijkheid om rechtstreeks te communiceren met SQL-servers door SQL-opdrachten uit te geven met behulp van Power shell.

De runbook-en module galerieën voor [Azure Automation](../../automation/automation-runbook-gallery.md) bieden diverse Runbooks van micro soft en de community die u in azure Automation kunt importeren. Als u één wilt gebruiken, moet u een runbook downloaden uit de galerie of u kunt vanuit de galerie of vanuit uw Automation-account rechtstreeks runbooks importeren in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Azure Automation hebt geleerd en hoe u deze kunt gebruiken om Azure SQL Database te beheren, volgt u deze koppelingen voor meer informatie over Azure Automation.

- [Overzicht van Azure Automation](../../automation/automation-intro.md)
- [Mijn eerste runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
