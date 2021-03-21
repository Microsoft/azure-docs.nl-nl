---
title: Upgrade van toepassing Service Fabric
description: Dit artikel bevat een inleiding tot het upgraden van een Service Fabric-toepassing, inclusief het kiezen van upgrade modi en het uitvoeren van status controles.
ms.topic: conceptual
ms.date: 8/5/2020
ms.openlocfilehash: f3fad8d0ede92004706d9a1f4e14353715361b63
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98792011"
---
# <a name="service-fabric-application-upgrade"></a>Upgrade van toepassing Service Fabric
Een Azure Service Fabric-toepassing is een verzameling van services. Tijdens een upgrade vergelijkt Service Fabric het nieuwe [toepassings manifest](service-fabric-application-and-service-manifests.md) met de vorige versie en bepaalt u welke services in de toepassing moeten worden bijgewerkt. Service Fabric vergelijkt de versie nummers in de service manifesten met de versie nummers in de vorige versie. Als een service niet is gewijzigd, wordt die service niet geüpgraded.

> [!NOTE]
> [ApplicationParameter](/dotnet/api/system.fabric.description.applicationdescription.applicationparameters#System_Fabric_Description_ApplicationDescription_ApplicationParameters)s blijven niet behouden in een toepassings upgrade. Als u de huidige toepassings parameters wilt behouden, moet de gebruiker eerst de para meters ophalen en deze door geven aan de aanroep van de upgrade-API zoals hieronder wordt weer gegeven:
```powershell
$myApplication = Get-ServiceFabricApplication -ApplicationName fabric:/myApplication
$appParamCollection = $myApplication.ApplicationParameters

$applicationParameterMap = @{}
foreach ($pair in $appParamCollection)
{
    $applicationParameterMap.Add($pair.Name, $pair.Value);
}

Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/myApplication -ApplicationTypeVersion 2.0.0 -ApplicationParameter $applicationParameterMap -Monitored -FailureAction Rollback
```

## <a name="rolling-upgrades-overview"></a>Overzicht van rolling upgrades
Bij een upgrade van een rolling toepassing wordt de upgrade in fasen uitgevoerd. In elke fase wordt de upgrade toegepast op een subset knoop punten in het cluster, een update domein genoemd. Als gevolg hiervan blijft de toepassing beschikbaar tijdens de upgrade. Tijdens de upgrade kan het cluster een combi natie van de oude en nieuwe versies bevatten.

Daarom moeten de twee versies vooruit en achterwaarts compatibel zijn. Als ze niet compatibel zijn, is de toepassings beheerder verantwoordelijk voor het klaarzetten van een upgrade voor meerdere fasen om de beschik baarheid te behouden. Bij een upgrade met meerdere fasen wordt de eerste stap bijgewerkt naar een tussenliggende versie van de toepassing die compatibel is met de vorige versie. De tweede stap is het bijwerken van de definitieve versie die de compatibiliteit met de pre-Update versie verbreekt, maar die compatibel is met de tussenliggende versie.

Update domeinen worden in het cluster manifest opgegeven wanneer u het cluster configureert. Update domeinen ontvangen geen updates in een bepaalde volg orde. Een update domein is een logische implementatie-eenheid voor een toepassing. Met update domeinen kunnen de services tijdens een upgrade hoge Beschik baarheid blijven.

Niet-rolling upgrades zijn mogelijk als de upgrade wordt toegepast op alle knoop punten in het cluster. Dit is het geval wanneer de toepassing slechts één update domein heeft. Deze methode wordt niet aanbevolen omdat de service uitvalt en niet beschikbaar is op het moment van de upgrade. Daarnaast biedt Azure geen garanties wanneer een cluster met slechts één update domein is ingesteld.

Nadat de upgrade is voltooid, blijven alle services en replica's (instanties) in dezelfde versie-i. e staan. als de upgrade is geslaagd, worden deze bijgewerkt naar de nieuwe versie. Als de upgrade mislukt en wordt teruggedraaid, worden ze teruggezet naar de oude versie.

## <a name="health-checks-during-upgrades"></a>Status controles tijdens upgrades
Voor een upgrade moet het status beleid worden ingesteld (of de standaard waarden kunnen worden gebruikt). Een upgrade wordt uitgevoerd wanneer alle update domeinen binnen de opgegeven time-outs worden bijgewerkt en wanneer alle update domeinen in orde worden beschouwd.  Een goed bijgewerkt update domein houdt in dat het update domein alle status controles heeft door gegeven die zijn opgegeven in het status beleid. Een status beleid kan bijvoorbeeld verplicht zijn om alle services binnen een toepassings exemplaar in *orde* te maken, omdat de status wordt bepaald door service Fabric.

Status beleid en controles tijdens de upgrade van Service Fabric zijn service-en toepassings neutraal. Dat wil zeggen dat er geen servicespecifieke tests worden uitgevoerd.  Uw service kan bijvoorbeeld een doorvoer vereiste hebben, maar Service Fabric heeft niet de informatie om de door voer te controleren. Raadpleeg de [status artikelen](service-fabric-health-introduction.md) voor de controles die worden uitgevoerd. De controles die tijdens een upgrade plaatsvinden, bevatten tests om te controleren of het toepassings pakket correct is gekopieerd, of het exemplaar is gestart, enzovoort.

De status van de toepassing is een aggregatie van de onderliggende entiteiten van de toepassing. Kortom, Service Fabric evalueert de status van de toepassing met behulp van de status die op de toepassing wordt gerapporteerd. Ook wordt de status van alle services voor de toepassing geëvalueerd op deze manier. Service Fabric evalueert de status van de toepassings services verder door de status van hun onderliggende items, zoals de service replica, samen te voegen. Zodra aan het toepassings status beleid is voldaan, kan de upgrade door gaan. Als het status beleid wordt geschonden, mislukt de upgrade van de toepassing.

## <a name="upgrade-modes"></a>Upgrade modi
De modus die wordt aanbevolen voor de upgrade van de toepassing is de bewaakte modus. Dit is de veelgebruikte modus. In de bewaakte modus wordt de upgrade uitgevoerd op één update domein, en als alle status controles (per beleid opgegeven) automatisch naar het volgende update domein worden verplaatst.  Als de status controles mislukken en/of time-outs zijn bereikt, wordt de upgrade teruggedraaid voor het update domein of wordt de modus gewijzigd in niet-bewaakt hand matig. U kunt de upgrade configureren om een van deze twee modi te kiezen voor mislukte upgrades. 

In de niet-bewaakte hand matige modus moet hand matige interventie worden uitgevoerd na elke upgrade op een update domein, om de upgrade uit te kunnen laten op het volgende update domein. Er worden geen Service Fabric status controles uitgevoerd. De beheerder voert de status controles uit voordat de upgrade wordt gestart in het volgende update domein.

## <a name="upgrade-default-services"></a>Standaard Services bijwerken
Sommige standaard service parameters die in het [manifest](service-fabric-application-and-service-manifests.md) van de toepassing zijn gedefinieerd, kunnen ook worden bijgewerkt als onderdeel van een toepassings upgrade. Alleen de service parameters die worden gewijzigd via [Update-ServiceFabricService](/powershell/module/servicefabric/update-servicefabricservice) kunnen worden gewijzigd als onderdeel van een upgrade. Het gedrag van het wijzigen van standaard services tijdens de upgrade van de toepassing is als volgt:

1. Standaard services in het nieuwe toepassings manifest die nog niet in het cluster bestaan, worden gemaakt.
2. Standaard-services die voor komen in zowel de vorige als de nieuwe toepassings manifesten, worden bijgewerkt. De para meters van de standaard service in het manifest van de nieuwe toepassing overschrijven de para meters van de bestaande service. De upgrade van de toepassing wordt automatisch ongedaan gemaakt als het bijwerken van een standaard service mislukt.
3. Standaard services die niet in het nieuwe toepassings manifest bestaan, worden verwijderd als deze in het cluster aanwezig zijn. **Houd er rekening mee dat het verwijderen van een standaard service ertoe leidt dat de status van de service wordt verwijderd en niet ongedaan kan worden gemaakt.**

Wanneer een upgrade van een toepassing wordt teruggedraaid, worden de standaard service parameters teruggezet naar hun oude waarden voordat de upgrade werd gestart, maar verwijderde Services kunnen niet opnieuw worden gemaakt met de oude status.

> [!TIP]
> De [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) -cluster configuratie-instelling moet *waar* zijn om regels 2 en 3) hierboven in te scha kelen (standaard service bijwerken en verwijderen). Deze functie wordt ondersteund vanaf Service Fabric versie 5,5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Meerdere toepassingen met HTTPS-eind punten upgraden
U moet ervoor zorgen dat u niet **dezelfde poort** gebruikt voor verschillende exemplaren van dezelfde toepassing wanneer u http **S** gebruikt. De reden hiervoor is dat Service Fabric het certificaat niet kan upgraden voor een van de toepassings exemplaren. Als bijvoorbeeld toepassing 1 of toepassing 2 beide zijn certificaat 1 wilt upgraden naar certificaat 2. Wanneer de upgrade wordt uitgevoerd, heeft Service Fabric mogelijk de registratie van certificaat 1 verwijderd met http.sys, zelfs al wordt de andere toepassing nog steeds gebruikt. Om dit te voor komen, Service Fabric detecteert dat er al een ander toepassings exemplaar is geregistreerd op de poort met het certificaat (vanwege http.sys) en de bewerking niet kan worden uitgevoerd.

