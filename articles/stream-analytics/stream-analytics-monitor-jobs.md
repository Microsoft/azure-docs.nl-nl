---
title: Azure Stream Analytics-taken via een programma controleren en beheren
description: In dit artikel wordt beschreven hoe u Stream Analytics-taken die zijn gemaakt via REST Api's, Azure SDK of Power shell, programmatisch kunt bewaken.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: dda1b6b652870440e7228e6640bb555f4c2e4a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015073"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Programmatisch een Stream Analytics taak controle maken

In dit artikel wordt beschreven hoe u bewaking voor een Stream Analytics taak inschakelt. Stream Analytics-taken die zijn gemaakt via REST Api's, Azure SDK of Power shell, hebben geen bewaking standaard ingeschakeld. U kunt dit hand matig inschakelen in de Azure Portal door naar de pagina monitor van de taak te gaan en op de knop inschakelen te klikken. u kunt dit proces ook automatiseren door de stappen in dit artikel te volgen. De bewakings gegevens worden weer gegeven in het gebied metrieken van de Azure Portal voor uw Stream Analytics taak.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit proces begint, moet u beschikken over de volgende vereisten:

* Visual Studio 2019 of 2015
* [Azure .NET SDK](https://azure.microsoft.com/downloads/) gedownload en geïnstalleerd
* Een bestaande Stream Analytics taak waarvoor bewaking moet zijn ingeschakeld

## <a name="create-a-project"></a>Een project maken

1. Maak een Visual Studio C# .NET-console toepassing.
2. Voer in de Package Manager-console de volgende opdrachten uit om de NuGet-pakketten te installeren. De eerste is de Azure Stream Analytics management .NET SDK. De tweede is de Azure Monitor SDK die wordt gebruikt om de bewaking in te scha kelen. De laatste is de Azure Active Directory-client die wordt gebruikt voor verificatie.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Voeg de sectie appSettings toe aan het App.config-bestand.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Vervang waarden voor *SubscriptionId* en *ActiveDirectoryTenantId* met uw Azure-abonnement en Tenant-id's. U kunt deze waarden ophalen door de volgende Power shell-cmdlet uit te voeren:
   
   ```powershell
   Get-AzureAccount
   ```
4. Voeg de volgende using-instructies toe aan het bronbestand (Program.cs) in het project.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Voeg een verificatie methode toe.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Beheer-clients maken

Met de volgende code worden de benodigde variabelen en managementclients ingesteld.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Bewaking inschakelen voor een bestaande Stream Analytics taak

Met de volgende code wordt de bewaking voor een **bestaande** stream Analytics-taak ingeschakeld. Het eerste deel van de code voert een GET-aanvraag uit voor de Stream Analytics-service om informatie over de betreffende Stream Analytics taak op te halen. De eigenschap *id* (opgehaald van de GET-aanvraag) wordt gebruikt als een para meter voor de put-methode in de tweede helft van de code, waarmee een put-aanvraag wordt verzonden naar de Insights-service om controle voor de stream Analytics-taak in te scha kelen.

> [!WARNING]
> Als u eerder bewaking hebt ingeschakeld voor een andere Stream Analytics taak, hetzij via de Azure Portal of via een programma via de onderstaande code, **raden we u aan dezelfde opslag accountnaam op te geven die u hebt gebruikt bij het eerder inschakelen van de bewaking.**
> 
> Het opslag account is gekoppeld aan de regio waarin u de Stream Analytics taak hebt gemaakt in, niet specifiek voor de taak zelf.
> 
> Alle Stream Analytics-taken (en alle andere Azure-resources) in diezelfde regio delen dit opslag account om bewakings gegevens op te slaan. Als u een ander opslag account opgeeft, kan dit leiden tot onbedoelde neven effecten bij de bewaking van uw andere Stream Analytics-taken of andere Azure-resources.
> 
> De naam van het opslag account dat u gebruikt voor `<YOUR STORAGE ACCOUNT NAME>` het vervangen van de volgende code moet een opslag account zijn dat zich in hetzelfde abonnement bevindt als de stream Analytics-taak waarvoor u controle inschakelt.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Ondersteuning krijgen

Probeer voor meer hulp onze [micro soft Q&een vraag pagina voor Azure stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](/rest/api/streamanalytics/)