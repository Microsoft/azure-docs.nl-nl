---
title: Exporteren naar Power BI vanuit Azure-toepassing inzichten | Microsoft Docs
description: Analytische query's kunnen worden weer gegeven in Power BI.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 1e88d60cd9a9d0e6267998237b33a361b12ffd84
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186997"
---
# <a name="feed-power-bi-from-application-insights"></a>Feed Power BI van Application Insights
[Power bi](https://www.powerbi.com/) is een suite met zakelijke hulp middelen waarmee u gegevens kunt analyseren en inzichten deelt. Uitgebreide Dash boards zijn beschikbaar op elk apparaat. U kunt gegevens uit een groot aantal bronnen combi neren, met inbegrip van analyse query's van [Azure-toepassing Insights](./app-insights-overview.md).

Er zijn drie methoden voor het exporteren van Application Insights gegevens naar Power BI:

* [**Analytics-Query's exporteren**](#export-analytics-queries). Dit is de voorkeursmethode. Schrijf elke gewenste query en exporteer deze naar Power BI. U kunt deze query op een dash board plaatsen, samen met andere gegevens.
* [**Continue export en Azure stream Analytics**](./export-stream-analytics.md). Deze methode is handig als u uw gegevens lange tijd wilt opslaan. Als u geen uitgebreide gegevens Bewaar vereist, gebruikt u de methode voor het exporteren van analyse query's. Continue export en Stream Analytics vergt meer werk voor het instellen en extra opslag overhead.
* **Power bi adapter**. De set met grafieken is vooraf gedefinieerd, maar u kunt uw eigen query's vanuit andere bronnen toevoegen.

> [!NOTE]
> De Power BI adapter is nu **afgeschaft**. De vooraf gedefinieerde grafieken voor deze oplossing worden gevuld door statische niet-bewerk baarheids query's. U beschikt niet over de mogelijkheid om deze query's te bewerken en afhankelijk van bepaalde eigenschappen van uw gegevens kan de verbinding met Power BI worden geslaagd, maar er zijn geen gegevens ingevuld. Dit wordt veroorzaakt door uitsluitings criteria die zijn ingesteld in de hardcoded-query. Hoewel deze oplossing nog steeds werkt voor sommige klanten, vanwege het gebrek aan flexibiliteit van de adapter, is de aanbevolen oplossing de functionaliteit voor het [**exporteren van analyse query's**](#export-analytics-queries) te gebruiken.

## <a name="export-analytics-queries"></a>Analytics-query's exporteren
Met deze route kunt u elke gewenste analyse query schrijven of exporteren uit gebruiks trechters en deze vervolgens exporteren naar een Power BI dash board. (U kunt toevoegen aan het dash board dat is gemaakt door de adapter.)

### <a name="one-time-install-power-bi-desktop"></a>Eén keer: Installeer Power BI Desktop
Als u uw Application Insights query wilt importeren, gebruikt u de bureaublad versie van Power BI. Vervolgens kunt u het publiceren op internet of in uw Power BI Cloud-werk ruimte. 

Installeer [Power bi Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Een Analytics-query exporteren
1. [Open Analytics en schrijf uw query](../log-query/log-analytics-tutorial.md).
2. U kunt de query testen en verfijnen totdat u tevreden bent met de resultaten. Zorg ervoor dat de query correct wordt uitgevoerd in Analytics voordat u deze exporteert.
3. Kies in het menu **exporteren** de optie **Power bi (M)**. Sla het tekstbestand op.
   
    ![Scherm opname van analyse, met het menu exporteren gemarkeerd](./media/export-power-bi/analytics-export-power-bi.png)
4. Selecteer in Power bi Desktop de optie **gegevens**  >  **lege query** ophalen. Selecteer vervolgens in de query-editor onder **weer gave** de optie **Geavanceerde editor**.

    Plak het geëxporteerde M-taal script in de Geavanceerde editor.

    ![Scherm opname van Power BI Desktop, met Geavanceerde editor gemarkeerd](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Als Power BI toegang tot Azure wilt toestaan, moet u mogelijk referenties opgeven. Gebruik het account van de **organisatie** om u aan te melden met uw Microsoft-account.
   
    ![Scherm afbeelding van Power BI dialoog venster query-instellingen](./media/export-power-bi/power-bi-import-sign-in.png)

    Als u de referenties wilt controleren, gebruikt u de menu opdracht **gegevens bron instellingen** in de query-editor. Zorg ervoor dat u de referenties opgeeft die u voor Azure gebruikt. Dit kan afwijken van uw referenties voor Power BI.
6. Kies een visualisatie voor uw query en selecteer de velden voor de x-as, y-as en segmenting dimensie.
   
    ![Scherm opname van Power BI Desktop visualisatie opties](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Publiceer uw rapport naar uw Power BI Cloud-werk ruimte. Van daaruit kunt u een gesynchroniseerde versie insluiten in andere webpagina's.
   
    ![Scherm opname van Power BI Desktop, met de knop publiceren gemarkeerd](./media/export-power-bi/publish-power-bi.png)
8. Vernieuw het rapport hand matig met intervallen of stel een geplande vernieuwing in op de pagina opties.

### <a name="export-a-funnel"></a>Een trechter exporteren
1. [Maak uw trechter](./usage-funnels.md).
2. Selecteer **Power bi**.

   ![Scherm afbeelding van de knop Power BI](./media/export-power-bi/button.png)

3. Selecteer in Power bi Desktop de optie **gegevens**  >  **lege query** ophalen. Selecteer vervolgens in de query-editor onder **weer gave** de optie **Geavanceerde editor**.

   ![Scherm opname van Power BI Desktop, met lege query knop gemarkeerd](./media/export-power-bi/blankquery.png)

   Plak het geëxporteerde M-taal script in de Geavanceerde editor. 

   ![Scherm afbeelding toont de Power BI Desktop, met Geavanceerde editor gemarkeerd](./media/export-power-bi/advancedquery.png)

4. Selecteer items in de query en kies een trechter visualisatie.

   ![Scherm afbeelding toont de visualisatie opties van Power BI Desktop trechter](./media/export-power-bi/selectsequence.png)

5. Wijzig de titel zodat deze zinvol is en publiceer uw rapport naar uw Power BI Cloud werkruimte. 

   ![Scherm opname van Power BI Desktop, waarbij de titel wijziging is gemarkeerd](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Problemen oplossen

Er kunnen fouten optreden met betrekking tot referenties of de grootte van de gegevensset. Hier vindt u informatie over wat u moet doen over deze fouten.

### <a name="unauthorized-401-or-403"></a>Niet geautoriseerd (401 of 403)
Dit kan gebeuren als uw vernieuwings token niet is bijgewerkt. Voer de volgende stappen uit om ervoor te zorgen dat u nog steeds toegang hebt:

1. Meld u aan bij de Azure Portal en zorg ervoor dat u toegang hebt tot de resource.
2. Probeer de referenties voor het dash board te vernieuwen.
3. Probeer de cache te wissen van uw Power BI Desktop.


   Open een ondersteunings ticket als u toegang hebt en de referenties niet werkt.

### <a name="bad-gateway-502"></a>Ongeldige gateway (502)
Dit wordt meestal veroorzaakt door een analyse query die te veel gegevens retourneert. Probeer een kleiner tijds bereik voor de query te gebruiken. 

Als het verminderen van de gegevensset die afkomstig is uit de analyse query niet voldoet aan uw vereisten, kunt u overwegen de [API](https://dev.applicationinsights.io/documentation/overview) te gebruiken om een grotere gegevensset te halen. U kunt het exporteren van de M-query converteren om de API te gebruiken.

1. Maak een [API-sleutel](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Werk het Power BI M-script dat u hebt geëxporteerd uit Analytics bij door de Azure Resource Manager URL te vervangen door de Application Insights-API.
   * **Https: \/ /Management.Azure.com/subscriptions/vervangen...**
   * met, **https: \/ /API.applicationinsights.io/Beta/apps/...**
3. Werk tot slot de referenties bij naar Basic en gebruik uw API-sleutel.

**Bestaand script**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Script bijgewerkt**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Over steek proeven
Afhankelijk van de hoeveelheid gegevens die door uw toepassing wordt verzonden, wilt u mogelijk de adaptieve sampling functie gebruiken. deze verzendt alleen een percentage van uw telemetrie. Dit geldt ook als u hand matig steek proeven hebt ingesteld in de SDK of op opname. Meer [informatie over steek proeven](./sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI adapter (afgeschaft)
Met deze methode maakt u een volledig dash board van telemetrie. De eerste gegevensset is vooraf gedefinieerd, maar u kunt er meer gegevens aan toevoegen.

### <a name="get-the-adapter"></a>De adapter ophalen
1. Meld u aan bij [Power BI](https://app.powerbi.com/).
2. Open de scherm afbeelding **gegevens ophalen** ![ van het pictogram GetData linksonder ](./media/export-power-bi/001.png) , **Services**.

    ![Scherm afbeeldingen toont de knop ophalen in het venster Services.](./media/export-power-bi/002.png)

3. Selecteer **nu downloaden** onder Application Insights.

   ![Scherm opnamen van de gegevens bron ophalen uit Application Insights](./media/export-power-bi/003.png)
4. Geef de details van de Application Insights resource op en **Meld** u aan.

    ![Scherm opname toont het venster verbinding maken met Application Insights.](./media/export-power-bi/005.png)

     Deze informatie vindt u in het overzichts venster van Application Insights:

     ![Scherm afbeelding van de gegevens bron ophalen uit Application Insights](./media/export-power-bi/004.png)

5. Open de zojuist gemaakte Application Insights Power BI-app.

6. Wacht enkele minuten of twee om de gegevens te importeren.

    ![Scherm afbeelding van Power BI adapter](./media/export-power-bi/010.png)

U kunt het dash board bewerken en de Application Insights grafieken combi neren met die van andere bronnen en met Analytics-query's. U kunt meer grafieken ophalen in de visualisatie galerie, en elke grafiek bevat para meters die u kunt instellen.

Na de eerste import blijven het dash board en de rapporten dagelijks worden bijgewerkt. U kunt het vernieuwings schema voor de gegevensset beheren.

## <a name="next-steps"></a>Volgende stappen
* [Power BI-meer informatie](https://www.powerbi.com/learning/)
* [Zelf studie voor analyse](../log-query/log-analytics-tutorial.md)