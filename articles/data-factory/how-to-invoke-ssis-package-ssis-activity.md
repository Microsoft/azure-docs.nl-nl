---
title: Voer een SSIS-pakket uit met de activiteit voor het uitvoeren van SSIS-pakketten
description: In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in een Azure Data Factory-pijp lijn met behulp van de activiteit voor het uitvoeren van SSIS-pakketten.
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 40e7d5c11a2ebc62e59c3d5d20dfefe18a33f9a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391606"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Een SSIS-pakket uitvoeren met de activiteit SSIS-pakket uitvoeren in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert in een Azure Data Factory-pijp lijn met behulp van de activiteit voor het uitvoeren van SSIS-pakketten. 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Een Azure SSIS Integration runtime (IR) maken als u er nog geen hebt, volgt u de stapsgewijze instructies in de [zelf studie: inrichting van Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md).

## <a name="run-a-package-in-the-azure-portal"></a>Een pakket uitvoeren in de Azure Portal
In deze sectie gebruikt u de Data Factory gebruikers interface (UI) of de app om een Data Factory pijp lijn te maken met een activiteit voor het uitvoeren van SSIS-pakketten die uw SSIS-pakket uitvoert.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijp lijn maken met een activiteit voor het uitvoeren van SSIS-pakketten
In deze stap gebruikt u de Data Factory-gebruikers interface of-app om een pijp lijn te maken. U voegt een activiteit voor het uitvoeren van SSIS-pakketten toe aan de pijp lijn en configureert deze voor het uitvoeren van uw SSIS-pakket. 

