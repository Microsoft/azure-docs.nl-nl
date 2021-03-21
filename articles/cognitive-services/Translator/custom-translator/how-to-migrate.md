---
title: Migratie van micro soft Translator hub-werk ruimte en-projecten? -Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u uw hub-werk ruimte en-projecten kunt migreren naar Azure Cognitive Services Custom Translator.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 89658ce88b4f1ac9d5bacac7bd45511b4aa0a1be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895708"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Hub-werk ruimte en projecten migreren naar een aangepaste vertaler

U kunt uw [micro soft Translator hub](https://hub.microsofttranslator.com/) -werk ruimte en-projecten eenvoudig migreren naar aangepaste vertalers. Migratie wordt gestart vanuit micro soft hub door een werk ruimte of project te selecteren, vervolgens een werk ruimte te selecteren in Custom Translator en vervolgens de opleidingen te selecteren die u wilt overdragen. Nadat de migratie is gestart, worden de geselecteerde trainings instellingen met alle relevante documenten overgebracht. Geïmplementeerde modellen zijn getraind en kunnen na voltooiing automatisch worden geïmplementeerd.

Deze acties worden uitgevoerd tijdens de migratie:
* De namen van alle documenten en project definities worden overgebracht met de toevoeging ' hub_ ', voorafgegaan door de naam. Automatisch gegenereerde test-en afstemmings gegevens worden hub_systemtune_ \<modelid> of hub_systemtest_ genoemd \<modelid> .
* Alle trainingen die in de geïmplementeerde toestand waren toen de migratie plaatsvindt, worden automatisch getraind met behulp van de documenten van de hub-training. Deze training wordt niet in rekening gebracht voor uw abonnement. Als automatisch implementeren is geselecteerd voor de migratie, wordt het getrainde model na voltooiing geïmplementeerd. Normale hosting kosten worden toegepast.
* Gemigreerde trainingen die zich niet in de geïmplementeerde status bevonden, worden in de gemigreerde concept status geplaatst. In deze status kunt u een model trainen met de gemigreerde definitie, maar er worden regel matig trainings kosten in rekening gebracht.
* Op elk gewenst moment kunt u de BLEU-Score die vanuit de hub-training is gemigreerd, vinden op de pagina TrainingDetails van het model in de kop ' Bleu Score in MT hub '.

> [!Note] 
> Voor een succes volle training vereist een aangepast Vertaal programma mini maal 10.000 unieke geëxtraheerde zinnen. Aangepaste Translator kan geen training uitvoeren met minder dan het [voorgestelde minimum](./sentence-alignment.md#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Werk ruimte-ID van aangepaste vertaler zoeken

Als u de [micro soft Translator hub](https://hub.microsofttranslator.com/) -werk ruimte wilt migreren, hebt u de doel werkruimte-id nodig in het aangepaste conversie programma. De doel werkruimte in het aangepaste conversie programma is de locatie waar alle hub-werk ruimten en-projecten worden gemigreerd.

U vindt de doel werkruimte-ID op de pagina aangepaste Vertaal instellingen:

1. Ga naar de pagina instellingen in de aangepaste Vertaal Portal.

2. De werk ruimte-ID is te vinden in de sectie basis informatie.

    ![De doel werkruimte-ID zoeken](media/how-to/how-to-find-destination-ws-id.png)

3. Zorg ervoor dat uw doel werkruimte-ID verwijst tijdens het migratie proces.

## <a name="migrate-a-project"></a>Een project migreren

Als u uw projecten selectief wilt migreren, biedt micro soft Translator hub u de mogelijkheid.

Een project migreren:

1. Meld u aan bij de micro soft Translator-hub.

2. Ga naar de pagina projecten.

3. Klik op de koppeling migreren voor het juiste project.

    ![Scherm afbeelding waarmee de knop voor het migreren van het geselecteerde project wordt gemarkeerd.](media/how-to/how-to-migrate-from-hub.png)

4. Wanneer u op de koppeling migreren klikt, krijgt u een formulier waarmee u het volgende kunt doen:
   * Geef de werk ruimte op die u wilt overdragen naar het aangepaste conversie programma
   * Geef aan of u alle trainingen wilt overdragen met succes volle trainingen of alleen de geïmplementeerde trainingen. Standaard worden alle geslaagde trainingen overgedragen.
   * Geef aan of u wilt dat uw training automatisch wordt geïmplementeerd wanneer de training is voltooid. Uw training wordt standaard niet automatisch geïmplementeerd wanneer deze is voltooid.

5. Klik op aanvraag verzenden.

## <a name="migrate-a-workspace"></a>Een werk ruimte migreren

Naast het migreren van één project, kunt u ook alle projecten migreren met geslaagde trainingen in een werk ruimte. Dit zorgt ervoor dat elk project in de werk ruimte wordt geëvalueerd alsof de migratie koppeling werd ingedrukt. Deze functie is geschikt voor gebruikers met veel projecten die al deze willen migreren naar een aangepaste vertaler met dezelfde instellingen. U kunt een werkruimte migratie starten vanaf de pagina instellingen van de Translator hub.

Een werk ruimte migreren:

1. Meld u aan bij de micro soft Translator-hub.

2. Ga naar de pagina instellingen.

3. Klik op de pagina instellingen op werk ruimte gegevens migreren naar aangepaste vertaler.

    ![Scherm afbeelding van de optie voor het migreren van werkruimte gegevens naar een aangepaste Translator.](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Selecteer op de volgende pagina een van deze twee opties:

    a. Alleen geïmplementeerde trainingen: als u deze optie selecteert, worden alleen de geïmplementeerde systemen en gerelateerde documenten gemigreerd.

    b. Alle geslaagde trainingen: als u deze optie selecteert, worden alle succes volle trainingen en gerelateerde documenten gemigreerd.

    c. Voer uw doel werkruimte-ID in het aangepaste conversie programma in.

    ![Migreren vanaf hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Klik op aanvraag verzenden.

## <a name="migration-history"></a>Migratie geschiedenis

Wanneer u de werk ruimte/project migratie van de hub hebt aangevraagd, vindt u de migratie geschiedenis op de pagina aangepaste Vertaal instellingen.

Voer de volgende stappen uit om de migratie geschiedenis weer te geven:

1. Ga naar de pagina instellingen in de aangepaste Vertaal Portal.

2. Klik in de sectie migratie geschiedenis van de pagina instellingen op migratie geschiedenis.

    ![Migratie geschiedenis](media/how-to/how-to-migration-history.png)

Pagina migratie geschiedenis geeft de volgende informatie weer als samen vatting voor elke aangevraagde migratie.

1. Gemigreerd door: naam en e-mail adres van de gebruiker heeft deze migratie aanvraag ingediend

2. Gemigreerd op: datum en tijds tempel van de migratie

3. Projecten: aantal aangevraagde projecten voor de migratie van het aantal projecten dat is gemigreerd.

4. Training: aantal trainingen dat is aangevraagd voor de migratie van het aantal opleidingen dat is gemigreerd.

5. Documenten: het aantal documenten dat is aangevraagd voor de migratie van het aantal documenten dat is gemigreerd.

    ![Details van de migratie geschiedenis](media/how-to/how-to-migration-history-details.png)

Als u meer gedetailleerde migratie rapporten wilt over uw projecten, trainingen en documenten, hebt u de optie Details exporteren als CSV.

## <a name="implementation-notes"></a>Implementatienotities
* Systemen met taal paren die nog niet beschikbaar zijn in het aangepaste conversie programma, zijn alleen beschikbaar voor toegang tot gegevens of het ontoegankelijk maken van de implementatie via een aangepaste vertaler. Deze projecten worden gemarkeerd als ' niet beschikbaar ' op de pagina projecten. Wanneer we nieuwe taal paren met aangepaste Translator inschakelen, worden de projecten actief voor Train en implementatie. 
* Het migreren van een project van een hub naar een aangepast Vertaal programma heeft geen invloed op uw hub-cursussen of-projecten. Tijdens een migratie worden er geen projecten of documenten van de hub verwijderd en worden de implementatie van modellen niet ongedaan gemaakt.
* U mag slechts één keer per project worden gemigreerd. Als u een migratie voor een project wilt herhalen, kunt u contact met ons opnemen.
* Aangepaste Translator ondersteunt NMT taal paren van en naar het Engels. [Bekijk de volledige lijst met ondersteunde talen](../language-support.md#customization). De hub vereist geen basislijn modellen en ondersteunt daarom een aantal duizenden talen. U kunt een niet-ondersteund taal paar migreren, maar we zullen alleen de migratie van documenten en project definities uitvoeren. Het nieuwe model kan niet worden getraind. Deze documenten en projecten worden bovendien als inactief weer gegeven om aan te geven dat ze op dit moment niet kunnen worden gebruikt. Als ondersteuning is toegevoegd voor deze projecten en/of documenten, worden ze actief en trainbaar.
* Aangepaste Translator biedt momenteel geen ondersteuning voor Monolingual-trainings gegevens. Net als niet-ondersteunde taal paren kunt u Monolingual-documenten migreren, maar ze worden als inactief weer gegeven totdat Monolingual-gegevens worden ondersteund.
* Aangepaste Translator vereist een 10k parallele zin om te kunnen trainen. Micro soft hub kan een kleinere set gegevens trainen. Als een training wordt gemigreerd die niet aan deze vereiste voldoet, wordt deze niet getraind.

## <a name="custom-translator-versus-hub"></a>Aangepaste Translator versus hub

Deze tabel vergelijkt de functies tussen micro soft Translator hub en aangepaste Translator.

| Functie | Hub | Custom Translator |
| ------- | :-: | :---------------: |
| Status van aanpassings functie    | Algemene Beschik baarheid    | Algemene Beschik baarheid |
| Tekst-API-versie    | V2     | V3  |
| SMT-aanpassing    | Ja    | Nee |
| NMT aanpassen    | Nee    | Ja |
| Nieuwe aanpassing van Unified speech Services    | Nee    | Ja |
| Geen tracering | Ja | Ja |

## <a name="new-languages"></a>Nieuwe talen

Als u een community of organisatie bent die bezig is met het maken van een nieuw taal systeem voor Translator, neem dan contact op met [custommt@microsoft.com](mailto:custommt@microsoft.com) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Train een model](how-to-train-model.md).
- Begin met het gebruik van uw geïmplementeerde aangepaste Vertaal model via [Translator v3](../reference/v3-0-translate.md?tabs=curl).