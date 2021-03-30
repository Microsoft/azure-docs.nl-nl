---
title: 'Azure AD Connect Sync: technische concepten | Microsoft Docs'
description: Hierin worden de technische concepten van Azure AD Connect Sync beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01e53b30a4c27296e30e031ffb771697afa8e1e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87019672"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect-synchronisatie: technische concepten
Dit artikel bevat een samen vatting van het onderwerp [over architectuur](how-to-connect-sync-technical-concepts.md).

Azure AD Connect synchronisatie bouwt voort op een Solid-synchronisatie platform van de werkmap.
In de volgende secties worden de concepten geïntroduceerd voor het synchroniseren van de werkmap.
De Azure Active Directory Sync Services biedt het volgende platform voor het maken van verbinding met gegevens bronnen, het synchroniseren van gegevens tussen gegevens bronnen en het inrichten en verwijderen van identiteiten op basis van MIIS (micro soft Identity Integration Server), ILM (Identity Lifecycle Manager) en FIM (Forefront Identity Manager).

![Technische concepten](./media/how-to-connect-sync-technical-concepts/scenario.png)

In de volgende secties vindt u meer informatie over de volgende aspecten van de FIM-synchronisatie service:

* Connector
* Kenmerkstroom
* Connector ruimte
* Metaverse
* Inrichten

## <a name="connector"></a>Connector
De code modules die worden gebruikt om te communiceren met een verbonden Directory worden Connect oren genoemd (voorheen bekend als beheer agenten (MAs)).

Deze worden geïnstalleerd op de computer waarop Azure AD Connect synchronisatie wordt uitgevoerd. De connectors bieden de mogelijkheid om te praten met externe systeem protocollen in plaats van te vertrouwen op de implementatie van gespecialiseerde agents. Dit betekent dat het risico en de implementatie tijden, met name bij het omgaan met essentiële toepassingen en systemen, worden verminderd.

In de bovenstaande afbeelding is de connector synoniem met de ruimte van de connector, maar omvat alle communicatie met het externe systeem.

De connector is verantwoordelijk voor alle import-en export functionaliteit voor het systeem en maakt ontwikkel aars vrij om inzicht te krijgen in de manier waarop een systeem eigen verbinding kan maken met behulp van declaratieve inrichting voor het aanpassen van gegevens transformaties.

Invoer en uitvoer worden alleen uitgevoerd wanneer deze zijn gepland, waardoor er verdere isolatie mogelijk is van wijzigingen die in het systeem optreden, omdat wijzigingen niet automatisch worden door gegeven aan de verbonden gegevens bron. Daarnaast kunnen ontwikkel aars ook hun eigen connectors maken voor het verbinden met vrijwel elke gegevens bron.

## <a name="attribute-flow"></a>Kenmerkstroom
Het omgekeerde is de geconsolideerde weer gave van alle gekoppelde identiteiten uit de ruimten van de naburige connector. In bovenstaande afbeelding wordt kenmerk stroom weer gegeven met lijnen met pijl punten voor zowel binnenkomende als uitgaande stroom. Kenmerk stroom is het proces van het kopiëren of transformeren van gegevens van het ene naar het andere systeem en alle kenmerk stromen (inkomend of uitgaand).

Er treedt een kenmerk stroom op tussen de connector ruimte en de omgekeerde bidirectionele wanneer synchronisatie (volledige of Delta) bewerkingen zijn gepland voor uitvoering.

Kenmerk stroom treedt alleen op wanneer deze synchronisaties worden uitgevoerd. Kenmerk stromen worden gedefinieerd in synchronisatie regels. Deze kunnen worden inkomend (ISR in de bovenstaande afbeelding) of uitgaand (OSR in de bovenstaande afbeelding).

## <a name="connected-system"></a>Verbonden systeem
Verbonden systeem (ook wel Connected Directory) verwijst naar het externe systeem Azure AD Connect synchronisatie is verbonden met en lezen en schrijven van identiteits gegevens van en naar.

## <a name="connector-space"></a>Connector ruimte
Elke verbonden gegevens bron wordt weer gegeven als een gefilterde subset van de objecten en kenmerken in de connector ruimte.
Hierdoor kan de synchronisatie service lokaal worden uitgevoerd zonder dat er contact moet worden opgenomen met het externe systeem bij het synchroniseren van de objecten en wordt de interactie beperkt tot invoer en uitvoer.

Wanneer de gegevens bron en de connector kunnen voorzien in een lijst met wijzigingen (een Delta-import), neemt de operationele efficiëntie toe als alleen wijzigingen sinds de laatste polling cyclus wordt uitgewisseld. Met de connector ruimte wordt de verbonden gegevens bron geïsoleerd van wijzigingen die automatisch worden door gegeven, door te vereisen dat het connector schema importeert en exporteert. Deze toegevoegde verzekering verleent u gemoeds rust tijdens het testen, bekijken of bevestigen van de volgende update.

## <a name="metaverse"></a>Metaverse
Het omgekeerde is de geconsolideerde weer gave van alle gekoppelde identiteiten uit de ruimten van de naburige connector.

Aangezien identiteiten samen worden gekoppeld en de instantie wordt toegewezen voor diverse kenmerken via import stroom toewijzingen, begint het centrale-object van de tekst van de gegevens van meerdere systemen. Met de toewijzing van deze object kenmerk stroom worden gegevens overgedragen aan uitgaande systemen.

Objecten worden gemaakt wanneer een gezaghebbend systeem projecten in de tekst van de inverse doorwerkt. Zodra alle verbindingen zijn verwijderd, wordt het omgekeerde object verwijderd.

Objecten in de omgekeerde tekst kunnen niet rechtstreeks worden bewerkt. Alle gegevens in het object moeten worden bijgedragen via de kenmerk stroom. De omgekeerde onderhoudt permanente connectors met elk connector gebied. Deze connectors hoeven niet opnieuw te worden geëvalueerd voor elke synchronisatie-uitvoering. Dit betekent dat Azure AD Connect Sync niet elke keer het overeenkomende externe object moet vinden. Dit voor komt dat dure agents wijzigingen in kenmerken die normaal gesp roken verantwoordelijk zouden zijn voor het correleren van de objecten te voor komen.

Bij het detecteren van nieuwe gegevens bronnen die mogelijk bestaande objecten bevatten die moeten worden beheerd, gebruikt Azure AD Connect sync een proces dat een joinlijn wordt genoemd om potentiële kandidaten te evalueren waarmee een koppeling tot stand kan worden gebracht.
Zodra de koppeling tot stand is gebracht, wordt deze evaluatie niet opnieuw uitgevoerd en wordt de normale kenmerk stroom mogelijk gemaakt tussen de externe verbonden gegevens bron en het omgekeerde.

## <a name="provisioning"></a>Inrichten
Wanneer een gezaghebbende bron projecten een nieuw object in het omgekeerde een nieuwe connector ruimte-object kan maken in een andere connector die een met downstream verbonden gegevens bron vertegenwoordigt.

Dit maakt inherent een koppeling en de kenmerk stroom kan in twee richtingen worden voortgezet.

Telkens wanneer een regel bepaalt dat er een nieuw connector-ruimte object moet worden gemaakt, wordt het inrichten genoemd. Omdat deze bewerking echter alleen plaatsvindt binnen de connector ruimte, wordt deze niet overgedragen naar de verbonden gegevens bron totdat er een export wordt uitgevoerd.

## <a name="additional-resources"></a>Aanvullende resources
* [Azure AD Connect synchronisatie: synchronisatie opties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
