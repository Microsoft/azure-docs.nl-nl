---
title: SSIS-pakketten (Execute SQL Server Integration Services) met het met Azure ingeschakelde dtexec-hulp programma
description: Meer informatie over het uitvoeren van SQL Server Integration Services (SSIS)-pakketten met het met Azure ingeschakelde dtexec-hulp programma.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 90c269435f8df2cb3e7aeedc8ff561693c831a09
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556474"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>SQL Server Integration Services-pakketten uitvoeren met het Azure-hulp programma dtexec

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt het opdracht prompt hulpprogramma voor Azure-dtexec (AzureDTExec) beschreven. Dit wordt gebruikt om SQL Server Integration Services (SSIS)-pakketten uit te voeren op de Azure-SSIS Integration Runtime (IR) in Azure Data Factory.

Het traditionele dtexec-hulp programma wordt geleverd met SQL Server. Zie [dtexec Utility](/sql/integration-services/packages/dtexec-utility)(Engelstalig) voor meer informatie. Het wordt vaak aangeroepen door Orchestrator of planners van derden, zoals ActiveBatch en Control-M, om SSIS-pakketten on-premises uit te voeren. 

Het hulp programma modern AzureDTExec wordt geleverd met een hulp programma van SQL Server Management Studio (SSMS). Het kan ook worden opgeroepen door Orchestrator of planners van derden om SSIS-pakketten uit te voeren in Azure. Het vereenvoudigt de opheffing en verschuiving van uw SSIS-pakketten naar de Cloud. Als u na de migratie het gebruik van Orchestrator of planners van derden wilt blijven gebruiken in uw dagelijkse activiteiten, kunnen ze nu AzureDTExec aanroepen in plaats van dtexec.

