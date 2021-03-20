---
title: Een Azure-Cloud service (klassiek) bewaken | Microsoft Docs
description: Hierin wordt beschreven welke bewaking een Azure-Cloud service omvat en wat uw opties zijn.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 60f320f86860cca482cdf25c7d93f84dae8c4e5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743369"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Inleiding tot de bewaking van Cloud service (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

U kunt belang rijke prestatie gegevens voor elke Cloud service bewaken. Elke Cloud service functie verzamelt minimale gegevens: CPU-gebruik, netwerk gebruik en schijf gebruik. Als de Cloud service de `Microsoft.Azure.Diagnostics` uitbrei ding op een rol heeft toegepast, kan die rol aanvullende gegevens punten verzamelen. Dit artikel bevat een inleiding tot Azure Diagnostics voor Cloud Services.

Met de basis controle worden gegevens van de prestatie meter items uit rolinstanties gesampled en verzameld met een interval van 3 minuten. Deze basis bewakings gegevens worden niet opgeslagen in uw opslag account en er zijn geen extra kosten aan verbonden.

Met geavanceerde controle worden extra metrische gegevens bemonsterd en verzameld met intervallen van vijf minuten, 1 uur en 12 uur. De geaggregeerde gegevens worden opgeslagen in een opslag account, in tabellen, en worden na 10 dagen verwijderd. Het gebruikte opslag account wordt geconfigureerd door de rol; u kunt verschillende opslag accounts voor verschillende rollen gebruiken. Dit is geconfigureerd met een connection string in de [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) -en [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) -bestanden.


## <a name="basic-monitoring"></a>Basis controle

Zoals vermeld in de inleiding, verzamelt een Cloud service automatisch elementaire bewakings gegevens van de virtuele machine van de host. Deze gegevens omvatten het CPU-percentage, het netwerk in/uit en de lees-en schrijf bewerking van de schijf. De verzamelde bewakings gegevens worden automatisch weer gegeven op het overzicht en de metrische pagina's van de Cloud service in de Azure Portal. 

Voor basis bewaking is geen opslag account vereist. 

![basis tegels voor Cloud Service bewaking](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Geavanceerde controle

Geavanceerde bewaking vereist het gebruik van de **Azure Diagnostics** extensie (en eventueel de Application Insights SDK) voor de rol die u wilt bewaken. De diagnostische uitbrei ding maakt gebruik van een configuratie bestand (per rol) met de naam **Diagnostische gegevens. wadcfgx** om de bewaakte metrische gegevens van de diagnostische functies te configureren. Met de diagnostische extensie van Azure worden gegevens verzameld en opgeslagen in een Azure Storage-account. Deze instellingen worden geconfigureerd in de **. wadcfgx**-, [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)-en [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) -bestanden. Dit betekent dat er extra kosten zijn gekoppeld aan geavanceerde bewaking.

Terwijl elke rol wordt gemaakt, voegt Visual Studio de Azure Diagnostics-extensie toe aan het bestand. Deze uitbrei ding van diagnostische gegevens kan de volgende typen informatie verzamelen:

* Aangepaste prestatie meter items
* Toepassings logboeken
* Windows-gebeurtenislogboeken
* .NET-gebeurtenis bron
* IIS-logboeken
* ETW op basis van manifest
* Crashdumps
* Fouten logboeken van klanten

> [!IMPORTANT]
> Hoewel al deze gegevens worden geaggregeerd naar het opslag account, biedt de portal **geen** systeem eigen manier om de gegevens in een grafiek te plaatsen. Het is sterk aan te raden om een andere service, zoals Application Insights, te integreren in uw toepassing.

## <a name="setup-diagnostics-extension"></a>Uitbrei ding voor installatie van diagnostische gegevens

Als u geen **klassiek** opslag account hebt, [maakt u er eerst een](../storage/common/storage-account-create.md). Zorg ervoor dat het opslag account is gemaakt met het **klassieke implementatie model** dat is opgegeven.

Ga vervolgens naar de resource van het **opslag account (klassiek)** . Selecteer **instellingen**  >  **toegangs sleutels** en kopieer de **primaire Connection String** waarde. U hebt deze waarde nodig voor de Cloud service. 

Er zijn twee configuratie bestanden die u moet wijzigen om geavanceerde diagnoses in te scha kelen: **ServiceDefinition. csdef** en **ServiceConfiguration. cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition. csdef

Voeg in het bestand **ServiceDefinition. csdef** een nieuwe instelling toe met de naam `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` voor elke rol die gebruikmaakt van geavanceerde diagnoses. Visual Studio voegt deze waarde toe aan het bestand wanneer u een nieuw project maakt. Als de app ontbreekt, kunt u deze nu toevoegen. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Hiermee definieert u een nieuwe instelling die moet worden toegevoegd aan elk **ServiceConfiguration. cscfg** -bestand. 

Waarschijnlijk hebt u twee **. cscfg** -bestanden, een met de naam **ServiceConfiguration. Cloud. cscfg** voor de implementatie naar Azure, en één met de naam **ServiceConfiguration. local. cscfg** die wordt gebruikt voor lokale implementaties in de geëmuleerde omgeving. Open en wijzig elk **cscfg** -bestand. Voeg een instelling toe met de naam `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` . Stel de waarde in op de **primaire Connection String** van het klassieke opslag account. Als u de lokale opslag op uw ontwikkel computer wilt gebruiken, gebruikt u `UseDevelopmentStorage=true` .

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Application Insights gebruiken

Wanneer u de Cloud service vanuit Visual Studio publiceert, krijgt u de optie om de diagnostische gegevens naar Application Insights te verzenden. U kunt de Application Insights Azure-resource op dat moment maken of de gegevens naar een bestaande Azure-resource verzenden. Uw Cloud service kan worden bewaakt door Application Insights voor Beschik baarheid, prestaties, fouten en gebruik. Aangepaste grafieken kunnen worden toegevoegd aan Application Insights zodat u de gegevens kunt zien die het meest van belang zijn. Gegevens van rolinstantie kunnen worden verzameld met behulp van de Application Insights SDK in uw Cloud service project. Zie [Application Insights met Cloud Services](../azure-monitor/app/cloudservices.md)voor meer informatie over het integreren van Application Insights.

Hoewel u Application Insights kunt gebruiken om de prestatie meter items (en de andere instellingen) weer te geven die u via de Windows Azure Diagnostics-extensie hebt opgegeven, krijgt u een uitgebreidere ervaring door de Application Insights SDK te integreren in uw werk nemer-en webrollen.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Application Insights met Cloud Services](../azure-monitor/app/cloudservices.md)
- [Prestatie meter items instellen](diagnostics-performance-counters.md)




