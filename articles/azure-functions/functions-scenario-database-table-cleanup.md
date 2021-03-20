---
title: Azure Functions gebruiken om een taak voor het opschonen van een Data Base uit te voeren
description: Gebruik Azure Functions om een taak te plannen die verbinding maakt met Azure SQL Database om periodiek rijen op te schonen.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/02/2019
ms.openlocfilehash: 0b5e255d7d108eb063ece4e5489a8762261a0bed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88207257"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Functions gebruiken om verbinding te maken met een Azure SQL Database

Dit artikel laat u zien hoe u Azure Functions kunt gebruiken om een geplande taak te maken die verbinding maakt met een Azure SQL Database of een Azure SQL Managed instance. Met de functie code worden rijen in een tabel in de data base opgeschoond. De nieuwe C#-functie wordt gemaakt op basis van een vooraf gedefinieerde Timer trigger sjabloon in Visual Studio 2019. Als u dit scenario wilt ondersteunen, moet u ook een Data Base connection string instellen als een app-instelling in de functie-app. Voor Azure SQL Managed instance moet u een [openbaar eind punt inschakelen](../azure-sql/managed-instance/public-endpoint-configure.md) om verbinding te kunnen maken vanaf Azure functions. In dit scenario wordt een bulk bewerking voor de data base gebruikt. 

Als dit uw eerste ervaring voor het werken met C#-functies is, moet u de [Azure functions c# Naslag informatie voor ontwikkel aars](functions-dotnet-class-library.md)lezen.

## <a name="prerequisites"></a>Vereisten

+ Volg de stappen in het artikel [uw eerste functie maken met Visual Studio](functions-create-your-first-function-visual-studio.md) om een lokale functie-app te maken die is gericht op versie 2. x of een latere versie van de runtime. U moet uw project ook hebben gepubliceerd naar een functie-app in Azure.

+ In dit artikel wordt een Transact-SQL-opdracht beschreven waarmee een bulksgewijze opschoning bewerking wordt uitgevoerd in de tabel **SalesOrderHeader** in de voorbeeld database AdventureWorksLT. Als u de voorbeeld database AdventureWorksLT wilt maken, voert u de stappen in het artikel [een Data Base maken in Azure SQL database met behulp van de Azure Portal](../azure-sql/database/single-database-create-quickstart.md).

+ U moet een [firewall regel op server niveau](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) toevoegen voor het open bare IP-adres van de computer die u voor deze Quick Start gebruikt. Deze regel is vereist om toegang te krijgen tot het SQL Database exemplaar vanaf uw lokale computer.  

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

U moet de connection string ophalen voor de data base die u hebt gemaakt tijdens het [maken van een data base in Azure SQL database met behulp van de Azure Portal](../azure-sql/database/single-database-create-quickstart.md).

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **SQL-data bases** in het menu aan de linkerkant en selecteer uw Data Base op de pagina **SQL-data bases** .

