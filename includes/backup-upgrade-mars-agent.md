---
title: De Azure Backup-Agent bijwerken
description: In deze informatie wordt uitgelegd waarom u de Azure Backup-agent moet upgraden en waar u de upgrade kunt downloaden.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78673190"
---
## <a name="upgrade-the-mars-agent"></a>De MARS-agent upgraden

De versies van de Microsoft Azure Recovery Services-agent (MARS) onder 2.0.9083.0 hebben een afhankelijkheid van de Azure Access Control-service. De MARS-agent wordt ook wel de Azure Backup-Agent genoemd.

In 2018 heeft micro soft [de Azure Access Control-service afgeschaft](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Vanaf 19 maart 2018 zullen alle versies van de MARS-agent onder 2.0.9083.0 back-upfouten ondervinden. Om back-upfouten te voor komen of op te lossen, [moet u de Mars-agent upgraden naar de nieuwste versie](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Volg de stappen in een [upgrade van de Microsoft Azure Recovery Services-agent (Mars)](../articles/backup/upgrade-mars-agent.md)om servers te identificeren waarvoor een Mars-agent upgrade is vereist.

De MARS-agent wordt gebruikt voor het maken van een back-up van bestanden en mappen en de systeem status gegevens naar Azure. System Center DPM en Azure Backup Server gebruiken de MARS-agent om een back-up te maken van gegevens in Azure.
