---
title: Omgevingen met meerdere VM's en PaaS-resources maken met sjablonen
description: Meer informatie over het maken van multi-VM-omgevingen en PaaS-resources in Azure DevTest Labs van een Azure Resource Manager sjabloon
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: f285acffe642a85fa27792ee51ea67a57f6d35a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790109"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen

Azure DevTest Labs omgevingen kunnen gebruikers eenvoudig complexe infrastructuren op een consistente manier implementeren binnen de omgevingen van het lab. U kunt de [Azure Resource Manager gebruiken om](../azure-resource-manager/templates/template-syntax.md) omgevingen te maken met sets resources in DevTest Labs. Deze omgevingen kunnen alle Azure-resources bevatten die Resource Manager sjablonen kunnen maken.

U kunt eenvoudig [één virtuele machine (VM)](devtest-lab-add-vm.md) tegelijk toevoegen aan een lab met behulp van [Azure Portal](https://portal.azure.com). Scenario's zoals web-apps met meerdere lagen of een SharePoint-farm hebben echter een mechanisme nodig om meerdere VM's in één stap te maken. Met behulp Azure Resource Manager sjablonen kunt u de infrastructuur en configuratie van uw Azure-oplossing definiëren en meerdere VM's herhaaldelijk in een consistente status implementeren.

Azure Resource Manager-sjablonen bieden ook de volgende voordelen:

- Azure Resource Manager sjablonen worden rechtstreeks vanuit uw GitHub- of Azure Repos-opslagplaats voor broncodebeheer geladen.
- Uw gebruikers kunnen een omgeving maken door een geconfigureerde Azure Resource Manager sjabloon te kiezen uit de Azure Portal, net als bij andere typen [VM-basissen.](devtest-lab-comparing-vm-base-image-types.md)
- U kunt Azure PaaS-resources en IaaS-VM's inrichten in een omgeving op basis van Azure Resource Manager sjabloon.
- U kunt de kosten van omgevingen in het lab bijhouden, naast afzonderlijke VM's die zijn gemaakt door andere typen basissen. PaaS-resources worden gemaakt en worden weergegeven in het bijhouden van kosten. Automatisch afsluiten van VM's is echter niet van toepassing op PaaS-resources.

Zie Voordelen van het gebruik van Resource Manager-sjablonen voor meer informatie over de voordelen van het gebruik van Resource Manager-sjablonen voor het implementeren, bijwerken of verwijderen van veel labresources in [één](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)bewerking.

> [!NOTE]
> Wanneer u een Resource Manager als basis gebruikt om lab-VM's te maken, zijn er enkele verschillen tussen het maken van meerdere VM's of één VM. Zie Use [a virtual machine's Azure Resource Manager template (Een virtuele machine gebruiken) Azure Resource Manager meer informatie.](devtest-lab-use-resource-manager-template.md)
>

## <a name="use-devtest-labs-public-environments"></a>Openbare DevTest Labs-omgevingen gebruiken
Azure DevTest Labs heeft een [](https://github.com/Azure/azure-devtestlab/tree/master/Environments) openbare opslagplaats met Azure Resource Manager-sjablonen die u kunt gebruiken om omgevingen te maken zonder zelf verbinding te moeten maken met een externe GitHub-bron. Deze openbare opslagplaats is vergelijkbaar met de openbare opslagplaats met artefacten die beschikbaar zijn in de Azure Portal voor elk lab dat u maakt. Met de opslagplaats voor de omgeving kunt u snel aan de slag met vooraf geschreven omgevingssjablonen met enkele invoerparameters. Deze sjablonen bieden u een soepele aan de slag-ervaring voor PaaS-resources binnen labs.

In de openbare opslagplaats hebben het DevTest Labs-team en anderen veelgebruikte sjablonen gemaakt en gedeeld, zoals Azure Web Apps, Service Fabric Cluster en een SharePoint Farm-ontwikkelomgeving. U kunt deze sjablonen rechtstreeks gebruiken of aanpassen aan uw behoeften. Zie Openbare omgevingen configureren en [gebruiken in DevTest Labs](devtest-lab-configure-use-public-environments.md)voor meer informatie. Nadat u uw eigen sjablonen hebt gemaakt, kunt u deze opslaan in deze opslagplaats om ze met anderen te delen of uw eigen Git-opslagplaats instellen.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Uw eigen sjabloon-opslagplaatsen maken

Als een van de best practices met infrastructure-as-code en configuration-as-code moet u omgevingssjablonen beheren in broncodebeheer. Azure DevTest Labs volgt deze praktijk en laadt alle sjablonen Azure Resource Manager rechtstreeks vanuit uw GitHub- of Azure-opslagplaatsen. Als gevolg hiervan kunt u de sjablonen Resource Manager hele releasecyclus gebruiken, van de testomgeving tot de productieomgeving.

Er zijn verschillende regels die u moet volgen om uw sjablonen Azure Resource Manager ordenen in een opslagplaats:

- U moet de naam van het hoofdsjabloonbestand *azuredeploy.jsop*.

- Als u parameterwaarden wilt gebruiken die zijn gedefinieerd in een parameterbestand, moet het parameterbestand de *naamazuredeploy.parameters.jsop*.

  U kunt de parameters en `_artifactsLocation` gebruiken om de parameters te `_artifactsLocationSasToken` makenLink-URI-waarde, zodat DevTest Labs automatisch geneste sjablonen kan beheren. Zie Deploy [nested Azure Resource Manager templates for testing environments (Geneste Azure Resource Manager implementeren voor testomgevingen) voor meer informatie.](deploy-nested-template-environments.md)

- U kunt metagegevens definiëren om de weergavenaam en beschrijving van de sjabloon op te geven in een bestand met de *metadata.jsop*, als volgt:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Sleutel-Azure Resource Manager sjabloonbestanden](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Sjabloon-opslagplaatsen toevoegen aan het lab

Nadat u uw opslagplaats hebt gemaakt en geconfigureerd, kunt u deze toevoegen aan uw lab met behulp van Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Alle services** en selecteer vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met labs het lab dat u wilt.
1. Selecteer configuratie en beleidsregels in **het** deelvenster Overzicht **van het lab.**

   ![Configuratie en beleid](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Selecteer **opslagplaatsen in** de lijst Instellingen voor configuratie **en beleid.** De **opslagplaats voor openbare artefacten** wordt automatisch gegenereerd voor alle labs en maakt verbinding met de openbare [GitHub-opslagplaats DevTest Labs.](https://github.com/Azure/azure-devtestlab)

1. Selecteer Toevoegen om Azure Resource Manager sjabloonopslagplaats toe **te voegen.**

   ![Openbare repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Voer in **het deelvenster Opslagplaatsen** de volgende gegevens in:

   - **Naam:** voer een naam in voor de opslagplaats die u in het lab wilt gebruiken.
   - **Git-kloon-URL:** voer de Git HTTPS-kloon-URL uit GitHub of Azure Repos in.
   - **Vertakking** (optioneel): voer de naam van de vertakking in voor toegang tot Azure Resource Manager sjabloondefinities.
   - **Persoonlijk toegang token:** voer het persoonlijke toegang token in dat wordt gebruikt voor veilige toegang tot uw opslagplaats.
     - Als u uw token wilt op halen uit Azure Repos, selecteert u onder uw profiel **Gebruikersinstellingen**  >  **Beveiliging**  >  **Persoonlijke toegangstokens.**
     - Als u uw token wilt op halen uit GitHub, selecteert u onder uw profiel Instellingen **Persoonlijke** toegangstokens voor instellingen  >    >  **voor ontwikkelaarsinstellingen.**
   - **Mappaden:** voer het mappad in dat relatief is ten opzichte van uw Git-kloon-URI voor uw artefactdefinities of uw Azure Resource Manager sjabloondefinities.

1. Selecteer **Opslaan**.

   ![Nieuwe opslagplaats toevoegen](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Zodra u een Azure Resource Manager aan het lab toevoegt, kunnen uw labgebruikers omgevingen maken met behulp van de sjabloon.

## <a name="configure-access-rights-for-lab-users"></a>Toegangsrechten configureren voor labgebruikers

Labgebruikers hebben **standaard de** rol Lezer, zodat ze de resources in een omgevingsresourcegroep niet kunnen wijzigen. Ze kunnen hun resources bijvoorbeeld niet stoppen of starten.

Volg deze stappen om uw labgebruikers de rol **Inzender** te geven zodat ze de resources in hun omgevingen kunnen bewerken:

1. Selecteer in [Azure Portal](https://portal.azure.com)in het deelvenster  Overzicht van uw lab de optie **Configuratie** en beleidsregels en selecteer **vervolgens Labinstellingen.**

1. Selecteer in **het deelvenster Labinstellingen** de optie **Inzender** en selecteer vervolgens **Opslaan om** labgebruikers schrijfmachtigingen te verlenen.

   ![Toegangsrechten voor labgebruikers configureren](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

In de volgende sectie worden omgevingen gemaakt op basis van Azure Resource Manager sjabloon.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Omgevingen maken op basis van sjablonen in Azure Portal

Wanneer u een sjabloon Azure Resource Manager lab toevoegt, kunnen uw labgebruikers omgevingen maken in de Azure Portal door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Alle services** en selecteer vervolgens **DevTest Labs** in de lijst.

1. Selecteer in de lijst met labs het lab dat u wilt.

1. Selecteer toevoegen op de pagina van **het** lab.

1. In **het deelvenster Een basis kiezen** worden de basisafbeeldingen weergegeven die u kunt gebruiken, Azure Resource Manager eerst worden weergegeven. Selecteer de Azure Resource Manager sjabloon die u wilt gebruiken.

   ![Een basis kiezen](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. Voer in **het deelvenster** Toevoegen de waarde **Omgevingsnaam** in die moet worden weergegeven voor omgevingsgebruikers.

   De Azure Resource Manager definieert de rest van de invoervelden. Als de sjabloon *azuredeploy.parameter.jsbestand standaardwaarden* definieert, worden deze waarden weergegeven in de invoervelden.

   Voor parameters van het type *beveiligde tekenreeks* kunt u geheimen uit uw Azure Key Vault. Zie Geheimen opslaan Azure Key Vault in een sleutelkluis voor meer informatie over het opslaan van geheimen in een sleutelkluis en het gebruik ervan bij het maken van [labbronnen.](devtest-lab-store-secrets-in-key-vault.md)  

   ![Deelvenster Toevoegen](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > De volgende parameterwaarden worden niet weergegeven in de invoervelden, zelfs niet als deze door de sjabloon worden opgegeven. In plaats daarvan toont het formulier lege invoervelden waar labgebruikers waarden moeten invoeren bij het maken van de omgeving.
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. Selecteer **Toevoegen om** de omgeving te maken.

   De omgeving begint onmiddellijk met inrichten, met de status die wordt weergegeven in de lijst **Mijn virtuele machines.** Het lab maakt automatisch een nieuwe resourcegroep voor het inrichten van alle resources die zijn gedefinieerd in Azure Resource Manager sjabloon.

1. Zodra de omgeving is gemaakt, selecteert u de omgeving in de lijst Mijn virtuele **machines** om het deelvenster Resourcegroep te openen en door alle resources te bladeren die de omgeving heeft ingericht.

   ![Omgevingsbronnen](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   U kunt de omgeving ook uitbreiden om alleen de lijst met VM's weer te geven die de omgeving heeft ingericht.

   ![Lijst met mijn virtuele machines](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Selecteer een van de omgevingen om de beschikbare acties weer te geven, zoals het toepassen van artefacten, het koppelen van gegevensschijven, het wijzigen van de tijd voor automatisch afsluiten, en meer.

   ![Omgevingsacties](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Het maken van een omgeving automatiseren met PowerShell

Het is haalbaar om de Azure Portal te gebruiken om één omgeving aan een lab toe te voegen, maar wanneer een ontwikkelings- of testscenario meerdere omgevingen moet maken, is geautomatiseerde implementatie een betere ervaring.

Voordat u doorgaat, moet u ervoor zorgen dat u een sjabloon Azure Resource Manager die de resources definieert die moeten worden gemaakt. [Voeg de sjabloon toe en configureer deze in een Git-opslagplaats](#configure-your-own-template-repositories)en [voeg de opslagplaats toe aan het lab](#add-template-repositories-to-the-lab).

Met het volgende voorbeeldscript maakt u een omgeving in uw lab. De opmerkingen helpen u het script beter te begrijpen.

1. Sla het volgende PowerShell-voorbeeldscript op uw harde schijf op *alsdeployenv.ps1*.

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

1. Voer het script als volgt uit met behulp van uw specifieke waarden voor SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (map in de Git-opslagplaats) en EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

U kunt Azure CLI ook gebruiken om resources te implementeren met Resource Manager sjablonen. Zie [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../azure-resource-manager/templates/deploy-cli.md) voor meer informatie.

> [!NOTE]
> Alleen een gebruiker met eigenaarsmachtigingen voor een lab kan virtuele Resource Manager maken met behulp van Azure PowerShell. Als u het maken van VM's wilt automatiseren met behulp van een Resource Manager-sjabloon en u alleen gebruikersmachtigingen hebt, kunt u de CLI-opdracht [az lab vm create gebruiken.](/cli/azure/lab/vm#az_lab_vm_create)

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Resource Manager sjabloonbeperkingen in DevTest Labs

Houd rekening met deze beperkingen bij het gebruik Resource Manager in DevTest Labs:

- U kunt geen formules of aangepaste afbeeldingen maken van lab-VM's die zijn gemaakt op basis van een Resource Manager sjabloon.

- De meeste beleidsregels worden niet geëvalueerd wanneer u sjablonen Resource Manager implementeren.

U kunt bijvoorbeeld een labbeleid hebben dat een gebruiker slechts vijf VM's kan maken. Een gebruiker kan echter een sjabloon Resource Manager om tientallen VM's te maken. Beleidsregels die niet worden geëvalueerd, zijn onder andere:

  - Aantal VM's per gebruiker

  - Aantal premium-VM's per labgebruiker

  - Aantal Premium-schijven per labgebruiker

## <a name="next-steps"></a>Volgende stappen
- Zodra u een VM hebt gemaakt, kunt u verbinding maken met de VM door Verbinding maken te selecteren **in** het beheervenster van de VM.
- U kunt resources in een omgeving weergeven en beheren door de omgeving te selecteren in de lijst **Mijn virtuele machines** in uw lab.
- Verken de [Azure Resource Manager-sjablonen uit de galerie met Azure-quickstartsjablonen.](https://github.com/Azure/azure-quickstart-templates)
