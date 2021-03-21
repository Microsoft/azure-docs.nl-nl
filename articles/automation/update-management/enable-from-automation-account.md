---
title: Azure Automation Updatebeheer inschakelen vanuit het Automation-account
description: In dit artikel leest u hoe u Updatebeheer kunt inschakelen vanuit een Automation-account.
services: automation
ms.subservice: update-management
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089d5d70d8ad8060455e5c1bee45e0bee4a12fae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575846"
---
# <a name="enable-update-management-from-an-automation-account"></a>Updatebeheer inschakelen vanaf een Automation-account

In dit artikel wordt beschreven hoe u uw Automation-account kunt gebruiken om de [updatebeheer](overview.md) -functie in te scha kelen voor virtuele machines in uw omgeving, met inbegrip van computers of servers die zijn geregistreerd bij [servers met Azure Arc-functionaliteit](../../azure-arc/servers/overview.md). Als u Azure-Vm's op schaal wilt inschakelen, moet u een bestaande Azure VM inschakelen met behulp van Updatebeheer.

> [!NOTE]
> Bij het inschakelen van Updatebeheer worden slechts bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werkruimte aan een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](../how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-account](../automation-security-overview.md) voor het beheren van computers.
* Een [virtuele machine van Azure](../../virtual-machines/windows/quick-create-portal.md)of een VM of server die is geregistreerd bij servers waarop Arc is ingeschakeld. Voor niet-Azure-Vm's of-servers moet de [log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md) voor Windows of Linux geïnstalleerd zijn en worden gerapporteerd aan de werk ruimte die is gekoppeld aan het Automation-account updatebeheer is ingeschakeld in. Het is raadzaam om de Log Analytics-agent voor Windows of Linux te installeren door eerst uw computer te verbinden met [servers met Azure-Arc](../../azure-arc/servers/overview.md)en vervolgens Azure Policy te gebruiken om de implementatie van [log Analytics agent toe te wijzen aan het ingebouwde beleid voor *Linux* of *Windows* Azure Arc-machines](../../governance/policy/samples/built-in-policies.md#monitoring) . Als u van plan bent om de machines met Azure Monitor voor VM's te bewaken, moet u in plaats daarvan het Azure Monitor voor VM's-initiatief [inschakelen](../../governance/policy/samples/built-in-initiatives.md#monitoring) .


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Updatebeheer inschakelen

1. Selecteer in uw Automation-account **Update beheer** onder **Update beheer**.

2. Kies de Log Analytics werk ruimte en het Automation-account en selecteer **inschakelen** om updatebeheer in te scha kelen. Het volt ooien van de installatie duurt Maxi maal 15 minuten.

    ![Updatebeheer inschakelen](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Virtuele Azure-machines inschakelen

1. Selecteer in uw Automation-account **Update beheer** onder **Update beheer**.

2. Selecteer **+ virtuele machines van Azure toevoegen** en selecteer een of meer virtuele machines in de lijst. Virtuele machines die niet kunnen worden ingeschakeld, worden grijs weer gegeven en kunnen niet worden geselecteerd. Azure-Vm's kunnen in elke regio bestaan, ongeacht de locatie van uw Automation-account.

3. Selecteer **inschakelen** om de geselecteerde vm's toe te voegen aan de computer groep opgeslagen zoek opdracht voor de functie.

    ![Virtuele Azure-machines inschakelen](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Vm's buiten Azure inschakelen

Voor computers of servers die buiten Azure worden gehost, met inbegrip van de services die zijn geregistreerd bij servers met Azure-Arc, voert u de volgende stappen uit om deze in te scha kelen met Updatebeheer.  

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Selecteer **niet-Azure-machine toevoegen**. Met deze actie wordt een nieuw browser venster geopend met [instructies voor het installeren en configureren van de log Analytics-agent voor Windows](../../azure-monitor/agents/log-analytics-agent.md) , zodat de computer kan beginnen met het rapporteren van updatebeheer. Als u een machine inschakelt die momenteel wordt beheerd door Operations Manager, is een nieuwe agent niet vereist. De werkruimte gegevens worden toegevoegd aan de agent configuratie.

## <a name="enable-machines-in-the-workspace"></a>Computers in de werk ruimte inschakelen

Hand matig geïnstalleerde computers of machines die al aan uw werk ruimte rapporteren, moeten worden toegevoegd aan Azure Automation om Updatebeheer in te scha kelen.

1. Selecteer **Updatebeheer** onder **Updatebeheer** in uw Automation-account.

2. Selecteer **machines beheren**. De knop **machines beheren** kan grijs worden weer gegeven als u eerder de optie **inschakelen op alle beschik bare en toekomstige computers** hebt gekozen

    ![Opgeslagen Zoek opdrachten](media/enable-from-automation-account/managemachines.png)

3. Als u Updatebeheer wilt inschakelen voor alle beschik bare computers die rapporteren aan de werk ruimte, selecteert u **inschakelen op alle beschik bare computers** op de pagina machines beheren. Met deze actie wordt het besturings element uitgeschakeld om computers afzonderlijk toe te voegen en worden alle machines die aan de werk ruimte rapporteren worden toegevoegd aan de computer groep opgeslagen Zoek query `MicrosoftDefaultComputerGroup` . Wanneer dit selectie vakje is ingeschakeld, wordt de optie **machines beheren** door deze actie uitgeschakeld.

4. Als u de functie wilt inschakelen voor alle beschik bare machines en toekomstige computers, selecteert u **inschakelen op alle beschik bare en toekomstige computers**. Met deze optie wordt de opgeslagen Zoek-en Scope configuratie verwijderd uit de werk ruimte en kan de functie alle Azure-en niet-Azure-machines die momenteel of in de toekomst zijn opgenomen, rapporteren aan de werk ruimte. Wanneer dit selectie vakje is ingeschakeld, wordt de optie voor het **beheren van computers** permanent uitgeschakeld, omdat er geen scope configuratie beschikbaar is.

    > [!NOTE]
    > Omdat met deze optie de opgeslagen Zoek-en Scope configuratie in Log Analytics wordt verwijderd, is het belang rijk dat u verwijderings vergrendelingen verwijdert uit de werk ruimte Log Analytics voordat u deze optie selecteert. Als dat niet het geval is, kan de optie de configuraties niet verwijderen en moet u ze hand matig verwijderen.

5. Als dat nodig is, kunt u de scope configuratie weer toevoegen door de oorspronkelijke opgeslagen Zoek query opnieuw toe te voegen. Zie [limiet updatebeheer-implementatie bereik](scope-configuration.md)voor meer informatie.

6. Als u de functie wilt inschakelen voor een of meer computers, selecteert u **inschakelen op geselecteerde computers** en selecteert u naast elke machine **toevoegen** . Met deze taak worden de geselecteerde computer namen toegevoegd aan de computer groep opgeslagen Zoek query voor de functie.

## <a name="next-steps"></a>Volgende stappen

* Zie [updates en patches voor uw virtuele machines beheren](manage-updates-for-vm.md)voor meer informatie over het gebruik van updatebeheer voor vm's.

* Zie [vm's verwijderen uit updatebeheer](remove-vms.md)als u geen vm's of servers meer wilt beheren met updatebeheer.

* Zie [Problemen met Updatebeheer oplossen](../troubleshoot/update-management.md) voor meer informatie over het oplossen van algemene Updatebeheer-fouten.
