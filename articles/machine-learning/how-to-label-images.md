---
title: Tags toevoegen aan afbeeldingen in een labelproject
title.suffix: Azure Machine Learning
description: Leer hoe u de hulpprogramma's voor het toevoegen van tags aan gegevens gebruikt om snel gegevens voor te bereiden voor machine learning in een Azure Machine Learning-labelproject.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: f14e120fb7373cc80b9d6e23f155dd964b37a551
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347962"
---
# <a name="tag-images-in-a-labeling-project"></a>Tags toevoegen aan afbeeldingen in een labelproject 

Nadat uw projectbeheerder [een labelproject](./how-to-create-labeling-projects.md#create-a-labeling-project) in Azure Machine Learning heeft gemaakt, kunt u het hulpprogramma voor het toevoegen van labels gebruiken om snel gegevens voor een Machine Learning-project voor te bereiden. In dit artikel wordt het volgende beschreven:

> [!div class="checklist"]
> * Toegang krijgen tot uw labelprojecten
> * De hulpprogramma's voor labels
> * De hulpprogramma's voor specifieke labeltaken gebruiken

## <a name="prerequisites"></a>Vereisten

* Een [Microsoft-account](https://account.microsoft.com/account) of een Azure Active Directory-account voor de organisatie en het project
* Toegang op inzenderniveau tot de werkruimte met het labelproject.

## <a name="sign-in-to-the-workspace"></a>Aanmelden bij de werkruimte

1. Meld u aan bij [Azure Machine Learning Studio](https://ml.azure.com).

1. Selecteer het abonnement en de werkruimte met het labelproject.  Vraag uw projectbeheerder om deze informatie.

1. Selecteer **Gegevenslabels** aan de linkerkant om het project te zoeken.  

## <a name="understand-the-labeling-task"></a>Begrip van de labeltaak

Selecteer in de tabel met gegevenslabelprojecten de optie **Labelkoppeling** voor uw project.

U ziet nu specifieke instructies voor uw project. In deze instructies wordt uitgelegd met welk type gegevens u te maken hebt, hoe u uw beslissingen neemt en andere relevante informatie. Nadat u deze informatie hebt gelezen, selecteert u bovenaan de pagina de optie **Taken**.  Of selecteer **Beginnen met labelen** onderaan de pagina.

## <a name="common-features-of-the-labeling-task"></a>Algemene functies van de labeltaak

In alle taken waarbij u afbeeldingen van een label voorziet, kiest u een of meer geschikte tags uit een set die door de projectbeheerder is opgegeven. U kunt de eerste negen tags selecteren met behulp van de cijfertoetsen op uw toetsenbord.  

In taken waarbij u afbeeldingen classificeert, kunt u ervoor kiezen om meerdere afbeeldingen tegelijkertijd weer te geven. Gebruik de pictogrammen boven het afbeeldingengebied om de opmaak te selecteren. 

Als u alle weergegeven afbeeldingen tegelijkertijd wilt selecteren, gebruikt u **Alles selecteren**. Als u afzonderlijke afbeeldingen wilt selecteren, gebruikt u de ronde selectieknop in de rechterbovenhoek van de afbeelding. U moet ten minste één afbeelding selecteren waarop u een tag wilt toepassen. Als u meerdere afbeeldingen selecteert, wordt elke tag die u selecteert, toegepast op alle geselecteerde afbeeldingen.

Hier hebben we een opmaak van twee bij twee gekozen en we staan op het punt om de tag 'Zoogdier' toe te passen op de afbeeldingen van de beer en de orka. De afbeelding van de haai was al getagd als 'Kraakbeenvis'; de leguaan is nog niet getagd.

![Opmaak en selectie van meerdere afbeeldingen](./media/how-to-label-images/layouts.png)

> [!Important] 
> Verander de opmaak alleen wanneer u een nieuwe pagina met niet-gelabelde gegevens hebt. Door de opmaak te veranderen, wordt het werk op de pagina dat u al hebt getagd, gewist.

In Azure is de knop **Indienen** beschikbaar zodra u alle afbeeldingen op de pagina hebt getagd. Selecteer **Indienen** om uw werk op te slaan.

Nadat u tags voor de beschikbare gegevens hebt ingediend, wordt de pagina vernieuwd met een nieuwe set afbeeldingen uit de werkwachtrij.

### <a name="assisted-machine-learning-preview"></a>Begeleide machine learning (preview) 

> [!IMPORTANT]
> Begeleide machine learning is momenteel beschikbaar als openbare preview-versie.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Mogelijk worden machine learning-algoritmen geactiveerd. Als deze algoritmen in uw project zijn ingeschakeld, ziet u het volgende:

* Nadat een aantal afbeeldingen is gelabeld, ziet u wellicht **geclusterde taken** bovenaan uw scherm, naast de projectnaam.  Dit betekent dat afbeeldingen zijn gegroepeerd om vergelijkbare afbeeldingen op dezelfde pagina bij elkaar te plaatsen.  Als dit het geval is, schakelt u over naar één van de weergaven met meerdere afbeeldingen om van de groepering te profiteren.  

* Later ziet u wellicht **taken die vooraf al van een label zijn voorzien** naast de projectnaam.  Afbeeldingen worden dan weergegeven met een voorgesteld label dat afkomstig is van een machine learning-classificeringsmodel. Geen enkel machine learning-model biedt 100% nauwkeurigheid. Hoewel we alleen afbeeldingen gebruiken waarvoor het model betrouwbaar is, is het nog wel mogelijk dat deze afbeeldingen onjuist vooraf zijn gelabeld.  Wanneer u deze labels ziet, herstelt u onjuiste labels voordat u de pagina verzendt.  

* Voor objectdetectiemodellen ziet u mogelijk dat er al begrenzingsvakken en labels aanwezig zijn.  Herstel eventueel onjuiste vakken en labels voordat u de pagina verzendt.

Met name in de beginfase van een labelproject is het mogelijk dat het machine learning-model alleen voldoende nauwkeurig is om slechts een kleine subset met afbeeldingen vooraf te labelen. Zodra deze afbeeldingen zijn gelabeld, keert het labelproject terug naar handmatige labels om meer gegevens te verzamelen voor de volgende ronde van de modeltraining. Na verloop van tijd wordt het model betrouwbaarder voor een hogere verhouding afbeeldingen. Dit leidt later in het project tot meer vooraf gelabelde taken.

## <a name="tag-images-for-multi-class-classification"></a>Afbeeldingen van een tag voorzien voor classificering met meerdere klassen

Als uw project van het type 'Classificering van afbeeldingen met meerdere klassen' is, wijst u één tag toe aan de gehele afbeelding. U kunt de richtlijnen op elk gewenst moment nalezen door naar de pagina **Instructies** te gaan en **Gedetailleerde instructies weergeven** te selecteren.

Als u zich realiseert dat u een fout hebt gemaakt nadat u een tag aan een afbeelding hebt toegewezen, kunt u dit herstellen. Selecteer de **X** op het label dat wordt weergegeven onder de afbeelding om de tag te wissen. Of selecteer de afbeelding en kies een andere klasse. De zojuist geselecteerde waarde vervangt de eerdere toegepaste tag.

## <a name="tag-images-for-multi-label-classification"></a>Afbeeldingen van een tag voorzien voor classificering met meerdere labels

Als u aan een project van het type 'Classificering van afbeeldingen met meerdere labels' werkt, past u één *of meer* tags toe op een afbeelding. Als u de projectspecifieke richtlijnen wilt bekijken, selecteert u **Instructies** en gaat u naar **Gedetailleerde instructies weergeven**.

Selecteer de afbeelding die u wilt labelen en selecteer vervolgens de tag. De tag wordt op alle geselecteerde afbeeldingen toegepast en vervolgens wordt de selectie van de afbeeldingen opgeheven. Als u meer tags wilt toepassen, moet u de afbeeldingen opnieuw selecteren. In de volgende animatie ziet u hoe u tags met meerdere labels aanbrengt:

1. **Alles selecteren** wordt gebruikt om de tag 'Oceaan' toe te passen.
1. Er wordt één afbeelding geselecteerd en van de tag 'Close-up' voorzien.
1. Drie afbeeldingen worden geselecteerd en van de tag 'Groothoek' voorzien.

![Animatie met werkstroom voor meerdere labels](./media/how-to-label-images/multilabel.gif)

Als u een fout wilt herstellen, klikt u op de **X** om een afzonderlijke tag te wissen, of selecteer de afbeeldingen en vervolgens de tag. Hiermee wordt de tag uit alle geselecteerde afbeeldingen verwijderd. Dit scenario wordt hier weergegeven. Wanneer u op 'Land' klikt, wordt die tag uit de twee geselecteerde afbeeldingen verwijderd.

![Schermopname met meerdere gedeselecteerde items](./media/how-to-label-images/multiple-deselection.png)

In Azure wordt de knop **Indienen** alleen ingeschakeld nadat u ten minste één tag op elke afbeelding hebt toegepast. Selecteer **Indienen** om uw werk op te slaan.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Afbeeldingen van een tag voorzien en begrenzingsvakken voor objectdetectie opgeven

Als uw project van het type 'Objectidentificatie (begrenzingsvakken)' is, geeft u een of meer begrenzingsvakken in de afbeelding op en past u op elk vak een tag toe. Afbeeldingen kunnen over meerdere begrenzingsvakken beschikken, elk met één tag. Gebruik **Gedetailleerde instructies weergeven** om te bepalen of er meerdere begrenzingsvakken worden gebruikt in uw project.

1. Selecteer een tag voor het begrenzingsvak dat u wilt maken.
1. Selecteer het hulpprogramma **Rechthoekig vak** ![hulpprogramma Rechthoekig vak](./media/how-to-label-images/rectangular-box-tool.png) of selecteer 'R'.
3. Klik en versleep de cursor diagonaal over uw doel om een onbewerkt begrenzingsvak te maken. Als u het begrenzingsvak wilt aanpassen, versleept u de randen of de hoeken.

![Begrenzingsvak maken](./media/how-to-label-images/bounding-box-sequence.png)

Als u een begrenzingsvak wilt verwijderen, klikt u op het X-vormige doel dat naast het begrenzingsvak wordt weergegeven zodra het vak is gemaakt.

U kunt de tag van een bestaand begrenzingsvak niet wijzigen. Als u bij het toewijzen van tags een fout hebt gemaakt, moet u het begrenzingsvak verwijderen en een nieuw vak maken met de juiste tag.

Standaard kunt u bestaande begrenzingsvakken bewerken. U kunt dit gedrag veranderen met behulp van het hulpprogramma **Regio's vergrendelen/ontgrendelen** ![hulpprogramma Regio's vergrendelen/ontgrendelen](./media/how-to-label-images/lock-bounding-boxes-tool.png) of met de 'L'-toets. Als regio's zijn vergrendeld, kunt u alleen de vorm of locatie van een nieuw begrenzingsvak wijzigen.

Gebruik het hulpprogramma voor het **bewerken van regio's** ![Dit is het pictogram voor het hulpprogramma voor het bewerken van regio's; vier pijlen die vanuit het midden naar boven, rechts, beneden en links wijzen.](./media/how-to-label-images/regions-tool.png) of 'M' om een bestaand begrenzingsvak aan te passen. Sleep de randen of hoeken om de vorm aan te passen. Klik in de binnenkant van het vak om het hele begrenzingsvak te verslepen. Als u een regio niet kunt bewerken, hebt u waarschijnlijk het hulpprogramma **Regio's vergrendelen/ontgrendelen** ingeschakeld.

Gebruik het hulpprogramma **Op een sjabloon gebaseerd vak** ![hulpprogramma Op een sjabloon gebaseerd vak](./media/how-to-label-images/template-box-tool.png) of 'T' om meerdere begrenzingsvakken van dezelfde grootte te maken. Als de afbeelding geen begrenzingsvakken heeft en u vakken op sjabloonbasis activeert, worden in het hulpprogramma vakken van 50 x 50 pixels gemaakt. Als u een begrenzingsvak maakt en vervolgens vakken op sjabloonbasis activeert, krijgen alle nieuwe begrenzingsvakken dezelfde grootte als het laatste vak dat u hebt gemaakt. De grootte van vakken op sjabloonbasis kan worden aangepast nadat u het vak hebt geplaatst. Met het aanpassen van de grootte van een vak op sjabloonbasis wordt alleen de grootte van dat specifieke vak aangepast.

Als u *alle* begrenzingsvakken in de huidige afbeelding wilt verwijderen, selecteert u het hulpprogramma **Alle regio's verwijderen** ![hulpprogramma Regio's verwijderen](./media/how-to-label-images/delete-regions-tool.png).

