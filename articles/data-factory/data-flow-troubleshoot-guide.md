---
title: Problemen met toewijzing van gegevens stromen oplossen
description: Meer informatie over het oplossen van problemen met gegevens stromen in Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 4545c3529baf92e2f90d9289ec6828ad9a720e3a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738001"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Problemen met toewijzing van gegevens stromen in Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor het toewijzen van gegevens stromen in Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Veelvoorkomende fout codes en-berichten 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Fout code: DF-uitvoerder-SourceInvalidPayload
- **Bericht**: uitvoering van voor beeld, fout opsporing en pipeline-gegevens stroom is mislukt omdat de container niet bestaat
- **Oorzaken**: wanneer dataset een container bevat die niet voor komt in de opslag
- **Aanbeveling**: Controleer of de container waarnaar wordt verwezen in uw gegevensset bestaat of toegankelijk is.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Fout code: DF-uitvoerder-SystemImplicitCartesian

- **Bericht**: impliciet Cartesisch product voor Inner join wordt niet ondersteund. gebruik in plaats daarvan cross join. Kolommen die in de samen voeging worden gebruikt, moeten een unieke sleutel voor rijen maken.
- **Oorzaken**: impliciet Cartesisch product voor Inner join tussen logische abonnementen wordt niet ondersteund. Als de kolommen in de samen voeging worden gebruikt, is de unieke sleutel met ten minste één kolom aan beide zijden van de relatie vereist.
- **Aanbeveling**: voor niet-gelijkheid gebaseerde samen voegingen moet u kiezen voor aangepaste cross-koppeling.

### <a name="error-code-df-executor-systeminvalidjson"></a>Fout code: DF-uitvoerder-SystemInvalidJson

- **Bericht**: JSON-Parseerfout, niet-ondersteunde code ring of meerdere regels
- **Oorzaken**: mogelijke problemen met het JSON-bestand: niet-ondersteunde code ring, beschadigde bytes of het gebruik van JSON-bron als één document op veel geneste lijnen
- **Aanbeveling**: Controleer of de code ring van het JSON-bestand wordt ondersteund. Op de bron transformatie die gebruikmaakt van een JSON-gegevensset, vouwt u JSON-instellingen uit en schakelt u ' single document ' in.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Fout code: DF-uitvoerder-BroadcastTimeout

- **Bericht**: er is een time-out opgetreden voor de broadcast. Zorg ervoor dat de stroom van de uitzending gegevens binnen 60 seconden produceert in debug-uitvoeringen en 300 seconden bij uitvoering
- **Oorzaken**: Broadcast heeft een standaardtime-out van 60 seconden in debug-uitvoeringen en 300 seconden in taak uitvoeringen. De voor de uitzending gekozen stroom lijkt te groot om gegevens te produceren binnen deze limiet.
- **Aanbeveling**: Controleer het tabblad optimaliseren op uw gegevensstroom transformaties voor samen voegen, bestaan en opzoeken. De standaard optie voor broadcast is ' auto '. Als ' auto ' is ingesteld, of als u de linker-of rechter kant hand matig instelt op broadcast onder ' fixed ', kunt u een grotere Azure Integration Runtime configuratie instellen of uitschakeling uitschakelen. De aanbevolen benadering voor de beste prestaties in gegevens stromen is om te voor komen dat Spark met ' auto ' wordt uitgezonden en een geoptimaliseerd voor geheugen gebruikt Azure IR. Als u de gegevens stroom uitvoert tijdens het uitvoeren van een debug-test uitvoering van een debug-pijp lijn, kunt u deze voor waarde vaker gebruiken. Dit komt doordat ADF de time-out van de uitzending beperkt tot 60 seconden om een snellere probleemoplossings ervaring te hand haven. Als u wilt uitbreiden naar de time-out van 300 seconden vanuit een geactiveerde uitvoering, kunt u de optie fout opsporing > activiteit gebruiken gebruiken om de Azure IR die is gedefinieerd in de pipeline-activiteit gegevens stroom uitvoeren uit te voeren.

