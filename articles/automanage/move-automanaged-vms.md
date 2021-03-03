---
title: Een Azure automanage-virtuele machine verplaatsen naar verschillende regio's
description: Meer informatie over het verplaatsen van een automatische beheerde virtuele machine in verschillende regio's
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 99371b8618756c196b75858288c5c4785272a7e8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650462"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Een virtuele machine voor automatisch beheren van Azure verplaatsen naar een andere regio
In dit artikel wordt beschreven hoe u automatisch beheer inschakelt op een virtuele machine (VM) wanneer u deze naar een andere regio verplaatst. Mogelijk wilt u uw virtuele machines om een aantal redenen verplaatsen naar een andere regio. Om bijvoorbeeld te profiteren van een nieuwe Azure-regio, om te voldoen aan de interne beleids-en beheer vereisten, of als reactie op vereisten voor capaciteits planning. De virtuele machines die u verplaatst, worden mogelijk momenteel niet meer beheerd en u wilt dat ze na het verplaatsen na uw verhuizing niet meer kunnen worden beheerd.

## <a name="prerequisites"></a>Vereisten
* Zorg ervoor dat de doel regio wordt [ondersteund door automanage](./automanage-virtual-machines.md#prerequisites).
* Zorg ervoor dat uw Log Analytics werkruimte regio, Automation-account regio en uw doel regio alle regio's zijn die [hier](../automation/how-to/region-mappings.md)worden ondersteund door de regio-toewijzingen.

## <a name="prepare-your-automanaged-vms-for-moving"></a>Bereid uw door u beheerde Vm's voor om te verplaatsen
Schakel automanage uit op uw Vm's die niet worden beheerd. U kunt dit doen door uw Vm's te selecteren op de Blade automanage en te klikken op **automanagement uitschakelen** op de Blade automanage.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Verplaats uw mijn beheer bare Vm's en schakel automanage opnieuw in
Zie dit [artikel](../resource-mover/tutorial-move-region-virtual-machines.md)voor meer informatie over het verplaatsen van uw vm's.

Wanneer u uw Vm's in verschillende regio's hebt verplaatst, kunt u de functie voor het opnieuw beheren van de virtuele machines opnieuw inschakelen. Meer informatie vindt u [hier](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over Azure automanage](./automanage-virtual-machines.md)
* [Veelgestelde vragen over Azure automanage weer geven](./faq.md)