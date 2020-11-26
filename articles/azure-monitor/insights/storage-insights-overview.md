---
title: Azure Storage services bewaken met Azure Monitor voor opslag | Microsoft Docs
description: In dit artikel wordt de Azure Monitor voor opslag functie beschreven waarmee opslag beheerders een duidelijk beeld krijgen van de prestaties en het gebruik van problemen met hun Azure Storage accounts.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 2538bc91be59ff12b39ee3f5f629e4c016480a97
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296332"
---
# <a name="monitoring-your-storage-service-with-azure-monitor-for-storage"></a>Uw opslag service controleren met Azure Monitor voor opslag

Azure Monitor voor opslag biedt uitgebreide controle over uw Azure Storage-accounts door een uniforme weer gave te bieden van de prestaties, capaciteit en beschik baarheid van uw Azure Storage services. U kunt de opslag capaciteit en prestaties op twee manieren observeren, rechtstreeks vanuit een opslag account of weer gave bekijken van Azure Monitor om over verschillende groepen opslag accounts te kijken. 

Dit artikel helpt u inzicht te krijgen in de ervaring Azure Monitor voor opslag om op schaal bruikbare kennis te verkrijgen over de status en prestaties van opslag accounts, met een mogelijkheid om te focussen op HOTS pots en latentie, beperking en beschikbaarheids problemen te onderzoeken.

## <a name="introduction-to-azure-monitor-for-storage"></a>Inleiding tot Azure Monitor voor opslag

Voordat u aan de slag gaat, moet u weten hoe de informatie wordt gepresenteerd en gevisualiseerd. Ongeacht of u de opslag functie rechtstreeks vanuit een opslag account of van Azure Monitor selecteert, is Azure Monitor voor opslag een consistente ervaring. 

Gecombineerd IT-levert:

* **Op schaal perspectief** met een moment opname van de beschik baarheid op basis van de status van de opslag service of de API-bewerking, het gebruik van het totale aantal aanvragen dat door de opslag service wordt ontvangen en latentie met de gemiddelde tijd dat het bewerkings type voor de opslag service of het API-berekenings aanvragen worden verwerkt. U kunt ook capaciteit weer geven per blob, bestand, tabel en wachtrij.

* **Inzoomen** op een specifiek opslag account om problemen op te lossen of gedetailleerde analyses uit te voeren op categorie-Beschik baarheid, prestaties, fouten en capaciteit. Als u een van deze opties selecteert, krijgt u een gedetailleerde weer gave van metrische gegevens.  

* **Aanpasbaar** waar u kunt wijzigen welke metrische gegevens u wilt zien, wijzigen of instellen van drempel waarden die worden uitgelijnd met uw limieten en opslaan als uw eigen werkmap. Grafieken in de werkmap kunnen worden vastgemaakt aan het dash board van Azure.  

Voor deze functie hoeft u niets in te scha kelen of te configureren. de metrische opslag gegevens van uw opslag accounts worden standaard verzameld. Als u niet bekend bent met metrische gegevens die beschikbaar zijn op Azure Storage, kunt u de beschrijving en definitie in Azure Storage metrieken bekijken door de [metrische gegevens voor Azure Storage](../../storage/blobs/monitor-blob-storage.md)te controleren.

