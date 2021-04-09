---
title: Gegevens velden toewijzen aan Azure-Sentinel-entiteiten | Microsoft Docs
description: Gegevens velden in tabellen toewijzen aan Azure Sentinel-entiteiten in analyse regels, voor betere incident gegevens
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: cb91d269f6b166510db54637d17d776e71137408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102456243"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>Gegevens velden toewijzen aan entiteiten in azure Sentinel 

> [!IMPORTANT]
>
> - De nieuwe versie van de functie voor entiteits toewijzing is in **Preview**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

> [!IMPORTANT]
>
> - Zie [opmerkingen over de nieuwe versie](#notes-on-the-new-version) aan het einde van dit document voor belang rijke informatie over achterwaartse compatibiliteit en verschillen tussen de nieuwe en oude versies van entiteits toewijzing.

## <a name="introduction"></a>Introductie

Entiteits toewijzing is een integraal onderdeel van de configuratie van [geplande query Analytics-regels](tutorial-detect-threats-custom.md). De regel uitvoer (waarschuwingen en incidenten) wordt verrijkt met essentiële informatie die fungeert als de bouw stenen van eventuele onderzoeken processen en herstel bewerkingen die volgen.

De procedure die hieronder wordt beschreven, maakt deel uit van de wizard analyse regel maken. Het wordt hier afzonderlijk behandeld om het scenario van het toevoegen of wijzigen van entiteits toewijzingen in een bestaande analyse regel te verhelpen.

## <a name="how-to-map-entities"></a>Entiteiten toewijzen

1. Selecteer in het navigatie menu van de Azure-Sentinel **Analytics**.

1. Selecteer een geplande query regel en klik op **bewerken**. Of maak een nieuwe regel door te klikken op **&#10132; geplande query regel maken** boven aan het scherm.

1. Klik op het tabblad **regel logica instellen** .

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="Velden toewijzen aan entiteiten":::

1. Selecteer in de sectie **waarschuwings verbetering** onder **entiteits toewijzing** een entiteits type in de vervolg keuzelijst **entiteits type** .

1. Selecteer een **id** voor de entiteit. Id's zijn kenmerken van een entiteit die deze voldoende kunnen identificeren. Kies een optie in de vervolg keuzelijst **id** en kies vervolgens een gegevens veld in de vervolg keuzelijst **waarde** die overeenkomt met de id. Met enkele uitzonde ringen wordt de lijst met **waarden** ingevuld door de gegevens velden in de tabel die is gedefinieerd als het onderwerp van de regel query.

    U kunt **Maxi maal drie id's** voor een bepaalde entiteit definiëren. Sommige id's zijn vereist, andere zijn optioneel. U moet ten minste één vereiste id kiezen. Als dat niet het geval is, geeft u een waarschuwings bericht door om aan te geven welke id's vereist zijn. Voor de beste resultaten: voor een maximale unieke identificatie moet u waar mogelijk **sterke id's** gebruiken en meerdere sterke id's gebruiken om de correlatie tussen gegevens bronnen mogelijk te maken. Bekijk de volledige lijst met beschik bare [entiteiten en id's](entities-reference.md).

1. Klik op **nieuwe entiteit toevoegen** om meer entiteiten toe te wijzen. U kunt **Maxi maal vijf entiteiten** toewijzen in één analyse regel. U kunt ook meer dan een van hetzelfde type toewijzen. U kunt bijvoorbeeld twee **IP-** entiteiten toewijzen, één uit het veld *bron-IP-adres* en een van de *IP-adres* velden van het doel. Op deze manier kunt u ze volgen.

    Als u van gedachten verandert of als u een fout hebt gemaakt, kunt u een entiteits toewijzing verwijderen door te klikken op het prullenbak pictogram naast de vervolg keuzelijst entiteit.

1. Wanneer u klaar bent met het toewijzen van entiteiten, klikt u op het tabblad **controleren en maken** . Zodra de validatie van de regel is voltooid, klikt u op **Opslaan**.

## <a name="notes-on-the-new-version"></a>Opmerkingen over de nieuwe versie

- Als u eerder entiteits toewijzingen voor deze analyse regel met de oude versie had gedefinieerd, worden deze toewijzingen weer gegeven in de query code. Entiteits toewijzingen die zijn gedefinieerd onder de nieuwe versie **worden niet weer gegeven in de query code**. Analyse regels kunnen slechts één versie van entiteits toewijzingen tegelijk ondersteunen en de nieuwe versie heeft voor rang. Daarom kunnen met één toewijzing die u hier definieert **elke en alle** toewijzingen die in de query code zijn gedefinieerd, worden **genegeerd** wanneer de query wordt uitgevoerd. 

- Als u nog steeds de **oude versie** van de entiteits toewijzing moet gebruiken (zolang de nieuwe versie nog steeds in preview is), kunt u deze nog steeds openen met behulp van een functie vlag in de URL. Plaats de cursor tussen `https://portal.azure.com/` en en `#blade` Voeg de tekst in `?feature.EntityMapping=false` .

  - De limieten voor de oude versie blijven van toepassing. U kunt alleen de gebruikers, de host, het IP-adres, de URL en de bestandshash en slechts één van beide toewijzen.

  - U moet alle entiteits toewijzingen die zijn gemaakt met behulp van de nieuwe versie **verwijderen** **voordat** u terugkeert naar de oude versie, anders worden entiteits toewijzingen die gebruikmaken van de oude versie **niet werken**.

- Zodra de nieuwe versie van entiteits toewijzing in algemene Beschik baarheid is, is het niet meer mogelijk om de oude versie te gebruiken. Het wordt ten zeerste aanbevolen om uw oude entiteits toewijzingen te migreren naar de nieuwe versie.


## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u gegevens velden kunt toewijzen aan entiteiten in azure Sentinel Analytics-regels. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Down load de volledige afbeelding over [geplande query Analytics-regels](tutorial-detect-threats-custom.md).
- Meer informatie over [entiteiten in azure Sentinel](entities-in-azure-sentinel.md).
