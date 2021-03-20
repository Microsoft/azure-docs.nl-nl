---
title: 'Quickstart: Nieuwe beleidstoewijzing met .NET Core'
description: In deze quickstart gebruikt u .NET Core om een Azure Policy-toewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91604548"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Quickstart: Een beleidstoewijzing maken om niet-compatibele resources met .NET Core te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources. In deze snelstart maakt u een beleidstoewijzing om te identificeren welke virtuele machines geen beheerde schijven gebruiken. Zodra de toewijzing is voltooid, kunt u de virtuele machines identificeren die _niet-compatibel_ zijn.

De .NET Core-bibliotheek wordt gebruikt om Azure-resources te beheren. In deze handleiding wordt uitgelegd hoe u de .NET Core-bibliotheek voor Azure Policy gebruikt om een beleidstoewijzing te maken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Een Azure-service-principal, met inbegrip van de _clientId_ en _clientSecret_. Als u geen service-principal hebt voor gebruik met Azure Policy of als u een nieuwe wilt maken, raadpleegt u [Azure-beheerbibliotheken voor .NET-verificatie](/dotnet/azure/sdk/authentication#mgmt-auth).
  Sla de stap over om de .NET Core-pakketten te installeren; dit doet u in de volgende stappen.

## <a name="create-the-azure-policy-project"></a>Het Azure Policy-project maken

Als u .NET Core voor het beheren van Azure Policy wilt inschakelen, maakt u een nieuwe consoletoepassing en installeert u de vereiste pakketten.

1. Controleer of de meest recente versie van .NET Core is geïnstalleerd (minimaal **3.1.8**). Als deze nog niet is geïnstalleerd, downloadt u deze op [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Initialiseer een nieuwe .NET Core-consoletoepassing met de naam policyAssignment:

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Wijzig de mappen in de nieuwe projectmap en installeer de vereiste pakketten voor Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

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
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Bouw en publiceer de consoletoepassing `policyAssignment`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze quickstart maakt u een beleidstoewijzing en wijst u de definitie **Controleer virtuele machines die niet gebruikmaken van beheerde schijven** (`06a78e20-9358-41c9-923c-fb736d382a4d`) toe. Deze beleidsdefinitie identificeert resources die niet voldoen aan de voorwaarden die zijn vastgelegd in de beleidsdefinitie.

1. Wijzig mappen in de `{run-folder}` die u hebt gedefinieerd met de vorige opdracht `dotnet publish`.

1. Voer de volgende opdracht in de terminal in:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

In de voorgaande opdrachten wordt de volgende informatie gebruikt:

- Vervang `{tenantId}` door uw tenant-id
- `{clientId}` - vervang door de client-id van uw service-principal
- `{clientSecret}` - vervang door het clientgeheim van uw service-principal
- Vervang `{subscriptionId}` door uw abonnements-ID
- **name**: de unieke naam voor het object voor beleidstoewijzing. In het bovenstaande voorbeeld wordt _audit-vm-manageddisks_ gebruikt.
- **displayName**: de weergavenaam voor de beleidstoewijzing. In dit geval gebruikt u de toewijzing _Virtuele machines zonder beheerde schijven controleren_.
- **policyDefID**: het pad van de beleidsdefinitie, op basis waarvan u de toewijzing maakt. In dit geval is het de id van de beleidsdefinitie _Virtuele machines zonder beheerde schijven controleren_.
- **description**: een grondigere uitleg van wat het beleid doet of waarom het aan dit bereik is toegewezen.
- **scope**: een bereik bepaalt voor welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Het kan variëren van een beheergroep tot een afzonderlijke resource. Zorg dat u `{scope}` vervangt door een van de volgende patronen:
  - Beheergroep: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonnement: `/subscriptions/{subscriptionId}`
  - Resourcegroep: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Resource: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

U kunt nu niet-compatibele resources identificeren om inzicht te krijgen in de nalevingsstatus van uw omgeving.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Nu uw beleidstoewijzing is gemaakt, kunt u resources identificeren die niet compatibel zijn.

1. Initialiseer een nieuwe .NET Core-consoletoepassing met de naam policyCompliance:

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Wijzig de mappen in de nieuwe projectmap en installeer de vereiste pakketten voor Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

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
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Bouw en publiceer de consoletoepassing `policyAssignment`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Wijzig mappen in de `{run-folder}` die u hebt gedefinieerd met de vorige opdracht `dotnet publish`.

1. Voer de volgende opdracht in de terminal in:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

In de voorgaande opdrachten wordt de volgende informatie gebruikt:

- Vervang `{tenantId}` door uw tenant-id
- `{clientId}` - vervang door de client-id van uw service-principal
- `{clientSecret}` - vervang door het clientgeheim van uw service-principal
- Vervang `{subscriptionId}` door uw abonnements-ID
- **name**: de unieke naam voor het object voor beleidstoewijzing. In het bovenstaande voorbeeld wordt _audit-vm-manageddisks_ gebruikt.

De resultaten in `response` komen overeen met wat u ziet op het tabblad **Resourcenaleving** van een beleidstoewijzing in de Azure Portal-weergave.

## <a name="clean-up-resources"></a>Resources opschonen

- Verwijder de beleidstoewijzing _VM's zonder beheerde schijven controleren_ via de portal. De beleidsdefinitie is ingebouwd, dus is er geen definitie om te verwijderen.

- Als u de .NET Core-consoletoepassing en de geïnstalleerde pakketten wilt verwijderen, kunt u dit doen door de projectmappen `policyAssignment` en `policyCompliance` te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een beleidsdefinitie toegewezen om niet-compatibele resources in uw Azure-omgeving te identificeren.

Voor meer informatie over het toewijzen van beleidsdefinities om te controleren of nieuwe resources compatibel zijn gaat u verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)
