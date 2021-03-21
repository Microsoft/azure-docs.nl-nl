---
title: Jacht-blad wijzers gebruiken voor gegevens onderzoek in azure Sentinel
description: In dit artikel wordt beschreven hoe u de Azure Sentinel jacht-blad wijzers gebruikt om gegevens bij te houden.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: 5e3b6802bab2c5fae97be8e55c8d5c11ff570f78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94651309"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Gegevens bijhouden tijdens opsporing met Azure Sentinel

Voor de dreigings-jacht moet u de bergen aan logboek gegevens controleren op het zoeken naar informatie over schadelijk gedrag. Tijdens dit proces vinden onderzoekers gebeurtenissen die ze graag willen onthouden, opnieuw bezoeken en analyseren als onderdeel van het valideren van mogelijke hypo theses en het volledige verhaal van een inbreuk.

Bewaak blad wijzers in azure Sentinel Help u dit doen door de query's te behouden die u in **Azure Sentinel-logs** hebt uitgevoerd, samen met de query resultaten die u relevant acht. U kunt ook uw contextuele waarnemingen vastleggen en naar uw bevindingen verwijzen door notities en tags toe te voegen. De van bladwijzers voorziene gegevens zijn zichtbaar voor u en uw teamleden waardoor samenwerking eenvoudig is.

U kunt uw bladwijzer gegevens op elk gewenst moment opnieuw bezoeken op het tabblad **blad wijzers** in het deel venster **jacht** . U kunt filter- en zoekopties gebruiken om snel specifieke gegevens te vinden voor uw huidige onderzoek. U kunt uw gebladde gegevens ook rechtstreeks in de tabel **HuntingBookmark** in uw log Analytics-werk ruimte weer geven. Bijvoorbeeld:

> [!div class="mx-imgBorder"]
> ![HuntingBookmark-tabel weer geven](./media/bookmarks/bookmark-table.png)

Door blad wijzers in de tabel weer te geven, kunt u met andere gegevens bronnen gebladeerde gegevens filteren, samenvatten en samen voegen, zodat u gemakkelijk corroborating bewijs materiaal kunt vinden.

Als er momenteel een preview-versie beschikbaar is, kunt u, als u iets vindt dat in uw logboeken dringend moet worden aangepakt, een blad wijzer maken en deze promo veren naar een incident, of de blad wijzer toevoegen aan een bestaand incident. Zie voor meer informatie over incidenten [zelf studie: incidenten onderzoeken met Azure Sentinel](tutorial-investigate-cases.md). 

U kunt ook in de preview-versie uw bladwijzer gegevens visualiseren door te klikken op **onderzoeken** in de bladwijzer gegevens. Hiermee wordt de onderzoek ervaring gestart waarbij u uw bevindingen kunt bekijken, onderzoeken en visueel kunnen communiceren met behulp van een interactief diagram en tijd lijn van een interactieve entiteit.

## <a name="add-a-bookmark"></a>Een bladwijzer toevoegen

1. Navigeer in het Azure Portal naar **Sentinel**  >  **Threat Management**-  >  **jacht** om query's uit te voeren voor verdacht en afwijkend gedrag.

2. Selecteer een van de zoek query's en rechts in de details van de query query's **uitvoeren**. 

3. Selecteer **query resultaten weer geven**. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![query resultaten van Azure Sentinel-jacht weer geven](./media/bookmarks/new-processes-observed-example.png)
    
    Met deze actie worden de resultaten van de query in het deel venster **Logboeken** geopend.

4. Gebruik de selectie vakjes in de resultaten lijst van de logboek query om een of meer rijen te selecteren die de informatie bevatten die u interessant vindt.

5. Selecteer **blad wijzer toevoegen**:
    
    > [!div class="mx-imgBorder"]
    > ![Jacht-blad wijzer aan query toevoegen](./media/bookmarks/add-hunting-bookmark.png)

