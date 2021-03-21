---
title: Een opslag plaats voor artefacten toevoegen aan uw Lab in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over het opgeven van uw eigen artefact opslagplaats voor uw Lab in Azure DevTest Labs om hulpprogram ma's op te slaan die niet beschikbaar zijn in de open bare artefact opslagplaats.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da4e345b18a46226853d71bbf66af0487f1a761f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502192"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Een opslag plaats voor artefacten toevoegen aan uw Lab in DevTest Labs
Met DevTest Labs kunt u een artefact opgeven die moet worden toegevoegd aan een virtuele machine op het moment dat de virtuele machine wordt gemaakt of nadat de virtuele machine is aangemaakt. Dit artefact kan een hulp programma of een toepassing zijn die u wilt installeren op de VM. Artefacten worden gedefinieerd in een JSON-bestand dat is geladen vanuit een GitHub-of Azure DevOps Git-opslag plaats.

De [open bare artefact opslagplaats](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), die wordt onderhouden door DevTest Labs, biedt veel algemene hulpprogram Ma's voor Windows en Linux. Er wordt automatisch een koppeling naar deze opslag plaats toegevoegd aan uw Lab. U kunt uw eigen artefact opslagplaats maken met specifieke hulpprogram ma's die niet beschikbaar zijn in de open bare artefact opslagplaats. Zie [aangepaste artefacten maken](devtest-lab-artifact-author.md)voor meer informatie over het maken van aangepaste artefacten.

Dit artikel bevat informatie over het toevoegen van uw aangepaste artefact opslagplaats met behulp van Azure Portal, Azure resource management-sjablonen en Azure PowerShell. U kunt automatiseren van het toevoegen van een artefact opslagplaats aan een lab door Power shell-of CLI-scripts te schrijven.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
Als u een opslag plaats aan uw Lab wilt toevoegen, moet u eerst belang rijke informatie uit uw opslag plaats ophalen. In de volgende secties wordt beschreven hoe u de vereiste gegevens ophaalt voor opslag plaatsen die worden gehost op **github** of **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>De kloon-URL van de GitHub-opslag plaats en het persoonlijke toegangs Token ophalen

1. Ga naar de start pagina van de GitHub-opslag plaats die de definities van de artefact-of Resource Manager-sjabloon bevat.
2. Selecteer **Klonen of downloaden**.
3. Als u de URL naar het klem bord wilt kopiëren, selecteert u de knop **https-kloon-URL** . Sla de URL op voor later gebruik.
4. Selecteer de profiel afbeelding in de rechter bovenhoek van GitHub en selecteer vervolgens **instellingen**.
5. Selecteer in het menu **persoonlijke instellingen** aan de linkerkant **instellingen voor ontwikkel aars**.
6. Selecteer **persoonlijke toegangs tokens** in het menu links.
7. Selecteer **nieuw token genereren**.
8. Voer op de pagina **nieuw persoonlijk toegangs token** onder **Beschrijving van token** een beschrijving in. Accepteer de standaard items onder **scopes selecteren** en selecteer vervolgens **token genereren**.
9. Sla het gegenereerde token op. U kunt het token later gebruiken.
10. Sluit GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>De Azure opslag plaatsen kloon-URL en het persoonlijke toegangs Token ophalen
1. Ga naar de start pagina van uw team verzameling (bijvoorbeeld `https://contoso-web-team.visualstudio.com` ) en selecteer vervolgens uw project.
2. Selecteer op de start pagina van het project **code**.
3. Als u de kloon-URL wilt weer geven, selecteert u in de project **code** pagina **klonen**.
4. Sla de URL op. U kunt de URL later gebruiken.
5. Als u een persoonlijk toegangs token wilt maken, selecteert u in de vervolg keuzelijst gebruikers account **Mijn profiel**.
6. Selecteer op de pagina profiel informatie **beveiliging**.
7. Selecteer **+ nieuw token** op het tabblad **beveiliging > persoonlijke toegangs tokens** .
8. Op de pagina **een nieuw persoonlijk toegangs token maken** :
   1. Voer een **naam** in voor het token.
   2. Selecteer in de lijst **organisatie** **alle toegankelijke organisaties**.
   3. In de lijst **verloop tijd (UTC)** selecteert u **90 dagen** of een aangepaste gedefinieerde verval periode.
   4. Selecteer de optie **volledige toegang** voor bereiken.
   5. Selecteer **Maken**.
9. Het nieuwe token wordt weer gegeven in de lijst met **persoonlijke toegangs tokens** . Selecteer **token kopiëren** en sla de token waarde op voor later gebruik.
10. Ga door naar de sectie uw Lab verbinden met de opslag plaats.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie worden de stappen beschreven voor het toevoegen van een artefact opslagplaats aan een lab in de Azure Portal.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **meer services** en selecteer vervolgens **DevTest Labs** in de lijst met Services.
3. Selecteer in de lijst met Labs uw Lab.
4. Selecteer **configuratie en beleid** in het menu links.
5. Selecteer **opslag** plaatsen onder **externe resources** in het menu aan de linkerkant.
6. Selecteer **+ toevoegen** op de werk balk.

    ![De knop opslag plaats toevoegen](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Geef op de pagina **opslag** plaatsen de volgende informatie op:
   1. **Naam**. Voer een naam in voor de opslag plaats.
   2. **Git clone URL**. Voer de Git HTTPS kloon-URL in die u eerder hebt gekopieerd uit GitHub of Azure DevOps Services.
   3. **Vertakking**. Voer de vertakking in om uw definities op te halen.
   4. **Persoonlijk toegangs token**. Voer het persoonlijke toegangs token in dat u eerder hebt ontvangen van een GitHub-of Azure DevOps-service.
   5. **Mappaden**. Voer ten minste één mappad in ten opzichte van de kloon-URL die uw sjabloon definities voor artefacten of Resource Manager bevat. Wanneer u een submap opgeeft, moet u ervoor zorgen dat u de slash in het mappad opneemt.

        ![Opslag gebied](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecteer **Opslaan**.

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager sjabloon gebruiken
Azure Resource Management (Azure Resource Manager)-Sjablonen zijn JSON-bestanden die de resources in azure beschrijven die u wilt maken. Zie [Azure Resource Manager sjablonen ontwerpen](../azure-resource-manager/templates/template-syntax.md)voor meer informatie over deze sjablonen.

Deze sectie bevat stappen voor het toevoegen van een artefact opslagplaats aan een Lab met behulp van een Azure Resource Manager sjabloon.  De sjabloon maakt de Lab als deze nog niet bestaat.

### <a name="template"></a>Sjabloon
De voorbeeld sjabloon die in dit artikel wordt gebruikt, verzamelt de volgende informatie via para meters. De meeste para meters hebben slimme standaard waarden, maar er zijn een paar waarden die moeten worden opgegeven. U moet de naam van het lab, de URI voor de artefact opslagplaats en het beveiligings token voor de opslag plaats opgeven.

- Naam van Lab.
- Weergave naam voor de opslag plaats voor artefacten in de gebruikers interface van DevTest Labs (UI). De standaard waarde is: `Team Repository` .
- URI naar de opslag plaats (voor beeld: `https://github.com/<myteam>/<nameofrepo>.git` of `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .
- Vertakking in de opslag plaats die artefacten bevat. De standaard waarde is: `master` .
- De naam van de map die artefacten bevat. De standaard waarde is: `/Artifacts` .
- Type opslag plaats. Toegestane waarden zijn `VsoGit` of `GitHub` .
- Toegangs token voor de opslag plaats.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>De sjabloon implementeren
Er zijn een aantal manieren om de sjabloon te implementeren in Azure en de resource te maken, als deze nog niet bestaat, of bijgewerkt als deze wel bestaat. Raadpleeg de volgende artikelen voor meer informatie:

- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Resources implementeren met Resource Manager-sjablonen en Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [Resources implementeren met Resource Manager-sjablonen en Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)

Laten we eens kijken hoe u de sjabloon in Power shell implementeert. Cmdlets die worden gebruikt om de sjabloon te implementeren, zijn context afhankelijke, zodat de huidige Tenant en het huidige abonnement worden gebruikt. Gebruik [set-AzContext](/powershell/module/az.accounts/set-azcontext) voordat u de sjabloon implementeert, indien nodig, om de context te wijzigen.

Maak eerst een resource groep met behulp van [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Als de resource groep die u wilt gebruiken al bestaat, slaat u deze stap over.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Maak vervolgens een implementatie naar de resource groep met behulp van [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Met deze cmdlet worden de resource wijzigingen toegepast op Azure. Er kunnen verschillende resource-implementaties worden gemaakt voor een bepaalde resource groep. Als u meerdere keren naar dezelfde resource groep implementeert, moet u ervoor zorgen dat de naam van elke implementatie uniek is.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Nadat de New-AzResourceGroupDeployment uitgevoerd, voert de opdracht belang rijke informatie uit, zoals de inrichtings status (moet zijn geslaagd) en eventuele uitvoer van de sjabloon.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken
Deze sectie biedt een voor beeld van een Power shell-script dat kan worden gebruikt om een artefact opslagplaats toe te voegen aan een lab. Als u geen Azure PowerShell hebt, raadpleegt u [Azure PowerShell installeren en configureren](/powershell/azure/) voor gedetailleerde instructies om het te installeren.

### <a name="full-script"></a>Volledige script
Dit is het volledige script, inclusief enkele uitgebreide berichten en opmerkingen:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the repository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Het PowerShell-script uitvoeren
In het volgende voor beeld ziet u hoe u het script uitvoert:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parameters
In het Power shell-voorbeeld script in dit artikel worden de volgende para meters gebruikt:

| Parameter | Beschrijving |
| --------- | ----------- |
| LabName | De naam van het lab. |
| ArtifactRepositoryName | Naam voor de nieuwe artefact opslagplaats. Het script maakt een wille keurige naam voor de opslag plaats als deze niet is opgegeven. |
| ArtifactRepositoryDisplayName | Weergave naam voor de artefact opslagplaats. Dit is de naam die wordt weer gegeven in de Azure Portal ( https://portal.azure.com) bij het weer geven van alle artefact opslagplaatsen voor een lab. |
| RepositoryUri | De URI naar de opslag plaats. Voor beelden: `https://github.com/<myteam>/<nameofrepo>.git` of `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .|
| RepositoryBranch | Vertakking waarin artefact bestanden kunnen worden gevonden. De standaard waarde is ' Master '. |
| FolderPath | De map waaronder artefacten kunnen worden gevonden. Standaard ingesteld op '/Artifacts ' |
| PersonalAccessToken | Beveiligings token voor toegang tot de opslag plaats GitHub of VSOGit. Zie de sectie vereisten voor instructies voor het verkrijgen van een persoonlijk toegangs token |
| SourceType | Of artefacten VSOGit of GitHub opslag plaats zijn. |

De opslag plaats zelf moet een interne naam hebben voor identificatie, wat afwijkt van de weergave naam die wordt weer gegeven in de Azure Portal. U ziet de interne naam niet met behulp van de Azure Portal, maar u ziet deze wanneer u Azure REST Api's of Azure PowerShell gebruikt. Het script geeft een naam, indien deze niet is opgegeven door de gebruiker van het script.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Power shell-opdrachten die in het script worden gebruikt

| PowerShell-opdracht | Opmerkingen |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Deze opdracht wordt gebruikt om details over het lab, zoals de locatie ervan, op te halen. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Er is geen specifieke opdracht voor het toevoegen van artefact opslagplaatsen. De algemene cmdlet [New-AzResource](/powershell/module/az.resources/new-azresource) voert de taak uit. Voor deze cmdlet **moet de waarde** **ResourceID** of de combi natie resourcenaam en **resource** type zijn ingesteld om te weten welk soort bron moet worden gemaakt. Dit voorbeeld script maakt gebruik van de resource naam en het resource type paar. <br/><br/>U ziet dat u de opslag plaats voor artefacten op dezelfde locatie en onder dezelfde resource groep maakt als het lab.|

Het script voegt een nieuwe resource toe aan het huidige abonnement. Gebruik [Get-AzContext](/powershell/module/az.accounts/get-azcontext) om deze informatie te bekijken. Gebruik [set-AzContext](/powershell/module/az.accounts/set-azcontext) om de huidige Tenant en het opgegeven abonnement in te stellen.

De beste manier om de informatie over de resource naam en het bron type te detecteren is met behulp van de [Azure rest api's](https://azure.github.io/projects/apis/) -website van het test station. Bekijk de [DevTest Labs-2016-05-15-](https://aka.ms/dtlrestapis) provider voor een overzicht van de beschik bare rest-api's voor de DevTest Labs-provider. Het script gebruikers de volgende resource-ID.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Het resource type is alles vermeld na ' providers ' in de URI, met uitzonde ring van items die worden weer gegeven in de accolades. De resource naam is alles wat in de accolades wordt weer gegeven. Als er meer dan één item wordt verwacht voor de resource naam, moet u elk item scheiden met een slash zoals we hebben gedaan.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Volgende stappen
- [Verplichte artefacten opgeven voor uw Lab in Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Aangepaste artefacten maken voor uw virtuele machine van DevTest Labs](devtest-lab-artifact-author.md)
- [Storingen in artefacten in het lab diagnosticeren](devtest-lab-troubleshoot-artifact-failure.md)
