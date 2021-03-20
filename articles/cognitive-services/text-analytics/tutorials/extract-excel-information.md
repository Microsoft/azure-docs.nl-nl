---
title: Informatie extraheren in Excel met behulp van Text Analytics en Power Automate
titleSuffix: Azure Cognitive Services
description: Meer informatie over het extra heren van Excel-tekst zonder code te hoeven schrijven met behulp van Text Analytics en energie automatisering.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359898"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Informatie extraheren in Excel met behulp van Text Analytics en Power Automate 

In deze zelf studie maakt u een stroom voor automatische stroom om tekst in een Excel-spread sheet te extra heren zonder dat u code hoeft te schrijven. 

Deze stroom maakt een werk blad van problemen die zijn gerapporteerd over een Apartment-complex en classificeert deze in twee categorieën: sanitair en andere. Ook worden de namen en telefoon nummers opgehaald van de tenants die deze hebben verzonden. Ten slotte voegt de stroom deze gegevens toe aan het Excel-werk blad. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Energie automatisering gebruiken om een stroom te maken
> * Excel-gegevens uploaden uit OneDrive voor bedrijven
> * Tekst uit Excel extra heren en verzenden naar de Text Analytics-API 
> * Gebruik de informatie van de API om een Excel-werk blad bij te werken.

## <a name="prerequisites"></a>Vereisten

