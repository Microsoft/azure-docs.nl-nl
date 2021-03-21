---
title: Voorlopig verwijderen inschakelen voor alle sleutelkluisobjecten - Azure Key Vault | Microsoft Docs
description: Gebruik dit document om zacht verwijderen te gebruiken voor alle sleutelkluizen en om toepassings- en beheerwijzigingen te maken om conflictfouten te voorkomen.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: sudbalas
ms.openlocfilehash: b96f2ca4f925846bd252e5cfd35088d832f5c216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98572864"
---
# <a name="soft-delete-will-be-enabled-on-all-key-vaults"></a>Voorlopig verwijderen wordt ingeschakeld voor alle sleutelkluizen

> [!WARNING]
> Wijziging die fouten veroorzaakt: de optie om geen gebruik te maken van Voorlopig verwijderen, wordt binnenkort afgeschaft. Het wordt aangeraden dat gebruikers en beheerders van Azure Key Vault voorlopig verwijderen direct inschakelen voor hun sleutelkluizen.
>
> Voor met Azure Key Vault beheerde HSM is voorlopig verwijderen standaard ingeschakeld en kan deze functie niet worden uitgeschakeld.

Wanneer een geheim wordt verwijderd uit een sleutelkluis zonder dat voorlopig verwijderen is ingeschakeld, wordt het geheim permanent verwijderd. Gebruikers kunnen zich momenteel uitschrijven voor voorlopig verwijderen tijdens het maken van de sleutelkluis. Microsoft schakelt echter wel snel bescherming tegen voorlopig verwijderen in op alle sleutelkluizen om geheimen te beschermen tegen onbedoelde of kwaadaardige verwijdering door een gebruiker. Gebruikers kunnen zich niet langer afmelden voor of voorlopige verwijdering uitschakelen.

:::image type="content" source="../media/softdeletediagram.png" alt-text="Diagram waarin wordt weergegeven hoe een sleutelkluis wordt verwijderd met bescherming tegen voorlopig verwijderen en zonder beveiliging tegen voorlopig verwijderen.":::

Zie [Azure Key Vault: overzicht van voorlopig verwijderen](soft-delete-overview.md) voor meer informatie over de functie voor voorlopig verwijderen.

## <a name="can-my-application-work-with-soft-delete-enabled"></a>Kan mijn toepassing werken terwijl Voorlopig verwijderen is ingeschakeld?

> [!Important] 
> Lees de volgende informatie aandachtig door voordat u Voorlopig verwijderen voor uw sleutelkluizen inschakelt.

Namen van sleutelkluizen zijn globaal uniek. De namen van geheimen die zijn opgeslagen in een sleutelkluis, zijn ook uniek. De naam van een sleutelkluis of sleutelkluisobject met de status Voorlopig verwijderd, kan niet opnieuw worden gebruikt. 

Als er bijvoorbeeld programmatisch een sleutelkluis met de naam 'kluis A' wordt gemaakt en 'kluis A' later wordt verwijderd, krijgt de sleutelkluis de status voorlopig verwijderd. De toepassing kan pas een andere sleutelkluis met de naam 'kluis A' maken als de sleutelkluis uit de status Voorlopig verwijderd is gehaald. 

En als er een sleutel met de naam `test key` in 'kluis A' wordt gemaakt en de sleutel later wordt verwijderd, kan de toepassing pas een nieuwe sleutel met de naam `test key` in 'kluis A' maken als het object `test key` uit de status Voorlopig verwijderd wordt gehaald. 

Als u probeert een sleutelkluisobject te verwijderen en dit vervolgens opnieuw te maken met dezelfde naam zonder het object eerst permanent te verwijderen, kan dit leiden tot conflictfouten. Deze fouten zorgen er mogelijk voor dat uw toepassingen of automatisering mislukken. Neem contact op met uw ontwikkelteam voordat u de volgende vereiste wijzigingen aan de toepassing en het beheer aanbrengt. 

### <a name="application-changes"></a>Toepassingswijzigingen

Als uw toepassing ervan uitgaat dat voorlopig verwijderen niet is ingeschakeld en verwacht dat verwijderde geheimen of namen van sleutelkluizen direct beschikbaar zijn voor hergebruik, moet u de volgende wijzigingen aanbrengen in uw toepassingslogica.

1. Verwijder de oorspronkelijke sleutelkluis of het geheim.
1. Voer een opschoning uit van de sleutelkluis of het geheim met de status van voorlopig verwijderd.
1. Wacht tot de opschoning is voltooid. Onmiddellijk opnieuw maken kan leiden tot een conflict.
1. Maak de sleutelkluis opnieuw met dezelfde naam.
1. Als de maakbewerking nog steeds leidt tot een naamconflict, probeert u de sleutelkluis opnieuw te maken. In het ergste geval duurt het mogelijk tot 10 minuten voordat Azure DNS-records zijn bijgewerkt.

### <a name="administration-changes"></a>Beheerwijzigingen

Beveiligings-principals die toegang moeten hebben tot permanent verwijderde geheimen, moeten meer toegangsbeleidsmachtigingen krijgen om deze geheimen en de sleutelkluis op te schonen.

Schakel Azure-beleid uit voor uw sleutelkluizen dat ervoor zorgt dat voorlopig verwijderen is uitgeschakeld. Mogelijk moet u dit probleem escaleren naar een beheerder die de Azure-beleidsregels beheert die zijn toegepast op uw omgeving. Als dit beleid niet is uitgeschakeld, verliest u mogelijk de optie om nieuwe sleutelkluizen te maken binnen het bereik van het toegepaste beleid.