- **Bericht**: er is een time-out opgetreden voor de broadcast-koppeling. u kunt dit probleem voor komen door de broadcast optie uit te voeren in de trans formatie samen voegen/bestaan/opzoeken. Als u van plan bent om verbinding te maken om de prestaties te verbeteren, zorgt u ervoor dat de uitzendings stroom binnen 60 seconden gegevens kan produceren in debug-uitvoeringen en 300 seconden in de uitvoering van taken.
- **Oorzaken**: Broadcast heeft een standaard time-out van 60 seconden in debug-uitvoeringen en 300 seconden in de uitvoering van taken. Bij broadcast-deelname lijkt de stroom die is gekozen voor broadcast te groot is om gegevens te produceren binnen deze limiet. Als er geen broadcast-koppeling wordt gebruikt, kan de standaard uitzending die wordt uitgevoerd door de gegevens stroom dezelfde limiet bereiken
- **Aanbeveling**: Schakel de optie Broadcast uit of Vermijd het uitzenden van grote gegevens stromen waarbij de verwerking meer dan 60 seconden kan duren. Kies in plaats daarvan een kleinere stroom om te broadcasten. Grote SQL/DW-tabellen en-bron bestanden zijn meestal onjuiste kandidaten. Als er geen broadcast wordt toegevoegd, gebruikt u een groter cluster als de fout optreedt.

### <a name="error-code-df-executor-conversion"></a>Fout code: DF-uitvoeringen-conversie

- **Bericht**: converteren naar een datum of tijd is mislukt vanwege een ongeldig teken
- **Oorzaken**: gegevens hebben niet de verwachte indeling
- **Aanbeveling**: gebruik het juiste gegevens type

### <a name="error-code-df-executor-invalidcolumn"></a>Fout code: DF-uitvoerder-InvalidColumn
- **Bericht**: de kolom naam moet worden opgegeven in de query, een alias instellen als u een SQL-functie gebruikt
- **Oorzaken**: er is geen kolom naam opgegeven
- **Aanbeveling**: Stel een alias in als u een SQL-functie gebruikt, zoals min ()/Max (), enzovoort.

### <a name="error-code-df-executor-drivererror"></a>Fout code: DF-uitvoerder-DriverError
- **Bericht**: INT96 is een verouderd tijds tempel type dat niet wordt ondersteund door de ADF-gegevens stroom. Overweeg het kolom Type bij te werken naar de meest recente typen.
- **Oorzaken**: stuurprogrammafout
- **Aanbeveling**: INT96 is verouderd tijds tempel type. dit wordt niet ondersteund door de ADF-gegevens stroom. Overweeg het kolom Type bij te werken naar de meest recente typen.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Fout code: DF-uitvoerder-BlockCountExceedsLimitError
- **Bericht**: het niet-toegewezen blok aantal mag de maximum limiet van 100.000 blokken niet overschrijden. Controleer de BLOB-configuratie.
- **Oorzaken**: er kunnen maxi maal 100.000 niet-doorgevoerde blokken in een BLOB zijn.
- **Aanbeveling**: Neem contact op met het product team van micro soft voor meer informatie

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Fout code: DF-uitvoerder-PartitionDirectoryError
- **Bericht**: het opgegeven bronpad heeft meerdere gepartitioneerde directory's (bijvoorbeeld <Source Path> /<partitie Root Directory 1>/a = 10/b = 20, <Source Path> /<partition root directory 2>/c = 10/d = 30) of gepartitioneerde directory met een ander bestand of een niet-gepartitioneerde directory (bijvoorbeeld <Source Path> /<partitie root directory 1>/a = 10/b = 20, <Source Path> /Directory 2/bestand1), verwijdert u de partitie basis directory van het bronpad en leest u deze via een afzonderlijke bron transformatie.
- **Oorzaken**: het bronpad heeft meerdere gepartitioneerde directory's of een gepartitioneerde map met een ander bestand of een niet-gepartitioneerde directory.
- **Aanbeveling**: Verwijder de gepartitioneerde hoofdmap van het bronpad en lees deze via een afzonderlijke bron transformatie.

