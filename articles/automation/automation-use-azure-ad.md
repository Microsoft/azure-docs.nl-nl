---
title: Azure AD instellen in Azure Automation om bij Azure te verifiëren
description: In dit artikel leest u hoe u Azure AD in Azure Automation kunt gebruiken als provider voor verificatie bij Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 336c0387ac9febcc517c2ce358d0b04c80d10678
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576800"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Azure AD gebruiken voor verificatie bij Azure

De [Azure Active Directory-service (AD)](../active-directory/fundamentals/active-directory-whatis.md) maakt een aantal beheer taken mogelijk, zoals gebruikers beheer, domein beheer en configuratie van eenmalige aanmelding. In dit artikel wordt beschreven hoe u Azure AD in Azure Automation kunt gebruiken als provider voor verificatie bij Azure. 

## <a name="install-azure-ad-modules"></a>Azure AD-modules installeren

U kunt Azure AD inschakelen via de volgende Power shell-modules:

* Azure Active Directory Power shell for Graph (AzureRM-en AZ-modules). Azure Automation wordt geleverd met de AzureRM-module en de recente upgrade, de AZ-module. De functionaliteit omvat niet-interactieve verificatie voor Azure met behulp van verificatie op basis van Azure AD User (OrgId). Zie [Azure ad 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory voor Windows Power shell (MSOnline-module). Deze module maakt interacties mogelijk met micro soft online, met inbegrip van Microsoft 365.

>[!NOTE]
>Power shell core biedt geen ondersteuning voor de MSOnline-module. Als u de module-cmdlets wilt gebruiken, moet u deze uitvoeren vanuit Windows Power shell. U wordt aangeraden de nieuwere Azure Active Directory Power shell voor Graph-modules te gebruiken in plaats van de MSOnline-module. 

### <a name="preinstallation"></a>Preinstallation

Voordat u de Azure AD-modules op uw computer installeert:

* Verwijder alle vorige versies van de AzureRM/AZ-module en de MSOnline-module. 

* Verwijder de micro soft Online Services Sign-In-assistent om te zorgen voor een juiste werking van de nieuwe Power shell-modules.  

### <a name="install-the-azurerm-and-az-modules"></a>De AzureRM-en AZ-modules installeren

>[!NOTE]
>Als u met deze modules wilt werken, moet u Power shell versie 5,1 of hoger gebruiken met een 64-bits versie van Windows. 

1. Installeer Windows Management Framework (WMF) 5,1. Zie [WMF 5,1 installeren en configureren](/powershell/scripting/wmf/setup/install-configure).

2. Installeer AzureRM en/of AZ met behulp van de instructies in [Install Azure PowerShell op Windows met PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps).

### <a name="install-the-msonline-module"></a>De MSOnline-module installeren

>[!NOTE]
>Als u de MSOnline-module wilt installeren, moet u lid zijn van een beheerdersrol. Zie [over beheerders rollen](/microsoft-365/admin/add-users/about-admin-roles).

1. Zorg ervoor dat de functie Microsoft .NET Framework 3.5. x is ingeschakeld op de computer. Waarschijnlijk is er een nieuwere versie geïnstalleerd op uw computer, maar achterwaartse compatibiliteit met oudere versies van de .NET Framework kan worden ingeschakeld of uitgeschakeld. 

2. Installeer de 64-bits versie van de [micro soft Online Services-aanmeld hulp](https://www.microsoft.com/Download/details.aspx?id=28177).

3. Voer Windows Power shell uit als beheerder om een Windows Power shell-opdracht prompt met verhoogde bevoegdheden te maken.

4. Implementeer Azure Active Directory van [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Als u wordt gevraagd om de NuGet-provider te installeren, typt u j en drukt u op ENTER.

6. Als u wordt gevraagd om de module te installeren vanaf [PSGallery](https://www.powershellgallery.com/), typt u j en drukt u op ENTER.

### <a name="install-support-for-pscredential"></a>Ondersteuning voor PSCredential installeren

Azure Automation maakt gebruik van de [PSCredential](/dotnet/api/system.management.automation.pscredential) -klasse om een referentie-element aan te duiden. Uw scripts halen `PSCredential` objecten op met de `Get-AutomationPSCredential` cmdlet. Zie [referentie-assets in azure Automation](shared-resources/credentials.md)voor meer informatie.

## <a name="assign-a-subscription-administrator"></a>Abonnementsbeheerder toewijzen

U moet een beheerder toewijzen voor het Azure-abonnement. Deze persoon heeft de rol van eigenaar voor het abonnements bereik. Zie op [rollen gebaseerd toegangs beheer in azure Automation](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Het wacht woord van de Azure AD-gebruiker wijzigen

Het wacht woord van de gebruikers van de Azure AD-gebruiker wijzigen:

1. Meld u af bij Azure.

2. Zorg ervoor dat de beheerder zich bij Azure aanmeldt als de Azure AD-gebruiker die u zojuist hebt gemaakt, met behulp van de volledige gebruikers naam (inclusief het domein) en een tijdelijk wacht woord. 

3. Vraag de beheerder om het wacht woord te wijzigen wanneer u hierom wordt gevraagd.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Azure Automation configureren voor het beheren van het Azure-abonnement

Als Azure Automation wilt communiceren met Azure AD, moet u de referenties ophalen die zijn gekoppeld aan de Azure-verbinding met Azure AD. Voor beelden van deze referenties zijn de Tenant-ID, abonnements-ID en soort gelijke. Zie [verbinding maken met uw organisatie met Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)voor meer informatie over de verbinding tussen Azure en Azure AD.

## <a name="create-a-credential-asset"></a>Een referentie-Asset maken

Wanneer de Azure-referenties voor Azure AD beschikbaar zijn, is het tijd om een Azure Automation referentie-Asset te maken om de Azure AD-referenties veilig op te slaan, zodat runbooks en wens-(DSC)-scripts toegang hebben tot deze gegevens. U kunt dit doen met behulp van de Azure Portal-of Power shell-cmdlets.

### <a name="create-the-credential-asset-in-azure-portal"></a>Het referentie-element in Azure Portal maken

U kunt de Azure Portal gebruiken om de referentie-Asset te maken. Voer deze bewerking uit vanuit uw Automation-account met behulp van **referenties** onder **gedeelde bronnen**. Zie [referentie-assets in azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Het referentie-element maken met Windows Power shell

Als u een nieuw referentie-element wilt voorbereiden in Windows Power shell, maakt uw script eerst een- `PSCredential` object met behulp van de toegewezen gebruikers naam en het bijbehorende wacht woord. Het script gebruikt vervolgens dit object om het activum te maken via een aanroep van de cmdlet [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) . Het script kan de cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) ook aanroepen om de gebruiker te vragen om een naam en wacht woord in te voeren. Zie [referentie-assets in azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Azure-resources beheren vanuit een Azure Automation-runbook

U kunt Azure-resources beheren vanuit Azure Automation runbooks met behulp van de referentie-Asset. Hieronder ziet u een voor beeld van een Power shell-runbook waarmee de referentie-Asset wordt verzameld die moet worden gebruikt voor het stoppen en starten van virtuele machines in een Azure-abonnement. Dit runbook wordt eerst gebruikt `Get-AutomationPSCredential` om de referentie op te halen die moet worden gebruikt om te verifiëren bij Azure. Vervolgens wordt de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) aangeroepen om verbinding te maken met Azure met behulp van de referentie. Het script maakt gebruik van de [Select-abonnement-](/powershell/module/servicemanagement/azure.service/select-azuresubscription) cmdlet om het abonnement te kiezen waarmee u wilt werken. 

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

* Zie [referenties beheren in azure Automation](shared-resources/credentials.md)voor meer informatie over het gebruik van referenties.
* Zie [modules beheren in azure Automation](shared-resources/modules.md)voor meer informatie over modules.
* Als u een runbook moet starten, raadpleegt u [een Runbook starten in azure Automation](start-runbooks.md).
* Zie Power shell- [documenten](/powershell/scripting/overview)voor meer informatie over Power shell.
