---
title: Een nabijheidsplaatsingsgroep maken met behulp van de portal
description: Meer informatie over het maken van een proximity-plaatsings groep met behulp van de Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 45d7977dda4870be9a087b3277dd1db828c91178
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871431"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Een nabijheidsplaatsingsgroep maken met behulp van de portal

Als u virtuele machines zo dicht mogelijk wilt ophalen, moet u deze implementeren in een [proximity-plaatsings groep](../co-location.md#proximity-placement-groups).

Een proximity-plaatsings groep is een logische groepering die wordt gebruikt om ervoor te zorgen dat Azure Compute-resources zich fysiek dicht bij elkaar bevinden. Proximity-plaatsings groepen zijn handig voor werk belastingen waarbij lage latentie een vereiste is.

> [!NOTE]
> Proximity-plaatsings groepen kunnen niet worden gebruikt met gereserveerde hosts.
>
> Als u beschikbaarheids zones samen met plaatsings groepen wilt gebruiken, moet u ervoor zorgen dat de virtuele machines in de plaatsings groep ook in dezelfde beschikbaarheids zone zijn opgenomen.
>

## <a name="create-the-proximity-placement-group"></a>De plaatsings groep voor proximity maken

1. Typ **proximity placement Group** in de zoek opdracht.
1. Selecteer onder **Services** in de zoek resultaten **proximity placement groups**.
1. Selecteer op de pagina **proximity placement groups** de optie **toevoegen**.
1. Controleer op het tabblad **basis beginselen** onder **Project Details** of het juiste abonnement is geselecteerd.
1. Selecteer in **resource groep** de optie **nieuwe maken** om een nieuwe groep te maken of selecteer een lege resource groep die al bestaat uit de vervolg keuzelijst. 
1. Selecteer in **regio** de locatie waar u de Proximity-plaatsings groep wilt maken.
1. Typ in **proximity-naam voor plaatsings groep** een naam en selecteer vervolgens **controleren + maken**.
1. Nadat de validatie is geslaagd, selecteert u **maken** om de Proximity-plaatsings groep te maken.

    ![Scherm afbeelding van het maken van een proximity-plaatsings groep](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Een virtuele machine maken

1. Ga tijdens het maken van een virtuele machine in de portal naar het tabblad **Geavanceerd** . 
1. Selecteer in de selectie voor de **plaatsings groep** de juiste plaatsings groep. 

    ![Scherm afbeelding van de sectie locatie van proximity-groep bij het maken van een nieuwe virtuele machine in de portal](./media/ppg/vm-ppg.png)

1. Wanneer u klaar bent met het maken van alle andere vereiste selecties, selecteert u **controleren + maken**.
1. Nadat de validatie is door gegeven, selecteert u **maken** om de virtuele machine in de plaatsings groep te implementeren.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Vm's in een beschikbaarheidsset toevoegen aan een proximity-plaatsings groep

Als de virtuele machine deel uitmaakt van de Beschikbaarheidsset, moet u de beschikbaarheidsset toevoegen aan de plaatsings groep voordat u de virtuele machines toevoegt.

1. In de [Portal](https://portal.azure.com) zoekt u naar *beschikbaarheids sets* en selecteert u de beschikbaarheidsset in de resultaten.
1. Stop\deallocate elke virtuele machine in de beschikbaarheidsset door de virtuele machine te selecteren, vervolgens op de pagina voor de virtuele machine **stoppen** te selecteren en vervolgens **OK** te selecteren om de virtuele machine te stoppen.
1. Controleer op de pagina voor uw beschikbaarheidsset of alle virtuele machines de **status** **gestopt hebben (toewijzing opgeheven)**.
1. Selecteer **Configuratie** in het menu aan de linkerkant.
1. Selecteer in de vervolg keuzelijst **proximity-plaatsings** groep een plaatsings groep en selecteer vervolgens **Opslaan**.
1. Selecteer **overzicht** in het menu links om de lijst met vm's opnieuw weer te geven. 
1. Selecteer elke virtuele machine in de beschikbaarheidsset en selecteer vervolgens **Start** op de pagina voor elke virtuele machine. 


## <a name="add-existing-vm-to-placement-group"></a>Bestaande VM toevoegen aan plaatsings groep 


1. Selecteer op de pagina voor de virtuele machine **stoppen**.
1. Zodra de status van de virtuele machine is **gestopt (toewijzing** ongedaan gemaakt), selecteert u **configuratie** in het menu links.
1. Selecteer in de vervolg keuzelijst **proximity-plaatsings** groep een plaatsings groep en selecteer vervolgens **Opslaan**.
1. Selecteer **overzicht** in het linkermenu en selecteer vervolgens **Start** om de virtuele machine opnieuw op te starten.

 

## <a name="next-steps"></a>Volgende stappen

U kunt ook de [Azure PowerShell](proximity-placement-groups.md) gebruiken om proximity-plaatsings groepen te maken.