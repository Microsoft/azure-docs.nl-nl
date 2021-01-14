---
title: Ondersteuning voor 32-bits besturings systemen in azure virtual machines | Microsoft Docs
description: Informatie over besturings systemen die worden ondersteund op virtuele machines van Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210185"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Ondersteuning voor 32-bits besturingssystemen in virtuele Azure-machines

Microsoft Azure kunnen gebruikers nu hun 32-bits Windows-besturings systemen overzetten naar Azure. Alleen gespecialiseerde Vhd's worden ondersteund en gegeneraliseerde installatie kopieën werken niet in Azure. Omdat sommige van deze besturings systemen al hun einde van de levens cyclus voor ondersteuning hebben bereikt, biedt micro soft mogelijk geen aanvullende ondersteuning voor hen. Er wordt ook geen ondersteuning geboden voor op Linux of Berkeley (Software Distribution) gebaseerde besturings systemen die worden uitgevoerd op een Microsoft Azure virtuele machine (VM).

> [!NOTE]
> Het Azure-platform heeft een beperking van de geheugen ruimte die is opgelegd op Vm's met 32-bits besturings systemen, waarbij slechts 1 GB aan geheugen beschikbaar kan worden gemaakt voor de VM (*met name op client-sku's zoals Win7 of Win10*), en de rest van het geheugen voor de virtuele machine wordt weer gegeven als gereserveerd in de gast-VM. Dit is een bekend probleem en we hebben momenteel geen ETA voor een oplossing. We raden u aan om over te stappen op 64-bits besturingssysteem versies.
> 

## <a name="more-information"></a>Meer informatie

Voor meer informatie over besturings systemen die worden ondersteund op virtuele machines van Azure, gaat u naar de volgende micro soft Knowledge Base-artikelen:

* [Ondersteuning van Microsoft-serversoftware voor virtuele Microsoft Azure-machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [Ondersteuning voor Linux- en opensource-technologie in Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>Referenties

* [Meer informatie over gratis uitgebreide beveiligings updates voor Windows Server 2008/R2 in azure](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [Meer informatie over ondersteuning voor Windows Server 2008 SP2 32-bits gespecialiseerde installatie kopieën in azure](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [Meer informatie over ondersteuning voor de migratie van installatie kopieën van Windows Server 2008 naar Azure met behulp van Azure Site Recovery](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [Meer informatie over ondersteunde besturings systemen voor Azure-extensies](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [Meer informatie over het uitvoeren van Windows Server 2003 op Microsoft Azure](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>Volgende stappen

Als u op elk gewenst moment meer hulp nodig hebt, neemt u contact op met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/).

U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
