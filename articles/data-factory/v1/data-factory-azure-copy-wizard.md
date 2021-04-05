---
title: Wizard Azure Copy Data Factory
description: Meer informatie over Data Factory het gebruik van de wizard Azure kopiëren om gegevens te kopiëren van ondersteunde gegevens bronnen naar Sinks.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393374"
---
# <a name="azure-data-factory-copy-wizard"></a>Wizard Azure Data Factory kopiëren

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

De wizard voor het Azure Data Factory kopiëren vereenvoudigt het proces van het opnemen van gegevens. Dit is meestal een eerste stap in een end-to-end gegevens integratie scenario. Wanneer u de wizard kopiëren van Azure Data Factoryt, hoeft u geen JSON-definities te begrijpen voor gekoppelde services, gegevens sets en pijp lijnen. De wizard maakt automatisch een pijp lijn om gegevens van de geselecteerde gegevens bron naar de geselecteerde bestemming te kopiëren. Daarnaast helpt de wizard kopiëren u bij het valideren van de gegevens die op het moment van ontwerpen worden opgenomen. Dit bespaart tijd, vooral wanneer u gegevens voor de eerste keer opneemt vanuit de gegevens bron. Als u de wizard kopiëren wilt starten, klikt u op de tegel **gegevens kopiëren** op de start pagina van uw Data Factory.