Nadat u de begrenzingsvakken voor een afbeelding hebt gemaakt, selecteert u **Indienen** om uw werk op te slaan, anders wordt uw werk in uitvoering niet opgeslagen.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Afbeeldingen labelen en veelhoeken tekenen om afbeeldingen te segmenteren 

Als u een project van het type 'Instantiesegmentatie (veelhoek)' heeft, dan voegt u één of meer veelhoeken toe aan de afbeelding en past u voor elke veelhoek een label toe. Afbeeldingen kunnen over meerdere begrenzingsveelhoeken beschikken, elk met één tag. Gebruik **Gedetailleerde instructies weergeven** om te bepalen of er meerdere begrenzingsveelhoeken worden gebruikt in uw project.

1. Selecteer een tag voor de veelhoek die u wilt maken.
1. Selecteer de tool **Veelhoekregio tekenen** ![tool Veelhoek tekenen](./media/how-to-label-images/polygon-tool.png) of druk op 'P'.
1. Klik voor elk punt in de veelhoek.  Wanneer uw vorm af is, dubbelklikt u om te voltooien.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Veelhoeken maken voor kat en hond":::

Als u een veelhoeken wilt verwijderen, klikt u op het X-vormige doel dat naast de veelhoek wordt weergegeven zodra het vak is gemaakt.

