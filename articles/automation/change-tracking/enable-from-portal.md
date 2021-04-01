---
title: Azure Automation Wijzigingen bijhouden en inventaris van de Azure Portal inschakelen
description: In dit artikel leest u hoe u de functie Wijzigingen bijhouden en inventaris kunt inschakelen vanuit de Azure Portal.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 63041e0b1b6e12c765299b12f28aa3637b6a6ccb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99052784"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>Wijzigingen bijhouden en inventaris inschakelen vanuit Azure Portal

In dit artikel wordt beschreven hoe u [Wijzigingen bijhouden en inventaris](overview.md) kunt inschakelen voor een of meer virtuele Azure-machines in de Azure Portal. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie.

Het aantal resource groepen dat u kunt gebruiken voor het beheren van uw Vm's, wordt beperkt door de [implementatie limieten van Resource Manager](../../azure-resource-manager/templates/deploy-to-resource-group.md). Implementaties van Resource Manager zijn beperkt tot vijf resource groepen per implementatie. Twee van deze resource groepen zijn gereserveerd voor het configureren van de Log Analytics-werk ruimte, het Automation-account en gerelateerde resources. Dit houdt in dat u drie resource groepen kunt selecteren voor beheer door Wijzigingen bijhouden en inventaris. Deze limiet is alleen van toepassing op gelijktijdige installatie, niet het aantal resource groepen dat kan worden beheerd door een automatiserings functie.

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [regio toewijzing voor Automation-account en log Analytics-werk ruimte](../how-to/region-mappings.md)voor een lijst met de ondersteunde toewijzings paren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-security-overview.md) voor het beheren van computers.
* Een [virtuele machine](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

1. Ga in het Azure Portal naar **virtuele machines**.

2. Gebruik de selectie vakjes om de Vm's te kiezen die u wilt toevoegen aan Wijzigingen bijhouden en inventarisatie. U kunt machines toevoegen voor Maxi maal drie verschillende resource groepen tegelijk. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account.

    ![Lijst met Vm's](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Gebruik de filter besturings elementen om Vm's uit verschillende abonnementen, locaties en resource groepen te selecteren. U kunt op het selectie vakje aan de bovenkant klikken om alle virtuele machines in een lijst te selecteren.

3. Selecteer **Wijzigingen bijhouden** of **inventaris** onder **configuratie beheer**.

4. De lijst met virtuele machines wordt gefilterd om alleen de virtuele machines weer te geven die zich in hetzelfde abonnement en dezelfde locatie bevinden. Als uw virtuele machines zich in meer dan drie resource groepen bevinden, worden de eerste drie resource groepen geselecteerd.

5. Een bestaande Log Analytics-werk ruimte en een Automation-account zijn standaard geselecteerd. Als u een andere Log Analytics-werk ruimte en een Automation-account wilt gebruiken, klikt u op **aangepast** om ze te selecteren op de pagina aangepaste configuratie. Wanneer u een werk ruimte voor Log Analytics kiest, wordt er een controle uitgevoerd om te bepalen of deze is gekoppeld aan een Automation-account. Als een gekoppeld Automation-account wordt gevonden, wordt het volgende scherm weer gegeven. Klik op **OK** wanneer u klaar bent.

    ![Werk ruimte en account selecteren](media/enable-from-portal/select-workspace-and-account.png)

6. Als de geselecteerde werk ruimte niet is gekoppeld aan een Automation-account, wordt het volgende scherm weer gegeven. Selecteer een Automation-account en klik op **OK** wanneer u klaar bent.

    ![Geen werk ruimte](media/enable-from-portal/no-workspace.png)

7. Schakel het selectie vakje naast een wille keurige virtuele machine die u niet wilt inschakelen uit. De selectie van Vm's die niet kunnen worden ingeschakeld, is al opheffen.

8. Klik op **inschakelen** om de functie in te scha kelen die u hebt geselecteerd. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden beheren](manage-change-tracking.md) en [inventaris beheren](manage-inventory-vms.md)voor meer informatie over het werken met de functie.
* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](../troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met de functie.