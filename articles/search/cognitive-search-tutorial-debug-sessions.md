---
title: 'Zelf studie: debug-sessies gebruiken om vaardig heden te herstellen'
titleSuffix: Azure Cognitive Search
description: Debug Sessions (preview) is een Azure Portal hulp programma dat wordt gebruikt voor het zoeken, vaststellen en oplossen van problemen in een vaardig heden.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/02/2021
ms.openlocfilehash: ed988baec46152d55cf63aec09fce7a298157212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509142"
---
# <a name="tutorial-debug-a-skillset-using-debug-sessions"></a>Zelf studie: fouten opsporen in een vaardig heden met behulp van debug-sessies

Vaardig heden coördineert een reeks acties waarmee inhoud wordt geanalyseerd of getransformeerd, waarbij de uitvoer van de ene vaardigheid de invoer van een andere is. Wanneer invoer afhankelijk is van uitvoer, kunnen fouten in de definities van vaardig heden en veld koppelingen worden veroorzaakt door gemiste bewerkingen en gegevens.

**Fout opsporing van sessies** in de Azure Portal biedt een holistische visualisatie van een vaardig heden. Met dit hulp programma kunt u inzoomen op specifieke stappen om eenvoudig te zien waar een actie kan worden ingedrukt.

In dit artikel gebruikt u Debug- **sessies** om 1 te zoeken en op te lossen, een ontbrekende invoer en 2) uitvoer veld toewijzingen. De zelf studie is helemaal inclusief. Het biedt gegevens die u kunt indexeren (gegevens van klinisch experimenteren), een postman-verzameling waarmee objecten worden gemaakt en instructies voor het gebruik van **fout opsporings sessies** om problemen in de vaardig heden te vinden en op te lossen.

> [!Important]
> Foutopsporingssessies is een preview-functie die beschikbaar is service level agreement en niet aanbevolen wordt voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u beschikken over de volgende vereisten:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

+ Een Azure Storage account met [Blob Storage](../storage/blobs/index.yml), dat wordt gebruikt voor het hosten van voorbeeld gegevens en voor het persistent maken van tijdelijke gegevens die zijn gemaakt tijdens een foutopsporingssessie.

+ [Postman desktop-app](https://www.getpostman.com/) en een [postman-verzameling](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) om objecten te maken met BEhulp van de rest-api's.

+ [Voorbeeld gegevens (klinisch onderzoek)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19).

> [!NOTE]
> Deze quickstart maakt ook gebruik van [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) voor de AI. Omdat de workload zo klein is, wordt de gratis verwerking (maximaal 20 transacties) van Cognitive Services achter de schermen gebruikt. Dit betekent dat u deze oefening kunt doen zonder dat u een nieuwe Cognitive Services-resource moet aanmaken.

## <a name="set-up-your-data"></a>Uw gegevens voorbereiden

In deze sectie maakt u de voor beeld-gegevensset in Azure Blob-opslag, zodat de Indexeer functie en de vaardig heden inhoud kunnen gebruiken.

1. [Down load voorbeeld gegevens (klinisch onderzoek-PDF-19)](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-pdf-19), bestaande uit 19 bestanden.

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal) of [een bestaand account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) zoeken. 

   + Kies dezelfde regio als Azure Cognitive Search om bandbreedtekosten te voorkomen.

   + Kies het accounttype StorageV2 (algemeen gebruik V2).

1. Ga naar de pagina's van Azure Storage services in de portal en maak een BLOB-container. Best practice is om het toegangsniveau 'privé' op te geven. Geef uw container een naam `clinicaltrialdataset`.

1. Klik in de container op **Uploaden** om de voorbeeldbestanden te uploaden die u in de eerste stap hebt gedownload en uitgepakt.

1. In de portal kunt u de connection string voor Azure Storage ophalen en opslaan. U hebt deze nodig voor de REST API-aanroepen die gegevens indexeren. U kunt de Connection String ophalen via **instellingen**  >  **toegangs sleutels** in de portal.

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

REST-aanroepen hebben voor elke aanvraag de service-URL en een toegangssleutel nodig. Een zoekservice wordt gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, volgt u deze stappen om de benodigde informatie op te halen:

