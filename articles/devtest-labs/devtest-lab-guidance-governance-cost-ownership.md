---
title: Kosten en eigendom beheren in Azure DevTest Labs
description: In dit artikel vindt u informatie over het optimaliseren van kosten en het uitlijnen van het eigendom in uw omgeving.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: dfac69a055c9b0c75032622caf7fb8502fad3406
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92328263"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Governance van Azure DevTest Labs-infra structuur-kosten en eigendom beheren
Kosten en eigendom zijn primair als u overweegt om uw ontwikkel-en test omgevingen te bouwen. In deze sectie vindt u informatie die u helpt bij het optimaliseren van kosten en het aanpassen van het eigendom in uw omgeving.

## <a name="optimize-for-cost"></a>Optimaliseren voor kosten

### <a name="question"></a>Vraag
Hoe kan ik de kosten in mijn DevTest Labs-omgeving optimaliseren?

### <a name="answer"></a>Antwoord
Er zijn een aantal ingebouwde functies van DevTest Labs waarmee u de kosten kunt optimaliseren. Zie [kosten beheer, drempels](devtest-lab-configure-cost-management.md) [en](devtest-lab-set-lab-policy.md) artikel artikelen om activiteiten van uw gebruikers te beperken. 

Wanneer u DevTest Labs gebruikt voor een ontwikkelings-en test werkbelasting, kunt u overwegen het [Enterprise dev/test-abonnements voordeel](https://azure.microsoft.com/offers/ms-azr-0148p/)te gebruiken als onderdeel van uw Enterprise overeenkomst. Als u betaalt als u bent, kunt u ook rekening houden met de [betalen naar gebruik-DevTest aanbieding](https://azure.microsoft.com/offers/ms-azr-0023p/).

Deze benadering biedt talloze voor delen:

- Speciale lagere dev/test-tarieven voor virtuele Windows-machines, Cloud Services, HDInsight, App Service en Logic Apps
- Fantastische tarieven voor Enterprise Overeenkomst (EA) op andere Azure-Services
- Toegang tot exclusieve dev/test-installatie kopieën in de galerie, waaronder Windows 8,1 en Windows 10
 
Alleen actieve Visual Studio-abonnees (standaard abonnementen, jaarlijkse Cloud abonnementen en maandelijkse Cloud abonnementen) kunnen gebruikmaken van Azure-resources die worden uitgevoerd in een Enter prise dev/test-abonnement. Eind gebruikers hebben echter toegang tot de toepassing om feedback te geven of acceptatie tests uit te voeren. Het gebruik van resources binnen dit abonnement is beperkt tot het ontwikkelen en testen van toepassingen, en er wordt geen garantie voor de uptime geboden.

Als u besluit de DevTest-aanbieding te gebruiken, moet u er rekening mee houden dat dit voor deel uitsluitend is voor het ontwikkelen en testen van uw toepassingen. Voor gebruik binnen het abonnement geldt geen SLA met financiële garantie, met uitzonde ring van het gebruik van Azure DevOps en HockeyApp.

## <a name="define-role-based-access-across-your-organization"></a>Op rollen gebaseerde toegang in uw organisatie definiëren
### <a name="question"></a>Vraag
Hoe kan ik op rollen gebaseerd toegangs beheer voor mijn DevTest Labs definiëren om ervoor te zorgen dat IT kan bepalen terwijl ontwikkel aars/tests hun werk kunnen doen? 

### <a name="answer"></a>Antwoord
Er is een breed patroon, maar de details zijn afhankelijk van uw organisatie.

U hoeft alleen maar te beschikken over wat er nodig is, en het project en toepassings teams in staat stellen het benodigde beheer niveau te gebruiken. Normaal gesp roken betekent het dat de centrale IT eigenaar is van het abonnement en dat de kern IT-functies, zoals netwerk configuraties, worden afgehandeld. De set **eigen aars** voor een abonnement moet klein zijn. Deze eigen aren kunnen extra eigen aren benoemen wanneer er behoefte is of beleid op abonnements niveau Toep assen, bijvoorbeeld ' geen openbaar IP-adres '.

Mogelijk is er een subset van gebruikers die toegang nodig hebben tot meerdere abonnementen, zoals Tier1 of Tier 2-ondersteuning. In dit geval wordt u aangeraden deze gebruikers de toegang tot de **Inzender** te geven, zodat ze de resources kunnen beheren, maar geen gebruikers toegang bieden of beleids regels aanpassen.

De DevTest Labs-resource moet eigendom zijn van eigen aren die dicht bij het project/toepassings team vallen. Dit is omdat ze inzicht hebben in hun vereisten op het gebied van machines en vereiste software. In de meeste organisaties is de eigenaar van deze DevTest Labs resource meestal het project/de ontwikkelings lead. Deze eigenaar kan gebruikers en beleids regels in de test omgeving beheren en alle virtuele machines in de DevTest Labs-omgeving beheren.

De leden van het project/het toepassings team moeten worden toegevoegd aan de rol DevTest Labs-gebruikers. Deze gebruikers kunnen virtuele machines maken (op basis van de beleids regels Lab en abonnement op abonnements niveau). Ze kunnen ook hun eigen virtuele machines beheren. Ze kunnen geen virtuele machines beheren die deel uitmaken van andere gebruikers.

Zie voor meer informatie [Azure Enter prise-steigers-](/azure/architecture/cloud-adoption/appendix/azure-scaffold) documentatie over het beheer van voorgeschreven abonnementen.


## <a name="next-steps"></a>Volgende stappen
Zie [bedrijfs beleid en naleving](devtest-lab-guidance-governance-policy-compliance.md).