Als u het label voor een veelhoek wilt wijzigen, selecteert u de tool **Gebied verplaatsen**, klikt u op de veelhoek en selecteert u de juiste tag.

U kunt bestaande veelhoeken bewerken. U kunt dit gedrag veranderen met behulp van de tool **Regio's vergrendelen/ontgrendelen** ![Veelhoeken bewerken met de tool Regio's vergrendelen/ontgrendelen](./media/how-to-label-images/lock-bounding-boxes-tool.png) of met de 'L'-toets. Als regio's zijn vergrendeld, kunt u alleen de vorm of locatie van een nieuwe veelhoek wijzigen.

Gebruik het hulpprogramma **Veelhoekpunten toevoegen of verwijderen** ![Dit is het pictogram voor het hulpprogramma Veelhoekpunten toevoegen of verwijderen.](./media/how-to-label-images/add-remove-points-tool.png) of druk op 'U' om een bestaande veelhoek te wijzigen. Klik op de veelhoek om een punt toe te voegen of te verwijderen. Als u een regio niet kunt bewerken, hebt u waarschijnlijk het hulpprogramma **Regio's vergrendelen/ontgrendelen** ingeschakeld.

Als u *alle* veelhoeken in de huidige afbeelding wilt verwijderen, selecteert u het hulpprogramma **Alle regio's verwijderen** ![hulpprogramma Alle regio's verwijderen](./media/how-to-label-images/delete-regions-tool.png).

Nadat u de veelhoeken voor een afbeelding hebt gemaakt, selecteert u **Indienen** om uw werk op te slaan, anders wordt uw werk in uitvoering niet opgeslagen.

## <a name="finish-up"></a>Voltooien

Wanneer u een pagina met getagde gegevens indient, worden nieuwe niet-gelabelde gegevens aan u toegewezen vanuit een werkwachtrij. Als er geen niet-gelabelde gegevens meer beschikbaar zijn, krijgt u een bericht waarin dit wordt vermeld, naast een koppeling naar de startpagina van de portal.

Wanneer u klaar bent met labelen, selecteert u uw naam in de rechterbovenhoek van de labelportal en vervolgens selecteert u **Afmelden**. Als u zich niet afmeldt, zal uiteindelijk een time-out worden gecreëerd en worden uw gegevens aan een andere labelmaker toegewezen.

## <a name="next-steps"></a>Volgende stappen

* Informatie over [Modellen voor de classificatie van afbeeldingen trainen in Azure](./tutorial-train-models-with-aml.md)


