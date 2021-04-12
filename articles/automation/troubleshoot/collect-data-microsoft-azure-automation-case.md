---
title: Gegevens die moeten worden verzameld bij het openen van een case voor Microsoft Azure automatisering | Microsoft Docs
description: In dit artikel wordt beschreven welke informatie u moet verzamelen voordat u een case opent voor Azure Automation met Microsoft Azure-ondersteuning.
services: automation
ms.subservice: ''
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.openlocfilehash: 3d2c0cf780b992d7bb464969c50d60ffa5093e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896559"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Te verzamelen gegevens bij het openen van een case voor Microsoft Azure Automation

In dit artikel wordt een deel van de informatie beschreven die u moet verzamelen voordat u een aanvraag voor Azure Automation opent met Microsoft Azure-ondersteuning. Deze informatie is niet vereist om de aanvraag te openen. Het kan micro soft echter helpen om uw probleem sneller op te lossen. Het is ook mogelijk dat u voor deze gegevens wordt gevraagd door de ondersteunings technicus nadat u de aanvraag hebt geopend.

## <a name="basic-data"></a>Basis gegevens

Verzamel de basis gegevens die zijn beschreven in Knowledge Base [-artikel 4034605-procedures voor het vastleggen van diagnostische Azure Automation scripts](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Gegevens voor Updatebeheer problemen op Linux

1. Naast de items die worden vermeld in KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), voert u het volgende hulp programma voor het verzamelen van Logboeken uit:

   [Logboek verzamelaar van OMS Linux-agent](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimeer de inhoud van de map **/var/opt/Microsoft/omsagent/run/automationworker/** en verzend vervolgens het gecomprimeerde bestand naar de ondersteuning van Azure.
 
3. Controleer of de ID van de werk ruimte die de Log Analytics agent voor Linux rapporteert, hetzelfde is als de ID voor de werk ruimte die wordt gecontroleerd op updates.

## <a name="data-for-update-management-issues-on-windows"></a>Gegevens voor Updatebeheer problemen in Windows

1. Gegevens verzamelen voor de items die worden vermeld in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exporteer de volgende gebeurtenis logboeken naar de EVTX-indeling:

   * Systeem
   * Toepassing
   * Beveiliging
   * Operations Manager
   * Micro soft-SMA/operationeel

3. Controleer of de ID van de werk ruimte waarmee de agent rapporteert, overeenkomt met de ID van de werk ruimte die wordt bewaakt door Windows-updates.

## <a name="data-for-job-issues"></a>Gegevens voor taak problemen

1. Gegevens verzamelen voor de items die worden vermeld in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. De taak-ID voor de taak met een probleem verzamelen:

   1. Ga in Azure Portal naar **Automation-accounts**.
   2. Selecteer het Automation-account dat u wilt oplossen en noteer de naam.
   3. Selecteer **taken**.
   4. Kies de taak die u wilt oplossen.
   5. Zoek in het deel venster taak overzicht naar de GUID-waarde in **taak-id**.

   ![Taak-ID in deel venster taak overzicht](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Verzamel een voor beeld van het script dat u uitvoert.

4. De logboek bestanden verzamelen:

   1. Ga in Azure Portal naar **Automation-accounts**.
   2. Selecteer het Automation-account dat u wilt oplossen.
   3. Selecteer **taken**.
   4. Kies de taak die u wilt oplossen.
   5. Selecteer **alle logboeken**.
   6. Verzamel de gegevens in het resulterende deel venster.

   ![Gegevens die worden vermeld in alle logboeken](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Gegevens voor module problemen

Naast de [basis gegevens items](#basic-data), moet u de volgende informatie verzamelen:

* De stappen die u hebt gevolgd, zodat het probleem kan worden gereproduceerd.
* Scherm opnamen van fout berichten.
* Scherm afbeeldingen van de huidige modules en hun versie nummers.

## <a name="next-steps"></a>Volgende stappen

Als u meer hulp nodig hebt:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.
* Een ondersteunings incident voor Azure. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
