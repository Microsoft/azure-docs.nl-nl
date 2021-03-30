---
title: Uw Azure DevTest Labs-infra structuur omhoog schalen
description: Dit artikel bevat richt lijnen voor het schalen van uw Azure DevTest Labs-infra structuur.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 50bf08678a12a1a0499abd08c52a264d03f4a401
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85478787"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Uw Azure DevTest Labs-infra structuur omhoog schalen
Voordat u DevTest Labs implementeert op het niveau van de onderneming, zijn er verschillende belang rijke beslissings punten. Met betrekking tot deze beslissings punten op een hoog niveau kan een organisatie in de toekomst ontwerp beslissingen nemen. Deze punten mogen echter niet terugvallen op het begin van het testen van een concept. De drie belangrijkste gebieden voor het plannen van de eerste schaal zijn:

- Netwerken en beveiliging
- Topologie van abonnement
- Rollen en verantwoordelijkheden

## <a name="networking-and-security"></a>Netwerken en beveiliging
Netwerken en beveiliging zijn hoek stenen voor alle organisaties. Hoewel een implementatie op ondernemings niveau een veel diepere analyse vereist, is er een kleiner aantal vereisten om een proef van het concept uit te voeren. Enkele belang rijke aspecten van de focus zijn:

- **Azure-abonnement** : als u DevTest Labs wilt implementeren, moet u toegang hebben tot een Azure-abonnement met de juiste rechten voor het maken van resources. Er zijn een aantal manieren om toegang te krijgen tot Azure-abonnementen, met inbegrip van een Enterprise Overeenkomst en betalen naar gebruik. Zie [licenties voor Azure voor ondernemingen](https://azure.microsoft.com/pricing/enterprise-agreement/)voor meer informatie over het verkrijgen van toegang tot een Azure-abonnement.
- **Toegang tot on-premises resources** : sommige organisaties vereisen dat hun resources in DevTest Labs toegang hebben tot on-premises resources. Er is een beveiligde verbinding van uw on-premises omgeving naar Azure nodig. Daarom is het belang rijk dat u een VPN-of Express route-verbinding instelt of configureert voordat u aan de slag gaat. Zie [overzicht van virtuele netwerken](../virtual-network/virtual-networks-overview.md)voor meer informatie.
- **Aanvullende beveiligings vereisten** : andere beveiligings vereisten, zoals computer beleid, toegang tot open bare IP-adressen, verbinding maken met internet, zijn scenario's die mogelijk moeten worden beoordeeld voordat een testen van het concept wordt geïmplementeerd. 

## <a name="subscription-topology"></a>Topologie van abonnement
De topologie van abonnementen is een kritische ontwerp overweging bij het implementeren van DevTest Labs in de onderneming. Het is echter niet nodig om alle beslissingen te versterkenen totdat een concept is voltooid. Bij het evalueren van het aantal abonnementen dat vereist is voor een bedrijfs implementatie, zijn er twee extreme: 

- Eén abonnement voor de hele organisatie
- Abonnement per gebruiker

Vervolgens markeren we de voor delen van elke benadering.

### <a name="one-subscription"></a>Eén abonnement
Vaak is de aanpak van één abonnement niet beheerbaar in een grote onderneming. Het beperken van het aantal abonnementen biedt echter de volgende voor delen:

- **Prognose** van de kosten voor Enter prise.  Budget tering wordt veel eenvoudiger in één abonnement, omdat alle resources zich in één groep bevinden. Deze aanpak maakt het mogelijk om eenvoudig besluit te nemen bij het uitvoeren van kostencontrole maatregelen op een bepaald moment in een facturerings cyclus.
- **Beheer baarheid** van vm's, artefacten, formules, netwerk configuratie, machtigingen, beleids regels, enzovoort is eenvoudiger omdat alle updates alleen in één abonnement zijn vereist, in plaats van updates te maken voor veel abonnementen.
- De **netwerk** inspanning is aanzienlijk vereenvoudigd in één abonnement voor ondernemingen waarbij een on-premises connectiviteit een vereiste is. Het verbinden van virtuele netwerken tussen abonnementen (hub-spoke model) is vereist voor extra abonnementen, waarvoor aanvullende configuratie, beheer, IP-adres ruimten, enzovoort.
- **Samen werking tussen teams** is eenvoudiger wanneer iedereen in hetzelfde abonnement werkt. het is bijvoorbeeld eenvoudiger om een virtuele machine opnieuw toe te wijzen aan een mede werker, team resources te delen, enzovoort.

### <a name="subscription-per-user"></a>Abonnement per gebruiker
Een afzonderlijk abonnement per gebruiker biedt gelijke kansen voor het alternatieve spectrum. De voor delen van het gebruik van veel abonnementen zijn:

- **Quota** voor het schalen van Azure worden niet belemmerd. Zo kunnen met behulp van deze schrijf bewerkingen in azure 200 opslag accounts per abonnement worden opgesteld. Er zijn operationele quota's voor de meeste services in azure (veel kunnen worden aangepast, sommige niet). In dit model van een abonnement per gebruiker is het zeer onwaarschijnlijk dat de meeste quota worden bereikt. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over de huidige quota voor het schalen van Azure.
- Het maken van **instortingen** naar groepen of individuele ontwikkel aars is veel eenvoudiger, waardoor organisaties hun kosten met hun huidige model kunnen verrekenen.
- **Eigendom & machtigingen** van de DevTest Labs-omgevingen zijn eenvoudig. U geeft ontwikkel aars de toegang op abonnements niveau en ze zijn 100% verantwoordelijk voor alles, met inbegrip van de netwerk configuratie, het lab-beleid en het beheer van virtuele machines.

In de onderneming zijn er mogelijk voldoende beperkingen voor de Extremes van het spectrum. Daarom moet u mogelijk abonnementen instellen op een manier die in het midden van deze extremees valt. Als best practice moet het doel van een organisatie het minimale aantal abonnementen gebruiken, zodat u rekening houdt met de afdwingings functies die het totale aantal abonnementen verhogen. De topologie van abonnementen is essentieel voor een Enter prise-implementatie van DevTest Labs, maar mag geen testen van het concept vertragen. In het artikel [governance](devtest-lab-guidance-governance-policy-compliance.md) vindt u meer informatie over het kiezen van een abonnement en een Lab-granulatie in de organisatie.

## <a name="roles-and-responsibilities"></a>Rollen en verantwoordelijkheden
Een DevTest Labs-concept heeft drie primaire rollen met gedefinieerde verantwoordelijkheden: abonnements eigenaar, DevTest Labs-eigenaar, DevTest Labs-gebruiker en optioneel een mede werker.

- **Abonnements eigenaar** : de eigenaar van het abonnement heeft rechten om een Azure-abonnement te beheren, met inbegrip van het toewijzen van gebruikers, het beheren van beleid, het maken van & netwerk topologie, het aanvragen van quotum verhogingen, enzovoort. Zie [dit artikel](../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie.
- **Eigenaar van DevTest Labs** : de eigenaar van de DevTest Labs heeft volledige beheerders toegang tot het lab. Deze persoon is verantwoordelijk voor het toevoegen/verwijderen van gebruikers, het beheren van kosten instellingen, algemene Lab-instellingen en andere taken op basis van virtuele machines en artefacten. Een Lab-eigenaar heeft ook alle rechten van een DevTest Labs-gebruiker.
- **DevTest Labs-gebruiker** : de DevTest Labs-gebruiker kan de virtuele machines in het lab maken en gebruiken. Deze personen hebben een aantal minimale beheer mogelijkheden op Vm's die ze maken (starten/stoppen/verwijderen/configureren van de Vm's). De gebruikers kunnen geen Vm's van andere gebruikers beheren.

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel in deze serie: [de implementatie van Azure DevTest Labs organiseren](devtest-lab-guidance-orchestrate-implementation.md)