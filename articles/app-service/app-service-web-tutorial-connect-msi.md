---
title: 'Zelfstudie: Toegang tot gegevens met beheerde identiteit'
description: Informatie over hoe u databaseverbindingen veiliger kunt maken met behulp van een beheerde identiteit en over hoe u het kunt toepassen op andere Azure-services.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 2c19ee2b8e7ec3c695b2c76c46402c118c559b40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736235"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Zelfstudie: Azure SQL Database-verbinding vanuit App Service beveiligen met een beheerde identiteit

[App Servicex](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie in Azure. De service bevat ook een [beheerde identiteit](overview-managed-identity.md) voor uw app. Dit is een gebruiksklare oplossing voor het beveiligen van toegang tot [Azure SQL Database](/azure/sql-database/) en andere Azure-services. Beheerde identiteiten in App Service maken uw app veiliger doordat geheimen in uw app, zoals referenties in de verbindingsreeksen, worden verwijderd. In deze zelfstudie voegt u een beheerde identiteit toe aan het voorbeeld van de web-app dat u hebt gemaakt in één van de volgende zelfstudies: 

- [Zelfstudie: Een ASP.NET-app in Azure bouwen met behulp van Azure SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Zelfstudie: Een ASP.NET Core- en Azure SQL Database-app bouwen in Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Wanneer u klaar bent, maakt uw voorbeeld-app veilig verbinding met SQL Database zonder dat een gebruikersnaam en wachtwoorden zijn vereist.

> [!NOTE]
> De stappen in deze zelfstudie ondersteunen de volgende versies:
> 
> - .NET Framework 4.7.2 en hoger
> - .NET Core 2.2 en hoger
>

U leert het volgende:

> [!div class="checklist"]
> * Beheerde identiteiten inschakelen
> * SQL Database toegang verlenen tot de beheerde identiteit
> * Entity Framework configureren voor het gebruik van Azure AD-verificatie met SQL Database
> * Verbinding maken met SQL Database vanuit Visual Studio met behulp van Azure AD-verificatie

> [!NOTE]
>Azure AD-verificatie _verschilt_ van [Geïntegreerde Windows-verificatie](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) in on-premises Active Directory (AD DS). AD DS en Azure AD gebruiken totaal verschillende verificatieprotocollen. Zie [Documentatie voor Azure AD Domain Services](../active-directory-domain-services/index.yml) voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Dit artikel gaat verder waar u bent gebleven in [Zelfstudie: Een ASP.NET-app bouwen in Azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) of [Zelfstudie: Een ASP.NET Core- en SQL Database-app maken in Azure App Service](tutorial-dotnetcore-sqldb-app.md). Als u dat nog niet hebt gedaan, volgt u eerst een van de twee zelfstudies. U kunt ook de stappen aanpassen voor uw eigen .NET-app met SQL Database.

Als u fouten wilt opsporen in uw app met SQL Database als de back-end, zorgt u ervoor dat de clientverbinding vanuit uw computer is toegestaan. Als dat niet het geval is, voegt u het IP-adres van de client toe door de stappen te volgen op [IP-firewallregels op serverniveau beheren met de Azure-portal](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

Bereid uw omgeving voor op Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Databasetoegang verlenen aan Azure AD-gebruiker

Schakel eerst Azure AD-verificatie in om SQL Database door een Azure AD-gebruiker toe te wijzen als de Active Directory-beheerder van de server. Deze gebruiker wijkt af van de Microsoft-account die u hebt gebruikt om u aan te melden voor uw Azure-abonnement. Dit moet een gebruiker zijn die u hebt gemaakt, geïmporteerd, gesynchroniseerd of uitgenodigd voor Azure AD. Zie [Azure AD-functies en -beperkingen in SQL Database](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations) voor meer informatie over de toegestane Azure AD-gebruikers.

Als uw Azure AD-tenant nog geen gebruiker heeft, maakt u er een door de stappen te volgen op [Gebruikers toevoegen of verwijderen met behulp van Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Zoek de object-id van de Azure AD-gebruiker met behulp van de [`az ad user list`](/cli/azure/ad/user#az-ad-user-list) en vervang *\<user-principal-name>* . Het resultaat wordt opgeslagen in een variabele.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Als u de lijst met alle UPN’s in Azure AD wilt zien, voert u `az ad user list --query [].userPrincipalName` uit.
>

Voeg deze Azure AD-gebruiker toe als een Active Directory-beheerder met behulp van opdracht [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) in de Cloud Shell. Vervang in de volgende opdracht *\<server-name>* met de servernaam (zonder het achtervoegsel `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Zie [Een Azure Active Directory-beheerder inrichten voor uw server](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) voor meer informatie over het toevoegen van een Active Directory-beheerder

## <a name="set-up-visual-studio"></a>Visual Studio instellen

### <a name="windows-client"></a>Windows-client
Visual Studio voor Windows is geïntegreerd met Azure AD-verificatie. Als u de ontwikkeling en foutopsporing in Visual Studio wilt inschakelen, voegt u uw Azure AD-gebruiker toe in Visual Studio door **Bestand** > **Accountinstellingen** te selecteren in het menu en op **Een account toevoegen** te klikken.

Als u de Azure AD-gebruiker voor Azure-serviceverificatie wilt instellen, selecteert u **Hulpprogramma's** > **Opties** in het menu en selecteert u vervolgens **Azure-serviceverificatie** > **Accounts selecteren**. Selecteer de Azure AD-gebruiker die u hebt toegevoegd en klik op **OK**.

U kunt nu uw app ontwikkelen en fouten opsporen met de SQL Database als back-end, met behulp van Azure AD-verificatie.

### <a name="macos-client"></a>macOS-client

Visual Studio voor Mac is niet geïntegreerd met Azure AD-verificatie. De [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)-bibliotheek die u later gebruikt, kan echter gebruikmaken van tokens van Azure CLI. Als u de ontwikkeling en foutopsporing in Visual Studio wilt inschakelen, moet u eerst [Azure CLI installeren](/cli/azure/install-azure-cli) op uw lokale computer.

Als Azure CLI is geïnstalleerd op uw lokale computer, meldt u zich met de volgende opdracht aan bij Azure CLI met behulp van uw Azure AD-gebruiker:

```azurecli
az login --allow-no-subscriptions
```
U kunt nu uw app ontwikkelen en fouten opsporen met de SQL Database als back-end, met behulp van Azure AD-verificatie.

## <a name="modify-your-project"></a>Uw project wijzigen

De stappen die u voor uw project uitvoert, zijn afhankelijk van het feit of het een ASP.NET-project of een ASP.NET Core-project is.

- [ASP.NET wijzigen](#modify-aspnet)
- [ASP.NET Core wijzigen](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET wijzigen

Open Package Manager Console in Visual Studio en voeg het NuGet-pakket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) toe:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

In *Web.config* werkt u vanaf de bovenkant van het bestand en brengt u de volgende wijzigingen aan:

- Voeg in `<configSections>` de volgende sectiedeclaratie toe:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- Voeg onder de tag voor het sluiten van `</configSections>` de volgende XML-code toe voor `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Zoek de verbindingsreeks met de naam `MyDbConnection` en vervang de waarde `connectionString` door `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Vervang _\<server-name>_ en _\<db-name>_ met uw servernaam en databasenaam.

> [!NOTE]
> De SqlAuthenticationProvider die u zojuist hebt geregistreerd, is gebaseerd op de AppAuthentication-bibliotheek die u eerder hebt geïnstalleerd. Standaard wordt een door het systeem toegewezen identiteit gebruikt. Als u gebruik wilt maken van een door de gebruiker toegewezen identiteit, moet u een aanvullende configuratie opgeven. Zie [ondersteuning voor verbindingsreeks](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support) voor de AppAuthentication-bibliotheek.

Dat is alles wat u nodig hebt om verbinding te maken met SQL Database. Als u fouten opspoort in Visual Studio, gebruikt uw code de Azure AD-gebruiker die u hebt geconfigureerd in [Visual Studio instellen](#set-up-visual-studio). U stelt SQL Database later in om verbinding te maken vanuit de beheerde identiteit van uw App Service-app.

Typ `Ctrl+F5` om de app opnieuw uit te voeren. Dezelfde CRUD-app in uw browser is nu rechtstreeks verbonden met de Azure SQL Database, met behulp van Azure AD-verificatie. Met deze installatie kunt u databasemigraties uitvoeren vanuit Visual Studio.

### <a name="modify-aspnet-core"></a>ASP.NET Core wijzigen

Open Package Manager Console in Visual Studio en voeg het NuGet-pakket [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) toe:

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

In de [ASP.NET Core en SQL Database-zelfstudie](tutorial-dotnetcore-sqldb-app.md)wordt de `MyDbConnection` verbindingsreeks helemaal niet gebruikt omdat de lokale ontwikkelingsomgeving een SQLite-databasebestand gebruikt en de Azure-productieomgeving een verbindingsreeks van App Service gebruikt. Met Active Directory-verificatie is het belangrijk dat beide omgevingen dezelfde verbindingsreeks gebruiken. Vervang in *appsettings.json* de waarde van de `MyDbConnection` verbindingsreeks door:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Vervolgens geeft u de Entity Framework databasecontext met het toegangstoken voor de SQL Database. Voeg in *Data\MyDatabaseContext.cs* de volgende code toe in de accolades van de lege `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` constructor:

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Deze demonstratiecode is synchroon voor duidelijkheid en eenvoud.

Dat is alles wat u nodig hebt om verbinding te maken met SQL Database. Als u fouten opspoort in Visual Studio, gebruikt uw code de Azure AD-gebruiker die u hebt geconfigureerd in [Visual Studio instellen](#set-up-visual-studio). U stelt SQL Database later in om verbinding te maken vanuit de beheerde identiteit van uw App Service-app. De `AzureServiceTokenProvider`-klasse slaat het token in de cache op en haalt het op voor het verstrijken van Azure AD. U hebt geen aangepaste code nodig om het token te vernieuwen.

> [!TIP]
> Als de Azure AD-gebruiker die u hebt geconfigureerd toegang heeft tot meerdere tenants, roept u `GetAccessTokenAsync("https://database.windows.net/", tenantid)` aan met de gewenste tenant-id om het juiste toegangstoken op te halen.

Typ `Ctrl+F5` om de app opnieuw uit te voeren. Dezelfde CRUD-app in uw browser is nu rechtstreeks verbonden met de Azure SQL Database, met behulp van Azure AD-verificatie. Met deze installatie kunt u databasemigraties uitvoeren vanuit Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Connectiviteit met beheerde identiteit gebruiken

Vervolgens configureert u uw App Service-app om verbinding te maken met SQL Database met een door het systeem toegewezen beheerde identiteit.

> [!NOTE]
> Hoewel de instructies in deze sectie betrekking hebben op een door het systeem toegewezen identiteit, kan een door de gebruiker toegewezen identiteit net zo eenvoudig worden gebruikt. Om dit te doen. u moet de `az webapp identity assign command` wijzigen om de gewenste door de gebruiker toegewezen identiteit toe te wijzen. Wanneer u de SQL-gebruiker maakt, moet u ervoor zorgen dat u de naam van de door de gebruiker toegewezen identiteitsresource gebruikt in plaats van de naam van de site.

### <a name="enable-managed-identity-on-app"></a>Een beheerde identiteit inschakelen voor een app

Als u een beheerde identiteit voor uw Azure-app wilt inschakelen, gebruikt u de opdracht [az webapp identity assign](/cli/azure/webapp/identity#az-webapp-identity-assign) in de Cloud Shell. Vervang *\<app-name>* in de volgende opdracht.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Hier is een voorbeeld van de uitvoer:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Machtigingen toewijzen aan beheerde identiteit

> [!NOTE]
> Als u wilt, kunt u de identiteit toevoegen aan een [Azure AD-groep](../active-directory/fundamentals/active-directory-manage-groups.md) en vervolgens SQL Database toegang verlenen tot de Azure AD-groep in plaats van de identiteit. Bijvoorbeeld, met de volgende opdrachten wordt de beheerde identiteit uit de vorige stap toegevoegd aan een nieuwe groep met de naam _myAzureSQLDBAccessGroup_:
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Meld u in de Cloud Shell aan bij SQL Database met behulp van de SQLCMD-opdracht. Vervang _\<server-name>_ door de naam van uw server _\<db-name>_ met de naam van de database die uw app gebruikt en _\<aad-user-name>_ en _\<aad-password>_ met de referenties van uw Azure AD-gebruiker.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Voer bij de SQL-prompt voor de gewenste database de volgende opdrachten uit om de machtigingen te verlenen die u app nodig heeft. Bijvoorbeeld: 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* is de naam van de beheerde identiteit in Azure AD. Als de identiteit is toegewezen aan het systeem, wordt de naam altijd hetzelfde als de naam van uw App Service-app. Als u machtigingen wilt verlenen voor een Azure AD-groep, gebruikt u in plaats daarvan de weergavenaam van de groep (bijvoorbeeld *myAzureSQLDBAccessGroup*).

Typ `EXIT` om terug te keren naar de Cloud Shell-prompt.

> [!NOTE]
> De back-end-services van beheerde identiteiten [onderhoudt ook een tokencache](overview-managed-identity.md#obtain-tokens-for-azure-resources) die het token voor een doelresource alleen bijwerkt wanneer het verloopt. Als u een fout maakt bij het configureren van uw SQL Database-machtigingen en de machtigingen probeert te wijzigen *nadat* u heeft geprobeerd om een token op te halen met uw app, krijgt u geen nieuw token met de bijgewerkte machtigingen totdat het token in de cache verloopt.

> [!NOTE]
> AAD wordt niet ondersteund voor on-premises SQL Server, waaronder MSI's. 

### <a name="modify-connection-string"></a>De verbindingsreeks wijzigen

Houd er rekening mee dat dezelfde wijzigingen die u hebt aangebracht in *web.config* of *appsettings.json* werken met de beheerde identiteit, dus het enige wat u moet doen, is het verwijderen van de bestaande verbindingsreeks in App Service, die Visual Studio de eerste keer heeft gemaakt bij het implementeren van uw app. Gebruik de volgende opdracht, maar vervang *\<app-name>* door de naam van uw app.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Uw wijzigingen publiceren

U hoeft nu alleen nog maar uw wijzigingen naar Azure te publiceren.

**Als u vanuit de [-zelfstudie werkt: Bouw een ASP.NET-app in Azure met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)** , publiceer uw wijzigingen in Visual Studio. Klik in de **Solution Explorer** met de rechtermuisknop op uw project **DotNetAppSqlDb** en selecteer **Publiceren**.

![Publiceren vanuit Solution Explorer](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Klik op de publicatiepagina op **Publiceren**. 

**Als u vanuit de [-zelfstudie werkt: Bouw een ASP.NET Core en SQL Database-app in Azure App Service](tutorial-dotnetcore-sqldb-app.md)** , publiceer uw wijzigingen met Git, met de volgende opdrachten:

```bash
git commit -am "configure managed identity"
git push azure main
```

Wanneer de nieuwe webpagina uw takenlijst weergeeft, maakt uw app verbinding met de database met behulp van de beheerde identiteit.

![Azure-app na Code First Migration](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

U zou nu de takenlijst moeten kunnen bewerken als voorheen.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Beheerde identiteiten inschakelen
> * SQL Database toegang verlenen tot de beheerde identiteit
> * Entity Framework configureren voor het gebruik van Azure AD-verificatie met SQL Database
> * Verbinding maken met SQL Database vanuit Visual Studio met behulp van Azure AD-verificatie

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw web-app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](app-service-web-tutorial-custom-domain.md)