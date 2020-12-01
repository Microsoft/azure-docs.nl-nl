---
title: Uw Bing Aangepaste zoekopdrachten-ervaring configureren | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Met de portal kunt u een zoek exemplaar maken dat de segmenten van het web opgeeft. domeinen, subpagina's en webpagina's.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: b2d4eb75002474bbec57753bbf0255cc4c2b4a16
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338636"
---
# <a name="configure-your-bing-custom-search-experience"></a>Uw Bing Aangepaste zoekopdrachten-ervaring configureren

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services overgezet. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met een aangepaste zoek opdracht kunt u de zoek ervaring zodanig aanpassen dat alleen inhoud wordt Inge sloten van websites waar uw gebruikers zich bevinden. In plaats van een zoek opdracht in het hele web uit te voeren, zoekt Bing alleen de segmenten van het web die u interesseren. Als u een aangepaste weergave van het web wilt maken, gebruikt u de [portal](https://www.customsearch.ai) van Bing Aangepaste zoekopdrachten.

Met de portal kunt u een zoek exemplaar maken dat de segmenten van het web bevat: domeinen, subpagina's en webpagina's, waarin u wilt zoeken naar een zoek opdracht en de pagina's die u niet wilt doorzoeken. De portal kan ook inhoud suggereren die u mogelijk wilt toevoegen.

Gebruik het volgende bij het definiëren van uw segmenten van het web:

| Segment naam | Beschrijving                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Domain     | Een domein segment bevat alle inhoud die binnen een Internet domein is gevonden. Bijvoorbeeld `www.microsoft.com`. Als u dit weglaat, `www.` zoekt Bing ook de subdomeinen van het domein. Als u bijvoorbeeld opgeeft `microsoft.com` , retourneert Bing ook resultaten van `support.microsoft.com` of `technet.microsoft.com` . |
| Subpagina    | Een segment van een subpagina bevat alle inhoud die is gevonden op de subpagina en de onderliggende paden. U kunt Maxi maal twee subpagina's in het pad opgeven. bijvoorbeeld `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Webpagina    | Een segment van een webpagina kan alleen die webpagina in een aangepaste zoek opdracht bevatten. U kunt eventueel ook opgeven of u subpagina's wilt toevoegen.                                                                                                                                                                                  |

> [!IMPORTANT]
> Alle domeinen, subpagina's en webpagina's die u opgeeft, moeten openbaar zijn en door Bing worden geïndexeerd. Als u eigenaar bent van een open bare site die u wilt gebruiken in de zoek opdracht, en Bing deze niet heeft geïndexeerd, raadpleegt u de Bing [webmaster-documentatie](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) voor meer informatie over het zoeken naar Bing. Raadpleeg ook de documentatie van de webmaster voor meer informatie over het verkrijgen van Bing om uw verkende site bij te werken als de index verouderd is.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>Segmenten van het web toevoegen aan uw aangepaste zoek instantie

Wanneer u uw aangepaste zoek exemplaar maakt, kunt u de segmenten van het web opgeven: domeinen, subpagina's en webpagina's die u wilt opnemen of die u wilt blok keren uit uw zoek resultaten. 

Als u weet welke segmenten u wilt opnemen in uw aangepaste zoek exemplaar, voegt u deze toe aan de **actieve** lijst van uw exemplaar. 

Als u niet zeker weet welke segmenten u wilt toevoegen, kunt u zoek query's naar Bing verzenden in het **voorbeeld** venster en de gewenste segmenten selecteren. Om dit te doen: 

1. Selecteer Bing in de vervolg keuzelijst in het deel venster voor beeld en voer een zoek opdracht in

2. Klik op **site toevoegen** naast het resultaat dat u wilt toevoegen. Klik vervolgens op OK.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>Uw zoek ervaring met actieve en geblokkeerde lijsten aanpassen 

U kunt de lijst met actieve en geblokkeerde segmenten openen door te klikken op de tabbladen **actief** en **geblokkeerd** in het aangepaste zoek exemplaar. Segmenten die zijn toegevoegd aan de actieve lijst worden opgenomen in uw aangepaste zoek opdracht. Geblokkeerde segmenten worden niet doorzocht en worden niet weer gegeven in de zoek resultaten.

Klik op het tabblad **actief** en voeg een of meer url's toe om de segmenten op te geven van het web waarnaar u wilt zoeken in Bing. Als u Url's wilt bewerken of verwijderen, gebruikt u de opties onder de kolom **besturings elementen** . 

Bij het toevoegen van Url's aan de **actieve** lijst kunt u enkele url's of meerdere url's tegelijkertijd toevoegen door een tekst bestand te uploaden met behulp van het pictogram uploaden.

![Het Bing Aangepaste zoekopdrachten actieve tabblad](media/file-upload-icon.png)

Als u een bestand wilt uploaden, maakt u een tekst bestand en geeft u één domein, subpagina of webpagina per regel op. Het bestand wordt geweigerd als het niet juist is ingedeeld.

> [!NOTE]
> * U kunt alleen een bestand uploaden naar de **actieve** lijst. U kunt deze niet gebruiken om segmenten toe te voegen aan de lijst met **geblokkeerde** toepassingen.  
> * Als de **geblokkeerde** lijst een domein, subpagina of webpagina bevat die u hebt opgegeven in het upload bestand, wordt deze verwijderd uit de lijst met **geblokkeerde** en aan de **actieve** lijst toegevoegd.
> * Dubbele vermeldingen in uw Upload bestand worden genegeerd door Bing Aangepaste zoekopdrachten. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Website suggesties voor uw zoek ervaring ophalen

Nadat u Web Slices aan de **actieve** lijst hebt toegevoegd, worden door de Bing aangepaste zoekopdrachten portal onder aan het tabblad suggesties voor websites en subpagina's gegenereerd. Dit zijn segmenten die Bing Aangepaste zoekopdrachten denkt dat u mogelijk wilt invoegen. Klik op **vernieuwen** om bijgewerkte suggesties te krijgen na het bijwerken van de instellingen van uw aangepaste zoek exemplaar. Deze sectie is alleen zichtbaar als er suggesties beschikbaar zijn.

## <a name="search-for-images-and-videos"></a>Zoeken naar afbeeldingen en Video's

U kunt zoeken naar afbeeldingen en Video's, op dezelfde manier als webinhoud met behulp van de [Bing custom afbeeldingen zoeken-API](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) of de [Bing Custom Video's zoeken-API](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference). U kunt deze resultaten weer geven met de [gehoste gebruikers interface](hosted-ui.md)of de api's. 

