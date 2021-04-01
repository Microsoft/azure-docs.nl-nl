---
title: Ontwerp patronen voor Azure Storage-tabellen | Microsoft Docs
description: Bekijk ontwerp patronen die geschikt zijn voor gebruik met Table service oplossingen in Azure. Los problemen en handels transacties op die in andere artikelen worden besproken.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 2eb109078728b8a9070b3991733450c1da790d9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879592"
---
# <a name="table-design-patterns"></a>Tabelontwerppatronen
In dit artikel worden enkele patronen beschreven die geschikt zijn voor gebruik met Table service oplossingen. U zult ook zien hoe u een aantal van de problemen en commerciële benadert die worden besproken in andere ontwerp artikelen van Table-opslag. In het volgende diagram ziet u een overzicht van de relaties tussen de verschillende patronen:  

![gerelateerde gegevens opzoeken](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


In het patroon schema hierboven ziet u een aantal relaties tussen patronen (blauw) en anti patronen (oranje) die in deze hand leiding worden beschreven. Er zijn veel andere patronen die moeten worden overwogen. Een van de belangrijkste scenario's voor Table service is bijvoorbeeld het gebruik van het [patroon gerealiseerde weer gave](/previous-versions/msp-n-p/dn589782(v=pandp.10)) van het [CQRS-patroon (Command query](/previous-versions/msp-n-p/jj554200(v=pandp.10)) promaterialing).  

## <a name="intra-partition-secondary-index-pattern"></a>Secundair index patroon voor de intra partitie
Sla meerdere exemplaren van elke entiteit op met behulp van verschillende **RowKey** -waarden (in dezelfde partitie) om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden. Updates tussen kopieën kunnen consistent worden gehouden met behulp van EGTs.  

### <a name="context-and-problem"></a>Context en probleem
De Table service indexeert automatisch entiteiten met behulp van de waarden **PartitionKey** en **RowKey** . Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van deze waarden. Met de tabel structuur die hieronder wordt weer gegeven, kan een client toepassing bijvoorbeeld een punt query gebruiken om een afzonderlijke werknemers entiteit op te halen met behulp van de afdelings naam en de werk nemer-ID (de waarden **PartitionKey** en **RowKey** ). Een client kan ook entiteiten ophalen die zijn gesorteerd op werk nemer-ID binnen elke afdeling.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Als u ook een werknemers entiteit wilt kunnen vinden op basis van de waarde van een andere eigenschap, zoals e-mail adres, moet u een minder efficiënte partitie Scan gebruiken om een overeenkomst te vinden. Dit komt doordat de Table service geen secundaire indexen biedt. Daarnaast is er geen optie voor het aanvragen van een lijst met werk nemers die in een andere volg orde zijn gesorteerd dan **RowKey** order.  

### <a name="solution"></a>Oplossing
Om het ontbreken van secundaire indexen te omzeilen, kunt u meerdere exemplaren van elke entiteit met elk exemplaar opslaan met behulp van een andere **RowKey** -waarde. Als u een entiteit met de hieronder weer gegeven structuren opslaat, kunt u op efficiënte wijze werknemers entiteiten ophalen op basis van het e-mail adres of de werk nemer-ID. Met de voorvoegsel waarden voor de **RowKey**, empid_ en email_ kunt u een query uitvoeren voor één werk nemer of een bereik van werk nemers met behulp van een reeks e-mail adressen of werk nemer-id's.  

![Werknemers entiteiten](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

De volgende twee filter criteria (één op te geven op basis van de werk nemer-ID en één op te zoeken op basis van een e-mail adres) beide opgeven punt query's:  

* $filter = (PartitionKey EQ ' Sales ') en (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') en (RowKey EQ ' email_jonesj@contoso.com ')  

Als u een query uitvoert voor een bereik van werk nemers, kunt u een bereik opgeven dat is gesorteerd in de volg orde van de werk nemer-ID of een bereik dat in de e-mail adres volgorde is gesorteerd door een query uit te sturen naar entiteiten met het juiste voor voegsel in de **RowKey**.  

* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een werk nemer-ID in het bereik 000100 tot 000199 gebruikt u: $filter = (PartitionKey EQ ' Sales ') en (RowKey ge ' empid_000100 ') en (RowKey Le ' empid_000199 ')  
* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een e-mail adres dat begint met de letter ' a ' gebruiken: $filter = (PartitionKey EQ ' Sales ') en (RowKey ge ' email_a ') en (RowKey lt ' email_b ')  
  
  De filter syntaxis die in de bovenstaande voor beelden wordt gebruikt, is afkomstig uit de Table service REST API, Zie [query-entiteiten](/rest/api/storageservices/Query-Entities)voor meer informatie.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Table Storage is relatief goed koop om te gebruiken, dus de kosten overhead van het opslaan van dubbele gegevens mag geen grote bezorgdheid zijn. U moet de kosten van uw ontwerp echter altijd evalueren op basis van uw verwachte opslag vereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die door uw client toepassing worden uitgevoerd.  
* Omdat de secundaire index entiteiten worden opgeslagen in dezelfde partitie als de oorspronkelijke entiteiten, moet u ervoor zorgen dat u de schaalbaarheids doelen voor een afzonderlijke partitie niet overschrijdt.  
* U kunt ervoor zorgen dat uw dubbele entiteiten consistent blijven door gebruik te maken van EGTs om de twee exemplaren van de entiteit atomisch bij te werken. Dit betekent dat u alle kopieën van een entiteit in dezelfde partitie moet opslaan. Zie de sectie [trans acties voor entiteits groepen gebruiken](table-storage-design.md#entity-group-transactions)voor meer informatie.  
* De waarde die wordt gebruikt voor de **RowKey** moet uniek zijn voor elke entiteit. Overweeg het gebruik van samengestelde sleutel waarden.  
* Met de opvullings numerieke waarden in de **RowKey** (bijvoorbeeld de werk nemer-id 000223) kunt u de sorteer-en filter functie op basis van de boven-en ondergrenzen sorteren.  
* U hoeft niet noodzakelijkerwijs alle eigenschappen van uw entiteit te dupliceren. Als de query's voor het opzoeken van de entiteiten die gebruikmaken van het e-mail adres in de **RowKey** nooit de leeftijd van de werk nemer nodig hebben, kunnen deze entiteiten de volgende structuur hebben:

   ![Entiteits structuur werk nemer](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Het is doorgaans beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens kunt ophalen die u nodig hebt met één query, dan om één query te gebruiken om een entiteit te zoeken en een andere om de vereiste gegevens op te zoeken.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer uw client toepassing entiteiten moet ophalen met behulp van verschillende sleutels, wanneer uw client entiteiten in verschillende sorteer volgorden moet ophalen en waar u elke entiteit kunt identificeren met behulp van verschillende unieke waarden. U moet er echter zeker van zijn dat u de schaal baarheids limieten van de partitie niet overschrijdt wanneer u entiteiten lookups uitvoert met behulp van de verschillende **RowKey** -waarden.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Secundair index patroon tussen partities](#inter-partition-secondary-index-pattern)
* [Samengesteld sleutel patroon](#compound-key-pattern)
* Trans acties van entiteits groep
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Secundair index patroon tussen partities
Sla meerdere exemplaren van elke entiteit op met behulp van verschillende **RowKey** -waarden in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden.  

### <a name="context-and-problem"></a>Context en probleem
De Table service indexeert automatisch entiteiten met behulp van de waarden **PartitionKey** en **RowKey** . Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van deze waarden. Met de tabel structuur die hieronder wordt weer gegeven, kan een client toepassing bijvoorbeeld een punt query gebruiken om een afzonderlijke werknemers entiteit op te halen met behulp van de afdelings naam en de werk nemer-ID (de waarden **PartitionKey** en **RowKey** ). Een client kan ook entiteiten ophalen die zijn gesorteerd op werk nemer-ID binnen elke afdeling.  

![Werknemers-id](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Als u ook een werknemers entiteit wilt kunnen vinden op basis van de waarde van een andere eigenschap, zoals e-mail adres, moet u een minder efficiënte partitie Scan gebruiken om een overeenkomst te vinden. Dit komt doordat de Table service geen secundaire indexen biedt. Daarnaast is er geen optie voor het aanvragen van een lijst met werk nemers die in een andere volg orde zijn gesorteerd dan **RowKey** order.  

U verwacht een groot aantal trans acties voor deze entiteiten en wilt het risico van de Table service beperking van uw client tot een minimum beperken.  

### <a name="solution"></a>Oplossing
Om het ontbreken van secundaire indexen te omzeilen, kunt u meerdere exemplaren van elke entiteit met elk exemplaar opslaan met behulp van verschillende **PartitionKey** -en **RowKey** -waarden. Als u een entiteit met de hieronder weer gegeven structuren opslaat, kunt u op efficiënte wijze werknemers entiteiten ophalen op basis van het e-mail adres of de werk nemer-ID. Met de voorvoegsel waarden voor de **PartitionKey**, empid_ en email_ kunt u bepalen welke index u wilt gebruiken voor een query.  

![Primaire index en secundaire index](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


De volgende twee filter criteria (één op te geven op basis van de werk nemer-ID en één op te zoeken op basis van een e-mail adres) beide opgeven punt query's:  

* $filter = (PartitionKey EQ ' empid_Sales ') en (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') en (RowKey EQ ' jonesj@contoso.com ')  

Als u een query uitvoert voor een bereik van werk nemers, kunt u een bereik opgeven dat is gesorteerd in de volg orde van de werk nemer-ID of een bereik dat in de e-mail adres volgorde is gesorteerd door een query uit te sturen naar entiteiten met het juiste voor voegsel in de **RowKey**.  

* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een werk nemer-ID in het bereik **000100** tot **000199** , gesorteerd op werk nemer-id order gebruik: $filter = (PartitionKey EQ ' empid_Sales ') en (RowKey ge 000100 ') en (RowKey Le ' 000199 ')  
* Als u wilt zoeken naar alle werk nemers van de afdeling verkoop met een e-mail adres dat begint met ' a ' gesorteerd op e-mail adres order gebruiken: $filter = (PartitionKey EQ ' email_Sales ') en (RowKey ge ' a ') en (RowKey lt ' b ')  

De filter syntaxis die in de bovenstaande voor beelden wordt gebruikt, is afkomstig uit de Table service REST API, Zie [query-entiteiten](/rest/api/storageservices/Query-Entities)voor meer informatie.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U kunt ervoor zorgen dat uw dubbele entiteiten uiteindelijk consistent zijn met elkaar met behulp van het [patroon voor uiteindelijk consistente trans acties](#eventually-consistent-transactions-pattern) voor het onderhouden van de primaire en secundaire index entiteiten.  
* Table Storage is relatief goed koop om te gebruiken, dus de kosten overhead van het opslaan van dubbele gegevens mag geen grote bezorgdheid zijn. U moet de kosten van uw ontwerp echter altijd evalueren op basis van uw verwachte opslag vereisten en alleen dubbele entiteiten toevoegen ter ondersteuning van de query's die door uw client toepassing worden uitgevoerd.  
* De waarde die wordt gebruikt voor de **RowKey** moet uniek zijn voor elke entiteit. Overweeg het gebruik van samengestelde sleutel waarden.  
* Met de opvullings numerieke waarden in de **RowKey** (bijvoorbeeld de werk nemer-id 000223) kunt u de sorteer-en filter functie op basis van de boven-en ondergrenzen sorteren.  
* U hoeft niet noodzakelijkerwijs alle eigenschappen van uw entiteit te dupliceren. Als de query's voor het opzoeken van de entiteiten die gebruikmaken van het e-mail adres in de **RowKey** nooit de leeftijd van de werk nemer nodig hebben, kunnen deze entiteiten de volgende structuur hebben:
  
   ![Werknemers entiteit (secundaire index)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Het is doorgaans beter om dubbele gegevens op te slaan en ervoor te zorgen dat u alle gegevens kunt ophalen die u nodig hebt met één query dan om één query te gebruiken om een entiteit te zoeken met behulp van de secundaire index en een andere om de vereiste gegevens in de primaire index op te zoeken.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer uw client toepassing entiteiten moet ophalen met behulp van verschillende sleutels, wanneer uw client entiteiten in verschillende sorteer volgorden moet ophalen en waar u elke entiteit kunt identificeren met behulp van verschillende unieke waarden. Gebruik dit patroon wanneer u wilt voor komen dat de schaal limieten voor partities worden overschreden wanneer u entiteits lookups uitvoert met behulp van de verschillende **RowKey** -waarden.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* [Secundair index patroon voor de intra partitie](#intra-partition-secondary-index-pattern)  
* [Samengesteld sleutel patroon](#compound-key-pattern)  
* Trans acties van entiteits groep  
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Patroon uiteindelijk consistent trans acties
Schakel uiteindelijk consistent gedrag in voor de partitie grenzen of de grenzen van het opslag systeem met behulp van Azure-wacht rijen.  

### <a name="context-and-problem"></a>Context en probleem
EGTs schakelt atomische trans acties in voor meerdere entiteiten die dezelfde partitie sleutel delen. Om redenen van prestaties en schaal baarheid kunt u ervoor kiezen om entiteiten op te slaan die consistentie vereisten hebben in afzonderlijke partities of in een afzonderlijk opslag systeem: in dit scenario kunt u EGTs niet gebruiken om consistentie te hand haven. U hebt bijvoorbeeld een vereiste voor het onderhouden van mogelijke consistentie tussen:  

* Entiteiten die zijn opgeslagen in twee verschillende partities in dezelfde tabel, in verschillende tabellen of in verschillende opslag accounts.  
* Een entiteit die is opgeslagen in de Table service en een blob die is opgeslagen in de Blob service.  
* Een entiteit die is opgeslagen in de Table service en een bestand in een bestands systeem.  
* Een entiteit die is opgeslagen in de Table service nog is geïndexeerd met de Azure Cognitive Search-service.  

### <a name="solution"></a>Oplossing
Met behulp van Azure queues kunt u een oplossing implementeren die uiteindelijke consistentie biedt in twee of meer partities of opslag systemen.
Om deze aanpak te illustreren, moet u ervan uitgaan dat u een vereiste hebt om oude werknemers entiteiten te archiveren. Oude werknemers entiteiten worden zelden gequeryeerd en moeten worden uitgesloten van activiteiten die worden uitgevoerd op huidige werk nemers. U kunt deze vereiste alleen implementeren als u actieve werk nemers in de **huidige** tabel en de oude werk nemers in de **Archief** tabel opslaat. Voor het archiveren van een werk nemer moet u de entiteit uit de **huidige** tabel verwijderen en de entiteit toevoegen aan de **Archief** tabel, maar u kunt geen EGT gebruiken om deze twee bewerkingen uit te voeren. Om het risico te voor komen dat een entiteit in beide of geen van beide tabellen wordt weer gegeven, moet de archief bewerking uiteindelijk consistent zijn. In het volgende sequentie diagram ziet u een overzicht van de stappen in deze bewerking. Meer gedetailleerde informatie over uitzonderings paden vindt u in de volgende tekst.  

![Oplossing voor Azure-wacht rijen](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Een client initieert de archief bewerking door een bericht in een Azure-wachtrij te plaatsen, in dit voor beeld om werk nemers te archiveren #456. Een werk rollen pollt de wachtrij voor nieuwe berichten; Wanneer er een wordt gevonden, wordt het bericht gelezen en wordt er een verborgen kopie in de wachtrij geplaatst. Met de rol werk rollen volgende wordt een kopie van de entiteit opgehaald uit de **huidige** tabel, wordt een kopie ingevoegd in de **Archief** tabel en wordt vervolgens het origineel uit de **huidige** tabel verwijderd. Ten slotte, als er geen fouten zijn opgetreden uit de vorige stappen, verwijdert de rol van de werk nemer het verborgen bericht uit de wachtrij.  

In dit voor beeld voegt stap 4 de werk nemer in de **Archief** tabel in. De werk nemer kan worden toegevoegd aan een BLOB in de Blob service of een bestand in een bestands systeem.  

### <a name="recovering-from-failures"></a>Herstellen van fouten
Het is belang rijk dat de bewerkingen in stap **4** en **5** moeten worden *idempotent* voor het geval de werk rollen de archief bewerking opnieuw moeten starten. Als u de Table service gebruikt, moet u voor stap **4** de bewerking ' invoegen of vervangen ' gebruiken. voor stap **5** moet u een bewerking delete if exists gebruiken in de client bibliotheek die u gebruikt. Als u een ander opslag systeem gebruikt, moet u een geschikte idempotent-bewerking gebruiken.  

Als de rol werk nemer nooit stap **6** voltooit, wordt na een time-out het bericht opnieuw in de wachtrij geplaatst om de werk rollen opnieuw te kunnen verwerken. De rol van de werk nemer kan controleren hoe vaak een bericht in de wachtrij is gelezen en, indien nodig, een ' verontreinigd ' bericht voor onderzoek worden verzonden door het naar een afzonderlijke wachtrij te verzenden. Zie [berichten ophalen](/rest/api/storageservices/Get-Messages)voor meer informatie over het lezen van wachtrij berichten en het controleren van het aantal in de wachtrij.  

Sommige fouten van de tabel-en wachtrij Services zijn tijdelijke fouten, en de client toepassing moet de juiste pogings logica voor het uitvoeren van deze taken bevatten.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing biedt geen transactie isolatie. Een client kan bijvoorbeeld de **huidige** en **Archief** tabellen lezen wanneer de rol van werk nemers tussen stap **4** en **5** was en een inconsistente weer gave van de gegevens te zien krijgt. De gegevens zullen uiteindelijk consistent zijn.  
* U moet er zeker van zijn dat stap 4 en 5 idempotent zijn om te zorgen voor een uiteindelijke consistentie.  
* U kunt de oplossing schalen door meerdere wacht rijen en worker-instanties te gebruiken.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u de uiteindelijke consistentie tussen entiteiten die in verschillende partities of tabellen bestaan, wilt garanderen. U kunt dit patroon uitbreiden om te zorgen voor mogelijke consistentie voor bewerkingen in het Table service en de Blob service en andere gegevens bronnen die niet Azure Storage zijn, zoals data base of het bestands systeem.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* Trans acties van entiteits groep  
* [Samen voegen of vervangen](#merge-or-replace)  

> [!NOTE]
> Als de transactie isolatie belang rijk is voor uw oplossing, kunt u het beste uw tabellen opnieuw ontwerpen zodat u EGTs kunt gebruiken.  
> 
> 

## <a name="index-entities-pattern"></a>Patroon van index entiteiten
Behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  

### <a name="context-and-problem"></a>Context en probleem
De Table service indexeert automatisch entiteiten met behulp van de waarden **PartitionKey** en **RowKey** . Hiermee kan een client toepassing een entiteit efficiënt ophalen met behulp van een Point-query. Met de tabel structuur die hieronder wordt weer gegeven, kan een client toepassing bijvoorbeeld een afzonderlijke werknemers entiteit ophalen met behulp van de afdelings naam en de werk nemer-ID (de **PartitionKey** en **RowKey**).  

![Entiteit werk nemer](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Als u ook een lijst met werknemers entiteiten wilt kunnen ophalen op basis van de waarde van een andere niet-unieke eigenschap, zoals de achternaam, moet u een minder efficiënte partitie Scan gebruiken om overeenkomsten te vinden in plaats van een index te gebruiken om deze direct te zoeken. Dit komt doordat de Table service geen secundaire indexen biedt.  

### <a name="solution"></a>Oplossing
Als u zoeken op achternaam wilt inschakelen met de entiteits structuur die hierboven wordt weer gegeven, moet u lijsten met werk nemer-Id's onderhouden. Als u de entiteiten van werk nemers met een bepaalde achternaam wilt ophalen, bijvoorbeeld Jansen, moet u eerst de lijst met werk nemer-Id's voor werk nemers met Jansen als achternaam vinden en vervolgens die entiteiten van de werk nemer ophalen. Er zijn drie belang rijke opties voor het opslaan van de lijsten met werk nemer-Id's:  

* Gebruik Blob Storage.  
* Maak index entiteiten in dezelfde partitie als de entiteiten van de werk nemer.  
* Maak index entiteiten in een afzonderlijke partitie of tabel.  

<u>Optie #1: Blob Storage gebruiken</u>  

Voor de eerste optie maakt u een BLOB voor elke unieke achternaam en slaat u in elke Blob een lijst op met de waarden van de **PartitionKey** (Department) en **RowKey** (werk nemer-id) voor werk nemers die deze achternaam hebben. Wanneer u een werk nemer toevoegt of verwijdert, moet u ervoor zorgen dat de inhoud van de relevante BLOB uiteindelijk consistent is met de entiteiten van de werk nemer.  

<u>Optie #2:</u> Index entiteiten in dezelfde partitie maken  

Voor de tweede optie gebruikt u index entiteiten waarin de volgende gegevens zijn opgeslagen:  

![Entiteit van werk nemer-index](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

De eigenschap **EmployeeIDs** bevat een lijst met werk nemer-id's voor werk nemers met de achternaam die is opgeslagen in de **RowKey**.  

De volgende stappen beschrijven het proces dat u moet volgen wanneer u een nieuwe werk nemer toevoegt als u de tweede optie gebruikt. In dit voor beeld voegen we een werk nemer met de ID 000152 en een achternaam Jansen toe op de verkoop afdeling:  

1. Haal de index entiteit op met de **PartitionKey** -waarde "Sales" en de **RowKey** -waarde "Jansen". Sla de ETag van deze entiteit op die u in stap 2 wilt gebruiken.  
2. Maak een trans actie voor een entiteits groep (dat wil zeggen een batch bewerking) die de nieuwe werknemers entiteit (**PartitionKey** waarde "Sales" en **RowKey** value "000152") invoegt en de index entiteit (**PartitionKey** waarde "Sales" en **RowKey** value Jansen ") BIJwerkt door de nieuwe werknemers-id toe te voegen aan de lijst in het veld EmployeeIDs. Zie trans acties voor entiteits groepen voor meer informatie over entiteits groeps transacties.  
3. Als de trans actie van de entiteits groep mislukt als gevolg van een optimistische gelijktijdigheids fout (iemand anders heeft zojuist de index entiteit gewijzigd), moet u opnieuw beginnen met stap 1.  

U kunt een soort gelijke aanpak gebruiken om een werk nemer te verwijderen als u de tweede optie gebruikt. Het wijzigen van de achternaam van een werk nemer is iets ingewik kelder omdat u de trans actie van een entiteits groep moet uitvoeren die drie entiteiten bijwerkt: de werknemers entiteit, de index entiteit voor de oude achternaam en de index entiteit voor de nieuwe achternaam. U moet elke entiteit ophalen voordat u wijzigingen aanbrengt om de ETag-waarden op te halen die u vervolgens kunt gebruiken om de updates uit te voeren met optimistische gelijktijdigheid.  

De volgende stappen geven een overzicht van het proces dat u moet volgen wanneer u alle werk nemers met een bepaalde achternaam in een afdeling moet opzoeken als u de tweede optie gebruikt. In dit voor beeld worden alle werk nemers met de naam Jansen op de verkoop afdeling opzoeken:  

1. Haal de index entiteit op met de **PartitionKey** -waarde "Sales" en de **RowKey** -waarde "Jansen".  
2. De lijst met werk nemer-Id's parseren in het veld EmployeeIDs.  
3. Als u meer informatie nodig hebt over elk van deze werk nemers (zoals hun e-mail adressen), haalt u elk van de werk nemers op met behulp van de **PartitionKey** -waarde "Sales" en **RowKey** -waarden uit de lijst met werk nemers die u in stap 2 hebt verkregen.  

<u>Optie #3:</u> Index entiteiten in een afzonderlijke partitie of tabel maken  

Voor de derde optie gebruikt u index entiteiten waarin de volgende gegevens worden opgeslagen:  

![Entiteit van werk nemer-index in een afzonderlijke partitie](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


De eigenschap **EmployeeIDs** bevat een lijst met werk nemer-id's voor werk nemers met de achternaam die is opgeslagen in de **RowKey**.  

Met de derde optie kunt u EGTs niet gebruiken om consistentie te behouden, omdat de index entiteiten zich in een afzonderlijke partitie van de entiteiten van de werk nemer bevinden. Zorg ervoor dat de index entiteiten uiteindelijk consistent zijn met de entiteiten van de werk nemer.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Deze oplossing vereist ten minste twee query's voor het ophalen van overeenkomende entiteiten: een om de index entiteiten te doorzoeken om de lijst met **RowKey** -waarden te verkrijgen, en vervolgens query's om elke entiteit in de lijst op te halen.  
* Als een afzonderlijke entiteit een maximum grootte van 1 MB heeft, wordt met de optie #2 en optie #3 in de oplossing ervan uitgegaan dat de lijst met werk nemer-Id's voor een gegeven achternaam nooit groter is dan 1 MB. Als de lijst met werk nemer-Id's waarschijnlijk groter dan 1 MB groot is, gebruikt u de optie #1 en slaat u de index gegevens op in Blob Storage.  
* Als u de optie #2 gebruikt (met behulp van EGTs om werk nemers toe te voegen en te verwijderen, en de achternaam van een werk nemer te wijzigen), moet u evalueren of het volume van de trans acties de schaalbaarheids limieten in een bepaalde partitie zal belasten. Als dit het geval is, moet u rekening houden met een uiteindelijk consistente oplossing (optie #1 of optie #3) die wacht rijen gebruikt voor het verwerken van de update aanvragen en u in staat stelt uw index entiteiten op te slaan in een afzonderlijke partitie van de entiteiten van de werk nemer.  
* In de optie #2 in deze oplossing wordt ervan uitgegaan dat u wilt zoeken op achternaam binnen een afdeling: u wilt bijvoorbeeld een lijst met werk nemers ophalen met een achternaam Jansen op de verkoop afdeling. Als u alle werk nemers met een achternaam Jansen wilt kunnen opzoeken in de hele organisatie, gebruikt u een van de opties #1 of optie #3.
* U kunt een op een wachtrij gebaseerde oplossing implementeren die uiteindelijke consistentie biedt (Zie het [patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern) voor meer informatie).  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een set entiteiten wilt opzoeken die alle een gemeen schappelijke eigenschaps waarde delen, zoals alle werk nemers met de achternaam Jansen.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* Trans acties van entiteits groep  
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Het denormalisatie patroon
Gerelateerde gegevens samen voegen in één entiteit zodat u alle gegevens kunt ophalen die u nodig hebt met één punt query.  

### <a name="context-and-problem"></a>Context en probleem
In een relationele data base normaliseren we doorgaans gegevens om duplicatie te verwijderen, wat resulteert in query's die gegevens uit meerdere tabellen ophalen. Als u uw gegevens in azure-tabellen normaliseert, moet u meerdere round trips van de client naar de server maken om de gerelateerde gegevens op te halen. Met de tabel structuur die hieronder wordt weer gegeven, hebt u bijvoorbeeld twee retour retouren nodig om de details van een afdeling op te halen: een voor het ophalen van de afdelings entiteit die de Manager-ID bevat en vervolgens een andere aanvraag om de details van de Manager op te halen in een entiteit van een werk nemer.  

![Afdelings-en werknemers entiteit](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Oplossing
In plaats van de gegevens in twee afzonderlijke entiteiten op te slaan, moet u de gegevens denormaliseren en een kopie van de details van de manager in de entiteit afdeling bewaren. Bijvoorbeeld:  

![Afdelings entiteit](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Als afdelings entiteiten met deze eigenschappen zijn opgeslagen, kunt u nu alle Details ophalen die u nodig hebt over een afdeling met behulp van een Point-query.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Er zijn kosten overhead gekoppeld aan het opslaan van een aantal gegevens twee maal. Het prestatie voordeel (als gevolg van minder aanvragen voor de opslag service) verlaagt doorgaans de marginale toename van de opslag kosten (en deze kosten worden deels gecompenseerd door een verlaging van het aantal trans acties dat u nodig hebt om de details van een afdeling op te halen).  
* U moet de consistentie van de twee entiteiten onderhouden die informatie over managers opslaan. U kunt het consistentie probleem afhandelen met behulp van EGTs om meerdere entiteiten in één atomische trans actie bij te werken. in dit geval worden de afdelings entiteit en de werknemers entiteit voor de afdelings manager in dezelfde partitie opgeslagen.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u vaak verwante informatie wilt opzoeken. Dit patroon vermindert het aantal query's dat door de client moet worden gemaakt om de benodigde gegevens op te halen.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* Trans acties van entiteits groep  
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Samengesteld sleutel patroon
Gebruik samengestelde **RowKey** -waarden om een client in staat te stellen gerelateerde gegevens op te zoeken met een single point-query.  

### <a name="context-and-problem"></a>Context en probleem
In een relationele data base is het natuurlijk om samen voegingen in query's te gebruiken om gerelateerde gegevens items te retour neren naar de client in één query. U kunt bijvoorbeeld de werk nemer-ID gebruiken om een lijst op te zoeken met gerelateerde entiteiten die de prestaties en de beoordelings gegevens voor die werk nemer bevatten.  

Stel dat u werknemers entiteiten opslaat in de Table service met behulp van de volgende structuur:  

![Scherm afbeelding die laat zien hoe u werknemers entiteiten kunt opslaan in de Table service.](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

U moet ook historische gegevens over beoordelingen en prestaties opslaan voor elk jaar dat de werk nemer heeft gewerkt aan uw organisatie en u deze gegevens per jaar moet kunnen openen. U kunt ook een andere tabel maken waarin entiteiten worden opgeslagen met de volgende structuur:  

![Entiteits structuur van alternatieve werk nemer](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Met deze aanpak kunt u ervoor kiezen om bepaalde gegevens (zoals de voor naam en achternaam) in de nieuwe entiteit te dupliceren, zodat u uw gegevens kunt ophalen met één aanvraag. U kunt echter geen sterke consistentie behouden omdat u geen EGT kunt gebruiken om de twee entiteiten atomisch bij te werken.  

### <a name="solution"></a>Oplossing
Sla een nieuw entiteits type op in de oorspronkelijke tabel met behulp van entiteiten met de volgende structuur:  

![Oplossing voor entiteits structuur van werk nemer](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

U ziet dat de **RowKey** nu een samengestelde sleutel is die bestaat uit de werk nemer-id en het jaar van de beoordelings gegevens waarmee u de prestaties van de werk nemer kunt ophalen en gegevens met één enkele aanvraag voor één entiteit kan controleren.  

In het volgende voor beeld wordt beschreven hoe u alle controle gegevens voor een bepaalde werk nemer kunt ophalen (zoals werk nemer 000123 van de afdeling verkoop):  

$filter = (PartitionKey EQ ' Sales ') en (RowKey ge ' empid_000123 ') en (RowKey lt ' 000123_2012 ') &$select = RowKey, Manager-classificatie, peer rating, opmerkingen  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet een geschikt scheidings teken gebruiken waarmee u de **RowKey** -waarde eenvoudig kunt parseren: bijvoorbeeld **000123_2012**.  
* U slaat deze entiteit ook op in dezelfde partitie als andere entiteiten die gerelateerde gegevens bevatten voor dezelfde werk nemer. Dit betekent dat u EGTs kunt gebruiken om sterke consistentie te hand haven.
* U kunt overwegen hoe vaak u de gegevens wilt opvragen om te bepalen of dit patroon geschikt is.  Bijvoorbeeld, als u de gegevens over de controle en de belangrijkste gegevens van werk nemers vaker opent, moet u deze als afzonderlijke entiteiten houden.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een of meer gerelateerde entiteiten wilt opslaan die u regel matig doorzoekt.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* Trans acties van entiteits groep  
* [Werken met heterogene entiteits typen](#working-with-heterogeneous-entity-types)  
* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Eind patroon van logboek
Haal de *n* -entiteiten die het laatst zijn toegevoegd aan een partitie op met behulp van een **RowKey** -waarde die wordt gesorteerd in omgekeerde datum-en tijd volgorde.  

### <a name="context-and-problem"></a>Context en probleem
Een algemene vereiste is de mogelijkheid om de meest recent gemaakte entiteiten op te halen, bijvoorbeeld de 10 meest recente onkosten claims die zijn ingediend door een werk nemer. Tabel query's bieden ondersteuning voor een **$Top** query bewerking voor het retour neren van de eerste *n* entiteiten uit een set: er is geen equivalente query bewerking om de laatste n-entiteiten in een set te retour neren.  

### <a name="solution"></a>Oplossing
Sla de entiteiten op met behulp van een **RowKey** die op natuurlijke wijze in omgekeerde datum/tijd wordt gesorteerd door gebruik te maken van de meest recente vermelding altijd het eerste item in de tabel.  

Als u bijvoorbeeld de 10 meest recente onkosten claims wilt ophalen die door een werk nemer zijn ingediend, kunt u een omgekeerde maat waarde gebruiken die is afgeleid van de huidige datum/tijd. In het volgende voor beeld van C#-code ziet u één manier om een geschikte ' omgekeerde Ticks '-waarde te maken voor een **RowKey** die van de meest recente naar de oudste sorteert:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

U kunt teruggaan naar de waarde datum en tijd met de volgende code:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

De tabel query ziet er als volgt uit:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* U moet de omgekeerde Tick-waarde met voorloop nullen aanvullen om ervoor te zorgen dat de teken reeks waarde op de verwachte manier wordt gesorteerd.  
* U moet rekening houden met de schaalbaarheids doelen op het niveau van een partitie. Wees voorzichtig met het maken van hot spot-partities.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten in omgekeerde datum/tijd-volg orde wilt gebruiken of wanneer u toegang wilt krijgen tot de meest recent toegevoegde entiteiten.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Anti-patroon laten voorafgaan door/toevoegen](#prepend-append-anti-pattern)  
* [Entiteiten ophalen](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Patroon voor hoog volume verwijderen
Schakel het verwijderen van een groot aantal entiteiten in door alle entiteiten voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel op te slaan. u verwijdert de entiteiten door de tabel te verwijderen.  

### <a name="context-and-problem"></a>Context en probleem
Veel toepassingen verwijderen oude gegevens die niet meer beschikbaar zijn voor een client toepassing of die de toepassing heeft gearchiveerd naar een ander opslag medium. Normaal gesp roken identificeert u gegevens met een datum: u hebt bijvoorbeeld een vereiste om records te verwijderen van alle aanmeldings aanvragen die meer dan 60 dagen oud zijn.  

Een mogelijk ontwerp is het gebruik van de datum en tijd van de aanmeldings aanvraag in de **RowKey**:  

![Datum en tijd waarop de aanmeldings poging is gedaan](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Deze aanpak voor komt het partitioneren van HOTS pots, omdat de toepassing aanmeldings entiteiten kan invoegen en verwijderen voor elke gebruiker in een afzonderlijke partitie. Deze aanpak kan echter kosten en tijd in beslag nemen als u een groot aantal entiteiten hebt, omdat u eerst een tabel scan moet uitvoeren om alle entiteiten te identificeren die moeten worden verwijderd. vervolgens moet u elke oude entiteit verwijderen. U kunt het aantal Retouren naar de server verminderen dat nodig is voor het verwijderen van de oude entiteiten door meerdere verwijderings aanvragen in batch op te nemen in EGTs.  

### <a name="solution"></a>Oplossing
Gebruik een afzonderlijke tabel voor elke dag van de aanmeldings pogingen. U kunt het entiteits ontwerp hierboven gebruiken om HOTS pots te voor komen bij het invoegen van entiteiten, en het verwijderen van oude entiteiten is nu gewoon een kwestie van het verwijderen van één tabel elke dag (één opslag bewerking) in plaats van honderden en duizenden afzonderlijke aanmeldings entiteiten elke dag te zoeken en te verwijderen.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Ondersteunt uw ontwerp andere manieren waarop uw toepassing de gegevens gebruikt, zoals het opzoeken van specifieke entiteiten, het koppelen met andere gegevens of het genereren van geaggregeerde gegevens?  
* Vermijdt uw ontwerp hot spots wanneer u nieuwe entiteiten invoegt?  
* Er wordt een vertraging verwacht als u dezelfde tabel naam wilt gebruiken nadat u deze hebt verwijderd. Het is beter om altijd unieke tabel namen te gebruiken.  
* Er wordt enige beperking verwacht wanneer u een nieuwe tabel voor het eerst gebruikt terwijl de Table service de toegangs patronen leert en de partities verdeeld over knoop punten. U kunt overwegen hoe vaak nieuwe tabellen moeten worden gemaakt.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een groot aantal entiteiten hebt dat tegelijk moet worden verwijderd.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* Trans acties van entiteits groep
* [Entiteiten wijzigen](#modifying-entities)  

## <a name="data-series-pattern"></a>Patroon van de gegevens reeks
Sla volledige gegevens reeksen op in één entiteit om het aantal aanvragen dat u maakt, te minimaliseren.  

### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomend scenario is voor een toepassing om een reeks gegevens op te slaan die normaal gesp roken allemaal tegelijk moeten worden opgehaald. Uw toepassing kan bijvoorbeeld registreren hoeveel berichten elke werk nemer elk uur verzendt, en vervolgens deze gegevens gebruiken om te tekenen hoe vaak elke gebruiker de voor gaande 24 uur heeft verzonden. Het kan zijn dat u voor elke werk nemer 24 entiteiten opslaat:  

![24 entiteiten opslaan voor elke werk nemer](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Met dit ontwerp kunt u de entiteit die u wilt bijwerken voor elke werk nemer eenvoudig vinden en bijwerken wanneer de toepassing de waarde voor het aantal berichten moet bijwerken. Als u echter de informatie wilt ophalen om een grafiek van de activiteit voor de voor gaande 24 uur te tekenen, moet u 24 entiteiten ophalen.  

### <a name="solution"></a>Oplossing
Gebruik het volgende ontwerp met een afzonderlijke eigenschap om het aantal berichten voor elk uur op te slaan:  

![Entiteit voor bericht statistieken](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Met dit ontwerp kunt u een samenvoeg bewerking gebruiken om het aantal berichten voor een werk nemer voor een bepaald uur bij te werken. U kunt nu alle informatie ophalen die u nodig hebt om de grafiek uit te zetten met behulp van een aanvraag voor één entiteit.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als uw volledige gegevens reeks niet in één entiteit past (een entiteit kan Maxi maal 252 eigenschappen hebben), gebruikt u een alternatief gegevens archief, zoals een blob.  
* Als u meerdere clients tegelijk een entiteit bijwerkt, moet u de **ETAG** gebruiken voor het implementeren van optimistische gelijktijdigheid. Als u veel clients hebt, kunt u veel conflicten ondervinden.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u een gegevens reeks die aan een afzonderlijke entiteit is gekoppeld, moet bijwerken en ophalen.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon voor grote entiteiten](#large-entities-pattern)  
* [Samen voegen of vervangen](#merge-or-replace)  
* [Uiteindelijk consistent transactie patroon](#eventually-consistent-transactions-pattern) (als u de gegevens reeksen opslaat in een blob)  

## <a name="wide-entities-pattern"></a>Patroon voor grote entiteiten
Gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  

### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit kan niet meer dan 252 eigenschappen hebben (met uitzonde ring van de verplichte systeem eigenschappen) en kan niet meer dan 1 MB aan gegevens in totaal opslaan. In een relationele data base krijgt u normaal gesp roken een afronding van de limieten voor de grootte van een rij door een nieuwe tabel toe te voegen en een 1-op-1-relatie tussen hen af te dwingen.  

### <a name="solution"></a>Oplossing
Met behulp van de Table service kunt u meerdere entiteiten opslaan om één groot zakelijk object met meer dan 252 eigenschappen aan te duiden. Als u bijvoorbeeld een telling wilt opslaan van het aantal IM-berichten dat elke werk nemer heeft verzonden voor de afgelopen 365 dagen, kunt u het volgende ontwerp gebruiken dat twee entiteiten met verschillende schema's gebruikt:  

![Meerdere entiteiten](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Als u een wijziging wilt aanbrengen waarvoor beide entiteiten moeten worden bijgewerkt zodat ze met elkaar gesynchroniseerd blijven, kunt u een EGT gebruiken. U kunt ook één samenvoeg bewerking gebruiken om het aantal berichten voor een specifieke dag bij te werken. Als u alle gegevens voor een individuele werk nemer wilt ophalen, moet u beide entiteiten ophalen, die u kunt doen met twee efficiënte aanvragen die zowel een **PartitionKey** als een **RowKey** -waarde gebruiken.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Het ophalen van een volledige logische entiteit omvat ten minste twee opslag transacties: één om elke fysieke entiteit op te halen.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten wilt opslaan waarvan de grootte of het aantal eigenschappen groter is dan de limieten voor een afzonderlijke entiteit in de Table service.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* Trans acties van entiteits groep
* [Samen voegen of vervangen](#merge-or-replace)

## <a name="large-entities-pattern"></a>Patroon voor grote entiteiten
Gebruik Blob Storage voor het opslaan van grote eigenschaps waarden.  

### <a name="context-and-problem"></a>Context en probleem
Een afzonderlijke entiteit kan niet meer dan 1 MB aan gegevens in totaal opslaan. Als een of meer van uw eigenschappen waarden opslaan die ervoor zorgen dat de totale grootte van uw entiteit deze waarde overschrijdt, kunt u de hele entiteit niet opslaan in de Table service.  

### <a name="solution"></a>Oplossing
Als uw entiteit groter is dan 1 MB, omdat een of meer eigenschappen een grote hoeveelheid gegevens bevatten, kunt u gegevens opslaan in de Blob service en het adres van de BLOB vervolgens opslaan in een eigenschap in de entiteit. U kunt bijvoorbeeld de foto van een werk nemer opslaan in Blob Storage en een koppeling naar de foto opslaan in de eigenschap **foto** van uw werknemers entiteit:  

![Foto-eigenschap](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Als u de uiteindelijke consistentie tussen de entiteit in het Table service en de gegevens in de Blob service wilt behouden, gebruikt u het patroon met de [uiteindelijk consistente trans acties](#eventually-consistent-transactions-pattern) om uw entiteiten te onderhouden.
* Het ophalen van een volledige entiteit omvat ten minste twee opslag transacties: een om de entiteit op te halen en één om de BLOB-gegevens op te halen.  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Gebruik dit patroon wanneer u entiteiten wilt opslaan waarvan de grootte de limieten voor een afzonderlijke entiteit in de Table service overschrijdt.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Patroon uiteindelijk consistent trans acties](#eventually-consistent-transactions-pattern)  
* [Patroon voor grote entiteiten](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Anti-patroon laten voorafgaan door/toevoegen
Verg root de schaal baarheid wanneer u een groot aantal toevoegingen hebt door de toevoegingen over meerdere partities te spreiden.  

### <a name="context-and-problem"></a>Context en probleem
In afwachting van de in behandeling zijnde of toegevoegde entiteiten aan uw opgeslagen entiteiten resulteert doorgaans in de toepassing door nieuwe entiteiten toe te voegen aan de eerste of laatste partitie van een reeks partities. In dit geval worden alle invoegingen op elk gewenst moment in dezelfde partitie geplaatst, waardoor er een hotspot wordt gemaakt waarmee wordt voor komen dat de tabel service van taak verdeling wordt ingevoegd op meerdere knoop punten, waardoor uw toepassing mogelijk de schaalbaarheids doelen voor de partitie kan bereiken. Als u bijvoorbeeld een toepassing hebt die netwerk-en bron toegang door werk nemers registreert, kan een entiteits structuur, zoals hieronder wordt weer gegeven, ertoe leiden dat de partitie van het huidige uur een hotspot wordt als het volume van trans acties het schaalbaarheids doel voor een afzonderlijke partitie bereikt:  

![Entiteits structuur](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Oplossing
De volgende alternatieve entiteits structuur voor komt een hotspot op een bepaalde partitie als de toepassing gebeurtenissen registreert:  

![Alternatieve entiteits structuur](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

In dit voor beeld ziet u hoe zowel de **PartitionKey** als de **RowKey** samengestelde sleutels zijn. De **PartitionKey** gebruikt zowel de afdeling als de werk nemer-id om de logboek registratie over meerdere partities te verdelen.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Beschouw de volgende punten als u besluit hoe u dit patroon wilt implementeren:  

* Biedt de alternatieve sleutel structuur waarmee wordt voor komen dat u Hot partitioneert op invoegingen de query's die uw client toepassing maakt, efficiënt worden ondersteund?  
* Betekent uw verwachte volume aan trans acties dat u waarschijnlijk de schaalbaarheids doelen voor een afzonderlijke partitie zult bereiken en dat deze wordt beperkt door de opslag service?  

### <a name="when-to-use-this-pattern"></a>Wanneer dit patroon gebruiken
Vermijd het Anti-patroon laten voorafgaan door/Append wanneer uw volume aan trans acties waarschijnlijk zal leiden tot een beperking door de opslag service wanneer u toegang hebt tot een dynamische partitie.  

### <a name="related-patterns-and-guidance"></a>Gerelateerde patronen en richtlijnen
De volgende patronen en richtlijnen zijn mogelijk ook relevant bij de implementatie van dit patroon:  

* [Samengesteld sleutel patroon](#compound-key-pattern)  
* [Eind patroon van logboek](#log-tail-pattern)  
* [Entiteiten wijzigen](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Anti-patroon van logboek gegevens
Normaal gesp roken moet u de Blob service gebruiken in plaats van de Table service om logboek gegevens op te slaan.  

### <a name="context-and-problem"></a>Context en probleem
Een veelvoorkomende use-case voor logboek gegevens is het ophalen van een selectie logboek vermeldingen voor een specifiek datum/tijd bereik: u wilt bijvoorbeeld alle fout-en kritieke berichten zoeken die uw toepassing heeft geregistreerd tussen 15:04 en 15:06 op een specifieke datum. U wilt de datum en tijd van het logboek bericht niet gebruiken om te bepalen met welke partitie u logboek entiteiten opslaat: op een bepaald moment worden alle logboek entiteiten met dezelfde **PartitionKey** -waarde gedeeld (Zie de sectie [laten voorafgaan door/Append anti-pattern](#prepend-append-anti-pattern)). Het volgende entiteits schema voor een logboek bericht resulteert bijvoorbeeld in een hete partitie, omdat de toepassing alle logboek berichten naar de partitie schrijft voor de huidige datum en tijd:  

![Entiteit van het logboek bericht](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

In dit voor beeld bevat de **RowKey** de datum en tijd van het logboek bericht om er zeker van te zijn dat logboek berichten worden opgeslagen in de volg orde van datum/tijd en een bericht-id bevat als meerdere logboek berichten dezelfde datum en tijd delen.  

Een andere benadering is het gebruik van een **PartitionKey** die ervoor zorgt dat de toepassing berichten schrijft over diverse partities. Als de bron van het logboek bericht bijvoorbeeld een manier biedt om berichten over meerdere partities te verdelen, kunt u het volgende entiteits schema gebruiken:  

![Andere entiteit van het logboek bericht](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Het probleem met dit schema is echter dat alle logboek berichten moeten worden opgehaald voor een specifieke periode die u moet doorzoeken op elke partitie in de tabel.

### <a name="solution"></a>Oplossing
In de vorige sectie is het probleem gemarkeerd dat probeert de Table service te gebruiken voor het opslaan van logboek vermeldingen en voorgestelde twee, niet-bevredigende ontwerpen. Een oplossing heeft geleid tot een hot partitie met het risico op slechte prestaties logboek berichten te schrijven. de andere oplossing resulteerde in slechtere query prestaties vanwege de vereiste om elke partitie in de tabel te scannen om logboek berichten op te halen voor een bepaalde periode. Blob Storage biedt een betere oplossing voor dit type scenario. Dit is de manier waarop Azure Opslaganalyse de logboek gegevens opslaat die worden verzameld.  

In deze sectie wordt beschreven hoe Opslaganalyse logboek gegevens opslaat in Blob Storage als een illustratie van deze benadering om gegevens op te slaan die u doorgaans doorzoekt op bereik.  

Opslaganalyse slaat logboek berichten op in een indeling met scheidings tekens in meerdere blobs. De indeling met scheidings tekens maakt het eenvoudig voor een client toepassing om de gegevens in het logboek bericht te parseren.  

Opslaganalyse gebruikt een naamgevings Conventie voor blobs waarmee u de BLOB (of blobs) kunt vinden die de logboek berichten bevat waarvoor u zoekt. Een blob met de naam Queue/2014/07/31/1800/000001. log bevat bijvoorbeeld logboek berichten die betrekking hebben op de wachtrij service voor het uur, te beginnen bij 18:00 op 31 juli 2014. De ' 000001 ' geeft aan dat dit het eerste logboek bestand voor deze periode is. Opslaganalyse registreert ook de tijds tempels van de eerste en laatste logboek berichten die in het bestand zijn opgeslagen als onderdeel van de meta gegevens van de blob. Met de API voor Blob Storage kunt u blobs vinden in een container op basis van een naam voorvoegsel: voor het zoeken van alle blobs die wachtrij logboek gegevens bevatten voor het uur vanaf 18:00, gebruikt u het voor voegsel ' queue/2014/07/31/1800 '.  

Opslaganalyse buffers worden intern logboek berichten opgeslagen en vervolgens wordt de juiste BLOB bijgewerkt of wordt er een nieuwe gemaakt met de laatste batch logboek vermeldingen. Dit beperkt het aantal schrijf bewerkingen dat moet worden uitgevoerd naar de BLOB-service.  

Als u een soort gelijke oplossing in uw eigen toepassing implementeert, moet u overwegen hoe u de afweging tussen de betrouw baarheid (het schrijven van elke logboek vermelding naar Blob-opslag wanneer deze wordt uitgevoerd) en de kosten en schaal baarheid (het bufferen van updates in uw toepassing en het schrijven naar Blob-opslag in batches) kunt beheren.  

### <a name="issues-and-considerations"></a>Problemen en overwegingen
Houd rekening met de volgende punten wanneer u bepaalt hoe u logboek gegevens wilt opslaan:  

* Als u een tabel ontwerp maakt waarmee mogelijke Hot-partities worden voor komen, is het mogelijk dat u geen efficiënt toegang hebt tot uw logboek gegevens.  
* Voor het verwerken van logboek gegevens moet een client vaak veel records laden.  
* Hoewel logboek gegevens vaak worden gestructureerd, is het mogelijk dat Blob Storage een betere oplossing is.  

## <a name="implementation-considerations"></a>Afwegingen bij de implementatie
In deze sectie worden enkele aandachtspunten besproken wanneer u de in de vorige secties beschreven patronen implementeert. In de meeste van deze sectie worden voor beelden gebruikt die zijn geschreven in C# en die gebruikmaken van de Storage-client bibliotheek (versie 4.3.0 op het moment van schrijven).  

## <a name="retrieving-entities"></a>Entiteiten ophalen
Zoals beschreven in het sectie ontwerp voor het uitvoeren van query's, is de meest efficiënte query een Point-query. In sommige scenario's is het echter mogelijk dat u meerdere entiteiten moet ophalen. In deze sectie worden enkele veelvoorkomende benaderingen beschreven om entiteiten op te halen met behulp van de Storage-client bibliotheek.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Een Point-query uitvoeren met de Storage-client bibliotheek
De eenvoudigste manier om een punt query uit te voeren, is door de bewerking tabel **ophalen** te gebruiken, zoals wordt weer gegeven in het volgende code fragment van C# waarmee een entiteit wordt opgehaald met een **PartitionKey** van de waarde "Sales" en een **RowKey** van de waarde "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

U ziet hoe in dit voor beeld wordt verwacht dat de entiteit die wordt opgehaald, van het type **EmployeeEntity** is.  

### <a name="retrieving-multiple-entities-using-linq"></a>Ophalen van meerdere entiteiten met behulp van LINQ
U kunt LINQ gebruiken om meerdere entiteiten uit de Table service op te halen wanneer u met Microsoft Azure Cosmos-tabel standaard bibliotheek werkt. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Om de onderstaande voor beelden te laten werken, moet u naam ruimten toevoegen:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

De employeeTable is een CloudTable-object dat een methode CreateQuery \<ITableEntity> () implementeert, die een TableQuery retourneert \<ITableEntity> . Objecten van dit type implementeren een IQueryable en toestaan de syntaxis van LINQ-query-expressies en punt notatie te gebruiken.

Meerdere entiteiten ophalen en worden bereikt door een query op te geven met een **where** -component. Als u een tabel scan wilt voor komen, moet u altijd de waarde **PartitionKey** in de component WHERE toevoegen en indien mogelijk de **RowKey** -waarde om te voor komen dat er tabel-en partitie scans worden uitgevoerd. De tabel service ondersteunt een beperkt aantal vergelijkings operatoren (groter dan, groter dan of gelijk aan, kleiner dan, kleiner dan of gelijk aan, gelijk aan en niet gelijk aan) die worden gebruikt in de component WHERE. 

In het volgende code fragment van C# vindt u alle werk nemers waarvan de achternaam begint met ' B ' (ervan uitgaande dat de **RowKey** de achternaam opslaat) op de verkoop afdeling (ervan uitgaande dat de **PartitionKey** de naam van de afdeling opslaat):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

U ziet hoe de query zowel een **RowKey** als een **PartitionKey** opgeeft om betere prestaties te garanderen.  

Het volgende code voorbeeld toont gelijkwaardige functionaliteit zonder gebruik te maken van de LINQ-syntaxis:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Het voor beeld nest meerdere **CombineFilters** -methoden om de drie filter voorwaarden op te nemen.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Grote aantallen entiteiten ophalen uit een query
Een optimale query retourneert een afzonderlijke entiteit op basis van een **PartitionKey** -waarde en een **RowKey** -waarde. In sommige scenario's is het echter mogelijk dat u een groot aantal entiteiten van dezelfde partitie of zelfs van een groot aantal partities moet retour neren.  

In dergelijke scenario's moet u de prestaties van uw toepassing altijd volledig testen.  

Een query voor de tabel service kan Maxi maal 1.000 entiteiten tegelijk retour neren en kan Maxi maal vijf seconden worden uitgevoerd. Als de resultatenset meer dan 1.000 entiteiten bevat, als de query niet binnen vijf seconden is voltooid, of als de query de grens van de partitie overschrijdt, retourneert de Table service een vervolg token om de client toepassing in staat te stellen de volgende set entiteiten aan te vragen. Zie querytime [-out en paginering](/rest/api/storageservices/Query-Timeout-and-Pagination)voor meer informatie over de werking van vervolg tokens.  

Als u de Storage-client bibliotheek gebruikt, kan het automatisch vervolg tokens voor u afhandelen wanneer het entiteiten van de Table service retourneert. In het volgende voor beeld van een C#-code met behulp van de Storage-client bibliotheek worden vervolg tokens automatisch verwerkt als de Table-service deze retourneert in een antwoord:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

De volgende C#-code verwerkt vervolg tokens expliciet:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Door vervolg tokens expliciet te gebruiken, kunt u bepalen wanneer uw toepassing het volgende segment met gegevens ophaalt. Als uw client toepassing bijvoorbeeld gebruikers in staat stelt om de entiteiten die zijn opgeslagen in een tabel in te wisselen, kan een gebruiker besluiten niet alle entiteiten die door de query zijn opgehaald, te gebruiken zodat uw toepassing alleen een vervolg token gebruikt om het volgende segment op te halen wanneer de gebruiker klaar was met het door lopen van alle entiteiten in het huidige segment. Deze benadering heeft verschillende voor delen:  

* Hiermee kunt u de hoeveelheid gegevens die moet worden opgehaald van de Table service, en die u via het netwerk verplaatst, beperken.  
* Hiermee kunt u asynchrone i/o uitvoeren in .NET.  
* U kunt hiermee het vervolg token serialiseren naar permanente opslag, zodat u kunt door gaan in het geval een toepassing vastloopt.  

> [!NOTE]
> Een vervolg token retourneert meestal een segment met 1.000 entiteiten, maar dit kan minder zijn. Dit is ook het geval als u het aantal vermeldingen dat door een query wordt geretourneerd, beperkt **door gebruik te maken van** de eerste n entiteiten die overeenkomen met uw zoek criteria: de Table-service retourneert een segment met minder dan n entiteiten en een vervolg token om u in staat te stellen de resterende entiteiten op te halen.  
> 
> 

De volgende C#-code laat zien hoe u het aantal entiteiten wijzigt dat binnen een segment wordt geretourneerd:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projectie aan server zijde
Eén entiteit kan Maxi maal 255 eigenschappen hebben en kan Maxi maal 1 MB groot zijn. Wanneer u een query uitvoert op de tabel en entiteiten ophaalt, hebt u mogelijk niet alle eigenschappen nodig en kunt u voor komen dat gegevens onnodig worden overgedragen (om latentie en kosten te beperken). U kunt projectie aan server zijde gebruiken om alleen de eigenschappen over te dragen die u nodig hebt. In het volgende voor beeld wordt alleen de **e-mail** eigenschap (samen met **PartitionKey**, **RowKey**, **Time Stamp** en **ETAG**) opgehaald uit de entiteiten die zijn geselecteerd door de query.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

U ziet dat de waarde **RowKey** beschikbaar is, ook al is deze niet opgenomen in de lijst met eigenschappen die moeten worden opgehaald.  

## <a name="modifying-entities"></a>Entiteiten wijzigen
Met de Storage-client bibliotheek kunt u uw entiteiten die in de tabel service zijn opgeslagen, wijzigen door entiteiten in te voegen, te verwijderen en bij te werken. U kunt EGTs gebruiken om meerdere insert-, update-en delete-bewerkingen samen te nemen om het aantal benodigde retouren te verminderen en de prestaties van uw oplossing te verbeteren.  

Uitzonde ringen die worden gegenereerd wanneer de Storage-client bibliotheek een EGT uitvoert, bevatten doorgaans de index van de entiteit die de oorzaak van de batch heeft veroorzaakt. Dit is handig bij het opsporen van fouten in code die gebruikmaakt van EGTs.  

U moet ook overwegen hoe uw ontwerp van invloed is op de manier waarop uw client toepassing gelijktijdig valuta's en update bewerkingen verwerkt.  

### <a name="managing-concurrency"></a>Gelijktijdigheid beheren
De tabel service implementeert standaard optimistische gelijktijdigheid controles op het niveau van afzonderlijke entiteiten voor **Insert**-, **Merge**-en **Delete** -bewerkingen, hoewel het mogelijk is dat een client de Table-service afdwingt deze controles over te slaan. Zie voor meer informatie over hoe de tabel service gelijktijdigheid beheert,  [gelijktijdigheid beheren in Microsoft Azure Storage](../blobs/concurrency-manage.md).  

### <a name="merge-or-replace"></a>Samen voegen of vervangen
De methode **replace** van de klasse **TableOperation** vervangt altijd de volledige entiteit in de Table service. Als u in de aanvraag geen eigenschap opneemt wanneer die eigenschap in de opgeslagen entiteit bestaat, wordt die eigenschap door de aanvraag uit de opgeslagen entiteit verwijderd. Tenzij u een eigenschap expliciet van een opgeslagen entiteit wilt verwijderen, moet u elke eigenschap in de aanvraag toevoegen.  

U kunt de methode **Merge** van de klasse **TableOperation** gebruiken om de hoeveelheid gegevens te verminderen die u naar de Table service stuurt wanneer u een entiteit wilt bijwerken. De methode **Merge** vervangt alle eigenschappen in de opgeslagen entiteit door eigenschaps waarden van de entiteit die in de aanvraag is opgenomen, maar laat alle eigenschappen in de opgeslagen entiteit intact die niet zijn opgenomen in de aanvraag. Dit is handig als u grote entiteiten hebt en alleen een klein aantal eigenschappen in een aanvraag hoeft bij te werken.  

> [!NOTE]
> De methoden **vervangen** en **samen voegen** mislukken als de entiteit niet bestaat. Als alternatief kunt u de methoden **InsertOrReplace** en **InsertOrMerge** gebruiken om een nieuwe entiteit te maken als deze niet bestaat.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Werken met heterogene entiteits typen
De Table service is een tabel archief met *schema-minder* . Dit betekent dat een enkele tabel entiteiten van meerdere typen kan opslaan die een grote flexibiliteit in uw ontwerp bieden. In het volgende voor beeld ziet u een tabel waarin zowel werk nemer-als afdelings entiteiten worden opgeslagen:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>E-mail</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Elke entiteit moet nog steeds waarden voor **PartitionKey**, **RowKey** en **Time Stamp** hebben, maar kan een set eigenschappen hebben. Bovendien is er niets om het type van een entiteit aan te geven, tenzij u ervoor kiest om die informatie ergens op te slaan. Er zijn twee opties voor het identificeren van het entiteits type:  

* Laten voorafgaan door het entiteits type naar de **RowKey** (of mogelijk de **PartitionKey**). Bijvoorbeeld **EMPLOYEE_000123** of **DEPARTMENT_SALES** als **RowKey** -waarden.  
* Gebruik een afzonderlijke eigenschap om het entiteits type vast te leggen, zoals wordt weer gegeven in de onderstaande tabel.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>E-mail</th>
</tr>
<tr>
<td>Werknemer</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>E-mail</th>
</tr>
<tr>
<td>Werknemer</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Afdeling</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>E-mail</th>
</tr>
<tr>
<td>Werknemer</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

De eerste optie, in afwachting van het entiteits type op de **RowKey**, is handig als er sprake is van een mogelijkheid dat twee entiteiten van verschillende typen dezelfde sleutel waarde hebben. Het groepeert ook entiteiten van hetzelfde type samen in de partitie.  

De technieken die in deze sectie worden beschreven, zijn met name relevant voor de relaties van de discussie [overname](table-storage-design-modeling.md#inheritance-relationships) eerder in deze hand leiding in de artikel [modellerings relaties](table-storage-design-modeling.md).  

> [!NOTE]
> U moet overwegen een versie nummer in de waarde entiteits type op te nemen om client toepassingen in staat te stellen POCO-objecten te ontwikkelen en te werken met andere versies.  
> 
> 

In de rest van deze sectie worden enkele van de functies in de Storage-client bibliotheek beschreven, waarmee u in dezelfde tabel met meerdere entiteits typen kan werken.  

### <a name="retrieving-heterogeneous-entity-types"></a>Heterogene entiteits typen ophalen
Als u de Storage-client bibliotheek gebruikt, hebt u drie opties voor het werken met meerdere entiteits typen.  

Als u het type entiteit weet dat is opgeslagen met een specifieke **RowKey** -en **PartitionKey** -waarden, kunt u het entiteits type opgeven wanneer u de entiteit ophaalt, zoals wordt weer gegeven in de vorige twee voor beelden waarin entiteiten van het type **EmployeeEntity** worden opgehaald: [een punt query wordt uitgevoerd met de Storage-client bibliotheek](#executing-a-point-query-using-the-storage-client-library) en [meerdere entiteiten met LINQ ophalen](#retrieving-multiple-entities-using-linq).  

De tweede optie is het gebruik van het type **DynamicTableEntity** (een eigenschappen verzameling) in plaats van een concreet poco entiteits type (deze optie kan ook de prestaties verbeteren omdat het niet nodig is om de entiteit te serialiseren en deserialiseren naar .net-typen). De volgende C#-code haalt mogelijk meerdere entiteiten van verschillende typen op uit de tabel, maar retourneert alle entiteiten als **DynamicTableEntity** -exemplaren. Vervolgens wordt de eigenschap **EntityType** gebruikt om het type van elke entiteit te bepalen:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Als u andere eigenschappen wilt ophalen, moet u de methode **TryGetValue** gebruiken voor de eigenschap **Properties** van de klasse **DynamicTableEntity** .  

Een derde optie is om te combi neren met behulp van het type **DynamicTableEntity** en een **EntityResolver** -exemplaar. Zo kunt u omzetten in meerdere POCO-typen in dezelfde query. In dit voor beeld gebruikt de gemachtigde van de **EntityResolver** de eigenschap **EntityType** om onderscheid te maken tussen de twee typen entiteiten die door de query worden geretourneerd. De methode **Resolve** gebruikt de **resolver** -gemachtigde voor het omzetten van **DynamicTableEntity** -instanties in **TableEntity** -exemplaren.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{
    TableEntity resolvedEntity = null;
    if (props["EntityType"].StringValue == "Department")
    {
        resolvedEntity = new DepartmentEntity();
    }
    else if (props["EntityType"].StringValue == "Employee")
    {
        resolvedEntity = new EmployeeEntity();
    }
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Heterogene entiteits typen wijzigen
U hoeft niet te weten welk type entiteit u wilt verwijderen en u weet altijd het type van een entiteit wanneer u deze invoegt. U kunt echter **DynamicTableEntity** type gebruiken om een entiteit bij te werken zonder het type ervan te weten en zonder een poco-entiteits klasse te gebruiken. Met het volgende code voorbeeld wordt één entiteit opgehaald en wordt gecontroleerd of de eigenschap **EmployeeCount** bestaat voordat deze wordt bijgewerkt.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Toegang beheren met hand tekeningen voor gedeelde toegang
U kunt Shared Access Signature-tokens (SAS) gebruiken om client toepassingen de mogelijkheid te bieden om tabel entiteiten te wijzigen (en query) zonder dat u de sleutel van uw opslag account in uw code hoeft op te slaan. Normaal gesp roken zijn er drie belang rijke voor delen van het gebruik van SAS in uw toepassing:  

* U hoeft de sleutel van uw opslag account niet te distribueren naar een niet-beveiligd platform (zoals een mobiel apparaat) om het apparaat in de Table service toe te staan om entiteiten te openen en te wijzigen.  
* U kunt een deel van het werk dat web-en werk rollen uitvoeren, gebruiken om uw entiteiten te beheren op client apparaten, zoals computers en mobiele apparaten voor eind gebruikers.  
* U kunt een beperkte en tijdgebonden set machtigingen toewijzen aan een client (zoals alleen-lezen toegang toestaan voor specifieke resources).  

Zie [using Shared Access signatures (SAS) (Engelstalig)](../../storage/common/storage-sas-overview.md)voor meer informatie over het gebruik van SAS-tokens met de Table service.  

U moet echter nog steeds de SAS-tokens genereren die een client toepassing verlenen aan de entiteiten in de Table-service: u moet dit doen in een omgeving met beveiligde toegang tot uw opslag account sleutels. Normaal gesp roken gebruikt u een web-of worker-rol voor het genereren van de SAS-tokens en levert u deze aan de client toepassingen die toegang nodig hebben tot uw entiteiten. Omdat er nog steeds overhead is betrokken bij het genereren en leveren van SAS-tokens aan clients, moet u rekening houden met het beste om deze overhead te reduceren, met name in scenario's met grote volumes.  

Het is mogelijk om een SAS-token te genereren dat toegang verleent tot een subset van de entiteiten in een tabel. Standaard maakt u een SAS-token voor een hele tabel, maar het is ook mogelijk om op te geven dat het SAS-token toegang verleent tot een reeks **PartitionKey** -waarden of een bereik van **PartitionKey** -en **RowKey** -waarden. U kunt ervoor kiezen om SAS-tokens te genereren voor afzonderlijke gebruikers van uw systeem, zodat de SAS-token van elke gebruiker alleen toegang tot hun eigen entiteiten in de tabel service toestaat.  

## <a name="asynchronous-and-parallel-operations"></a>Asynchrone en parallelle bewerkingen
Als u uw aanvragen verspreidt over meerdere partities, kunt u de reactie snelheid van de door Voer en de client verbeteren met behulp van asynchrone of parallelle query's.
Stel dat u twee of meer worker-instanties tegelijk toegang hebt tot uw tabellen. U kunt afzonderlijke worker-rollen hebben die verantwoordelijk zijn voor bepaalde sets van partities of alleen meerdere rolinstanties hebben, die elk toegang hebben tot alle partities in een tabel.  

Binnen een client exemplaar kunt u de door Voer verbeteren door opslag bewerkingen asynchroon uit te voeren. Met de Storage-client bibliotheek kunt u eenvoudig asynchrone query's en wijzigingen schrijven. U kunt bijvoorbeeld beginnen met de synchrone methode waarmee alle entiteiten in een partitie worden opgehaald, zoals wordt weer gegeven in de volgende C#-code:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

U kunt deze code eenvoudig wijzigen zodat de query asynchroon wordt uitgevoerd:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

In dit asynchrone voor beeld ziet u de volgende wijzigingen ten opzichte van de synchrone versie:  

* De methode handtekening bevat nu de **async** -modificator en retourneert een **taak** exemplaar.  
* In plaats van de **ExecuteSegmented** -methode aan te roepen om de resultaten op te halen, roept de methode nu de **ExecuteSegmentedAsync** -methode aan en gebruikt de wijzigings functie **await** om resultaten asynchroon op te halen.  

De client toepassing kan deze methode meerdere keren aanroepen (met verschillende waarden voor de para meter **afdeling** ) en elke query wordt uitgevoerd in een afzonderlijke thread.  

Er is geen asynchrone versie van de methode **Execute** in de klasse **TableQuery** , omdat de **IEnumerable** -interface geen ondersteuning biedt voor asynchrone inventarisatie.  

U kunt ook instanties asynchroon invoegen, bijwerken en verwijderen. In het volgende C#-voor beeld ziet u een eenvoudige, synchrone methode voor het invoegen of vervangen van een werknemers entiteit:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

U kunt deze code eenvoudig aanpassen zodat de update op de volgende manier asynchroon wordt uitgevoerd:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

In dit asynchrone voor beeld ziet u de volgende wijzigingen ten opzichte van de synchrone versie:  

* De methode handtekening bevat nu de **async** -modificator en retourneert een **taak** exemplaar.  
* In plaats van de **Execute** -methode aan te roepen om de entiteit bij te werken, roept de methode nu de methode **ExecuteAsync** aan en gebruikt de wijzigings functie **await** om resultaten asynchroon op te halen.  

De client toepassing kan meerdere asynchrone methoden, zoals deze, aanroepen en elke methode aanroep wordt uitgevoerd op een afzonderlijke thread.  

## <a name="next-steps"></a>Volgende stappen

- [Relaties modelleren](table-storage-design-modeling.md)
- [Ontwerp voor query's](table-storage-design-for-query.md)
- [Tabelgegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)