---
title: Levenscyclus gebeurtenissen van Cloud service (klassiek) verwerken | Microsoft Docs
description: Meer informatie over het gebruik van de levenscyclus methoden van een Cloud serviceprovider in .NET, waaronder RoleEntryPoint, waarmee methoden kunnen worden gereageerd op levenscyclus gebeurtenissen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b5aa4bd061647f63ebcc70109f0ba21b39e814cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741329"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>De levens cyclus van een web-of worker-rol in .NET aanpassen

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Wanneer u een worker-rol maakt, breidt u de klasse [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) uit. deze biedt methoden die u kunt opheffen, waarmee u kunt reageren op levenscyclus gebeurtenissen. Voor webrollen is deze klasse optioneel, dus u moet deze gebruiken om te reageren op levenscyclus gebeurtenissen.

## <a name="extend-the-roleentrypoint-class"></a>De klasse RoleEntryPoint uitbreiden
De klasse [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) bevat methoden die worden aangeroepen door Azure bij het **starten**, **uitvoeren** of **stoppen** van een web-of worker-rol. U kunt deze methoden eventueel overschrijven om de functie-initialisatie, de afsluitings volgorde van rollen of de uitvoerings thread van de rol te beheren. 

Wanneer u **RoleEntryPoint** uitbreidt, moet u rekening houden met het volgende gedrag van de methoden:

* De methode [ONSTART](/previous-versions/azure/reference/ee772851(v=azure.100)) retourneert een Booleaanse waarde, waardoor het mogelijk is dat **False** wordt geretourneerd van deze methode.
  
   Als uw code **Onwaar** retourneert, wordt het Role-proces abrupt beëindigd, zonder dat er een afsluit procedure wordt uitgevoerd die u mogelijk hebt. Over het algemeen moet u voor komen dat **Onwaar** wordt geretourneerd van de methode **onstart** .
* Een niet-onderschepte uitzonde ring in een overbelasting van een **RoleEntryPoint** -methode wordt behandeld als een onverwerkte uitzonde ring.
  
   Als er een uitzonde ring optreedt in een van de levenscyclus methoden, wordt de [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) -gebeurtenis door Azure verhoogd, waarna het proces wordt beëindigd. Nadat uw rol offline is gehaald, wordt deze opnieuw gestart door Azure. Wanneer er een onverwerkte uitzonde ring optreedt, wordt de gebeurtenis [stoppen](/previous-versions/azure/reference/ee758136(v=azure.100)) niet geactiveerd en wordt de methode **OnStop** niet aangeroepen.

Als uw rol niet kan worden gestart, of als deze wordt gerecycled tussen de status initialisatie, bezet en gestopt, kan de code een onverwerkte uitzonde ring veroorzaken binnen een van de levenscyclus gebeurtenissen telkens wanneer de rol opnieuw wordt gestart. In dit geval gebruikt u de gebeurtenis [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) om de oorzaak van de uitzonde ring te bepalen en deze op de juiste manier te verwerken. Uw rol kan ook worden geretourneerd uit de methode [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) , waardoor de rol opnieuw wordt opgestart. Zie [algemene problemen waardoor rollen kunnen worden gerecycled](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)voor meer informatie over de implementatie status.

> [!NOTE]
> Als u de Azure- **Hulpprogram ma's voor micro soft Visual Studio** gebruikt voor het ontwikkelen van uw toepassing, breidt de Project sjablonen voor rollen automatisch de klasse **RoleEntryPoint** uit voor u, in de bestanden *webrole. cs* en *WorkerRole. cs* .
> 
> 

## <a name="onstart-method"></a>Onstart-methode
De methode **ONSTART** wordt aangeroepen wanneer uw rolinstantie online wordt gebracht door Azure. Terwijl de onstart code wordt uitgevoerd, wordt de rolinstantie gemarkeerd als **bezet** en wordt er geen extern verkeer naar het proces geleid door de Load Balancer. U kunt deze methode onderdrukken om initialisatie werkzaamheden uit te voeren, zoals het implementeren van gebeurtenis-handlers en het starten van [Azure Diagnostics](cloud-services-how-to-monitor.md).

Als **ONSTART** **True** retourneert, wordt het exemplaar geïnitialiseerd en roept Azure de methode **RoleEntryPoint. run** aan. Als **ONSTART** **Onwaar** retourneert, wordt de rol onmiddellijk beëindigd zonder dat er geplande afsluit reeksen worden uitgevoerd.

In het volgende code voorbeeld ziet u hoe u de methode **ONSTART** overschrijft. Met deze methode wordt een diagnostische monitor geconfigureerd en gestart wanneer de rolinstantie wordt gestart en een overdracht van logboek gegevens naar een opslag account wordt ingesteld:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Methode OnStop
De methode **OnStop** wordt aangeroepen nadat een Rolinstantie door Azure offline is gehaald en voordat het proces wordt afgesloten. U kunt deze methode onderdrukken om code aan te roepen die vereist is voor het correct afsluiten van uw rolinstantie.

> [!IMPORTANT]
> De code die wordt uitgevoerd in de **OnStop** -methode heeft een beperkte tijd om te worden voltooid wanneer deze wordt aangeroepen om andere redenen dan een door de gebruiker geïnitieerde afsluiting. Nadat deze tijd is verstreken, wordt het proces beëindigd. u moet er dus voor zorgen dat de code in de methode **OnStop** snel kan worden uitgevoerd of niet wordt uitgevoerd om te worden voltooid. De methode **OnStop** wordt aangeroepen nadat de gebeurtenis **stoppen** is gegenereerd.
> 
> 

## <a name="run-method"></a>Run-methode
U kunt de methode **Run** overschrijven om een langlopende thread voor uw rolinstantie te implementeren.

Het is niet nodig om de methode **Run** te overschrijven. met de standaard implementatie wordt een thread gestart die permanent in slaap stand wordt gezet. Als u de methode **Run** overschrijft, moet uw code voor onbepaalde tijd worden geblokkeerd. Als de methode **Run** wordt geretourneerd, wordt de rol automatisch zonder problemen gerecycled. met andere woorden: Azure verhoogt de gebeurtenis **stoppen** en roept de methode **OnStop** aan zodat uw afsluit sequenties kunnen worden uitgevoerd voordat de rol offline wordt genomen.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>De ASP.NET levenscyclus methoden voor een webrole implementeren
U kunt de ASP.NET levenscyclus methoden, naast die van de klasse **RoleEntryPoint** , gebruiken voor het beheren van initialisatie-en afsluit reeksen voor een webrole. Dit kan handig zijn voor compatibiliteits doeleinden als u een bestaande ASP.NET-toepassing naar Azure wilt overbrengen. De ASP.NET levenscyclus methoden worden aangeroepen vanuit de **RoleEntryPoint** -methoden. De **\_ Start** methode van de toepassing wordt aangeroepen nadat de methode **RoleEntryPoint. onstart** is voltooid. De **\_ End** -methode van de toepassing wordt aangeroepen voordat de methode **RoleEntryPoint. OnStop** wordt aangeroepen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [maken van een Cloud service pakket](cloud-services-model-and-package.md).




