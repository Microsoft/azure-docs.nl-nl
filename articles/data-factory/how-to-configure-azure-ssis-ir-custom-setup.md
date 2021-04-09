---
title: De instellingen voor een Azure-SSIS Integration Runtime aanpassen
description: In dit artikel wordt beschreven hoe u de aangepaste configuratie-interface gebruikt voor een Azure-SSIS Integration Runtime om extra onderdelen te installeren of instellingen te wijzigen
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387730"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>De instellingen voor een Azure-SSIS Integration Runtime aanpassen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

U kunt uw Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory (ADF) aanpassen via aangepaste instellingen. U kunt hiermee uw eigen stappen toevoegen tijdens het inrichten of opnieuw configureren van uw Azure-SSIS IR. 

Door aangepaste instellingen te gebruiken, kunt u de standaard configuratie van het besturings systeem of de omgeving van uw Azure-SSIS IR wijzigen. Als u bijvoorbeeld extra Windows-Services wilt starten, moet u toegangs referenties voor bestands shares behouden of gebruikmaken van sterke crypto grafie/veiliger netwerk protocol (TLS 1,2). U kunt ook extra onderdelen, zoals assembly's, stuur Programma's of uitbrei dingen, op elk knoop punt van uw Azure-SSIS IR installeren. Ze kunnen aangepaste onderdelen, open source-of componenten van derden zijn. Zie [Ingebouwde/vooraf geïnstalleerde onderdelen op Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md) voor meer informatie over ingebouwde en vooraf geïnstalleerde onderdelen.

U kunt op twee manieren aangepaste installatie op uw Azure-SSIS IR uitvoeren: 
* **Aangepaste standaard installatie met een script**: bereid een script en de bijbehorende bestanden voor en upload deze allemaal samen naar een BLOB-container in uw Azure Storage-account. Vervolgens geeft u een Shared Access Signature (SAS) Uniform Resource Identifier (URI) voor uw container op wanneer u uw Azure-SSIS IR instelt of opnieuw configureert. Elk knoop punt van uw Azure-SSIS IR downloadt vervolgens het script en de bijbehorende bestanden uit uw container en voert uw aangepaste installatie uit met verhoogde machtigingen. Wanneer uw aangepaste installatie is voltooid, uploadt elk knoop punt de standaard uitvoer van de uitvoering en andere logboeken naar uw container.
* **Snelle aangepaste installatie zonder script**: Voer enkele algemene systeem configuraties en Windows-opdrachten uit of installeer populaire of aanbevolen extra onderdelen zonder scripts te gebruiken.