6. Op de rechter kant kunt u in het deel venster **blad wijzer toevoegen** optioneel de naam van de blad wijzer bijwerken, Tags toevoegen en notities om u te helpen bij het identificeren van wat er interessant is voor het item.

7. Gebruik in de sectie **query gegevens** de vervolg keuzelijst om informatie uit de query resultaten voor de entiteits typen **account**, **host** en **IP-adres** te extra heren. Met deze actie wordt het geselecteerde entiteits type toegewezen aan een specifieke kolom uit het query resultaat. Bijvoorbeeld:
    
    > [!div class="mx-imgBorder"]
    > ![Entiteits typen toewijzen voor jacht-blad wijzer](./media/bookmarks/map-entity-types-bookmark.png)
    
    Als u de blad wijzer in het onderzoek diagram (momenteel in preview-versie) wilt weer geven, moet u ten minste één entiteits type toewijzen dat een **account**, **host** of **IP-adres** is. 

5. Klik op **Opslaan** om uw wijzigingen door te voeren en de blad wijzer toe te voegen. Alle gegevens met een blad wijzer worden gedeeld met andere onderzoekers en zijn een eerste stap in de buurt van een samenwerkings ervaring.

 
> [!NOTE]
> De query resultaten van de logboeken ondersteunen blad wijzers wanneer dit deel venster wordt geopend vanuit Azure Sentinel. U kunt bijvoorbeeld **algemene**  >  **Logboeken** selecteren op de navigatie balk, gebeurtenis koppelingen selecteren in de grafiek onderzoeken of een waarschuwings-id selecteren uit de volledige details van een incident (momenteel als preview-versie). U kunt geen blad wijzers maken wanneer het deel venster **Logboeken** wordt geopend vanaf andere locaties, zoals rechtstreeks vanuit Azure monitor.

## <a name="view-and-update-bookmarks"></a>Blad wijzers weer geven en bijwerken 

1. Navigeer in het Azure Portal naar **Sentinel**  >  **Threat Management**-  >  **jacht**. 

2. Selecteer het tabblad **blad wijzers** om de lijst met blad wijzers weer te geven.

3. Gebruik het zoekvak of de filter opties om een specifieke blad wijzer te vinden.

4. Selecteer afzonderlijke blad wijzers en Bekijk de bladwijzer Details in het rechterdeel venster met details.

5. Breng de gewenste wijzigingen aan, die automatisch worden opgeslagen.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Blad wijzers in het onderzoek diagram verkennen

> [!IMPORTANT]
> Het verkennen van blad wijzers in het onderzoek diagram en de onderzoek grafiek zelf bevinden zich momenteel in de open bare preview.
> Deze functies worden zonder service level agreement gegeven en worden niet aanbevolen voor productie werkbelastingen.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

1. Ga in het Azure Portal naar het tabblad voor het zoeken naar **Sentinel**  >  **Threat Management**  >    >  -**Blades** en selecteer de blad wijzer of blad wijzers die u wilt onderzoeken.

2. Zorg er in de Blade Details voor dat er ten minste één entiteit is toegewezen. Voor **entiteiten** ziet u bijvoorbeeld vermeldingen voor **IP**, **machine** of **account**.

3. Klik op **onderzoeken** om de blad wijzer in het onderzoek diagram weer te geven.

Voor instructies voor het gebruik van de onderzoek grafiek raadpleegt u [de onderzoek grafiek gebruiken om te diep](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)vinden.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Blad wijzers toevoegen aan een nieuw of bestaand incident

> [!IMPORTANT]
> Het toevoegen van blad wijzers aan een nieuw of bestaand incident is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

1. Ga in het Azure Portal naar het tabblad voor het zoeken naar **Sentinel**  >  **Threat Management**  >    >  -**Blades** en selecteer de blad wijzer of blad wijzers die u aan een incident wilt toevoegen.

2. Selecteer **incident acties (preview)** op de opdracht balk:
    
    > [!div class="mx-imgBorder"]
    > ![Blad wijzers toevoegen aan incident](./media/bookmarks/incident-actions.png)

