---
title: Community tools-klassieke resources naar Azure Resource Manager verplaatsen
description: In dit artikel worden de hulpprogram ma's die door de community zijn verschaft, gecatalogiseerd voor het migreren van IaaS-resources van klassiek naar het implementatie model van Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f165acb72fdf881a0828c38db577be1f8741384e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674742"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Hulpprogram ma's van de community voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager

> [!IMPORTANT]
> Nu gebruiken we op ongeveer 90% IaaS Vm's [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Vanaf 28 februari 2020 zijn klassieke Vm's afgeschaft en worden ze volledig buiten gebruik gesteld op 1 maart 2023. Meer [informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe dit van invloed is op u](classic-vm-deprecation.md#how-does-this-affect-me).

In dit artikel worden de hulpprogram ma's die door de community zijn verschaft, gecatalogiseerd om hulp te bieden bij de migratie van IaaS-resources van klassiek naar het Azure Resource Manager-implementatie model.

> [!NOTE]
> Deze hulpprogram ma's worden niet officieel ondersteund door Microsoft Ondersteuning. Daarom worden ze op GitHub geopend en zijn we blij met het accepteren van pull voor fixes of extra scenario's. Als u een probleem wilt melden, gebruikt u de functie GitHub issues.
> 
> Door te migreren met deze hulpprogram ma's wordt uitval tijd voor uw klassieke virtuele machine veroorzaakt. Als u op zoek bent naar platform ondersteunde migratie, gaat u naar 
> 
>   * [Door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager stack](migration-classic-resource-manager-overview.md)
>   * [Technisch diep gaande kennis van de migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Migratie van IaaS-resources van klassiek naar Azure Resource Manager met behulp van Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Dit is een verzameling hulpprogram ma's die zijn gemaakt als onderdeel van ondernemings migraties van Azure service management tot Azure Resource Manager. Met dit hulp programma kunt u uw infra structuur repliceren naar een ander abonnement dat kan worden gebruikt voor het testen van de migratie en eventuele problemen verhelpen voordat u de migratie op uw productie abonnement uitvoert.

[Koppeling naar de documentatie van het hulp programma](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz is een extra optie voor het migreren van een volledige set klassieke IaaS-resources naar Azure Resource Manager IaaS-resources. De migratie kan plaatsvinden binnen hetzelfde abonnement of tussen verschillende abonnementen en abonnements typen (bijvoorbeeld CSP-abonnementen).

[Koppeling naar de documentatie van het hulp programma](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Power shell gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [CLI gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md)
* [Bekijk de veelgestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md)
