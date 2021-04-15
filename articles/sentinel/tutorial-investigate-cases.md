---
title: Incidenten met Azure Sentinel| Microsoft Docs
description: In deze zelfstudie leert u hoe u Azure Sentinel om geavanceerde waarschuwingsregels te maken die incidenten genereren die u kunt toewijzen en onderzoeken.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2021
ms.author: yelevin
ms.openlocfilehash: 8980a8920b4f41f5a8e6afe106415032eef2055b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375832"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Zelfstudie: Incidenten met Azure Sentinel

> [!IMPORTANT]
> De onderzoeksgrafiek is nu algemeen **beschikbaar.** 

In deze zelfstudie kunt u incidenten met Azure Sentinel. Nadat u uw gegevensbronnen hebt verbonden met Azure Sentinel, wilt u een melding ontvangen wanneer er iets verdachts gebeurt. Als u dit wilt doen, kunt Azure Sentinel geavanceerde waarschuwingsregels maken die incidenten genereren die u kunt toewijzen en onderzoeken.

In dit artikel wordt het volgende beschreven:
> [!div class="checklist"]
> * Incidenten onderzoeken
> * De onderzoeksgrafiek gebruiken
> * Op bedreigingen reageren

Een incident kan meerdere waarschuwingen bevatten. Het is een aggregatie van alle relevante bewijzen voor een specifiek onderzoek. Er wordt een incident gemaakt op basis van analyseregels die u hebt gemaakt op de **pagina** Analyse. De eigenschappen met betrekking tot de waarschuwingen, zoals ernst en status, worden ingesteld op incidentniveau. Nadat u hebt Azure Sentinel welke soorten bedreigingen u zoekt en hoe u deze kunt vinden, kunt u gedetecteerde bedreigingen bewaken door incidenten te onderzoeken.

## <a name="prerequisites"></a>Vereisten
- U kunt het incident alleen onderzoeken als u de velden voor entiteitstoewijzing hebt gebruikt bij het instellen van uw analyseregel. Voor de onderzoeksgrafiek is vereist dat uw oorspronkelijke incident entiteiten omvat.

