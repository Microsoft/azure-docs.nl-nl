---
title: Azure Web Application firewall en Azure Policy
description: Azure Web Application firewall (WAF) in combi natie met Azure Policy kan u helpen organisatie standaarden af te dwingen en op schaal naleving te beoordelen voor WAF-resources
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7798d7e960286d4f8aa971eb2eb0b03d24bd6360
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589454"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web Application firewall en Azure Policy

Azure Web Application firewall (WAF) in combi natie met Azure Policy kan u helpen organisatie standaarden af te dwingen en de naleving op schaal te beoordelen voor WAF-resources. Azure Policy is een beheer programma dat een geaggregeerde weer gave biedt om de algehele status van de omgeving te evalueren, met de mogelijkheid om in te zoomen op de nauw keurigheid per bron per beleid. Azure Policy helpt u ook om uw resources te laten voldoen aan de naleving van bulksgewijs herstel voor bestaande resources en automatisch herstel voor nieuwe resources.

## <a name="azure-policy-for-web-application-firewall"></a>Azure Policy voor Web Application firewall

Er zijn verschillende ingebouwde Azure Policy definities voor het beheren van WAF-resources. Een uitsplitsing van de beleids definities en hun functies is als volgt:

1. **Web Application firewall (WAF) moet zijn ingeschakeld voor de Azure front deur-service**: Azure front-deur services worden geëvalueerd op als er een WAF aanwezig is bij het maken van resources. Het beleid heeft drie effecten: controleren, weigeren en uitschakelen. Controle nummers wanneer een Azure front-deur service geen WAF heeft en gebruikers kan zien wat de Azure front-deur service niet naleeft. Als er geen Azure front-deur service wordt gemaakt, wordt voor komen dat er een WAF is gekoppeld. Als u dit beleid uitschakelt, wordt deze instelling uitgeschakeld.

2. **Web Application firewall (WAF) moet zijn ingeschakeld voor Application Gateway**: toepassings gateways worden geëvalueerd als er WAF aanwezig zijn bij het maken van resources. Het beleid heeft drie effecten: controleren, weigeren en uitschakelen. Controle sporen wanneer een Application Gateway geen WAF heeft en laat gebruikers zien wat Application Gateway niet voldoet. Weigeren voor komt dat Application Gateway worden gemaakt als er geen WAF is gekoppeld. Als u dit beleid uitschakelt, wordt deze instelling uitgeschakeld.

3. **Web Application firewall (WAF) moet de opgegeven modus voor de Azure front deur-service gebruiken**: verplicht het gebruik van de modus detectie of preventie te activeren op alle firewall beleidsregels voor webtoepassingen voor de Azure front-deur service. Het beleid heeft drie effecten: controleren, weigeren en uitschakelen. Controle sporen wanneer een WAF niet voldoet aan de opgegeven modus. Als deze optie niet is ingesteld op de juiste modus, voor komt u dat een WAF wordt gemaakt. Als u dit beleid uitschakelt, wordt deze instelling uitgeschakeld.

4. **Web Application firewall (WAF) moet de opgegeven modus gebruiken voor Application Gateway**: verplicht het gebruik van de modus detectie of preventie actief te maken op alle firewall-beleids regels voor webtoepassingen voor Application Gateway. Het beleid heeft drie effecten: controleren, weigeren en uitschakelen. Controle sporen wanneer een WAF niet voldoet aan de opgegeven modus. Als deze optie niet is ingesteld op de juiste modus, voor komt u dat een WAF wordt gemaakt. Als u dit beleid uitschakelt, wordt deze instelling uitgeschakeld.

## <a name="launch-an-azure-policy"></a>Een Azure Policy starten

1.  Op de start pagina van Azure typt u beleid in de zoek balk en klikt u op het pictogram Azure Policy

2.  Selecteer **toewijzingen** in de Azure Policy-service, onder **ontwerpen**.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Tabblad toewijzingen in Azure Policy":::

3.  Selecteer op de pagina Toewijzingen het pictogram **beleid** aan de bovenkant.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Tabblad basis informatie op de pagina beleid toewijzen":::

4.  Werk op het tabblad basis beginselen van beleid toewijzen de volgende velden bij:
    1.  **Bereik**: Selecteer wat Azure-abonnementen en-resource groepen moeten worden beïnvloed door de beleids definitie.
    2.  **Uitsluitingen**: Selecteer alle resources uit het bereik die u wilt uitsluiten van de beleids toewijzing.
    3.  **Beleids definitie**: Selecteer de beleids definitie die moet worden toegepast op het bereik met uitsluitingen. Typ ' Web Application Firewall ' in de zoek balk om de relevante Web Application firewall-Azure Policy te kiezen.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Scherm opname van het tabblad beleids definities op de pagina beschik bare definities.":::

5.  Selecteer het tabblad **para meters** en werk de para meters voor beleids toewijzing bij. Als u verder wilt verduidelijken wat de para meter doet, houdt u de muis aanwijzer boven het info pictogram naast de parameter naam voor verdere uitleg.

6.  Selecteer **beoordeling + maken** om uw beleids toewijzing te volt ooien. De beleids toewijzing duurt ongeveer 15 minuten totdat deze actief is voor nieuwe resources.
