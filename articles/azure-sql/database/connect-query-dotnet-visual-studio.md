---
title: Visual Studio gebruiken met .NET en C# om query's uitvoeren
description: Gebruik Visual Studio om een C#-app te maken die verbinding maakt met een database in Azure SQL Database of Azure SQL Managed Instance en query's uitvoert.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1fe39e0ff9acc0c092199ab2dd199cf396f67d01
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874761"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Quickstart: Gebruik .NET en C# in Visual Studio om verbinding te maken met een database en er query's op uit te voeren
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

In deze quickstart ziet u hoe u het [.NET Framework](https://dotnet.microsoft.com) en C#-code in Visual Studio gebruikt om query's uit te voeren op een database in Azure SQL of Synapse SQL met Transact-SQL-instructies.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community-, Professional- of Enterprise-editie.
- Een database waarin u een query kunt uitvoeren.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Code maken om query's uit te voeren op de database in Azure SQL Database

1. Maak een nieuw project in Visual Studio. 
   
1. Selecteer in het dialoogvenster **Nieuw project** **Visual C#** en vervolgens **Consoletoepassing (.NET Framework)** .
   
1. Voer *sqltest* in voor de projectnaam en selecteer vervolgens **OK**. Het nieuwe project wordt gemaakt. 
   
1. Selecteer **Project** > **NuGet-pakketten beheren**. 
   
1. Selecteer in **NuGet Package Manager** het tabblad **Bladeren** en zoek en selecteer **Microsoft.Data.SqlClient**.
   
1. Selecteer op de pagina **Microsoft.Data.SqlClient** de optie **Installeren**. 
   - Selecteer **OK** om door te gaan met de installatie. 
   - Als een venster voor **akkoord gaan met de licentie** wordt weergegeven, selecteert u **Ik ga akkoord**.
   
1. Wanneer de installatie is voltooid, kunt u **NuGet Package Manager** sluiten. 
   
1. Vervang in de code-editor de inhoud **Program.cs** door de volgende code. Vervang `<your_server>`, `<your_username>`, `<your_password>` en `<your_database>` door uw eigen waarden.
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>De code uitvoeren

1. Om de app uit te voeren, selecteert u **Fouten opsporen** > **Foutopsporing starten** of selecteert u **Start** op de werkbalk of drukt u op **F5**.
1. Controleer of de databasenamen en sorteringen zijn geretourneerd. Sluit vervolgens het app-venster.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [verbinding maken met en een query uitvoeren op een database in Azure SQL Database met behulp van .NET Core](connect-query-dotnet-core.md) in Windows/Linux/macOS.  
- Meer informatie over [Aan de slag met .NET Core in Windows/Linux/macOS met behulp van de opdrachtregel](/dotnet/core/tutorials/using-with-xplat-cli).
- Meer informatie over [Uw eerste database in Azure SQL Database ontwerpen met behulp van SSMS](design-first-database-tutorial.md) of [Uw eerste database in Azure SQL Database ontwerpen met behulp van .NET](design-first-database-csharp-tutorial.md).
- Raadpleeg de [.NET-documentatie](/dotnet/) voor meer informatie over .NET.
- Voorbeeld van pogingslogica: [Flexibel verbinding maken met Azure SQL via ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