3. Selecteer een **nieuw incident maken** of **toevoegen aan een bestaand incident**, indien nodig. Daarna kunt u het volgende doen:
    
    - Voor een nieuw incident: werk eventueel de details van het incident bij en selecteer vervolgens **maken**.
    - Ga als volgt te werk om een blad wijzer toe te voegen aan een bestaand incident: Selecteer één incident en selecteer vervolgens **toevoegen**. 

De blad wijzer binnen het incident bekijken: Navigeer naar **Sentinel**  >  **Threat Management**  >  **incidenten** en selecteer het incident met uw blad wijzer. Selecteer **volledige details weer geven** en selecteer vervolgens het tabblad **blad wijzers** .

> [!TIP]
> Als alternatief voor de optie **incident acties (preview)** op de opdracht balk kunt u het context menu (**...**) gebruiken voor een of meer blad wijzers om opties te selecteren voor het maken van een **nieuw incident**, **het toevoegen van een bestaand incident en het** **verwijderen van incidenten**. 

## <a name="view-bookmarked-data-in-logs"></a>Blade gegevens in logboeken weer geven

Als u de Blade query's, resultaten of hun geschiedenis wilt weer geven, selecteert u de blad wijzer op het tabblad **jacht**-  >  **blad wijzers** en gebruikt u de koppelingen in het detail venster: 

- **Bron query weer** geven om de bron query in het deel venster **Logboeken** weer te geven.

- **Bekijk de blad wijzer logboeken** om alle meta gegevens van de blad wijzer te bekijken. deze bevatten de bijgewerkte waarden en de tijd waarop de update is uitgevoerd.

U kunt ook de gegevens van de onbewerkte Blade voor alle blad wijzers weer geven door **bladwijzer logboeken** te selecteren in de opdracht balk op het tabblad **jacht**-  >  **blad wijzers** :

> [!div class="mx-imgBorder"]
> ![Bladwijzer logboeken](./media/bookmarks/bookmark-logs.png)

In deze weer gave worden alle blad wijzers met gekoppelde meta gegevens weer gegeven. U kunt [query language](/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) -query's (KQL) gebruiken om te filteren op de nieuwste versie van de Blade die u zoekt.

> [!NOTE]
> Er kan een aanzienlijke vertraging optreden (gemeten in minuten) tussen de tijd dat u een blad wijzer maakt en wanneer deze wordt weer gegeven op het tabblad **blad wijzers** .

## <a name="delete-a-bookmark"></a>Een blad wijzer verwijderen
 
1.  Ga in het Azure Portal naar het tabblad voor het zoeken naar **Sentinel**  >  **Threat Management**  >    >  -**Blades** en selecteer de blad wijzer of blad wijzers die u wilt verwijderen. 

2. Klik met de rechter muisknop op uw selecties en selecteer de optie voor het verwijderen van de blad wijzer of blad wijzers. U kunt bijvoorbeeld **blad wijzer verwijderen** als u slechts één blad wijzer hebt geselecteerd en **2 blad wijzers verwijderen** als u twee blad wijzers hebt geselecteerd.
    
Als u de blad wijzer verwijdert, wordt de blad wijzer verwijderd uit de lijst op het tabblad **blad wijzer** . De tabel **HuntingBookmark** voor uw log Analytics-werk ruimte blijft vorige bladwijzer vermeldingen bevatten, maar met de laatste vermelding wordt de **SoftDelete** -waarde gewijzigd in True, waardoor het gemakkelijk is om oude blad wijzers te filteren. Als u een blad wijzer verwijdert, worden er geen entiteiten uit de onderzoek ervaring verwijderd die aan andere blad wijzers of waarschuwingen zijn gekoppeld. 


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een jacht onderzoek uitvoert met behulp van blad wijzers in azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:


- [Proactief zoeken naar bedreigingen](hunting.md)
- [Notitie blokken gebruiken voor het uitvoeren van geautomatiseerde jacht-campagnes](notebooks.md)