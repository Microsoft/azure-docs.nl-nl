---
title: 'Azure AD Connect Sync: informatie over gebruikers, groepen en contact personen | Microsoft Docs'
description: Hierin worden gebruikers, groepen en contact personen beschreven in Azure AD Connect synchronisatie.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e015f7937db6788aa4473a8a04434121299901e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861779"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect Sync: informatie over gebruikers, groepen en contact personen
Er zijn verschillende redenen waarom u meerdere Active Directory forests zou hebben en er verschillende implementatie topologieën zijn. Algemene modellen bevatten een account-resource-implementatie en algemene sync'ed-forests na een fusie & overname. Maar zelfs als er zuivere modellen zijn, zijn hybride modellen ook gebruikelijk. De standaard configuratie in Azure AD Connect-synchronisatie gaat niet uit van een bepaald model, maar afhankelijk van de manier waarop gebruikers overeenkomst in de installatie handleiding is geselecteerd, kan verschillende gedragingen worden waargenomen.

In dit onderwerp gaan we uit van de manier waarop de standaard configuratie zich in bepaalde topologieën gedraagt. De configuratie wordt door lopen en de editor voor synchronisatie regels kan worden gebruikt om de configuratie te bekijken.

Er zijn enkele algemene regels die de configuratie veronderstelt:
* Ongeacht de volg orde die we importeren vanuit de bron Active Directory, moet het eind resultaat altijd hetzelfde zijn.
* Bij een actief account worden aanmeldings gegevens altijd bijgemeld, waaronder **userPrincipalName** en **Source Anchor**.
* Met een uitgeschakeld account worden userPrincipalName en source Anchor gedraagt, tenzij het een gekoppeld postvak is, als er geen actief account is gevonden.
* Een account met een gekoppeld postvak wordt nooit gebruikt voor userPrincipalName en source Anchor. Er wordt van uitgegaan dat een actief account later wordt gevonden.
* Een contact-object kan worden ingericht naar Azure AD als contact persoon of als gebruiker. U weet pas zeker totdat alle bron Active Directory bossen zijn verwerkt.

## <a name="groups"></a>Groepen
Belang rijke punten waar u rekening mee moet houden bij het synchroniseren van groepen van Active Directory naar Azure AD:

* Azure AD Connect ingebouwde beveiligings groepen uit te sluiten van Directory synchronisatie.

* Azure AD Connect biedt geen ondersteuning voor het synchroniseren van [primaire groepslid maatschappen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11)) naar Azure AD.

* Azure AD Connect biedt geen ondersteuning voor het synchroniseren van [dynamische distributie groepslid maatschappen](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups) naar Azure AD.

* Een Active Directory groep als een e-mail groep synchroniseren met Azure AD:

    * Als het kenmerk *proxyAddress attribuut* van de groep leeg is, moet het *e-mail* kenmerk een waarde hebben

    * Als het kenmerk *proxyAddress attribuut* van de groep niet-leeg is, moet deze ten minste één SMTP-proxy adres waarde bevatten. Hier volgen enkele voorbeelden:
    
      * Een Active Directory groep waarvan het kenmerk proxyAddress attribuut de waarde *{"een x500:/0 = contoso. com/OE = users/CN = testgroup"}* heeft, wordt in azure AD geen e-mail ingeschakeld. Het bevat geen SMTP-adres.
      
      * Een Active Directory groep waarvan het kenmerk proxyAddress attribuut waarden *{"een x500:/0 = contoso. com/OE = users/CN = testgroup", "SMTP: janjansen \@ contoso.com"} heeft,* wordt in azure AD ingeschakeld als e-mail adres.
      
      * Een Active Directory-groep waarvan het kenmerk proxyAddress attribuut waarden *{"een x500:/0 = contoso. com/OE = users/CN = testgroup", "SMTP: janjansen \@ contoso.com"} heeft,* wordt ook in azure AD ingeschakeld.

