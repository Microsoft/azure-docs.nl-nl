---
title: Problemen met VPN-gateways oplossen en controleren-Azure Automation
titleSuffix: Azure Network Watcher
description: In dit artikel wordt beschreven hoe u on-premises connectiviteit met Azure Automation en Network Watcher opspoort
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: af671996722524de9af1a90ae8dfde27f814c8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011809"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>VPN-gateways bewaken met Network Watcher probleem oplossing

Het verkrijgen van diepe inzichten op uw netwerk prestaties is van cruciaal belang voor het leveren van betrouw bare services aan klanten. Daarom is het belang rijk om snel netwerk storingen te detecteren en corrigerende maat regelen te nemen om de storings voorwaarde te verminderen. Met Azure Automation kunt u een taak in een programmatische manier implementeren en uitvoeren via runbooks. Met Azure Automation maakt u een perfecte recept voor het uitvoeren van doorlopende en proactieve netwerk bewaking en waarschuwingen.

## <a name="scenario"></a>Scenario

Het scenario in de volgende afbeelding is een toepassing met meerdere lagen, met on-premises connectiviteit tot stand gebracht met behulp van een VPN Gateway en tunnel. Zorg ervoor dat de VPN Gateway actief is en is essentieel voor de prestaties van de toepassingen.

Er wordt een runbook gemaakt met een script om te controleren op verbindings status van de VPN-tunnel, met behulp van de resource Troubleshooting API om te controleren op de status van de verbindings tunnel. Als de status niet in orde is, wordt er een e-mail trigger verzonden naar beheerders.

![Voorbeeldscenario][scenario]

Dit scenario gaat als volgt:

- Een runbook maken dat de `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet aanroept om de verbindings status op te lossen
- Een planning aan het runbook koppelen

## <a name="before-you-begin"></a>Voordat u begint

Voordat u met dit scenario begint, moet u beschikken over de volgende vereisten:

- Een Azure Automation-account in Azure. Zorg ervoor dat het Automation-account de meest recente modules heeft en ook de module AzureRM. Network heeft. De module AzureRM. Network is beschikbaar in de module galerie als u deze aan uw Automation-account wilt toevoegen.
- U moet een set referenties configureren in Azure Automation. Meer informatie over [Azure Automation Security](../automation/automation-security-overview.md)
- Een geldige SMTP-server (Microsoft 365, uw on-premises e-mail adres of een andere) en referenties gedefinieerd in Azure Automation
- Een geconfigureerde Virtual Network gateway in Azure.
- Een bestaand opslag account met een bestaande container waarin de logboeken worden opgeslagen.

> [!NOTE]
> De infra structuur die in de voor gaande afbeelding wordt weer gegeven, is bedoeld voor illustratie doeleinden en wordt niet gemaakt met de stappen in dit artikel.

### <a name="create-the-runbook"></a>Het runbook maken

De eerste stap voor het configureren van het voor beeld is het runbook te maken. In dit voor beeld wordt een uitvoeren als-account gebruikt. Ga voor meer informatie over run-as-accounts naar [Runbooks verifiëren met Azure uitvoeren als-account](../automation/manage-runas-account.md)

### <a name="step-1"></a>Stap 1

Navigeer naar Azure Automation in het [Azure Portal](https://portal.azure.com) en klik op **Runbooks**

![overzicht van Automation-account][1]

### <a name="step-2"></a>Stap 2

Klik op **een Runbook toevoegen** om het proces voor het maken van het runbook te starten.

![Blade runbooks][2]

### <a name="step-3"></a>Stap 3

Klik onder **snel maken** op **een nieuw runbook maken** om het runbook te maken.

![een runbook-Blade toevoegen][3]

### <a name="step-4"></a>Stap 4

In deze stap geven we het runbook een naam, in het voor beeld wordt **Get-VPNGatewayStatus** genoemd. Het is belang rijk om het runbook een beschrijvende naam te geven en het aan te bevelen een naam toe te wijzen die voldoet aan de standaard naamgevings regels voor Power shell. Het runbook-type voor dit voor beeld is **Power shell**, de andere opties zijn grafische, Power shell-werk stroom en grafische power shell-werk stroom.

![runbook-Blade][4]

### <a name="step-5"></a>Stap 5

In deze stap wordt het runbook gemaakt, het volgende code voorbeeld bevat de code die nodig is voor het voor beeld. De items in de code die \<value\> moeten worden vervangen door de waarden van uw abonnement.

Gebruik de volgende code als klikken op **Opslaan**

```powershell
# Set these variables to the proper values for your environment
$automationCredential = "<work or school account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for work or school account
$cred = Get-AutomationPSCredential -Name $automationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Stap 6

Zodra het runbook is opgeslagen, moet er een schema aan worden gekoppeld om het starten van het runbook te automatiseren. Klik op **plannen** om het proces te starten.

![Stap 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Een planning aan het runbook koppelen

U moet een nieuw schema maken. Klik op **een planning aan uw Runbook koppelen**.

![Stap 7][7]

### <a name="step-1"></a>Stap 1

Klik op **een nieuwe planning maken** op de Blade **schema**

![Stap 8][8]

### <a name="step-2"></a>Stap 2

Vul op de Blade **Nieuw schema** de plannings gegevens in. De waarden die kunnen worden ingesteld, vindt u in de volgende lijst:

- **Name** : de beschrijvende naam van het schema.
- **Beschrijving** : een beschrijving van het schema.
- **Start** -deze waarde is een combi natie van de datum, tijd en tijd zone die het tijdstip vormt waarop de planning wordt geactiveerd.
- **Recurrence** -deze waarde bepaalt de herhaling van de planning.  Geldige waarden zijn **eenmaal** of **terugkerend**.
- **Keer herhalen elke** -het interval van het terugkeer patroon van de planning in uren, dagen, weken of maanden.
- **Verval datum instellen** : de waarde bepaalt of het schema moet verlopen of niet. Kan worden ingesteld op **Ja** of **Nee**. Als u Ja kiest, moet u een geldige datum en tijd opgegeven.

> [!NOTE]
> Als u een runbook vaker wilt uitvoeren dan elk uur, moeten er meerdere planningen worden gemaakt met verschillende intervallen (dat wil zeggen 15, 30, 45 minuten na het uur)

![Stap 9][9]

### <a name="step-3"></a>Stap 3

Klik op Opslaan om de planning op te slaan in het runbook.

![Stap 10][10]

## <a name="next-steps"></a>Volgende stappen

Nu u een goed idee hebt over het integreren van Network Watcher het oplossen van problemen met Azure Automation, lees dan hoe u pakket opnames kunt activeren voor VM-waarschuwingen door te bezoeken [een waarschuwing voor het vastleggen van pakketten met Azure Network Watcher maken](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png