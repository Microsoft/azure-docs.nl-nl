---
title: Identiteitsmodel
titleSuffix: An Azure Communication Services concept
description: Meer informatie over de identiteits-en toegangs tokens
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 254d35331459e70ad56bcef43569f51ff6f50a93
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656207"
---
# <a name="identity-model"></a>Identiteitsmodel

Azure Communication Services is een id-neutraal-service. Dit ontwerp biedt meerdere voor delen:

- Bestaande identiteiten uit uw identiteits beheersysteem opnieuw gebruiken
- Biedt flexibiliteit voor integratie scenario's
- Houdt uw identiteiten privé in azure Communication Services

In plaats van de gegevens in uw systeem te dupliceren, houdt u de toewijzings relatie bij die uw bedrijf nodig heeft. U kunt bijvoorbeeld identiteiten 1:1, 1: N, N:1, N:M. Externe id's, zoals telefoon nummers, gebruikers, apparaten, toepassingen en GUID'S, kunnen niet worden gebruikt voor identiteit in azure Communication Services. Toegangs tokens die worden gegenereerd voor een Azure Communication Services-identiteit worden gebruikt voor toegang tot primitieven, zoals chatten of aanroepen.

## <a name="identity"></a>Identiteit

U kunt identiteiten maken met behulp van de Azure Communication Services-identiteits bibliotheek. Een identiteit fungeert als een id in gesp rekken. Het wordt gebruikt om toegangs tokens te maken. Dezelfde identiteit kan deel uitmaken van meerdere gelijktijdige sessies op meerdere apparaten. Een identiteit kan tegelijkertijd meerdere actieve toegangs tokens hebben.

Als u een identiteit, resource of abonnement verwijdert, worden alle toegangs tokens ongeldig. Deze actie verwijdert ook alle gegevens die zijn opgeslagen voor de identiteit. Een verwijderde identiteit kan geen nieuwe toegangs tokens maken of eerder opgeslagen gegevens openen (bijvoorbeeld chat berichten).

Er worden geen kosten in rekening gebracht voor het aantal identiteiten dat u hebt. In plaats daarvan worden er kosten in rekening gebracht voor het gebruik van primitieven. Het aantal identiteiten hoeft niet te worden beperkt hoe u de identiteiten van uw toepassingen toewijst aan de Azure Communication Services-identiteiten.

Met de vrijheid van toewijzing is de privacy-verantwoordelijkheid. Als een gebruiker van uw systeem wil verwijderen, moet u alle identiteiten verwijderen die aan die gebruiker zijn gekoppeld.

Azure Communication Services biedt geen speciale identiteiten voor anonieme gebruikers. Het houdt geen toewijzing tussen de gebruikers en identiteiten, en kan niet bepalen of een identiteit anoniem is. U kunt het identiteits concept ontwerpen dat aan uw behoeften voldoet. Onze aanbeveling is een nieuwe identiteit te maken voor elke anonieme gebruiker op elke toepassing.

