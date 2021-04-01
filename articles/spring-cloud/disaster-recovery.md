---
title: Geo-nood herstel voor Azure Lente in de Cloud | Microsoft Docs
description: Meer informatie over het beveiligen van uw lente-Cloud toepassing vanuit regionale uitval
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e18193b40bb7c59a21a279f451673dc7d11140e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092899"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Herstel na nood geval in de cloud van Azure lente

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

In dit artikel worden enkele strategieën beschreven die u kunt gebruiken om te voor komen dat uw Azure lente-Cloud toepassingen downtime-uitval ondervinden.  Elke regio of Data Center kan downtime ondervinden die is veroorzaakt door regionale rampen, maar een zorgvuldige planning kan invloed hebben op uw klanten.

## <a name="plan-your-application-deployment"></a>De implementatie van uw toepassing plannen

Azure lente-Cloud toepassingen worden uitgevoerd in een specifieke regio.  Azure werkt vanuit diverse geografische regio’s over de hele wereld. Een Azure-geografie is een gedefinieerd gebied van de wereld dat ten minste één Azure-regio bevat. Een Azure-regio is een gebied binnen een geografie met een of meer data centers.  Elke Azure-regio is gekoppeld aan een andere regio binnen dezelfde geografie, waarbij een regionale koppeling wordt gemaakt. Azure serializt platform updates (gepland onderhoud) over regionale paren, zodat slechts één regio per paar tegelijk wordt bijgewerkt. In het geval van een storing die betrekking heeft op meerdere regio's, wordt voor het herstel ten minste één regio in elk paar een prioriteit gegeven.

Voor hoge Beschik baarheid en bescherming tegen rampen moet u uw lente-Cloud toepassingen implementeren in meerdere regio's.  Azure biedt een lijst met [gekoppelde regio's](../best-practices-availability-paired-regions.md) , zodat u uw lente-Cloud implementaties kunt plannen voor regionale paren.  We raden u aan drie belang rijke factoren te overwegen bij het ontwerpen van uw micro service-architectuur: regionale Beschik baarheid, Azure gekoppelde regio's en beschik baarheid van de service.

*  Beschik baarheid van regio: Kies een geografisch gebied dat uw gebruikers dicht kunnen bevinden om de netwerk vertraging en de verzend tijd te minimaliseren.
*  Gekoppelde Azure-regio's: Kies gepaarde regio's binnen uw gekozen geografische gebied om ervoor te zorgen dat er gecooerdineerde platform updates en op prioriteiten gebaseerde herstel taken worden uitgevoerd.
*  Beschik baarheid van de service: Bepaal of de gekoppelde regio's warm/hot, Hot/warme of warme/koud moeten worden uitgevoerd.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager gebruiken om verkeer te routeren

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) biedt taak verdeling van verkeer op basis van DNS en kan netwerk verkeer distribueren over meerdere regio's.  Gebruik Azure Traffic Manager om klanten te leiden naar het dichtstbijzijnde Azure veer Cloud service-exemplaar.  Voor de beste prestaties en redundantie moet alle toepassings verkeer via Azure Traffic Manager worden doorgestuurd voordat het naar uw Azure lente-Cloud service wordt verzonden.

Als u Azure lente-Cloud toepassingen in meerdere regio's hebt, kunt u Azure Traffic Manager gebruiken om de stroom van verkeer naar uw toepassingen in elke regio te beheren.  Definieer een Azure Traffic Manager-eind punt voor elke service met behulp van het service-IP-adres. Klanten moeten verbinding maken met een Azure Traffic Manager DNS-naam die verwijst naar de Azure lente-Cloud service.  Met Azure Traffic Manager Load wordt verkeer gespreid over de gedefinieerde eind punten.  Als een nood geval een Data Center doorgeeft, stuurt Azure Traffic Manager verkeer van die regio naar het bijbehorende paar, waardoor de continuïteit van de service wordt gegarandeerd.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Azure-Traffic Manager maken voor Azure lente-Cloud

1. Maak een Azure lente-Cloud in twee verschillende regio's.
U hebt twee service-exemplaren van Azure lente-Cloud geïmplementeerd in twee verschillende regio's (VS-Oost en Europa-west). Start een bestaande Azure lente-Cloud toepassing met behulp van de Azure Portal om twee service-exemplaren te maken. Elk moet als primair en failover-eind punt voor verkeer fungeren. 

**Twee informatie over service-exemplaren:**

| Servicenaam | Locatie | Toepassing |
|--|--|--|
| service-voor beeld-a | VS - oost | Gateway/auth-service/account-service |
| service-voor beeld-b | Europa -west | Gateway/auth-service/account-service |

2. Aangepast domein voor service instellen Volg [aangepast domein document](spring-cloud-tutorial-custom-domain.md) voor het instellen van een aangepast domein voor deze twee bestaande service-exemplaren. Nadat de installatie is voltooid, worden beide service-exemplaren gebonden aan het aangepaste domein: bcdr-test.contoso.com

3. Een Traffic Manager en twee eind punten maken: [Maak een Traffic Manager profiel met behulp van de Azure Portal](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Dit is het Traffic Manager-profiel:
* Traffic Manager DNS-naam: `http://asc-bcdr.trafficmanager.net`
* Eindpunt profielen: 

| Profiel | Type | Doel | Prioriteit | Instellingen voor aangepaste header |
|--|--|--|--|--|
| Een profiel voor een eind punt | Extern eind punt | service-sample-a.asc-test.net | 1 | host: bcdr-test.contoso.com |
| Endpoint B-profiel | Extern eind punt | service-sample-b.asc-test.net | 2 | host: bcdr-test.contoso.com |

4. Maak een CNAME-record in DNS-zone: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net. 

5. De omgeving is nu volledig ingesteld. Klanten moeten toegang kunnen krijgen tot de app via: bcdr-test.contoso.com

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Uw eerste Azure Spring Cloud-toepassing implementeren](spring-cloud-quickstart.md)
