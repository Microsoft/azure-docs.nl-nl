---
title: Aangesloten machine agent installeren met behulp van Windows Power shell DSC
description: In dit artikel leert u hoe u met behulp van Windows Power shell DSC computers kunt verbinden met Azure met behulp van Azure Arc-servers.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: c0ae9c97afe14559aa36c1b8387f07897aa4c43b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587643"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>De verbonden machine agent installeren met behulp van Windows Power shell DSC

Met [Windows Power shell desired state Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted) (DSC) kunt u software-installatie en-configuratie voor een Windows-computer automatiseren. In dit artikel wordt beschreven hoe u DSC kunt gebruiken om de met Azure Arc ingeschakelde computer agent op hybride Windows-computers te installeren.

## <a name="requirements"></a>Vereisten

- Windows Power shell-versie 4,0 of hoger

- De DSC-module [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc)

- Een Service-Principal om de computers te koppelen aan Azure Arc-servers die niet interactief zijn. Volg de stappen in de sectie [een service-principal maken voor onboarding op schaal](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) als u nog geen Service-Principal hebt gemaakt voor het inschakelen van Arc-servers.

## <a name="install-the-connectedmachine-dsc-module"></a>De ConnectedMachine DSC-module installeren

1. Als u de module hand matig wilt installeren, moet u de bron code downloaden en de inhoud van de projectmap uitpakken naar de `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Of voer de volgende opdracht uit om te installeren vanuit de Power shell Gallery met behulp van PowerShellGet (in Power shell 5,0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Als u de installatie wilt bevestigen, voert u de volgende opdracht uit en controleert u of er de DSC-resources van Azure Connected machine beschikbaar zijn.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   In de uitvoer moet er ongeveer als volgt uitzien:

   ![Bevestiging van installatie voor beeld van een verbonden computer DSC-module](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>De agent installeren en verbinding maken met Azure

De resources in deze module zijn ontworpen voor het beheren van de configuratie van de Azure Connected machine agent. Ook is een Power shell `AzureConnectedMachineAgent.ps1` -script gevonden dat in de map is opgenomen `AzureConnectedMachineDsc\examples` . Het maakt gebruik van community-bronnen voor het automatiseren van het downloaden en installeren en tot stand brengen van een verbinding met Azure Arc. Dit script voert soort gelijke stappen uit die worden beschreven in het artikel de [Azure Portal hybride computers verbinden met Azure](onboard-portal.md) .

Als de machine moet communiceren via een proxy server naar de service, moet u nadat u de agent hebt geïnstalleerd, een opdracht uitvoeren die [hier](manage-agent.md#update-or-remove-proxy-settings)wordt beschreven. Hiermee stelt u de systeem omgevingsvariabele van de proxy server in `https_proxy` . In plaats van de opdracht hand matig uit te voeren, kunt u deze stap met DSC uitvoeren met behulp van de [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) -module.

>[!NOTE]
>Als u DSC wilt uitvoeren, moet u Windows configureren om externe Power shell-opdrachten te ontvangen, zelfs wanneer u een localhost-configuratie uitvoert. Als u uw omgeving eenvoudig op de juiste wijze wilt configureren, voert u de opdracht `Set-WsManQuickConfig -Force` uit in een Power shell-terminal met verhoogde bevoegdheden.
>

Configuratie documenten (MOF-bestanden) kunnen worden toegepast op de computer met behulp van de- `Start-DscConfiguration` cmdlet.

Hieronder vindt u de para meters die u aan het Power shell-script geeft.

- `TenantId`: De unieke id (GUID) die staat voor uw toegewezen exemplaar van Azure AD.

- `SubscriptionId`: De abonnements-ID (GUID) van uw Azure-abonnement waarvoor u de computers wilt.

- `ResourceGroup`: De naam van de resource groep waaraan u de verbonden computers wilt koppelen.

- `Location`: Zie [ondersteunde Azure-regio's](overview.md#supported-regions). Deze locatie kan hetzelfde zijn als, of verschillen van, de locatie van de resourcegroep.

- `Tags`: Teken reeks matrix van tags die moeten worden toegepast op de bron van de verbonden machine.

- `Credential`: Een Power shell-referentie object met de **ApplicationId** en het **wacht woord** dat wordt gebruikt om machines op schaal te registreren met behulp van een [Service-Principal](onboard-service-principal.md).

1. Navigeer in een Power shell-console naar de map waarin u het `.ps1` bestand hebt opgeslagen.

2. Voer de volgende Power shell-opdrachten uit om het MOF-document te compileren (Zie DSC- [configuraties](/powershell/scripting/dsc/configurations/configurations)voor informatie over het compileren van DSC-configuraties:

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Hiermee maakt u een `localhost.mof file` in een nieuwe map met de naam `C:\dsc` .

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met servers met Azure-Arc, gaat u naar de Azure Portal om te controleren of de server met succes is verbonden. Bekijk uw computers in [Azure Portal](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Toevoegen aan bestaande configuraties

Deze resource kan worden toegevoegd aan bestaande DSC-configuraties om een end-to-end-configuratie voor een machine aan te duiden. U kunt deze resource bijvoorbeeld toevoegen aan een configuratie waarin de instellingen voor beveiligde besturings systemen worden ingesteld.

De [CompositeResource](https://www.powershellgallery.com/packages/compositeresource) -module van de PowerShell Gallery kan worden gebruikt om een [samengestelde resource](/powershell/scripting/dsc/resources/authoringResourceComposite) van de voorbeeld configuratie te maken, om het combi neren van configuraties verder te vereenvoudigen.

## <a name="next-steps"></a>Volgende stappen

* Informatie over probleem oplossing vindt u in de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

* Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), moet u controleren of de computer rapporteert aan de verwachte log Analytics-werk ruimte, de bewaking inschakelen met [Azure monitor met vm's](../../azure-monitor/vm/vminsights-enable-policy.md)en nog veel meer.

* Meer informatie over de [log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u het besturingssysteem en workloads op de machine proactief wilt monitoren, deze wilt beheren met Automation-runbooks of oplossingen zoals Updatebeheer, of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-introduction.md) wilt gebruiken.