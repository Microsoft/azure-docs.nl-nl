---
title: Uitgebreide metrische gegevens voor virtuele Azure-machines toevoegen
description: Dit artikel helpt u bij het inschakelen en configureren van uitgebreide diagnostische metrische gegevens voor uw Azure-VM's.
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: da2e20e333fe499998fff72b175442650f5f1e28
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595315"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Uitgebreide metrische gegevens voor virtuele Azure-machines toevoegen

Cloudyn gebruikt metrische Azure-gegevens van uw Azure-VM's om gedetailleerde informatie over hun resources weer te geven. Metrische gegevens, ook wel prestatiemeteritems genoemd, worden door Cloudyn gebruikt voor het genereren van rapporten. Cloudyn verzamelt echter niet automatisch alle metrische gegevens van Azure uit gast-VM's. U moet het verzamelen van metrische gegevens eerst inschakelen. Dit artikel helpt u bij het inschakelen en configureren van aanvullende diagnostische metrische gegevens voor uw Azure-VM's.

Nadat u het verzamelen van metrische gegevens hebt ingeschakeld, kunt u het volgende doen:

- Vaststellen wanneer uw VM's de limieten voor geheugen, schijfopslag en CPU bereiken.
- Trends en afwijkingen van het gebruik detecteren.
- Uw kosten beheren door de grootte van VM's aan te passen op basis van het gebruik.
- Optimalisatieaanbevelingen van Cloudyn ontvangen voor aanpassen van grootte.

U kunt bijvoorbeeld het CPU-percentage en het percentage geheugengebruik van uw virtuele Azure-machines bewaken. De metrische gegevens van de Azure-VM komen overeen met _Percentage CPU_ en _\Geheugen\% Percentage toegewezen bytes in gebruik_.

