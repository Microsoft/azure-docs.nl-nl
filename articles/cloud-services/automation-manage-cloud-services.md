---
title: Azure Cloud Services (klassiek) beheren met Azure Automation | Microsoft Docs
description: Meer informatie over hoe de Azure Automation-Service kan worden gebruikt voor het beheren van Azure Cloud Services op schaal.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 530efd09f3632637c6a12648495dcff0e7bf0e6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743488"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Azure Cloud Services (klassiek) beheren met Azure Automation

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.
In deze hand leiding vindt u informatie over de Azure Automation-Service en hoe u deze kunt gebruiken om het beheer van uw Azure-Cloud Services te vereenvoudigen.

## <a name="what-is-azure-automation"></a>Wat is Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) is een Azure-service voor het vereenvoudigen van het beheer van Clouds met behulp van proces automatisering. Het gebruik van Azure Automation, langlopende, hand matige, fout gevoelige en regel matig herhaalde taken kunnen automatisch worden uitgevoerd om de betrouw baarheid, efficiëntie en tijd voor uw organisatie te verg Roten.

Azure Automation biedt een zeer betrouw bare en Maxi maal beschik bare engine voor werk stroom uitvoering die kan worden geschaald om te voldoen aan uw behoeften naarmate uw organisatie groeit. In Azure Automation kunnen processen hand matig worden gestart door systemen van derden of op geplande intervallen, zodat taken precies worden uitgevoerd wanneer dat nodig is.

Verlaag operationele overhead en maak IT/DevOps-personeel meer werk dat bedrijfs waarde kan toevoegen door te verplaatsen van uw cloud Beheer taken zodat deze automatisch door Azure Automation worden uitgevoerd.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Hoe kan Azure Automation helpen bij het beheren van Azure Cloud Services?
Azure Cloud Services kunnen worden beheerd in Azure Automation met behulp van de Power shell-cmdlets die beschikbaar zijn in de [Azure PowerShell-hulpprogram ma's](/powershell/). Azure Automation heeft de volgende Power shell-cmdlets voor Cloud Services beschikbaar in het vak, zodat u al uw beheer taken voor de Cloud service in de service kunt uitvoeren. U kunt deze cmdlets ook in Azure Automation koppelen met de cmdlets voor andere Azure-Services, om complexe taken in Azure-Services en systemen van derden te automatiseren.

Een voor beeld van het gebruik van Azure Automation voor het beheren van Azure Cloud Services omvatten:

* [Continue implementatie van een Cloud service wanneer cscfg of cspkg wordt bijgewerkt in Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Cloud service-exemplaren parallel opnieuw opstarten, één upgrade domein tegelijk](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van Azure Automation hebt geleerd en hoe u deze kunt gebruiken om Azure Cloud Services te beheren, volgt u deze koppelingen voor meer informatie over Azure Automation.

* [Overzicht van Azure Automation](../automation/automation-intro.md)
* [Mijn eerste runbook](../automation/learn/automation-tutorial-runbook-graphical.md)