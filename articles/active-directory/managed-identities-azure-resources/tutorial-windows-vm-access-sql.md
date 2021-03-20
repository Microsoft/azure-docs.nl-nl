---
title: 'Zelfstudie: Een beheerde identiteit gebruiken voor toegang tot Azure SQL Database - Windows - Azure AD'
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het gebruiken van een door het Windows-VM-systeem toegewezen beheerde identiteit om toegang te krijgen tot Azure SQL Database.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4f56ce9fa86dc27b77ad6b463479d13c8e4e7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91856509"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot Azure SQL Database met een door het systeem toegewezen identiteit voor een virtuele Windows-machine (VM). Managed Service Identity's worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
>
> * Uw virtuele machine toegang verlenen tot Azure SQL Database
> * Azure AD-verificatie inschakelen
> * Een ingesloten gebruiker maken in de database die staat voor de door de systeem toegewezen id van de VM
> * Een toegangstoken ophalen met de identiteit van de virtuele machine en daarmee een query uitvoeren op Azure SQL Database

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>Inschakelen

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>Toegang verlenen

Als u uw virtuele machine toegang wilt verlenen tot een database in Azure SQL Database, kunt u een bestaande [logische SQL-server](../../azure-sql/database/logical-servers.md) gebruiken of een nieuwe server maken. Voor het maken van een nieuwe server en database met behulp van Azure Portal, volgt u deze [Azure SQL-snelstart](../../azure-sql/database/single-database-create-quickstart.md). Er zijn ook snelstarts in de [documentatie over Azure SQL](/azure/sql-database/) voor het gebruik van Azure CLI en Azure Powershell.

U moet twee stappen uitvoeren om uw virtuele machine toegang te verlenen tot een database:

1. Schakel Azure AD-verificatie in voor de server.
2. Maak een **ingesloten gebruiker** in de database die staat voor de door de systeem toegewezen id van de VM.

### <a name="enable-azure-ad-authentication"></a>Azure AD-verificatie inschakelen

**[Azure AD-verificatie configureren](../../azure-sql/database/authentication-aad-configure.md):**

1. Selecteer **SQL-servers** in het linkernavigatievenster van Azure Portal.
2. Klik op de SQL-server waarvoor Azure AD-verificatie moet worden ingeschakeld.
3. Klik in de sectie **Instellingen** van de blade op **Active Directory-beheerder**.
4. Klik in de opdrachtbalk op **Beheerder instellen**.
5. Selecteer een Azure AD-gebruikersaccount dat beheerder van de server moet worden gemaakt en klik op **Selecteren**.
6. Klik in de opdrachtbalk op **Opslaan**.

### <a name="create-contained-user"></a>Ingesloten gebruiker maken

In dit gedeelte wordt getoond hoe u een ingesloten gebruiker in de database maakt die staat voor de door de systeem toegewezen id van de VM. Voor deze stap hebt u [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) nodig. Voordat u begint, kan het ook handig zijn de volgende artikelen te lezen voor meer achtergrondinformatie over Azure AD-integratie:

- [Universele verificatie met SQL Database en Azure Synapse Analytics (SSMS-ondersteuning voor MFA)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [Azure Active Directory-verificatie configureren en beheren met SQL Database of Azure Synapse Analytics](../../azure-sql/database/authentication-aad-configure.md)

Voor SQL DB zijn unieke AAD-weergavenamen vereist. Hiermee moeten de AAD-accounts, zoals gebruikers, groepen en service-principals (toepassingen), en VM-namen die voor een beheerde identiteit zijn ingeschakeld, voor wat betreft de weergavenamen uniek zijn gedefinieerd in AAD. In SQL DB wordt de AAD-weergavenaam gecontroleerd tijdens het maken van dergelijke gebruikers met T-SQL. Als de naam niet uniek is, kan er geen unieke AAD-weergavenaam voor een bepaald account worden aangevraagd.

**Een ingesloten gebruiker maken:**

1. Start SQL Server Management Studio.
2. Voer in het dialoogvenster **Verbinding maken met server** de naam van de server in het veld **Servernaam** in.
3. Selecteer in het veld **Verificatie** **Active Directory - Universeel met ondersteuning voor MFA**.
4. Voer in het veld **Gebruikersnaam** de naam in van het Azure AD-account dat u hebt ingesteld als de beheerder van de server, bijvoorbeeld helen@woodgroveonline.com
5. Klik op **Opties**.
6. Voer in het veld **Verbinding maken met database** de naam in van de niet-systeemdatabase die u wilt configureren.
7. Klik op **Verbinden**. Voltooi het aanmeldingsproces.
8. Vouw in **Objectverkenner** de map **Databases** uit.
9. Klik met de rechtermuisknop op een gebruikersdatabase en klik op **Nieuwe query**.
10. Voer in het queryvenster de volgende regel in en klik op **Uitvoeren** in de werkbalk:

    > [!NOTE]
    > `VMName` in de volgende opdracht staat de naam van de VM waarvoor u door het systeem toegewezen id's hebt ingeschakeld in het gedeelte met vereisten.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    De opdracht voor het maken van de ingesloten gebruiker voor de door het systeem toegewezen id voor de VM wordt uitgevoerd.
11. Wis het queryvenster, voer de volgende regel in en klik op **Uitvoeren** in de werkbalk:

    > [!NOTE]
    > `VMName` in de volgende opdracht staat de naam van de VM waarvoor u door het systeem toegewezen id's hebt ingeschakeld in het gedeelte met vereisten.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    De opdracht wordt uitgevoerd en de ingesloten gebruiker heeft nu leestoegang tot de gehele database.

Code die wordt uitgevoerd op de VM kan nu een token verkrijgen via de door het systeem toegewezen beheerde identiteit en het token gebruiken voor verificatie bij de server.

## <a name="access-data"></a>Toegang tot gegevens

In dit gedeelte wordt getoond hoe u een toegangstoken kunt ophalen met behulp van de door het systeem toegewezen beheerde identiteit van de VM en dit kunt gebruiken om Azure SQL aan te roepen. Azure SQL biedt systeemeigen ondersteuning voor Azure AD-verificatie, zodat toegangstokens die zijn verkregen met behulp van beheerde identiteiten voor Azure-resources direct kunnen worden geaccepteerd. U gebruikt de toegangsmethode met het **toegangstoken** voor het maken van een verbinding met SQL. Dit maakt deel uit van de integratie van Azure SQL met Azure AD en wijkt af van het opgeven van referenties in de verbindingsreeks.

Hier volgt een voorbeeld van .NET-code voor het openen van een verbinding met SQL met behulp van een toegangstoken. De code moet worden uitgevoerd op de virtuele machine om toegang te krijgen tot het eindpunt van de door het systeem toegewezen beheerde identiteit van de virtuele machine. **.NET framework 4.6** of hoger of **.NET Core 2.2** is vereist voor het gebruik van de toegangsmethode met een toegangstoken. Vervang AZURE-SQL-SERVERNAME en DATABASE door de benodigde waarden. De resource-id voor Azure SQL is `https://database.windows.net/`.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Met PowerShell kunt u snel de end-to-end-instellingen testen zonder een app te hoeven schrijven en implementeren op de virtuele machine.

1. Navigeer in Azure Portal naar **Virtuele machines**, ga naar uw virtuele Windows-machine en klik op de pagina **Overzicht** op **Verbinden**.
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de virtuele Windows-machine.
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u **PowerShell** in de externe sessie.
4. Verstuur met de cmdlet `Invoke-WebRequest` van Powershell een aanvraag naar het lokale eindpunt van de beheerde identiteit om een toegangstoken voor Azure SQL op te halen.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    Converteer de reactie van een JSON-object naar een PowerShell-object.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extraheer het toegangstoken uit de reactie.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. Open een verbinding met de server. Vervang de waarden voor AZURE-SQL-SERVERNAME en DATABASE.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Maak vervolgens een query en verzend de query naar de server. Vergeet niet de waarde voor TABLE te vervangen.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Bekijk de waarde van `$DataSet.Tables[0]` om de resultaten van de query weer te geven.

## <a name="disable"></a>Uitschakelen

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd om met een door het systeem toegewezen beheerde identiteit toegang te krijgen tot Azure SQL Database. Raadpleeg voor meer informatie over Azure SQL Database:

> [!div class="nextstepaction"]
> [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)
