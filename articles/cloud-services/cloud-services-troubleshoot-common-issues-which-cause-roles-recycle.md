---
title: Veelvoorkomende oorzaken voor het recyclen van Cloud service (klassiek) rollen | Microsoft Docs
description: Een Cloud service functie die plotseling kan worden gerecycled, kan aanzienlijke uitval tijd veroorzaken. Hier volgen enkele veelvoorkomende problemen die ertoe leiden dat rollen worden gerecycled, zodat u downtime kunt verlagen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 9610b32207f8367b9415c0881e49b54e24c49ad7
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741159"
---
# <a name="common-issues-that-cause-azure-cloud-service-classic-roles-to-recycle"></a>Veelvoorkomende problemen die ervoor zorgen dat de rollen van de Azure-Cloud service (klassiek) worden gerecycled

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In dit artikel worden enkele veelvoorkomende oorzaken van implementatie problemen beschreven en tips voor het oplossen van problemen die u kunnen helpen bij het oplossen van deze problemen. Een indicatie dat er een probleem is met een toepassing is wanneer de rolinstantie niet kan worden gestart, of wordt gerecycled tussen de status initialisatie, bezet en gestopt.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Ontbrekende runtime-afhankelijkheden
Als een rol in uw toepassing afhankelijk is van een assembly die geen deel uitmaakt van de .NET Framework of de door Azure beheerde bibliotheek, moet u deze assembly expliciet opnemen in het toepassings pakket. Denk eraan dat andere micro soft Frameworks niet standaard beschikbaar zijn in Azure. Als uw rol afhankelijk is van een dergelijk Framework, moet u deze assembly's toevoegen aan het toepassings pakket.

Controleer het volgende voordat u uw toepassing bouwt en inpakt:

* Als u Visual Studio gebruikt, moet u ervoor zorgen dat de **lokale eigenschap Copy** is ingesteld op **True** voor elke assembly waarnaar wordt verwezen in uw project die geen deel uitmaakt van de Azure SDK of de .NET Framework.
* Zorg ervoor dat het web.config bestand niet verwijst naar ongebruikte assembly's in het compilatie-element.
* De **Build-actie** van elk. cshtml-bestand is ingesteld op **inhoud**. Dit zorgt ervoor dat de bestanden correct worden weer gegeven in het pakket en dat andere bestanden waarnaar wordt verwezen, kunnen worden weer gegeven in het pakket.

## <a name="assembly-targets-wrong-platform"></a>Assembly doelen onjuist platform
Azure is een 64-bits omgeving. Daarom werken .NET-assembly's die zijn gecompileerd voor een 32-bits doel niet in Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>De rol genereert niet-verwerkte uitzonde ringen tijdens het initialiseren of stoppen
Uitzonde ringen die worden veroorzaakt door de methoden van de klasse [RoleEntryPoint] , die de methoden [onstart], [OnStop]en [Run] bevatten, zijn niet-verwerkte uitzonde ringen. Als er een onverwerkte uitzonde ring optreedt in een van deze methoden, wordt de rol opnieuw gerecycled. Als de rol herhaaldelijk wordt gerecycled, wordt er een onverwerkte uitzonde ring gegenereerd telkens wanneer deze wordt gestart.

## <a name="role-returns-from-run-method"></a>Rol wordt geretourneerd vanuit run-methode
De methode [Run] is bedoeld om voor onbepaalde tijd te worden uitgevoerd. Als uw code de methode [Run] overschrijft, moet de slaap stand voor onbepaalde tijd worden ingeschakeld. Als de [Run] -methode retourneert, wordt de rol gerecycled.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Onjuiste DiagnosticsConnectionString-instelling
Als de toepassing Azure Diagnostics gebruikt, moet uw service configuratie bestand de `DiagnosticsConnectionString` configuratie-instelling opgeven. Met deze instelling moet een HTTPS-verbinding met uw opslag account in Azure worden opgegeven.

Controleer het volgende om ervoor te zorgen dat uw `DiagnosticsConnectionString` instelling juist is voordat u het toepassings pakket implementeert in Azure:  

* De `DiagnosticsConnectionString` instelling wijst naar een geldig opslag account in Azure.  
  Deze instelling verwijst standaard naar het geëmuleerde opslag account, dus u moet deze instelling expliciet wijzigen voordat u het toepassings pakket implementeert. Als u deze instelling niet wijzigt, wordt er een uitzonde ring gegenereerd wanneer de rolinstantie de diagnostische monitor probeert te starten. Dit kan ertoe leiden dat de rolinstantie voor onbepaalde tijd wordt gerecycled.
* De connection string is opgegeven in de volgende [indeling](../storage/common/storage-configure-connection-string.md). (Het protocol moet worden opgegeven als HTTPS.) Vervang *MyAccountName* door de naam van uw opslag account en *MyAccountKey* met uw toegangs sleutel:    

```console
DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey
```

  Als u uw toepassing ontwikkelt met behulp van Azure-Hulpprogram Ma's voor micro soft Visual Studio, kunt u de eigenschappen pagina's gebruiken om deze waarde in te stellen.

## <a name="exported-certificate-does-not-include-private-key"></a>Het geëxporteerde certificaat bevat geen persoonlijke sleutel
Als u een webrole onder TLS wilt uitvoeren, moet u ervoor zorgen dat het geëxporteerde beheer certificaat de persoonlijke sleutel bevat. Als u *Windows certificaat beheer* gebruikt om het certificaat te exporteren, moet u **Ja** selecteren voor de optie **de persoonlijke sleutel exporteren** . Het certificaat moet worden geëxporteerd in de PFX-indeling. Dit is de enige indeling die momenteel wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen
Bekijk meer [artikelen over probleem oplossing](../index.yml?product=cloud-services&tag=top-support-issue) voor Cloud Services.

Bekijk meer scenario's voor het recyclen van rollen in [de blog serie van Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

[RoleEntryPoint]: /previous-versions/azure/reference/ee758619(v=azure.100)
[OnStart]: /previous-versions/azure/reference/ee772851(v=azure.100)
[OnStop]: /previous-versions/azure/reference/ee772844(v=azure.100)
[Uitvoeren]: /previous-versions/azure/reference/ee772746(v=azure.100)