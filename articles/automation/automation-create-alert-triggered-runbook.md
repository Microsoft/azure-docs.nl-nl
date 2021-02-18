---
title: Een waarschuwing gebruiken om een Azure Automation runbook te activeren
description: In dit artikel leest u hoe u een runbook kunt activeren dat wordt uitgevoerd wanneer een Azure-waarschuwing wordt gegenereerd.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: add2bbb7b8f9eeb72c8c58b8c54b070a6b14d8e6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586067"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Een waarschuwing gebruiken om een Azure Automation runbook te activeren

U kunt [Azure monitor](../azure-monitor/overview.md) gebruiken voor het bewaken van metrische gegevens op basis niveau en logboeken voor de meeste services in Azure. U kunt Azure Automation runbooks aanroepen met behulp van [actie groepen](../azure-monitor/alerts/action-groups.md) of met klassieke waarschuwingen om taken te automatiseren op basis van waarschuwingen. In dit artikel wordt beschreven hoe u een runbook configureert en uitvoert met behulp van waarschuwingen.

## <a name="alert-types"></a>Waarschuwingstypen

U kunt Automation-runbooks gebruiken met drie waarschuwings typen:

* Algemene waarschuwingen
* Waarschuwingen voor activiteitenlogboeken
* Bijna realtime waarschuwingen voor metrische gegevens

> [!NOTE]
> Het algemene waarschuwings schema standaardisert de verbruiks ervaring voor waarschuwings meldingen in azure vandaag. In het verleden hebben de drie waarschuwings typen in azure vandaag (metrische gegevens, logboeken en activiteiten Logboeken) hun eigen e-mail sjablonen, webhook-schema's, enzovoort. Zie het [algemene waarschuwings schema](../azure-monitor/alerts/alerts-common-schema.md) voor meer informatie.

Wanneer een waarschuwing een runbook aanroept, is de daad werkelijke aanroep een HTTP POST-aanvraag naar de webhook. De hoofd tekst van de POST-aanvraag bevat een JSON-indelings object met handige eigenschappen die aan de waarschuwing zijn gerelateerd. De volgende tabel bevat koppelingen naar het payload-schema voor elk waarschuwings type:

|Waarschuwing  |Beschrijving|Payload-schema  |
|---------|---------|---------|
|[Algemene waarschuwing](../azure-monitor/alerts/alerts-common-schema.md)|Het algemene waarschuwings schema waarmee de verbruiks ervaring voor waarschuwings meldingen in azure vandaag wordt gestandaardiseerd.|Schema voor algemene nettoladingen van waarschuwingen|
|[Waarschuwing voor activiteiten logboek](../azure-monitor/alerts/activity-log-alerts.md)    |Hiermee verzendt u een melding wanneer een nieuwe gebeurtenis in het Azure-activiteiten logboek overeenkomt met specifieke voor waarden. Bijvoorbeeld wanneer een `Delete VM` bewerking plaatsvindt in **myProductionResourceGroup** of wanneer er een nieuwe Azure service Health gebeurtenis met de status actief wordt weer gegeven.| [Schema waarschuwing waarschuwings lading activiteiten logboek](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Waarschuwing voor bijna realtime metrische gegevens](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Hiermee wordt een melding sneller verzonden dan metrische waarschuwingen wanneer een of meer metrische gegevens op platform niveau voldoen aan de opgegeven voor waarden. Als de waarde voor **CPU%** op een VM bijvoorbeeld groter is dan 90 en de waarde voor **netwerk in** groter is dan 500 MB voor de afgelopen vijf minuten.| [Bijna real-time metrische schema voor waarschuwingen nettolading](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Omdat de gegevens die door elk type waarschuwing worden gegeven, verschillend zijn, wordt elk waarschuwings type anders afgehandeld. In de volgende sectie leert u hoe u een runbook maakt om verschillende soorten waarschuwingen te verwerken.

## <a name="create-a-runbook-to-handle-alerts"></a>Een runbook maken voor het verwerken van waarschuwingen

Als u automatisering met waarschuwingen wilt gebruiken, hebt u een runbook nodig dat logica heeft die de JSON-nettolading van de waarschuwing beheert die wordt door gegeven aan het runbook. Het volgende voor beeld-runbook moet worden aangeroepen vanuit een Azure-waarschuwing.

Zoals beschreven in de vorige sectie, heeft elk type waarschuwing een ander schema. Het script gebruikt de gegevens van de webhook van een waarschuwing in de `WebhookData` invoer parameter van het runbook. Vervolgens evalueert het script de JSON-nettolading om te bepalen welk waarschuwings type wordt gebruikt.

In dit voor beeld wordt een waarschuwing van een virtuele machine gebruikt. De VM-gegevens worden opgehaald uit de payload en vervolgens die informatie gebruikt om de virtuele machine te stoppen. De verbinding moet worden ingesteld in het Automation-account waarop het runbook wordt uitgevoerd. Wanneer u waarschuwingen gebruikt voor het activeren van runbooks, is het belang rijk om de status van de waarschuwing te controleren in het runbook dat wordt geactiveerd. De runbook wordt geactiveerd telkens wanneer de status van de waarschuwing verandert. Waarschuwingen hebben meerdere statussen, waarbij de twee meest voorkomende worden geactiveerd en opgelost. Controleer of de status van de runbook-logica wordt gebruikt om te controleren of het runbook niet meer dan één keer wordt uitgevoerd. In het voor beeld in dit artikel ziet u hoe u waarschuwingen met alleen de status geactiveerd kunt zoeken.

Het runbook maakt gebruik van het `AzureRunAsConnection` [Run as-account](./automation-security-overview.md) voor de verbinding met Azure om de beheer actie uit te voeren op de virtuele machine.

Gebruik dit voor beeld om een runbook met de naam **Stop-AzureVmInResponsetoVMAlert** te maken. U kunt het Power shell-script wijzigen en het gebruiken met veel verschillende bronnen.

1. Ga naar uw Azure Automation-account.
2. Selecteer onder **Procesautomatisering** de optie **Runbooks**.
3. Selecteer boven aan de lijst met runbooks de optie **+ Een runbook maken**.
4. Voer op de pagina **Runbook toevoegen** de waarde **Stop-AzureVmInResponsetoVMAlert** in voor de runbooknaam. Selecteer **PowerShell** voor het type runbook. Ten slotte selecteert u **Create**.  
5. Kopieer het volgende Power shell-voor beeld naar de pagina **bewerken** .

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Selecteer **Publiceren** om het runbook op te slaan en te publiceren.

## <a name="create-the-alert"></a>De waarschuwing maken

Waarschuwingen gebruiken actie groepen, die bestaan uit verzamelingen acties die worden geactiveerd door de waarschuwing. Runbooks zijn slechts een van de vele acties die u kunt gebruiken met actie groepen.

1. Selecteer in uw Automation-account **waarschuwingen** onder **bewaking**.
1. Selecteer **+ Nieuwe waarschuwingsregel**.
1. Klik op **selecteren** onder **resource**. Selecteer op de pagina **een resource selecteren** de virtuele machine waarvoor u een waarschuwing wilt ontvangen en klik op **gereed**.
1. Klik op **voor waarde toevoegen** onder **voor waarde**. Selecteer het signaal dat u wilt gebruiken, bijvoorbeeld **percentage CPU** en klik op **gereed**.
1. Voer op de pagina **signaal logica configureren** uw **drempel waarde** in onder **waarschuwings logica** en klik op **gereed**.
1. Selecteer onder **Actiegroepen** de optie **Nieuwe maken**.
1. Geef op de pagina **actie groep toevoegen** een naam en een korte naam op voor de actie groep.
1. Geef een naam op voor de actie. Selecteer **Automation Runbook** voor het actie type.
1. Selecteer **Details bewerken**. Selecteer op de pagina **Runbook configureren** onder **Runbook-bron** de optie **gebruiker**.  
1. Selecteer uw **abonnement** en **Automation-account** en selecteer vervolgens het runbook **Stop-AzureVmInResponsetoVMAlert** .  
1. Selecteer **Ja** om **het schema common Alerts in te scha kelen**.
1. Selecteer **OK** om de actie groep te maken.

    ![Pagina actie groep toevoegen](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    U kunt deze actie groep gebruiken in de [activiteiten logboek waarschuwingen](../azure-monitor/alerts/activity-log-alerts.md) en [bijna realtime waarschuwingen](../azure-monitor/alerts/alerts-overview.md) die u maakt.

1. Voeg onder **waarschuwings Details** een naam en beschrijving voor de waarschuwings regel toe en klik op **waarschuwings regel maken**.

## <a name="next-steps"></a>Volgende stappen

* Als u een runbook wilt starten met een webhook, raadpleegt u [een Runbook starten vanuit een webhook](automation-webhooks.md).
* Zie [een Runbook starten](./start-runbooks.md)om verschillende manieren te ontdekken om een runbook te starten.
* Zie [waarschuwingen voor activiteiten logboek maken](../azure-monitor/alerts/activity-log-alerts.md)voor informatie over het maken van een waarschuwing voor een activiteiten logboek.
* Zie [een waarschuwings regel maken in de Azure Portal](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)voor meer informatie over het maken van een nabije realtime-waarschuwing.
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
