---
title: De Azure Backup-Agent bijwerken
description: In deze informatie wordt uitgelegd waarom u de Azure Backup-agent moet upgraden en waar u de upgrade kunt downloaden.
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294151"
---
## <a name="upgrade-the-mars-agent"></a>De MARS-agent upgraden

De versies van de Microsoft Azure Recovery Services-agent (MARS) onder 2.0.9083.0 hebben een afhankelijkheid van de Azure Access Control-service. De MARS-agent wordt ook wel de Azure Backup-Agent genoemd.

In 2018 heeft micro soft [de Azure Access Control-service afgeschaft](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). Vanaf 19 maart 2018 zullen alle versies van de MARS-agent onder 2.0.9083.0 back-upfouten ondervinden. Om back-upfouten te voor komen of op te lossen, [moet u de Mars-agent upgraden naar de nieuwste versie](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Volg de stappen in een [upgrade van de Microsoft Azure Recovery Services-agent (Mars)](../articles/backup/upgrade-mars-agent.md)om servers te identificeren waarvoor een Mars-agent upgrade is vereist.

De MARS-agent wordt gebruikt voor het maken van een back-up van bestanden en mappen en de systeem status gegevens naar Azure. System Center DPM en Azure Backup Server gebruiken de MARS-agent om een back-up te maken van gegevens in Azure.
