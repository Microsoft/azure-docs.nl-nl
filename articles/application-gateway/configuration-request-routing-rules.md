---
title: Configuratie van de routerings regels voor Azure-toepassing gateway aanvragen
description: In dit artikel wordt beschreven hoe u de routerings regels voor aanvraag Azure-toepassing gateway configureert.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397532"
---
# <a name="application-gateway-request-routing-rules"></a>Routerings regels voor Application Gateway aanvragen

Wanneer u een toepassings gateway maakt met behulp van de Azure Portal, maakt u een standaard regel (*firewallregel1*). Deze regel koppelt de standaard-listener (*appGatewayHttpListener*) aan de standaard back-end-pool (*appGatewayBackendPool*) en de standaard back-end-http-instellingen (*appGatewayBackendHttpSettings*). Nadat u de gateway hebt gemaakt, kunt u de instellingen van de standaard regel bewerken of nieuwe regels maken.

## <a name="rule-type"></a>Regel type

Wanneer u een regel maakt, kiest u tussen [ *basis* en *op basis van het pad*](./application-gateway-components.md#request-routing-rules).

- Kies basis als u alle aanvragen voor de gekoppelde listener (bijvoorbeeld *blog <i></i> . contoso.com/ \* )* wilt door sturen naar één back-end-groep.
- Kies op basis van pad als u aanvragen van specifieke URL-paden naar specifieke back-endservers wilt routeren. Het pad patroon wordt alleen toegepast op het pad van de URL, niet op de query parameters.

### <a name="order-of-processing-rules"></a>Volg orde van de verwerkings regels

Voor de v1-en v2-SKU wordt het vergelijken van het patroon van binnenkomende aanvragen verwerkt in de volg orde waarin de paden worden weer gegeven in de URL-pad toewijzing van de op pad gebaseerde regel. Als een aanvraag overeenkomt met het patroon in twee of meer paden in de pad-map, wordt het pad dat als eerste wordt weer gegeven. En de aanvraag wordt doorgestuurd naar de back-end die is gekoppeld aan het pad.

## <a name="associated-listener"></a>Gekoppelde listener

Koppel een listener aan de regel zodat de regel voor het door sturen van de *aanvraag* die is gekoppeld aan de listener, wordt geëvalueerd om de back-end-pool te bepalen voor het routeren van de aanvraag.

## <a name="associated-back-end-pool"></a>Gekoppelde back-end-pool

Koppel aan de regel de back-end-groep die de back-end-doelen bevat die aanvragen bedienen die de listener ontvangt.

 - Voor een eenvoudige regel is slechts één back-end-pool toegestaan. Alle aanvragen voor de bijbehorende listener worden doorgestuurd naar die back-end-pool.

 - Voor een regel op basis van een pad voegt u meerdere back-end-Pools toe die overeenkomen met elk URL-pad. De aanvragen die overeenkomen met het opgegeven URL-pad worden doorgestuurd naar de bijbehorende back-end-groep. Voeg ook een standaard back-end-pool toe. Aanvragen die niet overeenkomen met een URL-pad in de regel, worden doorgestuurd naar die groep.

## <a name="associated-back-end-http-setting"></a>Gekoppelde back-end-HTTP-instelling

Voeg een back-end-HTTP-instelling voor elke regel toe. Aanvragen worden gerouteerd van de toepassings gateway naar de back-end-doelen met behulp van het poort nummer, het protocol en andere informatie die is opgegeven in deze instelling.

Voor een eenvoudige regel is slechts één back-end HTTP-instelling toegestaan. Alle aanvragen voor de bijbehorende listener worden doorgestuurd naar de bijbehorende back-end-doelen met behulp van deze HTTP-instelling.

Voor een regel op basis van een pad voegt u meerdere back-end-HTTP-instellingen toe die overeenkomen met elk URL-pad. Aanvragen die overeenkomen met het URL-pad in deze instelling worden doorgestuurd naar de bijbehorende back-end-doelen met behulp van de HTTP-instellingen die overeenkomen met elk URL-pad. Voeg ook een standaard HTTP-instelling toe. Aanvragen die niet overeenkomen met een URL-pad in deze regel, worden doorgestuurd naar de standaard back-end-groep met behulp van de standaard-HTTP-instelling.

## <a name="redirection-setting"></a>Omleidings instelling

Als omleiding is geconfigureerd voor een basis regel, worden alle aanvragen voor de gekoppelde listener omgeleid naar het doel. Dit is *wereld wijde* omleiding. Als omleiding is geconfigureerd voor een op een pad gebaseerde regel, worden alleen aanvragen in een specifiek site gebied omgeleid. Een voor beeld is een boodschappen mand gebied dat wordt aangeduid met */Cart/ \**. Dit is een omleiding *op basis van pad* .

Zie [Application Gateway omleidings overzicht](redirect-overview.md)voor meer informatie over omleidingen.

### <a name="redirection-type"></a>Type omleiding

Kies het type omleiding vereist: *permanent (301)*, *tijdelijk (307*), *gevonden (302)* of *Zie other (303)*.

### <a name="redirection-target"></a>Doel van omleiding

Kies een andere listener of een externe site als het doel voor omleiding.

#### <a name="listener"></a>Listener

Kies listener als omleidings doel om verkeer van de ene listener naar de andere op de gateway om te leiden. Deze instelling is vereist wanneer u HTTP-naar-HTTPS-omleiding wilt inschakelen. Het omgeleide verkeer van de bron-listener die controleert op binnenkomende HTTP-aanvragen bij de doel-listener die controleert op binnenkomende HTTPS-aanvragen. U kunt er ook voor kiezen om de query teken reeks en het pad op te halen uit de oorspronkelijke aanvraag in de aanvraag die wordt doorgestuurd naar het omleidings doel.

![Het dialoog venster Application Gateway onderdelen](./media/configuration-overview/configure-redirection.png)

Zie voor meer informatie over HTTP-naar-HTTPS-omleiding:
- [HTTP-naar-HTTPS-omleiding met behulp van de Azure Portal](redirect-http-to-https-portal.md)
- [HTTP-naar-HTTPS-omleiding met behulp van Power shell](redirect-http-to-https-powershell.md)
- [HTTP-naar-HTTPS-omleiding via de Azure CLI](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Externe site

Kies externe site wanneer u het verkeer wilt omleiden van de listener die is gekoppeld aan deze regel naar een externe site. U kunt ervoor kiezen om de query reeks uit de oorspronkelijke aanvraag op te halen in de aanvraag die wordt doorgestuurd naar het omleidings doel. U kunt het pad naar de externe site die zich in de oorspronkelijke aanvraag bevonden, niet door sturen.

Zie voor meer informatie over omleiding:
- [Verkeer omleiden naar een externe site met behulp van Power shell](redirect-external-site-powershell.md)
- [Verkeer omleiden naar een externe site door gebruik te maken van de CLI](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>HTTP-headers en URL opnieuw schrijven

Met behulp van regels voor herschrijven kunt u HTTP (S)-aanvragen en-antwoord headers toevoegen, verwijderen of bijwerken, evenals het URL-pad en de query reeks parameters, omdat de aanvraag-en antwoord pakketten worden verplaatst tussen de client-en back-end-groepen via de toepassings gateway.

De para meters headers en URL kunnen worden ingesteld op statische waarden of op andere headers en Server variabelen. Dit helpt bij belang rijke gebruiks gevallen, zoals het extra heren van client-IP-adressen, het verwijderen van gevoelige informatie over de back-end, het toevoegen van meer beveiliging, enzovoort.
Zie voor meer informatie:

 - [Overzicht van HTTP-headers en URL'S opnieuw schrijven](rewrite-http-headers-url.md)
 - [Herschrijven van HTTP-header configureren](rewrite-http-headers-portal.md)
 - [URL opnieuw schrijven configureren](rewrite-url-portal.md)

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over HTTP-instellingen](configuration-http-settings.md)