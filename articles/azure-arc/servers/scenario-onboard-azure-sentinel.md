---
title: Onboarding van Azure Arc ingeschakelde server naar Azure-Sentinel
description: Meer informatie over het toevoegen van uw Azure Arc-servers aan Azure Sentinel en het proactief controleren van de beveiligings status.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2364ba72ac5b10ec4e1f433cc6d591c3ca389ecd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584732"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Onboarding van Azure Arc ingeschakelde servers naar Azure-Sentinel

Dit artikel is bedoeld om u te helpen bij de voor bereiding van uw Azure Arc-server naar [Azure Sentinel](../../sentinel/overview.md) en om te beginnen met het verzamelen van beveiligings gebeurtenissen. Azure Sentinel biedt één oplossing voor waarschuwings detectie, zicht baarheid van bedreigingen, proactieve jacht en bedreigings reacties binnen de onderneming.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u aan de volgende vereisten voldoet:

- Een [Log Analytics-werkruimte](../../azure-monitor/logs/data-platform-logs.md). Zie [De implementatie van uw Azure Monitor-logboeken ontwerpen](../../azure-monitor/logs/design-logs-deployment.md)voor meer informatie over Log Analytics-werkruimten.

- Azure-Sentinel [ingeschakeld in uw abonnement](../../sentinel/quickstart-onboard.md).

- U bent machine of server verbonden met servers voor Azure-Arc.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Onboarding van Azure Arc ingeschakelde servers naar Azure-Sentinel

Azure Sentinel wordt geleverd met een aantal connectors voor micro soft-oplossingen, die beschikbaar zijn in de doos en waarmee realtime-integratie kan worden geboden. Voor fysieke en virtuele machines kunt u de Log Analytics-agent installeren waarmee de logboeken worden verzameld en doorgestuurd naar Azure Sentinel. Arc ingeschakelde servers bieden ondersteuning voor de implementatie van de Log Analytics-agent met behulp van de volgende methoden:

- Het Framework van VM-extensies gebruiken.

    Met deze functie in azure Arc enabled servers kunt u de VM-extensie van Log Analytics agent implementeren op een niet-Azure Windows-en/of Linux-server. VM-extensies kunnen worden beheerd met behulp van de volgende methoden op uw hybride computers of servers die worden beheerd door servers met Arc-functionaliteit:

    - De [Azure Portal](manage-vm-extensions-portal.md)
    - De [Azure cli](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - Azure [Resource Manager-sjablonen](manage-vm-extensions-template.md)

- Azure Policy gebruiken.

    Met deze methode gebruikt u de Azure Policy [log Analytics agent implementeren voor Linux of Windows Azure Arc machines](../../governance/policy/samples/built-in-policies.md#monitoring) ingebouwd beleid om te controleren of de op de Arc ingeschakelde server de log Analytics-agent is geïnstalleerd. Als de agent niet is geïnstalleerd, wordt deze automatisch geïmplementeerd met een herstel taak. Als u van plan bent om de machines met Azure Monitor voor VM's te controleren, gebruikt u in plaats daarvan het Azure Monitor voor VM's-initiatief [inschakelen](../../governance/policy/samples/built-in-initiatives.md#monitoring) en configureert u de log Analytics agent.

U kunt het beste de Log Analytics-agent voor Windows of Linux installeren met behulp van Azure Policy.

Nadat uw Arc-servers zijn verbonden, worden uw gegevens streamen naar Azure Sentinel en kunt u aan de slag gaan met. U kunt de logboeken in de [ingebouwde werkmappen](../../sentinel/quickstart-get-visibility.md) weergeven en beginnen met het bouwen van query's in Log Analytics om [de gegevens te onderzoeken](../../sentinel/tutorial-investigate-cases.md).

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md).