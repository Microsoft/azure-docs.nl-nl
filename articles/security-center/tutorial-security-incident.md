---
title: Zelfstudie voor het reageren op waarschuwingen - Azure Security Center
description: In deze zelfstudie leert u hoe u beveiligingswaarschuwingen kunt sorteren en de hoofdoorzaak en het bereik van een waarschuwing kunt vaststellen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 41f96202bffc0169be9705cd7e67c1eedbecbc05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453441"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Zelfstudie: Beveiligingswaarschuwingen sorteren, onderzoeken en erop reageren
Security Center analyseert voortdurend de werkbelasting van uw hybride cloud met behulp van geavanceerde analysen en bedreigingsinformatie, om u te waarschuwen voor mogelijk schadelijke activiteiten in uw cloudresources. U kunt ook waarschuwingen van andere beveiligingsproducten en -services integreren in Security Center. Wanneer een waarschuwing wordt gegeven, moet snel actie worden ondernomen om het mogelijke beveiligingsprobleem te onderzoeken en tegen te gaan. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Beveiligingswaarschuwingen onderzoeken
> * Een beveiligingswaarschuwing onderzoeken om de hoofdoorzaak vast te stellen
> * Reageren op een beveiligingswaarschuwing en de hoofdoorzaak verminderen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
Om de functies in deze zelfstudie te doorlopen, moet Azure Defender zijn ingeschakeld. U kunt Azure Defender gratis uitproberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie. De quickstart [Aan de slag met Security Center](security-center-get-started.md) begeleidt u bij de upgrade.


## <a name="triage-security-alerts"></a>Beveiligingswaarschuwingen onderzoeken
Security Center biedt een overkoepelend overzicht van alle beveiligingswaarschuwingen. Beveiligingswaarschuwingen worden geclassificeerd op basis van de ernst van de gedetecteerde activiteit. 

Sorteer uw waarschuwingen op de pagina **Beveiligingswaarschuwingen** :

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lijst met beveiligings waarschuwingen voor Azure Security Center":::

Gebruik deze pagina om de actieve beveiligingswaarschuwingen in uw omgeving te bekijken om te bepalen welke waarschuwing het eerst moet worden onderzocht.

