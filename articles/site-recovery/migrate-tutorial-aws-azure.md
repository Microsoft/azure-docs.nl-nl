---
title: AWS-VM's naar Azure migreren met Azure Migrate
description: Dit artikel biedt een beschrijving van de opties voor het migreren van AWS-exemplaren naar Azure en doet een aanbeveling voor Azure Migrate.
services: site-recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2019
ms.custom: MVC
ms.openlocfilehash: 6c3f20f0fa3bc6ad41e76626d3cb02ec1c0b96e9
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581345"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>AWS-VM’s (Amazon Web Services) migreren naar Azure

In dit artikel worden de opties voor het migreren van Amazon Web Services-exemplaren (AWS) naar Azure beschreven.

## <a name="migrate-with-azure-migrate"></a>Migreren met Azure Migrate

Het wordt aanbevolen AWS EC2-exemplaren naar Azure te migreren met behulp van de service [Azure Migrate](../migrate/migrate-services-overview.md). Azure Migrate is speciaal ontworpen voor servermigratie. Azure Migrate biedt een centrale hub voor detectie, evaluatie en migratie van on-premises machines naar Azure.

[Meer informatie](../migrate/tutorial-migrate-aws-virtual-machines.md) over het migreren van AWS-exemplaren met Azure Migrate. 


## <a name="migrate-with-site-recovery"></a>Migreren met Site Recovery

Site Recovery moet alleen worden gebruikt voor herstel na noodgevallen, niet voor migratie.

Als u Azure Site Recovery al gebruikt en u dit wilt blijven gebruiken voor AWS-migratie, volgt u dezelfde stappen die u gebruikt om [herstel van fysieke machines na noodgevallen](physical-azure-disaster-recovery.md) in te stellen.


> [!NOTE]
> Wanneer u een failover uitvoert voor herstel na noodgevallen, moet u als laatste stap de failover doorvoeren. Wanneer u AWS-exemplaren migreert, is de optie **Doorvoeren** niet relevant. Selecteer in plaats daarvan de optie **Migratie voltooien**. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees de veelgestelde vragen](../migrate/resources-faq.md) over Azure Migrate.
