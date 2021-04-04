---
title: Azure Automation integreren met Event Grid | Microsoft Docs
description: Leer hoe u automatisch een tag toevoegt wanneer een nieuwe virtuele machine wordt gemaakt en een melding verzendt naar Microsoft Teams.
keywords: automatisering, runbook, teams, event grid, virtuele machine, VM
services: automation,event-grid
author: eamonoreilly
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 07/07/2020
ms.author: eamono
ms.openlocfilehash: 3b9b49a4d38566891f442a3d2d7eac9bf1d36465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87462000"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Zelfstudie: Azure Automation integreren met Event Grid en Microsoft-Teams

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een voorbeeldrunbook importeren in Event Grid.
> * Een optionele Microsoft Teams-webhook maken.
> * Een webhook voor het runbook maken.
> * Hiermee wordt een Event Grid-abonnement gemaakt.
> * Een virtuele machine maken die het runbook activeert.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Voor het volgen van deze zelfstudie is een [Azure Automation-account](../automation/index.yml) vereist voor het opslaan van het runbook dat wordt geactiveerd vanuit het Azure Event Grid-abonnement.

* De `AzureRM.Tags`module moet worden geladen in uw Automation-account. Zie [Modules in Azure Automation importeren](../automation/automation-update-azure-modules.md) voor informatie over het importeren van modules in Azure Automation.

## <a name="import-an-event-grid-sample-runbook"></a>Een voorbeeldrunbook importeren in Event Grid

1. Selecteer uw Automation-account en selecteer de pagina **Runbooks**.

   ![Runbooks selecteren](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Selecteer de knop **Bladeren in galerie**.

3. Zoek naar **Event Grid** en selecteer **Azure Automation integreren met Event Grid**.

    ![Galerierunbook importeren](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Selecteer **Importeren** en noem het runbook **Watch VMWrite**.

5. Nadat het is geïmporteerd, selecteert u **Bewerken** om de runbookbron weer te geven. 
6. Werk de regel 74 in het script bij voor het gebruik van `Tag` in plaats van `Tags`.

    ```powershell
    Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose
    ```
7. Selecteer de knop **Publiceren**.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Een optionele Microsoft Teams-webhook maken

1. Selecteer in Microsoft Teams **Meer opties** naast de naam van het kanaal, en selecteer vervolgens **Connectors**.

    ![Microsoft Teams-connectors](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Blader door de lijst met connectors naar **Binnenkomende webhook** en selecteer **Toevoegen**.

3. Voer **AzureAutomationIntegration** in voor de naam en selecteer **Maken**.

4. Kopieer de webhook-URL naar het klembord en sla deze op. De webhook-URL wordt gebruikt voor het verzenden van gegevens naar Microsoft Teams.

5. Selecteer **Gereed** om de webhook op te slaan.

## <a name="create-a-webhook-for-the-runbook"></a>Een webhook voor het runbook maken

1. Open het runbook Watch VMWrite.

2. Selecteer **Webhooks** en selecteer de knop **Webhook toevoegen**.

3. Voer **WatchVMEventGrid** in voor de naam. Kopieer de URL naar het klembord en sla deze op.

    ![Webhooknaam configureren](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Selecteer **Parameters en runinstellingen configureren** en voer de URL van de Microsoft Teams-webhook voor **CHANNELURL** in. Laat **WEBHOOKDATA** leeg.

    ![Webhookparameters configureren](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Selecteer **Maken** om de Automation-runbookwebhook te maken.

## <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

1. Selecteer op de overzichtspagina **Automation-Account** de optie **Event Grid**.

    ![Event Grid selecteren](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Klik op **+ Gebeurtenisabonnement**.

3. Configureer het abonnement met de volgende gegevens:
    1. Selecteer **Azure-abonnementen** voor **Onderwerptype**.
    2. Schakel het selectievakje **Abonneren op alle gebeurtenistypen** uit.
    3. Voer **AzureAutomation** in voor de naam.
    4. Schakel in de vervolgkeuzelijst **Gedefinieerde gebeurtenistypen** alle opties uit behalve **Schrijven resource gelukt**.

        > [!NOTE] 
        > In Azure Resource Manager wordt momenteel geen onderscheid gemaakt tussen Maken en Bijwerken. Hierdoor kan het implementeren van deze zelfstudie voor alle gebeurtenissen van Microsoft.Resources.ResourceWriteSuccess leiden tot een grote hoeveelheid aanroepen in uw Azure-abonnement.
    1. Selecteer **Webhook** voor **Eindpunttype**.
    2. Klik op **Een eindpunt selecteren**. Plak op de pagina **Webhook selecteren** die wordt geopend, de webhook-URL die u hebt gemaakt voor het runbook Watch-VMWrite.
    3. Voer onder **FILTERS** het abonnement en de resourcegroep in waarin u wilt zoeken naar de nieuwe virtuele machines die zijn gemaakt. Dit ziet er als volgt uit: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Selecteer **Maken** om het Event Grid-abonnement op te slaan.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Een virtuele machine maken die het runbook activeert

1. Maak een nieuwe virtuele machine in de resourcegroep die u hebt opgegeven in het voorvoegselfilter van het Event Grid-abonnement.

2. Het VMWrite-runbook moet worden aangeroepen en er moet een nieuwe tag worden toegevoegd aan de virtuele machine.

    ![VM-tag](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Er wordt een nieuw bericht verzonden naar het Microsoft Teams-kanaal.

    ![Microsoft Teams-melding](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u de integratie tussen Event Grid en Automation in. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een voorbeeldrunbook importeren in Event Grid.
> * Een optionele Microsoft Teams-webhook maken.
> * Een webhook voor het runbook maken.
> * Hiermee wordt een Event Grid-abonnement gemaakt.
> * Een virtuele machine maken die het runbook activeert.

> [!div class="nextstepaction"]
> [Aangepaste gebeurtenissen maken en routeren met behulp van Event Grid](../event-grid/custom-event-quickstart.md)