### <a name="error-code-df-executor-outofmemoryerror"></a>Fout code: DF-uitvoerder-OutOfMemoryError
- **Bericht**: er is een fout opgetreden tijdens het uitvoeren van het cluster. Probeer het opnieuw met een Integration runtime met grotere kern aantallen en/of geoptimaliseerd voor geheugen
- **Oorzaken**: er is onvoldoende geheugen beschikbaar voor het cluster
- **Aanbeveling**: clusters voor fout opsporing zijn bedoeld voor ontwikkelings doeleinden. Gebruik gegevens bemonstering, het juiste reken type en de grootte om de payload uit te voeren. Raadpleeg de [richt lijnen voor het toewijzen van gegevens stromen](concepts-data-flow-performance.md) voor het afstemmen om de beste prestaties te krijgen.

### <a name="error-code-df-executor-invalidtype"></a>Fout code: DF-uitvoerder-InvalidType
- **Bericht**: Controleer of het type para meter overeenkomt met het type van de waarde die is door gegeven. Het door geven van float-para meters van pijp lijnen wordt momenteel niet ondersteund.
- **Oorzaken**: incompatibele gegevens typen tussen het gedeclareerde type en de werkelijke parameter waarde
- **Aanbeveling**: Controleer of de parameter waarden die zijn door gegeven in een gegevens stroom overeenkomen met het gedeclareerde type.

### <a name="error-code-df-executor-columnunavailable"></a>Fout code: DF-uitvoerder-ColumnUnavailable
- **Bericht**: de kolom naam die in de expressie wordt gebruikt, is niet beschikbaar of is ongeldig
- **Oorzaken**: ongeldige of niet-beschik bare kolom naam die wordt gebruikt in expressies
- **Aanbeveling**: Controleer de kolom namen die in expressies worden gebruikt

### <a name="error-code-df-executor-parseerror"></a>Fout code: DF-uitvoerder-ParseError
- **Bericht**: expressie kan niet worden geparseerd
- **Oorzaken**: de expressie bevat fouten parseren vanwege opmaak
- **Aanbeveling**: Controleer de opmaak in de expressie.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Fout code: DF-uitvoerder-SystemImplicitCartesian
- **Bericht**: impliciet Cartesisch product voor Inner join wordt niet ondersteund. gebruik in plaats daarvan cross join. Kolommen die in de samen voeging worden gebruikt, moeten een unieke sleutel voor rijen maken.
- **Oorzaken**: impliciet Cartesisch product voor Inner join tussen logische abonnementen wordt niet ondersteund. Als de kolommen die in de samen voeging worden gebruikt, de unieke sleutel maken
- **Aanbeveling**: voor niet-gelijkheid gebaseerde samen voegingen moet u kiezen voor cross-koppeling.

### <a name="error-code-df-executor-systeminvalidjson"></a>Fout code: DF-uitvoerder-SystemInvalidJson
- **Bericht**: JSON-Parseerfout, niet-ondersteunde code ring of meerdere regels
- **Oorzaken**: mogelijke problemen met het JSON-bestand: niet-ondersteunde code ring, beschadigde bytes of het gebruik van JSON-bron als één document op veel geneste lijnen
- **Aanbeveling**: Controleer of de code ring van het JSON-bestand wordt ondersteund. Op de bron transformatie die gebruikmaakt van een JSON-gegevensset, vouwt u JSON-instellingen uit en schakelt u ' single document ' in.



