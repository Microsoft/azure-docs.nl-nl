---
title: Verbinding maken met beheerde identiteit - Azure Database for MySQL
description: Meer informatie over verbinding maken en verifiëren met behulp van beheerde identiteit voor verificatie met Azure Database for MySQL
author: sunilagarwal
ms.author: sunila
ms.service: mysql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: c9c5c938650d1932349f17bde6b30c65718ef72a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774679"
---
# <a name="connect-with-managed-identity-to-azure-database-for-mysql"></a>Een beheerde identiteit verbinden met Azure Database for MySQL

In dit artikel wordt beschreven hoe u een door de gebruiker toegewezen identiteit voor een virtuele Azure-machine (VM) gebruikt voor toegang tot een Azure Database for MySQL server. Managed Service Identity's worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. 

In deze zelfstudie leert u procedures om het volgende te doen:

- Uw VM toegang verlenen tot een Azure Database for MySQL server
- Maak een gebruiker in de database die staat voor de door de gebruiker toegewezen identiteit van de VM
- Een toegangs token opvragen met behulp van de identiteit van de VM en deze gebruiken om een query uit te voeren op Azure Database for MySQL server
- Het ophalen van het token implementeren in een C#-voorbeeldtoepassing

> [!IMPORTANT]
> Verbinding maken met beheerde identiteit is alleen beschikbaar voor MySQL 5.7 en nieuwer.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](../../articles/active-directory/managed-identities-azure-resources/overview.md). Als u geen Azure-account hebt, [registreert u zich voor een gratis account](https://azure.microsoft.com/free/) voordat u verder gaat.
- Als u de vereiste resource wilt maken en rollen wilt beheren, moet uw account 'Eigenaar'-machtigingen hebben voor het juiste bereik (uw abonnement of resourcegroep). Als u hulp nodig hebt met roltoewijzing, zie Azure-rollen toewijzen om toegang tot de resources van uw [Azure-abonnement te beheren.](../../articles/role-based-access-control/role-assignments-portal.md)
- U hebt een Azure-VM nodig (bijvoorbeeld het uitvoeren van Ubuntu Linux) die u wilt gebruiken voor toegang tot uw database met behulp van beheerde identiteit
- U hebt een Azure Database for MySQL-databaseserver nodig waar [Azure AD-verificatie is](howto-configure-sign-in-azure-ad-authentication.md) geconfigureerd
- Als u het C#-voorbeeld wilt volgen, voltooit u eerst de handleiding verbinding [maken met C#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Een door de gebruiker toegewezen beheerde identiteit maken voor uw VM

Maak een identiteit in uw abonnement met behulp van [de opdracht az identity create.](/cli/azure/identity#az_identity_create) U kunt dezelfde resourcegroep gebruiken waarin uw virtuele machine wordt uitgevoerd, of een andere.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Als u de identiteit in de volgende stappen wilt configureren, gebruikt u de opdracht [az identity show](/cli/azure/identity#az_identity_show) om de resource-id en client-id van de identiteit op te slaan in variabelen.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

We kunnen nu de door de gebruiker toegewezen identiteit toewijzen aan de VM met de [opdracht az vm identity assign:](/cli/azure/vm/identity#az_vm_identity_assign)

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Als u de installatie wilt voltooien, toont u de waarde van de client-id, die u in de volgende stappen nodig hebt:

```bash
echo $clientID
```

## <a name="creating-a-mysql-user-for-your-managed-identity"></a>Een MySQL-gebruiker maken voor uw beheerde identiteit

Maak nu als azure AD-beheerder verbinding met uw MySQL-database en voer de volgende SQL-instructies uit:

```sql
SET aad_auth_validate_oids_in_tenant = OFF;
CREATE AADUSER 'myuser' IDENTIFIED BY 'CLIENT_ID';
```

De beheerde identiteit heeft nu toegang bij het verifiëren met de gebruikersnaam `myuser` (vervang door een naam van uw keuze).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Het toegangs token ophalen uit de Azure Instance Metadata-service

Uw toepassing kan nu een toegangsteken ophalen uit de Azure Instance Metadata-service en gebruiken voor de authenticatie bij de database.

Dit token wordt opgehaald door een HTTP-aanvraag te maken aan `http://169.254.169.254/metadata/identity/oauth2/token` en de volgende parameters door te geven:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (die u eerder hebt opgehaald)

U krijgt een JSON-resultaat terug dat een veld bevat. Deze lange tekstwaarde is het toegangs token voor beheerde identiteit, dat u als wachtwoord moet gebruiken bij het maken van verbinding met de `access_token` database.

Voor testdoeleinden kunt u de volgende opdrachten uitvoeren in uw shell. Houd er rekening mee `curl` dat u , en de `jq` `mysql` -client moet hebben geïnstalleerd.

```bash
# Retrieve the access token
accessToken=$(curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token)

# Connect to the database
mysql -h SERVER --user USER@SERVER --enable-cleartext-plugin --password=$accessToken
```

U bent nu verbonden met de database die u eerder hebt geconfigureerd.

## <a name="connecting-using-managed-identity-in-c"></a>Verbinding maken met behulp van beheerde identiteit in C #

In deze sectie ziet u hoe u een toegangs token op kunt halen met behulp van de door de gebruiker toegewezen beheerde identiteit van de VM en hoe u dit kunt gebruiken om een Azure Database for MySQL. Azure Database for MySQL biedt standaard ondersteuning voor Azure AD-verificatie, zodat toegangstokens die zijn verkregen met behulp van beheerde identiteiten voor Azure-resources direct kunnen worden geaccepteerd. Wanneer u een verbinding met MySQL maakt, geeft u het toegangs token door in het wachtwoordveld.

Hier is een voorbeeld van .NET-code voor het openen van een verbinding met MySQL met behulp van een toegangs token. Deze code moet worden uitgevoerd op de VM om toegang te krijgen tot het eindpunt van de door de gebruiker toegewezen beheerde identiteit van de VM. .NET framework 4.6 of hoger of .NET Core 2.2 is vereist voor het gebruik van de toegangsmethode met een toegangstoken. Vervang de waarden van HOST, USER, DATABASE en CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using MySql.Data.MySqlClient;

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

        static async Task Main(string[] args)
        {
            //
            // Get an access token for MySQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for MySQL is https://ossrdbms-aad.database.windows.net/
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
            // Open a connection to the MySQL server using the access token.
            //
            var builder = new MySqlConnectionStringBuilder
            {
                Server = Host,
                Database = Database,
                UserID = User,
                Password = accessToken,
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT VERSION()";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine("\nConnected!\n\nMySQL version: {0}", reader.GetString(0));
                        }
                    }
                }
            }
        }
    }
}
```

Bij het uitvoeren geeft deze opdracht een uitvoer als deze:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

MySQL version: 5.7.27
```

## <a name="next-steps"></a>Volgende stappen

* Bekijk de algemene concepten voor [Azure Active Directory verificatie met Azure Database for MySQL](concepts-azure-ad-authentication.md)
