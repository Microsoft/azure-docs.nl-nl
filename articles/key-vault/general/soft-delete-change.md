---
title: Voorlopig verwijderen inschakelen voor alle Azure-sleutelkluizen | Microsoft Docs
description: Gebruik dit document om voorlopig verwijderen te implementeren voor alle sleutelkluizen.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: e512cccdbfdc56500fa7c69372ca38f59d3195c2
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590083"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Voorlopig verwijderen wordt ingeschakeld voor alle sleutelkluizen

> [!WARNING]
> **Wijziging die fouten veroorzaakt**: De optie om geen gebruik te maken van Voorlopig verwijderen, wordt binnenkort afgeschaft. Het wordt aangeraden dat gebruikers en beheerders van Azure Key Vault voorlopig verwijderen direct inschakelen voor hun sleutelkluizen.
>
> Voor beheerde HSM is voorlopig verwijderen standaard ingeschakeld en kan deze functie niet worden uitgeschakeld.

Wanneer een geheim wordt verwijderd uit een sleutelkluis zonder dat voorlopig verwijderen is ingeschakeld, wordt het geheim permanent verwijderd. Gebruikers kunnen zich op dit moment afmelden voor voorlopig verwijderen tijdens het maken van een sleutelkluis, maar om uw geheimen te beschermen tegen onbedoeld of kwaadaardig verwijderen door een gebruiker zal Microsoft binnenkort voorlopig verwijderen inschakelen voor **alle** sleutelkluizen en hebben gebruikers niet langer de optie om zich uit te schrijven voor deze functie of om deze uit te schakelen.

:::image type="content" source="../media/softdeletediagram.png" alt-text="<alt-tekst>":::

Zie [Azure Key Vault: overzicht van voorlopig verwijderen](soft-delete-overview.md) voor meer informatie over de functie voor voorlopig verwijderen.

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Kan mijn toepassing werken terwijl Voorlopig verwijderen is ingeschakeld?

> [!Important] 
> **Lees de volgende informatie aandachtig door voordat u Voorlopig verwijderen voor uw sleutelkluizen inschakelt**

Namen van sleutelkluizen zijn globaal uniek. De namen van geheimen die zijn opgeslagen in een sleutelkluis, zijn ook uniek. De naam van een sleutelkluis of sleutelkluisobject met de status Voorlopig verwijderd, kan niet opnieuw worden gebruikt. 

**Voorbeeld 1** Als er programmatisch een sleutelkluis met de naam kluis A wordt gemaakt en kluis A later wordt verwijderd. De sleutelkluis gaat over in de status Voorlopig verwijderd. De toepassing kan pas een andere sleutelkluis met de naam Kluis A maken als de sleutelkluis uit de status Voorlopig verwijderd is gehaald. 

**Voorbeeld 2** Als er een sleutel met de naam `test key` in sleutelkluis A wordt gemaakt en de sleutel later uit kluis A wordt verwijderd, kan de toepassing pas een nieuwe sleutel met de naam `test key` in sleutelkluis A maken als het `test key`-object uit de status Voorlopig verwijderd wordt gehaald. 

Dit kan conflictfouten veroorzaken als u probeert een sleutelkluisobject te verwijderen en dit vervolgens opnieuw te maken met dezelfde naam zonder het object eerst permanent te verwijderen. Dit kan ertoe leiden dat uw toepassing of automatisering mislukt. Neem contact op met uw ontwikkelteam voordat u hieronder de vereiste wijzigingen aan de toepassing en het beheer aanbrengt. 

### <a name="application-changes"></a>Toepassingswijzigingen

Als uw toepassing ervan uitgaat dat voorlopig verwijderen niet is ingeschakeld en verwacht dat verwijderde geheimen of namen van sleutelkluizen direct beschikbaar zijn voor hergebruik, moeten in de toepassingslogica de volgende wijzigingen worden aangebracht om deze wijziging te verwerken.

1. Verwijder de oorspronkelijke sleutelkluis of het geheim
2. Voer een opschoning uit van de sleutelkluis of het geheim met de status van voorlopig verwijderd.
3. Wachten: direct opnieuw maken kan een conflict veroorzaken.
4. Maak de sleutelkluis opnieuw met dezelfde naam.
5. Implementeer opnieuw proberen als bij het maken van de bewerking nog steeds een naamsconflict optreedt. In het slechtste geval kan het tot tien minuten duren voordat DNS-records zijn bijgewerkt.

### <a name="administration-changes"></a>Beheerwijzigingen

Beveiligings-principals die toegang moeten hebben tot permanent verwijderde geheimen, moeten aanvullende toegangsbeleidsmachtigingen krijgen om deze geheimen en de sleutelkluis op te schonen.

Als er een Azure-beleid is ingesteld voor de sleutelkluizen die vereisen dat voorlopig verwijderen is uitgeschakeld, moet dit beleid worden uitgeschakeld.  Mogelijk moet u dit probleem escaleren naar een beheerder die de Azure-beleidsregels beheert die zijn toegepast op uw omgeving. Als dit beleid niet is uitgeschakeld, verliest u mogelijk de optie om nieuwe sleutelkluizen te maken binnen het bereik van het toegepaste beleid.