### <a name="error-code-df-executor-conversion"></a>Fout code: DF-uitvoeringen-conversie
- **Bericht**: converteren naar een datum of tijd is mislukt vanwege een ongeldig teken
- **Oorzaken**: gegevens hebben niet de verwachte indeling
- **Aanbeveling**: gebruik het juiste gegevens type.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Fout code: DF-uitvoerder-BlockCountExceedsLimitError
- **Bericht**: het niet-toegewezen blok aantal mag de maximum limiet van 100.000 blokken niet overschrijden. Controleer de BLOB-configuratie.
- **Oorzaken**: er kunnen maxi maal 100.000 niet-doorgevoerde blokken in een BLOB zijn.
- **Aanbeveling**: Neem contact op met het product team van micro soft voor meer informatie

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Fout code: DF-uitvoerder-PartitionDirectoryError
- **Bericht**: het opgegeven bronpad heeft meerdere gepartitioneerde directory's (bijvoorbeeld *<Source Path> /<partitie root directory 1>/a = 10/b = 20, <Source Path> /<Partition root directory 2>/c = 10/d = 30*) of gepartitioneerde directory met een ander bestand of een niet-gepartitioneerde directory (bijvoorbeeld *<Source Path> /<partitie root directory 1>/A = 10/b = 20, <Source Path> /Directory 2/bestand1*), verwijdert u de partitie basis directory van het bronpad en leest u deze via een afzonderlijke bron transformatie.
- **Oorzaken**: het bronpad heeft meerdere gepartitioneerde directory's of een gepartitioneerde map met een ander bestand of een niet-gepartitioneerde directory.
- **Aanbeveling**: Verwijder de gepartitioneerde hoofdmap van het bronpad en lees deze via een afzonderlijke bron transformatie.

### <a name="error-code-getcommand-outputasync-failed"></a>Fout code: GetCommand OutputAsync is mislukt
- **Bericht**: tijdens fout opsporing en voor beeld van gegevens stroom: GetCommand OutputAsync is mislukt met...
- **Oorzaken**: dit is een fout in de back-end-service. U kunt de bewerking opnieuw proberen en ook de foutopsporingssessie opnieuw opstarten.
- **Aanbeveling**: als het probleem niet wordt opgelost door opnieuw op te starten, neemt u contact op met de klant ondersteuning. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Fout code: DF-uitvoerder-OutOfMemoryError
 
- **Bericht**: er is een fout opgetreden tijdens het uitvoeren van het cluster. Probeer het opnieuw met een Integration runtime met grotere kern aantallen en/of geoptimaliseerd voor geheugen
- **Oorzaken**: het geheugen van het cluster wordt niet meer gebruikt.
- **Aanbeveling**: clusters voor fout opsporing zijn bedoeld voor ontwikkelings doeleinden. Profiteer van de bemonsterde gegevens bemonstering van het juiste reken type en de grootte om de payload uit te voeren. Raadpleeg de [hand leiding voor gegevensstroom prestaties](./concepts-data-flow-performance.md) voor het afstemmen van de gegevens stromen voor de beste prestaties.

### <a name="error-code-df-executor-illegalargument"></a>Fout code: DF-uitvoerder-illegalArgument
- **Bericht**: Controleer of de toegangs sleutel in de gekoppelde service juist is.
- **Oorzaken**: de account naam of de toegangs sleutel is onjuist.
- **Aanbeveling**: Geef de juiste account naam of toegangs sleutel op.

- **Bericht**: Controleer of de toegangs sleutel in de gekoppelde service juist is
- **Oorzaken**: de account naam of de toegangs sleutel is onjuist
- **Aanbeveling**: Controleer of de account naam of de toegangs sleutel die is opgegeven in de gekoppelde service juist is. 

### <a name="error-code-df-executor-invalidtype"></a>Fout code: DF-uitvoerder-InvalidType
- **Bericht**: Controleer of het type para meter overeenkomt met het type van de waarde die is door gegeven. Het door geven van float-para meters van pijp lijnen wordt momenteel niet ondersteund.
- **Oorzaken**: incompatibele gegevens typen tussen het gedeclareerde type en de werkelijke parameter waarde
- **Aanbeveling**: Geef de juiste gegevens typen op.

### <a name="error-code-df-executor-columnunavailable"></a>Fout code: DF-uitvoerder-ColumnUnavailable
- **Bericht**: de kolom naam die in de expressie wordt gebruikt, is niet beschikbaar of is ongeldig.
- **Oorzaken**: ongeldige of niet-beschik bare kolom naam wordt gebruikt in expressies.
- **Aanbeveling**: Controleer de namen van de kolommen die worden gebruikt in expressies.


