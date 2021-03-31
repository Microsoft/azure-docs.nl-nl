---
title: Azure Block Chain Service consortium
description: Overzicht van de manier waarop Azure Block Chain Service consortium Block chain-netwerken implementeert.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84712527"
---
# <a name="azure-blockchain-service-consortium"></a>Azure Block Chain Service consortium

Met de Azure Block Chain-Service kunt u particuliere consortium Block chain-netwerken maken waarbij elk block chain-netwerk kan worden beperkt tot specifieke deel nemers in het netwerk. Alleen deel nemers in het particuliere consortium Block chain-netwerk kunnen de Block Chain bekijken en ermee werken. Consortium netwerken in azure Block Chain service kunnen twee soorten rollen van deel nemers bevatten:

* Deel nemers met **beheerders** rechten die acties voor consortium beheer kunnen uitvoeren en kunnen deel nemen aan block Chain-trans acties.

* **Gebruikers** : deel nemers die geen consortium beheer actie kunnen ondernemen, maar kunnen deel nemen aan block Chain-trans acties.

Consortium netwerken kunnen een combi natie zijn van deel nemer rollen en kunnen een wille keurig aantal van elk type rol hebben. Er moet ten minste één beheerder zijn.

In het volgende diagram ziet u een consortium netwerk met meerdere deel nemers:

![Netwerk diagram van particulier consortium](./media/consortium/network-diagram.png)

Met consortium Management in azure Block Chain Service kunt u deel nemers in het consortium netwerk beheren. Het beheer van het consortium is gebaseerd op het consensus model van het netwerk. In de huidige preview-versie biedt Azure Block Chain service een gecentraliseerd consensus model voor consortium beheer. Alle bevoegde deel nemers met een beheer functie kunnen consortium beheer acties uitvoeren, zoals het toevoegen of verwijderen van deel nemers uit een netwerk.

## <a name="roles"></a>Rollen

Deel nemers aan een consortium kunnen individuen of organisaties zijn en kunnen aan een gebruikersrol of een beheerdersrol worden toegewezen. De volgende tabel geeft een overzicht van de verschillen op hoog niveau tussen de twee rollen:

| Bewerking | Gebruikersrol | Beheerdersrol
|--------|:----:|:------------:|
| Nieuw lid maken | Ja | Ja |
| Nieuwe leden uitnodigen | Nee | Ja |
| Rol van leden deel nemer instellen of wijzigen | Nee | Ja |
| Weergave naam van lid wijzigen | Alleen voor eigen lid | Alleen voor eigen lid |
| Leden verwijderen | Alleen voor eigen lid | Ja |
| Deel nemen aan block Chain-trans acties | Ja | Ja |

### <a name="user-role"></a>Gebruikersrol

Gebruikers zijn consortium deelnemers zonder beheerders mogelijkheden. Ze kunnen niet deel nemen aan leden die betrekking hebben op het consortium. Gebruikers kunnen hun weergave naam van het lid wijzigen en kunnen zichzelf uit een consortium verwijderen.

### <a name="administrator"></a>Beheerder

Een beheerder kan leden beheren binnen het consortium. Een beheerder kan leden uitnodigen, leden verwijderen of leden rollen in het consortium bijwerken.
Er moet altijd ten minste één beheerder binnen een consortium zijn. De laatste beheerder moet een andere deel nemer opgeven als beheerdersrol voordat een consortium wordt verlaten.

## <a name="managing-members"></a>Leden beheren

Alleen beheerders kunnen andere deel nemers uitnodigen voor het consortium. Beheerders nodigen deel nemers uit met hun Azure-abonnements-ID.

Na de uitnodiging kunnen deel nemers deel nemen aan het block Chain consortium door een nieuw lid te implementeren in de Azure Block Chain-service. Als u het uitgenodigde consortium wilt weer geven en lid wilt worden van het invite, moet u dezelfde Azure-abonnements-ID opgeven die wordt gebruikt in de uitnodiging door de netwerk beheerder.

Beheerders kunnen alle deel nemers uit het consortium verwijderen, met inbegrip van andere beheerders. Leden kunnen zich alleen uit een consortium verwijderen.

## <a name="consortium-management-smart-contract"></a>Smart-contract management consortium

Consortium beheer in azure Block Chain service wordt uitgevoerd via slimme contracten voor consortium management. De slimme contracten worden automatisch geïmplementeerd op uw knoop punten wanneer u een nieuw Block Chain-lid implementeert.

Het adres van het hoofd consortium Management Smart contract kan worden weer gegeven in de Azure Portal. Het **RootContract-adres** bevindt zich in de sectie met het overzicht van Block Chain-leden.

![RootContract-adres](./media/consortium/rootcontract-address.png)

U kunt communiceren met het consortium Management Smart contract met behulp van de Management Manager [Power shell-module](manage-consortium-powershell.md), Azure portal of rechtstreeks via het slimme contract met behulp van het Ethereum-account dat door de Azure Block Chain-service is gegenereerd.

## <a name="ethereum-account"></a>Ethereum-account

Wanneer een lid wordt gemaakt, wordt er een Ethereum-account sleutel gemaakt. De Azure Block Chain-service gebruikt de sleutel om trans acties te maken die betrekking hebben op consortium beheer. De Ethereum-account sleutel wordt automatisch beheerd door de Azure Block Chain-service.

Het leden account kan worden weer gegeven in de Azure Portal. De leden account bevindt zich in de sectie met het overzicht van de Block chain.

![Lid-account](./media/consortium/member-account.png)

U kunt uw Ethereum-account opnieuw instellen door op uw leden account te klikken en een nieuw wacht woord in te voeren. Het adres van het Ethereum-account en het wacht woord worden opnieuw ingesteld.  

## <a name="next-steps"></a>Volgende stappen

Acties voor consortium beheer kunnen worden geopend via Power shell. Zie voor meer informatie [Managed consortium members in azure Block Chain service using Power shell](manage-consortium-powershell.md)(Engelstalig).