1. [Meld u aan bij Azure Portal](https://portal.azure.com/) en haal op de pagina **Overzicht** van uw zoekservice de URL op. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

1. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Een HTTP-eindpunt en toegangssleutel ophalen" border="false":::

Voor alle aanvragen is een API-sleutel vereist op elke aanvraag die naar uw service wordt verzonden. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="create-data-source-skillset-index-and-indexer"></a>Gegevensbron, vaardighedenset, index en indexeerfunctie maken

In deze sectie worden postman en een gegeven verzameling gebruikt voor het maken van de Cognitive Search gegevens bron, de vaardig heden, de index en de indexer. Als u niet bekend bent met Postman, raadpleegt u [deze Snelstartgids](search-get-started-rest.md).

U hebt de [postman-verzameling](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) gemaakt voor deze zelf studie om deze taak te volt ooien. 

1. Begin met het verzenden van de verzameling. Selecteer onder **Bestanden** > **Nieuw** de verzameling die u wilt importeren.
1. Nadat de verzameling is geïmporteerd, vouwt u de lijst met acties uit (...).
1. Klik op **Bewerken**.
1. Voer onder huidige waarde de naam van uw `searchService` (bijvoorbeeld, als het eind punt is `https://mydemo.search.windows.net` , de service naam is `mydemo` ).
1. Voer de `apiKey` primaire of secundaire sleutel van uw zoek service in.
1. Voer `storageConnectionString` op de pagina sleutels van uw Azure Storage-account de in.
1. Voer de `containerName` in voor de container die u in het opslag account hebt gemaakt en klik vervolgens op **bijwerken**.

   :::image type="content" source="media/cognitive-search-debug/postman-enter-variables.png" alt-text="variabelen bewerken in Postman":::

De verzameling bevat vier verschillende REST-aanroepen die worden gebruikt voor het maken van objecten die in deze zelf studie worden gebruikt.

Met de eerste aanroep maakt u de gegevensbron. `clinical-trials-ds`. Met de tweede aanroep maakt u de vaardighedenhedenset, `clinical-trials-ss`. Met de derde aanroep maakt u de index, `clinical-trials`. Met de vierde en laatste aanroep maakt u de indexeerfunctie `clinical-trials-idxr`.

+ Open elke aanvraag op zijn beurt en klik op **verzenden** om elke aanvraag naar de zoek service te verzenden. 

Nadat alle aanroepen in de verzameling zijn voltooid, sluit u Postman en keert u terug naar Azure Portal.

## <a name="check-results-in-the-portal"></a>Resultaten controleren in de portal

De voorbeeld code maakt opzettelijk een probleem met de fout opsporing als gevolg van problemen die zijn opgetreden tijdens de uitvoering van de vaardig heden. Er ontbreken gegevens in het probleem. 

1. Selecteer in Azure Portal op de pagina overzicht van zoek services het tabblad **indexen** . 
1. Selecteer de `clinical-trials` index.
1. Voer deze query reeks `$select=metadata_storage_path, organizations, locations&$count=true` in: om velden te retour neren voor specifieke documenten (aangeduid met het unieke `metadata_storage_path` veld).
1. Klik op **zoeken** om de query uit te voeren, waarbij alle 19 documenten worden geretourneerd, waarbij lege waarden worden weer gegeven voor "organisaties" en "locaties".

Deze velden moeten worden ingevuld via de [vaardigheids kwalificatie](cognitive-search-skill-entity-recognition.md)van de vaardig heden, die wordt gebruikt voor het vinden van organisaties en locaties overal in de inhoud van de blob. In de volgende oefening gebruikt u foutopsporingssessie om te bepalen wat er mis is gegaan.

Een andere manier om fouten en waarschuwingen te onderzoeken, is via de Azure Portal.

1. Open het tabblad **Indexeer functies** en selecteer `clinical-trials-idxr` .
1. U ziet dat tijdens de indexering van de Indexeer functie een totaal van 57 waarschuwingen is.
1. Klik op **geslaagd** om de waarschuwingen weer te geven (als er meestal fouten zijn opgetreden, **is** de koppeling naar Details mislukt). U ziet een lange lijst met elke waarschuwing die wordt verzonden door de Indexeer functie.

   :::image type="content" source="media/cognitive-search-debug/portal-indexer-errors-warnings.png" alt-text="waarschuwingen weer geven":::

## <a name="start-your-debug-session"></a>Uw foutopsporingssessie starten

:::image type="content" source="media/cognitive-search-debug/new-debug-session-screen-required.png" alt-text="een nieuwe foutopsporingssessie starten":::

1. Klik op de pagina overzicht van zoek opdrachten op het tabblad **fouten opsporen** .
1. Selecteer **+ nieuwe foutopsporingssessie**.
1. Geef de sessie een naam. 
1. Verbind de sessie met uw opslagaccount. 
1. Geef in de sjabloon Indexere naam op voor de Indexeer functie. De indexeerfunctie heeft verwijzingen naar de gegevensbron, de vaardighedenset en de index.
1. Accepteer de standaard documentkeuze voor het eerste document in de verzameling. 
1. **Sla** de sessie op. Als u de sessie opslaat, wordt de AI-verrijkingspijplijn gestart zoals gedefinieerd door de vaardighedenset.

> [!Important]
> Een foutopsporingssessie werkt alleen met één document. U kunt kiezen welk document moet worden opgespoord, of alleen de eerste gebruiken.

<!-- > :::image type="content" source="media/cognitive-search-debug/debug-execution-complete1.png" alt-text="New debug session started"::: -->

Wanneer de fout opsporing is voltooid, wordt de sessie standaard ingesteld op het tabblad **AI-verrijkingen** , waarbij de **vaardigheids grafiek** wordt gemarkeerd. De vaardigheids grafiek biedt een visuele hiërarchie van de vaardig heden en de volg orde van uitvoering opeenvolgend en parallel.

## <a name="find-issues-with-the-skillset"></a>Problemen met de vaardig heden vinden

Alle problemen die worden gerapporteerd door de Indexeer functie vindt u op het tabblad aangrenzende **fouten/waarschuwingen** . 

U ziet dat het tabblad **fouten/waarschuwingen** een veel kleinere lijst levert dan het gedeelte dat eerder wordt weer gegeven, omdat in deze lijst alleen de fouten voor één document worden beschreven. Net als voor de lijst die wordt weergegeven door de indexeerfunctie, kunt u op een waarschuwingsbericht klikken en de details van deze waarschuwing weergeven.

Selecteer **fouten/waarschuwingen** om de meldingen te bekijken. U ziet nu drie:

   + ' Locaties van het uitvoer veld kunnen niet worden toegewezen aan de zoek index. Controleer de eigenschap outputFieldMappings van de Indexeer functie.
Ontbrekende waarde '/document/merged_content/locations '. '

   + Kan het uitvoer veld ' organisaties ' niet toewijzen aan de zoek index. Controleer de eigenschap outputFieldMappings van de Indexeer functie.
Ontbrekende waarde '/document/merged_content/organizations '. '

   + "De vaardigheid is uitgevoerd, maar kan onverwachte resultaten opleveren omdat een of meer vaardigheids invoer ongeldig is.
Optionele vaardigheids invoer ontbreekt. Naam: ' language code ', Bron: '/document/languageCode '. Problemen met het parseren van expressie taal: ontbrekende waarde '/document/languageCode '. '

   Veel vaardig heden hebben een ' language code-para meter. Door de bewerking te controleren, kunt u zien dat de invoer van de taal code ontbreekt in de `Enrichment.NerSkillV2.#1` . Dit is dezelfde entiteit herkennings vaardigheid die problemen heeft met de uitvoer van locaties en organisaties. 

Omdat alle drie de meldingen over deze vaardigheid zijn, is de volgende stap het opsporen van fouten in deze vaardigheid. Als dat mogelijk is, kunt u eerst beginnen met het oplossen van invoer problemen voordat u outputFieldMapping-problemen gaat verhelpen.

 :::image type="content" source="media/cognitive-search-debug/debug-session-errors-warnings.png" alt-text="Nieuwe foutopsporingssessie gestart":::

<!-- + The Skill Graph provides a visual hierarchy of the skillset and its order of execution from top to bottom. Skills that are side by side in the graph are executed in parallel. Color coding of skills in the graph indicate the types of skills that are being executed in the skillset. In the example, the green skills are text and the red skill is vision. Clicking on an individual skill in the graph will display the details of that instance of the skill in the right pane of the session window. The skill settings, a JSON editor, execution details, and errors/warnings are all available for review and editing.

+ The Enriched Data Structure details the nodes in the enrichment tree generated by the skills from the source document's contents. -->

## <a name="fix-missing-skill-input-value"></a>Ontbrekende invoerwaarde van vaardigheid herstellen

Op het tabblad **fouten/waarschuwingen** vindt u een fout melding voor een bewerking met het label `Enrichment.NerSkillV2.#1` . In de beschrijving van deze fout wordt aangegeven dat er een probleem is met de vaardigheidsinvoerwaarde /document/languageCode. 

1. Ga terug naar het tabblad **verrijkingen voor AI** .
1. Klik op de **Vaardigheidsgrafiek**.
1. Klik op de vaardigheid met het label **#1** om de details ervan weer te geven in het rechterdeel venster.
1. Zoek naar de invoerwaarde voor 'languageCode'.
1. Selecteer het symbool **</>** aan het begin van de regel en open de expressie-evaluator.
1. Klik op de knop **Evalueren** om te bevestigen dat deze expressie een fout heeft opgeleverd. Er wordt bevestigd dat de eigenschap 'languageCode' een ongeldige invoer is.

   :::image type="content" source="media/cognitive-search-debug/expression-evaluator-language.png" alt-text="Expressie-evaluator":::

Er zijn twee manieren om deze fout in de sessie te onderzoeken. U kunt eerst kijken waar de invoer vandaan komt. Welke vaardigheid in de hiërarchie hoort dit resultaat te produceren? Op het tabblad Uitvoerbewerkingen in het detailvenster Vaardigheden vindt u de bron van de invoer. Als er geen bron is, duidt dit op een fout in de veldtoewijzing.

1. Klik op het tabblad **Uitvoerbewerkingen**.
1. Bekijk de INPUTS en zoek 'languageCode'. Er is geen bron voor deze invoer vermeld. 
1. Schakel het linkerdeelvenster in om de verrijkte gegevensstructuur weer te geven. Er is geen toegewezen pad dat overeenkomt met 'languageCode'.

   :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-language.png" alt-text="Verrijkte gegevensstructuur":::

Er is een toegewezen pad voor 'language'. Er is dus een typfout in de vaardigheidsinstellingen. Om deze expressie op te lossen in de #1 vaardigheid met de expressie '/document/language ' moet worden bijgewerkt.

1. Open de expressie-evaluator **</>** voor het pad 'language'.
1. Kopieer de expressie. Sluit het venster.
1. Ga naar de vaardigheidsinstellingen voor de vaardigheid #1 en open de expressie-evaluator **</>** voor de invoer 'languageCode'.
1. Plak de nieuwe waarde /document/language in het vak Expressie en klik op **Evalueren**.
1. De juiste invoer 'en' moet worden weergegeven. Klik op Toepassen om de expressie bij te werken.
1. Klik op **Opslaan** in het detailvenster Vaardigheid.
1. Klik op **Uitvoeren** in het venstermenu van de sessie. Hiermee wordt een andere uitvoering van de vaardighedenset met behulp van het document gestart. 

Nadat de uitvoering van de foutopsporingssessie is voltooid, klikt u op het tabblad Fouten/waarschuwingen. Hier ziet u dat de fout 'Enrichment.NerSkillV2.#1' is verdwenen. Er zijn echter nog twee waarschuwingen dat de service geen uitvoervelden voor organizations en locations aan de zoekindex kan toewijzen. Er ontbreken waarden: /document/merged_content/organizations en /document/merged_content/locations.

## <a name="fix-missing-skill-output-values"></a>Ontbrekende uitvoerwaarden voor vaardigheid herstellen

:::image type="content" source="media/cognitive-search-debug/warnings-missing-value-locations-organizations.png" alt-text="Fouten en waarschuwingen":::

Er ontbreken uitvoerwaarden van een vaardigheid. Als u de vaardigheid met de fout wilt identificeren, gaat u naar de verrijkte gegevensstructuur, zoekt u de waardenaam en bekijkt u de oorspronkelijke bron. De ontbrekende waarden voor organisaties en locaties zijn uitvoerwaarden van vaardigheid #1. Als u de expressie-evaluator </> voor elk pad opent, worden respectievelijk de expressies /document/content/organizations en /document/content/locations weergegeven.

:::image type="content" source="media/cognitive-search-debug/expression-eval-missing-value-locations-organizations.png" alt-text="Expressie-evaluator voor de entiteit organizations":::

De uitvoer voor deze entiteiten is leeg en mag niet leeg zijn. Wat zijn de invoerwaarden die dit resultaat opleveren?

1. Ga naar **Vaardigheidsgrafiek** en selecteer vaardigheid #1.
1. Selecteer het tabblad **Uitvoerbewerkingen** in het rechterdetailvenster van de vaardigheid.
1. Open de expressie-evaluator **</>** voor de INPUT 'text'.

   :::image type="content" source="media/cognitive-search-debug/input-skill-missing-value-locations-organizations.png" alt-text="Invoer voor vaardigheid text":::

Het weergegeven resultaat voor deze invoer ziet er niet uit als een tekstinvoer. Het lijkt op een afbeelding die wordt omgeven door nieuwe lijnen. Het ontbreken van tekst betekent dat er geen entiteiten kunnen worden geïdentificeerd. In de hiërarchie van de vaardighedenset ziet u dat de inhoud eerst wordt verwerkt door vaardigheid #6 (OCR) en vervolgens wordt doorgegeven aan vaardigheid #5 (samenvoegen). 

1. Selecteer vaardigheid #5 (samenvoegen) in de **Vaardigheidsgrafiek**.
1. Selecteer het tabblad **Uitvoerbewerkingen** in het detailvenster van de vaardigheid en open de expressie-evaluator **</>** voor de OUTPUTS 'mergedText'.

   :::image type="content" source="media/cognitive-search-debug/merge-output-detail-missing-value-locations-organizations.png" alt-text="Uitvoer voor de vaardigheid Samenvoegen":::

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

   :::image type="content" source="media/cognitive-search-debug/skill-output-detail-missing-value-locations-organizations.png" alt-text="Uitvoer voor de entiteit organizations":::

Als u het resultaat van de expressie evalueert, wordt het juiste resultaat geretourneerd. De vaardigheid is bezig met het identificeren van de juiste waarde voor de entiteit 'organizations'. De uitvoertoewijzing in het pad van de entiteit levert echter nog steeds een fout op. Bij het vergelijken van het uitvoerpad in de vaardigheid met het uitvoerpad dat een fout oplevert, de vaardigheid die boven de uitvoerwaarden ligt, organizations en locations onder het knooppunt /document/content. Terwijl bij de toewijzing van het uitvoerveld wordt verwacht dat de resultaten onder het knooppunt/document/merged_content vallen. In de vorige stap is de invoer gewijzigd van /document/content in /document/merged_content. De context van de vaardigheidsinstellingen moet worden gewijzigd om ervoor te zorgen dat de uitvoer wordt gegenereerd met de juiste context.

1. Selecteer het tabblad **AI-verrijkingen**.
1. Ga naar **Vaardigheidsgrafiek** en selecteer vaardigheid #1.
1. Zoek in de **vaardigheidsinstellingen** naar 'context'.
1. Dubbelklik op de instelling voor 'context' en bewerk deze om /document/merged_content te lezen.
1. Klik op **Opslaan** in het detailvenster Vaardigheid.
1. Klik op **Uitvoeren** in het venstermenu van de sessie. Hiermee wordt een andere uitvoering van de vaardighedenset met behulp van het document gestart.

   :::image type="content" source="media/cognitive-search-debug/skill-setting-context-correction-missing-value-locations-organizations.png" alt-text="Correctie van context in vaardigheidsinstelling":::

Alle fouten zijn opgelost.

## <a name="commit-changes-to-the-skillset"></a>Wijzigingen doorvoeren in de vaardighedenset

Toen de foutopsporingssessie werd gestart, heeft de zoekservice een kopie van de vaardighedenset gemaakt. Dit is gedaan om de oorspronkelijke vaardig heden voor uw zoek service te beveiligen. Nu u de fout opsporing van uw vaardig heden hebt voltooid, kunnen de oplossingen worden vastgelegd (Overschrijf de oorspronkelijke vaardig heden). 

Als u geen wijzigingen wilt door voeren, kunt u ook de foutopsporingssessie opslaan en later opnieuw openen.

1. Klik op **Wijzigingen doorvoeren** in het menu Foutopsporingssessies.
1. Klik op **OK** om te bevestigen dat u uw vaardighedenset wilt bijwerken.
1. Sluit de foutopsporingssessie en selecteer het tabblad **Indexeerfuncties**.
1. Open uw clinical-trials-idxr.
1. Klik op **Opnieuw instellen**.
1. Klik op **Run**. Klik op **OK** om te bevestigen.

Als de Indexeer functie is voltooid, moet er een groen vinkje staan en is het woord voltooid naast het tijds tempel voor de meest recente uitvoering op het tabblad **uitvoerings geschiedenis** . Om ervoor te zorgen dat de wijzigingen zijn toegepast:

1. Selecteer op de pagina overzicht van zoek opdrachten het tabblad **index** .
1. Open de index ' klinisch-proef versie ' en voer deze query reeks in op het tabblad Zoeken in Verkenner: `$select=metadata_storage_path, organizations, locations&$count=true` om velden te retour neren voor specifieke documenten (aangeduid met het unieke `metadata_storage_path` veld).
1. Klik op **Zoeken**.

De resultaten moeten aangeven dat organisaties en locaties nu zijn gevuld met de verwachte waarden.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Deze zelf studie is gerakend op verschillende aspecten van de definitie en verwerking van vaardig heden. Raadpleeg de volgende artikelen voor meer informatie over concepten en werk stromen:

+ [Informatie over het toewijzen van vaardig heden-uitvoer velden aan velden in een zoek index](cognitive-search-output-field-mapping.md)

+ [Vaardig heden in azure Cognitive Search](cognitive-search-working-with-skillsets.md)

+ [Caching configureren voor incrementele verrijking](cognitive-search-incremental-indexing-conceptual.md)