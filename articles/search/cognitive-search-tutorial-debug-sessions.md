---
title: 'Zelfstudie: Gebruik foutopsporingssessies om uw vaardighedenset te diagnosticeren, te herstellen en er wijzigingen in aan te brengen'
titleSuffix: Azure Cognitive Search
description: Foutopsporingssessies (preview) bieden een portalgebaseerde interface om problemen/fouten in uw vaardighedensets te beoordelen en te herstellen
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 8ec39c4616f5a34f8326b56d4f0ba6e15cdad91c
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699114"
---
# <a name="tutorial-diagnose-repair-and-commit-changes-to-your-skillset"></a>Zelfstudie: Veranderingen in uw vaardighedenset diagnosticeren, herstellen en doorvoeren

In dit artikel gebruikt u Azure Portal voor het openen van foutopsporingssessies om problemen met de verstrekte vaardighedenset te herstellen. Er zijn fouten opgetreden in de vaardighedenset die moeten worden opgelost. In deze zelfstudie loopt u door het foutopsporingsproces waarin u problemen met in- en uitvoer van vaardigheden identificeert en oplost.

> [!Important]
> Foutopsporingssessies is een preview-functie die beschikbaar is service level agreement en niet aanbevolen wordt voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Een [gratis account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) of uw huidige abonnement gebruiken
> * Een service-exemplaar van Azure Cognitive Search
> * Een Azure Storage-account
> * [Postman bureaublad-app](https://www.getpostman.com/)

## <a name="create-services-and-load-data"></a>Services maken en gegevens laden

In deze zelfstudie wordt gebruikgemaakt van Azure Cognitive Search- en Azure Storage-services.

* [Voorbeeldgegevens downloaden](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19), die bestaan uit 19 bestanden.

* [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal) of [een bestaand account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) zoeken. 

   Kies dezelfde regio als Azure Cognitive Search om bandbreedtekosten te voorkomen.
   
   Kies het accounttype StorageV2 (algemeen gebruik V2).

* Open de pagina Opslagservices en maak een container. Best practice is om het toegangsniveau 'privé' op te geven. Geef uw container een naam `clinicaltrialdataset`.

* Klik in de container op **Uploaden** om de voorbeeldbestanden te uploaden die u in de eerste stap hebt gedownload en uitgepakt.

* [Een Azure Cognitive Search-service maken](search-create-service-portal.md) of [een bestaande service zoeken](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). U kunt een gratis service voor deze quickstart gebruiken.

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Een HTTP-eindpunt en toegangssleutel ophalen" border="false":::

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="create-data-source-skillset-index-and-indexer"></a>Gegevensbron, vaardighedenset, index en indexeerfunctie maken

In deze sectie worden Postman en een opgegeven verzameling gebruikt om de gegevensbron, vaardighedenset, index en indexeerfunctie van de zoekservice te maken.

1. Als u Postman nog niet hebt geïnstalleerd, kunt u [de Postman-desktop-app hier downloaden](https://www.getpostman.com/).
1. [De foutopsporingssessies voor Postman-verzameling downloaden](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions)
1. Postman starten
1. Selecteer onder **Bestanden** > **Nieuw** de verzameling die u wilt importeren.
1. Nadat de verzameling is geïmporteerd, vouwt u de lijst met acties uit (...).
1. Klik op **Bewerken**.
1. Voer de naam van uw zoekservice in (als het eindpunt bijvoorbeeld `https://mydemo.search.windows.net` is, is de servicenaam '`mydemo`').
1. Voer de apiKey in met de primaire of de secundaire sleutel van uw zoekservice.
1. Voer de storageConnectionString in op de pagina Sleutels van uw Azure Storage-account.
1. Voer de containerName in voor de container die u in het opslagaccount hebt gemaakt.

> :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="variabelen bewerken in Postman":::

De verzameling bevat vier verschillende REST-aanroepen die worden gebruikt om deze sectie te voltooien.

Met de eerste aanroep maakt u de gegevensbron. `clinical-trials-ds`. Met de tweede aanroep maakt u de vaardighedenhedenset, `clinical-trials-ss`. Met de derde aanroep maakt u de index, `clinical-trials`. Met de vierde en laatste aanroep maakt u de indexeerfunctie `clinical-trials-idxr`. Nadat alle aanroepen in de verzameling zijn voltooid, sluit u Postman en keert u terug naar Azure Portal.

> :::image type="content" source="media/cognitive-search-debug/postman-create-data-source.png" alt-text="Postman gebruiken om gegevensbron te maken":::

## <a name="check-the-results"></a>De resultaten controleren

De vaardighedenset bevat enkele veelvoorkomende fouten. In deze sectie worden meerdere fouten weergegeven wanneer een lege query wordt uitgevoerd om alle documenten te retourneren. In de volgende stappen worden de problemen opgelost met behulp van een foutopsporingssessie.

1. Ga naar uw zoekservice in Azure Portal. 
1. Selecteer het tabblad **Indexen**. 
1. De index `clinical-trials` selecteren
1. Klik op **Zoeken** om een lege query uit te voeren. 

Nadat de zoekopdracht is voltooid, worden twee velden zonder gegevens weergegeven: 'organizations' en 'locations' worden in het venster weergegeven. Volg de stappen om alle problemen te ontdekken die met de vaardighedenset zijn gegenereerd.

1. Ga terug naar de pagina Overzicht van de zoekservice.
1. Selecteer het tabblad **Indexeerfuncties**. 
1. Klik op `clinical-trials-idxr` en selecteer het waarschuwingsbericht. 

Er zijn veel problemen met veldtoewijzingen voor projectie-uitvoer en op pagina drie staan waarschuwingen, omdat een of meer invoerwaarden van de vaardigheid ongeldig zijn.

Ga terug naar het scherm Overzicht van de zoekservice.

## <a name="start-your-debug-session"></a>Uw foutopsporingssessie starten

> :::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="een nieuwe foutopsporingssessie starten":::

1. Klik op het tabblad Foutopsporingssessies (preview-versie).
1. Selecteer +NewDebugSession
1. Geef de sessie een naam. 
1. Verbind de sessie met uw opslagaccount. 
1. Geef de naam van de indexeerfunctie op. De indexeerfunctie heeft verwijzingen naar de gegevensbron, de vaardighedenset en de index.
1. Accepteer de standaard documentkeuze voor het eerste document in de verzameling. 
1. **Sla** de sessie op. Als u de sessie opslaat, wordt de AI-verrijkingspijplijn gestart zoals gedefinieerd door de vaardighedenset.

> [!Important]
> Een foutopsporingssessie werkt alleen met één document. U kunt een specifiek document in de gegevensset > selecteren of de sessie wordt standaard ingesteld op het eerste document.

> :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="Nieuwe foutopsporingssessie gestart":::

Wanneer de foutopsporingssessie is voltooid, wordt de sessie standaard ingesteld op het tabblad AI-verrijkingen, waarbij de vaardigheidsgrafiek wordt gemarkeerd.

+ De vaardigheidsgrafiek bevat een visuele hiërarchie van de vaardighedenset en de volgorde waarin deze van boven naar beneden wordt uitgevoerd. De vaardigheden die naast elkaar in de grafiek staan, worden parallel uitgevoerd. Met kleurcodering van vaardigheden in de grafiek worden de soorten vaardigheden aangegeven die in de vaardighedenset worden uitgevoerd. In het voorbeeld zijn de groene vaardigheden text en is de rode vaardigheid vision. Als u op een afzonderlijke vaardigheid in de grafiek klikt, worden de details van die instantie van de vaardigheid in het rechterdeelvenster van het sessievenster weergegeven. De instellingen voor vaardigheden, een JSON-editor, uitvoeringsdetails en fouten/waarschuwingen kunnen allemaal worden gecontroleerd en bewerkt.
+ De verrijkte gegevensstructuur toont de knooppunten in de verrijkingsstructuur die worden gegenereerd door de vaardigheden uit de inhoud van het brondocument.

Op het tabblad Fouten/waarschuwingen vindt u een veel kortere lijst dan de lijst die eerder werd weergegeven, omdat in deze lijst alleen de fouten voor één document worden beschreven. Net als voor de lijst die wordt weergegeven door de indexeerfunctie, kunt u op een waarschuwingsbericht klikken en de details van deze waarschuwing weergeven.

## <a name="fix-missing-skill-input-value"></a>Ontbrekende invoerwaarde van vaardigheid herstellen

Op het tabblad Fouten/waarschuwingen vindt u een foutmelding voor een bewerking met het label `Enrichment.NerSkillV2.#1`. In de beschrijving van deze fout wordt aangegeven dat er een probleem is met de vaardigheidsinvoerwaarde /document/languageCode. 

1. Ga terug naar het tabblad AI-verrijkingen.
1. Klik op de **Vaardigheidsgrafiek**.
1. Klik op de vaardigheid met het label #1 om de details ervan weer te geven in het rechterdeelvenster.
1. Zoek naar de invoerwaarde voor 'languageCode'.
1. Selecteer het symbool **</>** aan het begin van de regel en open de expressie-evaluator.
1. Klik op de knop **Evalueren** om te bevestigen dat deze expressie een fout heeft opgeleverd. Er wordt bevestigd dat de eigenschap 'languageCode' een ongeldige invoer is.

> :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Expressie-evaluator":::

Er zijn twee manieren om deze fout in de sessie te onderzoeken. U kunt eerst kijken waar de invoer vandaan komt. Welke vaardigheid in de hiërarchie hoort dit resultaat te produceren? Op het tabblad Uitvoerbewerkingen in het detailvenster Vaardigheden vindt u de bron van de invoer. Als er geen bron is, duidt dit op een fout in de veldtoewijzing.

1. Klik op het tabblad **Uitvoerbewerkingen**.
1. Bekijk de INPUTS en zoek 'languageCode'. Er is geen bron voor deze invoer vermeld. 
1. Schakel het linkerdeelvenster in om de verrijkte gegevensstructuur weer te geven. Er is geen toegewezen pad dat overeenkomt met 'languageCode'.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Verrijkte gegevensstructuur":::

Er is een toegewezen pad voor 'language'. Er is dus een typfout in de vaardigheidsinstellingen. U kunt dit oplossen door de expressie in de vaardigheid #1 met de expressie /document/language bij te werken.

1. Open de expressie-evaluator **</>** voor het pad 'language'.
1. Kopieer de expressie. Sluit het venster.
1. Ga naar de vaardigheidsinstellingen voor de vaardigheid #1 en open de expressie-evaluator **</>** voor de invoer 'languageCode'.
1. Plak de nieuwe waarde /document/language in het vak Expressie en klik op **Evalueren**.
1. De juiste invoer 'en' moet worden weergegeven. Klik op Toepassen om de expressie bij te werken.
1. Klik op **Opslaan** in het detailvenster Vaardigheid.
1. Klik op **Uitvoeren** in het venstermenu van de sessie. Hiermee wordt een andere uitvoering van de vaardighedenset met behulp van het document gestart. 

Nadat de uitvoering van de foutopsporingssessie is voltooid, klikt u op het tabblad Fouten/waarschuwingen. Hier ziet u dat de fout 'Enrichment.NerSkillV2.#1' is verdwenen. Er zijn echter nog twee waarschuwingen dat de service geen uitvoervelden voor organizations en locations aan de zoekindex kan toewijzen. Er ontbreken waarden: /document/merged_content/organizations en /document/merged_content/locations.

## <a name="fix-missing-skill-output-values"></a>Ontbrekende uitvoerwaarden voor vaardigheid herstellen

> :::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Fouten en waarschuwingen":::

Er ontbreken uitvoerwaarden van een vaardigheid. Als u de vaardigheid met de fout wilt identificeren, gaat u naar de verrijkte gegevensstructuur, zoekt u de waardenaam en bekijkt u de oorspronkelijke bron. De ontbrekende waarden voor organisaties en locaties zijn uitvoerwaarden van vaardigheid #1. Als u de expressie-evaluator </> voor elk pad opent, worden respectievelijk de expressies /document/content/organizations en /document/content/locations weergegeven.

> :::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Expressie-evaluator voor de entiteit organizations":::

De uitvoer voor deze entiteiten is leeg en mag niet leeg zijn. Wat zijn de invoerwaarden die dit resultaat opleveren?

1. Ga naar **Vaardigheidsgrafiek** en selecteer vaardigheid #1.
1. Selecteer het tabblad **Uitvoerbewerkingen** in het rechterdetailvenster van de vaardigheid.
1. Open de expressie-evaluator **</>** voor de INPUT 'text'.

> :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Invoer voor vaardigheid text":::

Het weergegeven resultaat voor deze invoer ziet er niet uit als een tekstinvoer. Het lijkt op een afbeelding die wordt omgeven door nieuwe lijnen. Het ontbreken van tekst betekent dat er geen entiteiten kunnen worden geïdentificeerd. In de hiërarchie van de vaardighedenset ziet u dat de inhoud eerst wordt verwerkt door vaardigheid #6 (OCR) en vervolgens wordt doorgegeven aan vaardigheid #5 (samenvoegen). 

1. Selecteer vaardigheid #5 (samenvoegen) in de **Vaardigheidsgrafiek**.
1. Selecteer het tabblad **Uitvoerbewerkingen** in het detailvenster van de vaardigheid en open de expressie-evaluator **</>** voor de OUTPUTS 'mergedText'.

> :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Uitvoer voor de vaardigheid Samenvoegen":::

Hier wordt de tekst gekoppeld aan de afbeelding. In de expressie /document/merged_content is de fout in de paden 'organizations' en 'locations' voor vaardigheid #1 zichtbaar. In plaats van /document/content moet /document/merged_content worden gebruikt voor de invoerwaarde 'text'.

1. Kopieer de expressie voor de uitvoerwaarde 'mergedText' en sluit het venster Expressie-evaluator.
1. Selecteer vaardigheid #1 in de **Vaardigheidsgrafiek**.
1. Selecteer het tabblad **Vaardigheidsinstellingen** in het rechterdetailvenster van de vaardigheid.
1. Open de expressie-evaluator **</>** voor de invoerwaarde 'text'.
1. Plak de nieuwe expressie in het vak. Klik op **Evalueren**.
1. De juiste invoer met de toegevoegde tekst moet worden weergegeven. Klik op **Toepassen** om de vaardigheidsinstellingen bij te werken.
1. Klik op **Opslaan** in het detailvenster Vaardigheid.
1. Klik op **Uitvoeren** in het venstermenu van de sessie. Hiermee wordt een andere uitvoering van de vaardighedenset met behulp van het document gestart.

Nadat de indexeerfunctie is uitgevoerd, zijn de fouten nog steeds aanwezig. Ga terug naar vaardigheid #1 en onderzoek het probleem. De invoer voor de vaardigheid is gewijzigd in merged_content van content. Wat zijn de uitvoerwaarden van deze entiteiten in de vaardigheid?

1. Selecteer het tabblad **AI-verrijkingen**.
1. Ga naar **Vaardigheidsgrafiek** en selecteer vaardigheid #1.
1. Zoek in de **vaardigheidsinstellingen** naar de 'uitvoerwaarden'.
1. Open de expressie-evaluator **</>** voor de entiteit 'organizations'.

> :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Uitvoer voor de entiteit organizations":::

Als u het resultaat van de expressie evalueert, wordt het juiste resultaat geretourneerd. De vaardigheid is bezig met het identificeren van de juiste waarde voor de entiteit 'organizations'. De uitvoertoewijzing in het pad van de entiteit levert echter nog steeds een fout op. Bij het vergelijken van het uitvoerpad in de vaardigheid met het uitvoerpad dat een fout oplevert, de vaardigheid die boven de uitvoerwaarden ligt, organizations en locations onder het knooppunt /document/content. Terwijl bij de toewijzing van het uitvoerveld wordt verwacht dat de resultaten onder het knooppunt/document/merged_content vallen. In de vorige stap is de invoer gewijzigd van /document/content in /document/merged_content. De context van de vaardigheidsinstellingen moet worden gewijzigd om ervoor te zorgen dat de uitvoer wordt gegenereerd met de juiste context.

1. Selecteer het tabblad **AI-verrijkingen**.
1. Ga naar **Vaardigheidsgrafiek** en selecteer vaardigheid #1.
1. Zoek in de **vaardigheidsinstellingen** naar 'context'.
1. Dubbelklik op de instelling voor 'context' en bewerk deze om /document/merged_content te lezen.
1. Klik op **Opslaan** in het detailvenster Vaardigheid.
1. Klik op **Uitvoeren** in het venstermenu van de sessie. Hiermee wordt een andere uitvoering van de vaardighedenset met behulp van het document gestart.

> :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Correctie van context in vaardigheidsinstelling":::

Alle fouten zijn opgelost.

## <a name="commit-changes-to-the-skillset"></a>Wijzigingen doorvoeren in de vaardighedenset

Toen de foutopsporingssessie werd gestart, heeft de zoekservice een kopie van de vaardighedenset gemaakt. Dit is gedaan om wijzigingen die zijn ingevoerd niet het productiesysteem te laten beïnvloeden. Nu u klaar bent met het opsporen van fouten in uw vaardighedenset, kunnen de fixes worden doorgevoerd (de oorspronkelijke vaardighedenset overschrijven) in het productiesysteem. Als u wilt doorgaan met het wijzigen van de vaardighedenset zonder dat dit van invloed is op het productiesysteem, kunt u de foutopsporingssessie opslaan voor later gebruik.

1. Klik op **Wijzigingen doorvoeren** in het menu Foutopsporingssessies.
1. Klik op **OK** om te bevestigen dat u uw vaardighedenset wilt bijwerken.
1. Sluit de foutopsporingssessie en selecteer het tabblad **Indexeerfuncties**.
1. Open uw clinical-trials-idxr.
1. Klik op **Opnieuw instellen**.
1. Klik op **Run**. Klik op **OK** om te bevestigen.

Als de indexeerfunctie is voltooid, ziet u een groen vinkje en staat het woord Voltooid naast de tijdstempel voor de meest recente uitvoering op het tabblad Uitvoeringsgeschiedenis. Zo kunt u controleren of de wijzigingen zijn toegepast:

1. Sluit **Indexeerfunctie** af en selecteer het tabblad **Index**.
1. Open de index clinical-trials en klik op het tabblad Search Explorer op **Zoeken**.
1. In het resultaatvenster ziet u dat de entiteiten organizations en locations nu zijn ingevuld met de verwachte waarden.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over vaardighedensets](./cognitive-search-working-with-skillsets.md)
> [Meer informatie over incrementele verrijking en caching](./cognitive-search-incremental-indexing-conceptual.md)