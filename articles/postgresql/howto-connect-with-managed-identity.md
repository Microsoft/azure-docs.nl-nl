---
title: Verbinding maken met beheerde identiteit-Azure Database for PostgreSQL-één server
description: Meer informatie over het maken van verbinding en verificatie met behulp van beheerde identiteit voor verificatie met Azure Database for PostgreSQL
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b0d6b083228ddab9d958bf3480b592f7ee0d892d
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092644"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Een beheerde identiteit verbinden met Azure Database for PostgreSQL

In dit artikel leest u hoe u een door de gebruiker toegewezen identiteit voor een virtuele Azure-machine (VM) gebruikt om toegang te krijgen tot een Azure Database for PostgreSQL-server. Managed Service Identity's worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. 

In deze zelfstudie leert u procedures om het volgende te doen:
- Uw VM toegang verlenen tot een Azure Database for PostgreSQL server
- Maak een gebruiker in de data base die de door de gebruiker toegewezen identiteit van de virtuele machine vertegenwoordigt
- Een toegangs Token ophalen met behulp van de VM-identiteit en dit gebruiken om een Azure Database for PostgreSQL server op te vragen
- Het ophalen van tokens implementeren in een C#-voorbeeld toepassing

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](../../articles/active-directory/managed-identities-azure-resources/overview.md). Als u geen Azure-account hebt, [registreert u zich voor een gratis account](https://azure.microsoft.com/free/) voordat u verder gaat.
- Voor het maken van de vereiste resources en rollen beheer moet uw account de machtigingen eigenaar hebben op het juiste bereik (uw abonnement of resource groep). Als u hulp nodig hebt bij roltoewijzing, raadpleegt u [Azure-rollen toewijzen om de toegang tot uw Azure-abonnements resources te beheren](../../articles/role-based-access-control/role-assignments-portal.md).
- U hebt een Azure-VM (bijvoorbeeld met Ubuntu Linux) nodig die u wilt gebruiken voor toegang tot uw data base met behulp van beheerde identiteit
- U hebt een Azure Database for PostgreSQL database server met [Azure AD-verificatie](howto-configure-sign-in-aad-authentication.md) geconfigureerd
- Als u het C#-voor beeld wilt volgen, voltooit u eerst de hand leiding How to [Connect with C#](connect-csharp.md) .

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Een door de gebruiker toegewezen beheerde identiteit voor uw virtuele machine maken

Maak een identiteit in uw abonnement met behulp van de opdracht [AZ Identity Create](/cli/azure/identity#az-identity-create) . U kunt dezelfde resource groep gebruiken als waarop uw virtuele machine wordt uitgevoerd, of een andere.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Als u de identiteit in de volgende stappen wilt configureren, gebruikt u de opdracht [AZ Identity show](/cli/azure/identity#az-identity-show) om de resource-id en de client-id van de identiteit op te slaan in variabelen.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

We kunnen nu de door de gebruiker toegewezen identiteit toewijzen aan de virtuele machine met de opdracht [AZ VM Identity Assign](/cli/azure/vm/identity#az-vm-identity-assign) :

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Als u de installatie wilt volt ooien, geeft u de waarde van de client-ID op die u nodig hebt in de volgende stappen:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Een PostgreSQL-gebruiker maken voor uw beheerde identiteit

Maak nu verbinding met de Azure AD-beheerder voor uw PostgreSQL-data base en voer de volgende SQL-instructies uit:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

De beheerde identiteit heeft nu toegang bij de verificatie met de gebruikers naam `myuser` (Vervang door een naam van uw keuze).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Het toegangs Token ophalen uit de Azure instance meta data-service

Uw toepassing kan nu een toegangs Token ophalen uit de meta gegevens service van Azure instance en gebruiken om te verifiëren met de data base.

Het ophalen van het token wordt uitgevoerd door een HTTP-aanvraag in te stellen `http://169.254.169.254/metadata/identity/oauth2/token` en de volgende para meters door te geven:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (die u eerder hebt opgehaald)

U krijgt een JSON-resultaat dat een veld bevat `access_token` -deze lange tekst waarde is het token Managed Identity Access dat u als het wacht woord moet gebruiken wanneer u verbinding maakt met de data base.

Voor test doeleinden kunt u de volgende opdrachten in uw shell uitvoeren. Houd er rekening mee dat u nodig hebt `curl` , `jq` en dat de- `psql` client is geïnstalleerd.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

U bent nu verbonden met de data base die u eerder hebt geconfigureerd.

## <a name="connecting-using-managed-identity-in-c"></a>Verbinding maken met behulp van beheerde identiteit in C #

In deze sectie wordt beschreven hoe u een toegangs token krijgt met behulp van de door de gebruiker toegewezen beheerde identiteit van de virtuele machine en deze gebruikt om Azure Database for PostgreSQL aan te roepen. Azure Database for PostgreSQL biedt systeem eigen ondersteuning voor Azure AD-verificatie, zodat het rechtstreeks de toegangs tokens kan accepteren die zijn verkregen met beheerde identiteiten voor Azure-resources. Wanneer u een verbinding met PostgreSQL maakt, geeft u het toegangs token door in het veld wacht woord.

Hier volgt een voor beeld van een .NET-code voor het openen van een verbinding met PostgreSQL met behulp van een toegangs token. Deze code moet worden uitgevoerd op de virtuele machine om toegang te krijgen tot het eind punt van de door de gebruiker toegewezen beheerde identiteit van de virtuele machine. .NET framework 4.6 of hoger of .NET Core 2.2 is vereist voor het gebruik van de toegangsmethode met een toegangstoken. Vervang de waarden van HOST, gebruiker, data base en CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
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
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Wanneer u deze opdracht uitvoert, wordt een uitvoer als volgt weer geven:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Volgende stappen

* Bekijk de algemene concepten voor [Azure Active Directory verificatie met Azure database for PostgreSQL](concepts-aad-authentication.md)