Deze Api's zijn vergelijkbaar met de niet-aangepaste [Bing Image Search](../Bing-Image-Search/overview.md) en [Bing Video Search](../bing-video-search/overview.md) api's, maar doorzoeken het hele web en vereisen geen `customConfig` query parameter. Raadpleeg deze documentatie sets voor meer informatie over het werken met afbeeldingen en Video's. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>Uw zoek exemplaar testen met het voorbeeld venster

U kunt uw zoek opdracht testen met behulp van het voorbeeld venster op de rechter kant van de portal om Zoek query's te verzenden en de resultaten weer te geven. 

1. Selecteer onder het zoekvak **mijn exemplaar**. U kunt de resultaten van uw zoek ervaring vergelijken met Bing door **Bing** te selecteren. 
2. Selecteer een veilig zoek filter en welke markt u wilt zoeken (Zie [query parameters](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Voer een query in en druk op ENTER of klik op het zoek pictogram om de resultaten van de huidige configuratie weer te geven. U kunt het Zoek type dat u hebt uitgevoerd, wijzigen door op **Web**, **afbeelding** of **video** te klikken om de bijbehorende resultaten te verkrijgen. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>De rang schikking van specifieke Zoek resultaten aanpassen

Met de portal kunt u de zoek volgorde van inhoud aanpassen van specifieke domeinen, subpagina's en webpagina's. Na het verzenden van een zoek opdracht in het voorbeeld venster bevat elk Zoek resultaat een lijst met aanpassingen die u kunt maken:  

| Wijzigingen | Beschrijving |
|------------|-------------|
| Blokkeren      | Hiermee verplaatst u het domein, de subpagina of de webpagina naar de lijst met geblokkeerde blokken. De inhoud van de geselecteerde site wordt uitgesloten van de zoek resultaten.                    |
| Promoties      | Verhoogt de inhoud van het domein of de subpagina naar een hoger niveau in de zoek resultaten.                                                                                        |
| Degraderen     | Hiermee wordt de inhoud van het domein of de subpagina lager in de zoek resultaten verlaagd. U selecteert of u de inhoud van het domein of de subpagina waartoe de webpagina behoort, wilt degraderen. |
| Vastmaken aan de bovenkant | Hiermee verplaatst u het domein, de subpagina of de webpagina naar de lijst met **vastgemaakte** lijsten. Hiermee wordt de webpagina geforceerd weer gegeven als het hoogste Zoek resultaat voor een bepaalde Zoek query.                   |

Het aanpassen van de positie is niet beschikbaar voor afbeeldingen of video-Zoek opdrachten.

### <a name="boosting-and-demoting-search-results"></a>Zoek resultaten verhogen en degraderen

U kunt een wille keurig domein of subpagina in de **actieve** lijst verhogen, verhogen of verlagen. Standaard worden alle segmenten toegevoegd zonder classificatie aanpassingen. De segmenten van het web die sterk worden verg root of versterkt, zijn hoger in de zoek resultaten (met een hogere classificatie dan Boost). Items die worden gedegradeerd, worden lager in de zoek resultaten gerangschikt.

U kunt items in de **actieve** lijst verhogen, verhogen of verlagen met behulp van de besturings elementen voor de **rang schikking aanpassen** of door de besturings elementen verhogen en degraderen in het voorbeeld venster te gebruiken. De service voegt het segment aan uw actieve lijst toe en past de rang schikking dienovereenkomstig aan.

> [!NOTE] 
> Het verhogen en degraderen van domeinen en subpagina's is een van de vele methoden die Bing Aangepaste zoekopdrachten gebruikt om de volg orde van de zoek resultaten te bepalen. Vanwege andere factoren die van invloed zijn op de rang schikking van verschillende webinhoud, kunnen de gevolgen van het aanpassen van de rang verschillen. Gebruik het voorbeeld venster om de gevolgen van het aanpassen van de positie van de zoek resultaten te testen. 

De Super Boost, het boosten en het degraderen zijn niet beschikbaar voor de afbeeldingen en video-Zoek opdrachten.

## <a name="pin-slices-to-the-top-of-search-results"></a>Segmenten vastmaken aan de bovenkant van de zoek resultaten

Met de portal kunt u ook Url's aan de bovenkant van de zoek resultaten vastmaken voor specifieke zoek termen, met behulp van het tabblad **vastgemaakt** . Voer een URL en een query in om de webpagina op te geven die als het beste resultaat wordt weer gegeven. Houd er rekening mee dat u Maxi maal één webpagina per zoek opdracht kunt vastmaken en dat alleen geïndexeerde webpagina's worden weer gegeven in Zoek opdrachten. Het vastmaken van resultaten is niet beschikbaar voor afbeeldingen of video-Zoek opdrachten.

U kunt op twee manieren een webpagina aan de bovenkant vastmaken:

* Voer op het tabblad **vastgemaakt** de URL in van de webpagina die u aan de bovenkant wilt vastmaken en de bijbehorende query.

* Voer in het deel venster **voor beeld** een zoek opdracht in en klik op zoeken. Zoek de webpagina die u wilt vastmaken voor uw query en klik op **vastmaken aan de bovenkant**. de webpagina en de query worden toegevoegd aan de lijst met **vastgemaakte** lijsten.

### <a name="specify-the-pins-match-condition"></a>De match-voor waarde voor de pincode opgeven

Standaard worden webpagina's alleen aan de bovenkant van de zoek resultaten vastgemaakt wanneer de query teken reeks van een gebruiker exact overeenkomt **met de pagina die in de lijst** is opgenomen. U kunt dit gedrag wijzigen door een van de volgende match-voor waarden op te geven:

> [!NOTE]
> Alle vergelijkingen tussen de zoek query van de gebruiker en de zoek query van de pincode zijn niet hoofdletter gevoelig.

| Waarde | Beschrijving                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Begint met | De pincode is een overeenkomst als de query reeks van de gebruiker begint met de query reeks van de pincode |
| Eindigt op   | De pincode is een overeenkomst als de query reeks van de gebruiker eindigt met de query teken reeks van de pincode.  |
| Contains    | De pincode is een overeenkomst als de query reeks van de pincode de query reeks bevat.   |


Als u de match-voor waarde van de pincode wilt wijzigen, klikt u op het bewerkings pictogram van de pincode. Klik in de kolom **voor waarde voor query overeenkomst** op de vervolg keuzelijst en selecteer de nieuwe voor waarde die u wilt gebruiken. Klik vervolgens op het pictogram opslaan om de wijziging op te slaan.

### <a name="change-the-order-of-your-pinned-sites"></a>De volg orde van uw vastgemaakte sites wijzigen

Als u de volg orde van uw pincodes wilt wijzigen, kunt u ze slepen en neerzetten of het bijbehorende Volg nummer bewerken door te klikken op het pictogram bewerken in de kolom **besturings elementen** van de lijst met **vastgemaakte** lijsten.

Als meerdere pincodes voldoen aan een match-voor waarde, gebruikt Bing Aangepaste zoekopdrachten het hoogste niveau in de lijst.

## <a name="view-statistics"></a>Statistieken weer geven

Als u zich op het juiste niveau hebt geabonneerd op een aangepaste zoek opdracht (Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), wordt het tabblad **Statistieken** toegevoegd aan uw productie-exemplaren. Het tabblad Statistieken bevat details over de manier waarop uw aangepaste zoek eindpunten worden gebruikt, inclusief oproep volume, populaire query's, geografische distributie, respons codes en veilige Zoek opdrachten. U kunt details filteren met behulp van de beschik bare besturings elementen.

## <a name="usage-guidelines"></a>Gebruiks richtlijnen

- Voor elk exemplaar van de aangepaste zoek opdracht is het maximum aantal rangorde aanpassingen dat u kunt aanbrengen in **actieve** en **geblokkeerde** segmenten beperkt tot 400.
- Het toevoegen van een segment aan de actieve of geblokkeerde tabbladen telt als een aanpassing van de rang schikking.
- Aantal verhogen en degraderen als twee aanpassingen van de classificatie.
- Voor elk exemplaar van de aangepaste zoek opdracht is het maximum aantal pincodes dat u kunt maken beperkt tot 200.

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste zoekopdrachten aanroepen](./search-your-custom-view.md)
- [Gehoste UI-ervaring configureren](./hosted-ui.md)
- [Decoratiemarkeringen gebruiken om tekst te markeren](../bing-web-search/hit-highlighting.md)
- [Bladeren door webpagina's](../bing-web-search/paging-search-results.md)