Ken bij het sorteren van beveiligingswaarschuwingen prioriteiten toe aan waarschuwingen op basis van de ernst van de waarschuwing door waarschuwingen met een hogere ernst eerst te behandelen. Lees meer informatie over de ernst van waarschuwingen in [Hoe worden waarschuwingen geclassificeerd?](security-center-alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> U kunt Azure Security Center verbinden met de populairste SIEM-oplossingen, waaronder Azure Sentinel, en de waarschuwingen van een hulpprogramma naar keuze gebruiken. Zie voor meer informatie [Waarschuwingen streamen naar een SIEM-, SOAR- of IT Service Management-oplossing](export-to-siem.md).


## <a name="investigate-a-security-alert"></a>Een beveiligingswaarschuwing onderzoeken

Wanneer u hebt besloten welke waarschuwing eerst moet worden onderzocht:

1. Selecteer de gewenste waarschuwing.
1. Selecteer op de overzichtspagina van de waarschuwing de resource die u als eerste wilt onderzoeken.
1. Begin met uw onderzoek vanuit het linkerdeelvenster, waarin informatie op hoog niveau over de beveiligingswaarschuwing wordt weergegeven.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Het linkerdeelvenster van de detailpagina voor de waarschuwing; de informatie op hoog niveau wordt gemarkeerd":::

    In dit deelvenster wordt het volgende weergegeven:
    - Ernst van waarschuwing, status en tijd van activiteit
    - Beschrijving met uitleg over de precieze gedetecteerde activiteit
    - Betrokken resources
    - De keten van intenties van de activiteit op de MITRE ATT&CK-matrix afbreken

1. Raadpleeg het tabblad **Meldingsdetails** voor gedetailleerde informatie over het onderzoeken van de verdachte activiteit.

1. Wanneer u de informatie op deze pagina hebt gecontroleerd, hebt u mogelijk voldoende om door te gaan met een antwoord. Als u meer informatie nodig hebt:

    - Neem contact op met de eigenaar van de resource om te controleren of de gedetecteerde activiteit een fout-positief is.
    - Onderzoek de onbewerkte logboeken die door de aangevallen resource zijn gegenereerd

## <a name="respond-to-a-security-alert"></a>Reageren op een beveiligingswaarschuwing
Nadat u een waarschuwing hebt onderzocht en de omvang ervan hebt begrepen, kunt u vanuit Azure Security Center reageren op de beveiligingswaarschwing:

1.  Open het tabblad **Actie ondernemen** om de aanbevolen reacties te bekijken.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Het tabblad Actie ondernemen voor beveiligingswaarschuwingen" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Raadpleeg de sectie **De bedreiging oplossen** voor de handmatige onderzoeksstappen die nodig zijn om het probleem te verhelpen.
1.  Om uw resources te beveiligen en toekomstige aanvallen van dit type te voorkomen, moet u de beveiligingsaanbevelingen in de sectie **Toekomstige aanvallen voorkomen** volgen.
1.  Gebruik de sectie **Geautomatiseerd antwoord activeren** als u een logische app met automatische reactie stappen wilt activeren.
1.  Als de gedetecteerde activiteit *niet* schadelijk is, kunt u toekomstige waarschuwingen van dit type onderdrukken met behulp van de sectie **Vergelijkbare waarschuwingen onderdrukken**.

1.  Als u het onderzoek in de waarschuwing hebt voltooid en op de juiste manier hebt gereageerd, wijzigt u de status in **Genegeerd**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="De status van een waarschuwing instellen":::

    Hiermee verwijdert u de waarschuwing uit de hoofdlijst met waarschuwingen. U kunt het filter op de pagina met waarschuwingen gebruiken om alle waarschuwingen met de status **Genegeerd** weer te geven.

1.  Stuur vooral feedback over de waarschuwing naar Microsoft:
    1. De waarschuwing markeren als **Nuttig** of **Niet nuttig**.
    1. Selecteer een reden en voeg een opmerking toe.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Feedback aan Microsoft geven over het nut van een waarschuwing":::

    > [!TIP]
    > We bekijken uw feedback om onze algoritmen te verbeteren en betere beveiligingswaarschuwingen te bieden.

## <a name="end-the-tutorial"></a>De zelfstudie beëindigen

Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u de volgende quickstarts en zelfstudies ook wilt doornemen, houdt u automatische inrichting en Azure Defender ingeschakeld. 

Als u niet van plan bent om door te gaan of als u een van deze functies wilt uitschakelen:

1. Ga terug naar het hoofdmenu van Security Center en selecteer **Prijzen en instellingen**.
1. Selecteer het betreffende abonnement.
1. Selecteer **Azure Defender uit** om te downgraden.
1. Als u automatische inrichting wilt uitschakelen, opent u de pagina **Gegevensverzameling** en stelt u **Automatische inrichting** in op **Uit**.
1. Selecteer **Opslaan**.

>[!NOTE]
> Wanneer u automatische inrichting uitschakelt, wordt de Log Analytics-agent niet verwijderd van virtuele Azure-machines waarop de agent al staat. Door automatische inrichting uit te schakelen, wordt de beveiligingsbewaking voor uw resources beperkt.
>

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd over Security Center-functies die kunnen worden gebruikt als u reageert op een beveiligingswaarschuwing. Zie voor gerelateerd materiaal:

- [Reageren op Azure Defender voor Key Vault-waarschuwingen](defender-for-key-vault-usage.md)
- [Referentiegids met beveiligingswaarschuwingen](alerts-reference.md)
- [Inleiding tot Azure Defender](azure-defender.md)
