---
title: VM-extensie inschakelen vanuit Azure Portal
description: In dit artikel wordt beschreven hoe u extensies van virtuele machines implementeert op Azure Arc servers die worden uitgevoerd in hybride cloudomgevingen vanuit de Azure Portal.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: b5b4ff79d68ec9ff0cc61b9dbb7d3c5d7fe93598
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388275"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure VM-extensies inschakelen vanuit Azure Portal

In dit artikel wordt beschreven hoe u Azure VM-extensies, ondersteund door Azure Arc-servers, implementeert en verwijdert op een hybride Linux- of Windows-machine via de Azure Portal.

> [!NOTE]
> De Key Vault VM-extensie (preview) biedt geen ondersteuning voor implementatie vanuit de Azure Portal, alleen met behulp van de Azure CLI, de Azure PowerShell of met behulp van een Azure Resource Manager-sjabloon.

> [!NOTE]
> Azure Arc-servers bieden geen ondersteuning voor het implementeren en beheren van VM-extensies op virtuele Azure-machines. Zie het volgende artikel over VM-extensies voor [Azure-VM's.](../../virtual-machines/extensions/overview.md)

## <a name="enable-extensions-from-the-portal"></a>Extensies inschakelen vanuit de portal

VM-extensies kunnen worden toegepast op uw Arc voor de server beheerde machine via de Azure Portal.

1. Ga in uw browser naar de [Azure Portal](https://portal.azure.com).

2. Blader in de portal naar **Servers - Azure Arc** en selecteer uw hybride machine in de lijst.

3. Kies **Extensies** en selecteer **vervolgens Toevoegen.** Kies de extensie die u wilt gebruiken in de lijst met beschikbare extensies en volg de instructies in de wizard. In dit voorbeeld implementeren we de Log Analytics VM-extensie.

    ![VM-extensie voor geselecteerde machine selecteren](./media/manage-vm-extensions/add-vm-extensions.png)

    In het volgende voorbeeld ziet u de installatie van de Log Analytics VM-extensie van de Azure Portal:

    ![Log Analytics VM-extensie installeren](./media/manage-vm-extensions/mma-extension-config.png)

    Als u de installatie wilt voltooien, moet u de werkruimte-id en primaire sleutel verstrekken. Zie Werkruimte-id en -sleutel verkrijgen als u niet bekend bent met het vinden [van deze informatie.](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

4. Nadat u de vereiste gegevens heeft bevestigd, selecteert u **Maken.** Er wordt een samenvatting van de implementatie weergegeven en u kunt de status van de implementatie bekijken.

>[!NOTE]
>Hoewel meerdere extensies tegelijk kunnen worden gebatcheerd en verwerkt, worden ze serieel geïnstalleerd. Zodra de eerste installatie van de extensie is voltooid, wordt geprobeerd de volgende extensie te installeren.

## <a name="list-extensions-installed"></a>Lijst met geïnstalleerde extensies

U kunt een lijst met de VM-extensies op uw Arc-server op de Azure Portal. Voer de volgende stappen uit om ze te bekijken.

1. Ga in uw browser naar de [Azure Portal.](https://portal.azure.com)

2. Blader in de portal naar **Servers - Azure Arc** selecteer uw hybride machine in de lijst.

3. Kies **Extensies** en de lijst met geïnstalleerde extensies wordt geretourneerd.

    ![Lijst met VM-extensies die zijn geïmplementeerd op de geselecteerde machine](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>Extensie verwijderen

U kunt een of meer extensies van een Arc-server verwijderen uit de Azure Portal. Voer de volgende stappen uit om een extensie te verwijderen.

1. Ga in uw browser naar de [Azure Portal.](https://portal.azure.com)

2. Blader in de portal naar **Servers - Azure Arc** selecteer uw hybride machine in de lijst.

3. Kies **Extensies** en selecteer vervolgens een extensie in de lijst met geïnstalleerde extensies.

4. Selecteer **Verwijderen en wanneer** u wordt gevraagd om dit te controleren, selecteert u Ja **om** door te gaan.

## <a name="next-steps"></a>Volgende stappen

- U kunt VM-extensies implementeren, beheren en verwijderen met behulp van [de Azure CLI,](manage-vm-extensions-cli.md) [PowerShell](manage-vm-extensions-powershell.md) [of Azure Resource Manager sjablonen.](manage-vm-extensions-template.md)

- Informatie over probleemoplossing vindt u in de handleiding Problemen met [VM-extensies oplossen.](troubleshoot-vm-extensions.md)