## <a name="contacts"></a>Contactpersonen
Het hebben van contact personen die een gebruiker in een ander forest vertegenwoordigen, is gebruikelijk nadat een fusie & verwerving waarbij een GALSync-oplossing twee of meer Exchange-forests overbrugget. Het contact-object wordt altijd aan de hand van de ruimte van de connector aan de tekst toegevoegd met behulp van het kenmerk mail. Als er al een contact object of gebruikers object is met hetzelfde e-mail adres, worden de objecten samengevoegd. Dit wordt geconfigureerd in de regel **in vanuit AD: contact opnemen met**. Er is ook een regel genoemd **in van AD: contact opnemen met common** met een kenmerk stroom naar het **sourceObjectType** kenmerk van de permanente **contact persoon**. Deze regel heeft een zeer lage prioriteit, dus als een gebruikers object lid is van hetzelfde omgekeerde object, wordt de waarde gebruiker door de regel **in van AD: gemeen schappelijk door de gebruiker gebruikt** . Met deze regel krijgt dit kenmerk de waarde contact als er geen gebruiker is toegevoegd en de waarde gebruiker als er ten minste één gebruiker is gevonden.

Voor het inrichten van een object voor Azure AD wordt met de uitgaande regel **out naar Aad – contact opnemen** gemaakt een object contact opgenomen als het **sourceObjectType** -kenmerk is ingesteld op **contact persoon**. Als dit kenmerk is ingesteld op **gebruiker**, wordt in plaats daarvan in de regel **naar Aad** een gebruikers object gemaakt.
Het is mogelijk dat een object wordt gepromoveerd van een contact persoon naar een gebruiker wanneer er meer Active Directory-bronnen worden geïmporteerd en gesynchroniseerd.

In een GALSync-topologie worden bijvoorbeeld contact objecten voor iedereen in het tweede forest gevonden bij het importeren van het eerste forest. Hiermee worden nieuwe contact objecten in de AAD-connector gemaakt. Wanneer we het tweede forest later importeren en synchroniseren, zullen we de echte gebruikers vinden en deze koppelen aan de bestaande omgekeerde objecten. Vervolgens wordt het contact object in AAD verwijderd en wordt in plaats daarvan een nieuw gebruikers object gemaakt.

Als u een topologie hebt waarin gebruikers worden weer gegeven als contact personen, zorg er dan voor dat u selecteert dat gebruikers overeenkomen met het kenmerk mail in de installatie handleiding. Als u een andere optie selecteert, hebt u een order afhankelijke configuratie. Contact objecten worden altijd gekoppeld aan het e-mail kenmerk, maar gebruikers objecten worden alleen toegevoegd aan het e-mail kenmerk als deze optie is geselecteerd in de installatie handleiding. U kunt vervolgens op twee verschillende objecten in de omgekeerde met hetzelfde e-mail kenmerk eindigen als het object contact persoon is geïmporteerd vóór het gebruikers object. Tijdens het exporteren naar Azure AD wordt een fout gegenereerd. Dit gedrag is inherent aan het ontwerp en duidt op onjuiste gegevens of de topologie is onjuist geïdentificeerd tijdens de installatie.

## <a name="disabled-accounts"></a>Uitgeschakelde accounts
Uitgeschakelde accounts worden ook gesynchroniseerd met Azure AD. Uitgeschakelde accounts zijn gebruikelijk om resources te vertegenwoordigen in Exchange, bijvoorbeeld Vergader zalen. De uitzonde ring is gebruikers met een gekoppeld postvak. zoals eerder vermeld, wordt er nooit een account in azure AD ingericht.

De veronderstelling is dat als er een uitgeschakeld gebruikers account wordt gevonden, er nog geen ander actief account wordt gedetecteerd en dat het object wordt ingericht in azure AD met de userPrincipalName en source Anchor gevonden. Als een ander actief account wordt toegevoegd aan hetzelfde omgekeerde object, worden de userPrincipalName en source Anchor gebruikt.

## <a name="changing-sourceanchor"></a>Source Anchor wijzigen
Wanneer een object naar Azure AD is geëxporteerd, mag het source Anchor niet meer worden gewijzigd. Wanneer het object is geëxporteerd, wordt het **cloudSourceAnchor** -kenmerk dat is ingesteld met de **Source Anchor** -waarde die is geaccepteerd door Azure AD. Als **Source Anchor** is gewijzigd en niet overeenkomt met **cloudSourceAnchor**, wordt met de regel **uit voor Aad-gebruiker toevoegen** het fout **kenmerk source Anchor** verwijderd. In dit geval moeten de configuratie of de gegevens worden gecorrigeerd, zodat dezelfde source anchor in de omgekeerde tekst opnieuw wordt weer gegeven voordat het object opnieuw kan worden gesynchroniseerd.

## <a name="additional-resources"></a>Aanvullende resources
* [Azure AD Connect synchronisatie: synchronisatie opties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
