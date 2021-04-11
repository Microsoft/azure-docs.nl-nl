---
title: 'Quickstart: Een beheergroep maken met .NET Core'
description: In deze quickstart gebruikt u .NET Core om een beheergroep te maken om uw resources in een resourcehiërarchie in te delen.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592548"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Quickstart: Een beheergroep maken met .NET Core

Managementgroepen zijn containers die u helpen bij het beheren van toegang, beleid en naleving voor meerdere abonnementen. Maak deze containers om een doeltreffende en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [op rollen gebaseerd toegangsbeheer van Azure](../../role-based-access-control/overview.md). Zie [Uw resources indelen met Azure-beheergroepen](overview.md) voor meer informatie over beheergroepen.

Het kan tot vijftien minuten duren voordat de eerste beheergroep die in de map is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheergroepen in Azure in te stellen voor uw map. U ontvangt een melding wanneer het proces is voltooid. Zie [Eerste configuratie van beheergroepen](./overview.md#initial-setup-of-management-groups) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Een Azure-service-principal, met inbegrip van de _clientId_ en _clientSecret_. Als u geen service-principal hebt voor gebruik met Azure Policy of als u een nieuwe wilt maken, raadpleegt u [Azure-beheerbibliotheken voor .NET-verificatie](/dotnet/azure/sdk/authentication#mgmt-auth).
  Sla de stap over om de .NET Core-pakketten te installeren; dit doet u in de volgende stappen.

- Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor beheergroepen is toegewezen als [Hiërarchie beschermen](./how-to/protect-resource-hierarchy.md#setting---require-authorization) niet is ingeschakeld. Deze nieuwe beheergroep wordt een onderliggend element van de hoofdbeheergroep of de [standaard beheergroep](./how-to/protect-resource-hierarchy.md#setting---default-management-group) en de maker krijgt de roltoewijzing "Eigenaar". Met de beheergroep-service kan deze functie worden toegewezen, zodat roltoewijzingen niet nodig zijn op hoofdmapniveau. Gebruikers hebben geen toegang tot de hoofdbeheergroep wanneer deze wordt gemaakt. Om te voorkomen dat de drempel van het vinden van de Azure AD Global Admins om beheergroepen te kunnen gebruiken te hoog is, wordt het maken van de eerste beheergroepen op hoofdmapniveau toegestaan.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Installatie van toepassing

Als u .NET Core voor het beheren van beheergroepen wilt inschakelen, maakt u een nieuwe consoletoepassing en installeert u de vereiste pakketten.

1. Controleer of de meest recente versie van .NET Core is geïnstalleerd (minimaal **3.1.8**). Als deze nog niet is geïnstalleerd, downloadt u deze op [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Initialiseer een nieuwe .NET Core-consoletoepassing met de naam mgCreate:

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Wijzig de mappen in de nieuwe projectmap en installeer de vereiste pakketten voor Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

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
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Bouw en publiceer de consoletoepassing `mgCreate`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>De beheergroep maken

In deze quickstart maakt u een nieuwe beheergroep in de hoofdbeheergroep.

1. Wijzig mappen in de `{run-folder}` die u hebt gedefinieerd met de vorige opdracht `dotnet publish`.

1. Voer de volgende opdracht in de terminal in:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

In de voorgaande opdrachten wordt de volgende informatie gebruikt:

- Vervang `{tenantId}` door uw tenant-id
- `{clientId}` - vervang door de client-id van uw service-principal
- `{clientSecret}` - vervang door het clientgeheim van uw service-principal
- `{groupID}` - vervang door de id voor uw nieuwe beheergroep
- `{displayName}` - vervang door de beschrijvende naam voor uw nieuwe beheergroep

Het resultaat is een nieuwe beheergroep in de hoofdbeheergroep.

## <a name="clean-up-resources"></a>Resources opschonen

- Verwijder de nieuwe beheergroep via de portal.

- Als u de .NET Core-consoletoepassing en de geïnstalleerde pakketten wilt verwijderen, kunt u dit doen door de projectmap `mgCreate` te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheergroep gemaakt om uw resource-hiërarchie te organiseren. De beheergroep kan abonnementen of andere beheergroepen bevatten.

Ga verder met het volgende voor meer informatie over beheergroepen en het beheren van uw resource-hiërarchie:

> [!div class="nextstepaction"]
> [Uw resources beheren met beheergroepen](./manage.md)