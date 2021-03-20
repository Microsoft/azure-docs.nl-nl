---
title: Extra bedreigingsbeveiliging in Azure Security Center
description: Meer informatie over de bedreigingsbeveiliging die beschikbaar is via Azure Security Center naast Azure Defender
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 5b9e9f5cc5184a083f45999eaeb031eb83b17c58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754331"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Extra bedreigingsbeveiliging in Azure Security Center
Naast de ingebouwde [Azure Defender-beveiligingsmethoden](azure-defender.md) biedt Azure Security Center de volgende mogelijkheden voor bedreigingsbeveiliging.

> [!TIP]
> Als u de bedreigingsbeschermingsmogelijkheden van Security Center wilt inschakelen, moet u Azure Defender inschakelen voor het abonnement dat de van toepassing zijnde workloads bevat.
>
> U kunt bedreigingsbeveiliging voor **Azure Database for MariaDB/MySQL/PostgreSQL** alleen inschakelen op resourceniveau.


## <a name="threat-protection-for-azure-network-layer"></a>Bedreigingsbeveiliging voor de Azure-netwerklaag <a name="network-layer"></a>
Analyse van de Security Center-netwerklaag is gebaseerd op voorbeelden van [IPFIX-gegevens](https://en.wikipedia.org/wiki/IP_Flow_Information_Export). Dit zijn pakketheaders die verzameld zijn door Azure-corerouters. Op basis van deze gegevensfeed gebruikt Security Center machinelearning-modellen om schadelijke verkeersactiviteiten te identificeren en markeren. Security Center gebruikt bovendien de Microsoft Threat Intelligence-database om IP-adressen te verrijken.

Sommige netwerkconfiguraties voorkomen dat Security Center waarschuwingen over verdachte netwerkactiviteit genereert. Security Center genereert netwerkwaarschuwingen als de volgende voorwaarden van toepassing zijn:
- Uw virtuele machine heeft een openbaar IP-adres (of bevindt zich op een load balancer met een openbaar IP-adres).
- Het uitgaande netwerkverkeer van uw virtuele machine wordt niet geblokkeerd door een externe ID-oplossing.

Zie de [Referentietabel met waarschuwingen](alerts-reference.md#alerts-azurenetlayer) voor een lijst met waarschuwingen voor de Azure-netwerklaag.


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Bedreigingsbeveiliging voor Azure Resource Manager (preview-versie)<a name ="management-layer"></a>
De beveiligingslaag van Security Center gebaseerd op Azure Resource Manager is momenteel als preview-versie beschikbaar.

Security Center biedt een extra beveiligingslaag door gebruik te maken van Azure Resource Manager-gebeurtenissen. Dit wordt beschouwd als het besturingsvlak voor Azure. Door de Azure Resource Manager-records te analyseren detecteert Security Center ongebruikelijke of mogelijk schadelijke bewerkingen in de Azure-abonnementsomgeving.

Zie de [Referentietabel met waarschuwingen](alerts-reference.md#alerts-resourcemanager) voor een lijst met waarschuwingen van Azure Defender voor Resource Manager.


>[!NOTE]
> Verschillende van de voorgaande analyses worden mogelijk gemaakt door Microsoft Cloud App Security. Als u van deze analyses wilt profiteren, moet u een Cloud App Security-licentie activeren. Als u al een licentie voor Cloud App Security hebt, zijn deze waarschuwingen standaard ingeschakeld. De waarschuwingen uitschakelen:
>
> 1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.
> 1. Selecteer het abonnement dat u wilt wijzigen.
> 1. Selecteer **Detectie van dreigingen**.
> 1. Schakel **Microsoft Cloud App Security toegang geven tot mijn gegevens** uit en selecteer **Opslaan**.


>[!NOTE]
>Security Center slaat klantgegevens met betrekking tot beveiliging op in dezelfde geografische regio als de resource. Als Security Center nog niet door Microsoft is geïmplementeerd in de geografische regio van de resource, worden de gegevens opgeslagen in de Verenigde Staten. Als Cloud App Security is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatieregels van Cloud App Security. Zie [Gegevensopslag voor niet-regionale services](https://azuredatacentermap.azurewebsites.net/) voor meer informatie.

1. Stel de werkruimte in waar u de agent gaat installeren. Zorg ervoor dat de werkruimte tot hetzelfde abonnement behoort dat u in Security Center gebruikt en dat u voor de werkruimte lees- en schrijfmachtigingen hebt.

1. Schakel **Azure Defender** in en selecteer **Opslaan**.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Bedreigingsbeveiliging voor Azure Cosmos DB (preview-versie)<a name="cosmos-db"></a>

De Azure Cosmos DB-waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om Azure Cosmos DB-accounts te openen of misbruiken.

Zie voor meer informatie:

* [Geavanceerde bedreigingsbeveiliging voor Azure Cosmos DB (preview-versie)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [De lijst met waarschuwingen voor beveiligingsbedreigingen voor Azure Cosmos DB (preview-versie)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Bedreigingsbeveiliging voor andere Microsoft-services <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Bedreigingsbeveiliging voor Azure WAF <a name="azure-waf"></a>

Azure Application Gateway biedt de functie Web Application Firewall (WAF) voor de gecentraliseerde beveiliging van uw webtoepassingen tegen bekende aanvallen en beveiligingsproblemen.

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van algemeen bekende beveiligingsproblemen. WAF voor Application Gateway is gebaseerd op Core Rule Set (CRS) 3.0 of 2.2.9 van het Open Web Application Security Project. De WAF wordt automatisch bijgewerkt om bescherming te bieden tegen nieuwe beveiligingsproblemen. 

Als u een licentie voor Azure WAF hebt, worden uw WAF-waarschuwingen gestreamd naar Security Center zonder dat er aanvullende configuratie nodig is. Zie [Web Application Firewall CRS rule groups and rules](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31) (CRS-regelgroepen en -regels voor Web Application Firewall) voor meer informatie over de door WAF gegenereerde waarschuwingen.


### <a name="threat-protection-for-azure-ddos-protection"></a>Bedreigingsbeveiliging voor Azure DDoS Protection <a name="azure-ddos"></a>

Van DDoS-aanvallen (Distributed Denial of service) is het bekend dat ze eenvoudig uit te voeren zijn. Ze zijn een aanzienlijk beveiligingsprobleem geworden, zeker als u uw toepassingen naar de cloud verplaatst. 

Met een DDoS-aanval wordt geprobeerd de resources van een toepassing uit te putten, waardoor de toepassing niet meer beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eindpunt dat kan worden bereikt via internet.

U kunt zich tegen DDoS-aanvallen beschermen door een licentie voor Azure DDoS Protection aan te schaffen en ervoor te zorgen dat u de aanbevolen procedures voor het ontwerpen van toepassingen volgt. DDoS Protection biedt verschillende servicelagen. Zie het [Overzicht van Azure DDoS Protection](../ddos-protection/ddos-protection-overview.md) voor meer informatie.

Zie de [Referentietabel met waarschuwingen](alerts-reference.md#alerts-azureddos) voor een lijst met waarschuwingen voor Azure DDoS Protection.


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over de beveiligingswaarschuwingen van deze functies voor bedreigingsbeveiliging:

* [Referentietabel voor alle Azure Security Center-waarschuwingen](alerts-reference.md)
* [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Security Center-gegevens continu exporteren](continuous-export.md)