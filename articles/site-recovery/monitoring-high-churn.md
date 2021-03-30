---
title: Verloop patronen bewaken op virtuele machines
description: Meer informatie over het bewaken van verloop patronen op Virtual Machines die zijn beveiligd met Azure Site Recovery
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359863"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>Verloop patronen bewaken op virtuele machines

In dit artikel vindt u een overzicht van de verschillende hulpprogram ma's die kunnen worden gebruikt voor het bewaken van verloop patronen op een virtuele machine. Door gebruik te maken van de juiste hulpprogram ma's is het eenvoudig om precies te achterhalen welke toepassing een hoog verloop veroorzaakt en vervolgens verdere acties voor die toepassing kunnen worden uitgevoerd.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>Voor virtuele machines van Azure (Windows of Linux)

Als uw machine wordt gehost in Azure en u een beheerde of onbeheerde schijf gebruikt voor opslag, kunt u de prestaties eenvoudig bijhouden door metrische schijf gegevens bij te houden. Zo kunt u nauw keurig controleren en de juiste schijf selectie aanpassen aan het gebruiks patroon van uw toepassing. U kunt dit ook gebruiken voor het maken van waarschuwingen, diagnoses en het bouwen van automatisering. [Meer informatie](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

Wanneer u uw computers hebt beveiligd met Azure Site Recovery, kunt u uw computers controleren met behulp van Azure Monitor-logboeken en Log Analytics. [Meer informatie](./monitor-log-analytics.md).

Er zijn ook een aantal specifieke hulpprogram ma's voor het besturings systeem die u kunt gebruiken.

## <a name="for-windows-machines"></a>Voor Windows-machines

Als u een machine hebt, deze on-premises is of niet, waarop Windows-besturings systeem wordt uitgevoerd, zijn er nog een aantal hulpprogram ma's beschikbaar.

Naast het controleren van het schijf gebruik in taak beheer, kunt u altijd verwijzen naar **Broncontrole** en de **prestatie meter**. Deze hulpprogram ma's zijn al aanwezig op Windows-computers.

### <a name="resource-monitor"></a>Broncontrole

**Broncontrole** geeft informatie weer over het gebruik van hardware-en software bronnen in realtime. Volg de onderstaande stappen om Broncontrole uit te voeren op een Windows-computer:

1. Druk op Windows + R en typ _resmon_.
1. Zodra de resmon, dat wil zeggen, Broncontrole, wordt in venster de schakel optie naar het tabblad schijf geopend. Het biedt de volgende weer gave-

    ![Tabblad Broncontrole schijf](./media/monitoring-high-churn/resmon-disk-tab.png)

1. Dit tabblad moet gedurende enige tijd continu worden gecontroleerd om een duidelijke afbeelding te krijgen. In het bovenstaande voor beeld zien we dat _wmiprv.exe_ een hoge mate van verloop krijgt.

Zodra u hebt vastgesteld welke toepassingen een hoog verloop op uw computer veroorzaken, kunt u de nodige maat regelen nemen om het verloop te behalen dat aan deze toepassingen is gerelateerd.

### <a name="performance-monitor"></a>Prestatiemeter

**Prestatie meter** bewaakt verschillende activiteiten op een computer, zoals CPU of geheugen gebruik. Als u prestatie meter wilt uitvoeren op een Windows-computer, volgt u de onderstaande stappen:

1. Druk op Windows + R en typ _perfmon_.
1. Zodra de perfmon, dat wil zeggen, de prestatie meter, wordt het venster weer gegeven:

    ![Stap 1-4 prestatie meter](./media/monitoring-high-churn/perfmon-step1.png)

1. Vouw de map **controle hulpprogramma's** aan de rechter kant uit en klik op prestatie meter. Hiermee opent u de onderstaande weer gave waarin u real-time informatie krijgt over de huidige prestaties-

    ![Step2 prestatie meter](./media/monitoring-high-churn/perfmon-step1.png)

1. Deze grafiek bewaakt momenteel slechts één monitor, dat wil zeggen, de '% processor tijd ', zoals wordt aangegeven door de tabel rechts onder de grafiek. U kunt meer items voor bewaking toevoegen door op de **+** boven aan het hulp programma te klikken.
1. Hieronder ziet u een visuele element van hoe prestatie meter eruitziet wanneer er meer tellers aan worden toegevoegd.

    ![Stap 3 prestatie meter](./media/monitoring-high-churn/perfmon-step3.png)

Meer informatie over prestatie meter [vindt u hier](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data).

## <a name="for-linux-machines"></a>Voor Linux-machines

Als u een machine hebt, deze on-premises is of niet, waarop Linux-besturings systeem wordt uitgevoerd, zijn er nog een aantal hulpprogram ma's beschikbaar om de verloop patronen te bewaken.

### <a name="iotop"></a>Iotop

Een van de meest gebruikte hulpprogram ma's is _iotop_. Het is een hulp programma voor het weer geven van real-time schijf activiteit. Het kan een lijst weer geven van de processen die I/O uitvoeren, samen met de schijf bandbreedte die ze gebruiken.

Open de opdracht prompt en voer de opdracht uit `iotop` .

### <a name="iostat"></a>Iostat gebruiken

Iostat gebruiken is een eenvoudig hulp programma waarmee statistieken over de systeem invoer en uitvoer apparaten worden verzameld en weer gegeven. Dit hulp programma wordt vaak gebruikt voor het traceren van prestatie problemen met opslag apparaten, waaronder apparaten, lokale schijven, externe schijven.

Open de opdracht prompt en voer de opdracht uit `iostat` .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bewaken met [Azure monitor](monitor-log-analytics.md).