---
title: Een resource uitsluiten van Azure Security Center beveiligings aanbevelingen en de beveiligde Score
description: Meer informatie over het uitsluiten van een resource van beveiligings aanbevelingen en de beveiligde Score
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b7780a0ef70a89a88070d5883cc840319a67fa3d
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122351"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Een resource uitsluiten van aanbevelingen en de beveiligingsscore

Een kern prioriteit van elk beveiligings team probeert ervoor te zorgen dat de analisten zich kunnen concentreren op de taken en incidenten die voor de organisatie van belang zijn. Security Center beschikt over een groot aantal functies voor het aanpassen van de gegevens die u het belangrijkst vindt en zorg ervoor dat uw beveiligde Score een geldige reflectie vormt van de beveiligings beslissingen van uw organisatie. Het uitsluiten van resources is een van deze functies.

Wanneer u een beveiligings aanbeveling in Azure Security Center onderzoekt, is een van de eerste stukjes informatie die u controleert de lijst met betrokken resources.

In sommige gevallen wordt een resource weer gegeven die u niet wilt opnemen. Dit kan zijn opgelost door een proces dat niet wordt bijgehouden door Security Center. Of misschien heeft uw organisatie besloten het risico voor die specifieke resource te accepteren. 

In dergelijke gevallen kunt u een uitzonderings regel maken en ervoor zorgen dat de resource niet in de toekomst wordt weer gegeven met de beschadigde resources en niet van invloed is op uw beveiligde Score. 

De resource wordt weer gegeven als niet van toepassing en de reden wordt weer gegeven als ' uitgesloten ' met de reden die u hebt geselecteerd.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Prijzen:|Dit is een Premium Azure-beleids mogelijkheid die wordt aangeboden aan Azure Defender-klanten zonder extra kosten. Voor andere gebruikers kunnen kosten in de toekomst worden toegepast.|
|Vereiste rollen en machtigingen:|**Eigenaar van abonnement** of **beleids bijdrage** voor het maken van een uitzonde ring<br>Als u een regel wilt maken, hebt u machtigingen nodig voor het bewerken van beleid in Azure Policy.<br>Meer informatie vindt u in de [Azure RBAC-machtigingen in azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||


## <a name="create-an-exemption-rule"></a>Een uitzonderings regel maken

1. Selecteer in de lijst met beschadigde resources het menu met weglatings tekens (...) voor de resource die u wilt uitsluiten.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Optie uitzonde ring maken in context menu":::

    Het deel venster uitzonde ring maken wordt geopend.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Het deel venster uitzonde ringen maken":::

1. Voer uw criteria in en selecteer een criterium waarom deze resource moet worden uitgesloten:
    - Opgelost **: dit** probleem is niet relevant voor de resource omdat het is verwerkt door een ander hulp programma of proces dan het wordt voorgesteld
    - **Afwijzingen** : het risico voor deze resource wordt geaccepteerd
1. Selecteer **Opslaan**.
1. Na een tijdje (het kan Maxi maal 24 uur duren):
    - De resource heeft geen invloed op uw beveiligde Score.
    - De resource wordt weer gegeven op het tabblad **niet van toepassing** op de pagina aanbevelings Details
    - In de informatie strook boven aan de pagina Details van aanbeveling wordt het aantal uitgesloten resources weer gegeven:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Aantal vrijgestelde resources":::

1. Open het tabblad **niet van toepassing** om uw uitgesloten resources te bekijken.

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Een uitzonde ring wijzigen":::

    De reden voor elke uitzonde ring is opgenomen in de tabel (1).

    Als u een uitzonde ring wilt wijzigen of verwijderen, selecteert u het menu met het weglatings teken ("..."), zoals weer gegeven (2).


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Alle uitzonderings regels voor uw abonnement controleren

Uitzonderings regels gebruiken Azure-beleid om een uitzonde ring voor de resource te maken op de beleids toewijzing.

U kunt Azure Policy gebruiken om al uw uitzonde ringen bij te houden op de pagina **uitzonde ringen** :

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Pagina uitzonde ringen van Azure Policy":::



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een resource kunt uitsluiten van een aanbeveling zodat deze geen invloed heeft op uw beveiligde Score. Zie voor meer informatie over beveiligde scores:

- [Beveiligingsscore in Azure Security Center](secure-score-security-controls.md)