![De wizard Kopiëren](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Ontworpen voor big data
Met deze wizard kunt u eenvoudig gegevens van een groot aantal bronnen naar bestemmingen verplaatsen binnen enkele minuten. Nadat u de wizard hebt door lopen, wordt er automatisch een pijp lijn met een Kopieer activiteit voor u gemaakt, samen met afhankelijke Data Factory entiteiten (gekoppelde services en gegevens sets). Er zijn geen extra stappen vereist om de pijp lijn te maken.   

![Gegevensbron selecteren](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Voor stapsgewijze instructies voor het maken van een voorbeeld pijplijn voor het kopiëren van gegevens van een Azure-Blob naar een Azure SQL Database tabel, raadpleegt u de [zelf studie](data-factory-copy-data-wizard-tutorial.md)over het kopiëren van de wizard.

De wizard is ontworpen met big data van het begin, met ondersteuning voor diverse gegevens en object typen. U kunt Data Factory pijp lijnen ontwerpen waarmee honderden mappen, bestanden of tabellen worden verplaatst. De wizard ondersteunt automatische gegevens weergave, schema-opname en-toewijzing en gegevens filtering.

## <a name="automatic-data-preview"></a>Automatische gegevens voorbeeld
U kunt een voor beeld van een deel van de gegevens uit de geselecteerde gegevens bron weer geven om te controleren of de gegevens die u wilt kopiëren, moeten worden gekopieerd. Als de bron gegevens zich in een tekst bestand bevinden, parseert de wizard kopiëren ook het tekst bestand om de rij-en kolom scheidings tekens en het schema automatisch te leren.

![Pagina Instellingen bestandsindelingen](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schema's vastleggen en toewijzen
Het schema van invoer gegevens komt mogelijk niet overeen met het schema van de uitvoer gegevens in sommige gevallen. In dit scenario moet u kolommen uit het bron schema toewijzen aan kolommen uit het doel schema.

> [!TIP]
> Bij het kopiëren van gegevens uit SQL Server of Azure SQL Database naar Azure Synapse Analytics, als de tabel niet bestaat in het doel archief, Data Factory ondersteuning voor het automatisch maken van tabellen met het schema van de bron. Meer informatie over [het verplaatsen van gegevens van en naar Azure Synapse Analytics met behulp van Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).

Gebruik een vervolg keuzelijst om een kolom uit het bron schema te selecteren die u wilt toewijzen aan een kolom in het doel schema. De wizard kopiëren probeert het patroon voor de kolom toewijzing te begrijpen. Hiermee wordt hetzelfde patroon op de rest van de kolommen toegepast, zodat u niet elke kolom afzonderlijk hoeft te selecteren om de schema toewijzing te volt ooien. Als u wilt, kunt u deze toewijzingen negeren door de kolommen één voor één toe te wijzen met behulp van de vervolg keuzelijsten. Het patroon wordt nauw keuriger naarmate u meer kolommen toewijst. Met de wizard kopiëren wordt het patroon continu bijgewerkt en uiteindelijk het juiste patroon bereikt voor de kolom toewijzing die u wilt bereiken.     

![Schema toewijzing](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Gegevens filteren
U kunt bron gegevens filteren om alleen de gegevens te selecteren die moeten worden gekopieerd naar de Sink-gegevens opslag. Filteren vermindert het volume van de gegevens die moeten worden gekopieerd naar de Sink-gegevens opslag en verhoogt daarom de door Voer van de Kopieer bewerking. Het biedt een flexibele manier om gegevens in een relationele data base te filteren met behulp van de SQL-query taal of bestanden in een Azure Blob-map door gebruik te maken van [Data Factory-functies en-variabelen](data-factory-functions-variables.md).   

### <a name="filtering-of-data-in-a-database"></a>Filteren van gegevens in een Data Base
De volgende scherm afbeelding toont een SQL-query met behulp van de `Text.Format` functie en de `WindowStart` variabele.

![Expressies valideren](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filteren van gegevens in een Azure Blob-map
U kunt variabelen in het mappad gebruiken om gegevens te kopiëren uit een map die wordt bepaald tijdens runtime op basis van [systeem variabelen](data-factory-functions-variables.md#data-factory-system-variables). De ondersteunde variabelen zijn: **{Year}**, **{Month}**, **{Day}**, **{Hour}**, **{Minute}** en **{Custom}**. Bijvoorbeeld: inputfolder/{year}/{month}/{Day}.

Stel dat u een invoer mappen hebt met de volgende indeling:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klik op de knop **Bladeren** voor **bestand of map**, blader naar een van deze mappen (bijvoorbeeld 2016->03->01->02) en klik op **kiezen**. U ziet `2016/03/01/02` in het tekstvak. Vervang nu **2016** door **{Year}**, **03** met **{Month}**, **01** met **{Day}** en **02** met **{Hour}** en druk op de **Tab** -toets. Er moeten vervolg keuzelijsten worden weer gegeven om de indeling voor deze vier variabelen te selecteren:

![Systeem variabelen gebruiken](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Zoals in de volgende scherm afbeelding wordt weer gegeven, kunt u ook een **aangepaste** variabele en [ondersteunde opmaak teken reeksen](/dotnet/standard/base-types/custom-date-and-time-format-strings)gebruiken. Als u een map met die structuur wilt selecteren, gebruikt u eerst de knop **Bladeren** . Vervang vervolgens een waarde door **{Custom}** en druk op de **Tab** -toets om het tekstvak weer te geven waarin u de notatie teken reeks kunt invoeren.     

![Aangepaste variabele gebruiken](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Plannings opties
U kunt de Kopieer bewerking eenmaal of volgens een schema uitvoeren (elk uur, dagelijks, enzovoort). Beide opties kunnen worden gebruikt voor de breedte van de connectors in omgevingen, waaronder on-premises, Cloud en lokale bureaublad kopieën.

Een eenmalige Kopieer bewerking maakt het verplaatsen van gegevens van een bron naar een bestemming slechts één keer mogelijk. Dit is van toepassing op gegevens van elke grootte en een ondersteunde indeling. Met de geplande kopie kunt u gegevens kopiëren naar een voorgeschreven terugkeer patroon. U kunt uitgebreide instellingen (zoals nieuwe poging, time-out en waarschuwingen) gebruiken om de geplande kopie te configureren.

![Plannings eigenschappen](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Problemen oplossen

In deze sectie worden algemene probleemoplossings methoden voor de wizard kopiëren in Azure Data Factory besproken.

> [!NOTE] 
> Deze tips voor probleem oplossing zijn van toepassing op de wizard kopiëren in versie 1 van Data Factory. Zie voor Data Factory v2 probleemoplossings gids bij het [oplossen van problemen Azure Data Factory](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Fout code: kan niet valideren in wizard kopiëren

- **Symptomen**: in de eerste stap van de wizard kopiëren wordt het volgende waarschuwings bericht over ' kan niet valideren ' aangetroffen.
- **Oorzaken**: dit kan gebeuren wanneer alle cookies van derden zijn uitgeschakeld.
- **Oplossing**: 
    - Gebruik Internet Explorer of micro soft Edge-browser.
    - Als u Chrome browser gebruikt, volgt u de onderstaande instructies om een uitzonde ring voor cookies voor *microsoftonline.com* en *Windows.net* toe te voegen.
        1.  Open de Chrome-browser.
        2.  Klik op de moersleutel of drie regels aan de rechter kant (Google Chrome aanpassen en beheren).
        3.  Klik op **Instellingen**.
        4.  Zoek **cookies** of ga naar **Privacy** onder Geavanceerde instellingen.
        5.  Selecteer **inhouds instellingen**.    
        6.  Cookies moeten worden ingesteld zodat **lokale gegevens kunnen worden ingesteld (aanbevolen)**.
        7.  Klik op **uitzonde ringen beheren**. Onder het- **hostname-patroon** voert u het volgende in en zorgt u ervoor dat het gedrag is ingesteld op **toestaan** .
            - login.microsoftonline.com
            - login.windows.net
        8.  Sluit de browser en start opnieuw.
    - Als u de Firefox-browser gebruikt, volgt u de onderstaande instructies om een uitzonde ring voor cookies toe te voegen.
        1. Ga in het menu van Firefox naar **extra**  >  **Opties**.
        2. Onder   >  **geschiedenis** van privacy ziet u mogelijk dat de huidige instelling **aangepaste instellingen voor geschiedenis gebruikt**.
        3. In **cookies** van derden accepteren is uw huidige instelling mogelijk **nooit**. Klik vervolgens op **uitzonde ringen** aan de rechter kant om de volgende sites toe te voegen.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Sluit de browser en start opnieuw. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Fout code: kan aanmeldings pagina niet openen en wacht woord invoeren

- **Symptomen**: door de wizard kopiëren wordt u omgeleid naar de aanmeldings pagina, maar de aanmeldings pagina wordt niet correct weer gegeven.
- **Oorzaken**: dit probleem kan zich voordoen als u de netwerk omgeving van het bedrijfs netwerk hebt gewijzigd in het thuis netwerk. Browsers bevatten enkele caches. 
- **Oplossing**: 
    1.  Sluit de browser en probeer het opnieuw. Ga naar de volgende stap als het probleem zich nog steeds voordoet.   
    2.  Als u Internet Explorer browser gebruikt, probeert u het te openen in de privé modus (druk op CTRL + SHIFT + P). Als u Chrome browser gebruikt, probeert u het te openen in de Incognito-modus (druk op CTRL + SHIFT + N). Ga naar de volgende stap als het probleem zich nog steeds voordoet. 
    3.  Probeer een andere browser te gebruiken. 


## <a name="next-steps"></a>Volgende stappen
Zie [zelf studie: een pijp lijn maken met behulp van de wizard kopiëren](data-factory-copy-data-wizard-tutorial.md)voor een snelle beschrijving van het gebruik van de wizard voor het kopiëren van Data Factory om een pijp lijn te maken met de Kopieer activiteit.