- Als u een gastgebruiker hebt die incidenten moet toewijzen, moet aan de gebruiker de rol [Directorylezer](../active-directory/roles/permissions-reference.md#directory-readers) worden toegewezen in uw Azure AD-tenant. Aan gewone (niet-gast)gebruikers is deze rol standaard toegewezen.

## <a name="how-to-investigate-incidents"></a>Incidenten onderzoeken

1. Selecteer **Incidenten**. Op **de** pagina Incidenten ziet u hoeveel incidenten u hebt, hoeveel er zijn geopend, hoeveel u hebt ingesteld op **Wordt** uitgevoerd en hoeveel er zijn gesloten. Voor elk incident kunt u zien hoe lang het heeft plaatsgevonden en wat de status van het incident is. Bekijk de ernst om te bepalen welke incidenten het eerst moeten worden verwerkt.

    ![Ernst van incident weergeven](media/tutorial-investigate-cases/incident-severity.png)

1. U kunt de incidenten zo nodig filteren, bijvoorbeeld op status of ernst.

1. Selecteer een specifiek incident om een onderzoek te starten. Aan de rechterkant ziet u gedetailleerde informatie over het incident, waaronder de ernst, een samenvatting van het aantal betrokken entiteiten, de onbewerkte gebeurtenissen die dit incident hebben geactiveerd en de unieke id van het incident.

1. Als u meer informatie wilt weergeven over de waarschuwingen en entiteiten in het incident, selecteert u Volledige details weergeven **op** de incidentpagina en bekijkt u de relevante tabbladen waarin de incidentinformatie wordt samengevat. 

    ![Waarschuwingsdetails weergeven](media/tutorial-investigate-cases/incident-timeline.png)

    Bijvoorbeeld:

    - Bekijk op **het tabblad** Tijdlijn de tijdlijn van waarschuwingen en bladwijzers in het incident, waarmee u de tijdlijn van de aanvalsactiviteiten kunt reconstrueren.
    - Controleer de **waarschuwing** zelf op het tabblad Waarschuwingen. U kunt alle relevante informatie over de waarschuwing bekijken: de query die de waarschuwing heeft geactiveerd, het aantal resultaten dat per query wordt geretourneerd en de mogelijkheid om playbooks uit te voeren op de waarschuwingen. Als u nog verder wilt inzoomen op het incident, selecteert u het aantal **gebeurtenissen**. Hiermee opent u de query die de resultaten en de gebeurtenissen heeft gegenereerd die de waarschuwing in Log Analytics hebben geactiveerd. 
    - Op het **tabblad Entiteiten** ziet u alle entiteiten die u als onderdeel van de definitie van de waarschuwingsregel hebt gekregen.

1. Als u een incident actief onderzoekt, is het een goed idee om de status van het incident in te **stellen op Wordt** uitgevoerd totdat u het sluit.

1. Incidenten kunnen worden toegewezen aan een specifieke gebruiker. Voor elk incident kunt u een eigenaar toewijzen door het veld **Incidenteigenaar in te** stellen. Alle incidenten beginnen als niet-toegewezen. U kunt ook opmerkingen toevoegen, zodat andere analisten kunnen begrijpen wat u hebt onderzocht en wat uw zorgen zijn over het incident.

    ![Incident toewijzen aan gebruiker](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Selecteer **Onderzoeken om** het onderzoekskaart te bekijken.

## <a name="use-the-investigation-graph-to-deep-dive"></a>De onderzoeksgrafiek gebruiken om dieper in te gaan

Met de onderzoeksgrafiek kunnen analisten de juiste vragen stellen voor elk onderzoek. De onderzoeksgrafiek helpt u inzicht te krijgen in het bereik en de hoofdoorzaak van een mogelijke beveiligingsrisico te identificeren door relevante gegevens te correuleren met elke betrokken entiteit. U kunt dieper ingaan op elke entiteit die in de grafiek wordt weergegeven door deze te selecteren en te kiezen tussen verschillende uitbreidingsopties.  
  
De onderzoeksgrafiek biedt u het volgende:

- **Visuele context van onbewerkte gegevens:** de live, visuele grafiek geeft entiteitsrelaties weer die automatisch zijn geëxtraheerd uit de onbewerkte gegevens. Hierdoor kunt u eenvoudig verbindingen tussen verschillende gegevensbronnen zien.

- **Detectie van het volledige onderzoekbereik:** breid uw onderzoeksbereik uit met behulp van ingebouwde onderzoeksquery's om het volledige bereik van een schending te verkennen.

- **Ingebouwde onderzoeksstappen:** gebruik vooraf gedefinieerde verkenningsopties om ervoor te zorgen dat u de juiste vragen stelt bij een bedreiging.

De onderzoeksgrafiek gebruiken:

1. Selecteer een incident en selecteer vervolgens **Onderzoeken.** Hiermee gaat u naar de onderzoeksgrafiek. De grafiek biedt een illustratief overzicht van de entiteiten die rechtstreeks zijn verbonden met de waarschuwing en elke resource die verder is verbonden.

   > [!IMPORTANT] 
   > - U kunt het incident alleen onderzoeken als u de velden voor entiteitstoewijzing hebt gebruikt bij het instellen van uw analyseregel. Voor de onderzoeksgrafiek is vereist dat uw oorspronkelijke incident entiteiten omvat.
   >
   > - Azure Sentinel ondersteunt momenteel onderzoek van incidenten die **maximaal 30 dagen oud zijn.**

   ![Kaart weergeven](media/tutorial-investigate-cases/map1.png)

1. Selecteer een entiteit om het deelvenster **Entiteiten te openen,** zodat u informatie over die entiteit kunt bekijken.

    ![Entiteiten weergeven in kaart](media/tutorial-investigate-cases/map-entities.png)
  
1. Vouw uw onderzoek uit door de muisaanwijzer over elke entiteit te bewegen om een lijst met vragen weer te geven die is ontworpen door onze beveiligingsexperts en analisten per entiteitstype om uw onderzoek te verdiepen. We noemen deze opties **verkenningsquery's**.

    ![Meer details verkennen](media/tutorial-investigate-cases/exploration-cases.png)

   Op een computer kunt u bijvoorbeeld gerelateerde waarschuwingen aanvragen. Als u een verkenningsquery selecteert, worden de resulterende entiteiten weer aan de grafiek toegevoegd. In dit voorbeeld heeft het selecteren **van Gerelateerde waarschuwingen** de volgende waarschuwingen in de grafiek geretourneerd:

    ![Gerelateerde waarschuwingen weergeven](media/tutorial-investigate-cases/related-alerts.png)

1. Voor elke verkenningsquery kunt u de optie selecteren om de onbewerkte gebeurtenisresultaten en de query die wordt gebruikt in Log Analytics te openen door **Gebeurtenissen te selecteren. \>**

1. Om het incident te begrijpen, geeft de grafiek u een parallelle tijdlijn.

    ![Tijdlijn op kaart weergeven](media/tutorial-investigate-cases/map-timeline.png)

1. Beweeg de muisaanwijzer over de tijdlijn om te zien welke dingen in de grafiek op welk moment hebben plaatsgevonden.

    ![Tijdlijn in kaart gebruiken om waarschuwingen te onderzoeken](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>Een incident sluiten

Zodra u een bepaald incident hebt opgelost (bijvoorbeeld wanneer uw onderzoek de conclusie heeft bereikt), moet u de status van het incident instellen op **Gesloten.** Wanneer u dit doet, wordt u gevraagd om het incident te classificeren door de reden op te geven dat u het sluit. Deze stap is verplicht. Klik **op Classificatie** selecteren en kies een van de volgende opties in de vervolgkeuzelijst:

- Terecht-positief - verdachte activiteit
- Goedaardig-positief - verdacht maar verwacht
- Fout-positief - onjuiste waarschuwingslogica
- Fout-positief - onjuiste gegevens
- Onbepaald

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="Schermopname met de classificaties die beschikbaar zijn in de lijst Classificatie selecteren.":::

Nadat u de juiste classificatie heeft gekozen, voegt u beschrijvende tekst toe in het **veld** Opmerking. Dit is handig in het geval dat u naar dit incident wilt verwijzen. Klik **op** Toepassen wanneer u klaar bent en het incident wordt gesloten.

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alt-text}":::

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u aan de slag gaat met het onderzoeken van incidenten met behulp Azure Sentinel. Ga verder met de zelfstudie [voor het reageren op bedreigingen met behulp van geautomatiseerde playbooks.](tutorial-respond-threats-playbook.md)
> [!div class="nextstepaction"]
> [Reageren op bedreigingen](tutorial-respond-threats-playbook.md) om uw reacties op bedreigingen te automatiseren.

