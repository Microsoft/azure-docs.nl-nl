---
title: Azure Automation Updatebeheer inschakelen vanaf een virtuele machine in azure
description: In dit artikel leest u hoe u Updatebeheer kunt inschakelen vanuit een Azure-VM.
services: automation
ms.subservice: update-management
ms.date: 11/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 39b5b1f988a118e609015f19a086fda434797356
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050259"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Updatebeheer inschakelen vanaf een virtuele Azure-machine

In dit artikel wordt beschreven hoe u de functie [updatebeheer](overview.md) kunt inschakelen op een of meer virtuele machines van Azure (VM). Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande Azure VM inschakelen met behulp van Updatebeheer.

> [!NOTE]
> Bij het inschakelen van Updatebeheer worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](../how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-security-overview.md) voor het beheren van computers.
* Een [virtuele machine](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-the-feature-for-deployment"></a>De functie voor implementatie inschakelen

1. In de [Azure Portal](https://portal.azure.com)selecteert u **virtuele machines** of zoekt en selecteert u **virtuele machines** op de start pagina.

2. Selecteer de virtuele machine waarvoor u Updatebeheer wilt inschakelen. Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account. U

3. Selecteer op de pagina VM onder **bewerkingen** de optie **gast en host-updates**.

    ![Selecteer gast en host updates in het linkerdeel venster](media/enable-from-vm/select-guest-and-os-updates.png)

4. U moet gemachtigd zijn `Microsoft.OperationalInsights/workspaces/read` om te bepalen of de virtuele machine is ingeschakeld voor een werk ruimte. Zie [machtigingen die nodig zijn om computers in te scha kelen](../automation-role-based-access-control.md#feature-setup-permissions)voor meer informatie over aanvullende machtigingen die vereist zijn. Zie [updatebeheer inschakelen vanuit een Automation-account](./enable-from-automation-account.md)voor meer informatie over het inschakelen van meerdere computers tegelijk.

5. Kies op de pagina Updatebeheer inschakelen de Log Analytics werk ruimte en het Automation-account en klik op **inschakelen** om updatebeheer in te scha kelen. Nadat u Updatebeheer hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de update-evaluatie van de VM kunt bekijken.

    ![Updatebeheer inschakelen](media/enable-from-vm/enable-update-management.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Updates en patches voor uw Azure-VM's beheren](manage-updates-for-vm.md) voor het gebruiken van Updatebeheer voor VM's.

* Zie [Problemen met Updatebeheer oplossen](../troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.