1. Selecteer **verbindings reeksen** onder **instellingen** en kopieer de volledige **ADO.net** -Connection String. Voor Azure SQL Managed instance Copy connection string voor een openbaar eind punt.

    ![Kopieer de verbindingsreeks voor ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>De verbindingsreeks instellen

Een functie-app fungeert als host voor de uitvoering van uw functies in Azure. Als aanbevolen beveiligings procedure slaat u verbindings reeksen en andere geheimen op in de instellingen van uw functie-app. Het gebruik van toepassings instellingen voor komt onbedoelde openbaar making van de connection string met uw code. U hebt rechtstreeks vanuit Visual Studio toegang tot de app-instellingen voor uw functie-app.

U moet uw app eerder hebben gepubliceerd naar Azure. Als u dit nog niet hebt gedaan, [kunt u de functie-app publiceren in azure](functions-develop-vs.md#publish-to-azure).

1. Klik in Solution Explorer met de rechter muisknop op het functie-app -project en kies  >  **bewerkings Azure app service instellingen** publiceren. Selecteer **instelling toevoegen** in **naam van nieuwe app-instelling**, type `sqldb_connection` en selecteer **OK**.

    ![Toepassings instellingen voor de functie-app.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Plak in de instelling nieuwe **sqldb_connection** de Connection String die u in de vorige sectie hebt gekopieerd naar het **lokale** veld en vervang `{your_username}` en `{your_password}` tijdelijke aanduidingen door echte waarden. Selecteer **waarde invoegen van lokaal** om de bijgewerkte waarde te kopiëren naar het **externe** veld, en selecteer vervolgens **OK**.

    ![SQL connection string-instelling toevoegen.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    De verbindings reeksen worden in azure (**extern**) opgeslagen. Om lekkage van geheimen te voor komen, moet de local.settings.jsop het project bestand (**lokaal**) worden uitgesloten van broncode beheer, zoals met behulp van een. gitignore-bestand.

## <a name="add-the-sqlclient-package-to-the-project"></a>Het SqlClient-pakket toevoegen aan het project

U moet het NuGet-pakket met de SqlClient-bibliotheek toevoegen. Deze Data Access-bibliotheek is vereist om verbinding te maken met SQL Database.

1. Open uw lokale functie-app-project in Visual Studio 2019.

1. Klik in Solution Explorer met de rechter muisknop op het project functie-app en kies **NuGet-pakketten beheren**.

1. Zoek op het tabblad **Bladeren** naar ```System.Data.SqlClient``` en selecteer deze.

1. Selecteer op de pagina **System. data. SqlClient** de optie versie `4.5.1` en klik vervolgens op **installeren**.

1. Wanneer de installatie is voltooid, controleert u de wijzigingen en klikt u vervolgens op **OK** om het venster **Preview** te sluiten.

1. Als een venster voor **akkoord gaan met de licentie** wordt weergegeven, klikt u op **Ik ga akkoord**.

U kunt nu de C#-functie code toevoegen die verbinding maakt met uw SQL Database.

## <a name="add-a-timer-triggered-function"></a>Een door een timer geactiveerde functie toevoegen

1. Klik in Solution Explorer met de rechter muisknop op het project functie- app en kies  >  **nieuwe Azure-functie** toevoegen.

1. Als u de **Azure functions** sjabloon hebt geselecteerd, kunt u een naam opgeven voor het nieuwe item `DatabaseCleanup.cs` en vervolgens **toevoegen** selecteren.

1. Kies in het dialoog venster **nieuwe Azure** -functie **Timer trigger** en klik vervolgens op **OK**. In dit dialoog venster wordt een code bestand gemaakt voor de functie Timer geactiveerd.

1. Open het nieuwe code bestand en voeg de volgende instructies toe aan het begin van het bestand:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Vervang de bestaande `Run` functie door de volgende code:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Deze functie wordt elke 15 seconden uitgevoerd om de `Status` kolom bij te werken op basis van de verzend datum. Zie [Timer trigger voor Azure functions voor](functions-bindings-timer.md)meer informatie over de timer trigger.

1. Druk op **F5** om de functie-app te starten. Het venster voor het uitvoeren van [Azure functions core tools](functions-develop-local.md) wordt geopend achter Visual Studio.

1. Na het starten van vijf tien seconden wordt de functie uitgevoerd. Bekijk de uitvoer en noteer het aantal rijen dat in de tabel **SalesOrderHeader** is bijgewerkt.

    ![De functie Logboeken weer geven.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Bij de eerste uitvoering moet u 32 rijen met gegevens bijwerken. Met de volgende opdracht worden Update gegevens rijen uitgevoerd, tenzij u wijzigingen aanbrengt in de tabel gegevens van de SalesOrderHeader zodat er meer rijen worden geselecteerd door de `UPDATE` instructie.

Als u van plan bent om [deze functie te publiceren](functions-develop-vs.md#publish-to-azure), moet u het `TimerTrigger` kenmerk wijzigen in een redelijk [cron-schema](functions-bindings-timer.md#ncrontab-expressions) dan elke 15 seconden.

## <a name="next-steps"></a>Volgende stappen

Vervolgens leert u hoe u kunt gebruiken. Werkt met Logic Apps om te integreren met andere services.

> [!div class="nextstepaction"]
> [Een functie maken die kan worden geïntegreerd met Logic Apps](functions-twitter-email.md)

Raadpleeg de volgende artikelen voor meer informatie over functies:

+ [Naslaginformatie over Azure Functions voor ontwikkelaars](functions-reference.md)  
  Naslaginformatie voor programmeurs over het coderen van functies en het definiëren van triggers en bindingen.
+ [Azure Functions testen](functions-test-a-function.md)  
  Beschrijft de verschillende hulpprogramma’s en technieken voor het testen van uw functies.  