### <a name="error-code-df-executor-parseerror"></a>Fout code: DF-uitvoerder-ParseError
- **Bericht**: expressie kan niet worden geparseerd.
- **Oorzaken**: de expressie bevat fouten bij het parseren van de indeling.
- **Aanbeveling**: Controleer de opmaak in de expressie.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Fout code: DF-uitvoerder-OutOfDiskSpaceError
- **Bericht**: interne server fout
- **Oorzaken**: er is onvoldoende schijf ruimte op het cluster.
- **Aanbeveling**: Voer de pijp lijn opnieuw uit. Als het probleem zich blijft voordoen, neemt u contact op met de klant ondersteuning.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Fout code: DF-uitvoerder-StoreIsNotDefined
- **Bericht**: de archief configuratie is niet gedefinieerd. Deze fout wordt mogelijk veroorzaakt door een ongeldige parameter toewijzing in de pijp lijn.
- **Oorzaken**: onbepaald
- **Aanbeveling**: Controleer de toewijzing van de parameter waarde in de pijp lijn. De parameter expressie bevat mogelijk ongeldige tekens.

### <a name="error-code-df-excel-invalidconfiguration"></a>Fout code: DF-Excel-InvalidConfiguration
- **Bericht**: er is een Excel-blad naam of-index vereist.
- **Oorzaken**: onbepaald
- **Aanbeveling**: Controleer de waarde van de para meter en geef blad naam of index op om Excel-gegevens te lezen.

- **Bericht**: er kan niet tegelijkertijd een Excel-blad naam en-index bestaan.
- **Oorzaken**: onbepaald
- **Aanbeveling**: Controleer de waarde van de para meter en geef blad naam of index op om Excel-gegevens te lezen.

