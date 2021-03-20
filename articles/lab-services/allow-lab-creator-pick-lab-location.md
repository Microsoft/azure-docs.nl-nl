---
title: Lab-Maker toestaan locatie in Azure Lab Services te kiezen
description: In dit artikel wordt beschreven hoe een Lab-account beheerder Lab-makers kan toestaan om locaties voor hun Labs te kiezen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85444196"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Lab Creator toestaan locatie voor het lab in Azure Lab Services te kiezen
In Azure Lab Services kan de eigenaar van een Lab-account Lab-makers (docenten) toestaan een locatie te kiezen voor het lab dat ze maken. Deze locatie kan afwijken van de locatie van het lab-account. Een locatie is een groep Azure-regio's. Verenigde Staten locatie is bijvoorbeeld een groep regio's, zoals VS-Oost, VS-West, enzovoort. 

Als eigenaar van een Lab-account kunt u de optie Lab- **Maker toestaan locatie van Lab kiezen** selecteren wanneer u een Lab-account maakt en nadat u het lab-account (of een bestaand Lab-account) hebt gemaakt. 

## <a name="at-the-time-of-lab-account-creation"></a>Op het moment dat het lab-account wordt gemaakt
Wanneer u een Lab-account maakt, wordt deze optie weer gegeven op het eerste scherm (tabblad **basis beginselen** ). 

![De optie inschakelen op het moment dat het lab wordt gemaakt](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Deze optie is uitgeschakeld als u een virtueel peer netwerk voor uw Lab-account selecteert op het tabblad **Geavanceerd** .  

![Als er een peer-virtueel netwerk is ingeschakeld](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Nadat het lab-account is gemaakt
Nadat u het lab-account hebt gemaakt, kunt u deze optie in-of uitschakelen door de volgende stappen uit te voeren: 

1. Selecteer op de pagina **Lab-account** de optie **Lab-instellingen** in het menu links.
2. Selecteer de optie Lab- **Creator toestaan om Lab-locatie te kiezen** als u wilt toestaan dat de maker van het lab een locatie voor het Lab kan selecteren. Als deze is uitgeschakeld, worden de Labs automatisch gemaakt op dezelfde locatie als het lab-account. 
    
    Dit veld wordt uitgeschakeld wanneer u een virtueel netwerk selecteert voor het veld **virtuele peer netwerk** . De reden hiervoor is dat Labs in het lab-account zich in dezelfde regio bevinden als het lab-account waarmee ze toegang hebben tot resources in het virtuele netwerk van de peer. 
1. Selecteer **Opslaan** op de werkbalk. 

    ![Lab-instellingen](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>De selectie van een virtueel netwerk en een locatie is niet toegestaan
In dit scenario hebt u niet de optie Lab- **Maker toestaan om Lab-locatie te selecteren** ingeschakeld. 

![Geen Lab-locatie](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Vervolgens zien Lab-makers (docenten) geen optie voor het kiezen van een locatie voor het lab. Ze zien de prijs per uur voor elke beschik bare grootte optie. Wanneer ze een lab maken, wordt deze gemaakt in een Azure-regio op dezelfde locatie als de Azure-regio waarin hun Lab-account zich bevindt. Als het lab-account zich bijvoorbeeld in **VS West** bevindt, wordt het lab mogelijk in **Zuid-Centraal VS** gemaakt, maar niet in **Canada-Oost**. We garanderen niets over de regio die we kiezen op de locatie. Als er momenteel een grootte is beperkt, wordt in de ontwikkel aars van het lab een selectie vakje weer geven waarin de grootten worden weer geven die normaal gesp roken worden ondersteund, maar die momenteel niet beschikbaar zijn. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>In het virtuele netwerk en de locatie electie is niet toegestaan
In dit scenario is de optie **Lab Creator toestaan voor Lab-locatie selecteren** uitgeschakeld, omdat u een virtueel peer netwerk hebt geselecteerd voor het lab-account. Vervolgens zien Lab-makers hetzelfde scherm als bij de vorige optie. Omdat alle virtuele machines zich in dezelfde Azure-regio als het virtuele netwerk moeten bevinden, wordt het lab gemaakt in de Azure-regio waarin het virtuele netwerk zich bevindt. Als de betreffende regio is beperkt voor een grootte, wordt de grootte weer gegeven als niet beschikbaar. 

## <a name="location-selection-is-enabled"></a>Locatie selectie is ingeschakeld
Wanneer u **Lab Creator toestaan selecteert om Lab-locatie te kiezen**, kunnen Lab-makers (docenten) een optie hebben om een locatie te selecteren bij het maken van een lab. 

![Een Lab-locatie kiezen](./media/allow-lab-creator-pick-lab-location/location-selection.png)

Lab-makers zien het prijs bereik voor alle locaties waarop de grootte zich bevindt en u kunt een locatie kiezen. Het lab wordt gemaakt in een Azure-regio die is toegewezen aan deze locatie.

Als een locatie beperkt is, wordt deze niet standaard weer gegeven in de lijst. Vouw de vervolg keuzelijst uit en selecteer niet- **beschik bare locaties voor deze grootte weer geven**. 

![Niet-beschik bare locaties weer geven](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Kosten
Eerdere prijzen zijn gebaseerd op de grootte van de virtuele machine die u kiest voor het lab. Nu is de prijs gebaseerd op de combi natie van besturings systeem (OS), grootte en locatie. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Uw Lab-netwerk verbinden met een virtueel peer netwerk](how-to-connect-peer-virtual-network.md)
- [Een galerie met gedeelde afbeeldingen koppelen aan een Lab](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als een Lab-eigenaar](how-to-add-user-lab-owner.md)
- [Firewall instellingen voor een Lab weer geven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)