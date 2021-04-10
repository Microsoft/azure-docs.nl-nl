---
title: Een gedeelde zelf-hostende Integration runtime maken met Power shell
description: Meer informatie over het maken van een gedeelde zelf-hostende Integration runtime in Azure Data Factory, zodat meerdere gegevens fabrieken toegang hebben tot de Integration runtime.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 16feeb124859fa6199303d9d590fa0a286033ef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389430"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Een gedeelde zelf-hostende Integration runtime maken in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze hand leiding wordt beschreven hoe u een gedeelde zelf-hostende Integration runtime maakt in Azure Data Factory. Vervolgens kunt u de gedeelde zelf-hostende Integration runtime in een andere data factory gebruiken.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Een gedeelde zelf-hostende Integration runtime maken in Azure Data Factory

U kunt een bestaande zelf-hostende Integration runtime-infra structuur die u al in een data factory hebt ingesteld, opnieuw gebruiken. Met dit hergebruik kunt u een gekoppelde zelf-hostende Integration runtime in een andere data factory maken door te verwijzen naar een bestaande gedeelde zelf-hostende IR.

Bekijk de volgende video van 12 minuten om een inleiding en demonstratie van deze functie weer te geven:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologie

- **Gedeelde IR**: een originele zelf-hostende IR die wordt uitgevoerd op een fysieke infra structuur.  
- **Gekoppelde IR**: een IR die verwijst naar een andere gedeelde IR. De gekoppelde IR is een logische IR en maakt gebruik van de infra structuur van een andere gedeelde zelf-hostende IR.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Een gedeelde zelf-hostende IR maken met Azure Data Factory gebruikers interface

Als u een gedeelde zelf-hostende IR wilt maken met behulp van Azure Data Factory gebruikers interface, kunt u de volgende stappen uitvoeren:

