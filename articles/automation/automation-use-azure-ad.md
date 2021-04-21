---
title: Azure AD instellen in Azure Automation om bij Azure te verifiëren
description: In dit artikel wordt beschreven hoe u Azure AD binnen Azure Automation provider gebruikt voor verificatie bij Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34033589a297b1a3a2abb97d346f1da478f950e6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830282"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Azure AD gebruiken voor verificatie bij Azure

De [Azure Active Directory (AD)-service](../active-directory/fundamentals/active-directory-whatis.md) maakt een aantal beheertaken mogelijk, zoals gebruikersbeheer, domeinbeheer en configuratie van een-op-een. In dit artikel wordt beschreven hoe u Azure AD binnen Azure Automation als provider voor verificatie bij Azure. 

## <a name="install-azure-ad-modules"></a>Azure AD-modules installeren

U kunt Azure AD inschakelen via de volgende PowerShell-modules:

* Azure Active Directory PowerShell voor Graph (AzureRM- en Az-modules). Azure Automation wordt geleverd met de AzureRM-module en de recente upgrade, de Az-module. Functionaliteit omvat niet-interactieve verificatie voor Azure met behulp van verificatie op basis van referenties van Azure AD-gebruikers (OrgId). Zie [Azure AD 2.0.2.76.](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76)

* Microsoft Azure Active Directory voor Windows PowerShell (MSOnline-module). Deze module maakt interactie met Microsoft Online mogelijk, waaronder Microsoft 365.

>[!NOTE]
>PowerShell Core biedt geen ondersteuning voor de MSOnline-module. Als u de module-cmdlets wilt gebruiken, moet u ze uitvoeren vanuit Windows PowerShell. U wordt aangeraden de nieuwere versie Azure Active Directory PowerShell voor Graph-modules te gebruiken in plaats van de MSOnline-module. 

### <a name="preinstallation"></a>Voorinstallatie

Voordat u de Azure AD-modules op uw computer installeert:

* Verwijder eerdere versies van de AzureRM/Az-module en de module MSOnline. 

* Verwijder de Microsoft Online Services Sign-In Assistant om de juiste werking van de nieuwe PowerShell-modules te garanderen.  

### <a name="install-the-azurerm-and-az-modules"></a>De AzureRM- en Az-modules installeren

>[!NOTE]
>Als u met deze modules wilt werken, moet u PowerShell versie 5.1 of hoger gebruiken met een 64-bits versie van Windows. 

1. Installeer Windows Management Framework (WMF) 5.1. Zie [WMF 5.1 installeren en configureren.](/powershell/scripting/wmf/setup/install-configure)

2. Installeer AzureRM en/of Az met behulp van instructies in [Azure PowerShell installeren in Windows met PowerShellGet.](/powershell/azure/azurerm/install-azurerm-ps)

### <a name="install-the-msonline-module"></a>De MSOnline-module installeren

>[!NOTE]
>Als u de MSOnline-module wilt installeren, moet u lid zijn van een beheerdersrol. Zie [Over beheerdersrollen.](/microsoft-365/admin/add-users/about-admin-roles)

1. Zorg ervoor dat Microsoft .NET Framework 3.5.x-functie is ingeschakeld op uw computer. Het is waarschijnlijk dat op uw computer een nieuwere versie is geïnstalleerd, maar achterwaartse compatibiliteit met oudere versies van de .NET Framework kan worden ingeschakeld of uitgeschakeld. 

2. Installeer de 64-bits versie van [de Microsoft Online Services-aan aanmeldingsassistent](https://www.microsoft.com/Download/details.aspx?id=28177).

3. Voer Windows PowerShell als beheerder uit om een opdrachtprompt met verhoogde Windows PowerShell maken.

4. Implementeer Azure Active Directory [vanuit MSOnline 1.0.](http://www.powershellgallery.com/packages/MSOnline/1.0)

5. Als u wordt gevraagd de NuGet-provider te installeren, typt u Y en drukt u op ENTER.

6. Als u wordt gevraagd de module te installeren vanuit [PSGallery,](https://www.powershellgallery.com/)typt u Y en drukt u op ENTER.

### <a name="install-support-for-pscredential"></a>Ondersteuning voor PSCredential installeren

Azure Automation gebruikt de [psCredential-klasse](/dotnet/api/system.management.automation.pscredential) om een referentie-asset weer te geven. Uw scripts halen `PSCredential` objecten op met behulp van de `Get-AutomationPSCredential` cmdlet . Zie [Referentie-assets in](shared-resources/credentials.md)Azure Automation.

## <a name="assign-a-subscription-administrator"></a>Abonnementsbeheerder toewijzen

U moet een beheerder toewijzen voor het Azure-abonnement. Deze persoon heeft de rol van Eigenaar voor het abonnementsbereik. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation.](automation-role-based-access-control.md) 

## <a name="change-the-azure-ad-users-password"></a>Het wachtwoord van de Azure AD-gebruiker wijzigen

Het wachtwoord van de Azure AD-gebruiker wijzigen:

1. Meld u af bij Azure.

2. Laat de beheerder zich bij Azure aanmelden als de Azure AD-gebruiker die zojuist is gemaakt, met de volledige gebruikersnaam (inclusief het domein) en een tijdelijk wachtwoord. 

3. Vraag de beheerder om het wachtwoord te wijzigen wanneer u hier om wordt gevraagd.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Configuratie Azure Automation om het Azure-abonnement te beheren

Als Azure Automation wilt communiceren met Azure AD, moet u de referenties ophalen die zijn gekoppeld aan de Azure-verbinding met Azure AD. Voorbeelden van deze referenties zijn tenant-id, abonnements-id en dergelijke. Zie Uw organisatie verbinden met Azure Active Directory voor meer informatie over de verbinding tussen Azure [Azure Active Directory.](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)

## <a name="create-a-credential-asset"></a>Een referentie-asset maken

Nu de Azure-referenties voor Azure AD beschikbaar zijn, is het tijd om een Azure Automation-referentie-asset te maken om de Azure AD-referenties veilig op te slaan, zodat runbooks en DSC-scripts (Desire State Configuration) er toegang toe hebben. U kunt dit doen met behulp van de Azure Portal of PowerShell-cmdlets.

### <a name="create-the-credential-asset-in-azure-portal"></a>De referentie-asset maken in Azure Portal

U kunt de Azure Portal gebruiken om de referentie-asset te maken. Doe deze bewerking vanuit uw Automation-account met **behulp van referenties onder** Gedeelde **resources.** Zie [Referentieactiva in Azure Automation.](shared-resources/credentials.md)

### <a name="create-the-credential-asset-with-windows-powershell"></a>Maak de referentie-asset met Windows PowerShell

Als u een nieuwe referentie-asset in Windows PowerShell, maakt uw script eerst een -object met behulp van de toegewezen `PSCredential` gebruikersnaam en het toegewezen wachtwoord. Het script gebruikt dit object vervolgens om de asset te maken via een aanroep naar de cmdlet [New-AzureAutomationCredential.](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) Het script kan ook de cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) aanroepen om de gebruiker te vragen een naam en wachtwoord in te voeren. Zie [Referentie-assets in Azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Azure-resources beheren vanuit een Azure Automation runbook

U kunt Azure-resources beheren vanuit Azure Automation runbooks met behulp van de referentie-asset. Hieronder vindt u een voorbeeld van een PowerShell-runbook dat de referentie-asset verzamelt die moet worden gebruikt voor het stoppen en starten van virtuele machines in een Azure-abonnement. Dit runbook gebruikt eerst om `Get-AutomationPSCredential` de referentie op te halen die moet worden gebruikt voor verificatie bij Azure. Vervolgens wordt de cmdlet [Connect-AzAccount aanroepen](/powershell/module/az.accounts/connect-azaccount) om verbinding te maken met Azure met behulp van de referentie. Het script maakt gebruik van de cmdlet [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription) om het abonnement te kiezen om mee te werken. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Volgende stappen

* Zie Referenties beheren in Azure Automation voor meer informatie over [het Azure Automation.](shared-resources/credentials.md)
* Zie Modules beheren in Azure Automation voor [meer informatie over modules.](shared-resources/modules.md)
* Als u een runbook wilt starten, zie [Een runbook starten in Azure Automation.](start-runbooks.md)
* Zie [PowerShell Docs voor meer informatie over PowerShell.](/powershell/scripting/overview)
