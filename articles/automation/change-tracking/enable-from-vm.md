---
title: Azure Automation Wijzigingen bijhouden en inventaris inschakelen vanuit een Azure VM
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt inschakelen vanuit een Azure-VM.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 61b45d6f6414b1e8e1f48f6d46957b21b9b8c58b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052495"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Wijzigingen bijhouden en inventaris inschakelen vanaf een virtuele Azure-machine

In dit artikel wordt beschreven hoe u een virtuele machine van Azure kunt gebruiken om [Wijzigingen bijhouden en inventaris](overview.md) op andere computers in te scha kelen. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie.

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](../how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-security-overview.md) voor het beheren van computers.
* Een [virtuele machine](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

1. In de [Azure Portal](https://portal.azure.com)selecteert u **virtuele machines** of zoekt en selecteert u **virtuele machines** op de start pagina.

2. Selecteer de virtuele machine waarvoor u Wijzigingen bijhouden en inventarisatie wilt inschakelen. Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account.

3. Selecteer op de pagina VM de optie **inventaris** of **Wijzigingen bijhouden** onder **configuratie beheer**.

4. U moet gemachtigd zijn `Microsoft.OperationalInsights/workspaces/read` om te bepalen of de virtuele machine is ingeschakeld voor een werk ruimte. Zie voor meer informatie over [aanvullende machtigingen die](../automation-role-based-access-control.md#feature-setup-permissions)zijn vereist. Zie [Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account](enable-from-automation-account.md)voor meer informatie over het inschakelen van meerdere computers tegelijk.

5. Kies de Log Analytics werk ruimte en het Automation-account en klik op **inschakelen** om wijzigingen bijhouden en inventaris voor de virtuele machine in te scha kelen. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden beheren](manage-change-tracking.md) en [inventaris beheren](manage-inventory-vms.md)voor meer informatie over het werken met de functie.

* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](../troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met de functie.