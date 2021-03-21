---
title: Gegevens normalisatie in azure-Sentinel | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe Azure Sentinel gegevens uit verschillende bronnen normaliseert en het normalisatie schema bijgeeft.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 5d847ac7ed805ad88bc24ed63896edc6f7596f9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729773"
---
# <a name="normalization-in-azure-sentinel"></a>Normalisatie in azure Sentinel

In dit artikel wordt uitgelegd hoe u gegevens schema normalisatie in azure Sentinel, en met name het schema voor netwerk verbindingen en sessies, waarin een koppeling is opgenomen.

## <a name="what-is-normalization"></a>Wat is normalisatie?

Bij het werken met verschillende gegevens typen en-tabellen worden uitdagingen gepresenteerd. U moet bekend zijn met een groot aantal verschillende gegevens typen en schema's, zodat u een unieke set analyse regels, werkmappen en query's kunt uitvoeren voor elke, zelfs voor degenen die commonalities delen (bijvoorbeeld met betrekking tot firewall apparaten). De correlatie tussen de verschillende gegevens typen die nodig zijn voor onderzoek en jacht, is ook lastig. Azure Sentinel biedt een naadloze ervaring voor het afhandelen van gegevens uit verschillende bronnen in uniforme, genormaliseerde weer gaven.

Het Azure Sentinel **Common Information-Model** bestaat uit drie aspecten:

- **Genormaliseerde schema's** hebben betrekking op veelvoorkomende sets van voorspellende gebeurtenis typen (tabellen) die eenvoudig kunnen worden gebruikt en voor het bouwen van geïntegreerde mogelijkheden op (bijvoorbeeld netwerken tabel). Het schema bevat ook een genormaliseerde kolom Conventie en definities voor de standaardisering van de waarde en indeling (standaard consistente weer gave van gegevens zoals IP-adressen).

- **Parsers** wijzen bestaande gegevens van verschillende typen toe aan het genormaliseerde schema. Volgens het model kunnen gegevens worden geparseerd naar het genormaliseerde schema in query tijd (met behulp van functies) of opname tijd. Op dit moment wordt alleen het parseren van de query tijd ondersteund.

- **Inhoud voor elk genormaliseerd schema** bevat analyse regels, interactieve werkmappen, jagers query's en aanvullende inhoud.

### <a name="current-release"></a>Huidige release

In deze release is de [genormaliseerde netwerk verbindingen en het schema voor sessies](./normalization-schema.md) (v 1.0.0) beschikbaar voor open bare preview. In het schema worden netwerk verbindingen of-sessies beschreven, zoals apparaten die zijn geregistreerd door firewalls, bedradings gegevens, NSG, netstroom, proxy systemen en Web Security-gateways.  Er zijn geselecteerde query-time-parsers en analyse regels beschikbaar in combi natie met het schema en deze gebruiken.

Het schema is momenteel alleen beschikbaar via query-time parsers (Zie de sectie parsers).

