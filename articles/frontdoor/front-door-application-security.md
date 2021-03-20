---
title: Azure front-deur-beveiliging van de toepassingslaag | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u met Azure front-deur de back-ends van uw toepassingen kunt beveiligen en beveiligen
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89399936"
---
# <a name="application-layer-security-with-front-door"></a>Beveiligingslaag van de toepassingslaag met de voor deur
Azure front-deur biedt beveiliging van webtoepassingen om uw webtoepassingen te beschermen tegen netwerk aanvallen en veelvoorkomende beveiligings problemen, zoals SQL-injectie of cross-site scripting (XSS). Ingeschakeld voor http (s) front-ends, de beveiliging van de toepassingslaag van de voor deur is wereld wijd gedistribueerd en altijd, waardoor kwaad aardige aanvallen op de netwerk Edge van Azure, ver weg van uw back-end, worden gestopt. Met extra optimalisatie van de beveiliging en prestaties biedt de voor deur snelle en veilige webervaringen voor uw eind gebruikers.

## <a name="application-protection"></a>Toepassings beveiliging
De beveiliging van de front deur-toepassing wordt op elke rand omgeving over de hele wereld geconfigureerd in overeenstemming met toepassingen en blokkeert automatisch niet-http (s)-verkeer van uw webtoepassingen. Onze gedistribueerde architectuur met meerdere tenants maakt wereld wijde beveiliging mogelijk zonder verlies van prestaties. Voor http (s)-workloads biedt de Web Application Protection-Service van front deur een geavanceerde regel engine voor aangepaste regels, vooraf geconfigureerde ruleset op basis van veelvoorkomende aanvallen en gedetailleerde logboek registratie voor alle aanvragen die overeenkomen met een regel. Flexibele acties, waaronder alleen toestaan, blok keren of Logboeken, worden ondersteund.

## <a name="custom-access-control-rules"></a>Aangepaste regels voor toegangs beheer
- Lijst met **toegestane IP-adressen en blok keren:** U kunt aangepaste regels configureren om de toegang tot uw webtoepassingen te beheren op basis van de lijst met client-IP-adressen. Zowel IP v4 als IP V6 worden ondersteund
- **Geografisch gebaseerd toegangs beheer:** U kunt aangepaste regels configureren om de toegang tot uw webtoepassingen te beheren op basis van de land code waarvan het client-IP-adres afkomstig is
- **Http-para meters filteren:** U kunt aangepaste toegangs regels configureren op basis van overeenkomende http (s)-aanvraag parameters, waaronder headers, URL'S en query reeksen

## <a name="azure-managed-rules"></a>Door Azure beheerde regels
- Een vooraf geconfigureerde set met regels voor veelvoorkomende OWASP-beveiligings problemen is standaard ingeschakeld. Bij de preview-versie bevat de set regels sqli-en XSS-aanvragen controleren. Aanvullende regels worden toegevoegd. U kunt ervoor kiezen om te beginnen met de actie alleen logboeken voor het valideren van vooraf geconfigureerde regels die op de verwachte manier werken voor uw toepassingen 

## <a name="rate-limiting"></a>Snelheidsbeperking
- Een regel voor frequentie beheer is het beperken van abnormaal hoog verkeer van elk client-IP-adres.  U kunt een drempel waarde instellen voor het aantal webaanvragen dat door een client-IP wordt toegestaan gedurende een duur van één minuut.

## <a name="centralized-protection-policy"></a>Gecentraliseerd beveiligings beleid
- U kunt verschillende beveiligings regels definiëren en deze toevoegen aan een beleid op volg orde van prioriteit. Aangepaste regels hebben een hogere prioriteit dan een beheerde ruleset voor het toestaan van uitzonde ringen. Er is één beleid gekoppeld aan uw webtoepassing.  Hetzelfde beveiligings beleid voor webtoepassingen wordt gerepliceerd naar alle edge-servers op alle locaties. Zorg ervoor dat het beleid in alle regio's consistent is.

## <a name="configuration"></a>Configuratie
- Tijdens de preview-periode kunt u REST Api's, Power shell of CLI gebruiken voor het maken en implementeren van regels en beleids regels voor de beveiliging van de voor deur. Toegang tot de portal wordt ondersteund voordat de service algemeen beschikbaar is. 


## <a name="monitoring"></a>Bewaking
De voor deur biedt de mogelijkheid om webtoepassingen te bewaken tegen aanvallen die gebruikmaken van real-time metrische gegevens die zijn geïntegreerd met Azure Monitor om waarschuwingen bij te houden en trends eenvoudig te bewaken.

## <a name="pricing"></a>Prijzen
De beveiliging van de toepassingslaag van de front deur is gratis tijdens de preview-versie.


## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
