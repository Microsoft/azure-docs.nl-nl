---
title: Een zelf-hostende Integration runtime configureren als proxy voor SSIS
description: Meer informatie over het configureren van een zelf-hostende Integration runtime als een proxy voor een Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/19/2020
ms.openlocfilehash: 82cc58d46061ec7b623d062ab0b0e5a1fdae7ddd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352215"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Een zelf-hostende IR configureren als proxy voor een Azure-SSIS IR in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u SQL Server Integration Services (SSIS)-pakketten uitvoert op een Azure-SSIS Integration Runtime (Azure-SSIS IR) in Azure Data Factory met een zelf-hostende Integration runtime (zelf-hostende IR) die is geconfigureerd als proxy. 

Met deze functie kunt u on-premises toegang krijgen tot gegevens zonder dat u [uw Azure-SSIS IR hoeft te koppelen aan een virtueel netwerk](./join-azure-ssis-integration-runtime-virtual-network.md). De functie is handig wanneer uw bedrijfs netwerk een configuratie te complex heeft of een beleid dat te beperkend is voor u om uw Azure-SSIS IR te injecteren.

Deze functie onderbreekt uw SSIS data flow-taak in twee staging-taken wanneer dit van toepassing is: 
* **On-premises faserings taak**: met deze taak wordt uw gegevensstroom onderdeel uitgevoerd dat verbinding maakt met een on-premises gegevens archief op uw zelf-hostende IR. De gegevens worden verplaatst van het on-premises gegevens archief naar een faserings gebied in uw Azure Blob-opslag of andersom.
* **Faserings taak** voor de Cloud: met deze taak wordt het gegevens stroom onderdeel uitgevoerd dat geen verbinding maakt met een on-premises gegevens archief op uw Azure-SSIS IR. De gegevens worden verplaatst van het faserings gebied in uw Azure Blob-opslag naar een gegevens opslag in de Cloud of andersom.

Als uw gegevens stroom taak gegevens verplaatst van on-premises naar de Cloud, zullen de eerste en tweede faserings taken respectievelijk on-premises en in de cloud worden geplaatst. Als uw gegevens stroom taak gegevens verplaatst van de Cloud naar on-premises, worden de eerste en tweede faserings taken respectievelijk de Cloud en on-premises faserings taken. Als uw gegevens stroom taak gegevens verplaatst van on-premises naar on-premises, zijn de eerste en tweede staging-taken beide lokale faserings taken. Als uw gegevens stroom taak gegevens verplaatst van de Cloud naar de Cloud, is deze functie niet van toepassing.

Met andere voor delen en mogelijkheden van deze functie kunt u bijvoorbeeld uw zelf-hostende IR instellen in regio's die nog niet door een Azure-SSIS IR worden ondersteund, en het open bare statische IP-adres van uw zelf-hostende IR toestaan op de firewall van uw gegevens bronnen.

## <a name="prepare-the-self-hosted-ir"></a>De zelf-hostende IR voorbereiden

Als u deze functie wilt gebruiken, maakt u eerst een data factory en stelt u een Azure-SSIS IR in. Als u dit nog niet hebt gedaan, volgt u de instructies in [een Azure-SSIS IR instellen](./tutorial-deploy-ssis-packages-azure.md).

Vervolgens stelt u uw zelf-hostende IR in op dezelfde data factory waar uw Azure-SSIS IR is ingesteld. Zie hiervoor [een zelf-hostende IR maken](./create-self-hosted-integration-runtime.md).

