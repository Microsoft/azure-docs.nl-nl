---
title: Always Encrypted configureren met behulp van Azure Key Vault
description: Deze zelf studie laat zien hoe u met behulp van de wizard Always Encrypted in SQL Server Management Studio gevoelige gegevens in een data base in Azure SQL Database kunt beveiligen met gegevens versleuteling.
keywords: gegevens versleuteling, versleutelings sleutel, Cloud versleuteling
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 11/02/2020
ms.openlocfilehash: 257abf03994c7006b1c3789174f550515dcd309a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94841458"
---
# <a name="configure-always-encrypted-by-using-azure-key-vault"></a>Always Encrypted configureren met behulp van Azure Key Vault 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]

In dit artikel wordt beschreven hoe u gevoelige gegevens in een data base in Azure SQL Database met gegevens versleuteling kunt beveiligen met behulp van de [wizard always encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard) in [SQL Server Management Studio (SSMS)](/sql/ssms/sql-server-management-studio-ssms). Het bevat ook instructies voor het opslaan van elke versleutelings sleutel in Azure Key Vault.

Always Encrypted is een technologie voor gegevens versleuteling waarmee u gevoelige gegevens op de server kunt beschermen tijdens het verkeer tussen de client en de server en terwijl de gegevens in gebruik zijn. Always Encrypted zorgt ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Nadat u gegevens versleuteling hebt geconfigureerd, hebben alleen client toepassingen of app-servers die toegang hebben tot de sleutels toegang tot tekst zonder opmaak. Zie [Always encrypted (data base-engine)](/sql/relational-databases/security/encryption/always-encrypted-database-engine)voor meer informatie.

Nadat u de Data Base hebt geconfigureerd voor het gebruik van Always Encrypted, maakt u met Visual Studio een client toepassing in C# om met de versleutelde gegevens te werken.

Volg de stappen in dit artikel en meer informatie over het instellen van Always Encrypted voor uw data base in Azure SQL Database of SQL Managed instance. In dit artikel wordt beschreven hoe u de volgende taken uitvoert:

- Gebruik de wizard Always Encrypted in SSMS om [Always encrypted sleutels](/sql/relational-databases/security/encryption/always-encrypted-database-engine#Anchor_3)te maken.
  - Maak een [hoofd sleutel voor een kolom (CMK)](/sql/t-sql/statements/create-column-master-key-transact-sql).
  - Maak een [kolom versleutelings sleutel (CEK)](/sql/t-sql/statements/create-column-encryption-key-transact-sql).
- Een database tabel maken en kolommen versleutelen.
- Een toepassing maken waarmee gegevens uit de versleutelde kolommen worden ingevoegd, geselecteerd en weer gegeven.

## <a name="prerequisites"></a>Vereisten


- Een Azure-account en -abonnement. Als u er nog geen hebt, kunt u zich aanmelden voor een [gratis proef versie](https://azure.microsoft.com/pricing/free-trial/).
- Een data base in [Azure SQL database](single-database-create-quickstart.md) of [Azure SQL Managed instance](../managed-instance/instance-create-quickstart.md).
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) versie 13.0.700.242 of hoger.
- [.NET Framework 4,6](/dotnet/framework/) of hoger (op de client computer).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
- [Azure PowerShell](/powershell/azure/) of [Azure cli](/cli/azure/install-azure-cli)

## <a name="enable-client-application-access"></a>Toegang tot client toepassingen inschakelen

U moet uw client toepassing inschakelen voor toegang tot uw data base in SQL Database door een Azure Active Directory-toepassing (Azure AD) in te stellen en de *toepassings-id en-* *sleutel* te kopiëren die u nodig hebt om uw toepassing te verifiëren.

Als u de *toepassings-id en-* *sleutel* wilt ophalen, volgt u de stappen in [Create a Azure Active Directory-toepassing en service-principal die toegang heeft tot resources](../../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Een sleutel kluis maken om uw sleutels op te slaan

Nu uw client-app is geconfigureerd en u uw toepassings-ID hebt, is het tijd om een sleutel kluis te maken en het toegangs beleid te configureren, zodat u en uw toepassing toegang hebben tot de geheimen van de kluis (de Always Encrypted sleutels). De machtigingen *maken*, *ophalen*, *lijst*, *ondertekenen*, *verifiëren*, *wrapKey* en *sleutel uitpakken* zijn vereist voor het maken van een nieuwe kolom hoofd sleutel en voor het instellen van versleuteling met SQL Server Management Studio.

U kunt snel een sleutel kluis maken door het volgende script uit te voeren. Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md)voor een gedetailleerde uitleg van deze opdrachten en meer informatie over het maken en configureren van een sleutel kluis.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

```powershell
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

Connect-AzAccount
$subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
Set-AzContext -SubscriptionId $subscriptionId

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
$subscriptionName = '<subscriptionName>'
$userPrincipalName = '<username@domain.com>'
$applicationId = '<applicationId from AAD application>'
$resourceGroupName = '<resourceGroupName>' # use the same resource group name when creating your database in Azure SQL Database below
$location = '<datacenterLocation>'
$vaultName = '<vaultName>'

az login
az account set --subscription $subscriptionName

az group create --location $location --name $resourceGroupName

az keyvault create --name $vaultName --resource-group $resourceGroupName --location $location

az keyvault set-policy --name $vaultName --key-permissions create get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --upn $userPrincipalName
az keyvault set-policy --name $vaultName --key-permissions get list sign unwrapKey verify wrapKey --resource-group $resourceGroupName --spn $applicationId
```

---

## <a name="connect-with-ssms"></a>Verbinden met SSMS

Open SQL Server Management Studio (SSMS) en maak verbinding met de server of worden beheerd met uw data base.

1. Open SQL Server Management Studio. (Ga naar **verbinding maken**  >  **Data base-engine** om het venster **verbinding maken met server** te openen als dit niet is geopend.)

2. Voer uw server naam of exemplaar naam en referenties in. 

    ![De verbindingsreeks kopiëren](./media/always-encrypted-azure-key-vault-configure/ssms-connect.png)

Als het venster **nieuwe firewall regel** wordt geopend, meldt u zich aan bij Azure en laat SSMS een nieuwe firewall regel voor u maken.

## <a name="create-a-table"></a>Een tabel maken

In deze sectie maakt u een tabel om patiënten-gegevens op te slaan. Het is niet eerst versleuteld. u configureert de versleuteling in de volgende sectie.

1. Vouw **data bases** uit.
2. Klik met de rechter muisknop op de data base en klik op **nieuwe query**.
3. Plak de volgende Transact-SQL (T-SQL) in het nieuwe query venster en **Voer** dit uit.

```sql
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

SSMS biedt een wizard waarmee u Always Encrypted eenvoudig kunt configureren door de kolom hoofd sleutel, kolom versleutelings sleutel en versleutelde kolommen in te stellen.

1. Vouw **data bases**  >  **Clinic**-  >  **tabellen** uit.
2. Klik met de rechter muisknop op de tabel **patiënten** en selecteer **kolommen versleutelen** om de wizard always encrypted te openen:

    ![Scherm afbeelding die de versleutelings kolommen markeert... menu optie.](./media/always-encrypted-azure-key-vault-configure/encrypt-columns.png)

De wizard Always Encrypted bevat de volgende secties: **kolom selectie**, **configuratie van hoofd sleutel**, **validatie** en **samen vatting**.

### <a name="column-selection"></a>Kolom selectie

Klik op **volgende** op de pagina **Inleiding** om de pagina **kolom selectie** te openen. Op deze pagina selecteert u de kolommen die u wilt versleutelen, [het type versleuteling en welke kolom versleutelings sleutel (CEK)](/sql/relational-databases/security/encryption/always-encrypted-wizard#Anchor_2) u wilt gebruiken.

Gegevens van **SSN** en **geboorte datum** versleutelen voor elke patiënt. De kolom SSN maakt gebruik van deterministische versleuteling, die ondersteuning biedt voor treffers, samen voegingen en Group by. In de kolom geboorte datum wordt wille keurige versleuteling gebruikt, die geen ondersteuning biedt voor bewerkingen.

Stel het **versleutelings type** voor de kolom SSN in op **deterministisch** en de kolom geboorte datum in **wille keurige volg orde**. Klik op **Volgende**.

![Kolommen versleutelen](./media/always-encrypted-azure-key-vault-configure/column-selection.png)

### <a name="master-key-configuration"></a>Configuratie van hoofd sleutel

Op de pagina **hoofd sleutel configuratie** kunt u uw CMK instellen en de sleutel archief provider selecteren waar de CMK worden opgeslagen. Op dit moment kunt u een CMK opslaan in het Windows-certificaat archief, Azure Key Vault of een Hardware Security module (HSM).

Deze zelf studie laat zien hoe u uw sleutels opslaat in Azure Key Vault.

1. Selecteer **Azure Key Vault**.
2. Selecteer de gewenste sleutel kluis in de vervolg keuzelijst.
3. Klik op **Volgende**.

![Configuratie van hoofd sleutel](./media/always-encrypted-azure-key-vault-configure/master-key-configuration.png)

### <a name="validation"></a>Validatie

U kunt de kolommen nu versleutelen of een Power shell-script opslaan om het later uit te voeren. Voor deze zelf studie selecteert u **nu door gaan naar volt ooien** en klikt u op **volgende**.

### <a name="summary"></a>Samenvatting

Controleer of de instellingen juist zijn en klik op **volt ooien** om de installatie voor always encrypted te volt ooien.

![Scherm afbeelding toont de resultaten pagina met taken die als door gegeven zijn gemarkeerd.](./media/always-encrypted-azure-key-vault-configure/summary.png)

### <a name="verify-the-wizards-actions"></a>De acties van de wizard controleren

Nadat de wizard is voltooid, is de data base ingesteld op Always Encrypted. De wizard heeft de volgende acties uitgevoerd:

- Een kolom hoofd sleutel gemaakt en opgeslagen in Azure Key Vault.
- Een kolom versleutelings sleutel gemaakt en opgeslagen in Azure Key Vault.
- De geselecteerde kolommen voor versleuteling zijn geconfigureerd. De tabel patiënten heeft momenteel geen gegevens, maar alle bestaande gegevens in de geselecteerde kolommen zijn nu versleuteld.

U kunt het maken van de sleutels in SSMS controleren door het uitvouwen van de **sessies**  >  **beveiliging**  >  **Always encrypted sleutels**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Een client toepassing maken die werkt met de versleutelde gegevens

Nu Always Encrypted is ingesteld, kunt u een toepassing bouwen waarmee de versleutelde kolommen worden *ingevoegd* en *geselecteerd* .  

> [!IMPORTANT]
> Uw toepassing moet [SqlParameter](/dotnet/api/system.data.sqlclient.sqlparameter) -objecten gebruiken bij het door geven van ongecodeerde gegevens naar de server met Always encrypted-kolommen. Het door geven van letterlijke waarden zonder gebruik te maken van SqlParameter-objecten leidt tot een uitzonde ring.

1. Open Visual Studio en maak een nieuwe C#- **console toepassing** (visual studio 2015 en eerder) of **console-app (.NET Framework)** (Visual Studio 2017 of hoger). Zorg ervoor dat uw project is ingesteld op **.NET Framework 4,6** of hoger.
2. Geef het project de naam **AlwaysEncryptedConsoleAKVApp** en klik op **OK**.
3. Installeer de volgende NuGet-pakketten door naar **hulpprogram ma's**  >  **NuGet package manager**  >  **Package Manager console** te gaan.

Voer de volgende twee regels code uit in de Package Manager-console:

   ```powershell
   Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Wijzig uw connection string om Always Encrypted in te scha kelen

In deze sectie wordt uitgelegd hoe u Always Encrypted in uw data base inschakelt connection string.

Als u Always Encrypted wilt inschakelen, moet u het tref woord voor de **kolom versleutelings instelling** toevoegen aan de Connection String en instellen op **ingeschakeld**.

U kunt dit rechtstreeks in het connection string instellen of u kunt dit instellen met behulp van [SqlConnectionStringBuilder](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder). De voorbeeld toepassing in de volgende sectie laat zien hoe u **SqlConnectionStringBuilder** kunt gebruiken.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Always Encrypted in het connection string inschakelen

Voeg het volgende tref woord toe aan uw connection string.

   `Column Encryption Setting=Enabled`

### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Always Encrypted met SqlConnectionStringBuilder inschakelen

De volgende code laat zien hoe u Always Encrypted inschakelt door [SqlConnectionStringBuilder. ColumnEncryptionSetting](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting) in te stellen op [ingeschakeld](/dotnet/api/system.data.sqlclient.sqlconnectioncolumnencryptionsetting).

```csharp
// Instantiate a SqlConnectionStringBuilder.
SqlConnectionStringBuilder connStringBuilder = new SqlConnectionStringBuilder("replace with your connection string");

// Enable Always Encrypted.
connStringBuilder.ColumnEncryptionSetting = SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>De Azure Key Vault provider registreren
De volgende code laat zien hoe u de Azure Key Vault provider registreert bij het ADO.NET-stuur programma.

```csharp
private static ClientCredential _clientCredential;

static void InitializeAzureKeyVaultProvider() {
    _clientCredential = new ClientCredential(applicationId, clientKey);

    SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider = new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

    Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers = new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

    providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
    SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
}
```

## <a name="always-encrypted-sample-console-application"></a>Voorbeeld console toepassing Always Encrypted

Dit voor beeld laat zien hoe u:

- Wijzig uw connection string om Always Encrypted in te scha kelen.
- Registreer Azure Key Vault als provider van de sleutel opslag van de toepassing.  
- Gegevens invoegen in de versleutelde kolommen.
- Selecteer een record door te filteren op een specifieke waarde in een versleutelde kolom.

Vervang de inhoud van *programma. cs* door de volgende code. Vervang de connection string voor de globale Connections Tring-variabele in de regel die de hoofd methode direct voorafgaat met uw geldige connection string van de Azure Portal. Dit is de enige wijziging die u moet aanbrengen in deze code.

Voer de app uit om Always Encrypted in actie te zien.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
using System.Data.SqlClient;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

namespace AlwaysEncryptedConsoleAKVApp {
    class Program {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";

        static void Main(string[] args) {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

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

            InsertPatient(new Patient() {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient() {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });

            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients()) {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null) {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }

        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider() {
            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope) {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient) {
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

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex) {
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


        static List<Patient> SelectAllPatients() {
            List<Patient> patients = new List<Patient>();

            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));

            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patients.Add(new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }

            return patients;
        }

        static Patient SelectPatientBySSN(string ssn) {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);

            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows) {
                        while (reader.Read()) {
                            patient = new Patient() {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else {
                        patient = null;
                    }
                }
                catch (Exception ex) {
                    throw;
                }
            }
            return patient;
        }

        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable() {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString)) {
                try {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex) {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
}
```

## <a name="verify-that-the-data-is-encrypted"></a>Controleren of de gegevens zijn versleuteld

U kunt snel controleren of de daad werkelijke gegevens op de server zijn versleuteld door de gegevens van de patiënten te doorzoeken met SSMS (met behulp van de huidige verbinding waar de instelling van de **kolom versleuteling** nog niet is ingeschakeld).

Voer de volgende query uit op de Clinic-data base.

```sql
SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

U kunt zien dat de versleutelde kolommen geen lees bare gegevens bevatten.

   ![Scherm opname die laat zien dat de versleutelde kolommen geen lees bare gegevens bevatten.](./media/always-encrypted-azure-key-vault-configure/ssms-encrypted.png)

Als u SSMS wilt gebruiken om toegang te krijgen tot de Lees bare gegevens, moet u eerst controleren of de gebruiker de juiste machtigingen heeft voor de Azure Key Vault: *Get*, *sleutel uitpakken* en *verify*. Zie [Column Master-sleutels maken en opslaan (always encrypted)](/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted)voor meer informatie.

Voeg vervolgens de para meter voor de *kolom versleutelings instelling = ingeschakeld* toe tijdens de verbinding.

1. Klik in SSMS met de rechter muisknop op uw server in **objectverkenner** en kies **verbinding verbreken**.
2. Klik **op**  >  **Data base-engine** verbinden om het venster **verbinding maken met server** te openen en klik op **Opties**.
3. Klik op **extra verbindings parameters** en type **kolom versleutelings instelling = ingeschakeld**.

    ![Scherm opname van het tabblad Extra correctie parameters.](./media/always-encrypted-azure-key-vault-configure/ssms-connection-parameter.png)

4. Voer de volgende query uit op de Clinic-data base.

   ```sql
   SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

   U kunt nu de Lees bare gegevens in de versleutelde kolommen weer geven.
   
   ![Nieuwe console toepassing](./media/always-encrypted-azure-key-vault-configure/ssms-plaintext.png)

## <a name="next-steps"></a>Volgende stappen

Nadat de data base is geconfigureerd voor het gebruik van Always Encrypted, kunt u het volgende doen:

- [Uw sleutels draaien en opschonen](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio).
- [Migreer gegevens die al zijn versleuteld met Always encrypted](/sql/relational-databases/security/encryption/migrate-sensitive-data-protected-by-always-encrypted).

## <a name="related-information"></a>Gerelateerde informatie

- [Always Encrypted (client ontwikkeling)](/sql/relational-databases/security/encryption/always-encrypted-client-development)
- [Transparante gegevens versleuteling](/sql/relational-databases/security/encryption/transparent-data-encryption)
- [Versleuteling SQL Server](/sql/relational-databases/security/encryption/sql-server-encryption)
- [Wizard Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-wizard)
- [Always Encrypted blog](/archive/blogs/sqlsecurity/always-encrypted-key-metadata)
