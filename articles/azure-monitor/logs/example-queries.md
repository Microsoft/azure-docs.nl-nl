---
title: Log Analytics in Azure Monitor biedt sets met voorbeeld query's die u zelf kunt uitvoeren of gebruiken als uitgangs punt voor uw eigen query's.
description: Query's die u kunt starten en wijzigen voor uw behoeften
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 06/16/2020
ms.openlocfilehash: e1b6ba22c41e2061acfdf33351619512f9d7f424
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611868"
---
# <a name="example-queries-in-azure-monitor-log-analytics"></a>Voorbeeld query's in Azure Monitor Log Analytics
Log Analytics biedt sets met voorbeeld query's die u zelf kunt uitvoeren of gebruiken als uitgangs punt voor uw eigen query's. In dit artikel worden voorbeeld query's en hoe u deze gebruikt.

Als u niet bekend bent met Log Analytics of de KQL-query taal, zijn voorbeeld query's een uitstekende manier om te starten. Ze kunnen direct inzicht bieden in een resource en een goede manier bieden om KQL te leren en te gebruiken, waardoor het veel tijd kost om Log Analytics te gebruiken. We hebben meer dan 250 voorbeeld query's verzameld en voorzien van meer dan een voor beeld van een onmiddellijke waarde. het aantal voorbeeld query's is continu groeien.

## <a name="in-context-queries"></a>Query's in de context

De nieuwe ervaring filtert en suggereert query's in context. Met andere woorden, het systeem toont automatisch alleen query's die relevant zijn voor het bereik dat u hebt geselecteerd.

- Voor **één resource** : query's worden gefilterd op basis van het resource type.
- Voor een **resource groep** : query's worden gefilterd op basis van de resources in de specifieke resource groep.
- Voor een **werk ruimte** : query's worden gefilterd op basis van de oplossingen die in de werk ruimte zijn geïnstalleerd.

Dit gedrag is consistent voor alle Log Analytics scopes. Als u een voorbeeld query voor het gewenste bron type niet ziet, kan dit worden veroorzaakt door filters, omdat deze zich in de context bevinden. In een latere sectie wordt beschreven hoe u in-context-scopes verwijdert zodat u alle mogelijke query's kunt weer geven.

> [!TIP]
> Hoe meer resources u in uw bereik hebt, hoe langer het duurt om de portal te filteren en het dialoog venster voor beeld van de query weer te geven.

## <a name="example-query-user-interface"></a>Voorbeeld query gebruikers interface

U kunt voorbeeld query's van twee verschillende locaties ontvangen.

### <a name="example-query-dialog"></a>Voorbeeld query dialoog venster

Wanneer u de Log Analytics-ervaring voor het eerst opgeeft, wordt het *dialoog venster voorbeeld query's* automatisch weer gegeven.  U kunt deze ook openen door in de rechter bovenhoek van het scherm op **voorbeeld query's** te klikken.