Als uw organisatie onderhevig is aan wettelijke nalevingsvereisten en het niet is toegestaan om verwijderde sleutelkluizen en geheimen voor langere tijd in een herstelbare staat te houden, moet u de bewaarperiode van voorlopig verwijderen aanpassen om te voldoen aan de standaarden van uw organisatie. U kunt de bewaarperiode instellen op 7 tot 90 dagen.

## <a name="procedures"></a>Procedures

### <a name="audit-your-key-vaults-to-check-if-soft-delete-is-enabled"></a>Uw sleutelkluizen controleren om te kijken of de functie voor voorlopig verwijderen is ingeschakeld

1. Meld u aan bij de Azure-portal.
1. Zoek naar **Azure Policy**.
1. Selecteer **Definities**.
1. Selecteer onder **Categorie** de optie **Key Vault** in het filter.
1. Selecteer de beleidsregel **Voorlopig verwijderen moet zijn ingeschakeld voor de sleutelkluis**.
1. Selecteer **Toewijzen**.
1. Stel het bereik in op uw abonnement.
1. Controleer of het effect van de beleidsregel is ingesteld op **Controleren**.
1. Selecteer **Controleren + maken**. Een volledige scan van uw omgeving duurt mogelijk tot 24 uur.
1. Klik in het venster **Azure Policy** op **Naleving**.
1. Selecteer het beleid dat u hebt toegepast.

U kunt nu filteren op welke sleutelkluizen voorlopig verwijderen hebben ingeschakeld (conforme resources) en voor welke sleutelkluizen voorlopig verwijderen is uitgeschakeld (niet-conforme resources).

### <a name="turn-on-soft-delete-for-an-existing-key-vault"></a>Voorlopig verwijderen inschakelen voor een bestaande sleutelkluis

1. Meld u aan bij de Azure-portal.
1. Zoek uw sleutelkluis.
1. Selecteer **Eigenschappen** onder **Instellingen**.
1. Schakel onder **Voorlopig verwijderen** de optie **Herstel van deze kluis en de objecten inschakelen** in.
1. Stel de bewaarperiode in voor voorlopig verwijderen.
1. Selecteer **Opslaan**.

### <a name="grant-purge-access-policy-permissions-to-a-security-principal"></a>Machtigingen voor het opschonen van toegangsbeleidsmachtigingen toekennen aan een beveiligings-principal

1. Meld u aan bij de Azure-portal.
1. Zoek uw sleutelkluis.
1. Selecteer **Toegangsbeleid** onder **Instellingen**.
1. Selecteer de service-principal waaraan u toegang wilt verlenen.
1. Balder door elk vervolgkeuzemenu onder **Sleutel**, **Geheim** en **Certificaatmachtigingen** tot u **Machtigingsbewerkingen** ziet. Selecteer de machtiging **Opschonen**.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="does-this-change-affect-me"></a>Heeft deze wijziging gevolgen voor mij?

Als u de voorlopig verwijderen al hebt ingeschakeld of als u geen sleutelkluisobjecten verwijdert en met dezelfde naam opnieuw probeert te maken, zult u waarschijnlijk geen verandering merken in het gedrag van de sleutelkluis.

Als u een toepassing hebt die regelmatig sleutelkluisobjecten verwijdert en met dezelfde naamconventies probeert een nieuwe kluis te maken, moet u de toepassingslogica aanpassen om het verwachte gedrag te behouden. Raadpleeg het gedeelte [Toepassingswijzigingen](#application-changes) in dit artikel.

### <a name="how-do-i-benefit-from-this-change"></a>Hoe kan ik voordeel hebben van deze wijziging?

Met de beveiliging door voorlopig verwijderen beschikt uw organisatie over een extra beveiligingslaag die bescherming biedt tegen onbedoelde of kwaadwillende verwijdering. Als sleutelkluisbeheerder kunt u de toegang beperken tot zowel herstelmachtigingen als machtigingen voor opschonen.

Als een gebruiker per ongeluk een sleutelkluis of geheim verwijdert, kunt u hem of haar toegangsmachtigingen verlenen om het geheim zelf te herstellen zonder dat u het risico loopt dat het geheim of de sleutelkluis permanent wordt verwijderd. Dit self-service proces vermindert de downtime in uw omgeving en garandeert de beschikbaarheid van geheimen.

### <a name="how-do-i-find-out-if-i-need-to-take-action"></a>Hoe kan ik erachter komen of ik actie moet ondernemen?

Volg de stappen in het gedeelte [Controleren of voorlopig verwijderen is ingeschakeld in uw sleutelkluizen](#audit-your-key-vaults-to-check-if-soft-delete-is-enabled) in dit artikel. Deze wijziging is van invloed op elke sleutelkluis waarvoor voorlopig verwijderen nog niet is ingeschakeld.

### <a name="what-action-do-i-need-to-take"></a>Welke actie moet ik nemen?

Nadat u hebt bevestigd dat u geen wijzigingen hoeft door te voeren in uw toepassingslogica, schakelt u voorlopig verwijderen voor al uw sleutelkluizen in.

### <a name="when-do-i-need-to-take-action"></a>Wanneer moet ik actie ondernemen?

Om ervoor te zorgen dat uw toepassingen niet worden beïnvloed, schakelt u de optie voor voorlopig verwijderen zo snel mogelijk in voor al uw sleutelkluizen.

## <a name="next-steps"></a>Volgende stappen

- Stuur een e-mail naar [akvsoftdelete@microsoft.com](mailto:akvsoftdelete@microsoft.com) als u nog vragen hebt over deze wijziging.
- Lees het [overzicht van voorlopig verwijderen](soft-delete-overview.md).
