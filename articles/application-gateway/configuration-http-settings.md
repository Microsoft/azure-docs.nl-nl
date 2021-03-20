---
title: Configuratie van HTTP-instellingen van Azure-toepassing gateway
description: In dit artikel wordt beschreven hoe u HTTP-instellingen voor Azure-toepassing gateway configureert.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89652880"
---
# <a name="application-gateway-http-settings-configuration"></a>Configuratie van HTTP-instellingen Application Gateway

De Application Gateway routeert het verkeer naar de back-endservers door gebruik te maken van de configuratie die u hier opgeeft. Nadat u een HTTP-instelling hebt gemaakt, moet u deze koppelen aan een of meer aanvraag-routerings regels.

## <a name="cookie-based-affinity"></a>Affiniteit op basis van cookies

Azure-toepassing gateway gebruikt door gateways beheerde cookies voor het onderhouden van gebruikers sessies. Wanneer een gebruiker de eerste aanvraag verzendt naar Application Gateway, wordt er een affiniteits cookie ingesteld in het antwoord met een hash-waarde die de sessie Details bevat, zodat de volgende aanvragen die de affiniteits cookie hebben, worden doorgestuurd naar dezelfde back-endserver voor het onderhouden van persistentie. 

Deze functie is handig als u een gebruikers sessie op dezelfde server wilt behouden en wanneer de sessie status lokaal op de server wordt opgeslagen voor een gebruikers sessie. Als de toepassing geen affiniteit op basis van cookies kan verwerken, kunt u deze functie niet gebruiken. Als u deze wilt gebruiken, moet u ervoor zorgen dat de clients cookies ondersteunen.

