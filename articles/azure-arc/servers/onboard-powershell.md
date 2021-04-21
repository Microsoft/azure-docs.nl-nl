---
title: Hybride machines verbinden met Azure met behulp van PowerShell
description: In dit artikel leert u hoe u de agent installeert en een machine verbindt met Azure met behulp van Azure Arc ingeschakelde servers. U kunt dit doen met PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d6963a53ac14c9d6727a8d53e781bc8b8389b76e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831613"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Hybride machines verbinden met Azure met behulp van PowerShell

Voor servers die zijn ingeschakeld met Azure Arc, kunt u handmatige stappen ondernemen om deze in te stellen voor een of meer Windows- of Linux-machines in uw omgeving. U kunt ook de PowerShell-cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) gebruiken om de Connected Machine-agent te downloaden, de agent te installeren en de machine te registreren bij Azure Arc. De cmdlet downloadt het Windows-agentpakket (Windows Installer) vanuit het Microsoft Downloadcentrum en het Linux-agentpakket uit de Pakketopslagplaats van Microsoft.

Voor deze methode moet u beheerdersmachtigingen hebben op de computer om de agent te installeren en te configureren. In Linux bent u met behulp van het hoofdaccount en in Windows lid van de groep Lokale beheerders. U kunt dit proces interactief of op afstand op een Windows-server voltooien met externe toegang [van PowerShell.](/powershell/scripting/learn/ps101/08-powershell-remoting)

Voordat u aan de slag gaat, controleert u [de vereisten en](agent-overview.md#prerequisites) controleert u of uw abonnement en resources voldoen aan de vereisten. Zie Ondersteunde Azure-regio's voor meer informatie over ondersteunde [regio's en andere gerelateerde overwegingen.](overview.md#supported-regions)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een machine met Azure PowerShell. Zie Install [and configure Azure PowerShell voor instructies.](/powershell/azure/)

U gebruikt PowerShell voor het beheren van VM-extensies op uw hybride servers die worden beheerd Azure Arc ingeschakelde servers. Voordat u PowerShell gebruikt, moet u de `Az.ConnectedMachine` module installeren. Voer de volgende opdracht uit op de server die is ingeschakeld met Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Wanneer de installatie is voltooien, ziet u het volgende bericht:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>De agent installeren en verbinding maken met Azure

1. Open een PowerShell-console met verhoogde bevoegdheden.

2. Meld u aan bij Azure door de opdracht uit te `Connect-AzAccount` voeren.

3. Als u de Connected Machine-agent wilt installeren, `Connect-AzConnectedMachine` gebruikt u met de parameters , en `-Name` `-ResourceGroupName` `-Location` . Gebruik de parameter om het standaardabonnement te overschrijven als gevolg van de `-SubscriptionId` Azure-context die is gemaakt na het aanmelden. Voer een van de volgende opdrachten uit:

    * Als u de Connected Machine-agent wilt installeren op de doelmachine die rechtstreeks met Azure kan communiceren, moet u het volgende uitvoeren:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Als u de Connected Machine-agent wilt installeren op de doelmachine die communiceert via een proxyserver, moet u het volgende uitvoeren:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Als de agent niet kan worden starten nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde foutinformatie. Controleer in Windows dit bestand: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log.* Controleer in Linux dit bestand: */var/opt/azcmagent/log/himds.log.*

## <a name="install-and-connect-by-using-powershell-remoting"></a>Installeren en verbinding maken met behulp van remoting van PowerShell

U kunt als volgende een of meer Windows-servers configureren met servers die zijn ingeschakeld met Azure Arc. U moet externe toegang van PowerShell inschakelen op de externe computer. Gebruik hiervoor de cmdlet `Enable-PSRemoting`.

1. Open een PowerShell-console als beheerder.

2. Meld u aan bij Azure door de opdracht uit te `Connect-AzAccount` voeren.

3. Als u de Connected Machine-agent wilt installeren, `Connect-AzConnectedMachine` gebruikt u met de parameters , en `-ResourceGroupName` `-Location` . De Azure-resourcenamen gebruiken automatisch de hostnaam van elke server. Gebruik de parameter om het standaardabonnement te overschrijven als gevolg van de `-SubscriptionId` Azure-context die is gemaakt na het aanmelden.

    * Voer de volgende opdracht uit om de Connected Machine-agent te installeren op de doelmachine die rechtstreeks met Azure kan communiceren:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Als u de Connected Machine-agent op meerdere externe machines tegelijk wilt installeren, voegt u een lijst met namen van externe machines toe, gescheiden door een komma.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    In het volgende voorbeeld ziet u de resultaten van de opdracht die gericht is op één computer:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>De verbinding met Azure Arc controleren

Nadat u de agent hebt geïnstalleerd en geconfigureerd voor registratie bij Azure Arc ingeschakelde servers, gaat u naar de Azure Portal om te controleren of de server verbinding heeft gemaakt. Bekijk uw machine in [Azure Portal](https://portal.azure.com).

![Schermopname van het dashboard Servers met een geslaagde serververbinding.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

* Zie indien nodig de handleiding Troubleshoot Connected Machine agent (Problemen met [de connected machine-agent oplossen).](troubleshoot-agent-onboard.md)

* Meer informatie over het beheren van uw computer met [behulp Azure Policy](../../governance/policy/overview.md). U kunt VM-gastconfiguratie gebruiken, controleren of de machine rapporteert aan de verwachte Log Analytics-werkruimte en bewaking inschakelen met Azure Monitor [met VM's.](../../azure-monitor/vm/vminsights-enable-policy.md) [](../../governance/policy/concepts/guest-configuration.md)

* Meer informatie over de [Log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist als u bewakingsgegevens van het besturingssysteem en de werkbelasting wilt verzamelen of wilt beheren met behulp van Azure Automation-runbooks of functies zoals Updatebeheer. Deze agent is ook vereist voor het gebruik van andere Azure-services, [zoals Azure Security Center.](../../security-center/security-center-introduction.md)