Iedereen met een geldig toegangs token kan toegang krijgen tot de huidige identiteits inhoud. Gebruikers hebben bijvoorbeeld toegang tot chat berichten die ze hebben verzonden. De toegang is beperkt tot scopes die deel uitmaken van het toegangs token. Zie de sectie [toegangs tokens](#access-tokens) in dit artikel voor meer informatie.

### <a name="identity-mapping"></a>Identiteits toewijzing

Azure Communication Services repliceert de functionaliteit van het Azure Identity Management-systeem niet. Het biedt klanten geen manier om klantspecifieke identiteiten te gebruiken. Klanten kunnen bijvoorbeeld geen telefoon nummer of e-mail adres gebruiken. In plaats daarvan biedt Azure Communication Services unieke id's. U kunt deze unieke id's toewijzen aan de identiteiten van uw toepassing. In azure Communication Services wordt geen informatie opgeslagen die de echte identiteit van uw gebruikers kan onthullen.

Plan gebruikers uit uw identiteits domein toe te wijzen aan Azure Communication Services-identiteiten om te voor komen dat informatie in uw systeem wordt gedupliceerd. U kunt elk soort patroon volgen. U kunt bijvoorbeeld 1:1, 1: N, N:1 of M:N. gebruiken Bepaal of één gebruiker is toegewezen aan een enkele identiteit of aan meerdere identiteiten.

Wanneer er een nieuwe identiteit wordt gemaakt, slaat u de toewijzing ervan op in de gebruikers of gebruikers van uw toepassing. Omdat identiteiten toegangs tokens vereisen om primitieven te gebruiken, moet de identiteit bekend zijn bij de gebruiker of gebruikers van uw toepassing.

Als u een relationele data base gebruikt om gebruikers gegevens op te slaan, kunt u uw ontwerp aanpassen op basis van uw toewijzings scenario. Voor scenario's waarin 1:1 of N:1 is toegewezen, wilt u mogelijk een `CommunicationServicesId` kolom toevoegen aan de tabel om uw Azure Communication Services-identiteit op te slaan. In scenario's waarin de relatie 1: N of N:M wordt gebruikt, kunt u overwegen om een afzonderlijke tabel in de relationele data base te maken.

## <a name="access-tokens"></a>Toegangstokens

Een toegangs token is een JSON Web Token (JWT) die kan worden gebruikt om toegang te krijgen tot de Azure Communication Service-primitieven. Een toegangs token dat is uitgegeven, heeft integriteits beveiliging. Dat wil zeggen dat de claims niet kunnen worden gewijzigd nadat deze is uitgegeven. Een hand matige wijziging van eigenschappen, zoals identiteit, verval datum of scopes, zal het toegangs token ongeldig maken. Als primitieven worden gebruikt met ongeldig tokens, wordt de toegang tot de primitieven geweigerd.

De eigenschappen van een toegangs token zijn:
* Persoon.
* Verval.
* Beveiligingsbereiken.

Een toegangs token is altijd 24 uur geldig. Nadat deze is verlopen, wordt het toegangs token ongeldig en kan het niet worden gebruikt voor toegang tot een primitieve.

Een identiteit moet een manier hebben om een nieuw toegangs token aan een server zijde-service aan te vragen. De *bereik* parameter definieert een niet-lege set primitieven die kunnen worden gebruikt. Azure Communication Services ondersteunt de volgende bereiken voor toegangs tokens.

|Naam|Beschrijving|
|---|---|
|Chat|  Verleent de mogelijkheid om deel te nemen aan een chatsessie|
|VoIP|  Hiermee verleent u de mogelijkheid om identiteiten en telefoon nummers aan te roepen|


Als u een toegangs token wilt intrekken vóór de verloop tijd, gebruikt u de Azure Communication Services-identiteits bibliotheek. Het intrekken van het token is niet direct. Het duurt Maxi maal 15 minuten om door te geven. Als u een identiteit, resource of abonnement verwijdert, worden alle toegangs tokens ingetrokken.

Als u de toegang van een gebruiker tot specifieke functionaliteit wilt verwijderen, trekt u alle toegangs tokens in. Vervolgens geeft u een nieuw toegangs token met een beperkt aantal bereiken.

In azure Communication Services trekt een rotatie van de toegangs sleutels alle actieve toegangs tokens die zijn gemaakt met behulp van een voormalige toegangs sleutel. Alle identiteiten hebben geen toegang meer tot Azure Communication Services en ze moeten nieuwe toegangs tokens uitgeven.

We raden u aan toegangs tokens te verlenen aan de server zijde en niet in de toepassing van de client. De reden hiervoor is dat een toegangs sleutel of een beheerde identiteit is vereist. Uit veiligheids overwegingen wordt het delen van toegangs sleutels met de client toepassing niet aanbevolen.

De client toepassing moet een vertrouwd service-eind punt gebruiken dat uw clients kan verifiëren. Het eind punt moet namens u toegangs tokens uitgeven. Zie [client-en server architectuur](./client-and-server-architecture.md)voor meer informatie.

Als u toegangs tokens in de cache opslaat voor een back-upopslag, kunt u het beste versleuteling gebruiken. Een toegangs token is gevoelige gegevens. Het kan worden gebruikt voor schadelijke activiteiten als deze niet zijn beveiligd. Iemand met een toegangs token kan de SDK starten en toegang krijgen tot de API. De toegankelijke API wordt alleen beperkt op basis van de bereiken die het toegangs token heeft. We raden u aan toegangs tokens met alleen de vereiste bereiken uit te geven.

## <a name="next-steps"></a>Volgende stappen

* Zie [toegangs tokens maken en beheren](../quickstarts/access-tokens.md)voor een inleiding tot het beheer van toegangs tokens.
* Zie [verifiëren bij Azure Communication Services](./authentication.md)voor een inleiding tot verificatie.
* Zie [Beschik baarheid van regio's en gegevens locatie](./privacy.md)voor een inleiding tot gegevens locatie en privacy.