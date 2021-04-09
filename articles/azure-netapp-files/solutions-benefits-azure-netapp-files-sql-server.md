---
title: Voor delen van het gebruik van Azure NetApp Files voor SQL Server-implementatie | Microsoft Docs
description: Toont een gedetailleerde prestatie voordelen voor kosten analyse over het gebruik van Azure NetApp Files voor SQL Server-implementatie.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863901"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Voor delen van het gebruik van Azure NetApp Files voor SQL Server-implementatie

Azure NetApp Files reduceert SQL Server total cost of ownership (TCO) in vergelijking met de oplossingen voor blok opslag.  Met blok opslag hebben virtuele machines limieten opgelegd voor I/O en band breedte voor schijf bewerkingen, worden limieten voor netwerk bandbreedte alleen toegepast op Azure NetApp Files.  Met andere woorden, er worden geen I/O-limieten op VM-niveau toegepast op Azure NetApp Files. Zonder deze I/O-limieten SQL Server die worden uitgevoerd op kleinere virtuele machines die zijn verbonden met Azure NetApp Files kunnen worden uitgevoerd en SQL Server op veel grotere virtuele machines worden uitgevoerd. Als u de schaal van een exemplaar wijzigt, worden de reken kosten beperkt tot 25% van de voormalige prijs code.  *U kunt reken kosten reduceren met Azure NetApp Files.*  

Reken kosten zijn echter klein vergeleken met SQL Server licentie kosten.  Microsoft SQL Server [licenties](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) zijn gekoppeld aan het aantal fysieke kernen. Als zodanig is een afnemende instantie grootte een nog grotere besparing op het besparen van software licenties. *U kunt de kosten voor software licenties verminderen met Azure NetApp Files.*

De kosten van de opslag zelf zijn variabel, afhankelijk van de werkelijke grootte van de data base. Ongeacht de opslag die is geselecteerd, is de capaciteit kosten, ongeacht of het een beheerde schijf of bestands share is.  Naarmate de omvang van de data base toeneemt en de opslag kosten toeneemt, draagt de opslag bij aan de totale kosten.  Als zodanig wordt de bevestiging als volgt aangepast: *u kunt SQL Server implementatie kosten verminderen met Azure NetApp files.* 

Dit artikel bevat een gedetailleerde kosten analyse en prestatie voordelen voor het gebruik van Azure NetApp Files voor SQL Server-implementatie. Het is niet alleen mogelijk dat kleinere instanties voldoende CPU hebben om de data base alleen te laten werken met blok keren voor grotere instanties. *in veel gevallen zijn de kleinere instanties nog meer presteert dan hun grotere, op schijven gebaseerde tegen Hangers vanwege Azure NetApp files.* 

## <a name="detailed-cost-analysis"></a>Gedetailleerde kostenanalyse 

In de twee sets met afbeeldingen in deze sectie wordt het TCO-voor beeld weer gegeven.  Het aantal en het type Managed disks, het Azure NetApp Files service niveau en de capaciteit voor elk scenario zijn geselecteerd om de beste prijs-capaciteits prestaties te krijgen.  Elke afbeelding bestaat uit gegroepeerde machines (D16 met Azure NetApp Files, vergeleken met D64 met beheerde schijf op voor beeld) en de prijzen voor elk type machine worden uitgesplitst.  

De eerste reeks grafische weer gave van de totale kosten van de oplossing met behulp van een TiB-data base grootte, vergeleken met de D16s_v3 met de D64, de D8 naar de D32 en de D4 op de D16. De geprojecteerde IOPs voor elke configuratie worden aangegeven door een groene of gele lijn en komt overeen met de rechter Y-as.

