---
title: 'Azure VPN Gateway: waarschuwingen voor diagnostische bron logboek gebeurtenissen configureren'
description: Meer informatie over het instellen van waarschuwingen op basis van bron logboek gebeurtenissen van Azure VPN Gateway, met behulp van Azure Monitor Log Analytics.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 1f10799ab32b6cb26a8b3b5b343f65b960d71357
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006998"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Waarschuwingen instellen voor resource logboek gebeurtenissen van VPN Gateway

Dit artikel helpt u bij het instellen van waarschuwingen op basis van bron logboek gebeurtenissen van Azure VPN Gateway met behulp van Azure Monitor Log Analytics. 

De volgende bron logboeken zijn beschikbaar in Azure:

|***Naam** _ | _*_Description_*_ |
|---        | ---               |
|GatewayDiagnosticLog | Bevat bron logboeken voor gateway configuratie gebeurtenissen, primaire wijzigingen en onderhouds gebeurtenissen |
|TunnelDiagnosticLog | Bevat status wijzigings gebeurtenissen voor de tunnel. Tunnel Connect/Disconnect-gebeurtenissen hebben een samenvattings reden voor de status wijziging, indien van toepassing |
|RouteDiagnosticLog | Registreert wijzigingen aan statische routes en BGP-gebeurtenissen die optreden op de gateway |
|IKEDiagnosticLog | Registreert IKE-besturings berichten en gebeurtenissen op de gateway |
|P2SDiagnosticLog | Registreert Point-to-site Control-berichten en gebeurtenissen op de gateway. De gegevens van de verbindings bron zijn alleen voor IKEv2-en OpenVPN-verbindingen. |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Waarschuwingen instellen in de Azure Portal

In de volgende voorbeeld stappen wordt een waarschuwing gemaakt voor een verbindings gebeurtenis waarbij een site-naar-site-VPN-tunnel is betrokken:


1. Zoek in de Azure Portal op _ *log Analytics** onder **alle services** en selecteer **log Analytics werk ruimten**.

   ![Selecties voor het naar Log Analytics werk ruimten gaan](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Maken")

2. Selecteer **maken** op de pagina **log Analytics** .

   ![Pagina Log Analytics met de knop maken](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Selecteer")

3. Selecteer **nieuwe maken** en vul de details in.

   ![Details voor het maken van een Log Analytics-werk ruimte](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Selecteer")

4. Zoek uw VPN-gateway op **Monitor** de  >  Blade **Diagnostische instellingen** controleren.

   ![Selecties voor het zoeken naar de VPN-gateway in Diagnostische instellingen](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Selecteer")

5. Als u Diagnostische gegevens wilt inschakelen, dubbelklikt u op de gateway en selecteert u vervolgens **Diagnostische gegevens inschakelen**.

   ![Selecties voor het inschakelen van diagnostische gegevens](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Selecteer")

6. Vul de details in en zorg ervoor dat **verzenden naar log Analytics** en **TunnelDiagnosticLog** zijn geselecteerd. Kies de Log Analytics-werk ruimte die u in stap 3 hebt gemaakt.

   ![Geselecteerde selectie vakjes](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Selecteer")

   > [!NOTE]
   > Het kan een paar uur duren voordat de gegevens in eerste instantie worden weer gegeven.

7. Ga naar het overzicht van de resource van de virtuele netwerk gateway en selecteer **waarschuwingen** op het tabblad **bewaking** . Maak vervolgens een nieuwe waarschuwings regel of bewerk een bestaande waarschuwings regel.

   ![Selecties voor het maken van een nieuwe waarschuwings regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecteer")

   ![punt-naar-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecteer")
8. Selecteer de Log Analytics-werk ruimte en de resource.

   ![Selecties voor werk ruimte en resource](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Selecteer")

9. Selecteer **aangepaste logboek zoekactie** als de signaal logica onder **voor waarde toevoegen**.

   ![Selecties voor een aangepaste zoek opdracht in Logboeken](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Selecteer")

10. Voer de volgende query in het tekstvak **Zoek query** in. Vervang de waarden in <> en TimeGenerated naar wens.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Stel de drempel waarde in op 0 en selecteer **gereed**.

    ![Een query invoeren en een drempel waarde selecteren](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Selecteer")

11. Selecteer op de pagina **regel maken** de optie **Nieuw maken** onder de sectie **actie groepen** . Vul de details in en selecteer **OK**.

    ![Details voor een nieuwe actie groep](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Selecteer")

12. Vul op de pagina **regel maken** de details in voor het **aanpassen van acties** en zorg ervoor dat de juiste naam wordt weer gegeven in de sectie naam van de **actie groep** . Selecteer **waarschuwings regel maken** om de regel te maken.

    ![Selecties voor het maken van een regel](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Selecteer")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Waarschuwingen instellen met behulp van Power shell

In de volgende voorbeeld stappen wordt een waarschuwing gemaakt voor een verbindings gebeurtenis waarbij een site-naar-site-VPN-tunnel betrokken is.

1. Een Log Analytics-werk ruimte maken:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Diagnostische gegevens inschakelen voor de VPN-gateway:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Een actie groep maken.

   Met deze code wordt een actie groep gemaakt waarmee een e-mail melding wordt verzonden wanneer een waarschuwing wordt geactiveerd:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Een waarschuwings regel maken op basis van een aangepaste zoek opdracht in Logboeken:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Volgende stappen

Zie [waarschuwingen instellen voor VPN gateway metrische gegevens](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)voor het configureren van waarschuwingen voor metrische gegevens van de tunnel.
