---
title: SSIS-pakketten uitvoeren met Azure SQL Managed instance agent
description: Meer informatie over het uitvoeren van SSIS-pakketten met behulp van de Azure SQL Managed instance agent.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 5f8b18375c517ab5b620b2d6d897f5133b14705d
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556457"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>SSIS-pakketten uitvoeren met behulp van de Azure SQL Managed instance agent

In dit artikel wordt beschreven hoe u een SQL Server Integration Services (SSIS)-pakket uitvoert met behulp van de Azure SQL Managed instance agent. Deze functie biedt problemen die vergelijkbaar zijn met het plannen van SSIS-pakketten door gebruik te maken van SQL Server Agent in uw on-premises omgeving.

Met deze functie kunt u SSIS-pakketten uitvoeren die zijn opgeslagen in SSISDB in een SQL Managed instance, een bestands systeem zoals Azure Files, of een Azure-SSIS Integration runtime-pakket archief.

## <a name="prerequisites"></a>Vereisten

Als u deze functie wilt gebruiken, [downloadt](/sql/ssms/download-sql-server-management-studio-ssms) en installeert u de nieuwste versie van SQL Server Management Studio (SSMS). Details van versie ondersteuning, zoals hieronder:

- Als u pakketten wilt uitvoeren in SSISDB of bestands systeem, installeert u SSMS-versie 18,5 of hoger.
- Als u pakketten wilt uitvoeren in de pakket opslag, installeert u SSMS-versie 18,6 of hoger.

U moet ook [een Azure SSIS Integration runtime inrichten](./tutorial-deploy-ssis-packages-azure.md) in azure Data Factory. Er wordt een SQL-beheerd exemplaar als een eindpunt server gebruikt.

## <a name="run-an-ssis-package-in-ssisdb"></a>Een SSIS-pakket uitvoeren in SSISDB

In deze procedure gebruikt u SQL Managed instance agent voor het aanroepen van een SSIS-pakket dat is opgeslagen in SSISDB.

