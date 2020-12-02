---
title: 'Zelfstudie: Een webpagina voor aangepaste zoekopdrachten maken - Bing Aangepaste zoekopdrachten'
titleSuffix: Azure Cognitive Services
description: Leer in deze zelfstudie hoe u een exemplaar voor aangepaste zoekopdrachten voor Bing maakt en integreert met een nieuwe webpagina.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: a789cb3fde05d12a8793196043f1c246bbab6559
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342410"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Zelfstudie: Een Aangepaste zoekopdrachten-webpagina bouwen

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht volgens het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's ingericht met behulp van Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Zie [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Met Bing Aangepaste zoekopdrachten kunt u op maat gemaakte zoekervaringen maken voor onderwerpen die u interesseren. Als u bijvoorbeeld een website over martial arts hebt die een zoekervaring biedt, kunt u de domeinen, subsites en webpagina's opgeven waarin Bing moet zoeken. Uw gebruikers zien dan zoekresultaten die zijn afgestemd op de inhoud die ze interessant vinden in plaats van dat ze door pagina's met algemene zoekresultaten moeten bladeren die mogelijk niet-relevante inhoud bevatten. 

Deze zelfstudie laat zien hoe u een exemplaar voor aangepaste zoekopdrachten maakt en integreert met een nieuwe webpagina.

De behandelde taken zijn:

> [!div class="checklist"]
> - Een exemplaar voor aangepaste zoekopdrachten maken
> - Actieve vermeldingen toevoegen
> - Geblokkeerde vermeldingen toevoegen
> - Vastgemaakte vermeldingen toevoegen
> - Aangepaste zoekopdrachten integreren met een webpagina

## <a name="prerequisites"></a>Vereisten

