---
title: Afschaffing van herstel na nood gevallen tussen door de klant beheerde sites (met VMM) met behulp van Azure Site Recovery | Microsoft Docs
description: Details over toekomstige afschaffing van DR tussen sites die eigendom zijn van de klant met Hyper-V en tussen sites die worden beheerd door SCVMM naar Azure en alternatieve opties
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421838"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Afschaffing van herstel na nood gevallen tussen door de klant beheerde sites (met VMM) met behulp van Azure Site Recovery

In dit artikel worden de geplande afschaffing, de overeenkomstige implicaties en de alternatieve opties voor de klanten beschreven voor het volgende scenario:

DR tussen sites die eigendom zijn van de klant die worden beheerd door System Center Virtual Machine Manager (SCVMM) met Site Recovery

> [!IMPORTANT]
> Klanten wordt aangeraden de herstels tappen zo snel mogelijk uit te voeren om onderbrekingen van hun omgeving te voor komen. 

## <a name="what-changes-should-you-expect"></a>Welke wijzigingen moet u verwachten?

- Vanaf 2020 ontvangt u Azure Portal meldingen & e-mail communicatie met de aanstaande afschaffing van site-naar-site-replicatie van Hyper-V-Vm's. De afschaffing wordt gepland voor maart 2023.

- Als u een bestaande configuratie hebt, heeft dit geen invloed op de instelling.

- Zodra de scenario's zijn afgeschaft, kunnen de bestaande replicaties worden onderbroken als de klant de alternatieve benaderingen volgt. Klanten kunnen geen DR-gerelateerde bewerkingen weer geven, beheren of uitvoeren via de Azure Site Recovery ervaring in Azure Portal.
 
## <a name="alternatives"></a>Alternatieven 

Hieronder vindt u de alternatieven die de klant kan kiezen om ervoor te zorgen dat de DR-strategie niet wordt beïnvloed wanneer het scenario wordt afgeschaft. 

- Optie 1 (aanbevolen): Kies ervoor om [Azure te gaan gebruiken als het Dr-doel](hyper-v-vmm-azure-tutorial.md).


- Optie 2: u kunt door gaan met site-naar-site-replicatie met behulp van de onderliggende [Hyper-V-replica oplossing](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), maar u kunt geen Dr-configuraties beheren met Azure site Recovery in de Azure Portal. 


## <a name="remediation-steps"></a>Herstelstappen

Voer de volgende stappen uit als u ervoor kiest om te gaan met optie 1:

1. [Schakel de beveiliging uit van alle virtuele machines die zijn gekoppeld aan de VMMs](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Gebruik de optie **replicatie en verwijderen uitschakelen** of voer de vermelde scripts uit om ervoor te zorgen dat de replicatie-instellingen op locatie worden opgeruimd. 

2. [Verwijder de registratie van alle VMM-servers](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) van de site-naar-site-replicatie Configuratie.

3. [Azure-resources voorbereiden](tutorial-prepare-azure-for-hyperv.md) voor het inschakelen van replicatie van uw virtuele machines.
4. [On-premises Hyper-V-servers voorbereiden](hyper-v-prepare-on-premises-tutorial.md)
5. [Replicatie voor de virtuele machines in de VMM-Cloud instellen](hyper-v-vmm-azure-tutorial.md)
6. Optioneel, maar aanbevolen: [een Dr-analyse uitvoeren](tutorial-dr-drill-azure.md)

Als u ervoor kiest om te gaan met optie 2 van Hyper-V replica, voert u de volgende stappen uit:

1. Klik in **beveiligde items**  >  **gerepliceerde items** met de rechter muisknop op de machine > **Schakel replicatie uit**.
2. Selecteer **verwijderen** in **replicatie uitschakelen**.

    Hiermee wordt het gerepliceerde item verwijderd uit de Azure Site Recovery (facturering wordt gestopt). De replicatie Configuratie op de on-premises virtuele machine **wordt niet** opgeschoond. 

## <a name="next-steps"></a>Volgende stappen
Plan de afschaffing en kies een andere optie die het meest geschikt is voor uw infra structuur en uw bedrijf. Als er query's over zijn, kunt u contact opnemen met Microsoft Ondersteuning