U kunt gratis (zonder licentie) en betaalde (gelicentieerde) onderdelen installeren met standaard en snelle aangepaste installatie. Als u een onafhankelijke software leverancier (ISV) bent, raadpleegt u [betaalde of gelicentieerde onderdelen voor Azure-SSIS IR ontwikkelen](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Om te profiteren van toekomstige verbeteringen raden we u aan om een reeks van v3 of later te gebruiken voor uw Azure-SSIS IR met aangepaste installatie.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen gelden alleen voor standaard aangepaste Setup:

- Als u *gacutil.exe* in uw script wilt gebruiken voor het installeren van assembly's in de Global assembly cache (GAC), moet u *gacutil.exe* opgeven als onderdeel van de aangepaste installatie. U kunt ook de kopie gebruiken die wordt vermeld in de voor *beeld* -map van onze *open bare preview* -container. Hieronder vindt u de sectie met **standaard instellingen voor aangepaste installatie** .

- Als u wilt verwijzen naar een submap in uw script, ondersteunt *msiexec.exe* de notatie niet `.\` voor verwijzing naar de hoofdmap. Gebruik een opdracht, zoals `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` in plaats van `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` .

- Beheerders shares of verborgen netwerk shares die automatisch door Windows worden gemaakt, worden momenteel niet ondersteund op de Azure-SSIS IR.

- Het ODBC-stuur programma voor de IBM iSeries-toegang wordt niet ondersteund op de Azure-SSIS IR. Tijdens uw aangepaste installatie worden mogelijk installatie fouten weer geven. Neem contact op met IBM Support voor hulp.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u uw Azure-SSIS IR wilt aanpassen, hebt u de volgende items nodig:

- [Een Azure-abonnement](https://azure.microsoft.com/)

- [Uw Azure-SSIS IR inrichten](./tutorial-deploy-ssis-packages-azure.md)

- [Een Azure-opslag account](https://azure.microsoft.com/services/storage/). Niet vereist voor snelle aangepaste installatie. Voor aangepaste standaard instellingen uploadt en slaat u uw aangepaste installatie script en de bijbehorende bestanden op in een BLOB-container. Het aangepaste installatie proces uploadt ook de uitvoerings logboeken naar dezelfde BLOB-container.

## <a name="instructions"></a>Instructies

U kunt uw Azure-SSIS IR inrichten of opnieuw configureren met aangepaste Setup op de ADF-gebruikers interface. Als u hetzelfde wilt doen met behulp van Power shell, downloadt en installeert u [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="standard-custom-setup"></a>Aangepaste standaard installatie

Voer de volgende stappen uit om uw Azure-SSIS IR in te richten of opnieuw te configureren met standaard aangepaste Setup op de ADF-gebruikers interface.

1. Bereid uw aangepaste installatie script en de bijbehorende bestanden voor (bijvoorbeeld. bat,. cmd,. exe,. dll,. msi of. ps1-bestanden).

   * U moet een script bestand met de naam *Main. cmd* hebben. Dit is het toegangs punt van uw aangepaste installatie.  
   * Om ervoor te zorgen dat het script op de achtergrond kan worden uitgevoerd, moet u het eerst testen op uw lokale machine.  
   * Als u wilt dat extra logboeken die worden gegenereerd door andere hulpprogram ma's (bijvoorbeeld *msiexec.exe*) worden geüpload naar uw container, geeft u de vooraf gedefinieerde omgevings variabele op, `CUSTOM_SETUP_SCRIPT_LOG_DIR` als de logboekmap in uw scripts (bijvoorbeeld *msiexec/i xxx.msi/quiet/LV% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install. log*).

1. Down load, installeer en open [Azure Storage Explorer](https://storageexplorer.com/).

   a. Onder **(lokaal en gekoppeld)** klikt u met de rechter muisknop op **opslag accounts** en selecteert **u verbinding maken met Azure Storage**.

      ![Verbinding maken met Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Selecteer **een opslag accountnaam en-sleutel gebruiken** en selecteer **volgende**.

      ![De naam en sleutel van een opslagaccount gebruiken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Voer de naam en sleutel van uw Azure Storage-account in, selecteer **volgende** en selecteer vervolgens **verbinding maken**.

      ![Naam en sleutel van opslag account opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Klik onder uw verbonden Azure Storage-account met de rechter muisknop op **BLOB-containers**, selecteer **BLOB-container maken** en geef de nieuwe container een naam.

      ![Een blobcontainer maken](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Selecteer de nieuwe container en upload uw aangepaste installatie script en de bijbehorende bestanden. Zorg ervoor dat u *Main. cmd* op het hoogste niveau van uw container uploadt, niet in een map. De container mag alleen de benodigde aangepaste installatie bestanden bevatten, dus het downloaden van deze naar uw Azure-SSIS IR kan later niet lang duren. De maximale duur van een aangepaste installatie is momenteel ingesteld op 45 minuten voordat er een time-out optreedt. Dit omvat de tijd voor het downloaden van alle bestanden uit uw container en het installeren ervan op het Azure-SSIS IR. Als er meer tijd nodig is, kunt u een ondersteunings ticket genereren.

      ![Bestanden uploaden naar de BLOB-container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Klik met de rechter muisknop op de container en selecteer vervolgens **Shared Access Signature ophalen**.

      ![De Shared Access Signature voor de container ophalen](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Maak de SAS-URI voor uw container met een voldoende lange verloop tijd en met de machtiging lezen/schrijven/lijst. U hebt de SAS-URI nodig om uw aangepaste installatie script en de bijbehorende bestanden te downloaden en uit te voeren. Dit gebeurt wanneer een knoop punt van uw Azure-SSIS IR wordt geimageeerd of opnieuw wordt gestart. U moet ook schrijf machtiging hebben om Setup-uitvoerings logboeken te uploaden.

      > [!IMPORTANT]
      > Zorg ervoor dat de SAS-URI niet verlopen en dat de aangepaste installatie resources altijd beschikbaar zijn tijdens de hele levens cyclus van uw Azure-SSIS IR, van het maken tot verwijderen, met name als u uw Azure-SSIS IR tijdens deze periode regel matig stopt en start.

      ![De Shared Access Signature voor de container genereren](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopieer de SAS-URI van uw container en sla deze op.

      ![De Shared Access Signature kopiëren en opslaan](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Schakel het selectie vakje **uw Azure-SSIS Integration runtime aanpassen met aanvullende systeem configuraties/onderdeel installaties** in op de pagina **Geavanceerde instellingen** van het deel venster **Setup van Integration runtime** . Voer vervolgens de SAS-URI van uw container in het tekstvak **aangepaste installatie container SAS URI** in.

   ![Geavanceerde instellingen met aangepaste installatie](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

Nadat de aangepaste standaard installatie is voltooid en uw Azure-SSIS IR wordt gestart, vindt u alle aangepaste installatie Logboeken in de map *Main. cmd. log* van uw container. Ze bevatten de standaard uitvoer van *Main. cmd* en andere uitvoerings Logboeken.

### <a name="express-custom-setup"></a>Snelle aangepaste installatie

Voer de volgende stappen uit om uw Azure-SSIS IR in te richten of opnieuw te configureren met snelle aangepaste installatie in de ADF-gebruikers interface.

1. Schakel het selectie vakje **uw Azure-SSIS Integration runtime aanpassen met aanvullende systeem configuraties/onderdeel installaties** in op de pagina **Geavanceerde instellingen** van het deel venster **Setup van Integration runtime** . 

1. Selecteer **Nieuw** om het deel venster **snelle aangepaste installatie toevoegen** te openen en selecteer vervolgens een type in de vervolg keuzelijst **type snelle aangepaste installatie** . Momenteel bieden we expliciet aangepaste Setup aan voor het uitvoeren van de opdracht cmdkey, het toevoegen van omgevings variabelen, het installeren van Azure PowerShell en het installeren van gelicentieerde onderdelen.

#### <a name="running-cmdkey-command"></a>Opdracht cmdkey uitvoeren

Als u het **opdracht cmdkey uitvoeren** selecteert voor uw snelle aangepaste installatie, kunt u de Windows cmdkey-opdracht uitvoeren op uw Azure-SSIS IR. Hiertoe voert u de naam van uw doel computer of de domein naam, de gebruikers naam, de accountnaam en het wacht woord of de account sleutel in de tekst vakken **/add**, **/User** en **/Pass** in. Zo kunt u toegangs referenties voor SQL-servers, bestands shares of Azure Files op uw Azure-SSIS IR behouden. Als u bijvoorbeeld toegang wilt krijgen tot Azure Files, kunt `YourAzureStorageAccountName.file.core.windows.net` u `azure\YourAzureStorageAccountName` respectievelijk, en `YourAzureStorageAccountKey` voor **/add**, **/User** en **/Pass** invoeren. Dit is vergelijkbaar met het uitvoeren van de Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) -opdracht op uw lokale computer. Slechts één snelle aangepaste installatie voor het uitvoeren van de opdracht cmdkey wordt nu ondersteund. Als u meerdere cmdkey-opdrachten wilt uitvoeren, gebruikt u in plaats daarvan een aangepaste standaard installatie.

#### <a name="adding-environment-variables"></a>Omgevings variabelen toevoegen

Als u het type **omgevings variabele toevoegen** voor uw snelle aangepaste installatie selecteert, kunt u een Windows-omgevings variabele toevoegen aan uw Azure-SSIS IR. Hiervoor voert u de naam en waarde van de omgevings variabele in de tekst vakken **variabele naam** en **variabele waarde** in. Zo kunt u de omgevings variabele gebruiken in uw pakketten die worden uitgevoerd op Azure-SSIS IR, bijvoorbeeld in script onderdelen/taken. Dit is vergelijkbaar met het uitvoeren van de Windows-opdracht [set](/windows-server/administration/windows-commands/set_1) op uw lokale computer.

#### <a name="installing-azure-powershell"></a>Azure PowerShell installeren

Als u het **Azure PowerShell type installeren** selecteert voor uw snelle aangepaste installatie, kunt u de AZ-module van Power shell op uw Azure-SSIS IR installeren. Als u dit wilt doen, voert u het versie nummer AZ module (x. y. z) uit een [lijst met ondersteunde items](https://www.powershellgallery.com/packages/az)in. Hiermee kunt u Azure PowerShell-cmdlets/scripts in uw pakketten uitvoeren om Azure-resources te beheren, bijvoorbeeld [Azure Analysis Services (aas)](../analysis-services/analysis-services-powershell.md).

#### <a name="installing-licensed-components"></a>Gelicentieerde onderdelen installeren

Als u het **onderdeel type licenties installeren** selecteert voor uw snelle aangepaste installatie, kunt u vervolgens een geïntegreerd onderdeel selecteren in onze ISV-partners in de vervolg keuzelijst **onderdeel naam** :

   * Als u het onderdeel **Task Factory van SentryOne** selecteert, kunt u de [taken fabrieks](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) suite van onderdelen van SentryOne op uw Azure-SSIS IR installeren. Als u dit wilt doen, voert u de product licentie code die u hebt aangeschaft vooraf in het tekstvak **licentie code** in. De huidige geïntegreerde versie is **2020.1.3**.

   * Als u de **oh22's-HEDDA selecteert. IO** -onderdeel kunt u de [HEDDA installeren.](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) Het onderdeel io-gegevens kwaliteit/opschoning van oh22 op uw Azure-SSIS IR. Om dit te doen, moet u de service vooraf aanschaffen. De huidige geïntegreerde versie is **1.0.14**.

   * Als u het **oh22's SQLPhonetics.net** -onderdeel selecteert, kunt u het [SQLPhonetics.net](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) Data Quality/matching-onderdeel van oh22 op uw Azure-SSIS IR installeren. Als u dit wilt doen, voert u de product licentie code die u hebt aangeschaft vooraf in het tekstvak **licentie code** in. De huidige geïntegreerde versie is **1.0.45**.

   * Als u het **SSIS Integration Toolkit** -onderdeel van de KingswaySoft selecteert, kunt u het pakket met de [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) van connectors voor CRM/ERP/marketing/samenwerkings-apps installeren, zoals micro soft Dynamics/share point/Project Server, Oracle/Sales Force marketing Cloud, enzovoort van KingswaySoft op uw Azure-SSIS IR. Als u dit wilt doen, voert u de product licentie code die u hebt aangeschaft vooraf in het tekstvak **licentie code** in. De huidige geïntegreerde versie is **2020,1**.

   * Als u het **SSIS Productivity Pack** -onderdeel van de KingswaySoft selecteert, kunt u de [SSIS-productiviteits pakket](https://www.kingswaysoft.com/products/ssis-productivity-pack) onderdelen van KingswaySoft op uw Azure-SSIS IR installeren. Als u dit wilt doen, voert u de product licentie code die u hebt aangeschaft vooraf in het tekstvak **licentie code** in. De huidige geïntegreerde versie is **20,1**.

   * Als u het onderdeel **Xtract van de Theobald-software** selecteert, kunt u de [Xtract](https://theobald-software.com/en/xtract-is/) een suite van connectors voor SAP-systemen (ERP, s/4HANA, BW) installeren van de Theobald-software op uw Azure-SSIS IR. Als u dit wilt doen, sleept u & het product licentie bestand dat u eerder hebt aangeschaft, naar het invoervak **licentie bestand** te slepen. De huidige geïntegreerde versie is **6.1.1.3**.

   * Als u het **Integration service** -onderdeel van AecorSoft selecteert, kunt u het [integratie service](https://www.aecorsoft.com/en/products/integrationservice) pakket van connectors voor SAP-en Sales Force-systemen installeren vanuit AecorSoft op uw Azure-SSIS IR. Als u dit wilt doen, voert u de product licentie code die u hebt aangeschaft vooraf in het tekstvak **licentie code** in. De huidige geïntegreerde versie is **3.0.00**.

   * Als u het **SSIS-standaard pakket** onderdeel voor CData selecteert, kunt u de [SSIS Standard-pakket](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) suite van de populairste onderdelen van CDATA installeren, zoals micro soft share point-connectors, op uw Azure-SSIS IR. Als u dit wilt doen, voert u de product licentie code die u hebt aangeschaft vooraf in het tekstvak **licentie code** in. De huidige geïntegreerde versie is **19,7354**.

   * Als u het **Extended SSIS-pakket** onderdeel van CDATA selecteert, kunt u de [SSIS Extended package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) suite van alle onderdelen van CData installeren, zoals micro soft Dynamics 365 Business Central connectors en andere onderdelen in hun **SSIS-standaard pakket**, op uw Azure-SSIS IR. Als u dit wilt doen, voert u de product licentie code die u hebt aangeschaft vooraf in het tekstvak **licentie code** in. De huidige geïntegreerde versie is **19,7354**. Als gevolg van de omvang van de installatie, moet u ervoor zorgen dat uw Azure-SSIS IR ten minste 4 CPU-kernen per knoop punt heeft.

Uw toegevoegde snelle aangepaste Setup wordt weer gegeven op de pagina **Geavanceerde instellingen** . Als u deze wilt verwijderen, schakelt u de selectie vakjes in en selecteert u **Verwijderen**.

### <a name="azure-powershell"></a>Azure PowerShell

Voer de volgende stappen uit om uw Azure-SSIS IR in te richten of opnieuw te configureren met aangepaste setups met behulp van Azure PowerShell.

1. Als uw Azure-SSIS IR al is gestart of wordt uitgevoerd, stopt u het eerst.

1. U kunt vervolgens aangepaste Setup toevoegen of verwijderen door de cmdlet uit te voeren `Set-AzDataFactoryV2IntegrationRuntime` voordat u begint met het Azure-SSIS IR.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Standaard aangepaste installatie voorbeelden

Voer de volgende stappen uit om enkele voor beelden van aangepaste standaard instellingen weer te geven en opnieuw te gebruiken.

1. Maak verbinding met onze open bare preview-container met behulp van Azure Storage Explorer.

   a. Onder **(lokaal en gekoppeld)** klikt u met de rechter muisknop op **opslag accounts**, selecteert **u verbinding maken met Azure Storage**, selecteert **u een Connection String of een URI voor de hand tekening voor gedeelde toegang gebruiken** en selecteert u vervolgens **volgende**.

      ![Verbinding maken met Azure Storage met de Shared Access Signature](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Selecteer **een SAS-URI gebruiken** en voer in het tekstvak **URI** de volgende SAS-URI in:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![De Shared Access Signature voor de container opgeven](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Selecteer **volgende** en selecteer vervolgens **verbinding maken**.

   d. Selecteer in het linkerdeel venster de container Connected **publicpreview** en dubbel klik vervolgens op de map *CustomSetupScript* . In deze map zijn de volgende items:

      * Een voor *beeld* van een map, die een aangepaste installatie bevat voor het installeren van een basis taak op elk knoop punt van uw Azure-SSIS IR. De taak heeft niets maar een paar seconden in de slaap stand. De map bevat ook een map *gacutil* , waarvan de volledige inhoud (*gacutil.exe*, *gacutil.exe.config* en *1033\gacutlrc.dll*) naar uw container kan worden gekopieerd.

      * Een *UserScenarios* -map, die verschillende aangepaste installatie voorbeelden bevat uit echte gebruikers scenario's. Als u meerdere voor beelden op uw Azure-SSIS IR wilt installeren, kunt u de aangepaste installatie script bestanden (*Main. cmd*) samen voegen met één en uploaden naar de container met alle bijbehorende bestanden.

        ![Inhoud van de open bare preview-container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Dubbel klik op de map *UserScenarios* om de volgende items te zoeken:

      * Een *.NET FRAMEWORK 3,5* -map die een aangepast installatie script (*Main. cmd*) bevat voor de installatie van een eerdere versie van de .NET Framework op elk knoop punt van uw Azure-SSIS IR. Deze versie is mogelijk vereist voor sommige aangepaste onderdelen.

      * Een *bcp* -map die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van SQL Server opdracht regel programma's (*MsSqlCmdLnUtils.msi*) op elk knoop punt van uw Azure-SSIS IR. Een van deze hulpprogram ma's is het pakket voor bulksgewijs kopiëren (*bcp*).

      * Een *DNS-achtervoegsel* , dat een aangepast installatie script (*Main. cmd*) bevat om uw eigen DNS-achtervoegsel (bijvoorbeeld *test.com*) toe te voegen aan een niet-gekwalificeerde domein naam met één label en deze in te scha kelen in een FQDN-naam (Fully Qualified Domain Name) voordat u deze in DNS-query's van uw Azure-SSIS IR gebruikt.

      * Een *Excel* -map, die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van enkele C#-assembly's en-bibliotheken op elk knoop punt van uw Azure-SSIS IR. U kunt ze gebruiken in script taken om Excel-bestanden dynamisch te lezen en te schrijven. 
      
        Down load eerst [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) en [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)en upload deze allemaal samen met *Main. cmd* naar uw container. Als u alleen de standaard-Excel-connectors wilt gebruiken (verbindings beheer, bron en doel), is het toegangs herdistribueerte bestand dat deze bevat, al vooraf geïnstalleerd op uw Azure-SSIS IR, zodat u geen aangepaste installatie hoeft te installeren.
      
      * Een *MySQL ODBC-* map, die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van de MySQL ODBC-stuur Programma's op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ODBC-connectors (verbindings beheer, bron en doel) gebruiken om verbinding te maken met de MySQL-server. 
     
        [Down load eerst de nieuwste 64-bits en 32-bits versies van de MySQL ODBC-stuur Programma's](https://dev.mysql.com/downloads/connector/odbc/) (bijvoorbeeld *mysql-connector-odbc-8.0.13-winx64.msi* en *mysql-connector-odbc-8.0.13-win32.msi*) en upload deze allemaal samen met *Main. cmd* naar uw container.

      * Een *Oracle Enter prise* -map, die een aangepast installatie script (*Main. cmd*) en een installatie configuratie bestand (*client. RSP*) bevat waarmee de Oracle-connectors en het OCI-stuur programma kunnen worden geïnstalleerd op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de Oracle-verbindings beheer,-bron en-bestemming gebruiken om verbinding te maken met de Oracle-server. 
      
        Down load eerst micro soft-connectors v 5.0 voor Oracle (*AttunitySSISOraAdaptersSetup.msi* en *AttunitySSISOraAdaptersSetup64.msi*) van het [micro soft Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=55179) en de nieuwste Oracle-client (bijvoorbeeld *winx64_12102_client.zip*) van [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html). Vervolgens uploadt u deze allemaal samen met *Main. cmd* en *client. RSP* naar uw container. Als u gebruikmaakt van TNS om verbinding te maken met Oracle, moet u ook *bestand Tnsnames. ora* downloaden, bewerken en uploaden naar uw container. Op deze manier kan deze tijdens de installatie worden gekopieerd naar de map Oracle-installatie.

      * Een *Oracle STANDARD ADO.net* -map met een aangepast installatie script (*Main. cmd*) om het Oracle ODP.net-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ADO.NET-verbindings beheer,-bron en-bestemming gebruiken om verbinding te maken met de Oracle-server. 
      
        [Down load eerst het meest recente Oracle ODP.net-stuur programma](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (bijvoorbeeld *ODP.NET_Managed_ODAC122cR1.zip*) en upload het vervolgens samen met *Main. cmd* naar uw container.
       
      * Een *ODBC* -standaardmap voor Oracle, die een aangepast installatie script (*Main. cmd*) bevat om het Oracle ODBC-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met het script wordt ook de naam van de gegevens bron (DSN) geconfigureerd. Met deze installatie kunt u de ODBC-verbindings beheer, de bron, de doel-of Power Query verbindings beheer en de bron met het ODBC-gegevens bron type gebruiken om verbinding te maken met de Oracle-server. 
      
        Down load eerst de nieuwste Oracle Instant-client (Basic package of Basic Lite package) en het ODBC-pakket en upload deze allemaal samen met *Main. cmd* naar uw container:
        * [64-bits pakketten downloaden](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-pakket: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Basic Lite-pakket: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC-pakket: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [32-bits pakketten downloaden](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic-pakket: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Basic Lite-pakket: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; ODBC-pakket: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Een *Oracle Standard OLEDB* -map, die een aangepast installatie script (*Main. cmd*) bevat om het Oracle OLEDB-stuur programma te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de OLEDB-verbindings beheer, de bron en de bestemming gebruiken om verbinding te maken met de Oracle-server. 
     
        [Down load eerst het nieuwste Oracle OLEDB-stuur programma](https://www.oracle.com/partners/campaign/index-090165.html) (bijvoorbeeld *ODAC122010Xcopy_x64.zip*) en upload het vervolgens samen met *Main. cmd* naar uw container.

      * Een *POSTGRESQL ODBC* -map, die een aangepast installatie script (*Main. cmd*) bevat om de POSTGRESQL ODBC-stuur Programma's te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u de ODBC-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de PostgreSQL-server. 
     
        [Down load eerst de nieuwste 64-bits en 32-bits versies van postgresql ODBC-stuur programma-installatie Programma's](https://www.postgresql.org/ftp/odbc/versions/msi/) (bijvoorbeeld *psqlodbc_x64.msi* en *psqlodbc_x86.msi*) en upload deze allemaal samen met *Main. cmd* naar uw container.

      * Een *SAP BW* map, die een aangepast installatie script (*Main. cmd*) bevat voor het installeren van de SAP .net-connector-Assembly (*librfc32.dll*) op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de SAP BW verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de SAP BW-server. 
      
        Upload eerst de 64-bits of de 32-bits versie van *librfc32.dll* vanuit de SAP-installatiemap samen met *Main. cmd* naar uw container. Het script kopieert vervolgens de SAP-assembly naar de map *%windir%\syswow64* of *%windir%\System32* tijdens de installatie.

      * Een *opslagmap* , die een aangepast installatie script (*Main. cmd*) bevat om Azure PowerShell te installeren op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u SSIS-pakketten implementeren en uitvoeren die [Azure PowerShell-cmdlets/scripts uitvoeren voor het beheren van uw Azure Storage](../storage/blobs/storage-quickstart-blobs-powershell.md). 
      
        Kopieer *Main. cmd*, een voor beeld *AzurePowerShell.msi* (of gebruik de meest recente versie) en *storage.ps1* naar uw container. Gebruik *Power shell. dtsx* als een sjabloon voor uw pakketten. De pakket sjabloon bevat een combi natie van een [Azure Blob-Download taak](/sql/integration-services/control-flow/azure-blob-download-task), waarmee een aanpas bare Power shell-script (*storage.ps1*) en een taak voor het uitvoeren van een [proces](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)worden gedownload, waarmee het script wordt uitgevoerd op elk knoop punt.

      * Een *TERADATA* -map die een aangepast installatie script (*Main. cmd*) bevat, het bijbehorende bestand (*install. cmd*) en de installatie pakketten (*. msi*). Met deze bestanden worden de Teradata-connectors, de TPT-API (parallel trans Porter) en het ODBC-stuur programma geïnstalleerd op elk knoop punt van uw Azure-SSIS IR Enter prise-editie. Met deze installatie kunt u de Teradata-verbindings beheer, bron en bestemming gebruiken om verbinding te maken met de Teradata-server. 
      
        [Down load eerst de Teradata-Hulpprogram ma's en Hulpprogram ma's 15. x zip-bestand](http://partnerintelligence.teradata.com) (bijvoorbeeld *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*) en upload het vervolgens samen met de eerder genoemde *. cmd* -en *MSI* -bestanden naar de container.

      * Een *TLS 1,2* -map, die een aangepast installatie script (*Main. cmd*) bevat voor het gebruik van sterke crypto grafie en veiliger netwerk protocol (TLS 1,2) op elk knoop punt van uw Azure-SSIS IR. Met het script worden ook oudere SSL/TLS-versies uitgeschakeld.

      * Een *ZULU OPENJDK* -map met een aangepast installatie script (*Main. cmd*) en Power shell-bestand (*install_openjdk.ps1*) voor het installeren van de ZULU OPENJDK op elk knoop punt van uw Azure-SSIS IR. Met deze installatie kunt u Azure Data Lake Store en flexibele bestands connectors gebruiken voor het verwerken van ORC-en Parquet-bestanden. Zie [Azure Feature Pack voor integratie Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java)voor meer informatie. 
      
        [Down load eerst de nieuwste Zulu-openjdk](https://www.azul.com/downloads/zulu/zulu-windows/) (bijvoorbeeld *zulu8.33.0.1-jdk8.0.192-win_x64.zip*) en upload deze vervolgens samen met *Main. cmd* en *install_openjdk.ps1* naar uw container.

        ![Mappen in de map gebruikers scenario's](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Als u deze standaard instellingen voor aangepaste installatie opnieuw wilt gebruiken, kopieert u de inhoud van de geselecteerde map naar uw container.

1. Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert op de ADF-gebruikers interface, schakelt u het selectie vakje **uw Azure-SSIS Integration runtime aanpassen met aanvullende systeem configuraties/onderdeel installaties** in op de pagina **Geavanceerde instellingen** van het deel venster **Setup van Integration runtime** . Voer vervolgens de SAS-URI van uw container in het tekstvak **aangepaste installatie container SAS URI** in.
   
1. Wanneer u uw Azure-SSIS IR inricht of opnieuw configureert met behulp van Azure PowerShell, stopt u het als het al is gestart of wordt uitgevoerd, voert u de `Set-AzDataFactoryV2IntegrationRuntime` cmdlet uit met de SAS-URI van uw container als waarde voor `SetupScriptContainerSasUri` para meter en start u vervolgens uw Azure-SSIS IR.

1. Nadat de aangepaste standaard installatie is voltooid en uw Azure-SSIS IR wordt gestart, vindt u alle aangepaste installatie Logboeken in de map *Main. cmd. log* van uw container. Ze bevatten de standaard uitvoer van *Main. cmd* en andere uitvoerings Logboeken.

## <a name="next-steps"></a>Volgende stappen

- [De Enter prise-editie van Azure-SSIS IR instellen](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Betaalde of gelicentieerde onderdelen ontwikkelen voor Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
