---
title: Roltoewijzing aan Windows Virtual Desktop (klassiek)-service-principal - Azure
description: Service-principals maken en rollen toewijzen met behulp van PowerShell in Windows Virtual Desktop (klassiek).
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 535ee2dace742a57877c9673e77bee64fd81c456
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445037"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-windows-virtual-desktop-classic"></a>Zelfstudie: Service-principals en roltoewijzingen maken met PowerShell in Windows Virtual Desktop (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten.

Service-principals zijn identiteiten die u in Azure Active Directory kunt maken om rollen en machtigingen voor een bepaald doel toe te wijzen. In Windows Virtual Desktop kunt u een service-principal maken om:

- specifieke Windows Virtual Desktop-beheertaken te automatiseren;
- te gebruiken als referenties in plaats van gebruikers met verplichte MFA (meervoudige verificatie) bij het uitvoeren van een Azure Resource Manager-sjabloon voor Windows Virtual Desktop.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een service-principal maken in Azure Active Directory.
> * Een roltoewijzing maken in Windows Virtual Desktop.
> * Meld u aan bij Windows Virtual Desktop met behulp van de service-principal.

## <a name="prerequisites"></a>Vereisten

Voordat u service-principals en roltoewijzingen kunt maken, moet u drie dingen doen:

1. Installeer de AzureAD-module. U installeert de module door PowerShell als een beheerder te starten en de volgende cmdlet uit te voeren:

    ```powershell
    Install-Module AzureAD
    ```

2. [Download en importeer de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/).

3. Volg alle instructies in dit artikel in dezelfde PowerShell-sessie. Het proces werkt mogelijk niet als u de PowerShell-sessie onderbreekt door het venster te sluiten en later opnieuw te openen.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Een service-principal maken in Azure Active Directory

Nadat u aan de vereisten in uw PowerShell-sessie hebt voldaan, voert u de volgende PowerShell-cmdlets uit om een service-principal voor meerdere tenants in Azure te maken.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Uw referenties weergeven in PowerShell

Voordat u de roltoewijzing voor uw service-principal maakt, bekijkt u uw referenties en noteert u deze voor later. Voor het wachtwoord is belangrijk, want u kunt dit niet ophalen nadat u deze PowerShell-sessie hebt gesloten.

Hier volgen de drie referenties die u moet noteren en de cmdlets die u moet uitvoeren om ze te verkrijgen:

- Wachtwoord:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Tenant-id:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Toepassings-id:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Een roltoewijzing maken in Windows Virtual Desktop

Vervolgens moet u een roltoewijzing maken zodat de service-principal zich kan aanmelden bij Windows Virtual Desktop. Zorg ervoor dat u zich aanmeldt met een account dat machtigingen heeft om roltoewijzingen te maken.

Eerst [downloadt en importeert u de Windows Virtual Desktop PowerShell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw PowerShell-sessie als u dat nog niet hebt gedaan.

Voer de volgende PowerShell-cmdlets uit om verbinding te maken met Windows Virtual Desktop en uw tenants weer te geven.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

Wanneer u de naam vindt van de tenant waarvoor u een roltoewijzing wilt maken, gebruikt u die naam in de volgende cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Aanmelden met de service-principal

Nadat u een roltoewijzing voor de service-principal hebt gemaakt, voert u de volgende cmdlet uit om te controleren of de service-principal zich kan aanmelden bij Windows Virtual Desktop:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

Nadat u zich hebt aangemeld, controleert u of alles werkt door enkele Windows Virtual Desktop PowerShell-cmdlets met de service-principal te testen.

## <a name="next-steps"></a>Volgende stappen

Nadat u de service-principal hebt gemaakt en hieraan een rol hebt toegewezen in uw Windows Virtual Desktop-tenant, kunt u deze gebruiken om een hostgroep te maken. Voor meer informatie over hostgroepen gaat u verder met de zelfstudie over het maken van een hostgroep in Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Een hostpool maken met Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
