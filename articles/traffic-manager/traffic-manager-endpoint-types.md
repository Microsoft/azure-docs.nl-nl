---
title: Traffic Manager eindpunt typen | Microsoft Docs
description: In dit artikel worden verschillende soorten eind punten uitgelegd die kunnen worden gebruikt met Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051224"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager-eindpunten

Met Microsoft Azure Traffic Manager kunt u bepalen hoe netwerk verkeer wordt gedistribueerd naar toepassings implementaties die in verschillende data centers worden uitgevoerd. U configureert elke toepassingsimplementatie als een eindpunt in Traffic Manager. Wanneer Traffic Manager een DNS-aanvraag ontvangt, wordt een beschikbaar eindpunt gekozen om te retourneren in het DNS-antwoord. Traffic Manager baseert de keuze voor de huidige eindpunt status en de verkeers routerings methode. Zie [How Traffic Manager werkt](traffic-manager-how-it-works.md)voor meer informatie.

Er zijn drie typen eindpunten die door Traffic Manager worden ondersteund:

* **Azure-eindpunten** worden gebruikt voor services die worden gehost in Azure.
* **Externe eind punten** worden gebruikt voor IPv4-en IPv6-adressen, FQDN of voor services die buiten Azure worden gehost. Deze services kunnen zowel on-premises als met een andere hosting provider zijn.
* Met **Geneste eindpunten** worden Traffic Manager-profielen gecombineerd om flexibelere verkeersrouteringschema's te krijgen om de behoeften van grotere, complexere implementaties te ondersteunen.

Er is geen beperking voor de manier waarop eind punten van verschillende typen worden gecombineerd in één Traffic Manager profiel. Elk profiel kan elke combinatie van eindpunttypen bevatten.

In de volgende secties wordt elk type eind punt uitgebreid beschreven.

## <a name="azure-endpoints"></a>Azure-eind punten

Azure-eind punten worden gebruikt voor services op basis van Azure in Traffic Manager. De volgende Azure-resource typen worden ondersteund:

* PaaS Cloud Services.
* Web Apps
* Web-app-sleuven
* PublicIPAddress-bronnen (die rechtstreeks of via een Azure Load Balancer kunnen worden verbonden met Vm's). Aan de publicIpAddress moet een DNS-naam zijn toegewezen om te worden gebruikt in een Traffic Manager profiel.

PublicIPAddress resources zijn Azure Resource Manager resources. Ze bestaan niet in het klassieke implementatie model en worden alleen ondersteund in de Azure Resource Manager-ervaringen van Traffic Manager. De andere eindpunt typen worden ondersteund via Resource Manager en het klassieke implementatie model.

Wanneer u Azure-eind punten gebruikt, detecteert Traffic Manager wanneer een web-app wordt gestopt en gestart. Deze status wordt weer gegeven in de eindpunt status. Zie [Traffic Manager endpoint-bewaking](traffic-manager-monitoring.md#endpoint-and-profile-status) voor meer informatie. Wanneer de onderliggende service wordt gestopt, worden er door Traffic Manager geen controle van de eindpunt status of het directe verkeer naar het eind punt uitgevoerd. Er zijn geen Traffic Manager facturerings gebeurtenissen voor het gestopte exemplaar. Wanneer de service opnieuw wordt gestart, worden de facturerings-en het eind punt in aanmerking komen voor het ontvangen van verkeer. Deze detectie is niet van toepassing op PublicIpAddress-eind punten.

## <a name="external-endpoints"></a>Externe eind punten

Externe eind punten worden gebruikt voor IPv4-en IPv6-adressen, FQDN-namen of voor services buiten Azure. Door het gebruik van IPv4/IPv6-adres eindpunten kunt u Traffic Manager de status van eind punten controleren zonder dat hiervoor een DNS-naam is vereist. Als gevolg hiervan kan Traffic Manager reageren op query's met A/AAAA-records wanneer dat eind punt in een antwoord wordt geretourneerd. Services buiten Azure kunnen een service omvatten die on-premises wordt gehost of met een andere provider. Externe eind punten kunnen afzonderlijk worden gebruikt of worden gecombineerd met Azure-eindpunten in hetzelfde Traffic Manager-profiel. De uitzonde ring hierop is voor eind punten die zijn opgegeven als IPv4-of IPv6-adres, die alleen externe eind punten kunnen zijn. Het combi neren van Azure-eind punten met externe eind punten maakt diverse scenario's mogelijk:

* Bied uitgebreide redundantie voor een bestaande on-premises toepassing in een actief-actief of actief/passief failover-model met behulp van Azure. 
* Verkeer routeren naar eind punten waaraan geen DNS-naam is gekoppeld. Vermindert ook de algehele latentie van de DNS-zoek opdracht door de nood zaak om een tweede DNS-query uit te voeren om een IP-adres van een DNS-naam op te halen.
* Verminder de latentie van de toepassing voor gebruikers over de hele wereld, breid een bestaande on-premises toepassing uit naar andere geografische locaties in Azure. Zie de [verkeers routering van Traffic manager prestaties](traffic-manager-routing-methods.md#performance)voor meer informatie.
* Bied meer capaciteit voor een bestaande on-premises toepassing, continu of als een ' burst-to-Cloud ' oplossing om te voldoen aan een piek in de vraag met behulp van Azure.

In sommige gevallen is het handig om externe eind punten te gebruiken om te verwijzen naar Azure-Services. Raadpleeg de [Veelgestelde vragen](traffic-manager-faqs.md#traffic-manager-endpoints) voor voor beelden. Status controles worden gefactureerd op basis van het aantal Azure-eind punten, niet op het externe eind punt. In tegens telling tot Azure-eind punten, wordt de facturering van de status controle voortgezet als u de onderliggende service stopt of verwijdert. De facturering wordt gestopt zodra u het eind punt in Traffic Manager hebt uitgeschakeld of verwijderd.

## <a name="nested-endpoints"></a>Geneste eind punten

Geneste eind punten combi neren meerdere Traffic Manager profielen voor het maken van flexibele verkeer routerings schema's ter ondersteuning van de behoeften van grotere en complexe implementaties. Met geneste eind punten wordt een onderliggend profiel toegevoegd als een eind punt aan een bovenliggend profiel. De onderliggende en bovenliggende profielen kunnen andere eind punten van elk type bevatten, inclusief andere geneste profielen. 

Zie [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md)voor meer informatie.

## <a name="web-apps-as-endpoints"></a>Web Apps als eind punten

Er zijn nog meer aandachtspunten bij het configureren van Web Apps als eind punten in Traffic Manager:

1. Alleen Web Apps in de Standard-SKU of hoger komen in aanmerking voor gebruik met Traffic Manager. Poging tot het toevoegen van een web-app van een lagere SKU mislukt. Het downgradeen van de SKU van een bestaande web-app leidt ertoe dat Traffic Manager geen verkeer meer naar die web-app verzendt. Zie de [app service-abonnementen](https://azure.microsoft.com/pricing/details/app-service/plans/) voor meer informatie over ondersteunde abonnementen.
2. Wanneer een eind punt een HTTP-aanvraag ontvangt, wordt de header ' host ' gebruikt in de aanvraag om te bepalen welke Web-App de aanvraag moet onderhouden. De hostheader bevat de DNS-naam die wordt gebruikt om de aanvraag te starten, bijvoorbeeld ' contosoapp.azurewebsites.net '. Als u een andere DNS-naam met uw web-app wilt gebruiken, moet de DNS-naam worden geregistreerd als een aangepaste domein naam voor de app. Wanneer u een web-app-eind punt toevoegt als Azure-eind punt, wordt de DNS-naam van het Traffic Manager-profiel automatisch geregistreerd voor de app. Deze registratie wordt automatisch verwijderd wanneer het eind punt wordt verwijderd.
3. Elk Traffic Manager profiel kan Maxi maal één web-app-eind punt hebben van elke Azure-regio. Als u wilt werken voor deze beperking, kunt u een web-app configureren als een extern eind punt. Raadpleeg de [Veelgestelde vragen](traffic-manager-faqs.md#traffic-manager-endpoints)voor meer informatie.

## <a name="enabling-and-disabling-endpoints"></a>Eind punten in-en uitschakelen

Het uitschakelen van een eind punt in Traffic Manager kan handig zijn om tijdelijk verkeer te verwijderen van een eind punt dat zich in de onderhouds modus bevindt of opnieuw wordt geïmplementeerd. Zodra het eind punt weer actief is, kan het opnieuw worden ingeschakeld.

U kunt eind punten in-of uitschakelen via de Traffic Manager Portal, Power shell, CLI of REST API.

> [!NOTE]
> Het uitschakelen van een Azure-eind punt heeft niets te maken met de implementatie status in Azure. Een Azure-service (zoals een virtuele machine of web-app blijft actief en kan verkeer ontvangen, zelfs als deze is uitgeschakeld in Traffic Manager. Verkeer kan rechtstreeks worden geadresseerd aan het service-exemplaar in plaats van via de DNS-naam van het Traffic Manager profiel. Zie [how Traffic Manager werkt](traffic-manager-how-it-works.md)voor meer informatie.

De huidige geschiktheid van elk eind punt voor het ontvangen van verkeer is afhankelijk van de volgende factoren:

* De profiel status (ingeschakeld of uitgeschakeld)
* De eindpunt status (ingeschakeld of uitgeschakeld)
* De resultaten van de status controles voor dat eind punt

Zie [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md#endpoint-and-profile-status)voor meer informatie.

> [!NOTE]
> Omdat Traffic Manager op het DNS-niveau werkt, kan dit geen invloed hebben op bestaande verbindingen met een eind punt. Wanneer een eind punt niet beschikbaar is, stuurt Traffic Manager nieuwe verbindingen naar een ander beschikbaar eind punt. De host achter het uitgeschakelde of beschadigde eind punt kan echter verkeer blijven ontvangen via bestaande verbindingen totdat deze sessies worden beëindigd. Toepassingen moeten de duur van de sessie beperken zodat verkeer van bestaande verbindingen kan worden verstopt.

Als alle eind punten in een profiel worden uitgeschakeld of als het profiel zelf wordt uitgeschakeld, stuurt Traffic Manager een ' NXDOMAIN-antwoord naar een nieuwe DNS-query.

## <a name="faqs"></a>Veelgestelde vragen

* [Kan ik Traffic Manager gebruiken met eind punten van meerdere abonnementen?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Kan ik Traffic Manager gebruiken met staging-sleuven van Cloud service?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Ondersteunt Traffic Manager IPv6-eind punten?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Kan ik Traffic Manager gebruiken met meer dan één web-app in dezelfde regio?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Hoe kan ik de Azure-eind punten van mijn Traffic Manager-profiel naar een andere resource groep verplaatsen?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over de werking van Traffic Manager](traffic-manager-how-it-works.md).
* Meer informatie over Traffic Manager [eindpunt bewaking en automatische failover](traffic-manager-monitoring.md).
* Meer informatie over Traffic Manager [routerings methoden voor verkeer](traffic-manager-routing-methods.md).