1. In de meest recente versie van SSMS maakt u verbinding met een SQL-beheerd exemplaar.
1. Maak een nieuwe agent taak en een nieuwe taak stap. Klik onder **SQL Server Agent** met de rechter muisknop op de map **Jobs** en selecteer vervolgens **nieuwe taak**.

   ![Selecties voor het maken van een nieuwe agent taak](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Selecteer op de pagina **nieuwe taak stap** **SQL Server Integration services pakket** als type.

   ![Selecties voor het maken van een nieuwe SSIS-taak stap](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Op het tabblad **pakket** selecteert u **SSIS Catalog** als de pakket locatie.
1. Omdat SSISDB zich in een SQL Managed instance bevindt, hoeft u geen verificatie op te geven.
1. Geef een SSIS-pakket op van SSISDB.

   ![Tabblad pakket met selecties voor het pakket bron type](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. Op het tabblad **configuratie** kunt u het volgende doen:
  
   - Geef parameter waarden op onder **para meters**.
   - Vervang waarden onder **verbindings beheer**.
   - Overschrijf de eigenschap en kies het logboek registratie niveau onder **Geavanceerd**.

   ![Tabblad Configuratie met selecties voor het pakket bron type](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Selecteer **OK** om de taak configuratie van de agent op te slaan.
1. Start de agent taak om het SSIS-pakket uit te voeren.

## <a name="run-an-ssis-package-in-the-file-system"></a>Een SSIS-pakket uitvoeren in het bestands systeem

In deze procedure gebruikt u SQL Managed instance agent om een SSIS-pakket uit te voeren dat is opgeslagen in het bestands systeem.

1. In de meest recente versie van SSMS maakt u verbinding met een SQL-beheerd exemplaar.
1. Maak een nieuwe agent taak en een nieuwe taak stap. Klik onder **SQL Server Agent** met de rechter muisknop op de map **Jobs** en selecteer vervolgens **nieuwe taak**.

   ![Selecties voor het maken van een nieuwe agent taak](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Selecteer op de pagina **nieuwe taak stap** **SQL Server Integration services pakket** als type.

   ![Selecties voor het maken van een nieuwe SSIS-taak stap](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Op het tabblad **pakket** :

   1. Voor de **pakket locatie** selecteert u **Bestands systeem**.

   1. Voor **Bestands bron type**:

      - Als uw pakket wordt geüpload naar Azure Files, selecteert u **Azure-bestands share**.

        ![Opties voor bestands bron type](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        Het pad naar het pakket is **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** .

        Voer onder **toegangs referenties voor pakket bestanden** de naam van het Azure-bestands account en de account sleutel in voor toegang tot het Azure-bestand. Het domein is ingesteld als **Azure**.

      - Als uw pakket wordt geüpload naar een netwerk share, selecteert u **netwerk share**.

        Het pad naar het pakket is het UNC-pad van het pakket bestand met de extensie. dtsx.

        Geef het domein, de gebruikers naam en het bijbehorende wacht woord op om toegang te krijgen tot het pakket bestand van de netwerk share.
   1. Als uw pakket bestand is versleuteld met een wacht woord, selecteert u **versleutelings wachtwoord** en voert u het wacht woord in.
1. Voer op het tabblad **configuraties** het pad naar het configuratie bestand in als u een configuratie bestand nodig hebt om het SSIS-pakket uit te voeren.
   Als u de configuratie opslaat in Azure Files, wordt het configuratiepad **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. Op het tabblad **uitvoerings opties** kunt u kiezen of u Windows- **verificatie** of **32-bits runtime** wilt gebruiken om het SSIS-pakket uit te voeren.
1. Op het tabblad **logboek registratie** kunt u het pad voor logboek registratie en bijbehorende toegangs referenties voor logboek registratie kiezen om de logboek bestanden op te slaan. 
   Het pad voor logboek registratie is standaard hetzelfde als het pad naar de map van het pakket en de toegangs referenties voor logboek registratie is hetzelfde als de toegangs referentie voor het pakket.
   Als u uw logboeken opslaat in Azure Files, wordt het pad voor logboek registratie **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Op het tabblad **waarden instellen** kunt u het pad en de waarde van de eigenschap opgeven om de pakket eigenschappen te overschrijven.

   Als u bijvoorbeeld de waarde van uw gebruikers variabele wilt overschrijven, voert u het pad in de volgende indeling in: **`\Package.Variables[User::<variable name>].Value`** .
1. Selecteer **OK** om de taak configuratie van de agent op te slaan.
1. Start de agent taak om het SSIS-pakket uit te voeren.

## <a name="run-an-ssis-package-in-the-package-store"></a>Een SSIS-pakket uitvoeren in de pakket opslag

In deze procedure gebruikt u SQL Managed instance agent om een SSIS-pakket uit te voeren dat is opgeslagen in de Azure-SSIS IR-pakket opslag.

1. In de meest recente versie van SSMS maakt u verbinding met een SQL-beheerd exemplaar.
1. Maak een nieuwe agent taak en een nieuwe taak stap. Klik onder **SQL Server Agent** met de rechter muisknop op de map **Jobs** en selecteer vervolgens **nieuwe taak**.

   ![Selecties voor het maken van een nieuwe agent taak](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Selecteer op de pagina **nieuwe taak stap** **SQL Server Integration services pakket** als type.

   ![Selecties voor het maken van een nieuwe SSIS-taak stap](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Op het tabblad **pakket** :

   1. Selecteer bij **pakket locatie** de optie **pakket archief**.

   1. Voor het pad van het **pakket**:

      Het pad naar het pakket is **`<package store name>\<folder name>\<package name>`** .

      ![Opties voor het type pakket archief](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. Als uw pakket bestand is versleuteld met een wacht woord, selecteert u **versleutelings wachtwoord** en voert u het wacht woord in.
1. Voer op het tabblad **configuraties** het pad naar het configuratie bestand in als u een configuratie bestand nodig hebt om het SSIS-pakket uit te voeren.
   Als u de configuratie opslaat in Azure Files, wordt het configuratiepad **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. Op het tabblad **uitvoerings opties** kunt u kiezen of u Windows- **verificatie** of **32-bits runtime** wilt gebruiken om het SSIS-pakket uit te voeren.
1. Op het tabblad **logboek registratie** kunt u het pad voor logboek registratie en bijbehorende toegangs referenties voor logboek registratie kiezen om de logboek bestanden op te slaan.
   Het pad voor logboek registratie is standaard hetzelfde als het pad naar de map van het pakket en de toegangs referenties voor logboek registratie is hetzelfde als de toegangs referentie voor het pakket.
   Als u uw logboeken opslaat in Azure Files, wordt het pad voor logboek registratie **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Op het tabblad **waarden instellen** kunt u het pad en de waarde van de eigenschap opgeven om de pakket eigenschappen te overschrijven.

   Als u bijvoorbeeld de waarde van uw gebruikers variabele wilt overschrijven, voert u het pad in de volgende indeling in: **`\Package.Variables[User::<variable name>].Value`** .
1. Selecteer **OK** om de taak configuratie van de agent op te slaan.
1. Start de agent taak om het SSIS-pakket uit te voeren.

## <a name="cancel-ssis-package-execution"></a>Uitvoering van SSIS-pakket annuleren

Als u de uitvoering van het pakket wilt annuleren vanuit een SQL Managed instance agent-taak, voert u de volgende stappen uit in plaats van de agent taak direct te stoppen:

1. Zoek uw SQL Agent- **jobId** vanuit **msdb.dbo.systaken**.
1. Zoek de overeenkomende SSIS- **executionid is vereist** op basis van de taak-id met behulp van deze query:
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   Als uw SSIS-pakketten zich in SSISDB bevinden, gebruikt u **ssisdb.internal.execution_parameter_values** als tabel voor het uitvoeren van taken. Als uw SSIS-pakketten zich in het bestands systeem bevinden, gebruikt u **ssisdb.internal.execution_parameter_values_noncatalog**.
1. Klik met de rechter muisknop op de catalogus SSISDB en selecteer vervolgens **actieve bewerkingen**.

   ![Actieve bewerkingen in het snelmenu van de SSISDB-catalogus](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Stop de bijbehorende bewerking op basis van **executionid is vereist**.

## <a name="next-steps"></a>Volgende stappen
U kunt SSIS-pakketten ook plannen met behulp van Azure Data Factory. Zie [Azure Data Factory Event trigger](how-to-create-event-trigger.md)voor stapsgewijze instructies.