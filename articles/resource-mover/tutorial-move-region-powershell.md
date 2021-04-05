---
title: Resources verplaatsen tussen regio's met behulp van Power shell in azure resource verhuizer
description: Meer informatie over het verplaatsen van resources over regio's met behulp van Power shell in azure resource verhuizer.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/21/2021
ms.author: raynew
ms.openlocfilehash: b728170521570ff0d83b67671109e82adb7fa7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584345"
---
# <a name="move-resources-across-regions-in-powershell"></a>Resources verplaatsen tussen regio's in Power shell

In dit artikel wordt uitgelegd hoe u Azure-resources naar een andere Azure-regio kunt verplaatsen met behulp van Power shell in [Azure resource verhuizer](overview.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De vereisten controleren.
> * Stel de verzameling verplaatsen in.
> * Resources toevoegen aan de verzameling verplaatsen en afhankelijkheden oplossen.
> * De bronresourcegroep voorbereiden en verplaatsen. 
> * De overige resources voorbereiden en verplaatsen.
> * Beslissen of u de verplaatsing wilt doorvoeren of niet. 
> * Desgewenst resources in de bronregio verwijderen na de verplaatsing.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint. Meld u vervolgens aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten
**Vereiste** | **Beschrijving**
--- | ---
**Abonnements machtigingen** | Controleer of u toegang hebt tot de *eigenaar* van het abonnement dat de resources bevat die u wilt verplaatsen<br/><br/> **Waarom heb ik de eigenaar toegang nodig?** De eerste keer dat u een resource toevoegt voor een specifiek bron- en doelpaar in een Azure-abonnement maakt Resource Mover een [door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (vroeger MSI genoemd (Managed Service Identity)) die door het abonnement wordt vertrouwd. Om de identiteit te maken en deze de juiste rol toe te wijzen (Inzender of Administrator voor gebruikerstoegang in het bronabonnement), moet het account dat u gebruikt om resources toe te voegen *Eigenaars* machtigingen hebben voor het abonnement. [Meer informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over rollen in Azure.
**Ondersteuning voor resource-toedrijfing** | [Bekijk](common-questions.md) ondersteunde regio's en andere veelgestelde vragen.
**VM-ondersteuning** |  Controleer of de Vm's die u wilt verplaatsen, worden ondersteund.<br/><br/> - [Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) de ondersteunde Windows-vm's.<br/><br/> - [Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde vm's en kernel-versies van Linux.<br/><br/> -Ondersteunde instellingen voor [Compute](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) controle.
**SQL-ondersteuning** | Als u SQL-resources wilt verplaatsen, raadpleegt u de [lijst met SQL-vereisten](tutorial-move-region-sql.md#check-sql-requirements).
**Doel abonnement** | Het abonnement in de doel regio moet voldoende quota hebben om de resources te maken die u verplaatst in de doel regio. Als de quota onvoldoende zijn, moet u [hogere limieten aanvragen](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Kosten van de doel regio** | Verifieer prijzen en kosten voor de doelregio waarnaar u virtuele machines verplaatst. Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om u daarbij te helpen.

### <a name="review-powershell-requirements"></a>Power shell-vereisten controleren

De meeste bewerkingen voor het verplaatsen van resources zijn hetzelfde, ongeacht of u de Azure Portal of Power shell gebruikt, met een paar uitzonde ringen.

**Bewerking** | **PowerShell** | **Portal**
--- | --- | ---
**Een verzameling voor verplaatsen maken** | Er wordt automatisch een verzameling voor verplaatsen (een lijst met alle resources die u verplaatst) gemaakt. Vereiste identiteits machtigingen worden toegewezen in de back-end door de portal. | U kunt Power shell-cmdlets gebruiken voor het volgende:<br/><br/> -Maak een resource groep voor de verzameling verplaatsen en geef de locatie hiervoor op.<br/><br/> -Een beheerde identiteit toewijzen aan de verzameling.<br/><br/> -Resources toevoegen aan de verzameling.
**Een verzameling verplaatsen verwijderen** | U kunt een verzameling verplaatsen niet rechtstreeks verwijderen in de portal. | U gebruikt een Power shell-cmdlet om een verzameling verplaatsen te verwijderen.
**Resources verplaatsen**<br/><br/> (Voorbereiden, verplaatsen, door voeren, enz.).| Enkele stappen met automatische validatie door middel van resource verplaatsen. | Power shell-cmdlets voor:<br/><br/> 1) afhankelijkheden valideren.<br/><br/> 2) de verplaatsing wordt uitgevoerd.
**Bron resources verwijderen** | Rechtstreeks in de resource-verhuizer Portal. | Power shell-cmdlets op het niveau van het resource type.


### <a name="sample-values"></a>Voorbeeldwaarden

We gebruiken deze waarden in onze script voorbeelden:

**Instelling** | **Waarde** 
--- | ---
Abonnements-id | abonnement-id
Bron regio |  VS - centraal
Doel regio | VS - west-centraal
Resource groep (meta gegevens voor de verzameling verplaatsen) | RG-MoveCollection-demoRMS
Verzamelings naam verplaatsen | PS-centraal-westcentralus-demoRMS
Resource groep (bron regio) | PSDemoRM 
Resource groep (doel regio) | PSDemoRM-doel
Service locatie van resource verplaatsing | VS - oost 2
Identity type | SystemAssigned
Te verplaatsen VM | PSDemoVM


## <a name="sign-into-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de cmdlet Connect-AzAccount:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>De verzameling verplaatsen instellen

Het MoveCollection-object slaat meta gegevens en configuratie gegevens op over resources die u wilt verplaatsen. Ga als volgt te werk om een verzameling voor verplaatsen in te stellen:

- Maak een resource groep voor de verzameling voor verplaatsen.
- Registreer de service provider bij het abonnement, zodat de MoveCollection-resource kan worden gemaakt.
- Maak het MoveCollection-object met beheerde identiteit. Voor het MoveCollection-object om toegang te krijgen tot het abonnement waarin de resource-overschakeling service zich bevindt, heeft het een door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (voorheen de Managed Service Identity (MSI)) die wordt vertrouwd door het abonnement.
- Verleen toegang tot het resource-verhuis abonnement voor de beheerde identiteit.

### <a name="create-the-resource-group"></a>De resourcegroep maken

Maak als volgt een resource groep voor de meta gegevens van de verzameling en configuratie-informatie:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Uitvoer**:

![Tekst uitvoeren na maken van resource groep](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>De resourceprovider registreren

1. Registreer de resource provider micro soft. migrate, zodat de MoveCollection-resource als volgt kan worden gemaakt:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>Een MoveCollection-object maken

Maak als volgt een MoveCollection-object en wijs er een beheerde identiteit aan toe: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Uitvoer**:

![Uitvoer tekst na maken van een verzameling voor verplaatsen](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Toegang verlenen tot de beheerde identiteit

Verleen de beheerde identiteit toegang tot het resource-verdrijfings abonnement als volgt. U moet de eigenaar van het abonnement zijn.

1. Haal identiteits gegevens op uit het MoveCollection-object.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Wijs de vereiste rollen toe aan de identiteit, zodat Azure resource-overschakeling toegang heeft tot uw abonnement om resources te kunnen verplaatsen.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Resources toevoegen aan de verzameling verplaatsen

Haal de Id's op voor de bestaande bron resources die u wilt verplaatsen. Maak het doel resource-instellingen object en Voeg resources toe aan de verzameling voor verplaatsen.

> [!NOTE]
> Resources die zijn toegevoegd aan een verplaatsingsverzameling moeten zich in hetzelfde abonnement bevinden, maar kunnen zich in verschillende resourcegroepen bevindt.

Voeg resources als volgt toe:

1. De bron Resource-ID ophalen:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Uitvoer**

    ![Uitvoer tekst na het ophalen van de resource-ID](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Maak het doel bron instellingen object in overeenstemming met de resource die u wilt verplaatsen. In ons geval is het een virtuele machine.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Stel het resource type en de doel resource naam voor het object in.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > De doel-VM heeft dezelfde naam als de virtuele machine in de bron regio. U kunt een andere naam kiezen.

4. Voeg de bron resources toe aan de verzameling verplaatsen met behulp van de resource-ID en het doel instellingen object dat u hebt opgehaald/gemaakt.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Uitvoer** ![ Uitvoer tekst na toevoegen van de resource](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Afhankelijkheden valideren en toevoegen

Controleer of de resources die u hebt toegevoegd afhankelijk zijn van andere resources en voeg deze indien nodig toe. 

1. U valideert afhankelijkheden als volgt:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Uitvoer (als er afhankelijkheden bestaan)**

    ![Uitvoer tekst na validatie van afhankelijkheden](./media/tutorial-move-region-powershell/dependency-output.png)

2. Ontbrekende afhankelijkheden voor identiteit:

    - Een lijst met alle ontbrekende afhankelijkheden ophalen:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant"
        ```
        **Uitvoer** ![ Uitvoer tekst na het ophalen van een lijst met alle afhankelijkheden](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - Alleen afhankelijkheden van het eerste niveau ophalen (directe afhankelijkheden voor de resource):

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Uitvoer** ![ Uitvoer tekst na het ophalen van een lijst met afhankelijkheden van het eerste niveau](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Als u openstaande afhankelijkheden wilt toevoegen, herhaalt u de bovenstaande instructies om [resources toe te voegen aan de verzameling verplaatsen](#add-resources-to-the-move-collection)en opnieuw te valideren totdat er geen openstaande resources zijn.

> [!NOTE]
> Als u resources uit de resource verzameling wilt verwijderen, volgt u de instructies in [dit artikel](remove-move-resources.md).

## <a name="add-the-source-resource-group"></a>De bron resource groep toevoegen

Voeg de bron resource groep toe die resources bevat die u wilt verplaatsen naar de verzameling verplaatsen.

1. Haal de ID van de resource groep op.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Uitvoer** ![ Uitvoer tekst na het ophalen van de ID van de bron resource groep](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > We gebruiken een resource groep die al deel uitmaakt van de doel regio.

 
2. Gebruik de opgehaalde ID om de resource groep toe te voegen aan de verzameling.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Uitvoer** ![ Uitvoer tekst na toevoegen van de bron resource groep aan de verzameling verplaatsen](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Controleer afhankelijkheden om er zeker van te zijn dat u niets hebt gemist na het toevoegen van de resource groep.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Er worden geen openstaande afhankelijkheden weer geven.

    **Uitvoer** ![ Uitvoer tekst na het controleren van afhankelijkheden](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Resources voorbereiden

Normaal gesp roken moet u resources voorbereiden in de bron regio vóór de verplaatsing. Bijvoorbeeld:

- Als u stateless resources, zoals virtuele netwerken van Azure, netwerk adapters, load balancers en netwerk beveiligings groepen, wilt verplaatsen, moet u mogelijk een Azure Resource Manager sjabloon exporteren.
- Als u stateful resources zoals Azure-Vm's en SQL-data bases wilt verplaatsen, moet u mogelijk bronnen repliceren van de bron naar de doel regio.

In deze zelf studie, omdat we Vm's verplaatsen, moeten we de bron resource groep voorbereiden en vervolgens de verplaatsing initiëren en door voeren, voordat we Vm's kunnen voorbereiden.

> [!NOTE]
> Als u een bestaande doel resource groep hebt, kunt u de verplaatsing rechtstreeks door voeren voor de bron resource groep en de fases voor het voorbereiden en het verplaatsen van een verhuizing overs Laan.

  
### <a name="prepare-the-source-resource-group"></a>De bron resource groep voorbereiden:

1. De resource groep voorbereiden:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Uitvoer**

    ![Uitvoer tekst na het voorbereiden van de bron resource groep](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Start de verplaatsing van de bron resource groep.

    ```azurepowershell-interactive
    "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Uitvoer tekst na het initiëren van de verplaatsing van de bron resource groep](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Voer de verplaatsing voor de bron resource groep door.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Uitvoer**

    ![Uitvoer tekst na het door voeren van de bron resource groep](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>VM-resources voorbereiden

Nadat u de bron groep hebt voor bereid en verplaatst, kunnen we VM-resources voorbereiden voor de verplaatsing.

1. Valideer de afhankelijkheden voordat u VM-resources voorbereidt.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Uitvoer**

    ![Uitvoer tekst na validatie van de virtuele machine voordat deze wordt voor bereid](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Haal de afhankelijke resources op die moeten worden voor bereid samen met de virtuele machine.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Uitvoer**

    ![Uitvoer tekst na het ophalen van afhankelijke VM-resources](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Start het voorbereidings proces voor alle afhankelijke resources.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Uitvoer**

    ![Uitvoer tekst na initating voorbereiden van alle resources](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > U kunt de bron Resource-ID opgeven in plaats van de resource naam als de invoer parameters voor de voorbereidings-cmdlet, maar ook in de cmdlets voor verplaatsen en door voeren. Voer hiervoor het volgende uit:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>Verplaatsing van VM-resources initiëren

1. Controleer of de VM-resources zich in de status *initiated verplaatsen in behandeling* bevinden:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Uitvoer**

    ![Uitvoer tekst na het controleren van de status initiatie](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. De verplaatsing initiëren:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Uitvoer**

    ![Uitvoer tekst na het initiëren van de verplaatsing van resources](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Verwijderen of doorvoeren?

Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt doorvoeren of verwijderen. 

- **Verwijderen**: Mogelijk wilt u een verplaatsing verwijderen als u een test uitvoert en de bronresource niet echt wilt verplaatsen. Als u de verplaatsing negeert, wordt de resource teruggezet in de status *Initiëren verplaatsing in behandeling*. U kunt de verplaatsing vervolgens opnieuw starten als dat nodig is.
- **Doorvoeren**: Met doorvoeren wordt de verplaatsing naar de doelregio voltooid. Na het doorvoeren heeft een bronresource de status *Verwijderen bron in behandeling* en kunt u besluiten of u deze wilt verwijderen.

### <a name="discard-the-move"></a>De verplaatsing verwijderen

De verplaatsing negeren:

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Uitvoer**

![Uitvoer tekst nadat de verplaatsing is verwijderd](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>De verplaatsing doorvoeren

1. Voer de volgende stappen uit:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Uitvoer**

    ![Uitvoer tekst na het door voeren van de verplaatsing](./media/tutorial-move-region-powershell/commit-move.png)

2. Controleer of alle resources zijn verplaatst naar de doel regio:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Alle resources bevinden zich nu in de status *verwijderings bron in behandeling* in de doel regio.

## <a name="delete-source-resources"></a>Bron resources verwijderen

Nadat de verplaatsing is doorgevoerd en u hebt gecontroleerd of de resources werken zoals verwacht in de doel regio, kunt u elke bron resource verwijderen in de [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [met behulp van Power shell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)of [Azure cli](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Virtuele Azure-machines verplaatst naar een andere Azure-regio met behulp van Power shell.
> * De resources die zijn gekoppeld aan VM's naar een andere regio verplaatst.

Probeer nu Azure-Vm's te verplaatsen met behulp van de portal

> [!div class="nextstepaction"]
> [Virtuele Azure-machines verplaatsen in de portal](./tutorial-move-region-virtual-machines.md)


