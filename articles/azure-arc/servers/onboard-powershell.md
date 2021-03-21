---
title: Hybride computers verbinden met Azure met behulp van Power shell
description: In dit artikel leert u hoe u de Agent installeert en een machine verbindt met Azure met behulp van Azure Arc-servers. U kunt dit doen met Power shell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 07a00de9077378ce3e3f7a7578b66e93d1b04f2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584940"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Hybride computers verbinden met Azure met behulp van Power shell

Voor servers met Azure Arc kunt u hand matige stappen uitvoeren om deze in te scha kelen voor een of meer Windows-of Linux-computers in uw omgeving. U kunt ook de Power shell-cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) gebruiken om de verbonden machine agent te downloaden, de agent te installeren en de machine te registreren met Azure Arc. Met de cmdlet wordt het Windows-agent pakket (Windows Installer) gedownload van het micro soft Download centrum en het Linux-agent pakket van de micro soft-pakket opslagplaats.

Voor deze methode moet u beheerders rechten op de computer hebben om de agent te installeren en configureren. Op Linux, met behulp van het hoofd account en in Windows, bent u lid van de lokale groep Administrators. U kunt dit proces interactief of extern uitvoeren op een Windows-Server met behulp van [externe communicatie met Power shell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Voordat u aan de slag gaat, moet u de [vereisten](agent-overview.md#prerequisites) controleren en controleren of uw abonnement en resources voldoen aan de vereisten. Zie [ondersteunde Azure-regio's](overview.md#supported-regions)voor meer informatie over ondersteunde regio's en andere gerelateerde overwegingen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een machine met Azure PowerShell. Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor instructies.

U kunt Power shell gebruiken om VM-extensies te beheren op uw hybride servers die worden beheerd door Azure Arc-servers. Voordat u Power shell gebruikt, moet u de `Az.ConnectedMachine` module installeren. Voer de volgende opdracht uit op de server die is ingeschakeld met Azure Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Wanneer de installatie is voltooid, wordt het volgende bericht weer gegeven:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>De agent installeren en verbinding maken met Azure

1. Open een Power shell-console met verhoogde bevoegdheden.

2. Meld u aan bij Azure door de opdracht uit te voeren `Connect-AzAccount` .

3. Als u de verbonden machine-agent wilt installeren, gebruikt u `Connect-AzConnectedMachine` met de `-Name` `-ResourceGroupName` `-Location` para meters, en. Gebruik de `-SubscriptionId` para meter om het standaard abonnement te negeren als gevolg van de Azure-context die is gemaakt na het aanmelden. Voer een van de volgende opdrachten uit:

    * Als u de verbonden machine-agent op de doel computer wilt installeren die rechtstreeks kan communiceren met Azure, voert u de volgende handelingen uit:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Voer de volgende stappen uit om de verbonden machine agent te installeren op de doel computer die communiceert via een proxy server:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

Als de agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. Controleer dit bestand op Windows: *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. Controleer dit bestand op Linux: */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Installeren en verbinding maken met behulp van externe communicatie met Power shell

U kunt als volgt een of meer Windows-servers configureren met servers die zijn ingeschakeld met Azure Arc. U moet externe communicatie van Power shell inschakelen op de externe computer. Gebruik hiervoor de cmdlet `Enable-PSRemoting`.

1. Open een Power shell-console als beheerder.

2. Meld u aan bij Azure door de opdracht uit te voeren `Connect-AzAccount` .

3. Als u de verbonden machine-agent wilt installeren, gebruikt u `Connect-AzConnectedMachine` met de `-ResourceGroupName` `-Location` para meters en. De naam van de Azure-resource gebruikt automatisch de hostnaam van elke server. Gebruik de `-SubscriptionId` para meter om het standaard abonnement te negeren als gevolg van de Azure-context die is gemaakt na het aanmelden.

    * Voer de volgende opdracht uit om de verbonden machine agent te installeren op de doel computer die rechtstreeks kan communiceren met Azure:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Als u de verbonden machine agent op meerdere externe computers tegelijk wilt installeren, voegt u een lijst met namen van externe computers toe, gescheiden door een komma.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    In het volgende voor beeld ziet u de resultaten van de opdracht die is gericht op één computer:
    
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

Nadat u de agent hebt geïnstalleerd en geconfigureerd voor registratie bij Azure Arc-servers, gaat u naar de Azure Portal om te controleren of de server met succes is verbonden. Bekijk uw machine in [Azure Portal](https://portal.azure.com).

![Scherm opname van het dash board servers, met een geslaagde server verbinding.](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

* Zie, indien nodig, de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

* Meer informatie over het beheren van uw machine met behulp van [Azure Policy](../../governance/policy/overview.md). U kunt VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md)gebruiken, controleren of de computer rapporteert aan de verwachte log Analytics-werk ruimte en de bewaking met [Azure monitor met vm's](../../azure-monitor/vm/vminsights-enable-policy.md)inschakelen.

* Meer informatie over de [log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u gegevens van het besturings systeem en werk belasting wilt verzamelen of beheren met Azure Automation runbooks of functies als Updatebeheer. Deze agent is ook vereist voor het gebruik van andere Azure-Services, zoals [Azure Security Center](../../security-center/security-center-introduction.md).