- Een Microsoft Azure-account. [Een gratis account maken](https://azure.microsoft.com/free/cognitive-services/) en [aanmelden](https://portal.azure.com/).
- Een Text Analytics resource. Als u er geen hebt, kunt u [er een maken in de Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) en de gratis laag gebruiken om deze zelf studie te volt ooien.
- De [sleutel en het eind punt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) die voor u zijn gegenereerd tijdens het aanmelden.
- Een werk blad met Tenant problemen. Voor beelden van gegevens vindt u op GitHub
- Microsoft 365, met OneDrive voor bedrijven.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Het Excel-bestand toevoegen aan OneDrive voor bedrijven

Down load het voorbeeld Excel-bestand van [github](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Dit bestand moet worden opgeslagen in uw OneDrive voor bedrijven-account.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Voor beelden van het Excel-bestand.":::

De problemen worden gerapporteerd in onbewerkte tekst. We gebruiken de benoemde entiteit herkenning van de Text Analytics-API om de naam en het telefoon nummer van de persoon op te halen. Vervolgens zoekt de stroom naar het woord ' sanitair ' in de beschrijving om de problemen te categoriseren. 

## <a name="create-a-new-power-automate-workflow"></a>Een nieuwe werk stroom voor het automatiseren van een stroom maken

Ga naar de [Power-site voor automatisch](https://preview.flow.microsoft.com/)aanmelden en meld u aan. Klik vervolgens op **maken** en **Geplande stroom**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Het scherm voor het maken van de stroom.":::


Initialiseer uw stroom op de pagina **een geplande stroom maken** met de volgende velden:

|Veld |Waarde  |
|---------|---------|
|**Stroomnaam**     | **Geplande beoordeling** of een andere naam.         |
|**Starten**     |  Voer de huidige datum en tijd in.       |
|**Herhalen elke**     | **1 uur**        |

## <a name="add-variables-to-the-flow"></a>Variabelen toevoegen aan de stroom

> [!NOTE]
> Als u een afbeelding van de voltooide stroom wilt zien, kunt u deze downloaden van [github](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Maak variabelen die de informatie vertegenwoordigen die wordt toegevoegd aan het Excel-bestand. Klik op **nieuwe stap** en zoek naar **initialisatie variabele**. Doe dit vier keer om vier variabelen te maken.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Variabelen initialiseren.":::

Voeg de volgende informatie toe aan de variabelen die u hebt gemaakt. Ze vertegenwoordigen de kolommen van het Excel-bestand. Als er variabelen zijn samengevouwen, kunt u erop klikken om deze uit te vouwen.

| Bewerking |Naam   | Type | Waarde |
|---------|---------|---|---|
| Variabele initialiseren | var_person | Tekenreeks | Person |
| Variabele 2 initialiseren | var_phone | Tekenreeks | Phone_Number |
| Variabele 3 initialiseren | var_plumbing | Tekenreeks | werkzaamheden |
| Variabele 4 initialiseren | var_other | Tekenreeks | other | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informatie in de stroom variabelen":::

## <a name="read-the-excel-file"></a>Het Excel-bestand lezen

Klik op **nieuwe stap** en typ **Excel** en selecteer vervolgens **lijst rijen weer geven in een tabel** in de lijst met acties.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Voeg Excel-rijen toe.":::

Voeg het Excel-bestand toe aan de stroom door de velden in deze actie in te vullen. Voor deze zelf studie moet het bestand zijn geüpload naar OneDrive voor bedrijven.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Excel-rijen vullen":::

Klik op **nieuwe stap** en voeg een **Toep assen op elke** actie toe.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Voeg een opdracht apply toe.":::

Klik op **een uitvoer van de vorige stap selecteren**. In het vak dynamische inhoud dat wordt weer gegeven, selecteert u **waarde**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Selecteer uitvoer uit het Excel-bestand.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Een aanvraag verzenden naar de Text Analytics-API

Als u dat nog niet hebt gedaan, moet u een [Text Analytics resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) maken in de Azure Portal.

### <a name="create-a-text-analytics-connection"></a>Een Text Analytics verbinding maken

Klik in de **Toep assen op elke** **actie toevoegen**. Ga naar de pagina voor de **sleutel en het eind punt** van de Text Analytics resource in de Azure Portal en haal de sleutel en het eind punt voor uw Text Analytics resource op.

Voer in uw stroom de volgende informatie in om een nieuwe Text Analytics verbinding te maken.

> [!NOTE]
> Als u al een Text Analytics verbinding hebt gemaakt en u de verbindings details wilt wijzigen, klikt u op het beletsel teken in de rechter bovenhoek en klikt u op **+ nieuwe verbinding toevoegen**.

| Veld           | Waarde                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Verbindingsnaam | Een naam voor de verbinding met uw Text Analytics bron. Bijvoorbeeld `TAforPowerAutomate`. |
| Accountsleutel     | De sleutel voor uw Text Analytics-resource.                                                                                   |
| Site-URL        | Het eind punt voor uw Text Analytics-resource.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom.":::

## <a name="extract-the-excel-content"></a>De Excel-inhoud extra heren 

Nadat de verbinding is gemaakt, zoekt u naar **Text Analytics** en selecteert u **entiteiten**. Hiermee wordt informatie opgehaald uit de kolom Beschrijving van het probleem.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Text Analytics entiteiten toevoegen.":::

Klik in het **tekst** veld en selecteer **Beschrijving** uit het venster met dynamische inhoud dat wordt weer gegeven. Voer `en` in voor de taal. (Klik op geavanceerde opties weer geven als u geen taal ziet)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Text Analytics-instellingen toevoegen.":::


## <a name="extract-the-person-name"></a>De persoons naam extra heren

Vervolgens wordt het entiteits type persoon in de Text Analytics-uitvoer weer te vinden. Klik in de **Toep assen op elke** actie op **een actie toevoegen** en maak een andere **Toep assen op elke** bewerking. Klik in het tekstvak en selecteer **entiteiten** in het venster dynamische inhoud dat wordt weer gegeven.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. twee":::

Klik binnen de zojuist gemaakte actie **Toep assen op elke 2** op **een actie toevoegen** en voeg een besturings element **voor waarde** toe.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 3D":::

Klik in het venster voor waarde op het eerste tekstvak. In het venster dynamische inhoud zoekt u naar het **type entiteiten** en selecteert u dit.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 3":::

Zorg ervoor dat het tweede vak is ingesteld op **is gelijk aan**. Selecteer vervolgens het derde vak en zoek `var_person` in het venster dynamische inhoud. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 5,0":::

In de voor waarde **Ja** , typt u in Excel en selecteert u vervolgens **een rij bijwerken**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 6,5":::

Voer de Excel-info in en werk de velden **sleutel kolom**, **sleutel waarde** en **persoons** naam bij. Hiermee wordt de naam die is gedetecteerd door de API toegevoegd aan het Excel-werk blad. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 7":::

## <a name="get-the-phone-number"></a>Het telefoon nummer ophalen

Minimaliseer de actie **Toep assen op elke 2** door op de naam te klikken. Voeg vervolgens een andere **toepassing toe op elke** actie, zoals voorheen. de naam wordt van **toepassing op elke 3**. Selecteer het tekstvak en voeg **entiteiten** als uitvoer voor deze actie toe. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. achtste":::

Voeg binnen **Toep assen op elke 3** een besturings element **voor waarde** toe. Deze krijgt de naam **voorwaarde 2**. Zoek in het eerste tekstvak het **type entiteiten** in het venster dynamische inhoud en voeg deze toe. Zorg ervoor dat het middelste vak is ingesteld op **is gelijk aan**. Voer vervolgens in het vak rechts de tekst in `var_phone` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 9,4":::

Voeg in de voor waarde **If ja** een actie voor het **bijwerken van een rij** toe. Voer vervolgens de bovenstaande gegevens in voor de kolom telefoon nummers van het Excel-werk blad. Hiermee wordt het telefoon nummer dat door de API is gedetecteerd, toegevoegd aan het Excel-werk blad. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 6":::


## <a name="get-the-plumbing-issues"></a>De sanitaire problemen ophalen

Minimaliseer **Toep assen op elke 3** door op de naam te klikken. Maak vervolgens een andere van **toepassing op elke** in de bovenliggende actie. Selecteer het tekstvak en voeg **entiteiten** als uitvoer voor deze actie toe vanuit het venster dynamische inhoud. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 9":::


Vervolgens wordt met de stroom gecontroleerd of de beschrijving van het probleem uit de Excel-tabelrij het woord ' sanitair ' bevat. Zo ja, dan wordt ' sanitaire ' toegevoegd in de kolom IssueType. Als dat niet het geval is, voert u ' Overig ' in.

In de actie **Toep assen op elke 4 voegt u** een besturings element **voor waarde** toe. Deze krijgt de naam **voor waarde 3**. In het eerste tekstvak zoekt en voegt u een **Beschrijving** van het Excel-bestand toe met behulp van het venster dynamische inhoud. Zorg ervoor dat het middelste vak de tekst **bevat**. Zoek en selecteer vervolgens in het juiste tekstvak `var_plumbing` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 12":::


In de voor waarde **Ja** , klikt u op **een actie toevoegen** en selecteert u **een rij bijwerken**. Voer vervolgens de gegevens in zoals voorheen. Selecteer in de kolom IssueType `var_plumbing` . Hiermee wordt het label ' sanitair ' toegepast op de rij.

In de voor waarde **als Nee** klikt u op **een actie toevoegen** en selecteert u **een rij bijwerken**. Voer vervolgens de gegevens in zoals voorheen. Selecteer in de kolom IssueType `var_other` . Hiermee wordt het label ' Overig ' toegepast op de rij.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Voeg Text Analytics referenties toe aan uw stroom. 13":::

## <a name="test-the-workflow"></a>De werkstroom testen

Klik in de rechter bovenhoek van het scherm op **Opslaan** en vervolgens op **testen**. Selecteer  **Ik voer de trigger actie uit**. Klik op **opslaan & test**, **Voer flow uit** en klik vervolgens op **gereed**.

Het Excel-bestand wordt bijgewerkt in uw OneDrive-account. Dit ziet er als volgt uit.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Het bijgewerkte Excel-werk blad.":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer oplossingen verkennen](../text-analytics-user-scenarios.md)
