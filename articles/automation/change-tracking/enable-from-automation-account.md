---
title: Azure Automation Wijzigingen bijhouden en inventaris inschakelen vanuit Automation-account
description: In dit artikel leest u hoe u Wijzigingen bijhouden en inventaris kunt inschakelen vanuit een Automation-account.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 64eace72b6ea203d4052c39404bcbd7ce4c4bfa0
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055158"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Wijzigingen bijhouden en inventaris inschakelen vanuit een Automation-account

In dit artikel wordt beschreven hoe u uw Automation-account kunt gebruiken om [Wijzigingen bijhouden en inventaris](overview.md) voor vm's in uw omgeving in te scha kelen. Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande VM inschakelen met behulp van Wijzigingen bijhouden en inventarisatie.

> [!NOTE]
> Bij het inschakelen van Wijzigingen bijhouden en inventaris worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](../how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-security-overview.md) voor het beheren van computers.
* Een [virtuele machine](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure op https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris inschakelen

1. Navigeer naar uw Automation-account en selecteer **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Kies de Log Analytics werk ruimte en het Automation-account en klik op **inschakelen** om wijzigingen bijhouden en inventarisatie in te scha kelen. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

    ![Wijzigingen bijhouden en Inventaris inschakelen](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Virtuele Azure-machines inschakelen

1. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Klik op **+ virtuele machines van Azure toevoegen** en selecteer een of meer virtuele machines in de lijst. Virtuele machines die niet kunnen worden ingeschakeld, worden grijs weer gegeven en kunnen niet worden geselecteerd. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account. 

3. Klik op **inschakelen** om de geselecteerde vm's toe te voegen aan de computer groep opgeslagen zoek opdracht voor de functie. Zie [beperken wijzigingen bijhouden en inventarisatie bereik](manage-scope-configurations.md)voor meer informatie.

      [![Virtuele Azure-machines inschakelen](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Vm's buiten Azure inschakelen

Machines die niet in azure zijn, moeten hand matig worden toegevoegd. Het is raadzaam om de Log Analytics-agent voor Windows of Linux te installeren door eerst uw computer te verbinden met [servers met Azure-Arc](../../azure-arc/servers/overview.md)en vervolgens Azure Policy te gebruiken om de implementatie van [log Analytics agent toe te wijzen aan het ingebouwde beleid voor *Linux* of *Windows* Azure Arc-machines](../../governance/policy/samples/built-in-policies.md#monitoring) . Als u ook van plan bent om de machines met Azure Monitor voor VM's te controleren, gebruikt u in plaats daarvan het [Azure monitor voor VM's](../../governance/policy/samples/built-in-initiatives.md#monitoring) -initiatief.

1. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Klik op **niet-Azure-machine toevoegen**. Met deze actie wordt een nieuw browser venster geopend met [instructies voor het installeren en configureren van de log Analytics-agent voor Windows](../../azure-monitor/platform/log-analytics-agent.md) , zodat de computer kan beginnen met het rapporteren van wijzigingen bijhouden-en inventarisatie bewerkingen. Als u een machine inschakelt die momenteel wordt beheerd door Operations Manager, is een nieuwe agent niet vereist en wordt de werkruimte informatie ingevoerd in de bestaande agent.

## <a name="enable-machines-in-the-workspace"></a>Computers in de werk ruimte inschakelen

Hand matig geïnstalleerde computers of machines die al aan uw werk ruimte rapporteren, moeten worden toegevoegd aan Azure Automation voor Wijzigingen bijhouden en inventaris moeten worden ingeschakeld.

1. Selecteer in uw Automation-account **inventarisatie** of **Wijzigingen bijhouden** onder **configuratie beheer**.

2. Selecteer **machines beheren**. De optie **computers beheren** kan grijs worden weer gegeven als u eerder de optie **inschakelen op alle beschik bare en toekomstige computers** hebt gekozen

    ![Opgeslagen Zoek opdrachten](media/enable-from-automation-account/manage-machines.png)

3. Als u Wijzigingen bijhouden en inventaris voor alle beschik bare computers wilt inschakelen, selecteert u **inschakelen op alle beschik bare computers** op de pagina **machines beheren** . Met deze actie wordt het besturings element uitgeschakeld om computers afzonderlijk toe te voegen en worden alle machines die aan de werk ruimte rapporteren worden toegevoegd aan de computer groep opgeslagen Zoek query. Wanneer dit selectie vakje is ingeschakeld, wordt de optie **machines beheren** door deze actie uitgeschakeld.

4. Als u de functie wilt inschakelen voor alle beschik bare machines en toekomstige computers, selecteert u **inschakelen op alle beschik bare en toekomstige computers**. Met deze optie wordt de opgeslagen Zoek-en Scope configuratie verwijderd uit de werk ruimte en wordt de functie geopend voor alle Azure-en niet-Azure-computers die rapporteren aan de werk ruimte. Wanneer dit selectie vakje is ingeschakeld, wordt de optie voor het **beheren van computers** permanent uitgeschakeld omdat er geen scope configuratie meer is.

    > [!NOTE]
    > Omdat met deze optie de opgeslagen Zoek-en Scope configuratie in Log Analytics wordt verwijderd, is het belang rijk dat u verwijderings vergrendelingen verwijdert uit de werk ruimte Log Analytics voordat u deze optie selecteert. Als dat niet het geval is, kan de optie de configuraties niet verwijderen en moet u ze hand matig verwijderen.

5. Als dat nodig is, kunt u de scope configuratie weer toevoegen door de eerste opgeslagen zoek opdracht opnieuw toe te voegen. Zie [beperken wijzigingen bijhouden en inventarisatie bereik](manage-scope-configurations.md)voor meer informatie.

6. Als u de functie wilt inschakelen voor een of meer computers, selecteert u **inschakelen op geselecteerde machines** en klikt u op **toevoegen** naast elke machine om in te scha kelen voor de functie. Met deze taak worden de geselecteerde computer namen toegevoegd aan de computer groep opgeslagen Zoek query voor de functie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wijzigingen bijhouden beheren](manage-change-tracking.md) en [inventaris beheren](manage-inventory-vms.md)voor meer informatie over het werken met de functie.

* Zie [problemen met wijzigingen bijhouden en voorraad problemen oplossen](../troubleshoot/change-tracking.md)voor informatie over het oplossen van algemene problemen met de functie.