>[!NOTE]
>Er zijn geen kosten verbonden aan het verkrijgen van toegang tot deze functie en er worden alleen kosten in rekening gebracht voor de Azure Monitor essentiële functies die u configureert of inschakelt, zoals wordt beschreven op de pagina met [Azure monitor prijs informatie](https://azure.microsoft.com/pricing/details/monitor/) .


## <a name="view-from-azure-monitor"></a>Weer geven van Azure Monitor

Vanuit Azure Monitor kunt u de trans actie-, latentie-en capaciteits gegevens van meerdere opslag accounts in uw abonnement weer geven en prestaties, capaciteits problemen en fouten helpen identificeren.

Voer de volgende stappen uit om het gebruik en de beschik baarheid van uw opslag accounts in al uw abonnementen weer te geven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **monitor** in het linkerdeel venster in het Azure Portal en selecteer in de sectie **insightss** de optie **opslag accounts**.

    ![Weer gave meerdere opslag accounts](./media/storage-insights-overview/multiple-storage-accounts-view-01.png)

### <a name="overview-workbook"></a>Overzichts werkmap

In de **overzichts** werkmap voor het geselecteerde abonnement worden in de tabel interactieve metrische opslag gegevens en status van service beschikbaarheid weer gegeven voor Maxi maal vijf opslag accounts die zijn gegroepeerd in het abonnement. U kunt de resultaten filteren op basis van de opties die u selecteert in de volgende vervolg keuzelijsten:

* **Abonnementen** : alleen abonnementen met opslag accounts worden weer gegeven.  

* **Opslag accounts** : standaard zijn er 5 opslag accounts vooraf geselecteerd. Als u alle of meerdere opslag accounts in de scope selector selecteert, worden er Maxi maal 200 opslag accounts geretourneerd. Als u bijvoorbeeld in totaal 573 opslag accounts hebt voor drie abonnementen die u hebt geselecteerd, worden er alleen 200-accounts weer gegeven. 

* **Tijds bereik** : de laatste 4 uur aan gegevens worden standaard weer gegeven op basis van de bijbehorende selecties.

De tegel item in de vervolg keuzelijst bevat een samen telling van het totale aantal opslag accounts in het abonnement en geeft aan hoeveel van het totaal wordt geselecteerd. Er zijn voorwaardelijke kleurcoderings-of Heatmaps voor kolommen in de werkmap die de metrische gegevens over de trans actie of fouten rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur op basis van de laagste waarden. Voor de kolommen op basis van fouten is de waarde rood en voor de kolommen op basis van metrische gegevens de waarde blauw.

Selecteer een waarde in de kolommen **Beschik baarheid**, **E2E latentie**, **Server latentie** en **transactie fout type/fouten** stuurt u een rapport dat is afgestemd op het specifieke type metrische opslag gegevens dat overeenkomt met de geselecteerde kolom voor dat opslag account. Zie de sectie [gedetailleerde opslag werkmappen](#detailed-storage-workbooks) hieronder voor meer informatie over de werkmappen voor elke categorie. 

>[!NOTE]
>Zie het [schema antwoord type](../../storage/blobs/monitor-blob-storage-reference.md#metrics-dimensions) en zoek naar antwoord typen zoals **ServerOtherError**, **ClientOtherError**, **ClientThrottlingError** voor meer informatie over welke fouten in het rapport kunnen worden weer gegeven. Afhankelijk van de geselecteerde opslag accounts, worden alle andere fouten weer gegeven onder de **andere** categorie als er meer dan drie typen fouten worden gerapporteerd.

De standaard drempelwaarde voor **Beschik baarheid** is:

* Waarschuwing: 99%
* Kritiek-90%

Als u een beschikbaarheids drempel wilt instellen op basis van de resultaten van uw waarneming of vereisten, raadpleegt u [de drempel waarde voor de beschik baarheid wijzigen](#modify-the-availability-threshold). 

### <a name="capacity-workbook"></a>Capaciteits werkmap

Selecteer de optie **capaciteit** boven aan de pagina en de **capaciteits** werkmap wordt geopend. Hier ziet u de totale hoeveelheid opslag ruimte die wordt gebruikt in het account en de capaciteit die door elke gegevens service in het account wordt gebruikt om te helpen bij het identificeren van en onder gebruik van opslag.

![Werkmap met capaciteit van meerdere opslag accounts](./media/storage-insights-overview/storage-account-capacity-02.png) 

Er zijn voorwaardelijke kleurcoderings-of Heatmaps voor kolommen in de werkmap die de metrische gegevens over de capaciteit met een blauw waarde rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur op basis van de laagste waarden.

Wanneer u een waarde selecteert onder een van de kolommen in de werkmap, zoomt u in op de **capaciteits** werkmap voor het opslag account. Meer informatie over het inzoom rapport wordt beschreven in de sectie [gedetailleerde opslag werkmappen](#detailed-storage-workbooks) hieronder. 

## <a name="view-from-a-storage-account"></a>Weer geven vanuit een opslag account

Direct toegang tot Azure Monitor voor VM's vanuit een opslag account:

1. Selecteer opslag accounts in het Azure Portal.

2. Kies een opslag account in de lijst. Kies inzichten in het gedeelte bewaking.

    ![Scherm afbeelding van de pagina overzichts werkmap voor het opslag account.](./media/storage-insights-overview/storage-account-direct-overview-01.png)

Op de **overzichts** werkmap voor het opslag account worden diverse metrische gegevens over opslag prestaties weer gegeven waarmee u snel kunt beoordelen:

* De status van de opslag service om onmiddellijk te zien of een probleem buiten uw besturings element wordt beïnvloed door de opslag service in de regio waarin het is geïmplementeerd, dat wordt vermeld in de kolom **samen vatting** .

* Interactieve prestatie grafieken met de meest essentiële gegevens met betrekking tot opslag capaciteit, Beschik baarheid, trans acties en latentie.  

* Tegels voor metrische gegevens en status markering Beschik baarheid van de service, het totale aantal trans acties voor de opslag service, E2E latentie en latentie van de server.

Als u een van de knoppen voor **fouten**, **prestaties**, **Beschik baarheid** en **capaciteit** selecteert, wordt de betreffende werkmap geopend. 

![Overzichts pagina voor de geselecteerde opslag account](./media/storage-insights-overview/storage-account-capacity-01.png)

## <a name="detailed-storage-workbooks"></a>Gedetailleerde opslag werkmappen

Hiermee wordt aangegeven of u een waarde hebt geselecteerd in de kolommen **Beschik baarheid**, **E2E latentie**, **Server latentie** en **transactie fout type/fouten** van **de werkmap met** meerdere opslag accounts, of dat u een van de knoppen voor **fouten**, **prestaties**, **Beschik baarheid** en **capaciteit** van de **overzichts** werkmap kunt selecteren op basis van een specifiek opslag account, waarbij elk een set interactieve gegevens met betrekking tot de opslag levert die  

* Met **Beschik baarheid** wordt de **beschikbaarheids** werkmap geopend. Hier ziet u de huidige status van Azure Storage service, een tabel met de beschik bare status van elk object dat is gecategoriseerd door de gegevens service die is gedefinieerd in het opslag account met een trend lijn die het geselecteerde tijds bereik vertegenwoordigt, en een diagram met beschikbaarheids trend voor elke gegevens service in het account.  

    ![Voor beeld van beschik baarheids rapport](./media/storage-insights-overview/storage-account-availability-01.png)

* **E2E-latentie** en **Server latentie** Hiermee opent u de werk werkmap **prestaties** . Het bevat een Rollup-status tegel met E2E latentie en server latentie, een prestatie grafiek van E2E versus server latentie en een tabel die de latentie van geslaagde aanroepen door de API gecategoriseerd door de gegevens service die is gedefinieerd in het opslag account.

    ![Voor beeld van prestatie rapport](./media/storage-insights-overview/storage-account-performance-01.png)

* Als u een van de fout categorieën in het raster selecteert, wordt de werkmap met **fouten** geopend. In het rapport worden metrische tegels van alle andere client-side-fouten weer gegeven, met uitzonde ring van beschreven en geslaagde aanvragen, client-bandbreedte fouten, een prestatie diagram voor het type trans actie- **antwoord** dat specifiek is voor het kenmerk ClientOtherError en twee tabellen- **trans acties op API-naam** en **trans acties per antwoord type**.

   ![Voor beeld van fouten rapport](./media/storage-insights-overview/storage-account-failures-01.png)

* Met **capaciteit** wordt de werkmap met **capaciteit** geopend. Hier wordt de totale hoeveelheid opslag ruimte weer gegeven die wordt gebruikt voor elk opslag gegevens object in het account in de tegels en de grafiek, en hoeveel gegevens objecten in het account zijn opgeslagen.  

    ![Pagina capaciteit geselecteerde opslag account](./media/storage-insights-overview/storage-account-capacity-01.png) 

## <a name="pin-and-export"></a>Vastmaken en exporteren

U kunt een van de metrische gedeelten aan een Azure-dash board vastmaken door het pictogram punaise rechtsboven in de sectie te selecteren.

![Voor beeld van de sectie metrische gegevens van de metriek naar dash board](./media/storage-insights-overview/workbook-pin-example.png)

Het **overzicht** van meerdere abonnementen en opslag accounts of **capaciteits** werkmappen ondersteunen het exporteren van de resultaten in Excel-indeling door het pictogram pijl-omlaag rechts van het pictogram punaise te selecteren.

![Voor beeld van resultaat van werkmap raster exporteren](./media/storage-insights-overview/workbook-export-example.png)

## <a name="customize-azure-monitor-for-storage"></a>Azure Monitor voor opslag aanpassen

In deze sectie worden algemene scenario's beschreven voor het bewerken van de werkmap om uw behoeften aan te passen aan uw gegevens analyse:

* De werkmap zo beperken dat er altijd een bepaald abonnement of opslag account (s) wordt geselecteerd
* Metrische gegevens in het raster wijzigen
* De beschikbaarheids drempel wijzigen
* De kleur weergave wijzigen

De aanpassingen worden opgeslagen in een aangepaste werkmap om te voor komen dat de standaard configuratie in onze gepubliceerde werkmap wordt overschreven. Werkmappen worden opgeslagen in een resource groep, hetzij in het gedeelte **mijn rapporten** dat persoonlijk is of in de sectie **gedeelde rapporten** dat toegankelijk is voor iedereen die toegang heeft tot de resource groep. Nadat u de aangepaste werkmap hebt opgeslagen, moet u naar de galerie met werkmappen gaan om deze te starten.

![Werkmap galerie starten vanaf de opdracht balk](./media/storage-insights-overview/workbook-command-bar-gallery.png)

### <a name="specifying-a-subscription-or-storage-account"></a>Een abonnement of een opslag account opgeven

U kunt de volgende stappen uitvoeren om het **overzicht** van meerdere abonnementen en opslag accounts of **capaciteits** werkmappen te configureren voor een bepaald abonnement (en) of opslag account (s) voor elke uitvoering.

1. Selecteer **monitor** in de portal en selecteer vervolgens **opslag accounts** in het linkerdeel venster.

2. Selecteer in de werk blad **overzicht** de optie **bewerken** in de opdracht balk.

3. Selecteer in de vervolg keuzelijst **abonnementen** een of meer abonnementen waarnaar de standaard instelling moet worden ingesteld. Houd er rekening mee dat de werkmap ondersteuning biedt voor het selecteren van Maxi maal 10 abonnementen.  

4. Selecteer in de vervolg keuzelijst **opslag accounts** een of meer accounts waarnaar de standaard instelling moet worden weer geven. Houd er rekening mee dat de werkmap het selecteren van Maxi maal 200 opslag accounts ondersteunt. 

5. Selecteer **Opslaan als** in de opdracht balk om een kopie van de werkmap met uw aanpassingen op te slaan en klik vervolgens op **gereed bewerken** om terug te keren naar de Lees modus.  

### <a name="modify-metrics-and-colors-in-the-workbook"></a>Metrische gegevens en kleuren in de werkmap wijzigen

De vooraf gemaakte werkmappen bevatten metrische gegevens en u hebt de mogelijkheid om een van de visualisaties te wijzigen of te verwijderen en aan te passen aan de specifieke behoeften van uw team.

In ons voor beeld werken we met de werkmap voor meerdere abonnementen en opslag accounts. zo kunt u zien hoe u:

* Een metriek verwijderen
* Kleur weergave wijzigen

U kunt dezelfde wijzigingen uitvoeren op een van de vooraf gebouwde **fouten**, **prestaties**, **Beschik baarheid** en **capaciteits** werkmappen.

1. Selecteer **monitor** in de portal en selecteer vervolgens **opslag accounts** in het linkerdeel venster.

2. Selecteer **capaciteit** om over te scha kelen naar de capaciteits werkmap en Selecteer vanuit de opdracht balk **bewerken** in de opdracht balk.

    ![Selecteer Bewerken om een werkmap te wijzigen](./media/storage-insights-overview/workbook-edit-workbook.png)

3. Selecteer in het gedeelte metrische gegevens de optie **bewerken**.

    ![Selecteer Bewerken om de metrische gegevens van de capaciteits werkmap te wijzigen](./media/storage-insights-overview/edit-metrics-capacity-workbook-01.png)

4. We gaan de kolom **gebruikte capaciteits tijdlijn** verwijderen, dus Selecteer **kolom instellingen** in het raster metrische gegevens.

    ![Kolom instellingen bewerken](./media/storage-insights-overview/edit-capacity-workbook-resource-grid.png)

5. Selecteer in het deel venster **kolom instellingen bewerken** onder het **Columns** gedeelte kolommen **micro soft. Storage/Storage accounts-capacity-UsedCapacity Timeline $ | Gebruikte capaciteit tijd lijn voor account $**, en onder de vervolg keuzelijst **kolom renderer** selecteert **verborgen**.

6. Selecteer **opslaan en sluiten** om de wijziging door te voeren.

We gaan nu het kleuren thema wijzigen voor de metrische gegevens over capaciteit in het rapport om groen te gebruiken in plaats van blauw.

1. Selecteer **kolom instellingen** in het raster metrische gegevens.

2. Selecteer in het deel venster **kolom instellingen bewerken** onder het **Columns** gedeelte kolommen **micro soft. Storage/Storage accounts-capacity-UsedCapacity $ micro soft. Storage/ `|` Storage accounts/blobservices-capacity-BlobCapacity $ `|` micro soft. Storage/Storage accounts/fileservices-capacity `|` `|`**-FileCapacity $ micro soft. Storage/Storage accounts/queueservices-capacity-QueueCapacity $ micro soft. Storage/Storage accounts/tableservices-capacity-TableCapacity $. Selecteer **groen** onder het **kleuren palet** vervolg keuzelijst.

3. Selecteer **opslaan en sluiten** om de wijziging door te voeren.

4. Selecteer **Opslaan als** in de opdracht balk om een kopie van de werkmap met uw aanpassingen op te slaan en klik vervolgens op **gereed bewerken** om terug te keren naar de Lees modus.  

### <a name="modify-the-availability-threshold"></a>De beschikbaarheids drempel wijzigen

In dit voor beeld werken we met de werkmap capaciteit van opslag account en laten we zien hoe de drempel waarde voor de beschik baarheid kan worden gewijzigd. De beschik baarheid van de tegel-en raster rapportage percentage wordt standaard geconfigureerd met een minimum drempel van 90 en een maximum drempel van 99. We gaan de minimale drempel waarde van het **beschik bare percentage** in het raster **Beschik baarheid per API-naam** wijzigen in 85%, wat betekent dat de status verandert in kritiek als de drempel waarde lager is dan 85 procent. 

1. Selecteer **opslag accounts** in de portal en selecteer vervolgens een opslag account in de lijst.

2. Selecteer **inzichten** in het linkerdeel venster.

3. Selecteer in de werkmap **Beschik baarheid** om over te scha kelen naar de beschikbaarheids werkmap en selecteer vervolgens **bewerken** in de opdracht balk. 

4. Ga naar de onderkant van de pagina en klik aan de linkerkant naast het raster **Beschik baarheid per API** op **bewerken**.

    ![Raster instellingen beschik baarheid bewerken per API-naam](./media/storage-insights-overview/availability-workbook-avail-by-apiname.png)

5. Selecteer **kolom instellingen** en selecteer in het deel venster **kolom instellingen bewerken** onder het gedeelte **kolommen** de optie **Beschik baarheid (%) (Drempel waarden + notatie)**.

6. Wijzig de waarde voor de **kritieke** status van **90** in **85** en klik vervolgens op **opslaan en sluiten**.

    ![Wijzig de drempel waarde voor Beschik baarheid voor kritieke status](./media/storage-insights-overview/edit-column-settings-capacity-workbook-01.png)

7. Selecteer **Opslaan als** in de opdracht balk om een kopie van de werkmap met uw aanpassingen op te slaan en klik vervolgens op **gereed bewerken** om terug te keren naar de Lees modus.

## <a name="troubleshooting"></a>Problemen oplossen

Raadpleeg voor algemene richt lijnen voor probleem oplossing het [artikel specifieke informatie over probleem](troubleshoot-workbooks.md)oplossing op basis van een werkmap.

Deze sectie helpt u bij het diagnosticeren en oplossen van problemen met enkele veelvoorkomende problemen die kunnen optreden bij het gebruik van Azure Monitor voor opslag. Gebruik de onderstaande lijst om de informatie te vinden die relevant is voor uw specifieke probleem.

### <a name="resolving-performance-capacity-or-availability-issues"></a>Problemen met prestaties, capaciteit of Beschik baarheid oplossen

Raadpleeg de Azure Storage [probleemoplossings richtlijnen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)voor hulp bij het oplossen van problemen met de opslag die u identificeert met Azure monitor voor opslag.  

### <a name="why-can-i-only-see-200-storage-accounts"></a>Waarom kan ik alleen 200 opslag accounts zien?

Voor het aantal geselecteerde opslag accounts geldt een limiet van 200, ongeacht het aantal abonnementen dat is geselecteerd.

### <a name="how-to-change-the-coloring-and-threshold-for-availability"></a>Hoe wijzigt u de kleur en drempel waarde voor Beschik baarheid?

Raadpleeg de sectie [de drempel waarde voor Beschik baarheid wijzigen](#modify-the-availability-threshold) voor gedetailleerde stappen voor het wijzigen van de kleur en drempel waarden voor Beschik baarheid.

### <a name="how-to-analyze-and-troubleshoot-the-data-shown-in-azure-monitor-for-storage"></a>Hoe kunt u de gegevens die worden weer gegeven in Azure Monitor voor opslag analyseren en problemen oplossen?

 Raadpleeg Microsoft Azure Storage artikel [controleren, diagnosticeren en problemen oplossen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md) voor meer informatie over het analyseren en oplossen van de Azure Storage gegevens die in azure monitor voor opslag worden weer gegeven.

### <a name="why-dont-i-see-all-the-types-of-errors-in-metrics"></a>Waarom zie ik niet alle typen fouten in metrische gegevens?

Op dit moment worden er Maxi maal drie verschillende typen fouten weer gegeven en worden de overige fouten gegroepeerd in één Bucket. Het wordt gecontroleerd met behulp van splitByLimit en kan worden gewijzigd. Voor het wijzigen van deze eigenschap:

1. Klik op werkmap bewerken.
2. Ga naar metrische gegevens, klik op bewerken en selecteer vervolgens **trans acties, som** of de metrische gegevens die u wilt bewerken.

    ![Ga naar metrische gegevens en klik op bewerken en vervolgens op trans acties, totalen](./media/storage-insights-overview/fqa7.png)

3. Wijzig vervolgens het aantal splitsingen.

    ![Metrische para meters selecteren "](./media/storage-insights-overview/fqa7-2.png)

Als u n verschillende typen fouten wilt zien dan splitByLimit opgeven als n + 1, 1 extra voor de rest van de fouten.

###  <a name="i-saved-my-workbook-while-on-some-storage-account-why-cant-i-find-it-now"></a>Ik heb mijn werkmap opgeslagen in een opslag account. Waarom kan ik dit nu niet vinden?

Elke werkmap wordt opgeslagen in het opslag account waarin u deze hebt opgeslagen. Zoek het specifieke opslag account waarin de gebruiker de werkmap heeft opgeslagen. Anders is het niet mogelijk om een specifieke werkmap te vinden zonder de resource (opslag account) te weten.

## <a name="next-steps"></a>Volgende stappen

* [Waarschuwingen voor metrische gegevens](../platform/alerts-metric.md) en [service status meldingen](../../service-health/alerts-activity-log-service-notifications-portal.md) configureren om automatische waarschuwingen in te stellen voor hulp bij het detecteren van problemen.

* Meer informatie over de scenario's werkmappen zijn ontworpen voor ondersteuning, het ontwerpen van nieuwe en het aanpassen van bestaande rapporten en meer door [interactieve rapporten maken met Azure monitor werkmappen](../platform/workbooks-overview.md)te controleren.

* Zie [Microsoft Azure Storage controleren, diagnosticeren en problemen oplossen](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)voor meer informatie over het gebruik van Opslaganalyse en andere hulpprogram ma's voor het identificeren, vaststellen en oplossen van problemen met Azure Storage.