AzureDTExec voert uw pakketten uit als uitvoering van SSIS-pakket activiteiten in Data Factory pijp lijnen. Zie [SSIS-pakketten uitvoeren als Azure Data Factory activiteiten](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie. 

AzureDTExec kan via SSMS worden geconfigureerd om een Azure Active Directory-toepassing (Azure AD) te gebruiken die pijp lijnen in uw data factory genereert. Het kan ook worden geconfigureerd voor toegang tot bestands systemen, bestands shares of Azure Files waar u uw pakketten opslaat. Op basis van de waarden die u geeft voor de aanroep opties, genereert AzureDTExec een unieke Data Factory-pijp lijn met een activiteit voor het uitvoeren van SSIS-pakketten. Wanneer u AzureDTExec aanroept met dezelfde waarden voor de opties, wordt de bestaande pijp lijn opnieuw uitgevoerd.

## <a name="prerequisites"></a>Vereisten
Als u AzureDTExec wilt gebruiken, downloadt en installeert u de meest recente versie van SSMS. Dit is versie 18,3 of hoger. Down load deze van [deze website](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="configure-the-azuredtexec-utility"></a>Het hulp programma AzureDTExec configureren
Als u SSMS op uw lokale computer installeert, wordt ook AzureDTExec geïnstalleerd. Als u de instellingen wilt configureren, start u SSMS met de optie **als administrator uitvoeren** . Selecteer vervolgens **hulpprogram ma's**  >  **migreren naar Azure**  >  **Azure-DTExec configureren**.

![Het dtexec-menu voor Azure ingeschakeld configureren](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Met deze actie opent u een **AzureDTExecConfig** -venster dat moet worden geopend met beheerders bevoegdheden zodat het kan worden geschreven naar het bestand *AzureDTExec. settings* . Als u SSMS niet als beheerder hebt uitgevoerd, wordt het venster Gebruikersaccountbeheer (UAC) geopend. Voer uw beheerders wachtwoord in om uw bevoegdheden uit te breiden.

![Instellingen voor Azure enabled dtexec configureren](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

Voer in het venster **AzureDTExecConfig** uw configuratie-instellingen als volgt in:

- **ApplicationId**: Voer de unieke id in van de Azure AD-app die u hebt gemaakt met de juiste machtigingen voor het genereren van pijp lijnen in uw Data Factory. Zie [een Azure AD-app en-service-principal maken via Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.
- **AuthenticationKey**: Voer de verificatie sleutel in voor uw Azure AD-app.
- **TenantId**: Voer de unieke id in van de Azure AD-Tenant, waaronder uw Azure AD-app wordt gemaakt.
- **DataFactory**: Voer de naam in van uw Data Factory waarin unieke pijp lijnen met de activiteit EXECUTe SSIS-pakket worden gegenereerd op basis van de waarden van de opties die worden opgegeven wanneer u AzureDTExec aanroept.
- **IRName**: Voer de naam in van de Azure-SSIS IR in uw Data Factory, waarop de pakketten die zijn opgegeven in het UNC-pad (Universal Naming Convention) worden uitgevoerd wanneer u AzureDTExec aanroept.
- **PipelineNameHashStrLen**: Geef de lengte van de hash-teken reeksen op die moeten worden gegenereerd op basis van de waarden van de opties die u opgeeft wanneer u AzureDTExec aanroept. De teken reeksen worden gebruikt om unieke namen te vormen voor Data Factory pijp lijnen die uw pakketten uitvoeren op de Azure-SSIS IR. Meestal is een lengte van 32 tekens voldoende.
- **ResourceGroup**: Voer de naam in van de Azure-resource groep waarin uw Data Factory is gemaakt.
- **SubscriptionId**: Voer de unieke id in van het Azure-abonnement waarmee uw Data Factory is gemaakt.
- **LogAccessDomain**: Voer de domein referentie in voor toegang tot uw Logboekmap in het UNC-pad wanneer u logboek bestanden schrijft. Dit is vereist wanneer **logPath** is opgegeven en **LogLevel** niet **Null** is.
- **LogAccessPassword**: Voer de wachtwoord referentie in om toegang te krijgen tot de logboekmap in het UNC-pad wanneer u logboek bestanden schrijft, wat vereist is wanneer **logPath** is opgegeven en **LogLevel** niet **Null** is.
- **LogAccessUserName**: Voer de gebruikers naam in om toegang te krijgen tot de logboekmap in het UNC-pad wanneer u logboek bestanden schrijft, wat vereist is wanneer **logPath** is opgegeven en **LogLevel** niet **Null** is.
- **LogLevel**: Voer het geselecteerde registratie bereik in op basis van vooraf gedefinieerde **waarden voor Null**-, **basis**-, **uitgebreide** of **prestatie** opties voor uw pakket uitvoeringen op de Azure-SSIS IR.
- **LogPath**: Voer het UNC-pad in van de logboekmap, waarin de logboek bestanden van de pakket uitvoeringen op de Azure-SSIS IR zijn geschreven.
- **PackageAccessDomain**: Voer de domein referentie in voor toegang tot uw pakketten in het UNC-pad dat wordt opgegeven wanneer u AzureDTExec aanroept.
- **PackageAccessPassword**: Voer de wachtwoord referentie in om toegang te krijgen tot uw pakketten in het UNC-pad dat is opgegeven wanneer u AzureDTExec aanroept.
- **PackageAccessUserName**: Voer de gebruikers naam referentie in voor toegang tot uw pakketten in het UNC-pad dat is opgegeven wanneer u AzureDTExec aanroept.

Als u uw pakketten en logboek bestanden wilt opslaan in bestands systemen of bestands shares op locatie, voegt u uw Azure-SSIS IR toe aan een virtueel netwerk dat is verbonden met uw on-premises netwerk, zodat het uw pakketten kan ophalen en uw logboek bestanden kan schrijven. Zie [een Azure-SSIS IR toevoegen aan een virtueel netwerk](./join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.

Om te voor komen dat gevoelige waarden worden weer gegeven in het bestand *AzureDTExec. settings* in tekst zonder opmaak, worden ze gecodeerd in teken reeksen van base64-code ring. Wanneer u AzureDTExec aanroept, worden alle met base64 gecodeerde teken reeksen gedecodeerd in hun oorspronkelijke waarden. U kunt het bestand *AzureDTExec. settings* verder beveiligen door de accounts te beperken waarmee toegang kan worden gezocht.

## <a name="invoke-the-azuredtexec-utility"></a>Het hulp programma AzureDTExec aanroepen
U kunt AzureDTExec aanroepen bij de opdracht regel prompt en de relevante waarden opgeven voor specifieke opties in uw gebruiks scenario.

Het hulp programma wordt geïnstalleerd op `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` . U kunt het pad toevoegen aan de omgevings variabele PATH zodat het kan worden aangeroepen vanaf elke locatie.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Het aanroepen van AzureDTExec biedt vergelijk bare opties als het aanroepen van dtexec. Zie [Dtexec Utility](/sql/integration-services/packages/dtexec-utility)(Engelstalig) voor meer informatie. Dit zijn de opties die momenteel worden ondersteund:

- **/F [estand]**: laadt een pakket dat is opgeslagen in bestands systeem, bestands share of Azure files. Als de waarde voor deze optie kunt u het UNC-pad opgeven voor het pakket bestand in bestands systeem, bestands share of Azure Files met de extensie. dtsx. Als het UNC-pad is opgegeven, plaatst u aanhalings tekens rond het hele pad.
- **/Conf [igFile]**: Hiermee geeft u een configuratie bestand op waaruit waarden moeten worden opgehaald. Met deze optie kunt u een runtime-configuratie voor uw pakket instellen die afwijkt van de versie die tijdens de ontwerp fase is opgegeven. U kunt verschillende instellingen in een XML-configuratie bestand opslaan en deze vervolgens laden vóór de uitvoering van het pakket. Zie [SSIS-pakket configuraties](/sql/integration-services/packages/package-configurations)voor meer informatie. Als u de waarde voor deze optie wilt opgeven, gebruikt u het UNC-pad voor uw configuratie bestand in bestands systeem, bestands share of Azure Files met de dtsConfig-extensie. Als het UNC-pad is opgegeven, plaatst u aanhalings tekens rond het hele pad.
- **/Conn [eveiliging]**: Hiermee geeft u de verbindings reeksen voor bestaande verbindings beheer in uw pakket op. Met deze optie kunt u run-time verbindings reeksen instellen voor bestaande verbindings beheer in uw pakket dat verschilt van het aantal dat tijdens de ontwerp fase is opgegeven. Geef als volgt de waarde voor deze optie op: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` .
- **/Set**: onderdrukt de configuratie van een para meter, variabele, eigenschap, container, logboek provider, foreach-enumerator of verbinding in uw pakket. Deze optie kan meerdere keren worden opgegeven. Geef als volgt de waarde voor deze optie op: `property_path;value` . `\package.variables[counter].Value;1`De waarde van de variabele wordt bijvoorbeeld vervangen door `counter` 1. Met de wizard **pakket configuratie** kunt u de waarde van items in uw pakket zoeken, kopiëren en plakken `property_path` waarvan u de waarde wilt overschrijven. Zie de [wizard pakket configureren](/sql/integration-services/packages/legacy-package-deployment-ssis)voor meer informatie.
- **/De [crypt]**: Hiermee stelt u het wacht woord voor ontsleuteling in voor uw pakket dat is geconfigureerd met het beveiligings niveau **EncryptAllWithPassword** / **EncryptSensitiveWithPassword** .

> [!NOTE]
> Als AzureDTExec wordt aangeroepen met nieuwe waarden voor de opties, wordt een nieuwe pijp lijn gegenereerd, met uitzonde ring van de optie **/de [script]**.

## <a name="next-steps"></a>Volgende stappen

Nadat unieke pijp lijnen met de activiteit run SSIS-pakket in worden gegenereerd en worden uitgevoerd wanneer u AzureDTExec aanroept, kunnen ze worden gecontroleerd op de Data Factory Portal. U kunt ook Data Factory triggers aan hen toewijzen als u ze wilt organiseren/plannen met behulp van Data Factory. Zie [SSIS-pakketten uitvoeren als Data Factory activiteiten](./how-to-invoke-ssis-package-ssis-activity.md)voor meer informatie.

> [!WARNING]
> De gegenereerde pijp lijn wordt verwacht alleen te worden gebruikt door AzureDTExec. De eigenschappen of para meters worden in de toekomst mogelijk gewijzigd, dus niet voor andere doel einden. Wijzigingen kunnen AzureDTExec afnemen. Als dit gebeurt, verwijdert u de pijp lijn. AzureDTExec genereert een nieuwe pijp lijn de volgende keer dat deze wordt aangeroepen.