---
title: Een waarschuwing gebruiken om een Azure Automation runbook te activeren
description: In dit artikel wordt beschreven hoe u een runbook activeert om te worden uitgevoerd wanneer er een Azure-waarschuwing wordt ontvangen.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 36f1881ddd10498e7736de2d117a42021075abdc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834872"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Een waarschuwing gebruiken om een Azure Automation runbook te activeren

U kunt deze [Azure Monitor](../azure-monitor/overview.md) voor het bewaken van metrische gegevens en logboeken op basisniveau voor de meeste services in Azure. U kunt runbooks Azure Automation met behulp van [actiegroepen](../azure-monitor/alerts/action-groups.md) om taken te automatiseren op basis van waarschuwingen. In dit artikel wordt beschreven hoe u een runbook kunt configureren en uitvoeren met behulp van waarschuwingen.

## <a name="alert-types"></a>Waarschuwingstypen

U kunt Automation-runbooks gebruiken met drie waarschuwingstypen:

* Algemene waarschuwingen
* Waarschuwingen voor activiteitenlogboeken
* Waarschuwingen voor bijna realtime metrische gegevens

> [!NOTE]
> Het algemene waarschuwingsschema standaardiseer de verbruikservaring voor waarschuwingsmeldingen in Azure. In het verleden hadden de drie waarschuwingstypen in Azure (metrische gegevens, logboeken en activiteitenlogboek) hun eigen e-mailsjablonen, webhookschema's, enzovoort. Zie Common [alert schema (Algemeen waarschuwingsschema) voor meer informatie](../azure-monitor/alerts/alerts-common-schema.md)

Wanneer een waarschuwing een runbook aanroept, is de werkelijke aanroep een HTTP POST-aanvraag naar de webhook. De body van de POST-aanvraag bevat een JSON-geformatteerd object met nuttige eigenschappen die zijn gerelateerd aan de waarschuwing. De volgende tabel bevat koppelingen naar het payloadschema voor elk waarschuwingstype:

|Waarschuwing  |Beschrijving|Nettoladingschema  |
|---------|---------|---------|
|[Algemene waarschuwing](../azure-monitor/alerts/alerts-common-schema.md)|Het algemene waarschuwingsschema dat momenteel de verbruikservaring voor waarschuwingsmeldingen in Azure standaardiseren.|Schema voor algemene nettolading van waarschuwing|
|[Waarschuwing voor activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts.md)    |Hiermee wordt een melding verzendt wanneer een nieuwe gebeurtenis in het Azure-activiteitenlogboek overeenkomt met specifieke voorwaarden. Bijvoorbeeld wanneer een `Delete VM` bewerking plaatsvindt in **myProductionResourceGroup** of wanneer er een nieuwe Azure Service Health gebeurtenis met de status Actief wordt weergegeven.| [Schema voor de nettolading van waarschuwingen voor activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Waarschuwing voor bijna realtime metrische gegevens](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Verzendt een melding sneller dan waarschuwingen voor metrische gegevens wanneer een of meer metrische gegevens op platformniveau voldoen aan de opgegeven voorwaarden. Bijvoorbeeld wanneer de waarde voor **CPU%op** een VM groter is dan 90 en de waarde voor **Netwerk in** groter is dan 500 MB voor de afgelopen 5 minuten.| [Nettoladingschema voor waarschuwingen voor bijna realtime metrische gegevens](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Omdat de gegevens die door elk type waarschuwing worden verstrekt anders zijn, wordt elk waarschuwingstype anders verwerkt. In de volgende sectie leert u hoe u een runbook maakt voor het afhandelen van verschillende soorten waarschuwingen.

## <a name="create-a-runbook-to-handle-alerts"></a>Een runbook maken voor het afhandelen van waarschuwingen

Als u Automation wilt gebruiken met waarschuwingen, hebt u een runbook nodig dat logica heeft die de JSON-nettolading van de waarschuwing beheert die aan het runbook wordt doorgegeven. Het volgende voorbeeldrunbook moet worden aangeroepen vanuit een Azure-waarschuwing.

Zoals beschreven in de vorige sectie, heeft elk type waarschuwing een ander schema. Het script neemt de webhookgegevens van een waarschuwing in de `WebhookData` invoerparameter van het runbook. Vervolgens evalueert het script de JSON-nettolading om te bepalen welk waarschuwingstype wordt gebruikt.

In dit voorbeeld wordt een waarschuwing van een VM gebruikt. De VM-gegevens worden opgehaald uit de nettolading en vervolgens wordt die informatie gebruikt om de VM te stoppen. De verbinding moet worden ingesteld in het Automation-account waar het runbook wordt uitgevoerd. Wanneer u waarschuwingen gebruikt om runbooks te activeren, is het belangrijk om de status van de waarschuwing te controleren in het runbook dat wordt geactiveerd. Het runbook wordt telkens wanneer de status van de waarschuwing verandert, triggers. Waarschuwingen hebben meerdere -staten. De twee meest voorkomende zijn Geactiveerd en Opgelost. Controleer de status van uw runbooklogica om ervoor te zorgen dat het runbook niet meer dan één keer wordt uitgevoerd. In het voorbeeld in dit artikel ziet u hoe u kunt zoeken naar waarschuwingen met de status Alleen geactiveerd.

Het runbook gebruikt het Uitvoeren als-account van de verbindingsactiva om te verifiëren bij Azure om de beheeractie uit te `AzureRunAsConnection` [](./automation-security-overview.md) voeren op de VM.

Gebruik dit voorbeeld om een runbook met de **naam Stop-AzureVmInResponsetoVMAlert te maken.** U kunt het PowerShell-script wijzigen en dit gebruiken met veel verschillende resources.

1. Ga naar uw Azure Automation-account.
2. Selecteer onder **Procesautomatisering** de optie **Runbooks**.
3. Selecteer boven aan de lijst met runbooks de optie **+ Een runbook maken**.
4. Voer op **de pagina Runbook** toevoegen **Stop-AzureVmInResponsetoVMAlert** in als de naam van het runbook. Selecteer **PowerShell** voor het type runbook. Ten slotte selecteert u **Create**.  
5. Kopieer het volgende PowerShell-voorbeeld naar de **pagina** Bewerken.

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

Waarschuwingen maken gebruik van actiegroepen. Dit zijn verzamelingen acties die door de waarschuwing worden geactiveerd. Runbooks zijn slechts een van de vele acties die u met actiegroepen kunt gebruiken.

1. Selecteer in uw Automation-account **Waarschuwingen onder** **Bewaking.**
1. Selecteer **+ Nieuwe waarschuwingsregel**.
1. Klik **op Selecteren** onder **Resource**. Selecteer op **de pagina Een resource** selecteren de VM waar u een waarschuwing voor wilt ontvangen en klik op **Done.**
1. Klik **op Voorwaarde toevoegen** onder **Voorwaarde**. Selecteer het signaal dat u wilt gebruiken, bijvoorbeeld **CPU-percentage** en klik op **Klaar.**
1. Voer op **de pagina Signaallogica** configureren uw **Drempelwaarde in** onder **Waarschuwingslogica** en klik op **Done.**
1. Selecteer onder **Actiegroepen** de optie **Nieuwe maken**.
1. Geef op **de pagina Actiegroep** toevoegen een naam en een korte naam op voor de actiegroep.
1. Geef een naam op voor de actie. Als actietype selecteert u **Automation Runbook**.
1. Selecteer **Details bewerken.** Selecteer op **de pagina Runbook** configureren onder **Runbookbron** de optie **Gebruiker**.  
1. Selecteer uw **Abonnement** **en Automation-account** en selecteer vervolgens het runbook **Stop-AzureVmInResponsetoVMAlert.**  
1. Selecteer **Ja bij** Het algemene **waarschuwingsschema inschakelen.**
1. Selecteer OK om de actiegroep te **maken.**

    ![Pagina Actiegroep toevoegen](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    U kunt deze actiegroep gebruiken in de waarschuwingen voor [activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts.md) en bijna [realtime waarschuwingen die](../azure-monitor/alerts/alerts-overview.md) u maakt.

1. Voeg **onder Waarschuwingsdetails** de naam en beschrijving van een waarschuwingsregel toe en klik **op Waarschuwingsregel maken.**

## <a name="next-steps"></a>Volgende stappen

* Zie Een runbook starten vanuit een webhook als u een runbook wilt starten met behulp [van een webhook.](automation-webhooks.md)
* Zie Een runbook starten voor verschillende manieren om een [runbook te starten.](./start-runbooks.md)
* Zie Waarschuwingen voor activiteitenlogboek maken voor informatie over het maken van een waarschuwing [voor activiteitenlogboek.](../azure-monitor/alerts/activity-log-alerts.md)
* Zie Een waarschuwingsregel maken in de Azure Portal voor meer informatie over het maken [van een bijna-realtime Azure Portal.](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