1. Selecteer op uw Data Factory overzicht of start pagina in de Azure Portal de tegel **auteur & monitor** om de Data Factory-gebruikers interface of-app op een afzonderlijk tabblad te starten. 

   ![Start pagina van Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**. 

   ![Pagina Aan de slag](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. Vouw in de werkset **activities** de optie **Algemeen** uit. Sleep vervolgens een activiteit voor het **uitvoeren van SSIS-pakketten** naar het ontwerp oppervlak voor pijp lijnen. 

   ![Sleep een uitvoeren SSIS-pakket activiteit naar het ontwerp oppervlak](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   Selecteer het object activiteit voor het uitvoeren van SSIS-pakket activiteiten om de tabbladen **Algemeen**, **instellingen**, **SSIS-para meters**, **verbindings beheer** en **Eigenschappen onderdrukkingen** te configureren.

#### <a name="general-tab"></a>Tabblad Algemeen

Voer de volgende stappen uit op het tabblad **Algemeen** van de activiteit SSIS-pakket uitvoeren.

![Eigenschappen instellen op het tabblad Algemeen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. Voer bij **naam** de naam in van de activiteit voor het uitvoeren van SSIS-pakketten.

   1. Voor **Beschrijving** voert u de beschrijving van de activiteit voor het uitvoeren van SSIS-pakketten in.

   1. Voer bij **time-out** de maximale hoeveelheid tijd in die de activiteit voor het uitvoeren van SSIS-pakketten kan uitvoeren. De standaard waarde is 7 dagen, de notatie is D. uu: MM: SS.

   1. Voor **nieuwe poging** voert u het maximum aantal nieuwe pogingen in voor de activiteit voor het uitvoeren van SSIS-pakketten.

   1. Voor het **interval voor opnieuw proberen** voert u het aantal seconden in tussen elke nieuwe poging voor de activiteit voor het uitvoeren van SSIS-pakketten. De standaard waarde is 30 seconden.

   1. Schakel het selectie vakje **beveiligde uitvoer** in om te kiezen of u de uitvoer van de activiteit voor het uitvoeren van SSIS-pakketten wilt uitsluiten van logboek registratie.

   1. Schakel het selectie vakje **beveiligde invoer** in om te kiezen of u de invoer van de activiteit voor het uitvoeren van SSIS-pakketten wilt uitsluiten van logboek registratie.

#### <a name="settings-tab"></a>Tabblad Instellingen

Voer de volgende stappen uit op het tabblad **instellingen** van de activiteit voor het uitvoeren van SSIS-pakketten.

![Eigenschappen instellen op het tabblad Instellingen-automatisch](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. Selecteer voor **Azure-SSIS IR** de aangewezen Azure-SSIS IR om de activiteit voor het uitvoeren van SSIS-pakketten uit te voeren.

   1. Voor **Beschrijving** voert u de beschrijving van de activiteit voor het uitvoeren van SSIS-pakketten in.

   1. Schakel het selectie vakje **Windows-verificatie** in om te kiezen of u Windows-verificatie wilt gebruiken voor toegang tot gegevens archieven, zoals SQL-servers/bestands shares op-locatie of Azure files.
   
      Als u dit selectie vakje inschakelt, geeft u de waarden op voor de referenties voor het uitvoeren van het pakket in de vakken **domein**, **gebruikers naam** en **wacht woord** . Bijvoorbeeld: voor toegang tot Azure Files is het domein `Azure` , de gebruikers naam `<storage account name>` en het wacht woord `<storage account key>` .

      U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault als waarden gebruiken. Als u dit wilt doen, schakelt u het selectie vakje **Azure Key kluis** in naast deze. Selecteer of bewerk uw bestaande sleutel kluis gekoppelde service of maak een nieuwe. Selecteer vervolgens de naam en versie van het geheim voor uw waarde. Wanneer u uw Key kluis gekoppelde service maakt of bewerkt, kunt u uw bestaande sleutel kluis selecteren of bewerken of een nieuwe maken. Zorg ervoor dat u Data Factory beheerde identiteits toegang verleent aan uw sleutel kluis als u dit nog niet hebt gedaan. U kunt uw geheim ook rechtstreeks in de volgende indeling invoeren: `<key vault linked service name>/<secret name>/<secret version>` .
      
   1. Schakel het selectie vakje **32-bits runtime** in om te kiezen of het pakket moet worden uitgevoerd op een 32-bits runtime.

   1. Voor **de pakket locatie** selecteert **u SSISDB**, **Bestands systeem (pakket)**, **Bestands systeem (project)**, **Inge sloten pakket** of **pakket opslag**. 

##### <a name="package-location-ssisdb"></a>Pakket locatie: SSISDB

**SSISDB** als uw pakket locatie wordt automatisch geselecteerd als uw Azure-SSIS IR is ingericht met een SSIS-catalogus (SSISDB) die wordt gehost door Azure SQL database server/beheerd exemplaar of u kunt het zelf selecteren. Als deze is geselecteerd, voert u de volgende stappen uit.

   1. Als uw Azure-SSIS IR actief is en het selectie vakje **hand matige invoer** is uitgeschakeld, bladert en selecteert u uw bestaande mappen, projecten, pakketten en omgevingen van SSISDB. Selecteer **vernieuwen** om de zojuist toegevoegde mappen, projecten, pakketten of omgevingen van SSISDB op te halen, zodat deze beschikbaar zijn voor bladeren en selectie. Als u de omgevingen voor uw pakket uitvoeringen wilt zoeken en selecteren, moet u uw projecten vooraf configureren om deze omgevingen toe te voegen als verwijzingen vanuit dezelfde mappen onder SSISDB. Zie [SSIS-omgevingen maken en toewijzen](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)voor meer informatie.

   1. Voor het **niveau van logboek registratie** selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. 

   1. Als uw Azure-SSIS IR niet wordt uitgevoerd of het selectie vakje **hand matige invoer** is ingeschakeld, voert u de paden voor het pakket en de omgeving van SSISDB rechtstreeks in de volgende indelingen in: `<folder name>/<project name>/<package name>.dtsx` en `<folder name>/<environment name>` .

      ![Eigenschappen instellen op het tabblad instellingen-hand matig](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>Pakket locatie: bestands systeem (pakket)

**Bestands systeem (pakket)** als uw pakket locatie wordt automatisch geselecteerd als uw Azure-SSIS IR is ingericht zonder SSISDB of u kunt het zelf selecteren. Als deze is geselecteerd, voert u de volgende stappen uit.

![Eigenschappen instellen op het tabblad Instellingen-Bestands systeem (pakket)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. Geef het pakket op dat moet worden uitgevoerd door een UNC-pad (Universal Naming Convention) naar uw pakket bestand (met `.dtsx` ) in het vak **pakket pad** op te geven. U kunt door het pakket bladeren en selecteren door **Bladeren in bestands opslag** te selecteren of het pad hand matig in te voeren. Als u bijvoorbeeld uw pakket opslaat in Azure Files, is het pad `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` . 
   
   1. Als u uw pakket in een afzonderlijk bestand configureert, moet u ook een UNC-pad naar uw configuratie bestand (met `.dtsConfig` ) in het vak **configuratiepad** opgeven. U kunt door de configuratie bladeren en deze selecteren door **Bladeren in file storage** te selecteren of het pad hand matig in te voeren. Als u bijvoorbeeld uw configuratie opslaat in Azure Files, is het pad `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Geef de referenties op voor toegang tot uw pakket-en configuratie bestanden. Als u de waarden voor de referenties voor het uitvoeren van het pakket (voor **Windows-verificatie**) eerder hebt ingevoerd, kunt u ze opnieuw gebruiken door hetzelfde selectie vakje voor het **uitvoeren van pakket uitvoerings referenties** in te scha kelen. Voer anders de waarden in voor uw pakket toegangs referenties in de vakken **domein**, **gebruikers naam** en **wacht woord** . Als u bijvoorbeeld uw pakket en configuratie opslaat in Azure Files, is het domein `Azure` , de gebruikers naam `<storage account name>` en het wacht woord `<storage account key>` . 

      U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault als waarden gebruiken. Als u dit wilt doen, schakelt u het selectie vakje **Azure Key kluis** in naast deze. Selecteer of bewerk uw bestaande sleutel kluis gekoppelde service of maak een nieuwe. Selecteer vervolgens de naam en versie van het geheim voor uw waarde. Wanneer u uw Key kluis gekoppelde service maakt of bewerkt, kunt u uw bestaande sleutel kluis selecteren of bewerken of een nieuwe maken. Zorg ervoor dat u Data Factory beheerde identiteits toegang verleent aan uw sleutel kluis als u dit nog niet hebt gedaan. U kunt uw geheim ook rechtstreeks in de volgende indeling invoeren: `<key vault linked service name>/<secret name>/<secret version>` . 

      Deze referenties worden ook gebruikt voor toegang tot uw onderliggende pakketten in de taak pakket uitvoeren waarnaar wordt verwezen door hun eigen pad en andere configuraties die in uw pakketten zijn opgegeven. 

   1. Als u het beveiligings niveau **EncryptAllWithPassword** of **EncryptSensitiveWithPassword** hebt gebruikt bij het maken van uw pakket via SQL Server Data tools (SSDT), voert u de waarde voor uw wacht woord in het vak **wacht woord voor versleuteling** in. U kunt ook een geheim dat is opgeslagen in uw Azure Key Vault als waarde gebruiken (zie hierboven).
      
      Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt, voert u uw gevoelige waarden opnieuw in bij configuratie bestanden of op de **SSIS-para meters**, **verbindings beheer** functies of **Eigenschappen onderdrukkingen** tabbladen (zie hieronder).
      
      Als u het beveiligings niveau **EncryptAllWithUserKey** hebt gebruikt, wordt dit niet ondersteund. U moet het pakket opnieuw configureren om een ander beveiligings niveau te gebruiken via SSDT of het `dtutil` opdracht regel programma. 

   1. Voor het **niveau van logboek registratie** selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. 
   
   1. Als u de pakket uitvoeringen wilt vastleggen naast het gebruik van de standaard logboek providers die in uw pakket kunnen worden opgegeven, geeft u uw logboekmap op door het UNC-pad op te geven in het vak **pad naar logboek** bestand. U kunt door de map voor het logboek bladeren en deze selecteren door **Bladeren in file storage** te selecteren of het pad hand matig in te voeren. Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het pad naar de logboek registratie `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Er wordt een submap gemaakt in dit pad voor elke afzonderlijke pakket uitvoering, met de naam na de run-ID van de uitvoering van SSIS-pakket activiteit en de logboek bestanden om de vijf minuten worden gegenereerd. 
   
   1. Geef de referenties op voor toegang tot uw Logboekmap. Als u de waarden voor uw pakket toegangs referenties eerder hebt ingevoerd (zie hierboven), kunt u ze opnieuw gebruiken door **hetzelfde selectie vakje als toegangs referenties** voor het pakket in te scha kelen. Voer anders de waarden in voor uw toegangs referenties voor logboek registratie in de vakken **domein**, **gebruikers naam** en **wacht woord** . Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het domein `Azure` , de gebruikers naam `<storage account name>` en het wacht woord `<storage account key>` . U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault gebruiken als waarden (zie hierboven).
   
Voor alle eerder genoemde UNC-paden moet de volledige naam van het bestand kleiner zijn dan 260 tekens. De mapnaam mag niet langer zijn dan 248 tekens.

##### <a name="package-location-file-system-project"></a>Pakket locatie: bestands systeem (project)

Als u **Bestands systeem (project)** als uw pakket locatie selecteert, voert u de volgende stappen uit.

![Eigenschappen instellen op het tabblad Instellingen-Bestands systeem (project)](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. Geef het pakket op dat moet worden uitgevoerd door een UNC-pad op te geven naar uw project bestand (met `.ispac` ) in het vak **pad naar project** en een pakket bestand (met `.dtsx` ) uit uw project in het vak **pakket naam** . U kunt door het project bladeren en selecteren door **Bladeren in file storage** te selecteren of het pad hand matig in te voeren. Als u bijvoorbeeld uw project opslaat in Azure Files, is het pad `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` .

   1. Geef de referenties op voor toegang tot uw project-en pakket bestanden. Als u de waarden voor de referenties voor het uitvoeren van het pakket (voor **Windows-verificatie**) eerder hebt ingevoerd, kunt u ze opnieuw gebruiken door hetzelfde selectie vakje voor het **uitvoeren van pakket uitvoerings referenties** in te scha kelen. Voer anders de waarden in voor uw pakket toegangs referenties in de vakken **domein**, **gebruikers naam** en **wacht woord** . Als u bijvoorbeeld uw project en pakket opslaat in Azure Files, is het domein, de `Azure` gebruikers naam `<storage account name>` en het wacht woord `<storage account key>` . 

      U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault als waarden gebruiken. Als u dit wilt doen, schakelt u het selectie vakje **Azure Key kluis** in naast deze. Selecteer of bewerk uw bestaande sleutel kluis gekoppelde service of maak een nieuwe. Selecteer vervolgens de naam en versie van het geheim voor uw waarde. Wanneer u uw Key kluis gekoppelde service maakt of bewerkt, kunt u uw bestaande sleutel kluis selecteren of bewerken of een nieuwe maken. Zorg ervoor dat u Data Factory beheerde identiteits toegang verleent aan uw sleutel kluis als u dit nog niet hebt gedaan. U kunt uw geheim ook rechtstreeks in de volgende indeling invoeren: `<key vault linked service name>/<secret name>/<secret version>` . 

      Deze referenties worden ook gebruikt om toegang te krijgen tot uw onderliggende pakketten in de taak pakket uitvoeren die vanuit hetzelfde project worden verwezen. 

   1. Als u het beveiligings niveau **EncryptAllWithPassword** of **EncryptSensitiveWithPassword** hebt gebruikt toen u het pakket hebt gemaakt via SSDT, voert u de waarde voor uw wacht woord in het vak **versleutelings wachtwoord** in. U kunt ook een geheim dat is opgeslagen in uw Azure Key Vault als waarde gebruiken (zie hierboven).
      
      Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt, voert u uw gevoelige waarden opnieuw in op de tabbladen **SSIS-para meters**, **verbindings beheer** of **eigenschaps onderdrukking** (zie hieronder).
      
      Als u het beveiligings niveau **EncryptAllWithUserKey** hebt gebruikt, wordt dit niet ondersteund. U moet het pakket opnieuw configureren om een ander beveiligings niveau te gebruiken via SSDT of het `dtutil` opdracht regel programma. 

   1. Voor het **niveau van logboek registratie** selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. 
   
   1. Als u de pakket uitvoeringen wilt vastleggen naast het gebruik van de standaard logboek providers die in uw pakket kunnen worden opgegeven, geeft u uw logboekmap op door het UNC-pad op te geven in het vak **pad naar logboek** bestand. U kunt door de map voor het logboek bladeren en deze selecteren door **Bladeren in file storage** te selecteren of het pad hand matig in te voeren. Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het pad naar de logboek registratie `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Er wordt een submap gemaakt in dit pad voor elke afzonderlijke pakket uitvoering, met de naam na de run-ID van de uitvoering van SSIS-pakket activiteit en de logboek bestanden om de vijf minuten worden gegenereerd. 
   
   1. Geef de referenties op voor toegang tot uw Logboekmap. Als u de waarden voor uw pakket toegangs referenties eerder hebt ingevoerd (zie hierboven), kunt u ze opnieuw gebruiken door **hetzelfde selectie vakje als toegangs referenties** voor het pakket in te scha kelen. Voer anders de waarden in voor uw toegangs referenties voor logboek registratie in de vakken **domein**, **gebruikers naam** en **wacht woord** . Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het domein `Azure` , de gebruikers naam `<storage account name>` en het wacht woord `<storage account key>` . U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault gebruiken als waarden (zie hierboven).
   
Voor alle eerder genoemde UNC-paden moet de volledige naam van het bestand kleiner zijn dan 260 tekens. De mapnaam mag niet langer zijn dan 248 tekens.

##### <a name="package-location-embedded-package"></a>Pakket locatie: Inge sloten pakket

Als u **Inge sloten pakket** als uw pakket locatie selecteert, voert u de volgende stappen uit.

![Eigenschappen instellen op het tabblad Instellingen-Inge sloten pakket](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. Sleep het pakket bestand (met) en `.dtsx` **Upload** het uit een bestandsmap naar het meegeleverde vak. Uw pakket wordt automatisch gecomprimeerd en Inge sloten in de nettolading van de activiteit. Als u het pakket hebt Inge sloten, kunt u het later **downloaden** om het te bewerken. U kunt ook uw Inge sloten pakket **para meters** door het toe te wijzen aan een pijplijn parameter die in meerdere activiteiten kan worden gebruikt, waardoor u de grootte van de pijplijn lading optimaliseert. Het insluiten van Project bestanden (met `.ispac` ) wordt momenteel niet ondersteund. u kunt geen SSIS-para meters/verbindings beheer gebruiken met bereik op project niveau in uw Inge sloten pakketten.
   
   1. Als uw Inge sloten pakket niet is versleuteld en wij het gebruik van de Execute package-taak (EPT) detecteren, wordt het selectie vakje **pakket taak uitvoeren** automatisch ingeschakeld en worden de onderliggende pakketten waarnaar wordt verwezen door het bestandssysteempad automatisch toegevoegd, zodat u ze ook kunt insluiten.
   
      Als het gebruik van EPT niet kan worden gedetecteerd, moet u hand matig het selectie vakje **pakket taak uitvoeren** inschakelen en uw onderliggende pakketten toevoegen waarnaar wordt verwezen door het bestandssysteempad een voor een. u kunt ze ook insluiten. Als uw onderliggende pakketten zijn opgeslagen in SQL Server Data Base (MSDB), kunt u ze niet insluiten. u moet er dus voor zorgen dat uw Azure-SSIS IR toegang heeft tot MSDB om ze op te halen met behulp van hun SQL Server verwijzingen. Het insluiten van Project bestanden (met `.ispac` ) wordt momenteel niet ondersteund. u kunt geen op project gebaseerde verwijzingen voor uw onderliggende pakketten gebruiken.
   
   1. Als u het beveiligings niveau **EncryptAllWithPassword** of **EncryptSensitiveWithPassword** hebt gebruikt toen u het pakket hebt gemaakt via SSDT, voert u de waarde voor uw wacht woord in het vak **versleutelings wachtwoord** in. 
   
      U kunt ook een geheim dat is opgeslagen in uw Azure Key Vault gebruiken als waarde. Als u dit wilt doen, schakelt u het selectie vakje **Azure Key kluis** in naast het. Selecteer of bewerk uw bestaande sleutel kluis gekoppelde service of maak een nieuwe. Selecteer vervolgens de naam en versie van het geheim voor uw waarde. Wanneer u uw Key kluis gekoppelde service maakt of bewerkt, kunt u uw bestaande sleutel kluis selecteren of bewerken of een nieuwe maken. Zorg ervoor dat u Data Factory beheerde identiteits toegang verleent aan uw sleutel kluis als u dit nog niet hebt gedaan. U kunt uw geheim ook rechtstreeks in de volgende indeling invoeren: `<key vault linked service name>/<secret name>/<secret version>` .
      
      Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt, voert u uw gevoelige waarden opnieuw in bij configuratie bestanden of op de **SSIS-para meters**, **verbindings beheer** functies of **Eigenschappen onderdrukkingen** tabbladen (zie hieronder).
      
      Als u het beveiligings niveau **EncryptAllWithUserKey** hebt gebruikt, wordt dit niet ondersteund. U moet het pakket opnieuw configureren om een ander beveiligings niveau te gebruiken via SSDT of het `dtutil` opdracht regel programma.

   1. Voor het **niveau van logboek registratie** selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. 
   
   1. Als u de pakket uitvoeringen wilt vastleggen naast het gebruik van de standaard logboek providers die in uw pakket kunnen worden opgegeven, geeft u uw logboekmap op door het UNC-pad op te geven in het vak **pad naar logboek** bestand. U kunt door de map voor het logboek bladeren en deze selecteren door **Bladeren in file storage** te selecteren of het pad hand matig in te voeren. Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het pad naar de logboek registratie `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Er wordt een submap gemaakt in dit pad voor elke afzonderlijke pakket uitvoering, met de naam na de run-ID van de uitvoering van SSIS-pakket activiteit en de logboek bestanden om de vijf minuten worden gegenereerd. 
   
   1. Geef de referenties op voor toegang tot uw Logboekmap door hun waarden in de vakken **domein**, **gebruikers naam** en **wacht woord** in te voeren. Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het domein `Azure` , de gebruikers naam `<storage account name>` en het wacht woord `<storage account key>` . U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault gebruiken als waarden (zie hierboven).
   
Voor alle eerder genoemde UNC-paden moet de volledige naam van het bestand kleiner zijn dan 260 tekens. De mapnaam mag niet langer zijn dan 248 tekens.

##### <a name="package-location-package-store"></a>Pakket locatie: pakket archief

Als u **pakket archief** als uw pakket locatie selecteert, voert u de volgende stappen uit.

![Eigenschappen instellen op het tabblad Instellingen-pakket archief](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. Voor de naam van het **pakket archief** selecteert u een bestaande pakket opslag die is gekoppeld aan uw Azure-SSIS IR.

   1. Geef uw pakket op dat moet worden uitgevoerd door het pad (zonder) op te geven `.dtsx` van het geselecteerde pakket archief in het vak **pakket pad** . Als het geselecteerde pakket archief zich boven op het bestands systeem/Azure Files bevindt, kunt u door **Bladeren door bestand opslag** selecteren en het pad in de indeling van opgeven `<folder name>\<package name>` . U kunt ook nieuwe pakketten importeren in het geselecteerde pakket archief via SQL Server Management Studio (SSMS), vergelijkbaar met de [verouderde SSIS-pakket opslag](/sql/integration-services/service/package-management-ssis-service). Zie [SSIS-pakketten beheren met pakketarchieven van Azure-SSIS IR](./azure-ssis-integration-runtime-package-store.md) voor meer informatie.

   1. Als u uw pakket in een afzonderlijk bestand configureert, moet u een UNC-pad naar uw configuratie bestand opgeven (met `.dtsConfig` ) in het vak **configuratiepad** . U kunt door de configuratie bladeren en deze selecteren door **Bladeren in file storage** te selecteren of het pad hand matig in te voeren. Als u bijvoorbeeld uw configuratie opslaat in Azure Files, is het pad `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` .

   1. Schakel het selectie vakje **toegangs referenties** voor de configuratie in om te kiezen of u de referenties voor de afzonderlijke toegang tot uw configuratie bestand wilt opgeven. Dit is nodig wanneer het geselecteerde pakket archief zich boven op SQL Server Data Base (MSDB) bevindt dat door uw door Azure SQL beheerde instantie wordt gehost of uw configuratie bestand niet opslaat.
   
      Als u de waarden voor de referenties voor het uitvoeren van het pakket (voor **Windows-verificatie**) eerder hebt ingevoerd, kunt u ze opnieuw gebruiken door hetzelfde selectie vakje voor het **uitvoeren van pakket uitvoerings referenties** in te scha kelen. Voer anders de waarden in voor uw toegangs referenties voor de configuratie in de vakken **domein**, **gebruikers naam** en **wacht woord** . Als u bijvoorbeeld uw configuratie opslaat in Azure Files, is het domein, de `Azure` gebruikers naam `<storage account name>` en het wacht woord `<storage account key>` . 

      U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault als waarden gebruiken. Als u dit wilt doen, schakelt u het selectie vakje **Azure Key kluis** in naast deze. Selecteer of bewerk uw bestaande sleutel kluis gekoppelde service of maak een nieuwe. Selecteer vervolgens de naam en versie van het geheim voor uw waarde. Wanneer u uw Key kluis gekoppelde service maakt of bewerkt, kunt u uw bestaande sleutel kluis selecteren of bewerken of een nieuwe maken. Zorg ervoor dat u Data Factory beheerde identiteits toegang verleent aan uw sleutel kluis als u dit nog niet hebt gedaan. U kunt uw geheim ook rechtstreeks in de volgende indeling invoeren: `<key vault linked service name>/<secret name>/<secret version>` .

   1. Als u het beveiligings niveau **EncryptAllWithPassword** of **EncryptSensitiveWithPassword** hebt gebruikt toen u het pakket hebt gemaakt via SSDT, voert u de waarde voor uw wacht woord in het vak **versleutelings wachtwoord** in. U kunt ook een geheim dat is opgeslagen in uw Azure Key Vault als waarde gebruiken (zie hierboven).
      
      Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt, voert u uw gevoelige waarden opnieuw in bij configuratie bestanden of op de **SSIS-para meters**, **verbindings beheer** functies of **Eigenschappen onderdrukkingen** tabbladen (zie hieronder).
      
      Als u het beveiligings niveau **EncryptAllWithUserKey** hebt gebruikt, wordt dit niet ondersteund. U moet het pakket opnieuw configureren om een ander beveiligings niveau te gebruiken via SSDT of het `dtutil` opdracht regel programma. 

   1. Voor het **niveau van logboek registratie** selecteert u een vooraf gedefinieerd bereik van logboek registratie voor de uitvoering van het pakket. Schakel het selectie vakje **aangepast** in als u in plaats daarvan de aangepaste logboek naam wilt invoeren. 
   
   1. Als u de pakket uitvoeringen wilt vastleggen naast het gebruik van de standaard logboek providers die in uw pakket kunnen worden opgegeven, geeft u uw logboekmap op door het UNC-pad op te geven in het vak **pad naar logboek** bestand. U kunt door de map voor het logboek bladeren en deze selecteren door **Bladeren in file storage** te selecteren of het pad hand matig in te voeren. Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het pad naar de logboek registratie `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` . Er wordt een submap gemaakt in dit pad voor elke afzonderlijke pakket uitvoering, met de naam na de run-ID van de uitvoering van SSIS-pakket activiteit en de logboek bestanden om de vijf minuten worden gegenereerd. 
   
   1. Geef de referenties op voor toegang tot uw Logboekmap door hun waarden in de vakken **domein**, **gebruikers naam** en **wacht woord** in te voeren. Als u bijvoorbeeld uw logboeken opslaat in Azure Files, is het domein `Azure` , de gebruikers naam `<storage account name>` en het wacht woord `<storage account key>` . U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault gebruiken als waarden (zie hierboven).
   
Voor alle eerder genoemde UNC-paden moet de volledige naam van het bestand kleiner zijn dan 260 tekens. De mapnaam mag niet langer zijn dan 248 tekens.

#### <a name="ssis-parameters-tab"></a>Tabblad SSIS-para meters

Voer de volgende stappen uit op het tabblad **SSIS-para meters** van de activiteit voor het uitvoeren van SSIS-pakketten.

![Eigenschappen instellen op het tabblad SSIS-para meters](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. Als uw Azure-SSIS IR wordt uitgevoerd, wordt **SSISDB** geselecteerd als uw pakket locatie en wordt het selectie vakje **hand matige vermeldingen** op het tabblad **instellingen** uitgeschakeld. de bestaande SSIS-para meters in het geselecteerde project en pakket van SSISDB worden weer gegeven zodat u waarden aan hen kunt toewijzen. Als dat niet het geval is, kunt u ze één voor één invoeren om waarden hand matig toe te wijzen. Zorg ervoor dat deze bestaan en correct zijn ingevoerd voor het slagen van de pakket uitvoering. 
   
   1. Als u het **EncryptSensitiveWithUserKey** -beveiligings niveau hebt gebruikt bij het maken van het pakket via SSDT en het **Bestands systeem (pakket)**, het **Bestands systeem (project)**, een **Inge sloten pakket** of **pakket archief** is geselecteerd als uw pakket locatie, moet u ook uw gevoelige para meters opnieuw invoeren om waarden toe te wijzen aan deze op dit tabblad. 
   
Wanneer u waarden toewijst aan uw para meters, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, Data Factory systeem variabelen en Data Factory pijplijn parameters of-variabelen.

U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault als waarden gebruiken. Als u dit wilt doen, schakelt u het selectie vakje **Azure Key kluis** in naast deze. Selecteer of bewerk uw bestaande sleutel kluis gekoppelde service of maak een nieuwe. Selecteer vervolgens de naam en versie van het geheim voor uw waarde. Wanneer u uw Key kluis gekoppelde service maakt of bewerkt, kunt u uw bestaande sleutel kluis selecteren of bewerken of een nieuwe maken. Zorg ervoor dat u Data Factory beheerde identiteits toegang verleent aan uw sleutel kluis als u dit nog niet hebt gedaan. U kunt uw geheim ook rechtstreeks in de volgende indeling invoeren: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="connection-managers-tab"></a>Tabblad verbindings beheer

Voer de volgende stappen uit op het tabblad **verbindings beheer** van de activiteit voor het uitvoeren van SSIS-pakketten.

![Eigenschappen instellen op het tabblad verbindings beheer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. Als uw Azure-SSIS IR wordt uitgevoerd, wordt **SSISDB** geselecteerd als uw pakket locatie en wordt het selectie vakje **hand matige vermeldingen** op het tabblad **instellingen** uitgeschakeld, worden de bestaande verbindings beheer in uw geselecteerde project en pakket van SSISDB weer gegeven om waarden aan hun eigenschappen toe te wijzen. Als dat niet het geval is, kunt u ze één voor één invoeren om waarden hand matig toe te wijzen aan hun eigenschappen. Zorg ervoor dat deze bestaan en correct zijn ingevoerd voor het slagen van de pakket uitvoering. 
   
      U kunt het juiste **bereik**, de **naam** en **eigenschapnamen** voor verbindings beheer verkrijgen door het pakket te openen dat het bevat op SSDT. Nadat het pakket is geopend, selecteert u de relevante verbindings beheer om de namen en waarden voor alle eigenschappen ervan weer te geven in het venster **Eigenschappen** van SSDT. Met deze informatie kunt u de waarden van eigenschappen van verbindings beheer tijdens runtime onderdrukken. 

      ![Eigenschappen van verbindings beheer ophalen uit SSDT](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      Bijvoorbeeld, zonder uw oorspronkelijke pakket op SSDT te wijzigen, kunt u de on-premises-to-on-premises gegevens stromen die worden uitgevoerd op SQL Server converteren naar on-premises-naar-Cloud gegevens stromen die worden uitgevoerd op SSIS IR in ADF door de waarden van **ConnectByProxy**, **Connections Tring** en **ConnectUsingManagedIdentity** -eigenschappen in bestaande verbindings beheer tijdens runtime te overschrijven.
      
      Met deze runtime onderdrukkingen kunt u Self-Hosted IR (SHIR) inschakelen als proxy voor SSIS IR bij het openen van gegevens on-premises, Zie [Configuring SHIR as a proxy for SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)en Azure SQL database/Managed instance Connections met behulp van het nieuwste MSOLEDBSQL-stuur programma waarmee Azure Active Directory (Aad)-verificatie wordt ingeschakeld met de [door ADF beheerde identiteit.](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

      ![Eigenschappen instellen van SSDT op het tabblad verbindings beheer](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt toen u het pakket hebt gemaakt via SSDT en **Bestands systeem (pakket)**, een **Bestands systeem (project)**, een **Inge sloten pakket** of **pakket archief** is geselecteerd als uw pakket locatie, moet u ook de eigenschappen van uw gevoelige verbindings beheer opnieuw invoeren om waarden toe te wijzen aan deze op dit tabblad. 

Wanneer u waarden toewijst aan de eigenschappen van verbindings beheer, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, Data Factory systeem variabelen en Data Factory pijplijn parameters of-variabelen. 

U kunt ook geheimen die zijn opgeslagen in uw Azure Key Vault als waarden gebruiken. Als u dit wilt doen, schakelt u het selectie vakje **Azure Key kluis** in naast deze. Selecteer of bewerk uw bestaande sleutel kluis gekoppelde service of maak een nieuwe. Selecteer vervolgens de naam en versie van het geheim voor uw waarde. Wanneer u uw Key kluis gekoppelde service maakt of bewerkt, kunt u uw bestaande sleutel kluis selecteren of bewerken of een nieuwe maken. Zorg ervoor dat u Data Factory beheerde identiteits toegang verleent aan uw sleutel kluis als u dit nog niet hebt gedaan. U kunt uw geheim ook rechtstreeks in de volgende indeling invoeren: `<key vault linked service name>/<secret name>/<secret version>` . 

#### <a name="property-overrides-tab"></a>Tabblad onderdrukkingen van eigenschappen

Voer de volgende stappen uit op het tabblad **Eigenschappen onderdrukking** van de activiteit voor het uitvoeren van SSIS-pakketten.

![Eigenschappen instellen op het tabblad Eigenschappen onderdrukkingen](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. Voer één voor één de paden van bestaande eigenschappen in het geselecteerde pakket in om de waarden hand matig toe te wijzen. Zorg ervoor dat deze bestaan en correct zijn ingevoerd voor het slagen van de pakket uitvoering. Als u bijvoorbeeld de waarde van uw gebruikers variabele wilt overschrijven, voert u het pad in de volgende indeling in: `\Package.Variables[User::<variable name>].Value` . 

      U kunt het juiste eigenschapspad voor elke **pakket eigenschap verkrijgen** door het pakket te openen dat het bevat op SSDT. Nadat het pakket is geopend, selecteert u de eigenschap controle stroom en **configuratie** in het venster **Eigenschappen** van SSDT. Selecteer vervolgens de knop met weglatings tekens (**...**) naast de bijbehorende **configuratie** -eigenschap om de **package configurations-Organizer** te openen die normaal gesp roken wordt gebruikt voor het [maken van pakket configuraties in pakket implementatie model](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations). 

      ![Pakket eigenschappen ophalen van SSDT-configurations-eigenschap](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      Schakel op **de package** configurations Organizer het selectie vakje **pakket configuraties inschakelen** in en de knop **toevoegen...** om de **wizard pakket configuratie** te openen. 
      
      Selecteer in de **wizard pakket configuratie** het item **XML-configuratie bestand** in het vervolg keuzemenu **configuratie type** en klik op de knop **configuratie-instellingen opgeven** , voer de naam van het configuratie bestand in en selecteer de knop **volgende >** . 

      ![Pakket eigenschappen ophalen uit SSDT-Configuration Organizer](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      Selecteer ten slotte de eigenschappen van het pakket waarvan u het pad wilt en de knop **volgende >** .  U kunt nu de gewenste paden voor het pakket eigenschap kopiëren & plakken en opslaan in het configuratie bestand. Met deze informatie kunt u de waarden van elke pakket eigenschappen tijdens runtime overschrijven. 

      ![Pakket eigenschappen ophalen van SSDT-configuratie wizard](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. Als u het beveiligings niveau **EncryptSensitiveWithUserKey** hebt gebruikt toen u het pakket hebt gemaakt via SSDT en **Bestands systeem (pakket)**, **Bestands systeem (project)**, **Inge sloten pakket** of **pakket archief** is geselecteerd als uw pakket locatie, moet u ook uw gevoelige pakket eigenschappen opnieuw invoeren om waarden toe te wijzen aan hen op dit tabblad. 
   
Wanneer u waarden toewijst aan de pakket eigenschappen, kunt u dynamische inhoud toevoegen met behulp van expressies, functies, Data Factory systeem variabelen en Data Factory pijplijn parameters of-variabelen.

De waarden die zijn toegewezen in configuratie bestanden en op het tabblad **SSIS-para meters** kunnen worden overschreven met behulp van de tabbladen **verbindings beheer** of **eigenschaps onderdrukkingen** . De waarden die zijn toegewezen op het tabblad **verbindings beheer** kunnen ook worden overschreven met behulp van het tabblad **Eigenschappen onderdrukkingen** .

Selecteer **valideren** op de werk balk om de configuratie van de pijp lijn te valideren. Selecteer om het **rapport pijplijn validatie** te sluiten **>>** .

Als u de pijp lijn naar Data Factory wilt publiceren, selecteert u **Alles publiceren**. 

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren
In deze stap activeert u een pijplijnuitvoering. 

1. Als u een pijplijn uitvoering wilt activeren, selecteert u **activeren** op de werk balk en selecteert u **nu activeren**. 

   ![Nu activeren](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. Selecteer in het venster **Pijplijnuitvoering** de optie **Voltooien**. 

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Ga naar het tabblad **Controleren** aan de linkerkant. U ziet de pijplijn uitvoering en de status ervan samen met andere informatie, zoals de start tijd van de **uitvoering** . Selecteer **Vernieuwen** om de weergave te vernieuwen.

   ![Pijplijnuitvoeringen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Selecteer de koppeling **Uitvoeringen van activiteit weergeven** in de kolom **Acties**. U ziet dat er slechts één activiteit wordt uitgevoerd omdat de pijp lijn maar één activiteit heeft. Het is de activiteit voor het uitvoeren van SSIS-pakketten.

   ![Uitvoering van activiteiten](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Voer de volgende query uit op de SSISDB-data base in uw SQL-Server om te controleren of het pakket is uitgevoerd. 

   ```sql
   select * from catalog.executions
   ```

   ![Pakket uitvoeringen controleren](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. U kunt ook de SSISDB-uitvoerings-ID ophalen uit de uitvoer van de uitvoering van de pijplijn activiteit en de ID gebruiken om uitgebreidere uitvoerings logboeken en fout berichten in SQL Server Management Studio te controleren.

   ![De uitvoerings-ID ophalen.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>De pijp lijn plannen met een trigger

U kunt ook een geplande trigger voor uw pijp lijn maken zodat de pijp lijn op een schema wordt uitgevoerd, zoals elk uur of dagelijks. Zie [een Data Factory-Data Factory gebruikers interface maken](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)voor een voor beeld.

## <a name="run-a-package-with-powershell"></a>Een pakket uitvoeren met Power shell
In deze sectie gebruikt u Azure PowerShell om een Data Factory-pijp lijn te maken met een activiteit voor het uitvoeren van SSIS-pakketten die uw SSIS-pakket uitvoert. 

Installeer de nieuwste Azure PowerShell-modules door de stapsgewijze instructies te volgen in [How to install and configure Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Een data factory met Azure-SSIS IR maken
U kunt een bestaand data factory gebruiken dat al Azure-SSIS IR ingericht is of een nieuw data factory maakt met Azure-SSIS IR. Volg de stapsgewijze instructies in de [zelf studie: implementeer SSIS-pakketten naar Azure via Power shell](./tutorial-deploy-ssis-packages-azure-powershell.md).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Een pijp lijn maken met een activiteit voor het uitvoeren van SSIS-pakketten 
In deze stap maakt u een pijp lijn met een activiteit voor het uitvoeren van SSIS-pakketten. De activiteit voert uw SSIS-pakket uit. 

1. Maak een JSON-bestand `RunSSISPackagePipeline.json` met de naam in de `C:\ADF\RunSSISPackage` map met inhoud die vergelijkbaar is met het volgende voor beeld.

   > [!IMPORTANT]
   > Vervang object namen, beschrijvingen en paden, eigenschaps-of parameter waarden, wacht woorden en andere variabelen waarden voordat u het bestand opslaat. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Als u pakketten wilt uitvoeren die zijn opgeslagen in bestands systeem/Azure Files, voert u de waarden voor uw pakket-en logboek locatie-eigenschappen als volgt in:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Als u pakketten wilt uitvoeren in projecten die zijn opgeslagen in het bestands systeem/Azure Files, voert u de waarden voor de pakket locatie-eigenschappen als volgt in:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Als u Inge sloten pakketten wilt uitvoeren, voert u de waarden voor de pakket locatie-eigenschappen als volgt in:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Als u pakketten wilt uitvoeren die zijn opgeslagen in pakket archieven, voert u de waarden voor uw pakket-en configuratie locatie eigenschappen als volgt in:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Ga in Azure PowerShell naar de `C:\ADF\RunSSISPackage` map.

3. Als u de pijp lijn **RunSSISPackagePipeline** wilt maken, voert u de cmdlet **set-AzDataFactoryV2Pipeline** uit.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Hier volgt een voor beeld van de uitvoer:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>De pijplijn uitvoeren
Gebruik de cmdlet **invoke-AzDataFactoryV2Pipeline** om de pijp lijn uit te voeren. De cmdlet retourneert de id voor de pijplijnuitvoering voor toekomstige controle.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken

Voer het volgende PowerShell-script uit om continu de status van de pijplijnuitvoering te controleren totdat het kopiëren van de gegevens is voltooid. Kopieer of plak het volgende script in het Power shell-venster en selecteer ENTER. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

U kunt de pijp lijn ook bewaken met behulp van de Azure Portal. Zie [de pijp lijn controleren](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)voor stapsgewijze instructies.

### <a name="schedule-the-pipeline-with-a-trigger"></a>De pijp lijn plannen met een trigger
In de vorige stap hebt u de pijp lijn op aanvraag uitgevoerd. U kunt ook een schema trigger maken om de pijp lijn op een schema uit te voeren, zoals elk uur of dagelijks.

1. Maak een JSON-bestand `MyTrigger.json` met de naam in de `C:\ADF\RunSSISPackage` map met de volgende inhoud: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Ga in Azure PowerShell naar de `C:\ADF\RunSSISPackage` map.
1. Voer de cmdlet **set-AzDataFactoryV2Trigger** uit om de trigger te maken. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. De trigger heeft standaard de status gestopt. Start de trigger door de cmdlet **Start-AzDataFactoryV2Trigger** uit te voeren. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Controleer of de trigger is gestart door de cmdlet **Get-AzDataFactoryV2Trigger** uit te voeren. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Voer de volgende opdracht na het volgende uur uit. Als de huidige tijd bijvoorbeeld 3:25 uur UTC is, voert u de opdracht uit op 4 PM UTC. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Voer de volgende query uit op de SSISDB-data base in uw SQL-Server om te controleren of het pakket is uitgevoerd. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Volgende stappen
Zie het volgende blog bericht:
- [Uw ETL/ELT-werk stromen moderniseren en uitbreiden met SSIS-activiteiten in Azure Data Factory pijp lijnen](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)