- **Bericht**: er is een ongeldig bereik verstrekt.
- **Oorzaken**: onbepaald
- **Aanbeveling**: Controleer de waarde van de para meter en geef een geldig bereik op Referentie: [Excel-eigenschappen](./format-excel.md#dataset-properties)op.

- **Bericht**: er is een ongeldig Excel-bestand ingevoerd terwijl alleen. XLSX en. xls worden ondersteund
- **Oorzaken**: onbepaald
- **Aanbeveling**: Zorg ervoor dat de Excel-bestands extensie. XLSX of. xls is.


 ### <a name="error-code-df-excel-invaliddata"></a>Fout code: DF-Excel-InvalidData
- **Bericht**: Excel-werk blad bestaat niet.
- **Oorzaken**: onbepaald
- **Aanbeveling**: Controleer de waarde van de para meter en geef een geldige blad naam of index op om Excel-gegevens te lezen.

- **Bericht**: het lezen van Excel-bestanden met een ander schema wordt nu niet ondersteund.
- **Oorzaken**: onbepaald
- **Aanbeveling**: gebruik het juiste Excel-bestand.

- **Bericht**: gegevens type wordt niet ondersteund.
- **Oorzaken**: onbepaald
- **Aanbeveling**: gebruik de gegevens typen van een Excel-bestand.

### <a name="error-code-4502"></a>Fout code: 4502
- **Bericht**: er zijn belang rijke gelijktijdige MappingDataflow-uitvoeringen waardoor storingen worden veroorzaakt door beperking onder Integration runtime.
- **Oorzaken**: het uitvoeren van een groot aantal gegevensstroom activiteiten wordt gelijktijdig op het Integration runtime. Lees meer over de [Azure Data Factory limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Aanbeveling**: als u meer gegevens stroom activiteiten parallel wilt uitvoeren, moet u deze distribueren op meerdere integratie-Runtimes.


### <a name="error-code-invalidtemplate"></a>Fout code: InvalidTemplate
- **Bericht**: de pijplijn expressie kan niet worden geëvalueerd.
- **Oorzaken**: de pijplijn expressie die is door gegeven in de gegevensstroom activiteit, wordt niet correct verwerkt vanwege een syntaxis fout.
- **Aanbeveling**: Verifieer uw activiteiten in activiteiten bewaking om de expressie te controleren.

### <a name="error-code-2011"></a>Fout code: 2011
- **Bericht**: de activiteit is uitgevoerd op Azure Integration runtime en het ontsleutelen van de referentie van de gegevens opslag of COMPUTE die is verbonden via een zelf-hosted Integration runtime is mislukt. Controleer de configuratie van gekoppelde services die zijn gekoppeld aan deze activiteit en zorg ervoor dat u het juiste type Integration runtime gebruikt.
- **Oorzaken**: gegevens stroom biedt geen ondersteuning voor de gekoppelde services met de zelf-hostende Integration runtime.
- **Aanbeveling**: Configureer de gegevens stroom om te worden uitgevoerd op Integration runtime met managed Virtual Network.

## <a name="miscellaneous-troubleshooting-tips"></a>Diverse tips voor probleem oplossing
- **Probleem**: er is een onverwachte uitzonde ring opgetreden en de uitvoering is mislukt
    - **Bericht**: tijdens de uitvoering van de gegevens stroom activiteit: er is een onverwachte uitzonde ring opgetreden en uitvoering mislukt.
    - **Oorzaken**: dit is een fout in de back-end-service. U kunt de bewerking opnieuw proberen en ook de foutopsporingssessie opnieuw opstarten.
    - **Aanbeveling**: als het probleem niet wordt opgelost door opnieuw op te starten, neemt u contact op met de klant ondersteuning.

-  **Probleem**: voor beeld van fout opsporing van gegevens zonder uitvoer gegevens bij samen voeging
    - **Bericht**: er zijn een groot aantal null-waarden of ontbrekende waarden die kunnen worden veroorzaakt doordat er te weinig rijen worden steek proeven. Probeer de limiet voor het aantal foutopsporingslogboeken bij te werken en de gegevens te vernieuwen.
    - **Oorzaken**: de samenvoegings voorwaarde komt niet overeen met rijen of resulteert in een groot aantal null-waarden tijdens de preview van de gegevens.
    - **Aanbeveling**: Ga naar instellingen voor fout opsporing en verhoog het aantal rijen in de limiet voor de bron rijen. Zorg ervoor dat u een Azure IR met een groot voldoende gegevens stroom cluster hebt geselecteerd om meer gegevens te verwerken.
    
- **Probleem**: validatie fout bij bron met CSV-bestanden met meerdere regels 
    - **Bericht**: u ziet mogelijk een van de volgende fout berichten:
        - De laatste kolom is null of ontbreekt.
        - Schema validatie bij bron mislukt.
        - Het importeren van het schema kan niet juist worden weer gegeven in de UX en de laatste kolom heeft een nieuw regel teken in de naam.
    - **Oorzaken**: in de stroom voor het toewijzen van gegevens werkt de CSV-bron van meerdere regels momenteel niet met het \r\n als scheidings teken voor rijen. Soms nemen extra regels met de bron waarden van het regel einde van het resultaat. 
    - **Aanbeveling**: Genereer het bestand bij de bron met \n als een scheidings teken in plaats van \r\n. U kunt ook de Kopieer activiteit gebruiken om het CSV-bestand met \r\n te converteren naar \n als een scheidings teken voor rijen.

## <a name="general-troubleshooting-guidance"></a>Algemene richt lijnen voor probleem oplossing
1. Controleer de status van uw gegevensset-verbindingen. Ga in elke bron-en Sink-trans formatie naar de gekoppelde service voor elke gegevensset die u gebruikt en test verbindingen.
2. Controleer de status van uw bestands-en tabel verbindingen van de ontwerp functie voor gegevens stromen. Schakel over op fout opsporing en klik op voor beeld van gegevens op de bron transformaties om ervoor te zorgen dat u toegang hebt tot uw gegevens.
3. Als alles er goed uitziet in de preview van gegevens, gaat u naar de ontwerp functie voor pijp lijnen en plaatst u uw gegevens stroom in een pijplijn activiteit. Fout opsporing voor de pijp lijn voor een end-to-end-test.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen kunt u de volgende bronnen proberen:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack overflow-forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)