---
title: Implementatie-Api's voor Azure Monitor metrische gegevens en automatisch schalen buiten gebruik stellen
description: Metrische gegevens en klassieke Api's automatisch schalen, ook wel Azure Service Management (ASM) of het implementatie model van RDFE buiten gebruik gesteld
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: a8900d238e7542864e3dc53f9d192e579e2c96d1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536690"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor buiten gebruik stellen van de Api's van het klassieke implementatie model voor metrische gegevens en automatisch schalen

Azure Monitor (voorheen Azure Insights wanneer het eerst werd uitgebracht) biedt momenteel de mogelijkheid om instellingen voor automatisch schalen te maken en te beheren voor de klassieke Vm's en klassieke Cloud Services. De oorspronkelijke set klassieke implementatie model-Api's worden **na 30 juni 2019** in alle open bare en particuliere Clouds van Azure in alle regio's buiten gebruik gesteld.   

Dezelfde bewerkingen worden gedurende een jaar ondersteund via een reeks Azure Resource Manager-Api's. De Azure Portal gebruikt de nieuwe REST Api's voor automatisch schalen en metrische gegevens. Er is ook een nieuwe SDK, Power shell en CLI beschikbaar op basis van deze resource manager-Api's. Onze partner services voor bewaking gebruiken de nieuwe op Resource Manager gebaseerde REST-Api's in Azure Monitor.  

## <a name="who-is-not-affected"></a>Wie wordt niet beïnvloed

Als u automatisch schalen beheert via de Azure Portal, de [nieuwe Azure monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), Power shell, CLI of Resource Manager-sjablonen, is geen actie nodig.  

Als u metrische gegevens gebruikt via de Azure Portal of via verschillende [bewakings partner services](./partners.md), is er geen actie vereist. Micro soft werkt samen met het controleren van partners om te migreren naar de nieuwe Api's.

## <a name="who-is-affected"></a>Die worden beïnvloed

Dit artikel is van toepassing op het gebruik van de volgende onderdelen:

- **Klassieke Azure Insights-SDK** : als u de [klassieke Azure Insights-SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)gebruikt, schakelt u over naar de nieuwe Azure monitor-SDK voor [.net](https://github.com/azure/azure-libraries-for-net#download) of [Java](https://github.com/azure/azure-libraries-for-java#download). Down load het [Azure monitor SDK NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klassiek automatisch schalen** : als u de [klassieke instellingen voor automatisch schalen](/previous-versions/azure/reference/mt348562(v=azure.100)) aanroept vanuit uw aangepaste hulpprogram ma's of met behulp van de [klassieke Azure Insights-SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), moet u overschakelen naar het [Azure monitor rest API van Resource Manager](/rest/api/monitor/autoscalesettings).

- **Klassieke metrische gegevens** : als u metrische gegevens gebruikt met behulp van de [klassieke rest-api's](/previous-versions/azure/reference/dn510374(v=azure.100)) of de  [klassieke Azure Insights-SDK](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) van aangepaste hulpprogram ma's, moet u overschakelen naar het Azure monitor van [Resource Manager rest API](/rest/api/monitor/autoscalesettings). 

Als u niet zeker weet of uw code of aangepaste hulp middelen de klassieke Api's aanroepen, bekijkt u het volgende:

- Controleer de URI waarnaar wordt verwezen in de code of het hulp programma. De klassieke Api's gebruiken de URI https://management.core.windows.net . U moet de nieuwere URI gebruiken voor de op Resource Manager gebaseerde Api's, te beginnen met `https://management.azure.com/` .

- Vergelijk de naam van de assembly op uw computer. De oudere klassieke assembly bevindt zich op  https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/ .

- Als u verificatie via certificaat gebruikt voor toegang tot metrische gegevens of Api's voor automatisch schalen, gebruikt u een klassiek eind punt en een bibliotheek. De nieuwere Resource Manager-Api's vereisen Azure Active Directory authenticatie via een service-principal of User Principal.

- Als u met behulp van aanroepen in de documentatie naar een van de volgende koppelingen verwijst, gebruikt u de oudere klassieke Api's.

  - [Klassen bibliotheek van Windows. Azure. Management. monitoring](/previous-versions/azure/dn510414(v=azure.100))

  - [Bewaking (klassiek) .NET](/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [IMetricOperations-interface](/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Waarom u moet overschakelen

De bestaande mogelijkheden voor automatisch schalen en metrische gegevens blijven werken via de nieuwe Api's.  

Migratie naar nieuwere Api's wordt geleverd met mogelijkheden op basis van Resource Manager, zoals ondersteuning voor consistent Azure-op rollen gebaseerd toegangs beheer (Azure RBAC) in al uw bewakings Services. U krijgt ook extra functionaliteit voor metrische gegevens: 

- ondersteuning voor dimensies
- consistente nauw keurigheid van metrische gegevens over 1 minuut voor alle services 
- betere query's
- hogere gegevens retentie (93 dagen met metrische cijfers versus 30 dagen) 

Over het algemeen, zoals alle andere services in azure, worden de prestaties, schaal baarheid en betrouw baarheid van de op Resource Manager gebaseerde Azure Monitor Api's verbeterd. 

## <a name="what-happens-if-you-do-not-migrate"></a>Wat gebeurt er als u niet migreert

### <a name="before-retirement"></a>Voor buiten gebruik stellen

Er zijn geen directe gevolgen voor uw Azure-Services of hun werk belastingen.  

### <a name="after-retirement"></a>Na pensionering

Aanroepen naar de klassieke Api's die eerder worden vermeld, mislukken en retour neren fout berichten die vergelijkbaar zijn met de volgende:

Voor automatisch schalen: *deze API is afgeschaft. Gebruik de sjablonen Azure Portal, Azure Monitor SDK, Power shell, CLI of Resource Manager voor het beheren van instellingen voor automatisch schalen*.  

Voor metrische gegevens: *deze API is afgeschaft. Gebruik de Azure Portal, Azure Monitor SDK, Power shell, CLI voor het opvragen van metrische gegevens*.

## <a name="email-notifications"></a>E-mailmeldingen

Er is een pensioen melding verzonden naar e-mail adressen voor de volgende account rollen: 

- Account-en service beheerders
- Cobeheerders  

Als u vragen hebt, kunt u contact met ons opnemen via MonitorClassicAPIhelp@microsoft.com .  

## <a name="references"></a>Referenties

- [Nieuwere REST-Api's voor Azure Monitor](/rest/api/monitor/) 
- [Nieuwere Azure Monitor-SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)

