---
title: SSIS-pakketten uitvoeren vanuit SSDT
description: Meer informatie over het uitvoeren van SSIS-pakketten in azure vanuit SSDT.
ms.service: data-factory
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: f11a3845e8644f3f60425538b2ef32cff668d88d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384925"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSIS-pakketten uitvoeren in azure vanuit SSDT

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt de functie van SSIS-projecten (Azure-enabled SQL Server Integration Services) beschreven op SQL Server Data Tools (SSDT). Hiermee kunt u de compatibiliteit van de cloud van uw SSIS-pakketten beoordelen en deze uitvoeren op Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF). U kunt deze functie gebruiken om uw bestaande pakketten te testen voordat u & Shift/migreert naar Azure of om nieuwe pakketten te ontwikkelen om uit te voeren in Azure.

Met deze functie kunt u een nieuw/bestaand Azure-SSIS IR koppelen aan SSIS-projecten en vervolgens uw pakketten hierop uitvoeren.  We ondersteunen het uitvoeren van pakketten die worden geïmplementeerd in de SSIS-catalogus (SSISDB) die wordt gehost door uw Azure SQL Database Server of beheerde instantie in het implementatie model van het project. We bieden ook ondersteuning voor het uitvoeren van pakketten die worden geïmplementeerd in het bestands systeem/Azure Files/SQL Server Data Base (MSDB) gehost door uw door Azure SQL beheerde instantie in pakket implementatie model. 

## <a name="prerequisites"></a>Vereisten

Als u deze functie wilt gebruiken, moet u de meest recente SSDT met SSIS projects-extensie voor Visual Studio (VS) downloaden en [installeren.](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) U kunt ook de meest recente [versie van SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)downloaden en installeren als zelfstandig installatie programma.

## <a name="azure-enable-ssis-projects"></a>Azure-SSIS-projecten inschakelen

### <a name="creating-new-azure-enabled-ssis-projects"></a>Nieuwe Azure-SSIS-projecten maken

