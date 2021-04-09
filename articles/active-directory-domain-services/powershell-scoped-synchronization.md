---
title: Scoped synchronisatie met Power shell voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over hoe u met Azure AD Power shell scoped Synchronization van Azure AD configureert in een door Azure Active Directory Domain Services beheerd domein
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: f877a631fd3c89d74b9e3b47cf205bbcf173ebc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453407"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Scoped Synchronization from Azure AD configureren voor Azure Active Directory Domain Services met behulp van Azure AD Power shell

Azure Active Directory Domain Services (Azure AD DS) om verificatie services te bieden, synchroniseert gebruikers en groepen van Azure AD. In een hybride omgeving kunnen gebruikers en groepen van een on-premises Active Directory Domain Services-omgeving (AD DS) eerst worden gesynchroniseerd met Azure AD met behulp van Azure AD Connect en vervolgens worden gesynchroniseerd met Azure AD DS.

Standaard worden alle gebruikers en groepen van een Azure AD-Directory gesynchroniseerd met een door Azure AD DS beheerd domein. Als u specifieke vereisten hebt, kunt u in plaats daarvan ervoor kiezen om alleen een gedefinieerde set gebruikers te synchroniseren.

In dit artikel wordt beschreven hoe u een beheerd domein maakt dat gebruikmaakt van een synchronisatie met een scope en vervolgens de set gebruikers met Azure AD Power shell wijzigt of uitschakelt. U kunt [deze stappen ook uitvoeren met behulp van de Azure Portal][scoped-sync].

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een Azure Active Directory Domain Services beheerd domein][tutorial-create-instance].
* U hebt *globale beheerders* bevoegdheden nodig in uw Azure AD-Tenant om het Azure AD DS-synchronisatie bereik te wijzigen.

## <a name="scoped-synchronization-overview"></a>Overzicht van scoped Synchronization

Alle gebruikers en groepen uit een Azure AD-adres lijst worden standaard gesynchroniseerd met een beheerd domein. Als slechts een paar gebruikers toegang nodig hebben tot het beheerde domein, kunt u alleen die gebruikers accounts synchroniseren. Deze synchronisatie met een bereik is gebaseerd op een groep. Wanneer u synchronisatie op basis van een groeps bereik configureert, worden alleen de gebruikers accounts die deel uitmaken van de groepen die u opgeeft gesynchroniseerd met het beheerde domein. Geneste groepen worden niet gesynchroniseerd, alleen de specifieke groepen die u selecteert.

U kunt het synchronisatie bereik wijzigen vóór of na het maken van het beheerde domein. Het synchronisatie bereik wordt gedefinieerd door een service-principal met de toepassings-id 2565bd9d-DA50-47d4-8b85-4c97f669dc36. Om te voor komen dat het bereik verloren gaat, moet u de Service-Principal niet verwijderen of wijzigen. Als deze per ongeluk is verwijderd, kan het synchronisatie bereik niet worden hersteld. 

Houd bij het wijzigen van het synchronisatie bereik de volgende voor behoud:

- Er wordt een volledige synchronisatie uitgevoerd.
- Objecten die niet meer in het beheerde domein zijn vereist, worden verwijderd. Nieuwe objecten worden gemaakt in het beheerde domein.

Zie voor meer informatie over het synchronisatie proces [begrijpen synchronisatie in azure AD Domain Services][concepts-sync].

## <a name="powershell-script-for-scoped-synchronization"></a>Power shell-script voor synchronisatie met scopes

Als u synchronisatie met een scope wilt configureren met behulp van Power shell, moet u eerst het volgende script opslaan in een bestand met de naam `Select-GroupsToSync.ps1` .

Met dit script wordt Azure AD DS zodanig geconfigureerd dat geselecteerde groepen vanuit Azure AD worden gesynchroniseerd. Alle gebruikers accounts die deel uitmaken van de opgegeven groepen, worden gesynchroniseerd met het beheerde domein.

Dit script wordt gebruikt in de extra stappen in dit artikel.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>Scoped synchronisatie inschakelen

Voer de volgende stappen uit om synchronisatie op basis van een groep in te scha kelen voor een beheerd domein:

1. Stel eerst *"filteredSync" = "enabled* " in op de Azure AD DS-resource en werk vervolgens het beheerde domein bij.

    Geef desgevraagd de referenties op voor een *globale beheerder* om u aan te melden bij uw Azure AD-Tenant met de cmdlet [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Geef nu de lijst met groepen op waarvan gebruikers moeten worden gesynchroniseerd met het beheerde domein.

    Voer het `Select-GroupsToSync.ps1` script uit en geef de lijst met groepen op die moeten worden gesynchroniseerd. In het volgende voor beeld zijn de groepen die moeten worden gesynchroniseerd *GroupName1* en *GroupName2*.

    > [!WARNING]
    > U moet de groep *Aad DC-Administrators* opnemen in de lijst met groepen voor synchronisatie met een bereik. Als u deze groep niet opneemt, is het beheerde domein onbruikbaar.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Het wijzigen van het bereik van de synchronisatie zorgt ervoor dat het beheerde domein alle gegevens opnieuw synchroniseert. Objecten die niet meer nodig zijn in het beheerde domein, worden verwijderd en het kan enige tijd duren voordat de synchronisatie is voltooid.

## <a name="modify-scoped-synchronization"></a>Scoped synchronisatie wijzigen

Als u de lijst met groepen wilt wijzigen waarvan gebruikers moeten worden gesynchroniseerd met het beheerde domein, voert u `Select-GroupsToSync.ps1` het script uit en geeft u de nieuwe lijst met groepen op die u wilt synchroniseren.

In het volgende voor beeld bevat de groepen die worden gesynchroniseerd niet langer *GroupName2* en bevat nu *GroupName3*.

> [!WARNING]
> U moet de groep *Aad DC-Administrators* opnemen in de lijst met groepen voor synchronisatie met een bereik. Als u deze groep niet opneemt, is het beheerde domein onbruikbaar.

Geef desgevraagd de referenties op voor een *globale beheerder* om u aan te melden bij uw Azure AD-Tenant met de cmdlet [Connect-AzureAD][Connect-AzureAD] :

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Het wijzigen van het bereik van de synchronisatie zorgt ervoor dat het beheerde domein alle gegevens opnieuw synchroniseert. Objecten die niet meer nodig zijn in het beheerde domein, worden verwijderd en het kan enige tijd duren voordat de synchronisatie is voltooid.

## <a name="disable-scoped-synchronization"></a>Scoped synchronisatie uitschakelen

Als u synchronisatie op basis van een groep wilt uitschakelen voor een beheerd domein, stelt u *"filteredSync" = "uitgeschakeld"* in op de Azure AD DS-resource en werkt u het beheerde domein bij. Wanneer dit is voltooid, worden alle gebruikers en groepen ingesteld op synchroniseren vanuit Azure AD.

Geef desgevraagd de referenties op voor een *globale beheerder* om u aan te melden bij uw Azure AD-Tenant met de cmdlet [Connect-AzureAD][Connect-AzureAD] :

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Het wijzigen van het bereik van de synchronisatie zorgt ervoor dat het beheerde domein alle gegevens opnieuw synchroniseert. Objecten die niet meer nodig zijn in het beheerde domein, worden verwijderd en het kan enige tijd duren voordat de synchronisatie is voltooid.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het synchronisatie proces [begrijpen synchronisatie in azure AD Domain Services](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
