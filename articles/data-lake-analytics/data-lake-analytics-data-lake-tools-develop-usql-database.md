---
title: Een U-SQL database project ontwikkelen-Azure Data Lake
description: Meer informatie over het ontwikkelen van een U-SQL database met behulp van Azure Data Lake-Hulpprogram Ma's voor Visual Studio.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 706457a602e20dd37e64e5f389948b351ac8ebcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015244"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Gebruik een U-SQL database project om een U-SQL database te ontwikkelen voor Azure Data Lake

U-SQL database biedt gestructureerde weer gaven voor ongestructureerde gegevens en beheerde gestructureerde gegevens in tabellen. Het bevat ook een algemeen Meta Data Catalog-systeem voor het organiseren van uw gestructureerde gegevens en aangepaste code. De-data base is het concept waarin deze verwante objecten worden gegroepeerd.

Meer informatie over [U-SQL database en DDL (Data Definition Language)](/u-sql/data-definition-language-ddl-statements).

Het U-SQL database project is een project type in Visual Studio waarmee ontwikkel aars hun U-SQL-data bases snel en eenvoudig kunnen ontwikkelen, beheren en implementeren.

## <a name="create-a-u-sql-database-project"></a>Een U-SQL database-project maken

Azure Data Lake-Hulpprogram Ma's voor Visual Studio hebben een nieuwe project sjabloon met de naam U-SQL database project toegevoegd na versie 2.3.3000.0. Als u een U-SQL-project wilt maken, selecteert u **bestand > nieuw > project**. U kunt het project U-SQL Database vinden onder **Azure Data Lake > U-SQL-knoop punt**.

