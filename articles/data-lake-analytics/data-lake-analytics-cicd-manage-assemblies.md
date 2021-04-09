---
title: U-SQL-assembly's beheren in een CI/CD-pijp lijn-Azure Data Lake
description: Meer informatie over de aanbevolen procedures voor het beheren van U-SQL C#-assembly's in een CI/CD-pijp lijn met Azure DevOps.
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015278"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Aanbevolen procedures voor het beheren van U-SQL-assembly's in een CI/CD-pijp lijn

In dit artikel leert u hoe u-SQL-assembly-bron code kunt beheren met het zojuist geïntroduceerde U-SQL database-project. U leert ook hoe u een pipeline voor een continue integratie en implementatie (CI/CD) voor assembly registratie instelt met behulp van Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Het U-SQL database project gebruiken voor het beheren van assembly-bron code

[Het U-SQL database project](data-lake-analytics-data-lake-tools-develop-usql-database.md) is een project type in Visual Studio waarmee ontwikkel aars hun U-SQL-data bases snel en eenvoudig kunnen ontwikkelen, beheren en implementeren. U kunt alle U-SQL database-objecten (met uitzonde ring van referenties) beheren met het U-SQL database-project.

Gebruik de volgende opties voor het beheren van de C#-bron code en de DDL-U-SQL-scripts voor assembly registratie.

- U-SQL database project om U-SQL-scripts voor assembly-registratie te beheren.
- Class Library (voor de U-SQL-toepassing) voor het beheren van de C#-bron code en afhankelijkheden voor door de gebruiker gedefinieerde Opera Tors, functies en aggregators (Udo's, UDFs en UDAGs).
- U-SQL database project om te verwijzen naar het klassen bibliotheek project.

Een U-SQL database project kan verwijzen naar een klassen bibliotheek (voor een U-SQL-toepassing). U kunt assembly's die zijn geregistreerd in de U-SQL database maken met behulp van C#-bron code waarnaar wordt verwezen in deze klassen bibliotheek (voor U-SQL-toepassing).

Volg deze stappen om projecten te maken en verwijzingen toe te voegen.

1. Maak een klassen bibliotheek (voor U-SQL-toepassing) door **bestand**  >  **Nieuw**  >  **project** te selecteren. Het project bevindt zich onder het **Azure Data Lake > U-SQL-** knoop punt.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio: C#-klassen bibliotheek project maken](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. Voeg uw door de gebruiker gedefinieerde C#-code toe aan het-project van de klassen bibliotheek (voor U-SQL-toepassing).

1. Een U-SQL-project maken door **bestand**  >  **Nieuw**  >  **project** te selecteren. Het project bevindt zich onder het knoop punt **Azure data Lake**  >  **U-SQL** .

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--een U-SQL database-project maken](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. Voeg een verwijzing toe naar het C#-klassen bibliotheek project voor het U-SQL database-project.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio-referentie toevoegen](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Data Lake-Hulpprogram Ma's voor Visual Studio-een U-SQL database project referentie toevoegen](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. Maak een assembly-script in het U-SQL database project door met de rechter muisknop op het project te klikken en **Nieuw item toevoegen** te selecteren.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--assembly-script toevoegen](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Open het assembly-script in de ontwerp weergave van de assembly. Selecteer de assembly waarnaar wordt verwezen in de vervolg keuzelijst **Assembly maken van verwijzing naar** .

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--assembly maken op basis van verwijzing](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. Voeg **beheerde afhankelijkheden** en **extra bestanden** toe, indien aanwezig. Wanneer u extra bestanden toevoegt, gebruikt het hulp programma het relatieve pad om ervoor te zorgen dat de assembly's op uw lokale machine en op de build-machine later kunnen worden gevonden.

**\@ _DeployTempDirectory** in het editor venster onderaan is een vooraf gedefinieerde variabele die het hulp programma naar de uitvoermap build verwijst. In de map build-uitvoer heeft elke assembly een submap met de naam van de assembly. Alle Dll's en extra bestanden bevinden zich in die submap.

## <a name="build-a-u-sql-database-project"></a>Een U-SQL database-project bouwen

Het build-uitvoer voor een U-SQL database project is een U-SQL database implementatie pakket. Deze heet met het achtervoegsel `.usqldbpack` . Het `.usqldbpack` pakket is een zip-bestand dat alle DDL-instructies bevat in één U-SQL-script in de DDL-map. Alle ingebouwde DLL-bestanden en aanvullende bestanden voor assembly's bevinden zich in de map Temp.

## <a name="deploy-a-u-sql-database"></a>Een U-SQL database implementeren

Het `.usqldbpack` pakket kan worden geïmplementeerd naar een lokaal account of een Azure data Lake Analytics-account. Gebruik Visual Studio of de implementatie-SDK.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Een U-SQL database implementeren in Visual Studio

U kunt een U-SQL database implementeren met behulp van een U-SQL database-project of een- `.usqldbpack` pakket in Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Implementeren met behulp van een U-SQL database project

1. Klik met de rechter muisknop op het U-SQL database project en selecteer vervolgens **implementeren**.

1. Selecteer in de wizard voor het **implementeren van u-SQL database** het **ADLA-account** waarnaar u de Data Base wilt implementeren. Zowel lokale accounts als ADLA-accounts worden ondersteund.

1. De **database bron** wordt automatisch ingevuld. Deze verwijst naar het. usqldbpack-pakket in de map build uitvoermap van het project.

1. Voer een naam in de naam van de **Data Base** in om een Data Base te maken. Als er al een Data Base met dezelfde naam bestaat in het doel Azure Data Lake Analytics account, worden alle objecten die in het database project zijn gedefinieerd, gemaakt zonder de data base opnieuw te maken.

1. Selecteer **verzenden** om de U-SQL database te implementeren. Alle resources, zoals assembly's en aanvullende bestanden, worden geüpload. Er wordt een U-SQL-taak met alle DDL-instructies verzonden.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--Implementeer U-SQL database project](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake-Hulpprogram Ma's voor Visual Studio--de wizard voor het implementeren van U-SQL database-project](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Een U-SQL database implementeren in azure DevOps

`PackageDeploymentTool.exe` biedt de programmeer-en opdracht regel interfaces die U helpen bij het implementeren van U-SQL-data bases. De SDK is opgenomen in het [U-SQL SDK Nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), dat zich bevindt in `build/runtime/PackageDeploymentTool.exe` .

In azure DevOps kunt u een opdracht regel taak en deze SDK gebruiken om een Automation-pijp lijn in te stellen voor de U-SQL database vernieuwen. Meer [informatie over de SDK en het instellen van een CI/cd-pijp lijn voor U-SQL database-implementatie](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Volgende stappen

- [Een CI/CD-pijp lijn voor Azure Data Lake Analytics instellen](data-lake-analytics-cicd-overview.md)
- [Uw Azure Data Lake Analytics-code testen](data-lake-analytics-cicd-test.md)
- [U-SQL-script uitvoeren op uw lokale computer](data-lake-analytics-data-lake-tools-local-run.md)
