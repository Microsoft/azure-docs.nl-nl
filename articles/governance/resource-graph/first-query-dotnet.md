---
title: 'Quickstart: Uw eerste .NET Core-query'
description: In deze quickstart voert u de stappen uit om de Resource Graph NuGet-pakketten voor .NET Core in te schakelen en uw eerste query uit te voeren.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917568"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Quickstart: uw eerste Resource Graph-query uitvoeren met .NET Core

De eerste stap voor het gebruik van Azure Resource Graph bestaat uit het controleren of de vereiste pakketten voor .NET Core zijn geïnstalleerd. In deze quickstart doorloopt u het proces voor het toevoegen van de pakketten aan uw .NET Core-installatie.

Aan het einde van dit proces hebt u de pakketten toegevoegd aan uw .NET Core-installatie en hebt u uw eerste Resource Graph-query uitgevoerd.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Een Azure-service-principal, met inbegrip van de _clientId_ en _clientSecret_. Als u geen service-principal hebt voor gebruik met Resource Graph of als u een nieuwe wilt maken, raadpleegt u [Azure-beheerbibliotheken voor .NET-verificatie](/dotnet/azure/sdk/authentication#mgmt-auth).
  Sla de stap over om de .NET Core-pakketten te installeren; dit doet u in de volgende stappen.

## <a name="create-the-resource-graph-project"></a>Het Resource Graph-project maken

Als u wilt dat .NET Core query's kan uitvoeren op een Azure Resource Graph, maakt u een nieuwe consoletoepassing en installeert u de vereiste pakketten.

1. Controleer of de meest recente versie van .NET Core is geïnstalleerd (minimaal **3.1.5**). Als deze nog niet is geïnstalleerd, downloadt u deze op [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Initialiseer een nieuwe .NET Core-consoletoepassing met de naam argQuery:

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Wijzig de mappen in de nieuwe projectmap en installeer de vereiste pakketten voor Azure Resource Graph:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Vervang de standaard `program.cs` door de volgende code en sla het bijgewerkte bestand op:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. Bouw en publiceer de consoletoepassing `argQuery`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Nu de .NET Core-consoletoepassing is gebouwd en gepubliceerd, is het tijd om een eenvoudige Resource Graph-query uit te proberen. De query retourneert de eerste vijf Azure-resources met de **naam** en het **resourcetype** van elke resource.

In elke aanroep naar `argQuery` worden verschillende variabelen gebruikt die u moet vervangen door uw eigen waarden:

- Vervang `{tenantId}` door uw tenant-id
- `{clientId}` - vervang door de client-id van uw service-principal
- `{clientSecret}` - vervang door het clientgeheim van uw service-principal
- Vervang `{subscriptionId}` door uw abonnements-ID

1. Wijzig mappen in de `{run-folder}` die u hebt gedefinieerd met de vorige opdracht `dotnet publish`.

1. Voer uw eerste Azure Resource Graph-query uit met de gecompileerde .NET Core-consoletoepassing:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Wijzig de laatste parameter in `argQuery.exe` en wijzig de query om `order by` de eigenschap **Naam**:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Met deze opdracht worden de queryresultaten eerst beperkt en vervolgens gerangschikt.

1. Wijzig de laatste parameter in `argQuery.exe` en wijzig de query om eerst te `order by` op de eigenschap **Naam** en daarna de resultaten van de top vijf te `limit`:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en gesorteerd op de eigenschap **Naam**, maar nog steeds beperkt tot de top vijf.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de .NET Core-consoletoepassing en de geïnstalleerde pakketten wilt verwijderen, kunt u dit doen door de projectmap `argQuery` te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een .NET Core-consoletoepassing gemaakt met de vereiste Resource Graph-pakketten en hebt u uw eerste query uitgevoerd. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)