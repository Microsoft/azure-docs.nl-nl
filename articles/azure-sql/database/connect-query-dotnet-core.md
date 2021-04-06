---
title: .NET Core gebruiken om verbinding te maken en een query uit te voeren op een database
description: Dit onderwerp laat zien hoe u .NET Core kunt gebruiken om een programma te maken dat is verbonden met een database in Azure SQL Database of Azure SQL Managed Instance,en hoe u dit kunt gebruiken om een query uit te voeren met T-SQL-instructies.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705208"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Quickstart: .NET Core (C#) gebruiken om een query uit te voeren op een database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

In deze quickstart gebruikt u [.NET Core](https://www.microsoft.com/net/) en C#-code om verbinding te maken met een database. Vervolgens moet u een Transact-SQL-instructie uitvoeren om een query op gegevens uit te voeren.

> [!TIP]
> In de volgende Microsoft-leermodule leert u gratis [Een ASP.NET-toepassing ontwikkelen en configureren die een query uitvoert op een Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [.NET Core voor uw besturingssysteem](https://www.microsoft.com/net/core) moet zijn geïnstalleerd.
- Een database waarin u een query kunt uitvoeren. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Een nieuw .NET Core-project maken

1. Open een opdrachtprompt en maak een map met de naam **sqltest**. Navigeer naar deze map en voer deze opdracht uit.

    ```cmd
    dotnet new console
    ```

    Met deze opdracht maakt u nieuwe app-projectbestanden, waaronder een eerste C#-codebestand (**Program.cs**), een XML-configuratiebestand (**sqltest.csproj**) en de benodigde binaire bestanden.

2. Open **sqltest.csproj** in een teksteditor en plak de volgende XML-code tussen de `<Project>`-tags. Met deze XML-code voegt u `System.Data.SqlClient` toe als een afhankelijkheid.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Code invoegen om query's uit te voeren op de database in Azure SQL Database

1. Open **Program.cs** in een teksteditor.

2. Vervang de inhoud door de volgende code en voeg de juiste waarden toe voor de server, de database, de gebruikersnaam en het wachtwoord.

> [!NOTE]
> Als u een ADO.NET-verbindingsreeks wilt gebruiken, vervangt u de vier regels in de code waarmee de server, de database, de gebruikersnaam en het wachtwoord worden ingesteld door de onderstaande regel. Stel in de verbindingsreeks uw gebruikersnaam en wachtwoord in.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>De code uitvoeren

1. Voer in de prompt de volgende opdrachten uit.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Controleer of de rijen zijn geretourneerd.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Kies **Enter** om het toepassingsvenster te sluiten.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag met .NET Core in Windows/Linux/macOS met behulp van de opdrachtregel ](/dotnet/core/tutorials/using-with-xplat-cli).
- Meer informatie over [verbinding maken met en een query uitvoeren in Azure SQL Database of Azure SQL Managed Instance, met behulp van .NET Framework en Visual Studio](connect-query-dotnet-visual-studio.md).  
- Meer informatie over [Uw eerste database ontwerpen met behulp van SSMS](design-first-database-tutorial.md) of [Een database ontwerpen en hiermee verbinding maken met behulp van C# en ADO.NET](design-first-database-csharp-tutorial.md).
- Raadpleeg de [.NET-documentatie](/dotnet/) voor meer informatie over .NET.
