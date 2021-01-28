---
title: Live Azure App Service-apps profileren met Application Insights | Microsoft Docs
description: Profileer Live apps op Azure App Service met Application Insights Profiler.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: b1158a614da9ba32f628aba5dd2ed2cc71b4b455
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947033"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Live Azure App Service-apps met Application Insights profiel

U kunt Profiler uitvoeren op ASP.NET en ASP.NET Core apps die worden uitgevoerd op Azure App Service met de Basic-servicelaag of hoger. Het inschakelen van Profiler op Linux is momenteel alleen mogelijk via [deze methode](profiler-aspnetcore-linux.md).

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> Profiler voor uw app inschakelen
Als u Profiler voor een app wilt inschakelen, volgt u de onderstaande instructies. Als u een ander type Azure-service gebruikt, zijn hier instructies voor het inschakelen van Profiler op andere ondersteunde platforms:
* [Cloudservices](./profiler-cloudservice.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Service Fabric toepassingen](./profiler-servicefabric.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Virtual Machines](./profiler-vm.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

Application Insights Profiler wordt vooraf geïnstalleerd als onderdeel van de App Services runtime. In de onderstaande stappen ziet u hoe u deze functie inschakelt voor uw App Service. Volg deze stappen, zelfs als u de app Insights-SDK in uw toepassing hebt opgenomen tijdens het bouwen.

1. Ga naar het onderdeel Azure van het configuratie scherm voor uw App Service.
1. Schakel de instelling altijd on in voor uw app service. U kunt deze instelling vinden onder **instellingen**, **configuratie** pagina (zie scherm afbeelding in de volgende stap) en klik op het tabblad **algemene instellingen** .
1. Navigeer naar **instellingen > pagina Application Insights** .

   ![App Insights inschakelen op App Services portal](./media/profiler/AppInsights-AppServices.png)

1. Volg de instructies in het deel venster om een nieuwe resource te maken of selecteer een bestaande app Insights-resource om uw app te controleren. Zorg er ook voor dat de Profiler is **ingeschakeld**. Als uw Application Insights-bron zich in een ander abonnement van uw App Service bevindt, kunt u deze pagina niet gebruiken om Application Insights te configureren. U kunt dit nog steeds hand matig doen door de benodigde app-instellingen hand matig te maken. [In de volgende sectie vindt u instructies voor het hand matig inschakelen van Profiler.](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![App Insights-site-uitbrei ding toevoegen][Enablement UI]

1. Profiler is nu ingeschakeld met een App Services app-instelling.

    ![App-instelling voor Profiler][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>Profiler hand matig of met Azure Resource Manager inschakelen
Application Insights Profiler kan worden ingeschakeld door app-instellingen voor uw Azure App Service te maken. De pagina met de hierboven weer gegeven opties maakt deze app-instellingen voor u. Maar u kunt het maken van deze instellingen automatiseren met een sjabloon of op een andere manier. Deze instellingen werken ook als uw Application Insights-resource zich in een ander abonnement bevindt dan uw Azure App Service.
Dit zijn de instellingen die nodig zijn om de Profiler in te scha kelen:

|App-instelling    | Waarde    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | iKey voor uw Application Insights-resource    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


U kunt deze waarden instellen met behulp van [Azure Resource Manager sjablonen](./azure-web-apps.md#app-service-application-settings-with-azure-resource-manager), [Azure POWERSHELL](/powershell/module/az.websites/set-azwebapp),  [Azure cli](/cli/azure/webapp/config/appsettings).

### <a name="enabling-profiler-for-other-clouds-manually"></a>Profiler hand matig inschakelen voor andere Clouds

Als u de Profiler voor andere Clouds wilt inschakelen, kunt u de onderstaande app-instellingen gebruiken.

|App-instelling    | Amerikaanse overheids waarden| Cloud in China |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://profiler.monitor.azure.us`    | `https://profiler.monitor.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>Profiler uitschakelen

Als u Profiler wilt stoppen of opnieuw wilt opstarten voor een exemplaar van een afzonderlijke app, onder **webjobs** en stop de Webtaak met de naam ApplicationInsightsProfiler3. Zelfs als de Profiler is uitgeschakeld met behulp van de schakel optie op de pagina Application Insights zoals hierboven wordt beschreven, wordt het profilerings proces nog steeds uitgevoerd. Er wordt gecontroleerd of de Profiler is ingeschakeld. Als de optie is uitgeschakeld, wordt deze gedurende een periode in de slaap stand gezet voordat deze opnieuw wordt gecontroleerd. Als deze functie is uitgeschakeld, wordt deze niet door een profile ring uitgevoerd. Als u deze Webtaak uitschakelt, wordt het profilerings proces helemaal niet uitgevoerd, zelfs om te controleren of deze is ingeschakeld.

  ![Profiler voor een Webtaak uitschakelen][disable-profiler-webjob]

U wordt aangeraden profilering in te scha kelen voor al uw apps om prestatie problemen zo snel mogelijk op te sporen.

Bestanden van Profiler kunnen worden verwijderd wanneer u Web implementeren gebruikt om wijzigingen in uw webtoepassing te implementeren. U kunt voor komen dat de verwijdering wordt verwijderd door de map App_Data uit te sluiten tijdens de implementatie. 


## <a name="next-steps"></a>Volgende stappen

* [Met Application Insights werken in Visual Studio](./visual-studio.md)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png

