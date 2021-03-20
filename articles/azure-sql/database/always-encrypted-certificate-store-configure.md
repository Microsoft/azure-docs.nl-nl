---
title: Always Encrypted configureren met behulp van het Windows-certificaat archief
description: In dit artikel wordt beschreven hoe u met behulp van de wizard Always Encrypted in SQL Server Management Studio (SSMS) gevoelige gegevens in Azure SQL Database kunt beveiligen met database versleuteling. U ziet ook hoe u de versleutelings sleutels opslaat in het Windows-certificaat archief.
keywords: versleutelen van gegevens, SQL-versleuteling, database versleuteling, gevoelige gegevens Always Encrypted
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 04/23/2020
ms.openlocfilehash: 60dea826a12ea475806adb6db88faa88e26463a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674831"
---
# <a name="configure-always-encrypted-by-using-the-windows-certificate-store"></a>Always Encrypted configureren met behulp van het Windows-certificaat archief

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel wordt beschreven hoe u met behulp van de [wizard always encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard) in [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms)gevoelige gegevens in Azure SQL database of Azure SQL Managed Instance kunt beveiligen met database versleuteling. U ziet ook hoe u de versleutelings sleutels opslaat in het Windows-certificaat archief.

Always Encrypted is een technologie voor gegevens versleuteling waarmee gevoelige gegevens in rust op de server worden beschermd tijdens het verkeer tussen de client en de server, terwijl de gegevens in gebruik zijn, zodat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Nadat u gegevens hebt versleuteld, hebben alleen client toepassingen of app-servers die toegang hebben tot de sleutels toegang tot tekst zonder opmaak. Zie [Always encrypted (data base-engine)](/sql/relational-databases/security/encryption/always-encrypted-database-engine)voor meer informatie.

Nadat u de Data Base hebt geconfigureerd voor het gebruik van Always Encrypted, maakt u met Visual Studio een client toepassing in C# om met de versleutelde gegevens te werken.

Volg de stappen in dit artikel voor meer informatie over het instellen van Always Encrypted voor SQL Database of SQL Managed instance. In dit artikel wordt beschreven hoe u de volgende taken uitvoert:

* Gebruik de wizard Always Encrypted in SSMS om [Always encrypted sleutels](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3)te maken.
  * Maak een [hoofd sleutel voor een kolom (CMK)](/sql/t-sql/statements/create-column-master-key-transact-sql).
  * Maak een [kolom versleutelings sleutel (CEK)](/sql/t-sql/statements/create-column-encryption-key-transact-sql).
* Een database tabel maken en kolommen versleutelen.
* Een toepassing maken waarmee gegevens uit de versleutelde kolommen worden ingevoegd, geselecteerd en weer gegeven.

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie hebt u het volgende nodig:

* Een Azure-account en -abonnement. Als u er nog geen hebt, kunt u zich aanmelden voor een [gratis proef versie](https://azure.microsoft.com/pricing/free-trial/).
- Een data base in [Azure SQL database](single-database-create-quickstart.md) of [Azure SQL Managed instance](../managed-instance/instance-create-quickstart.md).
* [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) versie 13.0.700.242 of hoger.
* [.NET Framework 4,6](/dotnet/framework/) of hoger (op de client computer).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="enable-client-application-access"></a>Toegang tot client toepassingen inschakelen

U moet uw client toepassing inschakelen voor toegang tot SQL Database of SQL Managed instance door een Azure Active Directory-toepassing (AAD) in te stellen en de *toepassings-id en-* *sleutel* te kopiëren die u nodig hebt om uw toepassing te verifiëren.

Als u de *toepassings-id en-* *sleutel* wilt ophalen, volgt u de stappen in [Create a Azure Active Directory-toepassing en service-principal die toegang heeft tot resources](../../active-directory/develop/howto-create-service-principal-portal.md).



## <a name="connect-with-ssms"></a>Verbinden met SSMS

Open SQL Server Management Studio (SSMS) en maak verbinding met de server of worden beheerd met uw data base.

1. Open SQL Server Management Studio. (Klik op **verbinden**  >  **Data base-engine** om het venster **verbinding maken met server** te openen als het niet is geopend).
2. Voer uw server naam en referenties in.

    ![De verbindingsreeks kopiëren](./media/always-encrypted-certificate-store-configure/ssms-connect.png)

Als het venster **nieuwe firewall regel** wordt geopend, meldt u zich aan bij Azure en laat SSMS een nieuwe firewall regel voor u maken.

## <a name="create-a-table"></a>Een tabel maken

In deze sectie maakt u een tabel om patiënten-gegevens op te slaan. Dit is in eerste instantie een normale tabel--u configureert de versleuteling in de volgende sectie.

1. Vouw **data bases** uit.
2. Klik met de rechter muisknop op de **Clinic** -data base en klik op **nieuwe query**.
3. Plak de volgende Transact-SQL (T-SQL) in het nieuwe query venster en **Voer** dit uit.
    
    ```tsql
    CREATE TABLE [dbo].[Patients](
    [PatientId] [int] IDENTITY(1,1),
    [SSN] [char](11) NOT NULL,
    [FirstName] [nvarchar](50) NULL,
    [LastName] [nvarchar](50) NULL,
    [MiddleName] [nvarchar](50) NULL,
    [StreetAddress] [nvarchar](50) NULL,
    [City] [nvarchar](50) NULL,
    [ZipCode] [char](5) NULL,
    [State] [char](2) NULL,
    [BirthDate] [date] NOT NULL
    PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
    GO
    ```

## <a name="encrypt-columns-configure-always-encrypted"></a>Kolommen versleutelen (Always Encrypted configureren)

SSMS biedt een wizard waarmee u Always Encrypted eenvoudig kunt configureren door de kolommen CMK, CEK en encrypted voor u in te stellen.

1. Vouw **data bases**  >  **Clinic**-  >  **tabellen** uit.
2. Klik met de rechter muisknop op de tabel **patiënten** en selecteer **kolommen versleutelen** om de wizard always encrypted te openen:

    ![Scherm opname van de versleutelings Colunns... menu optie in de tabel patiënten.](./media/always-encrypted-certificate-store-configure/encrypt-columns.png)

De wizard Always Encrypted bevat de volgende secties: **kolom selectie**, **configuratie van de hoofd sleutel** (CMK), **validatie** en **samen vatting**.

### <a name="column-selection"></a>Kolom selectie

Klik op **volgende** op de pagina **Inleiding** om de pagina **kolom selectie** te openen. Op deze pagina selecteert u de kolommen die u wilt versleutelen, [het type versleuteling en welke kolom versleutelings sleutel (CEK)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) u wilt gebruiken.

Gegevens van **SSN** en **geboorte datum** versleutelen voor elke patiënt. De kolom **SSN** maakt gebruik van deterministische versleuteling, die ondersteuning biedt voor treffers, samen voegingen en Group by. In de kolom **geboorte datum** wordt wille keurige versleuteling gebruikt, die geen ondersteuning biedt voor bewerkingen.

Stel het **versleutelings type** voor de kolom **SSN** in op **deterministisch** en de kolom **geboorte datum** in **wille keurige volg orde**. Klik op **Volgende**.

![Kolommen versleutelen](./media/always-encrypted-certificate-store-configure/column-selection.png)

### <a name="master-key-configuration"></a>Configuratie van hoofd sleutel

Op de pagina **hoofd sleutel configuratie** kunt u uw CMK instellen en de sleutel archief provider selecteren waar de CMK worden opgeslagen. Op dit moment kunt u een CMK opslaan in het Windows-certificaat archief, Azure Key Vault of een Hardware Security module (HSM). Deze zelf studie laat zien hoe u uw sleutels opslaat in het Windows-certificaat archief.

Controleer of **Windows-certificaat archief** is geselecteerd en klik op **volgende**.

![Configuratie van hoofd sleutel](./media/always-encrypted-certificate-store-configure/master-key-configuration.png)

### <a name="validation"></a>Validatie

U kunt de kolommen nu versleutelen of een Power shell-script opslaan om het later uit te voeren. Voor deze zelf studie selecteert u **nu door gaan naar volt ooien** en klikt u op **volgende**.

### <a name="summary"></a>Samenvatting

Controleer of de instellingen juist zijn en klik op **volt ooien** om de installatie voor always encrypted te volt ooien.

![Scherm afbeelding toont de resultaten pagina met taken die als door gegeven zijn gemarkeerd.](./media/always-encrypted-certificate-store-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>De acties van de wizard controleren

Nadat de wizard is voltooid, is de data base ingesteld op Always Encrypted. De wizard heeft de volgende acties uitgevoerd:

* Er is een CMK gemaakt.
* Er is een CEK gemaakt.
* De geselecteerde kolommen voor versleuteling zijn geconfigureerd. De tabel **patiënten** bevat momenteel geen gegevens, maar alle bestaande gegevens in de geselecteerde kolommen zijn nu versleuteld.

U kunt het maken van de sleutels in SSMS controleren door naar de **Clinic**  >  **Security**  >  **Always encrypted Keys** te gaan. U kunt nu de nieuwe sleutels zien die de wizard voor u heeft gegenereerd.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Een client toepassing maken die werkt met de versleutelde gegevens

Nu Always Encrypted is ingesteld, kunt u een toepassing bouwen waarmee de versleutelde kolommen worden *ingevoegd* en *geselecteerd* . Als u de voorbeeld toepassing wilt uitvoeren, moet u deze uitvoeren op dezelfde computer als waarop u de Always Encrypted wizard hebt uitgevoerd. Als u de toepassing wilt uitvoeren op een andere computer, moet u uw Always Encrypted-certificaten implementeren op de computer waarop de client-app wordt uitgevoerd.  

> [!IMPORTANT]
> Uw toepassing moet [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) -objecten gebruiken bij het door geven van ongecodeerde gegevens naar de server met Always encrypted-kolommen. Het door geven van letterlijke waarden zonder gebruik te maken van SqlParameter-objecten leidt tot een uitzonde ring.

1. Open Visual Studio en maak een nieuwe C#-console toepassing. Zorg ervoor dat uw project is ingesteld op **.NET Framework 4,6** of hoger.
2. Geef het project de naam **AlwaysEncryptedConsoleApp** en klik op **OK**.

![Scherm afbeelding met het nieuwe benoemde AlwaysEncryptedConsoleApp-project.](./media/always-encrypted-certificate-store-configure/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Wijzig uw connection string om Always Encrypted in te scha kelen

In deze sectie wordt uitgelegd hoe u Always Encrypted in uw data base inschakelt connection string. U wijzigt de console-app die u zojuist hebt gemaakt in de volgende sectie, ' Always Encrypted voorbeeld console toepassing '.

Als u Always Encrypted wilt inschakelen, moet u het tref woord voor de **kolom versleutelings instelling** toevoegen aan de Connection String en instellen op **ingeschakeld**.

U kunt dit rechtstreeks in het connection string instellen of u kunt het instellen met behulp van een [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder). De voorbeeld toepassing in de volgende sectie laat zien hoe u **SqlConnectionStringBuilder** kunt gebruiken.

> [!NOTE]
> Dit is de enige wijziging vereist in een client toepassing die specifiek is voor Always Encrypted. Als u een bestaande toepassing hebt die de connection string extern opslaat (dat wil zeggen, in een configuratie bestand), kunt u Always Encrypted mogelijk maken zonder dat u code hoeft te wijzigen.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Always Encrypted in het connection string inschakelen

Voeg het volgende tref woord toe aan uw connection string:

`Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Always Encrypted met een SqlConnectionStringBuilder inschakelen

De volgende code laat zien hoe u Always Encrypted inschakelt door de [SqlConnectionStringBuilder. ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) in te stellen op [ingeschakeld](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder =
    new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting =
    SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="always-encrypted-sample-console-application"></a>Voorbeeld console toepassing Always Encrypted

Dit voor beeld laat zien hoe u:

* Wijzig uw connection string om Always Encrypted in te scha kelen.
* Gegevens invoegen in de versleutelde kolommen.
* Selecteer een record door te filteren op een specifieke waarde in een versleutelde kolom.

Vervang de inhoud van **programma. cs** door de volgende code. Vervang de connection string voor de globale Connections Tring-variabele in de regel direct boven de methode Main door met uw geldige connection string van de Azure Portal. Dit is de enige wijziging die u moet aanbrengen in deze code.

Voer de app uit om Always Encrypted in actie te zien.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();

            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Controleren of de gegevens zijn versleuteld

U kunt snel controleren of de daad werkelijke gegevens op de server zijn versleuteld door de gegevens van de **patiënten** te DOORZOEKEN met SSMS. (Gebruik uw huidige verbinding waarbij de kolom versleutelings instelling nog niet is ingeschakeld.)

Voer de volgende query uit op de Clinic-data base.

```tsql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

U kunt zien dat de versleutelde kolommen geen lees bare gegevens bevatten.

   ![Scherm opname met de versleutelde gegevens in de versleutelde kolommen.](./media/always-encrypted-certificate-store-configure/ssms-encrypted.png)

Als u SSMS wilt gebruiken om toegang te krijgen tot de Lees bare gegevens, kunt u de para meter voor **kolom versleutelings instelling = ingeschakeld** toevoegen aan de verbinding.

1. Klik in SSMS met de rechter muisknop op uw server in **objectverkenner** en klik vervolgens op **verbinding verbreken**.
2. Klik op   >  **Data base-engine** verbinden om het venster **verbinding maken met server** te openen en klik vervolgens op **Opties**.
3. Klik op **extra verbindings parameters** en type **kolom versleutelings instelling = ingeschakeld**.

    ![Scherm opname van het tabblad Extra verbindings parameters met kolom versleutelings instelling = ingeschakeld in het vak.](./media/always-encrypted-certificate-store-configure/ssms-connection-parameter.png)
4. Voer de volgende query uit op de **Clinic** -data base.

    ```tsql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

     U kunt nu de Lees bare gegevens in de versleutelde kolommen weer geven.

    ![Nieuwe console toepassing](./media/always-encrypted-certificate-store-configure/ssms-plaintext.png)

> [!NOTE]
> Als u verbinding maakt met SSMS (of een wille keurige client) vanaf een andere computer, heeft deze geen toegang tot de versleutelings sleutels en kan de gegevens niet worden ontsleuteld.

## <a name="next-steps"></a>Volgende stappen

Nadat u een Data Base hebt gemaakt die gebruikmaakt van Always Encrypted, kunt u het volgende doen:

* Voer dit voor beeld uit vanaf een andere computer. Het heeft geen toegang tot de versleutelings sleutels, dus heeft geen toegang tot de niet-gecodeerde gegevens en kan niet worden uitgevoerd.
* [Uw sleutels draaien en opschonen](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio).
* [Migreer gegevens die al zijn versleuteld met Always encrypted](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).
* [Implementeer always encrypted certificaten op andere client computers](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted#Anchor_1) (Zie de sectie ' certificaten beschikbaar maken voor toepassingen en gebruikers ').

## <a name="related-information"></a>Gerelateerde informatie

* [Always Encrypted (client ontwikkeling)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
* [Transparante gegevensversleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Versleuteling SQL Server](/sql/relational-databases/security/encryption/sql-server-encryption)
* [Wizard Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard)
* [Always Encrypted blog](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)