![Data Lake-Hulpprogram Ma's voor Visual Studio--een U-SQL database-project maken](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png)

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>U-SQL database-objecten ontwikkelen met behulp van een database project

Klik met de rechter muisknop op het U-SQL database project. Selecteer **> nieuw item toevoegen**. Alle nieuwe ondersteunde object typen vindt u in de wizard **Nieuw item toevoegen** .

Voor een niet-Assembly-object (bijvoorbeeld een functie met tabel waarden) wordt een nieuw U-SQL-script gemaakt nadat u een nieuw item hebt toegevoegd. U kunt beginnen met het ontwikkelen van de DDL-instructie voor dat object in de editor.

Voor een assembly-object biedt het hulp programma een gebruiks vriendelijke UI-editor waarmee u de assembly kunt registreren en DLL-bestanden en andere extra bestanden moet implementeren. De volgende stappen laten zien hoe u een assembly-object definitie kunt toevoegen aan het U-SQL database project:

1. Voeg verwijzingen naar het C#-project toe die de UDO/UDAG/UDF bevatten voor het U-SQL database-project.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio-referentie toevoegen](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png)

   ![Data Lake-Hulpprogram Ma's voor Visual Studio-een U-SQL database project referentie toevoegen](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2. Kies in de ontwerp weergave van de assembly de assembly waarnaar wordt verwezen in de vervolg keuzelijst een **assembly-verwijzing maken** .

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--assembly maken op basis van verwijzing](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3. Voeg **beheerde afhankelijkheden** en **extra bestanden** toe, indien aanwezig. Wanneer u extra bestanden toevoegt, gebruikt het hulp programma het relatieve pad om ervoor te zorgen dat de assembly's op uw lokale computer en op de build-machine later kunnen worden gevonden.

@_DeployTempDirectory is een vooraf gedefinieerde variabele die het hulp programma naar de map build uitvoermap wijst. In de map build-uitvoer heeft elke assembly een submap met de naam van de assembly. Alle Dll's en extra bestanden bevinden zich in die submap.

## <a name="build-a-u-sql-database-project"></a>Een U-SQL database-project bouwen

Het build-uitvoer voor een U-SQL database project is een U-SQL database implementatie pakket met de naam met het achtervoegsel `.usqldbpack` . Het `.usqldbpack` pakket is een zip-bestand dat alle DDL-instructies bevat in één U-SQL-script in de **DDL** -map en alle dll's en aanvullende bestanden voor assembly's in de map **temp** .

Meer informatie over het [maken van een U-SQL database project met de MSBuild opdracht regel en een Azure DevOps Services-bouw taak](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Een U-SQL database implementeren

Het. usqldbpack-pakket kan worden geïmplementeerd naar een lokaal account of een Azure Data Lake Analytics account met behulp van Visual Studio of de implementatie-SDK.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Een U-SQL database implementeren in Visual Studio

U kunt een U-SQL database implementeren via een U-SQL database-project of een. usqldbpack-pakket in Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Implementeren via een U-SQL database-project

1. Klik met de rechter muisknop op het U-SQL database project en selecteer vervolgens **implementeren**.

1. Selecteer in de wizard voor het **implementeren van u-SQL database** het **ADLA-account** waarnaar u de Data Base wilt implementeren. Zowel lokale accounts als ADLA-accounts worden ondersteund.

1. De **Data Base** wordt automatisch ingevuld en verwijst naar het. usqldbpack-pakket in de map build uitvoermap van het project.

1. Voer een naam in de naam van de **Data Base** in om een Data Base te maken. Als er al een Data Base met dezelfde naam bestaat in het doel Azure Data Lake Analytics account, worden alle objecten die in het database project zijn gedefinieerd, gemaakt zonder de data base opnieuw te maken.

1. Selecteer **verzenden** om de U-SQL database te implementeren. Alle resources (assembly's en aanvullende bestanden) worden geüpload en een U-SQL-taak die alle DDL-instructies bevat, wordt verzonden.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--Implementeer U-SQL database project](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--de wizard voor het implementeren van U-SQL database-project](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Implementeren via een U-SQL database implementatie pakket

1. Open **Server Explorer**. Vouw vervolgens het **Azure data Lake Analytics account** uit waarop u de Data Base wilt implementeren.

1. Klik met de rechter muisknop op **U-SQL-data bases** en kies vervolgens **Data Base implementeren**.

1. Stel de **database bron** in op het pad naar U-SQL database-implementatie pakket (. usqldbpack-bestand).

1. Voer de **database naam** in om een Data Base te maken. Als er een Data Base is met dezelfde naam die al bestaat in het doel Azure Data Lake Analytics account, worden alle objecten die in het database project zijn gedefinieerd, gemaakt zonder de data base opnieuw te maken.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--een U-SQL database-pakket implementeren](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--Deploying U-SQL database package wizard](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>U-SQL database implementeren met behulp van de SDK

`PackageDeploymentTool.exe` biedt de programmeer-en opdracht regel interfaces die U helpen bij het implementeren van U-SQL-data bases. De SDK is opgenomen in het [U-SQL SDK Nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), dat zich bevindt in `build/runtime/PackageDeploymentTool.exe` .

Meer [informatie over de SDK en het instellen van CI/cd-pijp lijn voor U-SQL database-implementatie](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Verwijzen naar een U-SQL database-project

Een U-SQL-project kan verwijzen naar een U-SQL database-project. De verwijzing is van invloed op twee werk belastingen:

- *Project build*: Stel de database omgevingen waarnaar wordt verwezen in voordat U de U-SQL-scripts bouwt.
- *Lokaal uitvoeren van (a local-project) account*: de database omgevingen waarnaar wordt verwezen, worden geïmplementeerd naar een account van het lokale project voordat U-SQL-script uitvoering. Meer [informatie over lokale uitvoeringen en het verschil tussen (de lokale machine) en (een lokaal project)-account](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Een U-SQL database-verwijzing toevoegen

1. Klik met de rechter muisknop op het U-SQL-project in **Solution Explorer** en kies vervolgens **toevoegen U-SQL database verwijzing...**.

    ![Data Lake-Hulpprogram Ma's voor Visual Studio--data base-project referentie toevoegen](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Een database referentie configureren van een U-SQL database project in de huidige oplossing of in een U-SQL database-pakket bestand.

3. Geef de naam op voor de data base.

    ![Wizard Data Lake-Hulpprogram Ma's voor Visual Studio data base-project referentie toevoegen](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Volgende stappen

- [Een CI/CD-pijp lijn voor Azure Data Lake Analytics instellen](data-lake-analytics-cicd-overview.md)
- [Uw Azure Data Lake Analytics code testen](data-lake-analytics-cicd-test.md)
- [U-SQL-script uitvoeren op uw lokale computer](data-lake-analytics-data-lake-tools-local-run.md)
