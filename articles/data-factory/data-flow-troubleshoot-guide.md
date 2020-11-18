---
title: Problemen met toewijzing van gegevens stromen oplossen
description: Meer informatie over het oplossen van problemen met gegevens stromen in Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 5f29474705919f402b1c114c3fd2df0df037cdae
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696061"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Problemen met toewijzing van gegevens stromen in Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor het toewijzen van gegevens stromen in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Veelvoorkomende fouten en berichten

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Fout code: DF-uitvoerder-SourceInvalidPayload
- **Bericht**: uitvoering van voor beeld, fout opsporing en pipeline-gegevens stroom is mislukt omdat de container niet bestaat
- **Oorzaken**: wanneer dataset een container bevat die niet voor komt in de opslag
- **Aanbeveling**: Controleer of de container waarnaar wordt verwezen in uw gegevensset bestaat of toegankelijk is.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Fout code: DF-uitvoerder-SystemImplicitCartesian

- **Bericht**: impliciet Cartesisch product voor Inner join wordt niet ondersteund. gebruik in plaats daarvan cross join. Kolommen die in de samen voeging worden gebruikt, moeten een unieke sleutel voor rijen maken.
- **Oorzaken**: impliciet Cartesisch product voor Inner join tussen logische abonnementen wordt niet ondersteund. Als de kolommen die worden gebruikt in de samen voeging de unieke sleutel maken, is ten minste één kolom van beide zijden van de relatie vereist.
- **Aanbeveling**: voor niet-gelijkheid gebaseerde samen voegingen moet u kiezen voor aangepaste cross-koppeling.

### <a name="error-code-df-executor-systeminvalidjson"></a>Fout code: DF-uitvoerder-SystemInvalidJson

- **Bericht**: JSON-Parseerfout, niet-ondersteunde code ring of meerdere regels
- **Oorzaken**: mogelijke problemen met het JSON-bestand: niet-ondersteunde code ring, beschadigde bytes of het gebruik van JSON-bron als één document op veel geneste lijnen
- **Aanbeveling**: Controleer of de code ring van het JSON-bestand wordt ondersteund. Op de bron transformatie die gebruikmaakt van een JSON-gegevensset, vouwt u JSON-instellingen uit en schakelt u ' single document ' in.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Fout code: DF-uitvoerder-BroadcastTimeout

- **Bericht**: er is een time-out opgetreden voor de broadcast. Zorg ervoor dat de stroom van de uitzending gegevens binnen 60 seconden produceert in debug-uitvoeringen en 300 seconden bij uitvoering
- **Oorzaken**: Broadcast heeft een standaardtime-out van 60 seconden in debug-uitvoeringen en 300 seconden in taak uitvoeringen. De voor de uitzending gekozen stroom lijkt te groot om gegevens te produceren binnen deze limiet.
- **Aanbeveling**: Controleer het tabblad optimaliseren op uw gegevensstroom transformaties voor samen voegen, bestaan en opzoeken. De standaard optie voor broadcast is ' auto '. Als ' auto ' is ingesteld, of als u de linker-of rechter kant hand matig instelt op broadcast onder ' fixed ', kunt u een grotere Azure Integration Runtime configuratie instellen of uitschakeling uitschakelen. De aanbevolen benadering voor de beste prestaties in gegevens stromen is om te voor komen dat Spark met ' auto ' wordt uitgezonden en een geoptimaliseerd voor geheugen gebruikt Azure IR.

Als u de gegevens stroom uitvoert tijdens het uitvoeren van een debug-test uitvoering van een debug-pijp lijn, kunt u deze voor waarde vaker gebruiken. Dit komt doordat ADF de time-out van de uitzending beperkt tot 60 seconden om een snellere probleemoplossings ervaring te hand haven. Als u wilt uitbreiden naar de time-out van 300 seconden vanuit een geactiveerde uitvoering, kunt u de optie fout opsporing > activiteit gebruiken gebruiken om de Azure IR die is gedefinieerd in de pipeline-activiteit gegevens stroom uitvoeren uit te voeren.

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

 ### <a name="error-code-df-executor-illegalargument"></a>Fout code: DF-uitvoerder-illegalArgument
- **Bericht**: Controleer of de toegangs sleutel in de gekoppelde service juist is
- **Oorzaken**: de account naam of de toegangs sleutel is onjuist
- **Aanbeveling**: Controleer of de account naam of de toegangs sleutel die is opgegeven in de gekoppelde service juist is. 

 ### <a name="error-code-df-executor-invalidtype"></a>Fout code: DF-uitvoerder-InvalidType