Daarom biedt Service Fabric geen ondersteuning voor het upgraden van twee verschillende services met **dezelfde poort** in verschillende exemplaren van de toepassing. Met andere woorden, u kunt niet hetzelfde certificaat gebruiken op verschillende services op dezelfde poort. Als u een gedeeld certificaat op dezelfde poort moet hebben, moet u ervoor zorgen dat de services op verschillende computers worden geplaatst met plaatsings beperkingen. Of overweeg Service Fabric dynamische poorten, indien mogelijk, te gebruiken voor elke service in elk toepassings exemplaar. 

Als een upgrade mislukt met https, wordt een fout melding weer gegeven dat de Windows HTTP-Server-API niet meerdere certificaten ondersteunt voor toepassingen die een poort delen.

## <a name="application-upgrade-flowchart"></a>Stroom diagram voor toepassings upgrades
Het stroom diagram dat volgt op deze alinea, kan u helpen inzicht te krijgen in het upgrade proces van een Service Fabric-toepassing. Met name wordt met de stroom beschreven hoe de time-outs, waaronder *HealthCheckStableDuration*, *HealthCheckRetryTimeout* en *UpgradeHealthCheckInterval*, kunnen worden beheerd wanneer de upgrade in één update domein als geslaagd of mislukt wordt beschouwd.

![Het upgrade proces voor een Service Fabric-toepassing][image]

## <a name="next-steps"></a>Volgende stappen
Als u een [upgrade uitvoert van uw toepassing met behulp van Visual Studio](service-fabric-application-upgrade-tutorial.md) , wordt u begeleid bij een toepassings upgrade met Visual Studio.

Als u uw toepassing bijwerkt [met Power shell](service-fabric-application-upgrade-tutorial-powershell.md) , kunt u een toepassings upgrade uitvoeren met behulp van Power shell.

Bepalen hoe uw toepassing wordt bijgewerkt met behulp van [upgrade parameters](service-fabric-application-upgrade-parameters.md).

Maak uw toepassings upgrades compatibel door te leren hoe u [gegevens serialisatie](service-fabric-application-upgrade-data-serialization.md)gebruikt.

Meer informatie over het gebruik van geavanceerde functionaliteit bij het upgraden van uw toepassing door te verwijzen naar [Geavanceerde onderwerpen](service-fabric-application-upgrade-advanced.md).

Corrigeer veelvoorkomende problemen in toepassings upgrades door te verwijzen naar de stappen in [Troubleshooting Application upgrades](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
