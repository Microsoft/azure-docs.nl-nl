---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86230972"
---
- Biedt geen ondersteuning voor Ultra disks.
- Kan niet worden ingeschakeld als Azure Disk Encryption (versleuteling van de gast-VM met behulp van BitLocker/VM-ontsleuteling) is ingeschakeld op uw Vm's/virtuele-machine schaal sets.
- Azure Disk Encryption kan niet worden ingeschakeld op schijven met versleuteling op de host.
- De versleuteling kan worden ingeschakeld voor een bestaande schaalset voor virtuele machines. Maar alleen nieuwe Vm's die zijn gemaakt na het inschakelen van de versleuteling, worden automatisch versleuteld.
- Bestaande Vm's moeten worden vrijgegeven en opnieuw worden toegewezen om te kunnen worden versleuteld.