Als uw organisatie onderhevig is aan wettelijke nalevingsvereisten en het niet is toegestaan om verwijderde sleutelkluizen en geheimen voor langere tijd in een herstelbare staat te houden, moet u de bewaarperiode van voorlopig verwijderen aanpassen (tussen 7 – 90 dagen) om te voldoen aan de standaarden van uw organisatie.

## <a name="procedures"></a>Procedures

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Uw sleutelkluizen controleren om te kijken of de functie voor voorlopig verwijderen is ingeschakeld

1. Meld u aan bij Azure Portal.
2. Zoek naar 'Azure Policy'.
3. Selecteer Definities.
4. Selecteer onder Categorie de optie Sleutelkluis in het filter.
5. Selecteer de beleidsregel Voorlopig verwijderen moet zijn ingeschakeld voor de sleutelkluis.
6. Klik op Toewijzen.
7. Stel het bereik in op uw abonnement.
8. Controleer of het effect van de beleidsregel is ingesteld op Controleren.
9. Selecteer Beoordelen en maken.
10. Het kan tot 24 uur duren voordat een volledige scan van uw omgeving is voltooid.
11. Klik op de blade Azure Policy op Naleving.
12. Selecteer het beleid dat u hebt toegepast.

U moet nu kunnen filteren op welke sleutelkluizen voorlopig verwijderen hebben ingeschakeld (conforme resources) en voor welke sleutelkluizen voorlopig verwijderen is uitgeschakeld (niet-conforme resources).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Voorlopig verwijderen inschakelen voor een bestaande sleutelkluis

1. Meld u aan bij Azure Portal.
2. Zoek uw sleutelkluis.
3. Selecteer Eigenschappen onder Instellingen.
4. Schakel onder Voorlopig verwijderen het keuzerondje in voor het activeren van herstel voor deze kluis en de bijbehorende objecten.
5. Stel de bewaarperiode in voor voorlopig verwijderen.
6. Selecteer Opslaan.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Machtigingen voor het opschonen van toegangsbeleidsmachtigingen toekennen aan een beveiligings-principal

1. Meld u aan bij Azure Portal.
2. Zoek uw sleutelkluis.
3. Selecteer Toegangsbeleid onder Instellingen.
4. Selecteer de service-principal waaraan u toegang wilt verlenen.
5. Scrol in elke vervolgkeuzelijst onder Sleutel, Geheim en Certificaatmachtigingen naar Geprivilegieerde bewerkingen en selecteer de machtiging Opschonen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-this-change-affect-me"></a>Heeft deze wijziging gevolgen voor mij?

Als u de voorlopig verwijderen al hebt ingeschakeld of als u geen sleutelkluisobjecten verwijdert en met dezelfde naam opnieuw probeert te maken, zult u waarschijnlijk geen verandering merken in het gedrag van de sleutelkluis.

Als u een toepassing hebt die regelmatig sleutelkluisobjecten verwijdert en met dezelfde naamconventies probeert een nieuwe kluis te maken, moet u de toepassingslogica aanpassen om het verwachte gedrag te behouden. Zie hiervoor 'Hoe moet ik reageren op wijzigingen die fouten veroorzaken?' eerder in dit artikel.

### <a name="how-do-i-benefit-from-this-change"></a>Hoe kan ik voordeel hebben van deze wijziging?

Met de beveiliging door voorlopig verwijderen beschikt uw organisatie over een extra beveiligingslaag die bescherming biedt tegen onbedoelde of kwaadwillende verwijdering. Als sleutelkluisbeheerder kunt u de toegang beperken tot zowel herstelmachtigingen als machtigingen voor opschonen.

Als een gebruiker per ongeluk een sleutelkluis of geheim verwijdert, kunt u hem of haar toegangsmachtigingen verlenen om het geheim zelf te herstellen zonder dat u het risico loopt dat het geheim of de sleutelkluis permanent wordt verwijderd. Dit self-service proces vermindert de downtime in uw omgeving en garandeert de beschikbaarheid van geheimen.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Hoe kan ik erachter komen of ik actie moet ondernemen?

Volg de bovenstaande stappen in de sectie 'Uw sleutelkluizen controleren om te kijken of de functie voor voorlopig verwijderen is ingeschakeld'. Deze wijziging is van invloed op elke sleutelkluis waarvoor voorlopig verwijderen niet is ingeschakeld. Er komen binnenkort extra hulpprogramma's beschikbaar om controles uit te voeren en dit document zal dan worden bijgewerkt.

### <a name="what-action-do-i-need-to-take"></a>Welke actie moet ik nemen?

Zorg ervoor dat u geen wijzigingen hoeft aan te brengen in de toepassingslogica. Zodra u dat hebt bevestigd, schakelt u voorlopig verwijderen in voor al uw sleutelkluizen.

### <a name="by-when-do-i-need-to-take-action"></a>Tot wanneer heb ik de tijd om actie te ondernemen?

Om ervoor te zorgen dat uw toepassingen niet worden beïnvloed, schakelt u de optie voor voorlopig verwijderen zo snel mogelijk in voor al uw sleutelkluizen.

## <a name="next-steps"></a>Volgende stappen

- Stuur een e-mail naar [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) als u nog vragen hebt over deze wijziging.
- Lees het [overzicht van voorlopig verwijderen](soft-delete-overview.md).
