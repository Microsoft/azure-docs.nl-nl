---
title: Prestaties presen teren met VM Insights
description: Prestaties is een functie van de VM-inzichten die automatisch toepassings onderdelen op Windows-en Linux-systemen detecteert en de communicatie tussen services toewijst. In dit artikel vindt u informatie over het gebruik ervan in verschillende scenario's.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/31/2020
ms.openlocfilehash: 9c69ea3da71063d7e20ebf31ae2eb3df9a51e2c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725438"
---
# <a name="how-to-chart-performance-with-vm-insights"></a>Prestaties presen teren met VM Insights

VM Insights bevat een reeks prestatie diagrammen die gericht zijn op verschillende Key Performance Indica tors (Kpi's) waarmee u kunt bepalen hoe goed een virtuele machine wordt uitgevoerd. In de grafieken wordt het resource gebruik gedurende een bepaalde periode weer gegeven, zodat u knel punten, afwijkingen kunt identificeren of overschakelen naar een perspectief waarin elke machine wordt weer gegeven op basis van de geselecteerde metriek. Hoewel er talrijke elementen zijn waarmee u rekening moet houden bij de uitvoering van prestaties, bewaakt VM Insights de prestatie-indica toren van het sleutel besturings systeem, gerelateerd aan de processor, het geheugen, de netwerk adapter en het schijf gebruik. Prestaties vormen een aanvulling op de status controle functie en helpen problemen op te lossen die duiden op een mogelijke systeem onderdeel fout, ondersteuning voor afstemming en Optima Lise ring om efficiëntie te beleven of capaciteits planning te ondersteunen.  

## <a name="limitations"></a>Beperkingen
Hieronder vindt u beperkingen bij het verzamelen van prestaties met VM Insights.

- **Beschikbaar geheugen** is niet beschikbaar voor virtuele machines waarop Red Hat Linux (RHEL) 6 wordt uitgevoerd. Deze metriek wordt berekend op basis van **MemAvailable** die is geïntroduceerd in [kernel versie 3,14](http://www.man7.org/linux/man-pages/man1/free.1.html).
- Metrieken zijn alleen beschikbaar voor gegevens schijven op virtuele Linux-machines met behulp van XFS File System of EXT File System-familie (EXT2, EXT3, EXT4).

## <a name="multi-vm-perspective-from-azure-monitor"></a>Multi-VM-perspectief van Azure Monitor

De functie prestaties van Azure Monitor biedt een overzicht van alle bewaakte Vm's die zijn geïmplementeerd in werk groepen in uw abonnementen of in uw omgeving. Voer de volgende stappen uit om toegang te krijgen vanaf Azure Monitor. 

1. Selecteer in de Azure Portal **monitor**. 
2. Kies **virtual machines** in het gedeelte **oplossingen** .
3. Selecteer het tabblad **Prestaties**.

![Top N lijst weergave van VM Insights-prestaties](media/vminsights-performance/vminsights-performance-aggview-01.png)

Als u meer dan één Log Analytics-werk ruimte hebt, kiest u in het tabblad **eerste N grafieken** de werk ruimte die is ingeschakeld met de oplossing van de **werkruimte** kiezer boven aan de pagina. De **groeps** kiezer retourneert abonnementen, resource groepen, [computer groepen](../logs/computer-groups.md)en virtuele-machine schaal sets van computers die zijn gerelateerd aan de geselecteerde werk ruimte, die u kunt gebruiken om de resultaten verder te filteren die in de grafieken op deze pagina en op de andere pagina's worden weer gegeven. Uw selectie is alleen van toepassing op de prestatie functie en de status of kaart wordt niet overgedragen.  

Standaard worden in de grafieken de afgelopen 24 uur weer gegeven. Met de **time Range** selector kunt u een query uitvoeren voor historische Peri Oden van Maxi maal 30 dagen om te laten zien hoe de prestaties in het verleden zijn bekeken.

De grafieken met vijf capaciteits gebruik die op de pagina worden weer gegeven, zijn:

* CPU-gebruik%: toont de vijf beste computers met het hoogste gemiddelde processor gebruik 
* Beschikbaar geheugen: hier worden de vijf meest gebruikte computers weer gegeven met de laagste gemiddelde hoeveelheid beschikbaar geheugen 
* Gebruikte logische schijf ruimte%: Hiermee worden de vijf meest gebruikte computers weer gegeven met de hoogste gemiddelde schijf ruimte die wordt gebruikt% op alle schijf volumes 
* Aantal verzonden bytes: toont de vijf meest voorkomende computers met het hoogste gemiddelde aan verzonden bytes 
* Aantal ontvangen bytes: toont de vijf meest voorkomende computers met het hoogste gemiddelde van ontvangen bytes 

Als u op het speld pictogram in de rechter bovenhoek van een van de vijf grafieken klikt, wordt de geselecteerde grafiek vastgemaakt aan het laatste Azure-dash board dat u het laatst hebt bekeken.  Vanuit het dash board kunt u het formaat en de positie van de grafiek wijzigen. Als u de grafiek in het dash board selecteert, wordt u omgeleid naar VM Insights en wordt het juiste bereik en de weer gave geladen.  

Als u op het pictogram aan de linkerkant van het speld pictogram op een van de vijf grafieken klikt, wordt de **bovenste N lijst** weergave geopend.  Hier ziet u het resource gebruik voor de metrische prestatie gegevens per afzonderlijke VM in een lijst weergave en welke machine het hoogst mogelijk maakt.  

![Top N lijst weergave voor een geselecteerde prestatie-metriek](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Wanneer u op de virtuele machine klikt, wordt het deel venster **Eigenschappen** aan de rechter kant uitgebreid om de eigenschappen van het geselecteerde item weer te geven, zoals systeem gegevens die zijn gerapporteerd door het besturings systeem, eigenschappen van de Azure VM, enzovoort. Wanneer u op een van de opties onder het gedeelte **snelle koppelingen** klikt, wordt u rechtstreeks vanuit de geselecteerde VM naar die functie geleid.  

![Eigenschappen deel venster voor virtuele machine](./media/vminsights-performance/vminsights-properties-pane-01.png)

Schakel over naar het tabblad **geaggregeerde grafieken** om de prestatie gegevens te bekijken die worden gefilterd op gemiddelde of percentiel waarden.  

![Statistische weer gave voor de prestaties van VM Insights](./media/vminsights-performance/vminsights-performance-aggview-02.png)

De volgende grafieken voor capaciteits gebruik zijn opgenomen:

* CPU-gebruik%: standaard wordt het gemiddelde en het bovenste 95e percentiel weer gegeven 
* Beschikbaar geheugen-standaard waarden met het gemiddelde, het bovenste vijfde en het tiende percentiel 
* Gebruikte logische schijf ruimte%: standaard waarden voor het gemiddelde en het 95e percentiel 
* Aantal verzonden bytes: standaard waarden die het gemiddelde aantal verzonden bytes weer geven 
* Aantal ontvangen bytes-standaard waarden voor ontvangen bytes

U kunt ook de granulariteit van de grafieken binnen het tijds bereik wijzigen door **AVG**, **min**, **Max**, **50e**, **negen** en **95e** te selecteren in de percentiel kiezer.

Als u het resource gebruik per afzonderlijke virtuele machine in een lijst weergave wilt weer geven en wilt zien welke machine met het hoogste gebruik wordt getrendd, selecteert u het tabblad **Top N-lijst** .  Op de pagina **eerste N lijst** ziet u de top 20 van de computers, gesorteerd op basis van het 95e percentiel voor het metrieke *CPU-gebruik%*.  U kunt meer machines weer geven door **extra belasting** te selecteren en de resultaten worden uitgevouwen om de meeste 500 machines weer te geven. 

>[!NOTE]
>De lijst kan niet meer dan 500 machines tegelijk weer geven.  
>

![Voor beeld van de eerste N lijst pagina](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Als u de resultaten wilt filteren op een specifieke virtuele machine in de lijst, voert u de computer naam in het tekstvak **zoeken op naam** in.  

Als u liever het gebruik van een andere prestatie metriek wilt weer geven, selecteert u in de vervolg keuzelijst **metriek** het **beschik bare geheugen**, de **logische schijf ruimte die wordt gebruikt%**, het **netwerk ontvangen bytes/s** of het **netwerk heeft bytes per seconde verzonden** , en de lijst bevat updates om het bereik van de metrische gegevens weer te geven.  

Als u een virtuele machine in de lijst selecteert, wordt het deel venster **Eigenschappen** aan de rechter kant van de pagina geopend. hier kunt u **prestatie Details** selecteren.  De detail pagina van de **virtuele machine** wordt geopend en er wordt een scope voor die VM weer gegeven, vergelijkbaar met de ervaring bij het openen van de prestaties van de Azure-generatie vanuit de VM.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Prestaties direct vanuit een Azure-VM weer geven

Voer de volgende stappen uit om rechtstreeks vanaf een virtuele machine toegang te krijgen.

1. Selecteer **virtual machines** In het Azure Portal. 
2. Kies een virtuele machine in de lijst en kies **inzichten** in de sectie **bewaking** .  
3. Selecteer het tabblad **Prestaties**. 

Deze pagina bevat niet alleen grafieken voor prestatiebelasting, maar ook een tabel die voor elke gedetecteerde logische schijf, de capaciteit, de belasting en het totale gemiddelde van elke meting laat zien.  

De volgende grafieken voor capaciteits gebruik zijn opgenomen:

* CPU-gebruik%: standaard wordt het gemiddelde en het bovenste 95e percentiel weer gegeven 
* Beschikbaar geheugen-standaard waarden met het gemiddelde, het bovenste vijfde en het tiende percentiel 
* Gebruikte logische schijf ruimte%: standaard waarden voor het gemiddelde en het 95e percentiel 
* IOPS voor logische schijven: standaard geeft het gemiddelde en het 95e percentiel weer
* Logische schijf, MB/s, standaard wordt het gemiddelde en het 95e percentiel weer gegeven
* Maximum aantal gebruikte logische schijven%: standaard waarden voor het gemiddelde en het 95e percentiel
* Aantal verzonden bytes: standaard waarden die het gemiddelde aantal verzonden bytes weer geven 
* Aantal ontvangen bytes-standaard waarden voor ontvangen bytes

Als u op het speld pictogram in de rechter bovenhoek van een van de grafieken klikt, wordt het geselecteerde diagram gespeld naar het laatste Azure-dash board dat u hebt bekeken. Vanuit het dash board kunt u het formaat en de positie van de grafiek wijzigen. Als u de grafiek in het dash board selecteert, wordt u omgeleid naar de weer gave van de prestatie gegevens voor de virtuele machine.  

![Prestaties van de VM-inzichten rechtstreeks vanuit de VM-weer gave](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Prestaties direct vanuit een Azure virtual machine-schaalset weer geven

Voer de volgende stappen uit om rechtstreeks toegang te krijgen tot de schaalset van een virtuele machine in Azure.

1. Selecteer in de Azure Portal **virtuele-machine schaal sets**.
2. Kies in de lijst een virtuele machine en klik in de sectie **bewaking** op **inzichten** om het tabblad **prestaties** weer te geven.

Op deze pagina wordt de weer gave Azure Monitor prestaties geladen, waarbij het bereik is ingesteld op de geselecteerde schaalset. Zo kunt u de eerste N instanties in de schaalset bekijken over de set bewaakte metrische gegevens, de statistische prestaties van de schaalset bekijken en de trends voor geselecteerde metrische gegevens in de afzonderlijke exemplaren van de schaalset bekijken. Als u een instantie in de lijst weergave selecteert, kunt u de kaart laden of naar een gedetailleerde weer gave van de prestaties voor die instantie navigeren.

Als u op het speld pictogram in de rechter bovenhoek van een van de grafieken klikt, wordt het geselecteerde diagram gespeld naar het laatste Azure-dash board dat u hebt bekeken. Vanuit het dash board kunt u het formaat en de positie van de grafiek wijzigen. Als u de grafiek in het dash board selecteert, wordt u omgeleid naar de weer gave van de prestatie gegevens voor de virtuele machine.  

![Prestaties van de VM-inzichten rechtstreeks vanuit de weer gave virtuele-machine schaal sets](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>U kunt ook een gedetailleerde weer gave van de prestaties voor een specifieke instantie openen vanuit de weer gave instanties voor uw schaalset. Navigeer naar **instanties** onder de sectie **instellingen** en kies **inzichten**.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het gebruik van [werkmappen](vminsights-workbooks.md) die zijn opgenomen in VM Insights om de prestaties en netwerk gegevens verder te analyseren.  

- Zie de [overzichts weergave van Vm's weer geven](vminsights-maps.md)voor meer informatie over gedetecteerde toepassings afhankelijkheden.