[![Afbeelding waarin de totale kosten van de oplossing worden weer gegeven met een grootte van 1 TIB-data base. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


De tweede set met afbeeldingen toont de totale kosten met behulp van een 50-TiB-data base. De vergelijkingen zijn anders dezelfde D16 vergeleken met Azure NetApp Files tegenover D64 met behulp van blok-voor beeld. 

[![Afbeelding waarin de totale kosten worden weer gegeven met een grootte van 50 TIB data base. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Prestaties en veel IT  

Om te zorgen voor de bewering over aanzienlijke kosten reductie zijn veel prestaties vereist: de grootste instanties in de algemene Azure-inventaris ondersteuning 80.000 schijf IOPS per voor beeld. Eén Azure NetApp Files volume kan 80.000 data base-IOPS behaalt, en instanties zoals de D16 kunnen dezelfde gegevens gebruiken. De D16, die normaal gesp roken geschikt is voor 25.600 schijf-IOPS, is 25% de grootte van de D64.  De D64s_v3 is geschikt voor 80.000 schijf-IOPS en biedt als zodanig een uitstekend vergelijkings punt op het hoogste niveau.

De D16s_v3 kan een Azure NetApp Files volume op 80.000-data base-IOPS. Zoals bewezen door het benchmarking-hulp programma voor SQL Storage Bench Mark (SSB), heeft het D16-exemplaar een werk belasting van 125% behaald dat groter is dan de hoeveelheid die in de D64-instantie aan de schijf is behaald.  Zie de sectie [test programma SSB](#ssb-testing-tool) voor meer informatie over het hulp programma.

Het gebruik van een TiB met een grootte van Maxi maal 1%, een update van 80% SQL Server werk belasting, de prestaties van de meeste exemplaren van de klasse D-instantie zijn gemeten; de meeste, niet alle, omdat de instanties D2 en D64 zelf zijn uitgesloten van testen. Het voormalige werd wegge laten omdat deze geen ondersteuning biedt voor versneld netwerken, en dat komt doordat het vergelijkings punt is. Raadpleeg de volgende grafiek voor meer informatie over de limieten van D4s_v3, respectievelijk D8s_v3, D16s_v3 en D32s_v3.  Testen van beheerde schijf opslag worden niet weer gegeven in de grafiek. Vergelijkings waarden worden direct opgehaald uit de [tabel limieten van Azure virtual machines](../virtual-machines/dv3-dsv3-series.md) voor het type D klasse-exemplaar.

Met Azure NetApp Files kan elk van de instanties in de klasse D voldoen aan de schijf prestatie mogelijkheden van exemplaren die twee keer groter zijn.  *U kunt de kosten voor software licenties aanzienlijk verminderen met Azure NetApp Files.*  

* Het CPU-gebruik van D4 om 75% komt overeen met de schijf mogelijkheden van de D16.  
    * De D16 is een frequentie limiet van 25.600 schijf-IOPS.  
* Het CPU-gebruik van D8 om 75% komt overeen met de schijf mogelijkheden van de D32.  
    * De D32 is een frequentie limiet van 51.200 schijf-IOPS.  
* Het CPU-gebruik van D16 om 55% komt overeen met de schijf mogelijkheden van de D64.  
    * De D64 is een frequentie limiet van 80.000 schijf-IOPS.  
* De D32 met een CPU-gebruik van 15% komt ook overeen met de schijf mogelijkheden van de D64.  
    * De D64 zoals hierboven is vermeld, is de frequentie beperkt op 80.000 schijf IOPS.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>S3B CPU-limieten testen: prestaties versus verwerkings kracht

Het volgende diagram geeft een overzicht van de test voor S3B CPU-limieten:

![Diagram waarin het gemiddelde CPU-percentage voor de SQL Server met één exemplaar wordt weer gegeven via Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

Schaal baarheid is slechts een onderdeel van het verhaal. Het andere deel is latentie.  Het is één ding voor kleinere virtuele machines, zodat er veel hogere I/O-tarieven kunnen worden aangestuurd. Dit is een andere manier om dit te doen met een lage latentie van één cijfer, zoals hieronder wordt weer gegeven.  

* De D4 zagen 26.000 IOPS op basis van Azure NetApp Files bij een latentie van 2,3 MS.  
* De D8 zagen 51.000 IOPS op basis van Azure NetApp Files bij een latentie van 2,0 MS.  
* De D16 zagen 88.000 IOPS op basis van Azure NetApp Files bij een latentie van 2,8 ms.
* De D32 zagen 80.000 IOPS op basis van Azure NetApp Files bij een latentie van 2,4 MS.  

### <a name="s3b-per-instance-type-latency-results"></a>Latentie resultaten van S3B per instantie type

In het volgende diagram ziet u de latentie voor SQL Server met één exemplaar over Azure NetApp Files:

![Diagram waarin de latentie voor de SQL Server van één exemplaar over Azure NetApp Files wordt weer gegeven.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>Test programma voor SSB 
 
Het hulp programma [TPC-E](http://www.tpc.org/tpce/) benchmarking, per ontwerp, *berekenings Compute* in plaats van *opslag*. De test resultaten die in deze sectie worden weer gegeven, zijn gebaseerd op een stress test programma met de naam SQL Storage Bench Mark (SSB).  De SQL Server Storage-Bench Mark kan een enorme schaal bare SQL-uitvoering bieden op basis van een SQL Server-Data Base om een OLTP-werk belasting te simuleren, vergelijkbaar met het [SLOB2 Oracle benchmarking tool](https://kevinclosson.net/slob/). 

Het hulp programma SSB genereert een op een werk last gebaseerde, gewerkte workload die de genoemde verklaringen rechtstreeks afgeeft aan de SQL Server Data Base die wordt uitgevoerd op de virtuele machine van Azure.  Voor dit project worden de bewerkings werkbelasting van SSB van 1 tot 100 SQL Server gebruikers, met 10 of 12 tussenliggende punten op 15 minuten per aantal gebruikers.  Alle prestatie gegevens van deze uitvoeringen zijn afkomstig uit het oogpunt van perfmon, voor Herhaal baarheid van SSB, drie keer per scenario. 

De tests zelf zijn geconfigureerd als 80% SELECT en 20% UPDATE-instructie, waardoor 90% wille keurig gelezen.  De data base zelf, welke SSB is gemaakt, was 1000 GB groot. Het bestaat uit 15 gebruikers tabellen en 9.000.000 rijen per gebruikers tabel en 8192 bytes per rij. 

De SSB-Bench Mark is een open source-hulp programma.  Het is gratis beschikbaar op de [pagina SQL-github Bench Mark-referentie](https://github.com/NetApp/SQL_Storage_Benchmark.git).  


## <a name="in-summary"></a>In samen vatting  

Met Azure NetApp Files kunt u de prestaties van SQL Server verbeteren en de total cost of ownership aanzienlijk verminderen. 

## <a name="next-steps"></a>Volgende stappen

* [Een SMB-volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Oplossings architecturen met behulp van Azure NetApp Files-SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