1. In de zelf-hostende IR die moet worden gedeeld, selecteert u **machtiging verlenen aan een andere Data Factory** en selecteert u op de pagina Integration runtime Setup de Data Factory waarin u de gekoppelde IR wilt maken.
      
    ![Knop voor het verlenen van machtigingen op het tabblad delen](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Noteer en kopieer de bron-ID van de zelf-hostende IR die u wilt delen.
         
3. Maak in de data factory waarvoor de machtigingen zijn verleend, een nieuwe zelf-hostende IR (gekoppeld) en voer de resource-ID in.
      
    ![Knop voor het maken van een zelf-hostende Integration runtime](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Knop voor het maken van een gekoppelde zelf-hostende Integration runtime](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Vakken voor naam en Resource-ID](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Een gedeelde zelf-hostende IR maken met behulp van Azure PowerShell

Als u een gedeelde zelf-hostende IR wilt maken met behulp van Azure PowerShell, kunt u de volgende stappen uitvoeren: 
1. Een data factory maken. 
1. Een zelf-hostende Integration Runtime maken.
1. Deel de zelf-hostende Integration runtime met andere gegevens fabrieken.
1. Maak een gekoppelde Integration runtime.
1. Trek het delen in.

### <a name="prerequisites"></a>Vereisten 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-abonnement**. Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. 

- **Azure PowerShell**. Volg de instructies in [Install Azure PowerShell in Windows met PowerShellGet](/powershell/azure/install-az-ps). U kunt Power shell gebruiken om een script uit te voeren om een zelf-hostende Integration runtime te maken die kan worden gedeeld met andere gegevens fabrieken. 

> [!NOTE]  
> Voor een lijst met Azure-regio's waarin Data Factory op dit moment beschikbaar is, selecteert u de regio's die u interesseren op de  [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start Windows PowerShell ISE (Integrated Scripting Environment).

1. Variabelen maken. Kopieer en plak het volgende script. Vervang de variabelen, zoals **subscriptionname** en **ResourceGroupName**, door de werkelijke waarden: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Meld u aan en selecteer een abonnement. Voeg de volgende code toe aan het script om u aan te melden en uw Azure-abonnement te selecteren:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Maak een resource groep en een data factory.

    > [!NOTE]  
    > Deze stap is optioneel. Als u al een data factory hebt, kunt u deze stap overs Laan. 

    Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd. In het volgende voor beeld wordt een resource groep gemaakt `myResourceGroup` met de naam op de locatie Europa West: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Voer de volgende opdracht uit om een data factory te maken: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Een zelf-hostende Integration Runtime maken

> [!NOTE]  
> Deze stap is optioneel. Als u de zelf-hostende Integration runtime die u wilt delen met andere gegevens fabrieken al hebt, kunt u deze stap overs Laan.

Voer de volgende opdracht uit om een zelf-hostende Integration runtime te maken:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>De verificatie sleutel voor Integration runtime ophalen en een knoop punt registreren

Voer de volgende opdracht uit om de verificatie sleutel voor de zelf-hostende Integration runtime op te halen:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

Het antwoord bevat de verificatie sleutel voor deze zelf-hostende Integration runtime. U gebruikt deze sleutel bij het registreren van het knoop punt voor Integration runtime.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>De zelf-hostende Integration runtime installeren en registreren

1. Down load het installatie programma voor de zelf-hostende Integration runtime van [Azure Data Factory Integration runtime](https://aka.ms/dmg).

2. Voer het installatie programma uit om de zelf-hostende integratie op een lokale computer te installeren.

3. Registreer de nieuwe zelf-hostende integratie met de verificatie sleutel die u in een vorige stap hebt opgehaald.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>De zelf-hostende Integration runtime delen met een andere data factory

#### <a name="create-another-data-factory"></a>Nog een data factory maken

> [!NOTE]  
> Deze stap is optioneel. Als u al de data factory hebt waarmee u wilt delen, slaat u deze stap over. Maar om roltoewijzingen aan andere data factory toe te voegen of te verwijderen, moet u `Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` machtigingen hebben, zoals beheerder of [eigenaar](../role-based-access-control/built-in-roles.md#owner)van de [gebruikers toegang](../role-based-access-control/built-in-roles.md#user-access-administrator) .

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Machtiging verlenen

Ken toestemming toe voor de data factory die toegang nodig heeft tot de zelf-hostende Integration runtime die u hebt gemaakt en geregistreerd.

> [!IMPORTANT]  
> Sla deze stap niet over.

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Een gekoppelde zelf-hostende Integration runtime maken

Voer de volgende opdracht uit om een gekoppelde zelf-hostende Integration runtime te maken:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

U kunt deze gekoppelde integratie-runtime nu gebruiken in elke gekoppelde service. De gekoppelde Integration runtime gebruikt de Shared Integration runtime om activiteiten uit te voeren.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Het delen van de integratie-runtime intrekken vanuit een data factory

Voer de volgende opdracht uit om de toegang van een data factory van de Shared Integration runtime in te trekken:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Als u de bestaande gekoppelde Integration runtime wilt verwijderen, voert u de volgende opdracht uit op de Shared Integration runtime:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="monitoring"></a>Bewaking

#### <a name="shared-ir"></a>Gedeelde IR

![Selecties voor het vinden van een gedeelde Integration runtime](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Een gedeelde integratie-runtime bewaken](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Gekoppelde IR

![Selecties voor het vinden van een gekoppelde Integration runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Een gekoppelde integratie-runtime bewaken](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)


### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Bekende beperkingen van zelf-hostende IR-delen

* De data factory waarin een gekoppelde IR wordt gemaakt, moet een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md)hebben. Standaard hebben de gegevens fabrieken die zijn gemaakt in de Azure Portal-of Power shell-cmdlets een impliciet gemaakte beheerde identiteit. Maar wanneer een data factory wordt gemaakt via een Azure Resource Manager sjabloon of SDK, moet u de **identiteits** eigenschap expliciet instellen. Deze instelling zorgt ervoor dat Resource Manager een data factory maakt die een beheerde identiteit bevat.

* De Data Factory .NET SDK die deze functie ondersteunt, moet versie 1.1.0 of hoger zijn.

* Als u machtigingen wilt verlenen, moet u de rol eigenaar of de overgenomen rol eigenaar hebben in de data factory waar de gedeelde IR zich bevindt.

* De functie voor delen werkt alleen voor gegevens fabrieken binnen dezelfde Azure AD-Tenant.

* Voor Azure AD- [gast gebruikers](../active-directory/governance/manage-guest-access-with-access-reviews.md), de zoek functie in de gebruikers interface, met alle gegevens fabrieken met behulp van een zoek woord, werkt niet. Maar zolang de gast gebruiker de eigenaar van het data factory is, kunt u de IR delen zonder de zoek functionaliteit. Voor de beheerde identiteit van de data factory die de IR moet delen, voert u die beheerde identiteit in het vak **machtiging toewijzen** in en selecteert u **toevoegen** in de Data Factory gebruikers interface.

  > [!NOTE]
  > Deze functie is alleen beschikbaar in Data Factory v2.


### <a name="next-steps"></a>Volgende stappen

- Bekijk de [concepten van de integratie-runtime in azure Data Factory](./concepts-integration-runtime.md).

- Meer informatie over [het maken van een zelf-hostende Integration runtime in de Azure Portal](./create-self-hosted-integration-runtime.md).