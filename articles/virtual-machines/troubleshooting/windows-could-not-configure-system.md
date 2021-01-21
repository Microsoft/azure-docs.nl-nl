---
title: Windows-probleemoplossing kon de configuratie van het systeem niet voltooien
titlesuffix: Azure Virtual Machines
description: Dit artikel bevat stappen voor het oplossen van problemen waarbij het Sysprep-proces het opstarten van een virtuele machine van Azure voor komt.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 6cb3467fec99bd12810ed058a61de1be7b39cdd0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629586"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Windows-probleemoplossing kon de configuratie van het systeem niet voltooien

Dit artikel bevat stappen voor het oplossen van problemen waarbij het Sysprep-proces het opstarten van een virtuele Azure-machine (VM) voor komt.

## <a name="symptom"></a>Symptoom

Wanneer u [Diagnostische gegevens over opstarten](./boot-diagnostics.md) gebruikt om de scherm opname van de virtuele machine weer te geven, ziet u dat in de scherm opname een Windows-fout tijdens de installatie wordt weer gegeven tijdens het starten van de service. Met de fout wordt het volgende bericht weer gegeven:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![In afbeelding 1 wordt een Windows-fout bij de installatie met het volgende bericht weer gegeven: "het configureren van het systeem door Windows is niet voltooid. Start de computer opnieuw op om de configuratie te hervatten. De services worden gestart](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt wanneer het [Sysprep-proces](/windows-hardware/manufacture/desktop/sysprep-process-overview)niet kan worden voltooid door het besturings systeem. Deze fout treedt op wanneer u een gegeneraliseerde VM het eerst probeert op te starten. Als u dit probleem ondervindt, moet u de algemene installatie kopie opnieuw maken, omdat de installatie kopie een niet-Implementeer bare status heeft en niet kan worden hersteld.

## <a name="solution"></a>Oplossing

> [!TIP]
> Als u een recente back-up van de virtuele machine hebt, kunt u proberen [de virtuele machine terug te zetten vanaf de back-up](../../backup/backup-azure-arm-restore-vms.md) om het opstart probleem op te lossen.

U kunt dit probleem oplossen door de [Azure-richt lijnen voor het voorbereiden/vastleggen van een installatie kopie](../windows/upload-generalized-managed.md) te volgen en een nieuwe gegeneraliseerde installatie kopie voor te bereiden.