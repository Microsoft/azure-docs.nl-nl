---
title: Herstel na noodgevallen voor Azure VM instellen naar een secundaire regio met Azure Site Recovery
description: Stel snel herstel na noodgevallen in naar een andere Azure-regio voor een Azure VM met behulp van de Azure Site Recovery-service.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135698"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Quickstart: Herstel na noodgeval instellen naar een secundaire Azure-regio voor een Azure-VM

De service [Azure Site Recovery](site-recovery-overview.md) draagt bij aan uw strategie voor zakelijke continuïteit en noodherstel (BCDR) door uw zakelijke apps online te houden tijdens geplande en ongeplande uitval. Site Recovery beheert en orkestreert noodherstel van on-premises machines en virtuele Azure-machines (VM), met inbegrip van replicatie, failover en herstel.

Deze quickstart beschrijft hoe u herstel na noodgevallen kunt instellen voor een Azure VM door het te repliceren naar een secundaire Azure-regio. In het algemeen worden de standaardinstellingen gebruikt voor replicatie.

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement en een VM nodig om deze zelfstudie te voltooien.

- Als u geen Azure-account met een actief abonnement hebt, kunt u [gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Een virtuele machine met minimaal 1 GB RAM is aanbevolen. [Lees meer](../virtual-machines/windows/quick-create-portal.md) over de manier waarop u een VM maakt.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Replicatie inschakelen voor de Azure-VM

Met de volgende stappen schakelt u VM-replicatie naar een secundaire locatie in.

1. Selecteer in het menu **Start** > **Virtuele machines** in het Azure-portal een VM om te repliceren.
1. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
1. Selecteer de doelregio in **Basisbeginselen** > **Doelregio**.
1. Selecteer **Controleren + Replicatie starten** om de replicatie-instellingen te bekijken. Als u de standaardinstellingen wilt wijzigen, selecteert u **Geavanceerde instellingen**.
1. Als u de taak wilt starten om VM-replicatie in te schakelen, selecteert u **Replicatie starten**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Schakel replicatie in.":::

## <a name="verify-settings"></a>Instellingen controleren

Nadat de replicatietaak is voltooid, kunt u de replicatiestatus controleren, de replicatie-instellingen wijzigen en de implementatie testen.

1. Selecteer **Virtuele machines** in het menu van het Azure-portal, en selecteer de VM die u heeft gerepliceerd.
1. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
1. Als u de replicatiedetails wilt weer geven in het **Overzicht**, selecteer dan **Essentials**. Er wordt meer informatie weergegeven bij de toewijzing **Integriteit en status**, **Failover-gereedheid** en **Infrastructuurweergave**.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Replicatiestatus.":::

## <a name="clean-up-resources"></a>Resources opschonen

Om de replicatie van de VM in de primaire regio stop te zetten, moet u replicatie uitschakelen:

- De bronreplicatie-instellingen worden automatisch opgeschoond.
- De Site Recovery-extensie die tijdens de replicatie op de virtuele machine is geïnstalleerd, wordt niet verwijderd.
- Site Recovery-facturering voor de VM wordt stopgezet.

Als u replicatie wilt uitschakelen, voert u de volgende stappen uit:

1. Selecteer **Virtuele machines** in het menu van het Azure-portal, en selecteer de VM die u heeft gerepliceerd.
1. Selecteer in **Bewerkingen** de optie **Herstel na noodgeval**.
1. Selecteer in het **Overzicht** de optie **Replicatie uitschakelen**.
1. Om de Site Recovery-extensie te verwijderen, gaat u naar de **Instellingen** > **Extensies** van de VM.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Schakel replicatie uit.":::

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u één VM gerepliceerd naar een secundaire regio. Stelt vervolgens replicatie in voor meerdere Azure VM's.

> [!div class="nextstepaction"]
> [Stel herstel na noodgevallen in voor Azure-VM's](azure-to-azure-tutorial-enable-replication.md)
