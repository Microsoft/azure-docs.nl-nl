---
title: Problemen met registerprestaties oplossen
description: Symptomen, oorzaken en oplossingen voor veelvoorkomende problemen met de prestaties van een REGI ster
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148413"
---
# <a name="troubleshoot-registry-performance"></a>Problemen met registerprestaties oplossen

Dit artikel helpt u bij het oplossen van problemen die kunnen optreden met de prestaties van een Azure container Registry. 

## <a name="symptoms"></a>Symptomen

Dit kan een of meer van de volgende zijn:

* Pull-of push-installatie kopieën met de docker-CLI duurt langer dan verwacht
* De implementatie van installatie kopieën naar een service zoals Azure Kubernetes service duurt langer dan verwacht
* Het is niet mogelijk om een groot aantal gelijktijdige pull-of push bewerkingen uit te voeren op de verwachte tijd
* Pull-of push-bewerkingen in een geo-gerepliceerd REGI ster duren langer dan verwacht of push mislukt met fout `Error writing blob` of `Error writing manifest`

## <a name="causes"></a>Oorzaken

* De snelheid van de netwerk verbinding kan leiden tot een trage register bewerking- [oplossing](#check-expected-network-speed)
* De compressie of extractie van de afbeelding slaag kan traag zijn bij de client [oplossing](#check-client-hardware)  
* U bereikt een geconfigureerde limiet in de service tier of omgevings [oplossing](#review-configured-limits) van uw REGI ster
* Het geo-gerepliceerde REGI ster bevat replica's in nabijgelegen regio's- [oplossing](#configure-geo-replicated-registry)
* U bent bezig met het ophalen van een geografisch onbewerkte register replica- [oplossing](#configure-dns-for-geo-replicated-registry)

Zie [Geavanceerde probleem oplossing](#advanced-troubleshooting) en [volgende stappen](#next-steps) voor andere opties als u uw probleem hier niet kunt oplossen.

## <a name="potential-solutions"></a>Mogelijke oplossingen

### <a name="check-expected-network-speed"></a>Verwachte netwerk snelheid controleren

Controleer de upload-en download snelheid van uw Internet of gebruik een hulp programma zoals AzureSpeed om het [uploaden](https://www.azurespeed.com/Azure/Uploadß) en [downloaden](https://www.azurespeed.com/Azure/Download) van Azure Blob-opslag te testen, die als host fungeert voor de lagen van het REGI ster.

Controleer de grootte van de afbeelding met de Maxi maal ondersteunde grootte en de ondersteunde down load of upload band breedte voor de servicelaag van uw REGI ster. Als uw REGI ster zich in de Basic-of Standard-laag bevindt, kunt u een upgrade uitvoeren om de prestaties te verbeteren. 

Voor implementatie van een installatie kopie naar andere services, controleert u de regio's waar het REGI ster en het doel zich bevinden. Overweeg het REGI ster en het implementatie doel te vinden in dezelfde regio of in het netwerk om de prestaties te verbeteren.

Gerelateerde links:

* [Service lagen Azure Container Registry](container-registry-skus.md)    
* [Veelgestelde vragen over container Registry](container-registry-faq.md)
* [Prestatie-en schaalbaarheids doelen voor Azure Blob Storage](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Client-hardware controleren

Het schijf type en de CPU op de docker-client kunnen invloed hebben op de snelheid van het extra heren of comprimeren van afbeeldings lagen op de client als onderdeel van pull-of push bewerkingen. Het is bijvoorbeeld mogelijk dat laag extractie op een harde schijf langer duurt dan op een schijf met een vaste status. Vergelijkt pull-bewerkingen voor vergelijk bare installatie kopieën uit uw Azure container Registry en een openbaar REGI ster zoals docker hub.

### <a name="review-configured-limits"></a>Geconfigureerde limieten controleren

Als u gelijktijdig meerdere of veel meerdere gelaagde installatie kopieën naar uw REGI ster pusht of ophaalt, raadpleegt u de ondersteunde limieten voor ReadOps en WriteOps voor de servicelaag van het REGI ster. Als uw REGI ster zich in de Basic-of Standard-laag bevindt, kunt u een upgrade uitvoeren om de limieten te verhogen. Neem ook contact op met uw netwerk provider over netwerk beperking die kan optreden bij veel gelijktijdige bewerkingen. 

Controleer de configuratie van de docker-daemon voor het maximale aantal gelijktijdige uploads of down loads voor elke push-of pull-bewerking op de client. Configureer zo nodig hogere limieten.

Omdat elke afbeelding slaag een afzonderlijke Lees-of schrijf bewerking van het REGI ster vereist, controleert u het aantal lagen in uw afbeeldingen. Houd rekening met strategieën om het aantal afbeeldings lagen te verminderen.

Gerelateerde links:

* [Service lagen Azure Container Registry](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Geo-gerepliceerd REGI ster configureren

Een docker-client die een installatie kopie naar een geo-gerepliceerd REGI ster pusht, kan niet alle afbeeldings lagen en het bijbehorende manifest naar één gerepliceerde regio pushen. Deze situatie kan zich voordoen omdat Azure Traffic Manager de register aanvragen naar het netwerk-dichtstbijzijnde gerepliceerde REGI ster stuurt. Als het REGI ster twee regio's voor replicatie in de buurt heeft, kunnen afbeeldings lagen en het manifest naar de twee sites worden gedistribueerd en mislukt de push bewerking wanneer het manifest is gevalideerd.

Als u de DNS-omzetting naar de dichtstbijzijnde replica tijdens het pushen van installatie kopieën wilt optimaliseren, configureert u een geo-gerepliceerd REGI ster in dezelfde Azure-regio's als de bron van de push bewerkingen of de dichtstbijzijnde regio bij het werken buiten Azure.

Als u bewerkingen wilt uitvoeren met een geo-gerepliceerd REGI ster, kunt u Traffic Manager route ring ook tijdelijk uitschakelen voor een of meer replicaties.

Gerelateerde links:

* [Geo-replicatie in Azure Container Registry](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>DNS configureren voor het geo-gerepliceerde REGI ster

Als pull-bewerkingen van een geo-gerepliceerd REGI ster langzaam worden weer gegeven, kan de DNS-configuratie op de client mogelijk worden omgezet naar een geografisch ongebruikte DNS-server. In dit geval kunnen Traffic Manager routerings aanvragen verzenden naar een replica die zich in het netwerk bevindt met de DNS-server, maar niet van de client. Voer een hulp programma zoals `nslookup` of `dig` (op Linux) uit om de replica te bepalen die Traffic Manager register aanvragen naar verzendt. Bijvoorbeeld:

```console
nslookup myregistry.azurecr.io
```

Een mogelijke oplossing is het configureren van een dichter DNS-server.

Gerelateerde links:

* [Geo-replicatie in Azure Container Registry](container-registry-geo-replication.md)
* [Problemen met push bewerkingen met geo-gerepliceerde registers oplossen](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Route ring naar replicatie tijdelijk uitschakelen](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Veelgestelde vragen over Traffic Manager](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Geavanceerde probleemoplossing

Als uw machtigingen voor register bronnen zijn toegestaan, [controleert u de status van de register omgeving](container-registry-check-health.md). Als er fouten worden gerapporteerd, raadpleegt u de [fout referentie](container-registry-health-error-reference.md) voor mogelijke oplossingen.

Als het [verzamelen van bron logboeken](container-registry-diagnostics-audit-logs.md) is ingeschakeld in het REGI ster, raadpleegt u het ContainterRegistryRepositoryEvents-logboek. In dit logboek worden gegevens opgeslagen voor bewerkingen, zoals push-of pull-gebeurtenissen. Query het logboek op [mislukte bewerkingen op opslagplaats niveau](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures). 

Gerelateerde links:

* [Logboeken voor diagnostische evaluaties en controles](container-registry-diagnostics-audit-logs.md)
* [Veelgestelde vragen over container Registry](container-registry-faq.md)
* [Aanbevolen procedures voor Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Volgende stappen

Als u uw probleem hier niet kunt oplossen, raadpleegt u de volgende opties.

* Andere onderwerpen over het oplossen van problemen met het REGI ster zijn:
  * [Problemen met register aanmelding oplossen](container-registry-troubleshoot-login.md)
  * [Problemen met het netwerk oplossen met het REGI ster](container-registry-troubleshoot-access.md)
* Opties voor [Community-ondersteuning](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A](/answers/products/)
* [Een ondersteuningsticket openen](https://azure.microsoft.com/support/create-ticket/)