De [V80-update](https://chromiumdash.appspot.com/schedule) van de [chroom browser](https://www.chromium.org/Home) heeft een opdracht ingediend waarbij HTTP-cookies zonder [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) kenmerk moeten worden behandeld als SameSite = slordig. In het geval van CORS-aanvragen (cross-Origin Resource Sharing), als de cookie moet worden verzonden in een context van een derde partij, moet het gebruik van *SameSite = none; Beveiligde* kenmerken en deze moeten alleen via HTTPS worden verzonden. Anders in een scenario met alleen HTTP, verzendt de browser de cookies niet in de context van derden. Het doel van deze update van Chrome is de beveiliging te verbeteren en CSRF-aanvallen (cross-site request vervalsing) te voor komen. 

Ter ondersteuning van deze wijziging wordt vanaf februari 17 2020, Application Gateway (alle SKU-typen) een andere cookie met de naam *ApplicationGatewayAffinityCORS* , naast de bestaande *ApplicationGatewayAffinity* cookie injecteerd. Aan het *ApplicationGatewayAffinityCORS* -cookie zijn twee meer kenmerken toegevoegd (*"SameSite = none; Beveiligd '*), zodat plak sessies zelfs voor cross-Origin-aanvragen worden bewaard.

Houd er rekening mee dat de naam van de standaard-affiniteits cookie *ApplicationGatewayAffinity* is en u kunt deze wijzigen. Als u een aangepaste cookie naam voor affiniteit gebruikt, wordt er een extra cookie toegevoegd met CORS als achtervoegsel. Bijvoorbeeld *CustomCookieNameCORS*.

> [!NOTE]
> Als het kenmerk *SameSite = none* is ingesteld, is het verplicht dat de cookie ook de *beveiligde* vlag bevat en moet worden verzonden via https.  Als sessie affiniteit vereist is voor CORS, moet u uw werk belasting migreren naar HTTPS. Raadpleeg de hand leiding voor TLS-offload en end-to-end TLS voor Application Gateway: [overzicht](ssl-overview.md), [een toepassings gateway met TLS-beëindiging configureren met behulp van de Azure Portal](create-ssl-portal.md), [end-to-end TLS configureren met behulp van Application Gateway met de portal](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Verwerkingsstop voor verbindingen

Met de verbinding verbreken kunt u de leden van de back-end groep tijdens geplande service-updates zonder problemen verwijderen. U kunt deze instelling Toep assen op alle leden van een back-end-pool door het verbreken van de verbinding in te scha kelen voor de HTTP-instelling. Zo zorgt u ervoor dat alle ongedaan maken van de registratie van exemplaren van een back-end-groep de bestaande verbindingen blijven behouden en dat er doorlopende aanvragen voor een Configureer bare time-out worden verzonden en geen nieuwe aanvragen of verbindingen worden ontvangen. De enige uitzonde ring hierop is het aantal aanvragen dat is gebonden voor het deregistreren van instanties vanwege gateway beheer sessie-affiniteit en zal worden doorgestuurd naar de instanties van de registratie. Het afbreken van de verbinding is van toepassing op back-end-exemplaren die expliciet uit de back-end-pool worden verwijderd.

## <a name="protocol"></a>Protocol

Application Gateway ondersteunt HTTP en HTTPS voor het routeren van aanvragen naar de back-endservers. Als u HTTP kiest, wordt het verkeer naar de back-endservers niet versleuteld. Als niet-versleutelde communicatie niet acceptabel is, kiest u HTTPS.

Deze instelling in combi natie met HTTPS in de listener ondersteunt [end-to-end TLS](ssl-overview.md). Zo kunt u veilig gevoelige gegevens die zijn versleuteld verzenden naar de back-end. Elke back-endserver in de back-end-groep waarvoor end-to-end TLS is ingeschakeld, moet worden geconfigureerd met een certificaat om beveiligde communicatie toe te staan.

## <a name="port"></a>Poort

Met deze instelling geeft u de poort op waarop de back-endservers worden geluisterd naar verkeer van de toepassings gateway. U kunt poorten configureren variërend van 1 tot en met 65535.

## <a name="request-timeout"></a>Time-out van aanvraag

Deze instelling is het aantal seconden dat de toepassings gateway wacht op het ontvangen van een reactie van de back-endserver.

## <a name="override-back-end-path"></a>Pad van back-end overschrijven

Met deze instelling kunt u een optioneel aangepast doorstuur traject configureren dat moet worden gebruikt wanneer de aanvraag wordt doorgestuurd naar de back-end. Elk deel van het binnenkomende pad dat overeenkomt met het aangepaste pad in het veld **onderdrukking van back-end pad** , wordt gekopieerd naar het doorgestuurde pad. De volgende tabel laat zien hoe deze functie werkt:

- Wanneer de HTTP-instelling is gekoppeld aan een basis regel voor het routeren van aanvragen:

  | Oorspronkelijke aanvraag  | Pad van back-end overschrijven | Aanvraag doorgestuurd naar back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | mijn            | overschrijven            | /override/home/              |
  | /home/secondhome/ | overschrijven            | /override/home/secondhome/   |

- Wanneer de HTTP-instelling is gekoppeld aan een op een pad gebaseerde aanvraag routerings regel:

  | Oorspronkelijke aanvraag           | Padregel       | Pad van back-end overschrijven | Aanvraag doorgestuurd naar back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | overschrijven            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | overschrijven            | /override/home/secondhome/   |
  | mijn                     | pathrule      | overschrijven            | /override/home/              |
  | /home/secondhome/          | pathrule      | overschrijven            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | overschrijven            | overschrijven                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | overschrijven            | /override/secondhome/        |
  | pathrule                 | pathrule      | overschrijven            | overschrijven                   |

## <a name="use-for-app-service"></a>Gebruiken voor app service

Dit is een snelkoppeling naar een gebruikers interface die de twee vereiste instellingen voor de Azure App Service back-end selecteert. Hiermee kunt u de **hostnaam van het back-end-adres kiezen** en wordt er een nieuwe aangepaste test gemaakt als u er nog geen hebt. (Zie de sectie [hostnaam selecteren uit het back-end-adres in](#pick-host-name-from-back-end-address)dit artikel voor meer informatie.) Er wordt een nieuwe test gemaakt en de test header wordt opgehaald uit het adres van het back-end-lid.

## <a name="use-custom-probe"></a>Aangepaste test gebruiken

Met deze instelling wordt een [aangepaste test](application-gateway-probe-overview.md#custom-health-probe) gekoppeld aan een http-instelling. U kunt slechts één aangepaste test koppelen met een HTTP-instelling. Als u een aangepaste test niet expliciet koppelt, wordt de [standaard test](application-gateway-probe-overview.md#default-health-probe-settings) gebruikt voor het controleren van de status van de back-end. We raden u aan om een aangepaste test te maken voor meer controle over de status controle van uw back-ends.

> [!NOTE]
> De aangepaste test controleert de status van de back-end-groep niet, tenzij de bijbehorende HTTP-instelling expliciet is gekoppeld aan een listener.

## <a name="pick-host-name-from-back-end-address"></a>Kies een hostnaam uit het back-end-adres

Met deze mogelijkheid wordt de *host* -header in de aanvraag dynamisch ingesteld op de hostnaam van de back-end-pool. Er wordt gebruikgemaakt van een IP-adres of een FQDN-naam.

Deze functie helpt wanneer de domein naam van de back-end verschilt van de DNS-naam van de toepassings gateway en de back-end is afhankelijk van een specifieke host-header om naar het juiste eind punt te leiden.

Een voor beeld is het gebruik van multi tenant Services als de back-end. Een app service is een multi tenant service die gebruikmaakt van een gedeelde ruimte met één IP-adres. Dit betekent dat een app service alleen toegankelijk is via de hostnamen die zijn geconfigureerd in de instellingen van het aangepaste domein.

De aangepaste domein naam is standaard *example.azurewebsites.net*. Als u toegang wilt krijgen tot uw app service met behulp van een toepassings gateway via een hostnaam die niet expliciet is geregistreerd in de app service of via de FQDN van de toepassings gateway, overschrijft u de hostnaam in de oorspronkelijke aanvraag naar de hostnaam van de app-service. Als u dit wilt doen, schakelt u de optie **hostnaam van back-end-adres kiezen in** .

U hoeft deze instelling niet in te scha kelen voor een aangepast domein waarvan de bestaande aangepaste DNS-naam wordt toegewezen aan de app service.

> [!NOTE]
> Deze instelling is niet vereist voor App Service Environment, een specifieke implementatie.

## <a name="host-name-override"></a>Hostnaam negeren

Deze functie vervangt de *host* -header in de inkomende aanvraag op de toepassings gateway met de hostnaam die u opgeeft.

Als bijvoorbeeld *www.contoso.com* is opgegeven in de instelling voor de **hostnaam** , wordt de oorspronkelijke aanvraag * `https://appgw.eastus.cloudapp.azure.com/path1` gewijzigd in * `https://www.contoso.com/path1` wanneer de aanvraag wordt doorgestuurd naar de back-endserver.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over de back-end-pool](configuration-overview.md#back-end-pool)