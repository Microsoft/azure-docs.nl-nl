---
title: Wat is er nieuw in Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Meer informatie over de meest recente wijzigingen in de API voor formulier herkenning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: a8eecabe71cb439949f7e0638c7339eb31b08500
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581258"
---
# <a name="whats-new-in-form-recognizer"></a>Wat is er nieuw in Form Recognizer?

De Form Recognizer-service wordt doorlopend bijgewerkt. Gebruik dit artikel om op de hoogte te blijven van de functie verbeteringen, oplossingen en documentatie-updates.

## <a name="november-2020"></a>November 2020

### <a name="new-features"></a>Nieuwe functies

**Formulier Recognizer v 2.1 Public Preview 2 is nu beschikbaar.** V 2.1: preview. 2 is uitgebracht, met inbegrip van de volgende functies: 

- **Nieuw vooraf gebouwd factuur model** : met het nieuwe vooraf gemaakte factuur model kunnen klanten facturen in verschillende indelingen nemen en gestructureerde gegevens retour neren om de factuur verwerking te automatiseren. Het combineert onze krachtige functies voor optische teken herkenning (OCR) met factuur uitgebreide leer modellen voor het extra heren van belang rijke informatie uit facturen in het Engels. Hiermee worden de tekst, tabellen en gegevens, zoals klant, leverancier, factuur-ID, verval datum van factuur, totaal, verschuldigd bedrag, belasting bedrag, verzen ding, factuur en meer geëxtraheerd.

  > [Meer informatie over het vooraf gemaakte factuur model](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="factuur voorbeeld" lightbox="./media/invoice-example.jpg":::

- **Uitgebreide tabel extractie** : formulier Recognizer biedt nu uitgebreide tabel extractie, waarin onze krachtige OCR-mogelijkheden (optische teken herkenning) worden gecombineerd met een analyse model met uitgebreide informatie. Met de formulier herkenning kunt u gegevens ophalen uit tabellen, inclusief complexe tabellen met samengevoegde kolommen, rijen, zonder randen en meer. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="voorbeeld van tabellen" lightbox="./media/tables-example.jpg":::

 
  > [Meer informatie over indelings extractie](concept-layout.md)

- **Update van client bibliotheek** : de nieuwste versies van de [client bibliotheken](quickstarts/client-library.md) voor .net, Python, Java en Java script bieden ondersteuning voor de API voor formulier herkenning 2,1.
- **Nieuwe ondersteunde taal: Japans** -de volgende nieuwe talen worden nu ondersteund: voor `AnalyzeLayout` en `AnalyzeCustomForm` : Japans ( `ja` ). [Taalondersteuning](language-support.md)
- **Tekst lijn stijl vermelding (handgeschreven/Overig) (alleen Latijnse talen)** : met formulier herkenning wordt nu een `appearance` object geclassificeerd waarin wordt aangegeven of elke tekst regel handgeschreven stijl is of niet, samen met een betrouwbaarheids Score. Deze functie wordt alleen ondersteund voor Latijnse talen.
- **Kwaliteits verbeteringen** : extractie verbeteringen, waaronder de extractie van één cijfer.
- **Nieuwe try-it-out-functie in de vorm van voor beeld van herkenning en labelen hulp programma** : de mogelijkheid om vooraf gemaakte factuur-, ontvangst-en visite kaart modellen en de lay-out van de indelings-API te gebruiken met behulp van het voor beeld labelen Bekijk hoe uw gegevens worden geëxtraheerd zonder dat u code hoeft te schrijven.

  > [Het voorbeeld programma voor formulier herkenning uitproberen](https://fott-preview.azurewebsites.net/)

  ![FOTT-voor beeld](./media/ui-preview.jpg)
  
- **Feedback-lus** : bij het analyseren van bestanden via het hulp programma voor het labelen van het voor beeld kunt u dit nu ook toevoegen aan de Trainingsset en zo nodig de labels aanpassen, zodat het model kan worden verbeterd.
- **Documenten automatisch labelen** : automatisch extra documenten labelen op basis van eerder gelabelde documenten in het project.

## <a name="august-2020"></a>Augustus 2020

### <a name="new-features"></a>Nieuwe functies

**De open bare preview van de formulier Recognizer v 2.1 is nu beschikbaar.** V 2.1-Preview. 1 is uitgebracht, met inbegrip van de volgende functies: 


- **Rest API referentie is beschikbaar** : Bekijk de [referentie v 2.1-Preview. 1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **Nieuwe talen die naast Engels worden ondersteund**, worden de volgende [talen](language-support.md) nu ondersteund: voor `Layout` en `Train Custom Model` : Engels ( `en` ), Chinees (vereenvoudigd) ( `zh-Hans` ), Nederlands ( `nl` ), Frans (), `fr` Duits ( `de` ), Italiaans ( `it` ), Portugees () `pt` en Spaans ( `es` ).
- **Detectie van selectie vakjes/selectie markering** : formulier Recognizer ondersteunt detectie en extractie van selectie markeringen zoals selectie vakjes en keuze rondjes. De selectie markeringen worden geëxtraheerd in `Layout` en u kunt nu ook labels maken en trainen in `Train Custom Model`  -  _trein voorzien_ om paren van sleutel waarden te extra heren voor selectie markeringen. 
- **Model opstellen** : Hiermee kunnen meerdere modellen worden samengesteld en met één model-id worden aangeroepen. Wanneer een document wordt ingediend om te worden geanalyseerd met een bestaande model-ID, wordt eerst een classificatie stap uitgevoerd om deze naar het juiste aangepaste model te routeren. Model opstellen is beschikbaar voor `Train Custom Model`  -  _trein met labels_.
- **Model naam** : Voeg een beschrijvende naam toe aan uw aangepaste modellen voor eenvoudiger beheer en het bijhouden van wijzigingen.
- **[Nieuw vooraf ontworpen model voor visite kaartjes](concept-business-cards.md)** voor het extra heren van algemene velden in het Engels, visite kaartjes voor talen.
- **[Nieuwe land instellingen voor vooraf gemaakte ontvangst bevestigingen](concept-receipts.md)** naast en-us, ondersteuning is nu beschikbaar voor en-au, en-ca, en-GB, en-in
- **Kwaliteits verbeteringen** voor `Layout` , `Train Custom Model`  -  _Train zonder labels_ en _trein met labels_.


**v 2.0** bevat de volgende update:

- De [client bibliotheken](quickstarts/client-library.md) voor net, Python, Java en Java script hebben algemene Beschik baarheid. 


**Nieuwe voor beelden** zijn beschikbaar op github. 
- De [recepten voor kennis extractie: formulieren Playbook](https://github.com/microsoft/knowledge-extraction-recipes-forms) verzamelen aanbevolen procedures van de klant afspraken van Real Form Recognizer en biedt bruikbare code voorbeelden, controle lijsten en voorbeeld pijplijnen die worden gebruikt voor het ontwikkelen van deze projecten. 
- Het [hulp programma voor het labelen](https://github.com/microsoft/OCR-Form-Tools) van het voor beeld is bijgewerkt ter ondersteuning van de nieuwe v 2.1-functionaliteit. Bekijk deze [Snelstartgids](quickstarts/label-tool.md) om aan de slag te gaan met het hulp programma. 
- Het voor beeld van een [intelligent kiosk](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) formulier Recognizer laat zien hoe u `Analyze Receipt` zonder labels integreert en `Train Custom Model`  -  _traint_.



## <a name="july-2020"></a>Juli 2020

### <a name="new-features"></a>Nieuwe functies

* **v 2.0-verwijzing beschikbaar** : Bekijk [de API-referentie voor v 2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) en de bijgewerkte sdk's voor [.net](/dotnet/api/overview/azure/ai.formrecognizer-readme), [python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme)en [Java script](/javascript/api/overview/azure/).
* Verbeteringen in **tabel verbeteringen en** uitbrei dingen voor extractie: bevat nauw keurige verbeteringen en verbeteringen in tabel extractie, met name de mogelijkheid om tabellen en structuren in een _aangepaste trein zonder labels_ te leren. 

* **Valuta-ondersteuning** -detectie en extractie van globale valuta symbolen.
* **Azure gov** -formulier herkenning is nu ook beschikbaar in azure gov.
* **Verbeterde beveiligings functies**: 
   * Als u **uw eigen sleutel** formulier Recognizer zet, worden uw gegevens automatisch versleuteld wanneer deze worden opgeslagen in de cloud om deze te beschermen en om u te helpen te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Uw abonnement maakt standaard gebruik van door Microsoft beheerde versleutelingssleutels. U kunt uw abonnement nu ook beheren met uw eigen coderings sleutels. [Door de klant beheerde sleutels, ook wel bekend als uw eigen sleutel (BYOK)](./form-recognizer-encryption-of-data-at-rest.md), bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelingssleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.  
   * **Persoonlijke eind punten** – Hiermee kunt u een virtueel netwerk (VNet) [gebruiken om veilig toegang te krijgen tot gegevens via een privé-koppeling.](../../private-link/private-link-overview.md)


## <a name="june-2020"></a>Juni 2020

### <a name="new-features"></a>Nieuwe functies
* **CopyModel-API toegevoegd aan client-sdk's** : u kunt nu de client-sdk's gebruiken om modellen van het ene naar het andere abonnement te kopiëren. Zie [back-ups maken en modellen herstellen](./disaster-recovery.md) voor algemene informatie over deze functie.
* **Azure Active Directory-integratie** : u kunt nu uw Azure AD-referenties gebruiken om uw formulieren Recognizer-client objecten in de sdk's te verifiëren.
* **Specifieke SDK-wijzigingen** : Dit geldt zowel voor het toevoegen van kleine functies als voor het verbreken van wijzigingen. Raadpleeg de SDK-changelogs voor meer informatie.
  * [C# SDK preview 3 wijzigingen logboek](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK preview 3 wijzigingen logboek](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK preview 3 wijzigingen logboek](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java script SDK preview 3 wijzigingen logboek](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>April 2020

### <a name="new-features"></a>Nieuwe functies
* **SDK-ondersteuning voor de open bare preview van Form Recognizer API v 2.0** -deze maand hebben we onze service ondersteuning uitgebreid tot een preview-SDK voor de versie van de formulier Recognizer v 2.0 (preview). Gebruik de onderstaande koppelingen om aan de slag te gaan met de taal van uw keuze: 
   * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
   * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
   * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
   * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  De nieuwe SDK ondersteunt alle functies van de v 2.0 REST API voor de formulier herkenner. U kunt bijvoorbeeld een model trainen met of zonder labels en extra tekst, sleutel waardeparen en tabellen uit uw formulieren extra heren, gegevens uit de bevestigingen ophalen met de vooraf gemaakte ontvangst bevestigingen en tekst en tabellen met de lay-outservice uit uw documenten ophalen. U kunt uw feedback op de Sdk's delen via het [SDK-feedback formulier](https://aka.ms/FR_SDK_v1_feedback).
 
* **Aangepast model kopiëren** U kunt nu modellen kopiëren tussen regio's en abonnementen met behulp van de nieuwe functie voor het kopiëren van aangepaste modellen. Voordat u de API voor het kopiëren van aangepaste modellen aanroept, moet u eerst autorisatie aanvragen voor het kopiëren naar de doel bron door de bewerking Copy Authorization aan te roepen voor het doel bron eindpunt.
   * [Een Kopieer autorisatie genereren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
   * [Een aangepast model kopiëren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>Verbeterde beveiliging

* Customer-Managed sleutels zijn nu beschikbaar voor FormRecognizer. Zie voor meer informatie [gegevens versleuteling in rust voor de formulier herkenner](./form-recognizer-encryption-of-data-at-rest.md).
* Gebruik beheerde identiteiten voor toegang tot Azure-resources met Azure Active Directory. Zie [toegang verlenen voor beheerde identiteiten](../authentication.md#authorize-access-to-managed-identities)voor meer informatie.

## <a name="march-2020"></a>Maart 2020 

### <a name="new-features"></a>Nieuwe functies

* **Waardetypen voor labelen** U kunt nu de typen waarden opgeven die u wilt labelen met het hulp programma voor het labelen van het voor beeld van de formulier herkenning. De volgende waardetypen en variaties worden momenteel ondersteund:
  * `string`
    * standaard, `no-whitespaces`, `alphanumeric`
  * `number`
    * standaard, `currency`
  * `date` 
    * standaard, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Raadpleeg de hand leiding voor het [labelen van labels](./quickstarts/label-tool.md#specify-tag-value-types) voor meer informatie over het gebruik van deze functie.


* **Tabel visualisatie** Het hulp programma labelen wordt nu weer gegeven met tabellen die in het document zijn herkend. Met deze functie kunt u de tabellen weer geven die zijn herkend en geëxtraheerd uit het document, voorafgaand aan het labelen en analyseren. U kunt deze functie in-of uitschakelen met de optie lagen.

  De volgende afbeelding is een voor beeld van hoe tabellen worden herkend en geëxtraheerd:

  > [!div class="mx-imgBorder"]
  > ![Tabel visualisatie met behulp van het voor beeld-programma labelen](./media/whats-new/formre-table-viz.png)

    De uitgepakte tabellen zijn beschikbaar in de JSON-uitvoer onder `"pageResults"` .

  > [!IMPORTANT]
  > Labels van tabellen worden niet ondersteund. Als tabellen niet automatisch worden herkend en extrated, kunt u ze alleen labelen als sleutel/waarde-paren. Bij het labelen van tabellen als sleutel/waarde-paren, labelt u elke cel als een unieke waarde.

### <a name="extraction-enhancements"></a>Uitbrei dingen voor extractie

Deze release bevat verbeteringen voor extractie en nauw keurigheid, met name de mogelijkheid om meerdere sleutel-waardeparen in dezelfde tekst regel te labelen en uit te pakken. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Voor beeld van labelen hulp programma is nu open-source

Het hulp programma voor het labelen van het voorbeeld formulier Recognizer is nu beschikbaar als een open-source project. U kunt dit integreren in uw oplossingen en klantspecifieke wijzigingen aanbrengen om aan uw behoeften te voldoen.

Raadpleeg de documentatie die beschikbaar is op [github](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)voor meer informatie over het hulp programma voor het labelen van het voor beeld van een formulier herkenning.

### <a name="tls-12-enforcement"></a>TLS 1.2 afdwingen

TLS 1.2 wordt nu afgedwongen voor alle HTTP-aanvragen bij deze service. Zie [Beveiliging van Azure Cognitive Services](../cognitive-services-security.md) voor meer informatie.

## <a name="january-2020"></a>Januari 2020

Deze release introduceert de formulier Recognizer 2,0 (preview). In de volgende secties vindt u meer informatie over nieuwe functies, verbeteringen en wijzigingen. 

### <a name="new-features"></a>Nieuwe functies

* **Aangepast model**
  * **Trainen met labels** U kunt nu een aangepast model trainen met hand matig gelabelde gegevens. Deze methode resulteert in betere uitvoering van modellen en kan modellen produceren die werken met complexe formulieren of formulieren met waarden zonder sleutels.
  * **ASYNCHRONE API** U kunt asynchrone API-aanroepen gebruiken om met grote gegevens sets en bestanden te trainen en te analyseren.
  * **Ondersteuning voor TIFF-bestanden** U kunt nu gegevens uit TIFF-documenten trainen en ophalen.
  * **Verbeteringen in de extractie nauwkeurigheid**

* **Gebaseerd ontvangst model**
  * **Fooien** U kunt nu fooie bedragen en andere handgeschreven waarden extra heren.
  * **Extractie van regel items** U kunt waarden van het regel item extra heren uit de bevestigingen.
  * **Betrouwbaarheids waarden** U kunt het vertrouwen van het model voor elke geëxtraheerde waarde weer geven.
  * **Verbeteringen in de extractie nauwkeurigheid**

* **Indelings extractie** U kunt nu de indelings-API gebruiken om tekst gegevens en tabel gegevens op te halen uit uw formulieren.

### <a name="custom-model-api-changes"></a>Wijzigingen in het aangepaste model-API

Alle Api's voor training en het gebruik van aangepaste modellen zijn hernoemd en sommige synchrone methoden zijn nu asynchroon. De volgende belang rijke wijzigingen zijn:

* Het proces voor het trainen van een model is nu asynchroon. U initieert training via de API-aanroep van **/Custom/models** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **aangepaste/modellen/{modelID}** om de resultaten van de training te retour neren.
* De extractie van sleutel/waarde wordt nu geïnitieerd door de API-aanroep van **/Custom/models/{modelID}/analyze** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **aangepaste/modellen/{modelID}/analyzeResults/{resultID}** om de resultaten van de extractie te retour neren.
* Bewerkings-Id's voor de trein bewerking zijn nu gevonden in de **locatie** header van http-antwoorden, niet op de locatie van de **bewerking** .

### <a name="receipt-api-changes"></a>Wijzigingen in de ontvangst-API

De naam van de Api's voor het lezen van de verkoop ontvangst is gewijzigd.

* Het uitpakken van ontvangst gegevens wordt nu geïnitieerd door de API-aanroep van **/prebuilt/Receipt/analyze** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **/prebuilt/Receipt/analyzeResults/{resultID}** om de resultaten van de extractie te retour neren.

### <a name="output-format-changes"></a>Wijzigingen in de uitvoer indeling

De JSON-antwoorden voor alle API-aanroepen hebben nieuwe notaties. Sommige sleutels en waarden zijn toegevoegd, verwijderd of de naam ervan is gewijzigd. Bekijk de Quick starts voor voor beelden van de huidige JSON-indelingen.

## <a name="next-steps"></a>Volgende stappen

Maak een [Snelstartgids](quickstarts/client-library.md) om aan de slag te gaan met het schrijven van een app voor het verwerken van formulieren met een formulier herkenner in de ontwikkelings taal van uw keuze.

## <a name="see-also"></a>Zie ook

* [Wat is Form Recognizer?](./overview.md)