- Als u deze zelfstudie wilt volgen, hebt u een abonnementssleutel nodig voor de Bing Aangepaste zoekopdrachten-API.  Als u een sleutel wilt verkrijgen [Maakt u een Bing Custom Search-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) in de Azure-portal.
- Als u Visual Studio 2017 of hoger nog niet hebt geïnstalleerd, kunt u het downloaden en de **gratis** [Community Edition van Visual Studio 2019](https://www.visualstudio.com/downloads/) gebruiken.

## <a name="create-a-custom-search-instance"></a>Een exemplaar voor aangepaste zoekopdrachten maken

Een exemplaar voor aangepaste zoekopdrachten met Bing maken:

1. Open een browser.  
  
2. Ga naar de [portal](https://customsearch.ai) voor aangepaste zoekopdrachten.  
  
3. Meld u aan bij de portal met een Microsoft-account (MSA). Als u geen MSA hebt, klikt u op **Create a Microsoft account**. Als dit de eerste keer is dat u de portal gebruikt, wordt u gevraagd om de service toegang te geven tot uw gegevens. Klik op **Ja**.  
  
4. Als u bent aangemeld, klikt u op **Create new instance**. Voer in het venster **Create a new custom search instance** een beschrijvende naam in die duidelijk aangeeft wat voor inhoud de zoekopdracht retourneert. U kunt de naam overigens altijd wijzigen.  
  
   ![Schermafbeelding van het vak voor het invoeren van een naam voor het nieuwe exemplaar voor aangepaste zoekopdrachten](../media/newCustomSrch.png)  
  
5. Klik op OK, geef een URL op en schakel het selectievakje in als u subpagina's wilt opnemen.  
  
   ![Schermafbeelding van pagina voor opgeven van URL](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Actieve vermeldingen toevoegen

Als u resultaten van bepaalde websites of URL's wilt opnemen, voegt u deze toe aan het tabblad **Active**.

1. Klik op de pagina **Configuration** op het tabblad **Active** en voer de URL in van een of meer websites die u wilt opnemen in de zoekopdracht.

    ![Schermafbeelding van het tabblad Active](../media/customSrchEditor.png)

2. Controleer of het exemplaar resultaten retourneert door een query in te voeren in het voorbeeldvenster aan de rechterkant. Bing retourneert alleen resultaten voor openbare websites die zijn geïndexeerd door de service.

## <a name="add-blocked-entries"></a>Geblokkeerde vermeldingen toevoegen

Als u resultaten van bepaalde websites of URL's wilt uitsluiten, voegt u deze toe aan het tabblad **Blocked**.

1. Klik op de pagina **Configuration** op het tabblad **Blocked** en voer de URL in van een of meer websites die u wilt uitsluiten van de zoekopdracht.

    ![Schermafbeelding van het tabblad Blocked](../media/blockedCustomSrch.png)


2. Controleer of het exemplaar geen resultaten retourneert van de geblokkeerde websites door een query in te voeren in het voorbeeldvenster aan de rechterkant. 

## <a name="add-pinned-entries"></a>Vastgemaakte vermeldingen toevoegen

Als u een bepaalde webpagina altijd aan het begin van de lijst met zoekresultaten wilt weergeven, voegt u de webpagina en zoekterm toe aan het tabblad **Pinned**. Het tabblad **Pinned** bevat een lijst van paren van webpagina's en zoektermen die de webpagina opgeven die als het beste resultaat wordt weergegeven voor een specifieke query. De webpagina wordt alleen vastgemaakt als de querytekenreeks van de gebruiker overeenkomt met de querytekenreeks van de vastgemaakte webpagina, op basis van de voorwaarde voor overeenkomst van de vastgemaakte pagina. In zoekopdrachten worden alleen geïndexeerde webpagina's weergegeven. Zie [Uw aangepaste weergave definiëren](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results) voor meer informatie.

1. Klik op de pagina **Configuration** op het tabblad **Pinned** en voer de webpagina en zoekterm in van de webpagina die u als het bovenste resultaat in de lijst wilt weergeven.  
  
2. De standaardinstelling is dat de queryreeks van de gebruiker exact moet overeenkomen met de queryreeks van de vastgemaakte webpagina om door Bing als het beste resultaat te worden geretourneerd. Om de voorwaarde voor overeenkomst te wijzigen, bewerkt u de vastgemaakte webpagina (klik op het potloodpictogram), klikt u op Exact in de kolom **Query match condition** en selecteert u de voorwaarde voor overeenkomst die geschikt is voor uw toepassing.  
  
    ![Schermafbeelding van het tabblad Pinned](../media/pinnedCustomSrch.png)
  
3. Controleer of uw exemplaar de opgegeven webpagina als het beste resultaat retourneert door in het voorbeeldvenster aan de rechterkant de zoekterm in te voeren die u hebt vastgemaakt.

## <a name="configure-hosted-ui"></a>Gehoste gebruikersinterface configureren

Custom Search biedt een gehoste gebruikersinterface voor het weergeven van het JSON-antwoord van het exemplaar voor aangepaste zoekopdrachten. Volg deze stappen om de ervaring in de gebruikersinterface te definiëren:

1. Klik op het tabblad **Hosted UI**.  
  
2. Selecteer een lay-out.  
  
   ![Schermafbeelding van het selecteren van een lay-out voor de gehoste gebruikersinterface](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Selecteer een kleurenthema.  
  
   ![Schermafbeelding van het selecteren van een kleurenthema voor de gehoste gebruikersinterface](./media/custom-search-hosted-ui-select-color-theme.png)  

   Als u het kleurenthema wilt afstemmen voor een betere integratie met uw web-app, klikt u op **Customize theme**. Niet alle kleurconfiguraties zijn van toepassing op alle lay-outthema's. Als u een kleur wilt wijzigen, voert u de hexadecimale RGB-waarde van de kleur (bijvoorbeeld #366eb8) in het bijbehorende tekstvak in. U kunt ook op de kleurknop klikken en vervolgens op de gewenste tint. Denk altijd aan toegankelijkheid bij het selecteren van kleuren.
  
   ![Schermafbeelding van het aanpassen van een kleurenthema voor de gehoste gebruikersinterface](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Geef aanvullende configuratieopties op.  
  
   ![Schermafbeelding van het opgeven van aanvullende configuraties voor de gehoste gebruikersinterface](./media/custom-search-hosted-ui-additional-configurations.png)  
  
   Als u geavanceerde configuraties wilt ophalen, klikt u op **Show advanced configurations**. U ziet dan opties zoals *Link target* onder Web search, *Enable filters* onder Image search en Video search, en *Search box text placeholder* onder Miscellaneous.

   ![Schermafbeelding van het opgeven van geavanceerde configuraties voor de gehoste gebruikersinterface](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Selecteer uw abonnementssleutels in de vervolgkeuzelijsten. U kunt de abonnementssleutel ook handmatig invoeren.
  
   ![Schermafbeelding van de abonnementssleutel voor de gehoste UI](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Gehoste gebruikersinterface implementeren

Er zijn twee manieren om de gehoste-gebruikersinterface te gebruiken.  

- Optie 1: Het opgegeven JavaScript-fragment integreren in de toepassing.
- Optie 2: Het opgegeven HTML-eindpunt gebruiken.

In de rest van deze zelfstudie wordt het volgende toegelicht: **Optie 1: JavaScript-fragment**.  

## <a name="set-up-your-visual-studio-solution"></a>Uw Visual Studio-oplossing instellen

1. Open **Visual Studio** op uw computer.  
  
2. Selecteer in het menu **Bestand** de optie **Nieuw** en kies vervolgens **Project**.  
  
3. Selecteer **Visual C# / Web / ASP.NET Core Web Application** in het venster **New Project**, geef het project een naam en klik vervolgens op **OK**.  
  
   ![Schermafbeelding van het dialoogvenster New Project](./media/custom-search-new-project.png)  
  
4. Selecteer in het venster **New ASP.NET Core Web Application** de optie **Web Application** en klik op **OK**.  
  
   ![Schermafbeelding van het dialoogvenster New WebApp](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>index.cshtml bewerken

1. Vouw in de **Solution Explorer** het onderdeel **Pages** uit en dubbelklik op **index.cshtml** om het bestand te openen.  
  
   ![Schermafbeelding van Solution Explorer met Pages uitgevouwen en index.cshtml geselecteerd](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. Verwijder in index.cshtml alles vanaf regel 7.  
  
   ```razor
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }    
   ```  
  
3. Voeg een regeleinde (br) toe en een div-element om te fungeren als een container.  
  
   ```html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch"></div>
   ```  
  
4. Blader op de pagina **Hosted UI** omlaag naar de sectie **Consuming the UI**. Klik op *Endpoints* voor toegang tot het JavaScript-fragment. U kunt ook naar het codefragment gaan door te klikken op **Production** en vervolgens op het tabblad **Hosted UI**.
  
   <!-- Get new screenshot after prod gets new bits
   ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
   -->
  
5. Plak het script-element in de container die u hebt toegevoegd.  
  
   ``` html
   @page
   @model IndexModel
   @{
      ViewData["Title"] = "Home page";
   }
   <br />
   <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
   </div>
   ```  
  
6. Klik in de **Solution Explorer** met de rechtermuisknop op **wwwroot** en klik op **View in Browser**.  
  
   ![Schermafbeelding van de optie View in Browser in het contextmenu van wwwroot in Solution Explorer](./media/custom-search-webapp-view-in-browser.png)  

De nieuwe webpagina voor aangepaste zoekopdrachten moet er ongeveer als volgt uitzien:

![Schermafbeelding van webpagina voor aangepaste zoekopdrachten](./media/custom-search-webapp-browse-index.png)

Het uitvoeren van een zoekopdracht levert resultaten op als deze:

![Schermafbeelding van resultaten van aangepaste zoekopdrachten](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bing Aangepaste zoekopdrachten-eindpunt aanroepen (C#)](../call-endpoint-csharp.md)