---
title: Multi-VM-omgevingen en PaaS-resources maken met sjablonen
description: Meer informatie over het maken van omgevingen met meerdere VM'S en PaaS resources in Azure DevTest Labs vanuit een Azure Resource Manager sjabloon
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 97659d4ab95fdbe75460161d0ceed71a1cb5cf82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88182405"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen

Met Azure DevTest Labs omgevingen kunnen gebruikers eenvoudig complexe infra structuren implementeren op een consistente manier binnen de verfijning van het lab. U kunt [Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md) gebruiken om omgevingen te maken met sets resources in DevTest Labs. Deze omgevingen kunnen alle Azure-resources bevatten die resource manager-sjablonen kunnen maken.

U kunt eenvoudig [één virtuele machine (VM) tegelijk](devtest-lab-add-vm.md) aan een Lab toevoegen met behulp van de [Azure Portal](https://portal.azure.com). Scenario's zoals multi-tier web-apps of een share point-Farm hebben echter een mechanisme nodig om meerdere Vm's in één stap te maken. Met behulp van Azure Resource Manager sjablonen kunt u de infra structuur en configuratie van uw Azure-oplossing definiëren en herhaaldelijk meerdere Vm's implementeren met een consistente status.

Azure Resource Manager sjablonen bieden ook de volgende voor delen:

- Azure Resource Manager sjablonen worden direct vanuit uw GitHub of Azure opslag plaatsen resource control-opslag plaats geladen.
- Uw gebruikers kunnen een omgeving maken door een geconfigureerde Azure Resource Manager-sjabloon uit de Azure Portal te kiezen, net als bij andere typen [VM](devtest-lab-comparing-vm-base-image-types.md)-basis.
- U kunt zowel Azure PaaS-resources inrichten als IaaS Vm's in een omgeving vanuit een Azure Resource Manager sjabloon.
- U kunt de kosten van omgevingen in het lab volgen, naast de afzonderlijke Vm's die door andere soorten bases zijn gemaakt. PaaS-resources worden gemaakt en worden weer gegeven in het bijhouden van kosten. Automatisch afsluiten van VM'S is echter niet van toepassing op PaaS-resources.

Zie [voor delen van het gebruik van Resource Manager-sjablonen](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)voor meer informatie over de voor delen van het gebruik van Resource Manager-sjablonen voor het implementeren, bijwerken of verwijderen van veel lab-resources in één bewerking.

> [!NOTE]
> Wanneer u een resource manager-sjabloon gebruikt als basis voor het maken van Lab-Vm's, zijn er enkele verschillen tussen het maken van meerdere Vm's of één virtuele machine. Zie [de Azure Resource Manager-sjabloon van een virtuele machine gebruiken](devtest-lab-use-resource-manager-template.md)voor meer informatie.
>

## <a name="use-devtest-labs-public-environments"></a>Open bare omgevingen van DevTest Labs gebruiken
Azure DevTest Labs heeft een [open bare opslag plaats van Azure Resource Manager sjablonen](https://github.com/Azure/azure-devtestlab/tree/master/Environments) die u kunt gebruiken om omgevingen te maken zonder zelf verbinding te hoeven maken met een externe github-bron. Deze open bare opslag plaats is vergelijkbaar met de open bare opslag plaats van artefacten die beschikbaar zijn in de Azure Portal voor elk lab dat u maakt. Met de opslag plaats omgeving kunt u snel aan de slag met vooraf gemaakte omgevings sjablonen met enkele invoer parameters. Deze sjablonen bieden u de mogelijkheid om aan de slag te gaan met PaaS-resources binnen Labs.

In de open bare opslag plaats hebben het DevTest Labs-team en andere veelgebruikte sjablonen gemaakt en gedeeld, zoals Azure Web Apps, Service Fabric cluster en een ontwikkel-Farm van share point. U kunt deze sjablonen rechtstreeks gebruiken of aanpassen aan uw behoeften. Zie [open bare omgevingen in DevTest Labs configureren en gebruiken](devtest-lab-configure-use-public-environments.md)voor meer informatie. Nadat u uw eigen sjablonen hebt gemaakt, kunt u deze opslaan in deze opslag plaats om ze met anderen te delen of uw eigen Git-opslag plaats in te stellen.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Uw eigen sjabloon opslagplaatsen maken

Als een van de best practices met infrastructuur codes en configuratie-as-code, moet u omgevings sjablonen beheren in broncode beheer. Azure DevTest Labs volgt deze procedure en laadt alle Azure Resource Manager sjablonen rechtstreeks vanuit uw GitHub-of Azure opslag plaatsen-opslag plaatsen. Als gevolg hiervan kunt u Resource Manager-sjablonen gebruiken in de volledige release cyclus, van de test omgeving naar de productie omgeving.

Er zijn verschillende regels die moeten worden gevolgd om uw Azure Resource Manager sjablonen in een opslag plaats in te delen:

- U moet de naam van het hoofd sjabloon bestand *azuredeploy.jsop*.

- Als u parameter waarden wilt gebruiken die zijn gedefinieerd in een parameter bestand, moet het parameter bestand de naam *azuredeploy.parameters.jsaan* hebben.

  U kunt de para meters gebruiken `_artifactsLocation` en `_artifactsLocationSasToken` de parametersLink URI-waarde maken, zodat DevTest Labs automatisch geneste sjablonen kan beheren. Zie [geneste Azure Resource Manager sjablonen implementeren voor test omgevingen](deploy-nested-template-environments.md)voor meer informatie.

- U kunt meta gegevens opgeven om de weergave naam en beschrijving van de sjabloon op te geven in een bestand met de naam *metadata.jsop*, als volgt:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Key Azure Resource Manager sjabloon bestanden](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Sjabloon opslagplaatsen aan het lab toevoegen

Nadat u de opslag plaats hebt gemaakt en geconfigureerd, kunt u deze toevoegen aan uw Lab met behulp van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met Labs het gewenste Lab.
1. Selecteer **configuratie en beleid** in het deel venster **overzicht** van de test omgeving.

   ![Configuratie en beleid](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Selecteer in de lijst **configuratie-en beleids** instellingen de optie **opslag** plaatsen. De opslag plaats-opslag plaats voor **open bare artefacten** wordt automatisch gegenereerd voor alle Labs en maakt verbinding met de [open bare github-opslag plaats van DevTest Labs](https://github.com/Azure/azure-devtestlab).

1. Selecteer **toevoegen** om uw Azure Resource Manager-sjabloon opslagplaats toe te voegen.

   ![Open bare opslag plaats](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Voer in het deel venster **opslag** plaatsen de volgende gegevens in:

   - **Naam**: Voer een naam in voor de opslag plaats die in het lab moet worden gebruikt.
   - **Git-kloon-URL**: Voer de Git https-kloon-URL in van github of Azure opslag plaatsen.
   - **Vertakking** (optioneel): Voer de naam van de vertakking in om toegang te krijgen tot uw Azure Resource Manager sjabloon definities.
   - **Persoonlijk toegangs token**: Voer het persoonlijke toegangs token in dat wordt gebruikt voor een veilige toegang tot uw opslag plaats.
     - Als u uw token wilt ophalen uit Azure opslag plaatsen, selecteert u onder uw profiel de optie **gebruikers instellingen**  >    >  **persoonlijke toegangs tokens**.
     - Als u uw token wilt ophalen uit github, selecteert u onder uw profiel **instellingen** voor  >  **ontwikkelaars instellingen**  >  **persoonlijke toegangs tokens**.
   - **Mappaden**: Voer het mappad in dat relatief is ten opzichte van uw Git-kloon-URI voor uw artefact definities of uw Azure Resource Manager sjabloon definities.

1. Selecteer **Opslaan**.

   ![Nieuwe opslag plaats toevoegen](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Zodra u een Azure Resource Manager sjabloon aan het Lab hebt toegevoegd, kunnen uw Lab-gebruikers omgevingen maken met behulp van de sjabloon.

## <a name="configure-access-rights-for-lab-users"></a>Toegangs rechten voor Lab-gebruikers configureren

Lab-gebruikers hebben standaard **lezers** rollen, zodat ze de resources in een omgevings resource groep niet kunnen wijzigen. Ze kunnen bijvoorbeeld hun resources niet stoppen of starten.

Ga als volgt te werk om uw Lab-gebruikers de rol **Inzender** te geven, zodat ze de resources in hun omgevingen kunnen bewerken:

1. Selecteer in het [Azure Portal](https://portal.azure.com), in het deel venster **overzicht** van uw Lab, **configuratie en beleid** en selecteer vervolgens **Lab-instellingen**.

1. Selecteer in het deel venster **Lab-instellingen** de optie **Inzender** en selecteer vervolgens **Opslaan** om schrijf machtigingen toe te kennen aan gebruikers van het lab.

   ![Gebruikers toegangs rechten voor lab configureren](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

In de volgende sectie wordt beschreven hoe u omgevingen maakt op basis van een Azure Resource Manager sjabloon.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Omgevingen maken op basis van sjablonen in de Azure Portal

Zodra u een Azure Resource Manager sjabloon aan het Lab hebt toegevoegd, kunnen uw Lab-gebruikers in de Azure Portal omgevingen maken door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met Labs het gewenste Lab.

1. Selecteer op de pagina Lab de optie **toevoegen**.

1. In het deel venster **een basis kiezen** worden de basis installatie kopieën weer gegeven die u kunt gebruiken, met de Azure Resource Manager sjablonen die als eerste worden weer gegeven. Selecteer de gewenste Azure Resource Manager sjabloon.

   ![Een basis kiezen](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. Voer in het deel venster **toevoegen** een waarde in voor de **omgevings naam** die moet worden weer gegeven voor omgevings gebruikers.

   De sjabloon Azure Resource Manager definieert de rest van de invoer velden. Als de sjabloon *azuredeploy.parameter.jsin* het bestand standaard waarden definieert, worden deze waarden weer gegeven in de invoer velden.

   Voor para meters van het type *beveiligde teken reeks* kunt u geheimen van uw Azure Key Vault gebruiken. Zie [geheimen opslaan in azure Key Vault](devtest-lab-store-secrets-in-key-vault.md)voor meer informatie over het opslaan van geheimen in een sleutel kluis en het gebruik ervan bij het maken van Lab-resources.  

   ![Deel venster toevoegen](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > De volgende parameter waarden worden niet weer gegeven in de invoer velden, zelfs niet als de sjabloon deze opgeeft. In plaats daarvan worden in het formulier lege invoer velden weer gegeven waarin Lab-gebruikers waarden moeten invoeren bij het maken van de omgeving.
   >
   > - GEN-UNIEKE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-WACHT WOORD

1. Selecteer **toevoegen** om de omgeving te maken.

   De omgeving wordt onmiddellijk ingericht, met de status weer gegeven in de lijst **mijn virtuele machines** . Het Lab maakt automatisch een nieuwe resource groep om alle resources in te richten die in de Azure Resource Manager sjabloon zijn gedefinieerd.

1. Nadat de omgeving is gemaakt, selecteert u de omgeving in de lijst **mijn virtuele machines** om het deel venster Resource groep te openen en gaat u naar alle resources die de omgeving heeft ingericht.

   ![Omgevings resources](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   U kunt ook de omgeving uitbreiden om alleen de lijst met Vm's weer te geven die de omgeving heeft ingericht.

   ![Lijst met virtuele machines](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selecteer een van de omgevingen om de beschik bare acties weer te geven, zoals het Toep assen van artefacten, het koppelen van gegevens schijven, het wijzigen van de tijd voor automatisch afsluiten en nog veel meer.

   ![Omgevings acties](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Omgeving maken automatiseren met Power shell

Het is handig om de Azure Portal te gebruiken om één omgeving aan een Lab toe te voegen, maar wanneer een ontwikkel-of test scenario meerdere omgevingen moet maken, is geautomatiseerde implementatie een betere ervaring.

Voordat u doorgaat, moet u ervoor zorgen dat u een Azure Resource Manager-sjabloon hebt waarmee de te maken resources worden gedefinieerd. [Voeg de sjabloon in een Git-opslag plaats toe en configureer](#configure-your-own-template-repositories)deze en [Voeg de opslag plaats toe aan het lab](#add-template-repositories-to-the-lab).

Met het volgende voorbeeld script maakt u een omgeving in uw Lab. De opmerkingen helpen u het script beter te begrijpen.

1. Sla het volgende Power shell-voorbeeld script op uw harde schijf op als *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Voer het script als volgt uit, waarbij u uw specifieke waarden gebruikt voor SubscriptionId, LabName, ResourceGroupName, opslagplaats, templatenaam (map in de Git opslag plaats) en omgevings namen.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

U kunt ook Azure CLI gebruiken om resources te implementeren met Resource Manager-sjablonen. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../azure-resource-manager/templates/deploy-cli.md) voor meer informatie.

> [!NOTE]
> Alleen een gebruiker met machtigingen voor een Lab-eigenaar kan Vm's maken op basis van een resource manager-sjabloon met behulp van Azure PowerShell. Als u het maken van een virtuele machine wilt automatiseren met een resource manager-sjabloon en u alleen gebruikers machtigingen hebt, kunt u de CLI-opdracht [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create)gebruiken.

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Beperkingen van Resource Manager-sjablonen in DevTest Labs

Houd rekening met deze beperkingen bij het gebruik van Resource Manager-sjablonen in DevTest Labs:

- U kunt geen formules of aangepaste installatie kopieën maken van Lab-Vm's die zijn gemaakt op basis van een resource manager-sjabloon.

- De meeste beleids regels worden niet geëvalueerd wanneer u Resource Manager-sjablonen implementeert.

Zo kunt u bijvoorbeeld een Lab-beleid hebben dat een gebruiker slechts vijf Vm's kan maken. Een gebruiker kan echter een resource manager-sjabloon implementeren waarmee tien tallen virtuele machines worden gemaakt. Beleids regels die niet worden geëvalueerd, zijn onder andere:

  - Aantal Vm's per gebruiker

  - Aantal Premium Vm's per Lab-gebruiker

  - Aantal Premium-schijven per Lab-gebruiker

## <a name="next-steps"></a>Volgende stappen
- Wanneer u een virtuele machine hebt gemaakt, kunt u verbinding maken met de virtuele machine door **verbinding maken** te selecteren in het deel venster beheer van de virtuele machine.
- Bekijk en beheer resources in een omgeving door de omgeving te selecteren in de lijst **mijn virtuele machines** in uw Lab.
- Verken de [Azure Resource Manager sjablonen vanuit de Azure Quick Start-sjabloon galerie](https://github.com/Azure/azure-quickstart-templates).