> [!NOTE]
> Het verzamelen van uitgebreide metrische gegevens wordt alleen ondersteund met Azure-bewaking op gastniveau. Cloudyn is niet compatibel met de [Log Analytics agent](../../azure-monitor/agents/agents-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>Bepalen of uitgebreide metrische gegevens zijn ingeschakeld

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer onder **Virtuele machines** een VM en selecteer vervolgens onder **Controle** **Metrische gegevens**. Er wordt een lijst met beschikbare metrische gegevens weergegeven.
3. Selecteer enkele metrische gegevens en u ziet een grafiek met die gegevens.  
    ![Voorbeeld van metrische gegevens – CPU-percentage van host](./media/azure-vm-extended-metrics/metric01.png)

In het vorige voorbeeld is een beperkt aantal standaard metrische gegevens beschikbaar voor uw hosts, maar geen metrische gegevens van het geheugen. Metrische gegevens van het geheugen maken namelijk deel uit van uitgebreide metrische gegevens. In dit geval zijn uitgebreide metrische gegevens niet ingeschakeld voor de VM. U moet een aantal extra stappen uitvoeren om uitgebreide metrische gegevens in te schakelen. De volgende informatie helpt u om dit te doen.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Uitgebreide metrische gegevens inschakelen in de Azure-portal

Standaard metrische gegevens zijn metrische gegevens van de host. _Percentage CPU_ is hier een voorbeeld van. Er zijn ook metrische basisgegevens voor gast-VM's en deze worden ook uitgebreide metrische gegevens genoemd. Voorbeelden van uitgebreide metrische gegevens zijn _\Geheugen\% Percentage toegewezen bytes in gebruik_ en _\Geheugen\Beschikbare bytes_.

Het inschakelen van uitgebreide metrische gegevens is heel eenvoudig. U hoeft alleen voor elke VM bewaking op gastniveau in te schakelen. Wanneer u bewaking op gastniveau inschakelt, wordt de Azure Diagnostics-agent geïnstalleerd op de VM. Standaard wordt er een basisset met uitgebreide metrische gegevens toegevoegd. Het volgende proces is hetzelfde voor klassieke en normale VM's, en ook hetzelfde voor virtuele Windows-en Linux-machines.

Houd er rekening mee dat bewaking op gastniveau voor zowel Windows als Linux een opslagaccount vereist. Wanneer u bewaking op gastniveau inschakelt en u geen bestaand opslagaccount kiest, wordt er een voor u gemaakt.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Bewaking op gastniveau inschakelen op bestaande VM's

1. Geef in **Virtuele machines** de lijst met VM's weer en selecteer een VM.
2. Selecteer **Diagnostische instellingen** onder **Controle**.
3. Klik op de pagina Diagnostische instellingen op **Bewaking op gastniveau inschakelen**.  
    ![Bewaking op gastniveau inschakelen op de pagina Overzicht](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Na een paar minuten is de Azure Diagnostics-agent geïnstalleerd op de VM. Er wordt een basisset met metrische gegevens toegevoegd. Vernieuw de pagina. De toegevoegde prestatiemeteritems worden weergegeven op het tabblad Overzicht.
5. Selecteer **Metrische gegevens** onder Controle.
6. Selecteer **Gast (klassiek)** in de grafiek met metrische gegevens onder **Metrische naamruimte**.
7. In de lijst met metrische gegevens kunt u alle beschikbare prestatiemeteritems voor de gast-VM bekijken.  
    ![lijst met voorbeelden van uitgebreide metrische gegevens](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Bewaking op gastniveau inschakelen op nieuwe VM's

Wanneer u nieuwe VM's maakt, selecteert u **Aan** voor **Diagnostische gegevens gastbesturingssysteem** op het tabblad Beheer.

![Diagnostische gegevens gastbesturingssysteem op Aan zetten](./media/azure-vm-extended-metrics/new-enable-diag.png)

Zie [Meer informatie over het gebruik van de Azure Linux-agent](../../virtual-machines/extensions/agent-linux.md) en [Overzicht van de agent voor virtuele Azure-machines](../../virtual-machines/extensions/agent-windows.md) voor meer informatie over het inschakelen van uitgebreide metrische gegevens voor virtuele Azure-machines.

## <a name="resource-manager-credentials"></a>Referenties van Resource Manager

Nadat u uitgebreide metrische gegevens hebt ingeschakeld, moet u ervoor zorgen dat Cloudyn toegang heeft tot uw [referenties van Resource Manager](./activate-subs-accounts.md). Cloudyn heeft uw referentie nodig om prestatiegegevens voor uw VM's te verzamelen en weer te geven. Ze worden ook gebruikt om aanbevelingen voor kostenoptimalisatie te maken. Cloudyn moet ten minste drie dagen aan prestatiegegevens van een exemplaar hebben om te kunnen bepalen of het een kandidaat is voor downsizing (kleinere VM-grootte).

## <a name="enable-vm-metrics-with-a-script"></a>Metrische gegevens van VM inschakelen met een script

U kunt metrische gegevens van een VM inschakelen met behulp van Azure PowerShell-scripts. Wanneer u veel VM's hebt waarvoor u metrische gegevens wilt inschakelen, kunt u een script gebruiken om het proces te automatiseren. Voorbeelden van scripts vindt u op GitHub in het gedeelte [azure-enable-diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Metrische gegevens van Azure-prestaties bekijken

Als u in de Cloudyn-portal metrische gegevens wilt bekijken van de prestaties van uw Azure-exemplaren, gaat u naar **Assets** > **Compute** > **Instance Explorer**. Vouw in de lijst met VM-exemplaren een exemplaar uit en vouw vervolgens een resource uit om details weer te geven.

![voorbeeld van gegevens in Instance Explorer](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Volgende stappen

- Als u Azure Resource Manager API-toegang nog niet hebt ingeschakeld voor uw accounts, gaat u verder met [Azure-abonnementen en -accounts activeren](./activate-subs-accounts.md).