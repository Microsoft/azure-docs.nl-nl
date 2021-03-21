---
title: Azure Automation Updatebeheer van de Azure Portal inschakelen
description: In dit artikel leest u hoe u Updatebeheer kunt inschakelen vanuit de Azure Portal.
services: automation
ms.subservice: update-management
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089c5fea6ac4a6fc4fb25af2d631335ef51cf4cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054903"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Updatebeheer inschakelen via Azure Portal

In dit artikel wordt beschreven hoe u de [updatebeheer](overview.md) functie voor vm's kunt inschakelen door te bladeren door de Azure Portal. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande Azure VM inschakelen met behulp van Updatebeheer.

Het aantal resource groepen dat u kunt gebruiken voor het beheren van uw Vm's, wordt beperkt door de [implementatie limieten van Resource Manager](../../azure-resource-manager/templates/deploy-to-resource-group.md). Implementaties van Resource Manager, niet te verwarren met update-implementaties, zijn beperkt tot vijf resource groepen per implementatie. Twee van deze resource groepen zijn gereserveerd voor het configureren van de Log Analytics-werk ruimte, het Automation-account en gerelateerde resources. Hiermee kunt u drie resource groepen selecteren voor beheer door Updatebeheer. Deze limiet is alleen van toepassing op gelijktijdige installatie, niet het aantal resource groepen dat kan worden beheerd door een automatiserings functie.

> [!NOTE]
> Als Updatebeheer wordt ingeschakeld, worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](../how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-security-overview.md) voor het beheren van computers.
* Een [virtuele machine](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure op https://portal.azure.com.

## <a name="enable-update-management"></a>Updatebeheer inschakelen

1. Ga in het Azure Portal naar **virtuele machines**.

2. Gebruik op de pagina **virtuele machines** de selectie vakjes om de vm's te kiezen die u wilt toevoegen aan updatebeheer. U kunt machines toevoegen voor Maxi maal drie verschillende resource groepen tegelijk. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account.

    ![Lijst met Vm's](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Gebruik de filter besturings elementen om Vm's uit verschillende abonnementen, locaties en resource groepen te selecteren. U kunt op het selectie vakje aan de bovenkant klikken om alle virtuele machines in een lijst te selecteren.

    [![Updatebeheer inschakelen](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. Selecteer **Services** en selecteer **updatebeheer** voor de functie updatebeheer.

4. De lijst met virtuele machines wordt gefilterd om alleen de virtuele machines weer te geven die zich in hetzelfde abonnement en dezelfde locatie bevinden. Als uw virtuele machines zich in meer dan drie resource groepen bevinden, worden de eerste drie resource groepen geselecteerd.

5. Een bestaande Log Analytics-werk ruimte en een Automation-account zijn standaard geselecteerd. Als u een andere Log Analytics-werk ruimte en een Automation-account wilt gebruiken, selecteert u **aangepast** om ze te selecteren op de pagina aangepaste configuratie. Wanneer u een werk ruimte voor Log Analytics kiest, wordt er een controle uitgevoerd om te bepalen of deze is gekoppeld aan een Automation-account. Als een gekoppeld Automation-account wordt gevonden, wordt het volgende scherm weer gegeven. Selecteer **Ok** wanneer u gereed bent.

    [![Werk ruimte en account selecteren](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. Als de geselecteerde werk ruimte niet is gekoppeld aan een Automation-account, wordt het volgende scherm weer gegeven. Selecteer een Automation-account en selecteer **OK** wanneer u klaar bent.

    ![Geen werk ruimte](media/enable-from-portal/no-workspace.png)

7. Maak de selectie van een virtuele machine die u niet wilt inschakelen onbeschikbaar. De selectie van Vm's die niet kunnen worden ingeschakeld, is al opheffen.

8. Selecteer **inschakelen** om de functie in te scha kelen. Nadat u Updatebeheer hebt ingeschakeld, kan het ongeveer 15 minuten duren voordat u de update-evaluatie kunt bekijken.

## <a name="next-steps"></a>Volgende stappen

* Zie [updates en patches voor uw virtuele machines beheren](manage-updates-for-vm.md)voor meer informatie over het gebruik van updatebeheer voor vm's.
* Zie [Problemen met Updatebeheer oplossen](../troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
* Zie [Problemen met Windows-updateagent oplossen](../troubleshoot/update-agent-issues.md)voor informatie over het oplossen van problemen met de Windows Update-agent.
* Zie problemen met de [Linux-Update agent oplossen](../troubleshoot/update-agent-issues-linux.md)voor informatie over het oplossen van problemen met de Linux-Update Agent.