- **Bericht**: Controleer of het type para meter overeenkomt met het type van de waarde die is door gegeven. Het door geven van float-para meters van pijp lijnen wordt momenteel niet ondersteund.
- **Oorzaken**: incompatibele gegevens typen tussen het gedeclareerde type en de werkelijke parameter waarde
- **Aanbeveling**: Controleer of de parameter waarden die zijn door gegeven in een gegevens stroom overeenkomen met het gedeclareerde type.

 ### <a name="error-code-df-executor-columnunavailable"></a>Fout code: DF-uitvoerder-ColumnUnavailable
- **Bericht**: de kolom naam die in de expressie wordt gebruikt, is niet beschikbaar of is ongeldig
- **Oorzaken**: ongeldige of niet-beschik bare kolom naam die wordt gebruikt in expressies
- **Aanbeveling**: kolom namen controleren die in expressies worden gebruikt

 ### <a name="error-code-df-executor-parseerror"></a>Fout code: DF-uitvoerder-ParseError
- **Bericht**: expressie kan niet worden geparseerd
- **Oorzaken**: de expressie bevat fouten parseren vanwege opmaak
- **Aanbeveling**: opmaak van expressie controleren

### <a name="error-code-getcommand-outputasync-failed"></a>Fout code: GetCommand OutputAsync is mislukt

- **Bericht**: tijdens fout opsporing en voor beeld van gegevens stroom: GetCommand OutputAsync is mislukt met...
- **Oorzaken**: dit is een fout in de back-end-service. U kunt de bewerking opnieuw proberen en ook de foutopsporingssessie opnieuw opstarten.
- **Aanbeveling**: als het probleem niet wordt opgelost door opnieuw op te starten, neemt u contact op met de klant ondersteuning.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Fout code: er is een onverwachte uitzonde ring opgetreden en de uitvoering is mislukt

- **Bericht**: tijdens de uitvoering van de gegevens stroom activiteit: er is een onverwachte uitzonde ring opgetreden en uitvoering mislukt.
- **Oorzaken**: dit is een fout in de back-end-service. U kunt de bewerking opnieuw proberen en ook de foutopsporingssessie opnieuw opstarten.
- **Aanbeveling**: als het probleem niet wordt opgelost door opnieuw op te starten, neemt u contact op met de klant ondersteuning.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Fout code: debug data preview zonder uitvoer gegevens bij samen voegen

- **Bericht**: er zijn een groot aantal null-waarden of ontbrekende waarden die kunnen worden veroorzaakt doordat er te weinig rijen worden steek proeven. Probeer de limiet voor het aantal foutopsporingslogboeken bij te werken en de gegevens te vernieuwen.
- **Oorzaken**: de samenvoegings voorwaarde komt niet overeen met rijen of resulteert in een groot aantal null-waarden tijdens de preview van de gegevens.
- **Aanbeveling**: Ga naar instellingen voor fout opsporing en verhoog het aantal rijen in de limiet voor de bron rijen. Zorg ervoor dat u een Azure IR met een groot voldoende gegevens stroom cluster hebt geselecteerd om meer gegevens te verwerken.

### <a name="error-code-validation-error-at-source-with-multiline-csv-files"></a>Fout code: validatie fout bij bron met CSV-bestanden met meerdere regels 

- **Bericht**: u ziet mogelijk een van de volgende fout berichten:
   - De laatste kolom is null of ontbreekt.
   - Schema validatie bij bron mislukt.
   - Het importeren van het schema kan niet juist worden weer gegeven in de UX en de laatste kolom heeft een nieuw regel teken in de naam.
- **Oorzaken**: in de stroom voor het toewijzen van gegevens werkt de CSV-bron van meerdere regels momenteel niet met het \r\n als scheidings teken voor rijen. Soms nemen extra regels met de bron waarden van het regel einde van het resultaat. 
- **Aanbeveling**: Genereer het bestand bij de bron met \n als een scheidings teken in plaats van \r\n. U kunt ook de Kopieer activiteit gebruiken om het CSV-bestand met \r\n te converteren naar \n als een scheidings teken voor rijen.

## <a name="general-troubleshooting-guidance"></a>Algemene richt lijnen voor probleem oplossing

1. Controleer de status van uw gegevensset-verbindingen. Ga in elke bron-en Sink-trans formatie naar de gekoppelde service voor elke gegevensset die u gebruikt en test verbindingen.
1. Controleer de status van uw bestands-en tabel verbindingen van de ontwerp functie voor gegevens stromen. Schakel over op fout opsporing en klik op voor beeld van gegevens op de bron transformaties om ervoor te zorgen dat u toegang hebt tot uw gegevens.
1. Als alles er goed uitziet in de preview van gegevens, gaat u naar de ontwerp functie voor pijp lijnen en plaatst u uw gegevens stroom in een pijplijn activiteit. Fout opsporing voor de pijp lijn voor een end-to-end-test.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:
*  [Data Factory Blog](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft Q&A-vragenpagina](/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatie gids voor gegevens stromen van ADF-toewijzing](concepts-data-flow-performance.md)
