---
title: Een CI/CD-pijp lijn voor Azure Data Lake Analytics instellen
description: Meer informatie over het instellen van continue integratie en continue implementatie voor Azure Data Lake Analytics.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 09/14/2018
ms.openlocfilehash: 95b638b85e0746d2995488f2a28a5fb2512b1063
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015261"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Een CI/CD-pijp lijn voor Azure Data Lake Analytics instellen  

In dit artikel leert u hoe u een doorlopende integratie-en implementatie pijplijn (CI/CD) kunt instellen voor U-SQL-taken en U-SQL-data bases.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>CI/CD voor U-SQL-taken gebruiken

Azure Data Lake-Hulpprogram Ma's voor Visual Studio biedt het U-SQL-project type waarmee u u-SQL-scripts kunt ordenen. Door gebruik te maken van het U-SQL-project om uw U-SQL-code te beheren, kunt U nog meer CI/CD-scenario's gebruiken.

## <a name="build-a-u-sql-project"></a>Een U-SQL-project bouwen

Een U-SQL-project kan worden gemaakt met de micro soft build Engine (MSBuild) door de bijbehorende para meters door te geven. Volg de stappen in dit artikel om een bouw proces voor een U-SQL-project in te stellen.

### <a name="project-migration"></a>Project migratie

Voordat u een bouw taak voor een U-SQL-project instelt, moet u ervoor zorgen dat u de nieuwste versie van het U-SQL-project hebt. Open het U-SQL-project bestand in uw editor en controleer of u deze items hebt geïmporteerd:

```xml
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
```

Als dat niet het geval is, hebt u twee opties voor het migreren van het project:

- Optie 1: Wijzig het oude import item naar het vorige bestand.
- Optie 2: Open het oude project in de Azure Data Lake-Hulpprogram Ma's voor Visual Studio. Gebruik een versie die nieuwer is dan 2.3.3000.0. De oude project-sjabloon wordt automatisch bijgewerkt naar de nieuwste versie. Nieuwe projecten die zijn gemaakt met nieuwere versies dan 2.3.3000.0, maken gebruik van de nieuwe sjabloon.

### <a name="get-nuget"></a>NuGet ophalen

MSBuild biedt geen ingebouwde ondersteuning voor U-SQL-projecten. Als u deze ondersteuning wilt ontvangen, moet u een verwijzing naar uw oplossing toevoegen aan het pakket [micro soft. Azure. DataLake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet waarmee de vereiste taal service wordt toegevoegd.

Als u de NuGet-pakket verwijzing wilt toevoegen, klikt u met de rechter muisknop op de oplossing in Visual Studio Solution Explorer en kiest u **NuGet-pakketten beheren**. U kunt ook een bestand `packages.config` met de naam in de map Solution toevoegen en de volgende inhoud in de oplossing plaatsen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
```

### <a name="manage-u-sql-database-references"></a>U-SQL database-verwijzingen beheren

U-SQL-scripts in een U-SQL-project bevatten mogelijk query-instructies voor U-SQL database-objecten. In dat geval moet u verwijzen naar het bijbehorende U-SQL database-project dat de object definitie bevat voordat u het U-SQL-project bouwt. Een voor beeld is wanneer u een query uitvoert op een U-SQL-tabel of naar een assembly verwijst.

Meer informatie over [U-SQL database project](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
> De instructie DROP kan een onbedoelde verwijdering veroorzaken. Als u instructie DROP wilt inschakelen, moet u expliciet de MSBuild-argumenten opgeven. **AllowDropStatement** maakt niet-gegevens gerelateerde neerzetten mogelijk, zoals de functie voor het verwijderen van een assembly en het verwijderen van een tabel waarde. **AllowDataDropStatement** maakt gegevens gerelateerde drop bewerking mogelijk, zoals drop table en drop schema. U moet AllowDropStatement inschakelen voordat u AllowDataDropStatement gebruikt.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Een U-SQL-project bouwen met de MSBuild opdracht regel

Migreer eerst het project en down load het NuGet-pakket. Vervolgens roept u de standaard MSBuild opdracht regel met de volgende aanvullende argumenten op om uw U-SQL-project te bouwen:

```console
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
```

De argumenten definitie en waarden zijn als volgt:

- **USQLSDKPath = \<U-SQL Nuget package> \build\runtime**. Deze para meter verwijst naar het installatiepad van het NuGet-pakket voor de U-SQL-taal service.
- **USQLTargetType = Merge of SyntaxCheck**:

  - **Samen voegen**. In de modus samen voegen worden code-behind bestanden gecompileerd. Voor beelden zijn **. cs**-, **. py**-en **. r** -bestanden. Het geeft de resulterende door de gebruiker gedefinieerde code bibliotheek in het U-SQL-script. Voor beelden zijn een dll binaire, python-of R-code.

  - **SyntaxCheck**. In de SyntaxCheck-modus worden code-behind-bestanden eerst samengevoegd in het U-SQL-script. Vervolgens compileert het U-SQL-script om uw code te valideren.

- **Data root = \<DataRoot path>**. Data root is alleen nodig voor de modus SyntaxCheck. Wanneer het script wordt gebouwd met de modus SyntaxCheck, controleert MSBuild de verwijzingen naar database objecten in het script. Voordat U bouwt, stelt U een overeenkomende lokale omgeving in die de objecten bevat waarnaar wordt verwezen vanuit de U-SQL database in de map Data root van de computer bouwen. U kunt deze database afhankelijkheden ook beheren door te [verwijzen naar een U-SQL database-project](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild controleert alleen database object verwijzingen, geen bestanden.

- **EnableDeployment = True** of **False**. EnableDeployment geeft aan of het is toegestaan om U-SQL-data bases te implementeren tijdens het bouw proces. Als u verwijst naar een U-SQL database project en de database objecten in uw U-SQL-script gebruikt, stelt u deze para meter in op **waar**.

### <a name="continuous-integration-through-azure-pipelines"></a>Continue integratie via Azure-pijp lijnen

Naast de opdracht regel kunt u ook de taak Visual Studio build of MSBuild gebruiken om U-SQL-projecten in azure-pijp lijnen te maken. Als u een build-pijp lijn wilt instellen, moet u twee taken toevoegen aan de build-pijp lijn: een NuGet-herstel taak en een MSBuild-taak.

![MSBuild-taak voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Voeg een NuGet-herstel taak toe voor het ophalen van het NuGet-pakket met oplossingen waarnaar wordt verwezen `Azure.DataLake.USQL.SDK` , zodat MSBuild de U-SQL-taal doelen kan vinden. Stel de **Geavanceerde**  >  **doelmap** in op `$(Build.SourcesDirectory)/packages` Als u het voor beeld van de MSBuild-argumenten rechtstreeks in stap 2 wilt gebruiken.

   ![NuGet-herstel taak voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Stel MSBuild-argumenten in Visual Studio build tools of in een MSBuild-taak in, zoals wordt weer gegeven in het volgende voor beeld. U kunt ook variabelen definiëren voor deze argumenten in de pijp lijn voor het bouwen van Azure-pijp lijnen.

   ![CI/CD MSBuild-variabelen definiëren voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
   ```

### <a name="u-sql-project-build-output"></a>Uitvoer van U-SQL-project build

Nadat u een build hebt uitgevoerd, worden alle scripts in het U-SQL-project gebouwd en uitgevoerd naar een zip-bestand met de naam `USQLProjectName.usqlpack` . De mapstructuur in uw project wordt bewaard in de gezipte build-uitvoer.

> [!NOTE]
> De code-behind bestanden voor elk U-SQL-script worden samengevoegd als een inline-instructie voor de uitvoer van het script.

## <a name="test-u-sql-scripts"></a>U-SQL-scripts testen

Azure Data Lake levert test projecten voor U-SQL-scripts en C# UDO/UDAG/UDF:

- Meer informatie over het [toevoegen van test cases voor U-SQL-scripts en uitgebreide C#-code](data-lake-analytics-cicd-test.md#test-u-sql-scripts)

- Meer informatie over het [uitvoeren van test cases in azure-pijp lijnen](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Een U-SQL-taak implementeren

Nadat u de code via het build-en test proces hebt gecontroleerd, kunt u U-SQL-taken rechtstreeks vanuit Azure-pijp lijnen verzenden via een Azure PowerShell taak. U kunt het script ook implementeren in Azure Data Lake Store of Azure Blob Storage en [de geplande taken uitvoeren via Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>U-SQL-taken verzenden via Azure-pijp lijnen

De build-uitvoer van het U-SQL-project is een zip-bestand met de naam **USQLProjectName. usqlpack**. Het zip-bestand bevat alle U-SQL-scripts in het project. U kunt de [Azure PowerShell taak](/azure/devops/pipelines/tasks/deploy/azure-powershell) in pijp lijnen gebruiken met het volgende Power shell-voorbeeld script om u-SQL-taken rechtstreeks vanuit Azure-pijp lijnen te verzenden.

```powershell
<#
   This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and submit all scripts one-by-one.

   Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.

   Example :
      USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

   return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
   $usqlFiles = GetUsqlFiles

   Write-Output "$($usqlFiles.Count) jobs to be submitted..."

   # Submit each usql script and wait for completion before moving ahead.
   foreach ($usqlFile in $usqlFiles)
   {
      $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

      Write-Output "Submitting job for '{$usqlFile}'"

      $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism

      LogJobInformation $jobToSubmit

      Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
      $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
      LogJobInformation $jobResult
   }
}

Function LogJobInformation($jobInfo)
{
   Write-Output "************************************************************************"
   Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
   Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
   Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
   Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
   Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
   Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
   Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
   if ($item -ne $null)
   {
      return $item
   }
   return ""
}

Function Main()
{
   Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
   Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
   Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

   Write-Output "Starting USQL script deployment..."

   SubmitAnalyticsJob

   Write-Output "Finished deployment..."
}

Main
```

>[!NOTE]
> De opdrachten: `Submit-AzDataLakeAnalyticsJob` en `Wait-AzDataLakeAnalyticsJob` zijn beide Azure PowerShell-cmdlets voor Azure data Lake Analytics in het Azure Resource Manager Framework. U vervolgens een werk station met Azure PowerShell geïnstalleerd. U kunt de [opdracht lijst](/powershell/module/Az.DataLakeAnalytics) raadplegen voor meer opdrachten en voor beelden.
>

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>U-SQL-taken implementeren via Azure Data Factory

U kunt u-SQL-taken rechtstreeks vanuit Azure-pijp lijnen indienen. U kunt ook de ingebouwde scripts uploaden naar Azure Data Lake Store of Azure Blob-opslag en [de geplande taken uitvoeren via Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md).

Gebruik de [Azure PowerShell taak](/azure/devops/pipelines/tasks/deploy/azure-powershell) in azure-pijp lijnen met het volgende Power shell-voorbeeld script voor het uploaden van de U-SQL-scripts naar een Azure data Lake Store-account:

```powershell
<#
   This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
   This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.

   Example :
      FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
   [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
   [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
   [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
   Write-Host "************************************************************************"
   Write-Host "Uploading files to $ADLSName"
   Write-Host "***********************************************************************"

   $usqlScripts = GetUsqlFiles

   $files = @(get-childitem $usqlScripts -recurse)
   foreach($file in $files)
   {
      Write-Host "Uploading file: $($file.Name)"
      Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
   }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
   $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
   Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
   Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

   $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

   $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

   foreach ($USQLPackfile in $USQLPackfiles)
   {
      Unzip $USQLPackfile $UnzipOutput
   }

   return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD voor een U-SQL database

Azure Data Lake-Hulpprogram Ma's voor Visual Studio biedt u-SQL database project sjablonen waarmee u u-SQL-data bases kunt ontwikkelen, beheren en implementeren. Meer informatie over een [U-SQL database project](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>U-SQL database project maken

### <a name="get-the-nuget-package"></a>Het NuGet-pakket ophalen

MSBuild biedt geen ingebouwde ondersteuning voor U-SQL database projecten. Om deze mogelijkheid te verkrijgen, moet u een verwijzing naar uw oplossing toevoegen aan het pakket [micro soft. Azure. DataLake. USQL. SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet waarmee de vereiste taal service wordt toegevoegd.

Als u de NuGet-pakket verwijzing wilt toevoegen, klikt u met de rechter muisknop op de oplossing in Visual Studio Solution Explorer. Kies **NuGet-pakketten beheren**. Zoek en installeer het NuGet-pakket. U kunt ook een bestand met de naam **packages.config** toevoegen aan de map Solution en de volgende inhoud in de oplossing plaatsen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>U-SQL een database project bouwen met de MSBuild opdracht regel

Als u uw U-SQL database project wilt maken, roept u de Standard MSBuild-opdracht regel aan en geeft U de micro soft SQL SDK NuGet-pakket verwijzing door met een extra argument. Zie het volgende voorbeeld: 

```console
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Het argument `USQLSDKPath=<U-SQL Nuget package>\build\runtime` verwijst naar het installatiepad van het NuGet-pakket voor de U-SQL-taal service.

### <a name="continuous-integration-with-azure-pipelines"></a>Continue integratie met Azure-pijplijnen

Naast de opdracht regel kunt u Visual Studio build of een MSBuild-taak gebruiken om U-SQL database projecten in azure-pijp lijnen te maken. Als u een build-taak wilt instellen, moet u twee taken toevoegen aan de build-pijp lijn: een NuGet-herstel taak en een MSBuild-taak.

   ![CI/CD MSBuild-taak voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png)

1. Voeg een NuGet-herstel taak toe voor het ophalen van het NuGet-pakket met oplossingen waarnaar wordt verwezen, `Azure.DataLake.USQL.SDK` zodat MSBuild de U-SQL-taal doelen kan vinden. Stel de **Geavanceerde**  >  **doelmap** in op `$(Build.SourcesDirectory)/packages` Als u het voor beeld van de MSBuild-argumenten rechtstreeks in stap 2 wilt gebruiken.

   ![CI/CD NuGet-taak voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Stel MSBuild-argumenten in Visual Studio build tools of in een MSBuild-taak in, zoals wordt weer gegeven in het volgende voor beeld. U kunt ook variabelen definiëren voor deze argumenten in de pijp lijn voor het bouwen van Azure-pijp lijnen.

   ![CI/CD MSBuild-variabelen definiëren voor een U-SQL database project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png)

   ```console
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```

### <a name="u-sql-database-project-build-output"></a>U-SQL database project build-uitvoer

Het build-uitvoer voor een U-SQL database project is een U-SQL database implementatie pakket met de naam met het achtervoegsel `.usqldbpack` . Het `.usqldbpack` pakket is een zip-bestand dat alle DDL-instructies bevat in één U-SQL-script in een DDL-map. Het bevat alle **. dll** -bestanden en aanvullende bestand voor assembly in een tijdelijke map.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Functies voor tabel waarden en opgeslagen procedures testen

Het is momenteel niet mogelijk om test cases toe te voegen voor functies met tabel waarden en opgeslagen procedures. Als tijdelijke oplossing kunt u een U-SQL-project maken met U-SQL-scripts die deze functies aanroepen en test cases voor hen schrijven. Voer de volgende stappen uit om test cases in te stellen voor tabelwaardefunctie en opgeslagen procedures die in het U-SQL database project zijn gedefinieerd:

1. Een U-SQL-project maken voor test doeleinden en U-SQL-scripts schrijven die de tabelwaardefunctie-functies en opgeslagen procedures aanroepen.

1. Voeg een database verwijzing toe aan het U-SQL-project. Als u de functie voor tabel waarden en de definitie van de opgeslagen procedure wilt ophalen, moet u verwijzen naar het database project dat de DDL-instructie bevat. Meer informatie over [database verwijzingen](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

1. Voeg test cases toe voor U-SQL-scripts die tabelwaardefunctie-functies en opgeslagen procedures aanroepen. Meer informatie over het [toevoegen van test cases voor U-SQL-scripts](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>U-SQL database implementeren via Azure-pijp lijnen

`PackageDeploymentTool.exe` biedt de programmeer-en opdracht regel interfaces die U helpen bij de implementatie van U-SQL database implementatie pakketten, **. usqldbpack**. De SDK is opgenomen in het [U-SQL SDK NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), dat zich bevindt op **build/runtime/PackageDeploymentTool.exe**. Met `PackageDeploymentTool.exe` kunt u u-SQL-data bases implementeren op zowel Azure data Lake Analytics als lokale accounts.

> [!NOTE]
>
> Power shell-opdracht regel ondersteuning en ondersteuning van de release taak van Azure pipelines voor U-SQL database-implementatie is momenteel in behandeling.
>

Voer de volgende stappen uit om een implementatie taak voor een data base in te stellen in azure-pijp lijnen:

1. Voeg een Power shell-script taak toe in een build of release-pijp lijn en voer het volgende Power shell-script uit. Deze taak helpt bij het ophalen van Azure SDK-afhankelijkheden voor `PackageDeploymentTool.exe` en `PackageDeploymentTool.exe` . U kunt de para meters **-AzureSDK** en **-DBDeploymentTool** instellen om de afhankelijkheden en het implementatie hulpprogramma te laden voor specifieke mappen. Geef het pad **-AzureSDK** naar `PackageDeploymentTool.exe` de para meter **-AzureSDKPath** in stap 2.

   ```powershell
   <#
      This script is used for getting dependencies and SDKs for U-SQL database deployment.
      PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.

      Example :
          GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
   #>

   param (
      [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
      [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
      [string]$workingfolder = "" # Folder to execute these command lines
   )

   if ([string]::IsNullOrEmpty($workingfolder))
   {
      $scriptpath = $MyInvocation.MyCommand.Path
      $workingfolder = Split-Path $scriptpath
   }
   cd $workingfolder

   echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
   echo "Downloading required packages..."

   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
   iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
   iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

   echo "Extracting packages..."

   Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
   Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
   Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
   Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
   Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
   Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

   echo "Copy required DLLs to output folder..."

   mkdir $AzureSDK -Force
   mkdir $DBDeploymentTool -Force
   copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
   copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
   copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
   copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
   copy USQLSDK\build\runtime\*.* $DBDeploymentTool
   ```

2. Voeg een **opdracht regel taak** toe in een build of release-pijp lijn en vul het script in door in te roepen `PackageDeploymentTool.exe` . `PackageDeploymentTool.exe` bevindt zich onder de gedefinieerde map **$DBDeploymentTool** . Het voorbeeld script is als volgt: 

   - Een U-SQL database lokaal implementeren:

      ```cmd
      PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
      ```

   - Interactieve verificatie modus gebruiken om een U-SQL database te implementeren op een Azure Data Lake Analytics-account:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
      ```

   - Gebruik **geheime** verificatie om een U-SQL database te implementeren op een Azure data Lake Analytics-account:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
      ```

   - Gebruik **CERT** -verificatie om een U-SQL database te implementeren op een Azure data Lake Analytics-account:

      ```cmd
      PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
      ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Beschrijvingen van PackageDeploymentTool.exe-para meters

#### <a name="common-parameters"></a>Algemene para meters

| Parameter | Beschrijving | Standaardwaarde | Vereist |
|---------|-----------|-------------|--------|
|Pakket|Het pad van het U-SQL database implementatie pakket dat moet worden geïmplementeerd.|null|true|
|Database|De naam van de data base die moet worden geïmplementeerd of gemaakt.|model|onjuist|
|Logbestand|Het pad van het bestand voor logboek registratie. Standaard ingesteld op standaard waarde (console).|null|false|
|Logniveau|Logboek niveau: uitgebreid, normaal, waarschuwing of fout.|LogLevel. Normal|onjuist|

#### <a name="parameter-for-local-deployment"></a>Para meter voor lokale implementatie

|Parameter|Beschrijving|Standaardwaarde|Vereist|
|---------|-----------|-------------|--------|
|Data root|Het pad van de lokale hoofdmap van de gegevens.|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Para meters voor Azure Data Lake Analytics implementatie

|Parameter|Beschrijving|Standaardwaarde|Vereist|
|---------|-----------|-------------|--------|
|Account|Hiermee geeft u op met welke Azure Data Lake Analytics-account moet worden geïmplementeerd op basis van de account naam.|null|true|
|ResourceGroup|De naam van de Azure-resource groep voor het Azure Data Lake Analytics-account.|null|true|
|SubscriptionId|De Azure-abonnements-ID voor het Azure Data Lake Analytics-account.|null|true|
|Tenant|De naam van de Tenant is de domein naam van de Azure Active Directory (Azure AD). Zoek het op de pagina abonnements beheer in het Azure Portal.|null|true|
|AzureSDKPath|Het pad voor het zoeken naar afhankelijke assembly's in de Azure SDK.|null|true|
|Interactief|Hiermee wordt aangegeven of interactieve modus moet worden gebruikt voor verificatie.|onjuist|onjuist|
|ClientId|De Azure AD-toepassings-ID die is vereist voor niet-interactieve verificatie.|null|Vereist voor niet-interactieve verificatie.|
|Geheim|Het geheim of het wacht woord voor niet-interactieve verificatie. Deze moet alleen worden gebruikt in een vertrouwde en beveiligde omgeving.|null|Vereist voor niet-interactieve verificatie, of gebruik SecreteFile.|
|SecreteFile|Het bestand slaat het geheim of het wacht woord voor niet-interactieve verificatie op. Zorg ervoor dat het alleen leesbaar is voor de huidige gebruiker.|null|Vereist voor niet-interactieve verificatie, of gebruik geheim.|
|CERT|Het bestand bespaart X. 509-certificering voor niet-interactieve verificatie. De standaard instelling is het gebruik van verificatie van client geheim.|null|false|
| JobPrefix | Het voor voegsel voor de implementatie van de data base van een U-SQL DDL-taak. | Deploy_ + DateTime. nu | onjuist |

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure data Lake Analytics code testen](data-lake-analytics-cicd-test.md).
- [Voer U-SQL-script uit op de lokale computer](data-lake-analytics-data-lake-tools-local-run.md).
- [Gebruik u-SQL database project om U-SQL database te ontwikkelen](data-lake-analytics-data-lake-tools-develop-usql-database.md).