---
title: Snapshot Debugger voor .NET-apps in Azure App Service inschakelen | Microsoft Docs
description: Snapshot Debugger voor .NET-apps in Azure App Service inschakelen
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6d111379cbeb0ea4eeac9ea0868b6d263a0a540c
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435719"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>Snapshot Debugger voor .NET-apps in Azure App Service inschakelen

Snapshot Debugger werkt momenteel voor ASP.NET-en ASP.NET Core-apps die worden uitgevoerd op Azure App Service op Windows-service plannen. Het is raadzaam om uw toepassing uit te voeren op de laag basis service of hoger wanneer u het fout opsporingsprogramma voor moment opnamen gebruikt. Voor de meeste toepassingen hebben de gratis en gedeelde service lagen onvoldoende geheugen om moment opnamen op te slaan.

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> Snapshot Debugger inschakelen
Volg de onderstaande instructies om Snapshot Debugger voor een app in te scha kelen. Als u een ander type Azure-service gebruikt, zijn hier instructies voor het inschakelen van Snapshot Debugger op andere ondersteunde platforms:
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines-en virtuele-machine schaal sets](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [On-premises virtuele of fysieke machines](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Als u een preview-versie van .NET Core gebruikt, volgt u de instructies voor het [inschakelen van Snapshot debugger in andere omgevingen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) eerst om het [micro soft. ApplicationInsights. SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet-pakket op te nemen met de toepassing en voert u de overige instructies hieronder uit. 

Application Insights Snapshot Debugger wordt vooraf geïnstalleerd als onderdeel van de App Services runtime, maar u moet deze inschakelen om moment opnamen voor uw App Service-app op te halen. Nadat u een app hebt geïmplementeerd, moet u, zelfs als u de SDK van Application Insights in de bron code hebt opgenomen, de volgende stappen volgen om de fout opsporing voor moment opnamen in te scha kelen.

1. Ga naar het onderdeel Azure van het configuratie scherm voor uw App Service.
2. Ga naar de pagina **instellingen > Application Insights** .

   ![App Insights inschakelen op App Services portal](./media/snapshot-debugger/applicationinsights-appservices.png)

3. Volg de instructies op de pagina om een nieuwe resource te maken of selecteer een bestaande app Insights-resource om uw app te controleren. Zorg er ook voor dat beide Schakel opties voor Snapshot Debugger zijn **ingeschakeld**.

   ![App Insights-site-uitbrei ding toevoegen][Enablement UI]

4. Snapshot Debugger is nu ingeschakeld met behulp van een App Services app-instelling.

    ![App-instelling voor Snapshot Debugger][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>Snapshot Debugger uitschakelen

Volg dezelfde stappen als voor **inschakelen snapshot debugger**, maar schakel beide Schakel opties voor Snapshot debugger in **uit**.
We raden u aan Snapshot Debugger in al uw apps in te scha kelen om de diagnose van toepassings uitzonderingen te vereenvoudigen.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

Voor een Azure App Service kunt u de app-instellingen in een Azure Resource Manager sjabloon instellen om Snapshot Debugger en Profiler in te scha kelen. U voegt een configuratie bron toe die de app-instellingen bevat als een onderliggende bron van de website:

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>Volgende stappen

- Verkeer genereren voor uw toepassing die een uitzonde ring kan veroorzaken. Wacht vervolgens 10 tot 15 minuten totdat moment opnamen naar het Application Insights-exemplaar worden verzonden.
- Zie [moment opnamen](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) in de Azure Portal.
- Zie [Snapshot debugger Troubleshooting](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)(Engelstalig) voor hulp bij het oplossen van problemen met snapshot debugger.

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

