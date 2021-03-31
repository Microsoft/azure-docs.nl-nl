---
title: Ondersteuning voor het gebruik van Azure Site Recovery met Azure Backup
description: Hierin wordt een overzicht gegeven van de manier waarop Azure Site Recovery en Azure Backup samen kunnen worden gebruikt.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: c334eee34eb878135d3d81ab15d03618c6604846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135176"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Ondersteuning voor het gebruik van Site Recovery met Azure Backup

In dit artikel wordt een overzicht gegeven van de ondersteuning voor het gebruik van de [site Recovery-service](site-recovery-overview.md) samen met de [Azure backup-service](../backup/backup-overview.md).

**Actie** | **Ondersteuning voor Site Recovery** | **Details**
--- | --- | ---
**Services samen implementeren** | Ondersteund | Services zijn interoperabel en kunnen samen worden geconfigureerd.
**Back-up/herstellen van bestanden** | Ondersteund | Als back-up en replicatie zijn ingeschakeld voor een virtuele machine en er back-ups worden gemaakt, is er geen probleem bij het herstellen van bestanden op de bron-en-groep Vm's. Replicatie wordt voortgezet zoals gebruikelijk zonder wijzigingen in de replicatie status.
**Schijf herstellen** | Geen huidige ondersteuning | Als u een back-up van de schijf herstelt, moet u de replicatie voor de VM opnieuw uitschakelen en opnieuw inschakelen.
**VM herstellen** | Geen huidige ondersteuning | Als u een virtuele machine of een groep virtuele machines herstelt, moet u de replicatie voor de virtuele machine uitschakelen en opnieuw inschakelen.  


