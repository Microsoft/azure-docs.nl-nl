---
title: Zelfstudie`:` Een beheerde identiteit gebruiken voor toegang tot Azure Resource Manager - Windows - Azure AD
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het krijgen van toegang tot Azure Resource Manager met een door de gebruiker toegewezen beheerde identiteit op een Windows-VM.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43f926f400bf8ddd522bbb5f62509519fa09903d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096600"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Zelfstudie: Toegang krijgen tot Azure Resource Manager met een door de gebruiker toegewezen beheerde identiteit op een Windows-VM

In deze zelfstudie wordt uitgelegd hoe u een door de gebruiker toegewezen identiteit kunt maken, deze toewijzen aan een virtuele Windows-machine (VM) en die identiteit vervolgens gebruiken om toegang te krijgen tot de Azure Resource Manager-API. Beheerde service-identiteiten worden automatisch beheerd door Azure. Ze maken verificatie mogelijk voor services die Azure AD-verificatie ondersteunen, zonder dat er referenties in uw code hoeven te worden ingesloten. 

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen beheerde identiteit maken
> * Uw door de gebruiker toegewezen identiteit toewijzen aan uw Windows-VM
> * De door de gebruiker toegewezen identiteit toegang verlenen tot een resourcegroep in Azure Resource Manager 
> * Een toegangstoken ophalen met behulp van de door de gebruiker toegewezen identiteit en daarmee Azure Resource Manager aanroepen 
> * De eigenschappen van een resourcegroep lezen

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Meld u aan bij Azure Portal](https://portal.azure.com)

- [Een Windows-VM maken](../../virtual-machines/windows/quick-create-portal.md)

- Om de stappen voor het maken van de vereiste resources en het rolbeheer in deze zelfstudie uit te voeren, moet uw account 'Eigenaar'-machtigingen hebben voor het juiste bereik (uw abonnement of resourcegroep). Als u hulp nodig hebt bij roltoewijzing, raadpleegt u [Azure-rollen toewijzen om de toegang tot uw Azure-abonnements resources te beheren](../../role-based-access-control/role-assignments-portal.md).

- Als u de voorbeeldscripts wilt uitvoeren, hebt u twee opties:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md), die u kunt openen met behulp van de knop **Probeer het nu** in de rechterbovenhoek van Code::Blocks.
    - Voer scripts lokaal uit met Azure PowerShell, zoals wordt beschreven in de volgende sectie.

### <a name="configure-azure-powershell-locally"></a>Azure PowerShell lokaal configureren

Als u Azure PowerShell voor dit artikel lokaal wilt gebruiken (in plaats van Cloud Shell), voert u de volgende stappen uit:

1. Installeer de [meest recente versie van Azure PowerShell](/powershell/azure/install-az-ps) als u dat nog niet hebt gedaan.

1. Meld u aan bij Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Installeer de [nieuwste versie van PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Mogelijk moet u `Exit` buiten de huidige PowerShell-sessie nadat u deze opdracht voor de volgende stap hebt uitgevoerd.

1. Installeer de voorlopige versie van de `Az.ManagedServiceIdentity`-module en voer de aan de gebruiker toegewezen identiteitsbewerkingen in dit artikel uit:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>Inschakelen

Voor een scenario dat is gebaseerd op een door de gebruiker toegewezen identiteit, moet u de volgende stappen uitvoeren:

- Een identiteit maken
- De zojuist gemaakte identiteit toewijzen

### <a name="create-identity"></a>Identiteit maken

In deze sectie leert u hoe u een door een gebruiker toegewezen beheerde identiteit kunt maken. Een door de gebruiker toegewezen identiteit wordt als een zelfstandige Azure-resource gemaakt. Met de [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity) wordt een identiteit gemaakt in uw Azure AD-tenant die kan worden toegewezen aan een of meer Azure-service-exemplaren.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Het antwoord bevat details voor de gemaakte door de gebruiker toegewezen identiteit, vergelijkbaar met het volgende voorbeeld. Noteer de waarden voor `Id` en `ClientId` voor de door de gebruiker toegewezen identiteit, aangezien deze worden gebruikt in volgende stappen:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>Identiteit toewijzen

In deze sectie leert u hoe u de door de gebruiker toegewezen identiteit aan een Windows-VM kunt toewijzen Een door de gebruiker toegewezen identiteit kan worden gebruikt door clients op meerdere Azure-resources. Gebruik de volgende opdrachten om de door de gebruiker toegewezen identiteit toe te wijzen aan één VM. Gebruik de eigenschap `Id` die in de vorige stap is geretourneerd voor de parameter `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Toegang verlenen 

In deze sectie leert u hoe u uw door de gebruiker toegewezen identiteit toegang kunt verlenen tot een resourcegroep in Azure Resource Manager. Beheerde identiteiten voor Azure-resources biedt identiteiten die uw code kan gebruiken om toegangstokens aan te vragen voor verificatie bij resource-API's die Azure AD-verificatie ondersteunen. In deze zelfstudie krijgt uw code toegang tot de Azure Resource Manager-API. 

Voordat uw code toegang tot de API kan krijgen, moet u de identiteit toegang geven tot een resource in Azure Resource Manager. In dit geval is dat de resourcegroep waarin de VM zich bevindt. Werk de waarde voor `<SUBSCRIPTION ID>` bij overeenkomstig uw omgeving.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Het antwoord bevat details voor de gemaakte roltoewijzing, vergelijkbaar met het volgende voorbeeld:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>Toegang tot gegevens

### <a name="get-an-access-token"></a>Een toegangstoken opvragen 

Voor de rest van de zelfstudie werkt u op de virtuele machine die we eerder hebben gemaakt.

1. Meld u aan bij Azure Portal op [https://portal.azure.com](https://portal.azure.com)

2. Navigeer in Azure Portal naar **Virtuele machines**, ga naar de virtuele Windows-machine en klik op de pagina **Overzicht** op **Verbinden**.

3. Voer de **gebruikersnaam** en het **wachtwoord** in die u hebt gebruikt bij het maken van de Windows-VM.

4. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u **PowerShell** in de externe sessie.

5. Dien met behulp van `Invoke-WebRequest` van Powershell een aanvraag in bij de lokale beheerde identiteiten om een toegangstoken voor Azure Resource Manager op te halen voor het Azure-resources-eindpunt.  De waarde voor `client_id` is de waarde die wordt geretourneerd wanneer u de door de gebruiker toegewezen beheerde identiteit maakt.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Leeseigenschappen

Gebruik het in de vorige stap opgehaalde toegangstoken om toegang te krijgen tot Azure Resource Manager en lees de eigenschappen van de resourcegroep waarvoor u uw door de gebruiker toegewezen identiteit toegang hebt verleend. Vervang `<SUBSCRIPTION ID>` door de abonnements-id van uw omgeving.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Het antwoord bevat de gegevens van de specifieke resourcegroep, vergelijkbaar met het volgende voorbeeld:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd een door de gebruiker toegewezen identiteit te maken en deze te koppelen aan een virtuele machine in Azure voor toegang tot de Azure Resource Manager-API.  Zie voor meer informatie over Azure Resource Manager:

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