U kunt gegevens parseren naar aanvullende weer gaven en het [naamgevings model OSSEM entiteiten](https://ossemproject.com/cdm/entities/intro.html#) gebruiken om kolommen te maken die consistent zijn met bestaande en toekomstige genormaliseerde tabellen.

## <a name="normalized-schema-and-parsing"></a>Genormaliseerd schema en parseren

### <a name="how-our-normalized-schemas-are-defined"></a>Hoe zijn de genormaliseerde schema's gedefinieerd?

Azure Sentinel wordt uitgelijnd met het gemeen schappelijke meta gegevens model van de [Open Source Security-gebeurtenissen (OSSEM)](https://ossemproject.com/intro.html) , waardoor voorspel bare entiteiten de correlatie over de genormaliseerde tabellen mogelijk maakt. OSSEM is een door de Community geleid project dat voornamelijk gericht is op de documentatie en standaardisering van beveiligings logboeken van verschillende gegevens bronnen en besturings systemen. Daarnaast biedt het project een Common Information Model (CIM) dat kan worden gebruikt voor data engineers tijdens gegevens normalisatie procedures om beveiligings analisten in staat te stellen gegevens in verschillende gegevens bronnen op te vragen en te analyseren.

De [OSSEM CIM](https://ossemproject.com/cdm/intro.html) definieert een set entiteiten (bijvoorbeeld: bestand, host, IP, proces) en definieert een set kenmerken voor elke entiteit. Daarnaast definieert de CIM een set tabellen (bijvoorbeeld een tabel met een [netwerk sessie](https://ossemproject.com/cdm/tables/network_session.html) ) die relevante kenmerken van deze entiteiten gebruikt, waardoor naadloze en voorspel bare correlatie mogelijk is. Houd er rekening mee dat entiteiten kunnen worden genest (bijvoorbeeld bron entiteit kan een bestands entiteit bevatten die een naam kenmerk heeft).

Ga voor meer informatie over de OSSEM-entiteits structuur naar de [officiële OSSEM-referentie](https://ossemproject.com/cdm/guidelines/entity_structure.html).

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>Hoe de genormaliseerde schema's worden geïmplementeerd in azure Sentinel

In de implementatie van de OSSEM CIM van Azure wordt de OSSEM-vertegenwoordiging geprojecteerd naar een Log Analytics in tabel vorm, of deze representatie een ingebouwde tabel is of is gemaakt met behulp van parsers voor query's of functies die bestaande gegevens aan deze representatie toewijzen. Voor de tabel weergave worden OSSEM entiteits namen en kenmerk namen samengevoegd en worden ze gezamenlijk toegewezen aan één kolom naam. Bijvoorbeeld een bron entiteit die een bestands entiteit bevat met een hash-entiteit die een MD5-kenmerk bevat, wordt geïmplementeerd als de volgende Log Analytics kolom: SrcFileHashMd5. (OSSEM maakt standaard gebruik van *snake_case* , terwijl Azure Sentinel en log Analytics *PascalCase* gebruiken. In OSSEM wordt een dergelijke kolom src_file_hash_md5.)

Er kunnen aanvullende aangepaste velden aanwezig zijn in de implementatie van Azure Sentinel, vanwege Log Analytics platform vereisten en use-cases die specifiek zijn voor Azure Sentinel-klanten.

### <a name="schema-reference"></a>Schema verwijzing

Raadpleeg het [document voor schema verwijzingen](./normalization-schema.md), evenals de officiële [OSSEM-project documentatie](https://ossemproject.com/cdm/intro.html), voor meer informatie.

De schema verwijzing bevat ook de standaardisering van value en Format. De bron velden oorspronkelijk of geparseerd zijn mogelijk niet in een standaard indeling of gebruiken de lijst met standaard waarden van het schema, en moeten daarom worden geconverteerd naar de schema standaard-weer gave om volledig te kunnen worden genormaliseerd.

## <a name="parsers"></a>Parsers

- [Wat is parseren?](#what-is-parsing)
- [Query tijd parsers gebruiken](#using-query-time-parsers)

### <a name="what-is-parsing"></a>Wat is parseren?

Als er een basisset gedefinieerde gestandaardiseerde tabellen beschikbaar is, moet u uw gegevens transformeren (parseren/toewijzen) in deze tabellen. Dat wil zeggen dat u specifieke gegevens uit het onbewerkte formulier in bekende kolommen in het genormaliseerde schema kunt ophalen. Parsering in azure Sentinel gebeurt op het moment dat de **query** wordt uitgevoerd. parsers worden gebouwd als log Analytics gebruikers functies (met behulp van Kusto query language-KQL) waarmee gegevens in bestaande tabellen (zoals CommonSecurityLog, aangepaste logboek tabellen, syslog) worden omgezet in het genormaliseerde tabellen schema.

De andere soort parsering, die nog niet wordt ondersteund in azure Sentinel, is op **opname tijd** , waardoor gegevens rechtstreeks kunnen worden verzameld in de genormaliseerde tabel (len) wanneer deze uit de gegevens bronnen ervan worden opgenomen. Het parseren van opname tijd levert betere prestaties wanneer het gegevens model rechtstreeks wordt opgevraagd zonder dat er functies hoeven te worden gebruikt.

### <a name="using-query-time-parsers"></a>Query tijd parsers gebruiken

- [Een parser installeren](#installing-a-parser)
- [De parsers gebruiken](#using-the-parsers)
- [Parsers aanpassen](#customizing-parsers)

#### <a name="installing-a-parser"></a>Een parser installeren

De beschik bare parsers van de query tijd zijn beschikbaar in de Azure Sentinel [officiële github-opslag plaats](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0)). Elke parser heeft een versie, zodat klanten hun toekomstige updates eenvoudig kunnen gebruiken en controleren. Een parser installeren:

1. Kopieer de relevante parser-inhoud van elk relevante KQL-bestand in de bovenstaande GitHub-koppeling naar het klem bord

1. Open in de Azure-Sentinel-Portal de pagina logboeken en plak de inhoud van het KQL-bestand in het scherm logboeken en klik op **Opslaan**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Een nieuwe parser installeren":::

1. Vul de velden in het dialoog venster Opslaan als volgt in:
    1. **Naam**: het wordt aanbevolen om dezelfde waarde te gebruiken die wordt gebruikt in het veld voor de **functie alias** (in het bovenstaande voor beeld *CheckPoint_Network_NormalizedParser*)
    
    1. **Opslaan als**: **functie** selecteren

    1. **Functie alias**: moet worden genoemd in de volgende naam conventie- *PRODUCT_Network_NormalizedParser* (in bovenstaand voor beeld *CheckPoint_Network_NormalizedParser*).

    1. **Categorie**: u kunt een bestaande categorie selecteren of een nieuwe categorie maken (zoals *NormalizedNetworkSessionsParsers*)
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="De parser opslaan":::

Als u de parsers correct wilt gebruiken, moet u ook de lege Network schema-parser installeren (die een lege tabellaire weer gave van alle velden van het netwerk sessie schema maakt) en de meta-parser van het netwerk (waarmee alle ingeschakelde parsers worden samengevoegd om één weer gave van gegevens te maken van verschillende bronnen in het netwerk schema). De installatie van deze twee parsers geschiedt op een vergelijk bare manier met de bovengenoemde stappen.

Bij het opslaan van een query functie kan het nodig zijn om de query Verkenner te sluiten en opnieuw te openen om de nieuwe functie weer te Spie laten.

#### <a name="using-the-parsers"></a>De parsers gebruiken

Wanneer deze functie is ingeschakeld, kunt u de meta-parser gebruiken om een query uit te geven op een gecombineerde weer gave op alle momenteel ingeschakelde parsers. Als u dit wilt doen, gaat u naar de pagina Azure Sentinel logs en voert u een query uit op de meta-parser:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Query uitvoeren op de parser":::
 
U kunt ook de meta-parser of de afzonderlijke parsers openen met behulp van query Explorer op de pagina logboeken door op query Explorer te klikken:

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Query Verkenner":::

Vouw in het deel venster aan de rechter kant het gedeelte ' opgeslagen query's ' uit en zoek de map ' NormalizedNetworkParsers ' (of de naam van de categorie die u hebt gekozen bij het maken van de parsers):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Uw parser zoeken":::

U kunt op elke afzonderlijke parser klikken en de onderliggende functie zien die wordt gebruikt, en deze uitvoeren (of rechtstreeks toegang krijgen tot de bijbehorende alias, zoals hierboven wordt beschreven). Houd er rekening mee dat sommige parsers de oorspronkelijke velden naast elkaar kunnen bewaren in de genormaliseerde velden voor het gemak. Dit kan eenvoudig worden bewerkt in de query van de parser.

> [!TIP]
> U kunt uw opgeslagen functies gebruiken in plaats van Azure-Sentinel-tabellen in een query, met inbegrip van jacht-en detectie query's. Zie voor meer informatie:
>
> - [Gegevens normalisatie in azure Sentinel](normalization.md#parsers)
> - [Tekst parseren in Azure Monitor-logboeken](../azure-monitor/logs/parse-text.md)
>
#### <a name="customizing-parsers"></a>Parsers aanpassen

U kunt de bovenstaande stappen herhalen (Zoek de parser in query Explorer), klikt u op de relevante parser en bekijkt u de functie-implementatie.
U kunt bijvoorbeeld besluiten om de meta-parser te bewerken om afzonderlijke parsers toe te voegen of te verwijderen.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Uw parser aanpassen":::
 
Zodra de functie is gewijzigd, klikt u nogmaals op opslaan en gebruikt u dezelfde naam, alias en categorie. Er wordt een dialoog venster voor onderdrukking geopend: druk op OK:

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Weet je het zeker":::

#### <a name="additional-information"></a>Aanvullende informatie

JSON, XML en CSV zijn met name handig voor het parseren van query's. Azure Sentinel heeft ingebouwde functies voor het parseren van JSON, XML en CSV, evenals een hulp programma voor JSON-parsering.  Zie [JSON-velden gebruiken in azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) (blog) voor meer informatie. 

Meer informatie over [opgeslagen query's](../azure-monitor/logs/example-queries.md) (de implementatie van query's tijd parsers) in log Analytics.


## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd over het normalisatie schema van Azure Sentinel. Zie [Azure Sentinel data normalisatie-schema referentie](./normalization-schema.md)voor het referentie schema zelf.

* [Azure-verklikker blog](https://aka.ms/azuresentinelblog). Raadpleeg de blogberichten over beveiliging en naleving in Azure.