Op SSDT kunt u nieuwe Azure-SSIS-projecten maken met behulp van de sjabloon voor het **Integration Services-project (Azure-enabled)** .

   ![Nieuw Azure-SSIS-project](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Nadat het Azure-project is gemaakt, wordt u gevraagd verbinding te maken met SSIS in Azure Data Factory.

   ![Verbinding maken Azure-SSIS IR prompt](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Als u meteen verbinding wilt maken met uw Azure-SSIS IR, raadpleegt u [verbinding maken met Azure-SSIS IR](#connectssisir) voor meer informatie. U kunt ook later verbinding maken door met de rechter muisknop op het knoop punt van het project te klikken in het Solution Explorer venster van SSDT om een menu te openen. Selecteer vervolgens het submenu **verbinding maken met SSIS in azure Data Factory** item in **SSIS in azure Data Factory** .

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure-bestaande SSIS-projecten inschakelen

Voor bestaande SSIS-projecten kunt u Azure inschakelen door de volgende stappen uit te voeren:

1. Klik met de rechter muisknop op het project knooppunt in het venster Solution Explorer van SSDT om een menu te openen. Selecteer vervolgens het **Azure-project** item in **SSIS in azure Data Factory** vervolg menu om de **wizard Azure-project** te starten.

   ![Azure-bestaand SSIS-project inschakelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. Selecteer op de pagina **configuratie van Visual Studio selecteren** uw bestaande versus configuratie om instellingen voor het uitvoeren van pakketten toe te passen in Azure. U kunt ook een nieuw abonnement maken als u dit nog niet hebt gedaan, raadpleegt u [een nieuwe versus configuratie maken](/visualstudio/ide/how-to-create-and-edit-configurations). We raden u aan ten minste twee verschillende versus configuraties te gebruiken voor het uitvoeren van pakketten in de lokale omgevingen en in de cloud omgeving, zodat u uw project kunt inschakelen op basis van de Cloud configuratie. Op deze manier kunt u, als u uw project of pakketten hebt geparametriseerde, verschillende waarden toewijzen aan uw project-of pakket parameters tijdens runtime op basis van de verschillende uitvoerings omgevingen (op uw lokale machine of in Azure). Zie bijvoorbeeld [pakket uitvoerings omgevingen omschakelen](#switchenvironment).

   ![Visual Studio-configuratie selecteren](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Voor Azure: door uw bestaande SSIS-projecten in te scha kelen, moet u de versie van de doel server instellen op de nieuwste, die door Azure-SSIS IR wordt ondersteund. Azure-SSIS IR is momenteel gebaseerd op **SQL Server 2017**. Zorg ervoor dat uw pakketten geen extra onderdelen bevatten die niet worden ondersteund op SQL Server 2017. Zorg er ook voor dat alle compatibele aanvullende onderdelen ook zijn geïnstalleerd op uw Azure-SSIS IR via aangepaste installatie. Zie [uw Azure-SSIS IR aanpassen](./how-to-configure-azure-ssis-ir-custom-setup.md)voor meer informatie. Selecteer de knop **volgende** om door te gaan.

   ![Doel server versie wijzigen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Zie [verbinding maken met Azure-SSIS IR](#connectssisir) om de verbinding van uw project met Azure-SSIS IR te volt ooien.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Verbinding maken met Azure-projecten op SSIS in Azure Data Factory

Door uw Azure-projecten te koppelen aan SSIS in ADF, kunt u uw pakketten uploaden naar Azure Files en deze uitvoeren op Azure-SSIS IR. U kunt dit doen door de volgende stappen uit te voeren:

1. Bekijk de inleiding op de pagina **SSIS in ADF (Inleiding** ) en selecteer de knop **volgende** om door te gaan.

   ![Introductie van SSIS in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. Selecteer op de pagina **SSIS IR selecteren op ADF** de bestaande ADF en Azure-SSIS IR om pakketten uit te voeren. U kunt ook nieuwe maken als u er nog geen hebt.
   - Als u uw bestaande Azure-SSIS IR wilt selecteren, selecteert u eerst het relevante Azure-abonnement en de ADF.
   - Als u uw bestaande ADF selecteert die geen Azure-SSIS IR heeft, selecteert u de knop **SSIS-IR maken** om een nieuwe te maken op de ADF-Portal. Zodra u deze hebt gemaakt, kunt u terugkeren naar deze pagina om uw nieuwe Azure-SSIS IR te selecteren.
   - Als u uw bestaande Azure-abonnement selecteert dat geen ADF heeft, selecteert u de knop **SSIS-IR maken** om de **wizard Integration runtime maken** te starten. In de wizard kunt u de aangewezen locatie en het voor voegsel voor ons opgeven om automatisch een nieuwe Azure-resource groep, Data Factory en SSIS IR te maken namens u, met de naam in het volgende patroon: **YourPrefix-RG/DF/IR-YourCreationTime**. Zodra u deze hebt gemaakt, kunt u terugkeren naar deze pagina om uw nieuwe ADF en Azure-SSIS IR te selecteren.

   ![SSIS IR selecteren in ADF](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. Selecteer op de pagina **Azure Storage selecteren** uw bestaande Azure Storage account om pakketten naar Azure files te uploaden. U kunt ook een nieuw abonnement maken als u er nog geen hebt.
   - Als u uw bestaande Azure Storage account wilt selecteren, selecteert u eerst het relevante Azure-abonnement.
   - Als u hetzelfde Azure-abonnement selecteert als uw Azure-SSIS IR dat geen Azure Storage account heeft, selecteert u de knop **Azure Storage maken** . Er wordt automatisch een nieuw certificaat op dezelfde locatie gemaakt als uw Azure-SSIS IR, met de naam door een voor voegsel van uw Azure-SSIS IR naam en de aanmaak datum te combi neren. Zodra u deze hebt gemaakt, kunt u terugkeren naar deze pagina om uw nieuwe Azure Storage-account te selecteren.
   - Als u een ander Azure-abonnement selecteert dat geen Azure Storage account heeft, selecteert u de knop **Azure Storage maken** om een nieuwe te maken op Azure Portal. Zodra u deze hebt gemaakt, kunt u terugkeren naar deze pagina om uw nieuwe Azure Storage-account te selecteren.

   ![Azure Storage selecteren](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Selecteer de knop **verbinding maken** om het project te volt ooien om verbinding te maken met Azure-SSIS IR. Uw geselecteerde Azure-SSIS IR en Azure Storage account worden weer gegeven in het knoop punt **gekoppelde Azure-resources** in Solution Explorer venster van SSDT. We vernieuwen ook regel matig de status van uw Azure-SSIS IR. U kunt uw Azure-SSIS IR beheren door met de rechter muisknop op het knoop punt te klikken om een menu te openen en vervolgens het **Start\Stop\Manage** -item te selecteren dat u naar de ADF-portal gaat.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>SSIS-project\packages evalueren voor uitvoeringen in azure

### <a name="assessing-single-or-multiple-packages"></a>Eén of meer pakketten beoordelen

Voordat u uw pakketten in azure uitvoert, kunt u deze beoordelen om mogelijke problemen met de compatibiliteit met de Cloud op te lossen. Dit zijn onder andere migratie blok keringen en aanvullende informatie waarvan u zich bewust moet zijn. 
-  U hebt de opties om één pakket één voor één of alle pakketten tegelijk te evalueren.

   ![Pakket beoordelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![Project beoordelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  In het venster **beoordelings rapport** van SSDT vindt u alle mogelijke problemen met de Cloud compatibiliteit die worden weer gegeven, elk met een eigen gedetailleerde beschrijving en aanbeveling. U kunt het beoordelings rapport ook exporteren naar een CSV-bestand dat kan worden gedeeld met iedereen die deze problemen moet verhelpen. 

   ![Evaluatierapport](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>Beoordelings regels onderdrukken

Wanneer u zeker weet dat er een aantal potentiële problemen met de Cloud compatibiliteit niet van toepassing is of in uw pakketten correct is opgelost, kunt u de relevante beoordelings regels die deze bevatten, onderdrukken. Hiermee wordt de ruis in uw volgende evaluatie rapporten verminderd.
-  Selecteer in het venster **beoordelings rapport** met SSDT de koppeling **beoordelings regel onderdrukken** van analyse configureren om pop-up te maken van het venster **instellingen voor evaluatie regel onderdrukken** , waarin u de evaluatie regels kunt selecteren die u wilt onderdrukken.

   ![Instellingen voor onderdrukking van evaluatie regels](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  U kunt ook met de rechter muisknop op uw project knooppunt klikken in het venster Solution Explorer van SSDT om een menu te openen. Selecteer het item **Azure-instellingen** in **SSIS in azure Data Factory** vervolg menu om een venster met de eigenschappen pagina's van uw project te openen. Selecteer de **onderdrukte id** -eigenschap voor de evaluatie regel in het gedeelte met **instellingen voor Azure** . Selecteer tot slot de knop met weglatings tekens (**...**) om het venster instellingen voor het **onderdrukken van evaluatie regels** te openen. hier kunt u de evaluatie regels selecteren die u wilt onderdrukken.

   ![Instellingen waarvoor Azure is ingeschakeld](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Instellingen voor het onderdrukken van evaluatie regels via Azure-instellingen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>SSIS-pakketten uitvoeren in azure

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Instellingen voor Azure ingeschakeld configureren

Voordat u uw pakketten in azure uitvoert, kunt u de instellingen voor Azure ingeschakeld configureren. U kunt bijvoorbeeld Windows-verificatie inschakelen op uw Azure-SSIS IR om toegang te krijgen tot on-premises/Cloud gegevens archieven door de volgende stappen uit te voeren:

1. Klik met de rechter muisknop op het project knooppunt in het venster Solution Explorer van SSDT om een menu te openen. Selecteer vervolgens het item met **Azure ingeschakelde instellingen** in **SSIS in azure Data Factory** vervolg menu om een venster met de eigenschappen pagina's van uw project te openen.

   ![Instellingen waarvoor Azure is ingeschakeld](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. Selecteer de sectie **Windows-verificatie inschakelen** in het gedeelte met **instellingen voor Azure** en selecteer **waar** in de vervolg keuzelijst. Selecteer vervolgens de eigenschap **Windows-verificatie referenties** en selecteer vervolgens de knop met het weglatings teken (**...**) om het venster **Windows-verificatie referenties** te openen.

   ![Windows-verificatie inschakelen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Voer uw Windows-verificatie referenties in. Als u bijvoorbeeld toegang wilt krijgen tot Azure Files, kunt `Azure` u `YourStorageAccountName` respectievelijk, en `YourStorageAccountKey` voor het **domein**, de **gebruikers naam** en het **wacht woord** invoeren.

   ![Windows-verificatie referenties](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>Pakket uitvoeringen starten

Nadat u uw Azure-projecten hebt verbonden met SSIS in ADF, de compatibiliteit van de Clouds te beoordelen en mogelijke problemen te verhelpen, kunt u uw pakketten uitvoeren en testen op Azure-SSIS IR.
-  Selecteer de knop **Start** op de werk balk SSDT om een menu omlaag te slepen. Selecteer vervolgens het item **uitvoeren in azure** .

   ![Uitvoeren in azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  U kunt ook met de rechter muisknop op uw pakket knooppunt klikken in het venster Solution Explorer van SSDT om een menu te openen. Selecteer vervolgens het **pakket uitvoeren in azure** item.

   ![Pakket uitvoeren in azure](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Voor het uitvoeren van uw pakketten in azure moet u een actief Azure-SSIS IR hebben, dus als uw Azure-SSIS IR is gestopt, wordt er een dialoog venster weer gegeven met de pop-up om het te starten. Als u een aangepaste instel tijd wilt uitsluiten, moet dit proces binnen vijf minuten worden voltooid, maar kan het ongeveer 20-30 minuten duren voordat Azure-SSIS IR lid wordt van een virtueel netwerk. Nadat u uw pakketten hebt uitgevoerd in azure, kunt u uw Azure-SSIS IR voor het beheren van de lopende kosten stoppen door met de rechter muisknop op het knoop punt van het Solution Explorer venster van SSDT te klikken om een menu te openen en vervolgens het **Start\Stop\Manage** -item te selecteren dat u naar de ADF-portal gaat.

### <a name="using-execute-package-task"></a>De taak pakket uitvoeren gebruiken

Als uw pakketten taken voor het uitvoeren van pakketten bevatten die verwijzen naar onderliggende pakketten die zijn opgeslagen op lokale bestands systemen, voert u de volgende aanvullende stappen uit:

1. Upload de onderliggende pakketten naar Azure Files onder hetzelfde Azure Storage account dat is verbonden met uw projecten en ontvang het nieuwe UNC-pad (Universal Naming Convention), bijvoorbeeld `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. Vervang het bestandspad van deze onderliggende pakketten in het bestands verbindings beheer van pakket taken uitvoeren met het nieuwe UNC-pad
   - Als uw lokale machine met SSDT geen toegang heeft tot het nieuwe UNC-pad, kunt u deze invoeren in het deel venster Eigenschappen van bestands verbindings beheer
   - U kunt ook een variabele voor het bestandspad gebruiken om de juiste waarde tijdens runtime toe te wijzen

Als uw pakketten taken voor het uitvoeren van pakketten bevatten die verwijzen naar onderliggende pakketten in hetzelfde project, hoeft u verder niets te doen.

### <a name="switching-package-protection-level"></a>Niveau van pakket beveiliging overschakelen

Het uitvoeren van SSIS-pakketten in Azure biedt geen ondersteuning voor **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** -beveiligings niveaus. Als uw pakketten zo zijn geconfigureerd dat ze worden gebruikt, worden deze tijdelijk geconverteerd naar respectievelijk met behulp van **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** Protection-niveaus. Er worden ook wille keurig versleutelings wachtwoorden gegenereerd wanneer we uw pakketten uploaden naar Azure Files voor uitvoeringen op uw Azure-SSIS IR.

> [!NOTE]
> Als uw pakketten uitvoerings pakket taken bevatten die verwijzen naar onderliggende pakketten die zijn geconfigureerd voor het gebruik van **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** -beveiligings niveaus, moet u deze onderliggende pakketten hand matig opnieuw configureren om **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** -beveiligings niveaus te gebruiken voordat u de pakketten uitvoert.

Als uw pakketten al zijn geconfigureerd voor het gebruik van **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** -beveiligings niveaus, blijven deze ongewijzigd. Er worden steeds wille keurig versleutelings wachtwoorden gegenereerd wanneer we uw pakketten uploaden naar Azure Files voor uitvoeringen op uw Azure-SSIS IR.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> Pakket uitvoerings omgevingen omschakelen

Als u uw project/pakketten para meters in het implementatie model van het project, kunt u meerdere VS-configuraties maken om te scha kelen tussen pakket uitvoerings omgevingen. Op deze manier kunt u tijdens runtime specifieke waarden toewijzen aan uw project/pakket-para meters. We raden u aan ten minste twee verschillende versus configuraties te gebruiken voor het uitvoeren van pakketten in de lokale omgevingen en in de cloud omgeving, zodat u uw projecten kunt inschakelen op basis van de Cloud configuratie. Hier volgt een stapsgewijze voor beeld van het omschakelen van de pakket uitvoerings omgevingen tussen uw lokale computer en Azure:

1. Stel dat uw pakket een bestandsbeheer taak bevat waarmee de kenmerken van een bestand worden ingesteld. Wanneer u het op uw lokale computer uitvoert, worden de kenmerken ingesteld van een bestand dat is opgeslagen op uw lokale bestands systeem. Wanneer u het op uw Azure-SSIS IR uitvoert, wilt u de kenmerken instellen van een bestand dat is opgeslagen in Azure Files. Maak eerst een pakket parameter van het teken reeks type en geef **deze de** naam om de waarde van het pad naar het doel bestand op te slaan.

   ![Pakket parameter maken](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. Op de pagina **Algemeen** van de **Bestands systeem taak editor** venster, para meters de eigenschap **SourceVariable** in de sectie **bron verbinding** met de **filepath** -pakket parameter. 

   ![Para meters-bron verbinding](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Standaard hebt u een bestaande versus configuratie voor het uitvoeren van pakketten in de lokale omgeving **ontwikkeling**. Een nieuwe versus configuratie maken voor pakket uitvoeringen in de cloud omgeving **Azure**, Zie [een nieuwe versus configuratie maken](/visualstudio/ide/how-to-create-and-edit-configurations), als u dit nog niet hebt gedaan.

4. Wanneer u de para meters van uw pakket bekijkt, selecteert u de knop **para meters aan configuraties toevoegen** om het venster **parameter waarden beheren** voor uw pakket te openen. Wijs vervolgens verschillende waarden van het pad naar het doel bestand toe aan de para meter **filepath** onder de **ontwikkel** -en **Azure** -configuraties.

   ![Parameter waarden toewijzen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure: uw project inschakelen op basis van de Cloud configuratie Zie [Azure-bestaande SSIS-projecten inschakelen](#azureenableproject)als u dit nog niet hebt gedaan. Configureer vervolgens Azure-instellingen om Windows-verificatie in te scha kelen voor uw Azure-SSIS IR om toegang te krijgen tot Azure Files. Zie [instellingen voor Azure-enabled configureren](#azureenabledsettings), als u dit nog niet hebt gedaan.

6. Voer uw pakket uit in Azure. U kunt de omgeving voor het uitvoeren van een pakket naar uw lokale machine herstellen door de **ontwikkelings** configuratie te selecteren.

   ![De configuratie van Visual Studio wijzigen](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>Pakket configuratie bestand gebruiken

Als u pakket configuratie bestanden gebruikt in pakket implementatie model, kunt u tijdens runtime specifieke waarden toewijzen aan de pakket eigenschappen. Deze bestanden worden met uw pakketten automatisch geüpload naar Azure Files voor uitvoeringen op uw Azure-SSIS IR.

### <a name="checking-package-execution-logs"></a>Uitvoer logboeken voor het pakket controleren

Nadat de uitvoering van het pakket is gestart, worden de logboeken in het **voortgangs** venster van SSDT opgemaakt en weer gegeven. Voor een langlopend pakket zullen de logboeken regel matig worden bijgewerkt op basis van de minuten. U kunt de uitvoering van het pakket onmiddellijk annuleren door de knop **stoppen** te selecteren op de werk balk SSDT. U kunt de onbewerkte gegevens van de logboeken ook tijdelijk vinden in het volgende UNC-pad: `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , maar we zullen deze na één dag opschonen.

## <a name="current-limitations"></a>Huidige beperkingen

-  De Azure-SSDT ondersteunt alleen commerciële/wereld wijde Cloud regio's en biedt momenteel geen ondersteuning voor overheids-en nationale Cloud regio's.

## <a name="next-steps"></a>Volgende stappen

Als u tevreden bent over het uitvoeren van uw pakketten in azure van SSDT, kunt u deze implementeren en uitvoeren als run SSIS-pakket activiteiten in ADF-pijp lijnen. Zie [SSIS-pakketten uitvoeren als uitvoeren SSIS-pakket activiteiten in ADF-pijp lijnen](./how-to-invoke-ssis-package-ssis-activity.md).