Ten slotte downloadt en installeert u de nieuwste versie van de zelf-hostende IR, evenals de extra Stuur Programma's en runtime op uw on-premises machine of virtuele Azure-machine (VM), als volgt:
- Down load en installeer de nieuwste versie van de [zelf-hostende IR](https://www.microsoft.com/download/details.aspx?id=39717).
- Als u gebruikmaakt van object linking en Embedding data base (OLEDB), Open Database Connectivity (ODBC) of ADO.NET-connectors in uw pakketten, downloadt en installeert u de relevante Stuur Programma's op dezelfde computer waarop uw zelf-hostende IR is geïnstalleerd, als u dit nog niet hebt gedaan.  

  Als u de eerdere versie van het OLEDB-stuur programma voor SQL Server (SQL Server Native Client [SQLNCLI]) gebruikt, [downloadt u de 64-bits versie](https://www.microsoft.com/download/details.aspx?id=50402).  

  Als u de nieuwste versie van OLEDB-stuur programma voor SQL Server (MSOLEDBSQL) gebruikt, [downloadt u de 64-bits versie](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Als u OLEDB/ODBC/ADO. NET-Stuur Programma's gebruikt voor andere database systemen, zoals PostgreSQL, MySQL, Oracle, enzovoort, kunt u de 64-bits versies downloaden van hun websites.
- Als u dit nog niet hebt gedaan, [downloadt en installeert u de 64-bits versie van de Visual C++ (VC) runtime](https://www.microsoft.com/download/details.aspx?id=40784) op dezelfde computer waarop uw zelf-hostende IR is geïnstalleerd.

### <a name="enable-windows-authentication-for-on-premises-staging-tasks"></a>Windows-verificatie inschakelen voor on-premises faserings taken

Als voor on-premises staging-taken op uw zelf-hostende IR Windows-verificatie is vereist, [configureert u uw SSIS-pakketten voor gebruik van dezelfde Windows-verificatie](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Uw on-premises faserings taken worden aangeroepen met het zelf-hostende IR-service account (standaard *NT SERVICE\DIAHostService*) en uw gegevens archieven worden geopend met het Windows-verificatie account. Voor beide accounts moet een bepaald beveiligings beleid worden toegewezen. Op de zelf-hostende IR-computer gaat u naar **lokaal**  >  **beleid lokale beleids regels**  >  **toewijzing van gebruikers rechten** en gaat u als volgt te werk:

1. Wijs de *geheugen quota voor het aanpassen van een proces* toe en *Vervang een token beleid op proces niveau* naar het zelf-hostende IR-service account. Dit moet automatisch gebeuren wanneer u uw zelf-hostende IR installeert met het standaard service account. Als dat niet het geval is, moet u dit beleid hand matig toewijzen. Als u een ander service account gebruikt, wijst u hieraan hetzelfde beleid toe.

1. Wijs het *Aanmelden als service* beleid toe aan het Windows-verificatie account.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>De gekoppelde Azure Blob Storage-service voorbereiden voor fase ring

Als u dit nog niet hebt gedaan, maakt u een gekoppelde Azure Blob Storage-service in hetzelfde data factory waar uw Azure-SSIS IR is ingesteld. Zie hiervoor [een Azure Data Factory-gekoppelde service maken](./quickstart-create-data-factory-portal.md#create-a-linked-service). Zorg ervoor dat u het volgende doet:
- Selecteer **Azure Blob Storage** voor **gegevens opslag**.  
- Voor **verbinden via Integration runtime** selecteert u **AutoResolveIntegrationRuntime** (niet uw Azure-SSIS IR en uw zelf-hostende IR), omdat we de standaard Azure IR gebruiken om toegangs referenties voor uw Azure Blob Storage op te halen.
- Selecteer voor **verificatie methode** **account sleutel**, **SAS-URI**, **Service-Principal** of **beheerde identiteit**.  

>[!TIP]
>Als u de **Service-Principal** -methode selecteert, geeft u de Service-Principal ten minste een rol voor *Storage BLOB data Inzender* . Zie [Azure Blob Storage-connector](connector-azure-blob-storage.md#linked-service-properties)voor meer informatie. Als u de methode **beheerde identiteit** selecteert, verleen uw ADF beheerde identiteit de juiste rollen om toegang te krijgen tot Azure Blob Storage. Zie [toegang tot Azure Blob Storage met behulp van Azure Active Directory-verificatie met beheerde identiteit voor ADF](/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-ver15#managed-identities-for-azure-resources-authentication)voor meer informatie.

![De aan Azure Blob Storage gekoppelde service voorbereiden voor fase ring](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Een Azure-SSIS IR met uw zelf-hostende IR configureren als proxy

Als u uw zelf-hostende IR-en Azure Blob Storage-gekoppelde service voor fase ring hebt voor bereid, kunt u nu uw nieuwe of bestaande Azure-SSIS IR configureren met de zelf-hostende IR als proxy in uw data factory-portal of-app. Voordat u dit doet, moet u, als uw bestaande Azure-SSIS IR al wordt uitgevoerd, stoppen en opnieuw starten.

1. In het deel venster **Setup van Integration runtime** gaat u verder met de secties **algemene instellingen** en **SQL-instellingen** door **volgende** te selecteren. 

1. Ga als volgt te werk in de sectie **Geavanceerde instellingen** :

   1. Schakel het selectie vakje **Self-Hosted Integration runtime instellen als proxy voor uw Azure-SSIS Integration runtime** in. 

   1. Selecteer in de vervolg keuzelijst **zelf-hostende Integration runtime** uw bestaande zelf-hostende IR als proxy voor de Azure-SSIS IR.

   1. Selecteer in de vervolg keuzelijst **faserings opslag gekoppelde service** uw bestaande Azure Blob Storage-gekoppelde service of maak een nieuwe voor fase ring.

   1. Geef in het vak **pad voor gefaseerde installatie** een BLOB-container op in het geselecteerde Azure Blob Storage-account of laat het veld leeg om een standaard waarde te gebruiken voor fase ring.

   1. Selecteer **Doorgaan**.

   ![Geavanceerde instellingen met een zelf-hostende IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

U kunt uw nieuwe of bestaande Azure-SSIS IR ook configureren met de zelf-hostende IR als een proxy met behulp van Power shell.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS-pakketten inschakelen om verbinding te maken via proxy

Door de meest recente SSDT te gebruiken als de extensie voor de SSIS-projecten voor Visual Studio of een zelfstandig installatie programma, kunt u een nieuwe `ConnectByProxy` eigenschap vinden die is toegevoegd aan de verbindings beheer Programma's voor ondersteunde gegevens stroom onderdelen.
* [De SSIS projects-extensie voor Visual Studio downloaden](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Het zelfstandige installatie programma downloaden](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Wanneer u nieuwe pakketten met gegevens stroom taken ontwerpt met onderdelen die toegang hebben tot gegevens on-premises, kunt u deze eigenschap inschakelen door deze in te stellen op *True* in het deel venster **Eigenschappen** van de relevante verbindings beheer Programma's.

![De eigenschap ConnectByProxy inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

U kunt deze eigenschap ook inschakelen wanneer u bestaande pakketten uitvoert, zonder dat u deze hand matig hoeft te wijzigen.  Er zijn twee opties:
- **Optie A**: open, Rebuild en implementeer het project met de pakketten met de meest recente SSDT om uit te voeren op uw Azure-SSIS IR. U kunt de eigenschap vervolgens inschakelen door deze in te stellen op *True* voor de relevante verbindings beheer Programma's. Wanneer u pakketten uit SSMS uitvoert, worden deze verbindings beheer weer gegeven op het tabblad **verbindings beheer** van het pop-upvenster **pakket uitvoeren** .

  ![ConnectByProxy property2 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  U kunt de eigenschap ook inschakelen door deze in te stellen op *True* voor de relevante verbindings beheer die wordt weer gegeven op het tabblad **verbindings beheer** van de [activiteit uitvoeren SSIS-pakket](./how-to-invoke-ssis-package-ssis-activity.md) wanneer u pakketten uitvoert in Data Factory pijp lijnen.
  
  ![ConnectByProxy property3 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **Optie B:** Implementeer het project met de pakketten opnieuw om het uit te voeren op uw SSIS-IR. U kunt de eigenschap vervolgens inschakelen door het pad naar de eigenschap op te geven, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` en deze in te stellen op *True* als eigenschaps onderdrukking op het tabblad **Geavanceerd** van het pop-upvenster **pakket uitvoeren** wanneer u pakketten uit SSMS uitvoert.

  ![ConnectByProxy property4 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  U kunt de eigenschap ook inschakelen door het pad naar de eigenschap op te geven, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` en deze in te stellen op *True* als eigenschaps onderdrukking op het tabblad **Eigenschappen overschrijft** van de [activiteit uitvoering van SSIS-pakket](./how-to-invoke-ssis-package-ssis-activity.md) wanneer u pakketten uitvoert in Data Factory pijp lijnen.
  
  ![ConnectByProxy property5 inschakelen](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>Fouten opsporen in de on-premises en Cloud staging tasks

Op uw zelf-hostende IR vindt u de runtime-Logboeken in de map *C:\ProgramData\SSISTelemetry* en de uitvoerings logboeken van on-premises staging-taken in de map *C:\ProgramData\SSISTelemetry\ExecutionLog* .  U kunt de uitvoerings logboeken van Cloud staging-taken vinden in uw SSISDB, opgegeven paden voor het logboek bestand of Azure Monitor, afhankelijk van of u uw pakketten opslaat in SSISDB, [Azure monitor-integratie](./monitor-using-azure-monitor.md#monitor-ssis-operations-with-azure-monitor)inschakelt, enzovoort. U kunt ook de unieke Id's van on-premises faserings taken vinden in de uitvoerings logboeken van Cloud staging tasks. 

![Unieke ID van de eerste faserings taak](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

Als u klant ondersteunings tickets hebt geactiveerd, kunt u de knop **Logboeken verzenden** op het tabblad **Diagnostische gegevens** van **Microsoft Integration Runtime Configuration Manager** die is geïnstalleerd op uw zelf-hostende IR om recente bewerkingen en uitvoerings logboeken te verzenden.

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>Facturering voor de on-premises en Cloud staging-taken

De on-premises faserings taken die worden uitgevoerd op uw zelf-hostende IR, worden afzonderlijk in rekening gebracht, net zoals activiteiten voor gegevens verplaatsing die worden uitgevoerd op een zelf-hostende IR. Dit is opgegeven in het artikel [Azure Data Factory gegevens pijplijn](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

De faserings taken voor de cloud die op uw Azure-SSIS IR worden uitgevoerd, worden niet afzonderlijk in rekening gebracht, maar uw actieve Azure-SSIS IR wordt gefactureerd zoals is opgegeven in het artikel [Azure-SSIS IR prijzen](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="enable-custom3rd-party-components"></a>Aangepaste/externe onderdelen inschakelen 

Volg de volgende instructies om uw aangepaste/derde onderdelen in staat te stellen om toegang te krijgen tot gegevens on-premises met een zelf-hostende IR als proxy voor Azure-SSIS IR:

1. Installeer uw aangepaste/derde onderdelen die zijn gericht op SQL Server 2017 op Azure-SSIS IR via [standaard/snelle aangepaste Setup](./how-to-configure-azure-ssis-ir-custom-setup.md).

1. Maak de volgende DTSPath-register sleutels op zelf-hostende IR als ze nog niet bestaan:
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` ingesteld op `C:\Program Files\Microsoft SQL Server\140\DTS\`
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` ingesteld op `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\`
   
1. Installeer uw aangepaste/derde onderdelen die gericht zijn op SQL Server 2017 op zelf-hostende IR onder de DTSPath hierboven en zorg ervoor dat uw installatie proces:

   1. Maakt `<DTSPath>` , `<DTSPath>/Connections` , en `<DTSPath>/PipelineComponents` `<DTSPath>/UpgradeMappings` mappen als deze nog niet bestaan.
   
   1. Hiermee maakt u uw eigen XML-bestand voor extensie toewijzingen in de `<DTSPath>/UpgradeMappings` map.
   
   1. Hiermee worden alle assembly's geïnstalleerd waarnaar wordt verwezen door de onderdelen van uw aangepaste/derde partij in de Global Assembly Cache (GAC).

Hier volgen enkele voor beelden van onze partners, [Theobald-software](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) en [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir), die hun onderdelen hebben aangepast om gebruik te maken van onze snelle aangepaste installatie en zelf-hostende IR als proxy voor Azure-SSIS IR.

## <a name="enforce-tls-12"></a>Enforce TLS 1.2

Als u een krachtig crypto grafie/veiliger netwerk protocol (TLS 1,2) moet gebruiken en oudere SSL/TLS-versies op uw zelf-hostende IR wilt uitschakelen, kunt u het *Main. cmd* -script downloaden en uitvoeren dat u kunt vinden in de map *CustomSetupScript/UserScenarios/TLS 1,2* van de open bare preview-container.  Met [Azure Storage Explorer](https://storageexplorer.com/)kunt u verbinding maken met onze open bare preview-container door de volgende SAS-URI in te voeren:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Huidige beperkingen

- Alleen gegevensstroom onderdelen die zijn ingebouwd/vooraf geïnstalleerd op Azure-SSIS IR Standard Edition, met uitzonde ring van Hadoop/HDFS/DQS-onderdelen, worden op dit moment ondersteund. Zie [alle ingebouwde en vooraf geïnstalleerde onderdelen op Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).
- Alleen aangepaste gegevens stroom onderdelen van derden die zijn geschreven in beheerde code (.NET Framework) worden momenteel ondersteund. deze zijn geschreven in systeem eigen code (C++) en worden momenteel niet ondersteund.
- Het wijzigen van variabelen waarden in zowel on-premises als in de Cloud faserings taken wordt momenteel niet ondersteund.
- Het wijzigen van variabelen waarden van het type object in on-premises faserings taken worden niet weer gegeven in andere taken.
- *ParameterMapping* in OLEDB-bron wordt momenteel niet ondersteund. Als tijdelijke oplossing gebruikt u *SQL-opdracht van variabele* als de *AccessMode* en gebruikt u de *expressie* om uw variabelen/para meters in een SQL-opdracht in te voegen. Zie het pakket *ParameterMappingSample. dtsx* dat u kunt vinden in de map *SelfHostedIRProxy/beperkingen* van onze open bare preview-container. Met Azure Storage Explorer kunt u verbinding maken met onze open bare preview-container door de bovenstaande SAS-URI in te voeren.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw zelf-hostende IR hebt geconfigureerd als proxy voor uw Azure-SSIS IR, kunt u uw pakketten implementeren en uitvoeren voor toegang tot on-premises gegevens als uitvoering van SSIS-pakket activiteiten in Data Factory-pijp lijnen. Zie [SSIS-pakketten uitvoeren als uitvoering van SSIS-pakket activiteiten in Data Factory pijp lijnen](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie.