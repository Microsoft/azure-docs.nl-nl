---
title: ActiveDirectoryInteractive maakt verbinding met SQL
description: Voor beeld van C#-code, met uitleg, om verbinding te maken met Azure SQL Database met behulp van de modus SqlAuthenticationMethod. ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: e2fa09ac8609310d4579590214bc25e5d7ee309f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641570"
---
# <a name="connect-to-azure-sql-database-with-azure-ad-multi-factor-authentication"></a>Verbinding maken met Azure SQL Database met Azure AD Multi-Factor Authentication
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dit artikel bevat een C#-programma dat verbinding maakt met Azure SQL Database. Het programma maakt gebruik van interactieve modus verificatie, die [Azure AD-multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)ondersteunt.

Zie [Azure Active Directory ondersteuning in SQL Server Data tools (SSDT)](/sql/ssdt/azure-active-directory)voor meer informatie over de ondersteuning van multi-factor Authentication voor SQL-hulpprogram ma's.

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Multi-Factor Authentication voor Azure SQL Database

Vanaf .NET Framework versie 4.7.2 heeft de Enum [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) een nieuwe waarde: `ActiveDirectoryInteractive` . In een C#-programma van de client stuurt de Enum-waarde het systeem voor het gebruik van de interactieve modus Azure Active Directory (Azure AD) die Multi-Factor Authentication ondersteunt om verbinding te maken met Azure SQL Database. De gebruiker die het programma uitvoert, ziet de volgende dialoog vensters:

* Een dialoog venster waarin een Azure AD-gebruikers naam wordt weer gegeven en wordt gevraagd om het wacht woord van de gebruiker.

   Als het domein van de gebruiker federatief is in azure AD, wordt dit dialoog venster niet weer gegeven, omdat er geen wacht woord nodig is.

   Als het Azure AD-beleid Multi-Factor Authentication voor de gebruiker oplegt, worden de volgende twee dialoog vensters weer gegeven.

* De eerste keer dat een gebruiker Multi-Factor Authenticationt, wordt in het systeem een dialoog venster weer gegeven waarin u wordt gevraagd om een mobiel telefoon nummer om tekst berichten te verzenden naar. Elk bericht bevat de *verificatie code* die de gebruiker in het volgende dialoog venster moet invoeren.

* Een dialoog venster waarin u wordt gevraagd om een Multi-Factor Authentication verificatie code, die het systeem naar een mobiele telefoon heeft verzonden.

Zie [aan de slag met Azure ad multi-factor Authentication in de Cloud](../../active-directory/authentication/howto-mfa-getstarted.md)voor meer informatie over het configureren van Azure ad om multi-factor Authentication in te stellen.

Zie [multi-factor Authentication configureren voor SQL Server Management Studio en Azure AD](authentication-mfa-ssms-configure.md)voor scherm opnamen van deze dialoog vensters.

> [!TIP]
> U kunt .NET Framework Api's zoeken met de [pagina .net API-browser](/dotnet/api/).
>
> U kunt ook direct zoeken met de [ &lt; &gt; para meter optioneel? term = Zoek waarde](/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configureer uw C#-toepassing in de Azure Portal

Voordat u begint, moet er een [logische SQL-Server](logical-servers.md) zijn gemaakt en beschikbaar zijn.

### <a name="register-your-app-and-set-permissions"></a>Uw app registreren en machtigingen instellen

Als u Azure AD-verificatie wilt gebruiken, moet uw C#-programma zich registreren als een Azure AD-toepassing. Als u een app wilt registreren, moet u een Azure AD-beheerder zijn of een gebruiker die de rol van Azure AD- *toepassings ontwikkelaar* heeft toegewezen. Zie voor meer informatie over het toewijzen van rollen [beheerders en niet-beheerders rollen toewijzen aan gebruikers met Azure Active Directory](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

Als u een app-registratie voltooit, wordt er een **toepassings-id** gegenereerd. Uw programma moet deze ID toevoegen om verbinding te maken.

De benodigde machtigingen voor uw toepassing registreren en instellen:

1. Selecteer in de Azure Portal **Azure Active Directory**  >  **app-registraties**  >  **nieuwe registratie**.

    ![App-registratie](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Nadat de app-registratie is gemaakt, wordt de waarde van de **toepassings-id** gegenereerd en weer gegeven.

    ![App-ID weer gegeven](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selecteer **API-machtigingen** > **Een machtiging toevoegen**.

    ![Machtigings instellingen voor de geregistreerde app](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **Api's selecteren mijn organisatie gebruikt** > type **Azure SQL database** in de zoek > en selecteert **Azure SQL database**.

    ![Toegang tot de API voor Azure SQL Database toevoegen](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecteer **gedelegeerde machtigingen**  >  **user_impersonation**  >  **machtigingen toe te voegen**.

    ![Machtigingen voor Azure SQL Database overdragen aan de API](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>Een Azure AD-beheerder voor uw server instellen

Als u het C#-programma wilt uitvoeren, moet een [logische SQL Server](logical-servers.md) -beheerder een Azure AD-beheerder voor uw server toewijzen.

Selecteer op de pagina **SQL Server** de optie beheerders beheerder   >  **instellen** Active Directory beheerder.

Voor meer informatie over Azure AD-beheerders en-gebruikers voor Azure SQL Database raadpleegt u de scherm opnamen in [Azure Active Directory authenticatie configureren en beheren met SQL database](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Een niet-beheerders gebruiker toevoegen aan een specifieke data base (optioneel)

Een Azure AD-beheerder voor een [logische SQL-Server](logical-servers.md) kan het C#-voorbeeld programma uitvoeren. Een Azure AD-gebruiker kan het programma uitvoeren als ze zich in de-data base bevinden. Een Azure AD SQL-beheerder of een Azure AD-gebruiker die al aanwezig is in de data base en de `ALTER ANY USER` machtiging voor de data base heeft, kan een gebruiker toevoegen.

U kunt een gebruiker toevoegen aan de data base met behulp van de SQL- [`Create User`](/sql/t-sql/statements/create-user-transact-sql) opdracht. Een voorbeeld is `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Zie [Azure Active Directory verificatie gebruiken voor verificatie met SQL database, een beheerd exemplaar of Azure Synapse Analytics](authentication-aad-overview.md)voor meer informatie.

## <a name="new-authentication-enum-value"></a>Nieuwe Enum-waarde voor verificatie

Het C#-voor beeld is afhankelijk van de [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient) naam ruimte. Een speciale interesse voor Multi-Factor Authentication is de Enum `SqlAuthenticationMethod` , die de volgende waarden heeft:

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Gebruik deze waarde met een Azure AD-gebruikers naam om Multi-Factor Authentication te implementeren. Deze waarde is de focus van het huidige artikel. Het produceert een interactieve ervaring door dialoog vensters voor het wacht woord van de gebruiker weer te geven en vervolgens voor Multi-Factor Authentication validatie als Multi-Factor Authentication voor deze gebruiker is opgelegd. Deze waarde is beschikbaar vanaf .NET Framework versie 4.7.2.

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Gebruik deze waarde voor een *federatief* account. Voor een federatief account is de gebruikers naam bekend bij het Windows-domein. Deze verificatie methode biedt geen ondersteuning voor Multi-Factor Authentication.

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Gebruik deze waarde voor verificatie waarvoor een Azure AD-gebruikers naam en-wacht woord zijn vereist. Azure SQL Database voert de verificatie uit. Deze methode biedt geen ondersteuning voor Multi-Factor Authentication.

> [!NOTE]
> Als u .NET Core gebruikt, moet u de naam ruimte [micro soft. data. SqlClient](/dotnet/api/microsoft.data.sqlclient) gebruiken. Zie de volgende [blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)voor meer informatie.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>C#-parameter waarden instellen vanuit de Azure Portal

Om het C#-programma te kunnen uitvoeren, moet u de juiste waarden toewijzen aan statische velden. Hier worden velden met voorbeeld waarden weer gegeven. Ook worden de Azure Portal locaties weer gegeven waar u de benodigde waarden kunt verkrijgen.

| Statische veld naam | Voorbeeldwaarde | Waar in Azure Portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | ' my-sqldb-svr.database.windows.net ' | **SQL-servers**  >  **Filteren op naam** |
| AzureAD_UserID | "auser \@ ABC.onmicrosoft.com" | **Azure Active Directory**  >  **Gebruiker**  >  **Nieuwe gast gebruiker** |
| Initial_DatabaseName | "myDatabase" | **SQL-servers**  >  **SQL-data bases** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory**  >  **App-registraties**  >  **Zoeken op naam**  >  **Toepassings-id** |
| RedirectUri | nieuwe URI (" https://mywebserver.com/ ") | **Azure Active Directory**  >  **App-registraties**  >  **Zoeken op naam**  >  *[Uw app-registratie]*  >  **Instellingen**  >  **RedirectURIs**<br /><br />Voor dit artikel is een geldige waarde in orde voor RedirectUri, omdat deze hier niet wordt gebruikt. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Controleren met SQL Server Management Studio

Voordat u het C#-programma uitvoert, is het een goed idee om te controleren of uw instellingen en configuraties juist zijn in SQL Server Management Studio (SSMS). Een C#-programma fout kan vervolgens worden beperkt tot de bron code.

### <a name="verify-server-level-firewall-ip-addresses"></a>IP-adressen van Firewall op server niveau controleren

Voer SSMS uit vanaf dezelfde computer, in hetzelfde gebouw, waar u van plan bent het C#-programma uit te voeren. Voor deze test is een wille keurige **verificatie** modus OK. Zie [firewall regels op server-en database niveau](firewall-configure.md) voor hulp als er een indicatie is dat de server uw IP-adres niet accepteert.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Azure Active Directory Multi-Factor Authentication controleren

Voer SSMS opnieuw uit, deze keer met **verificatie** ingesteld op **Azure Active Directory-Universal met MFA**. Voor deze optie is SSMS versie 17,5 of hoger vereist.

Zie [multi-factor Authentication configureren voor SSMS en Azure AD](authentication-mfa-ssms-configure.md)voor meer informatie.

> [!NOTE]
> Als u een gast gebruiker in de data base bent, moet u ook de Azure AD-domein naam opgeven voor de Data Base: Selecteer **Opties**  >  **AD-domein naam of Tenant-id**. Als u de domein naam in de Azure Portal wilt zoeken, selecteert u **Azure Active Directory**  >  **aangepaste domein namen**. In het C#-voorbeeld programma is het opgeven van een domein naam niet nodig.

## <a name="c-code-example"></a>Voor beeld van C#-code

> [!NOTE]
> Als u .NET Core gebruikt, moet u de naam ruimte [micro soft. data. SqlClient](/dotnet/api/microsoft.data.sqlclient) gebruiken. Zie de volgende [blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)voor meer informatie.

Het voor beeld C#-programma is afhankelijk van de dll-assembly [*micro soft. Identity model. clients. ActiveDirectory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory) .

Als u dit pakket wilt installeren, selecteert u in Visual Studio **project**  >  **NuGet-pakketten beheren**. Zoek en Installeer **micro soft. Identity model. clients. ActiveDirectory**.

Dit is een voor beeld van C#-bron code.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  .
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  .
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Dit is een voor beeld van de test uitvoer van C#.

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek.

& [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)