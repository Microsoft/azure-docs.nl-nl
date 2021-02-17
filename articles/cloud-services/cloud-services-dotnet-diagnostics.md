---
title: Azure Diagnostics (.NET) gebruiken met Cloud Services (klassiek) | Microsoft Docs
description: Azure Diagnostics gebruiken voor het verzamelen van gegevens uit Azure Cloud Services voor het opsporen van fouten, het meten van prestaties, bewaking, verkeers analyse en meer.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e4b5372a2b6e9b833f3a0213d69649b75ae2589b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578195"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services-classic"></a>Inschakelen van Azure Diagnostics in azure Cloud Services (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Zie [Azure Diagnostics overzicht](../azure-diagnostics.md) voor een achtergrond op Azure Diagnostics.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Diagnostische gegevens inschakelen in een worker-rol
In dit scenario wordt beschreven hoe u een Azure worker-rol implementeert die telemetriegegevens verzendt met behulp van de .NET Event source-klasse. Azure Diagnostics wordt gebruikt om de telemetriegegevens te verzamelen en op te slaan in een Azure-opslag account. Wanneer u een werknemersrol maakt, schakelt Visual Studio automatisch diagnostische gegevens van 1,0 in als onderdeel van de oplossing in azure Sdk's voor .NET 2,4 en eerdere versies. De volgende instructies beschrijven het proces voor het maken van de rol van werk nemers, het uitschakelen van diagnose 1,0 van de oplossing en het implementeren van diagnostische gegevens 1,2 of 1,3 voor uw werknemersrol.

### <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u een Azure-abonnement hebt en Visual Studio gebruikt met de Azure SDK. Als u geen Azure-abonnement hebt, kunt u zich aanmelden voor de [gratis proef versie][Free Trial]. Zorg ervoor dat u [Azure PowerShell versie 0.8.7 of hoger installeert en configureert][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Stap 1: een werk rollen maken
1. Start **Visual Studio**.
2. Maak een **Azure-Cloud service** project op basis van de **Cloud** sjabloon die gericht is op .NET Framework 4,5.  Geef het project de naam ' WadExample ' en klik op OK.
3. Selecteer **werk rollen** en klik op OK. Het project wordt gemaakt.
4. Dubbel klik in **Solution Explorer** op het eigenschappen bestand **WorkerRole1** .
5. Schakel op het tabblad **configuratie** de optie **Diagnostische gegevens inschakelen** uit om diagnostische gegevens te uitschakelen 1,0 (Azure SDK 2,4 en eerder).
6. Bouw uw oplossing om te controleren of er geen fouten zijn.

### <a name="step-2-instrument-your-code"></a>Stap 2: uw code instrumenteren
Vervang de inhoud van WorkerRole.cs door de volgende code. De klasse SampleEventSourceWriter, overgenomen van de [klasse Event source][EventSource Class], implementeert vier logboek methoden: **SendEnums**, **MessageMethod**, **SetOther** en **HighFreq**. De eerste para meter voor de methode **etw** definieert de id voor de betreffende gebeurtenis. De methode Run implementeert een oneindige lus die elke 10 seconden een van de logboek registratie methoden aanroept die in de **SampleEventSourceWriter** -klasse zijn geïmplementeerd.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Stap 3: uw werk rollen implementeren

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Implementeer uw werknemersrol in azure vanuit Visual Studio door het project **WadExample** te selecteren in de Solution Explorer klik vervolgens op **publiceren** in het menu **Build** .
2. Kies uw abonnement.
3. Selecteer in het dialoog venster **Microsoft Azure publicatie-instellingen** de optie **nieuwe maken...**.
4. Voer in het dialoog venster **Cloud service en opslag account maken** een **naam** in (bijvoorbeeld ' WadExample ') en selecteer een regio of affiniteits groep.
5. Stel de **omgeving** in op **staging**.
6. Wijzig indien nodig de andere **instellingen** en klik op **publiceren**.
7. Nadat de implementatie is voltooid, controleert u in het Azure Portal dat de Cloud service wordt **uitgevoerd** .

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Stap 4: het diagnostische configuratie bestand maken en de uitbrei ding installeren
1. Down load de schema definitie van het open bare configuratie bestand door de volgende Power shell-opdracht uit te voeren:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Voeg een XML-bestand toe aan uw **WorkerRole1** -project door met de rechter muisknop op het **WorkerRole1** -project te klikken en nieuw item **toevoegen** te selecteren  ->  **...** -> **Visual C#-items**  ->  **Gegevens**  ->  **XML-bestand**. Geef het bestand de naam WadExample.xml.

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Koppel de WadConfig. XSD aan het configuratie bestand. Zorg ervoor dat het venster WadExample.xml editor het actieve venster is. Druk op **F4** om het venster **Eigenschappen** te openen. Klik op de eigenschap **schemas** in het venster **Eigenschappen** . Klik op de **..** . in de eigenschap **schemas** . Klik op de knop **Toevoegen...** en navigeert u naar de locatie waar u het XSD-bestand hebt opgeslagen en selecteert u het bestand WadConfig. XSD. Klik op **OK**.

4. Vervang de inhoud van het WadExample.xml configuratie bestand door de volgende XML en sla het bestand op. Dit configuratie bestand definieert een aantal prestatie meter items die moeten worden verzameld: één voor CPU-gebruik en één voor geheugen gebruik. Vervolgens definieert de configuratie de vier gebeurtenissen die overeenkomen met de methoden in de SampleEventSourceWriter-klasse.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Stap 5: diagnostische gegevens installeren voor uw Werknemersrol
De Power shell-cmdlets voor het beheren van diagnostische gegevens voor een web-of worker-rol zijn: set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension en Remove-AzureServiceDiagnosticsExtension.

1. Open Azure PowerShell.
2. Voer het script uit om diagnostische gegevens te installeren voor uw werknemersrol (Vervang *StorageAccountKey* door de sleutel van het opslag account voor uw wadexample-opslag account en *config_path* met het pad naar het *WadExample.xml* bestand):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Stap 6: uw telemetrie-gegevens bekijken
Ga in Visual Studio **Server Explorer** naar het opslag account wadexample. Nadat de Cloud service ongeveer vijf (5) minuten is uitgevoerd, ziet u de tabellen **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** en **WADSetOtherTable**. Dubbel klik op een van de tabellen om de telemetrie weer te geven die is verzameld.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schema van configuratie bestand
Het configuratie bestand voor diagnostische gegevens definieert waarden die worden gebruikt voor het initialiseren van diagnostische configuratie-instellingen wanneer de diagnostische agent wordt gestart. Zie de [meest recente schema referentie](../azure-monitor/agents/diagnostics-extension-versions.md) voor geldige waarden en voor beelden.

## <a name="troubleshooting"></a>Problemen oplossen
Zie [probleem oplossing Azure Diagnostics](../azure-monitor/agents/diagnostics-extension-troubleshooting.md) voor hulp bij veelvoorkomende problemen als u problemen ondervindt.

## <a name="next-steps"></a>Volgende stappen
[Bekijk een lijst met verwante artikelen over diagnostische gegevens van virtuele machines van Azure](../azure-monitor/agents/diagnostics-extension-overview.md) voor het wijzigen van de door u verzamelde informatie, het oplossen van problemen of meer informatie over diagnostiek in het algemeen.

[EventSource Class]: /dotnet/api/system.diagnostics.tracing.eventsource

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: /previous-versions/azure/gg433048(v=azure.100)
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: /powershell/azure/