![Terzijde-voorbeeld query's](media/example-queries/sidebar-2.png)

Het dialoog venster voor beeld query wordt weer gegeven, zoals hieronder wordt weer gegeven:  

![Scherm met voorbeeld query's](media/example-queries/example-query-start.png)

In de vorige scherm afbeelding ziet u het scherm logboeken voor een Azure Key Vault-exemplaar. Zoals eerder in dit artikel wordt vermeld, worden de query's weer gegeven in-context.  Als gevolg hiervan worden alleen Key Vault gerelateerde voor beelden weer gegeven in de scherm opname. Als u een volledig abonnement selecteert, worden er query's voor alle resource typen in dat abonnement weer gegeven.  

Elke voorbeeld query wordt weer gegeven door een kaart. U kunt snel de query's scannen om te vinden wat u nodig hebt. U kunt de query rechtstreeks vanuit het dialoog venster uitvoeren of ervoor kiezen om deze in de query-editor te laden voor meer nauw keurigheid en aanpassing.

### <a name="sidebar-query-experience"></a>Sidebar-query-ervaring

U kunt alle dezelfde functionaliteit van de dialoog venster openen vanuit het paneel query's aan de rechter zijbalk van Log Analytics. U kunt de muis aanwijzer over een query naam bewegen om de beschrijving van de query en aanvullende functionaliteit op te halen.

![Scherm opname van het deel venster query's.](media/example-queries/sidebar-3.png)

## <a name="finding-and-filtering-queries"></a>Query's zoeken en filteren

De opties in deze sectie zijn beschikbaar in het dialoog venster voor het maken van query's en Sidebar, maar met een iets andere gebruikers interface.  

### <a name="use-favorites"></a>Favorieten gebruiken

U kunt veelgebruikte query's gebruiken om sneller toegang te krijgen.

> [!TIP]
> Het verzamelen en weer geven van query's is een goede manier om aan de slag te gaan. Zoek de query's die u nodig hebt en klik op de ster naast de query om deze toe te voegen aan de favorieten. Als u de query later niet nuttig vindt, kunt u de zoek opdracht ongedaan maken.  

### <a name="filtering-and-group-by"></a>Filteren en groeperen op

Hoewel het dialoog venster voor het doorzoeken van bestanden alleen query's met de juiste context toont, kunt u ervoor kiezen het filter te verwijderen en alle query's weer te geven.

### <a name="group-by"></a>Groeperen op

Wijzig de groepering van de query's door te klikken op de vervolg keuzelijst *groeperen op* :

![Voorbeeld query's scherm GroupBy](media/example-queries/example-query-groupby.png)

Het dialoog venster ondersteunt groeperen op:

- **Resource type** : een resource zoals gedefinieerd in azure, zoals een virtuele machine. Zie de [Naslag informatie over Azure monitor](/azure/azure-monitor/reference/tables/tables-resourcetype) tabellen voor een volledige toewijzing van Azure monitor logboeken/log Analytics tabellen naar resource type.  
- **Categorie** : een type informatie zoals *beveiliging* of *controle*. Categorieën zijn identiek aan de categorieën die zijn gedefinieerd in het deel venster tabellen. Zie de [Naslag informatie over Azure monitor tabellen](/azure/azure-monitor/reference/tables/tables-category) voor een volledige lijst met categorieën.  
- **Oplossing** : een Azure monitor oplossing die is gekoppeld aan de query's
- **Onderwerp** : het onderwerp van de voorbeeld query, zoals *activiteiten logboeken* of *app-logboeken*. De eigenschap onderwerp is uniek voor voorbeeld query's en kan verschillen op basis van het specifieke resource type.

De groeperings waarden fungeren ook als een actieve inhouds opgave. Als u op een van de waarden aan de linkerkant van het scherm klikt, wordt de weer gave query's rechts naar het item geklikt.

### <a name="filter"></a>Filter

U kunt de query's ook filteren **op basis van** de waarden die eerder zijn vermeld. In het dialoog venster voor beeld query worden de filters bovenaan gevonden.

![Scherm filter voor voorbeeld query's](media/example-queries/example-query-filter.png)

### <a name="combining-group-by-and-filter"></a>Group by en filter combi neren

De functies filter en groeperen op zijn ontworpen om samen te werken. Ze bieden flexibiliteit in de rang schikking van query's. Als u bijvoorbeeld een resource groep met meerdere resources gebruikt, wilt u mogelijk filteren op een specifiek resource type en de resulterende query's rangschikken op onderwerp.

## <a name="sample-query-dialog-appearance-behavior"></a>Weergave gedrag van voorbeeld query dialoog venster

Als u een KQL Pro bent en liever rechtstreeks naar de query-editor wilt gaan, kunt u het dialoog venster voor query's in-/uitschakelen. Als de schakel optie is uitgeschakeld, wordt het dialoog venster voor beeld query niet geladen wanneer Log Analytics scherm wordt geladen.

![Voor beelden in-/uitschakelen](media/example-queries/examples-on-off.png)

U hebt altijd toegang tot de pop-upfunctie voor voorbeeld query's via de knop *voor beeld query's* in de bovenste balk van log Analytics.

## <a name="query-explorer"></a>Queryverkenner

De query Explorer-ervaring voor het opslaan en delen van door gebruikers gegenereerde query's blijft ongewijzigd gedurende de tijd.

## <a name="next-steps"></a>Volgende stappen

[Aan de slag met KQL-Query's](../log-query/get-started-queries.md)

