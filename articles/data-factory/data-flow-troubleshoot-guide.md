---
title: Problemen met toewijzing van gegevens stromen oplossen
description: Meer informatie over het oplossen van problemen met gegevens stroom in Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.openlocfilehash: a475441a845300d74014924415a4e48ae4de16df
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628285"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Problemen met toewijzing van gegevens stromen in Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor het toewijzen van gegevens stromen in Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Veelvoorkomende fout codes en-berichten 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Fout code: DF-uitvoerder-SourceInvalidPayload
- **Bericht**: uitvoering van voor beeld, fout opsporing en pipeline-gegevens stroom is mislukt omdat de container niet bestaat
- **Oorzaak**: een gegevensset bevat een container die niet voor komt in de opslag.
- **Aanbeveling**: Controleer of de container waarnaar wordt verwezen in uw gegevensset bestaat en of deze toegankelijk is.

### <a name="error-code-df-executor-systeminvalidjson"></a>Fout code: DF-uitvoerder-SystemInvalidJson

- **Bericht**: JSON-Parseerfout, niet-ondersteunde code ring of meerdere regels
- **Oorzaak**: mogelijke problemen met het JSON-bestand: niet-ondersteunde code ring, beschadigde bytes of het gebruik van JSON-bron als één document op veel geneste regels.
- **Aanbeveling**: Controleer of de code ring van het JSON-bestand wordt ondersteund. Vouw op de bron transformatie die gebruikmaakt van een JSON-gegevensset **JSON-instellingen** uit en schakel vervolgens **één document** in.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Fout code: DF-uitvoerder-BroadcastTimeout

- **Bericht**: er is een time-out opgetreden voor de broadcast. Zorg ervoor dat de stroom van de uitzending gegevens binnen 60 seconden produceert in debug-uitvoeringen en 300 seconden bij uitvoering
- **Oorzaak**: Broadcast heeft een standaard time-out van 60 seconden op debug-uitvoeringen en 300 seconden bij uitvoering van taken. De stroom die voor de broadcast is gekozen, is te groot voor het produceren van gegevens binnen deze limiet.
- **Aanbeveling**: Controleer het tabblad **optimaliseren** op uw gegevensstroom transformaties voor samen voegen, bestaan en opzoeken. De standaard optie voor broadcast is **auto**. Als **automatisch** is ingesteld, of als u de linker-of rechter kant hand matig instelt op broadcast onder **vast**, kunt u een grotere configuratie van Azure Integration runtime (IR) instellen of de uitzending uitschakelen. Voor de beste prestaties van gegevens stromen raden we u aan om Spark te broadcasten met behulp van **auto** en een Azure IR geoptimaliseerd voor geheugen te gebruiken. 
 
  Als u de gegevens stroom uitvoert tijdens het uitvoeren van een debug-test uitvoering van een debug-pijp lijn, kunt u deze voor waarde vaker gebruiken. Dat komt omdat Azure Data Factory de time-out van de uitzending tot 60 seconden beperkt om een snellere fout opsporing te ondervinden. U kunt de time-out verlengen naar de 300-seconde time-out van een geactiveerde uitvoering. Hiervoor kunt u de optie Runtime-activiteit voor **fout opsporing** gebruiken gebruiken  >   om de Azure IR te gebruiken die is gedefinieerd in de pijplijn activiteit gegevens stroom uitvoeren.

- **Bericht**: er is een time-out opgetreden voor de broadcast-koppeling. u kunt dit probleem voor komen door de broadcast optie uit te voeren in de trans formatie samen voegen/bestaan/opzoeken. Als u van plan bent om verbinding te maken om de prestaties te verbeteren, zorgt u ervoor dat de uitzendings stroom binnen 60 seconden gegevens kan produceren in debug-uitvoeringen en 300 seconden in de uitvoering van taken.
- **Oorzaak**: Broadcast heeft een standaardtime-out van 60 seconden in debug-uitvoeringen en 300 seconden in taak uitvoeringen. Op de broadcast-koppeling is de stroom die u hebt gekozen voor broadcast te groot voor het produceren van gegevens binnen deze limiet. Als een broadcast-koppeling niet wordt gebruikt, kan de standaard broadcast op stroom gegevens dezelfde limiet bereiken.
- **Aanbeveling**: Schakel de optie Broadcast uit of Vermijd het uitzenden van grote gegevens stromen waarvoor de verwerking meer dan 60 seconden kan duren. Kies een kleinere stroom om te broadcasten. Grote Azure SQL Data Warehouse tabellen en bron bestanden zijn doorgaans geen goede keuze. Als er geen broadcast wordt toegevoegd, gebruikt u een groter cluster als deze fout optreedt.

### <a name="error-code-df-executor-conversion"></a>Fout code: DF-uitvoeringen-conversie

- **Bericht**: converteren naar een datum of tijd is mislukt vanwege een ongeldig teken
- **Oorzaak**: de gegevens hebben niet de verwachte indeling.
- **Aanbeveling**: gebruik het juiste gegevens type.

### <a name="error-code-df-executor-invalidcolumn"></a>Fout code: DF-uitvoerder-InvalidColumn
- **Bericht**: de kolom naam moet worden opgegeven in de query, een alias instellen als u een SQL-functie gebruikt
- **Oorzaak**: er is geen kolom naam opgegeven.
- **Aanbeveling**: Stel een alias in als u een SQL-functie gebruikt zoals min () of Max ().

### <a name="error-code-df-executor-drivererror"></a>Fout code: DF-uitvoerder-DriverError
- **Bericht**: INT96 is een verouderd tijds tempel type dat niet wordt ondersteund door de ADF-gegevens stroom. Overweeg het kolom Type bij te werken naar de meest recente typen.
- **Oorzaak**: stuurprogrammafout.
- **Aanbeveling**: INT96 is een verouderd tijds tempel type dat niet wordt ondersteund door Azure Data Factory gegevens stroom. Overweeg het kolom Type bij te werken naar het meest recente type.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Fout code: DF-uitvoerder-BlockCountExceedsLimitError
- **Bericht**: het niet-toegewezen blok aantal mag de maximum limiet van 100.000 blokken niet overschrijden. Controleer de BLOB-configuratie.
- **Oorzaak**: het maximum aantal niet-toegewezen blokken in een blob is 100.000.
- **Aanbeveling**: Neem contact op met het product team van micro soft voor meer informatie over dit probleem.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Fout code: DF-uitvoerder-PartitionDirectoryError
- **Bericht**: het opgegeven bronpad heeft meerdere gepartitioneerde directory's (bijvoorbeeld <Source Path> /<partitie Root Directory 1>/a = 10/b = 20, <Source Path> /<partition root directory 2>/c = 10/d = 30) of gepartitioneerde directory met een ander bestand of een niet-gepartitioneerde directory (bijvoorbeeld <Source Path> /<partitie root directory 1>/a = 10/b = 20, <Source Path> /Directory 2/bestand1), verwijdert u de partitie basis directory van het bronpad en leest u deze via een afzonderlijke bron transformatie.
- **Oorzaak**: het bronpad heeft meerdere gepartitioneerde directory's of een gepartitioneerde map met een ander bestand of een niet-gepartitioneerde directory.
- **Aanbeveling**: Verwijder de gepartitioneerde hoofdmap van het bronpad en lees deze via een afzonderlijke bron transformatie.

### <a name="error-code-df-executor-invalidtype"></a>Fout code: DF-uitvoerder-InvalidType
- **Bericht**: Controleer of het type para meter overeenkomt met het type van de waarde die is door gegeven. Het door geven van float-para meters van pijp lijnen wordt momenteel niet ondersteund.
- **Oorzaak**: het gegevens type voor het gedeclareerde type is niet compatibel met de werkelijke parameter waarde.
- **Aanbeveling**: Controleer of de parameter waarden die zijn door gegeven aan de gegevens stroom overeenkomen met het gedeclareerde type.

### <a name="error-code-df-executor-parseerror"></a>Fout code: DF-uitvoerder-ParseError
- **Bericht**: expressie kan niet worden geparseerd
- **Oorzaak**: er is een expressie gegenereerd met het parseren van fouten vanwege een onjuiste opmaak.
- **Aanbeveling**: Controleer de opmaak in de expressie.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Fout code: DF-uitvoerder-SystemImplicitCartesian
- **Bericht**: impliciet Cartesisch product voor Inner join wordt niet ondersteund. gebruik in plaats daarvan cross join. Kolommen die in de samen voeging worden gebruikt, moeten een unieke sleutel voor rijen maken.
- **Oorzaak**: impliciet Cartesisch product voor Inner joins tussen logische plannen worden niet ondersteund. Als u kolommen in de samen voeging gebruikt, maakt u een unieke sleutel.
- **Aanbeveling**: voor niet-gelijkheid gebaseerde samen voegingen gebruikt u cross-koppeling.

### <a name="error-code-getcommand-outputasync-failed"></a>Fout code: GetCommand OutputAsync is mislukt
- **Bericht**: tijdens fout opsporing en voor beeld van gegevens stroom: GetCommand OutputAsync is mislukt met...
- **Oorzaak**: dit is een fout van de back-end-service. 
- **Aanbeveling**: Voer de bewerking opnieuw uit en start de foutopsporingssessie opnieuw. Als het probleem niet wordt opgelost door het opnieuw te proberen en opnieuw op te starten, neemt u contact op met de klant ondersteuning. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Fout code: DF-uitvoerder-OutOfMemoryError
 
- **Bericht**: er is een fout opgetreden tijdens het uitvoeren van het cluster. Probeer het opnieuw met een Integration runtime met grotere kern aantallen en/of geoptimaliseerd voor geheugen
- **Oorzaak: er** is onvoldoende geheugen op het cluster.
- **Aanbeveling**: het oplossen van problemen met clusters is bedoeld voor ontwikkeling. Gebruik gegevens steekproef en een geschikt reken type en grootte om de payload uit te voeren. Zie prestatie handleiding voor het [toewijzen van gegevens stromen](concepts-data-flow-performance.md)voor tips voor betere prestaties.

### <a name="error-code-df-executor-illegalargument"></a>Fout code: DF-uitvoerder-illegalArgument

- **Bericht**: Controleer of de toegangs sleutel in de gekoppelde service juist is
- **Oorzaak**: de account naam of de toegangs sleutel is onjuist.
- **Aanbeveling**: Controleer of de account naam of de toegangs sleutel die is opgegeven in de gekoppelde service juist is. 

### <a name="error-code-df-executor-columnunavailable"></a>Fout code: DF-uitvoerder-ColumnUnavailable
- **Bericht**: de kolom naam die in de expressie wordt gebruikt, is niet beschikbaar of is ongeldig.
- **Oorzaak**: er wordt een ongeldige of niet-beschik bare kolom naam gebruikt in een expressie.
- **Aanbeveling**: Controleer de kolom namen die worden gebruikt in expressies.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Fout code: DF-uitvoerder-OutOfDiskSpaceError
- **Bericht**: interne server fout
- **Oorzaak: er** is onvoldoende schijf ruimte op het cluster.
- **Aanbeveling**: Voer de pijp lijn opnieuw uit. Als u dit probleem niet oplost, neemt u contact op met de klant ondersteuning.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Fout code: DF-uitvoerder-StoreIsNotDefined
- **Bericht**: de archief configuratie is niet gedefinieerd. Deze fout wordt mogelijk veroorzaakt door een ongeldige parameter toewijzing in de pijp lijn.
- **Oorzaak**: onbepaald.
- **Aanbeveling**: Controleer de toewijzing van de parameter waarde in de pijp lijn. Een parameter expressie kan ongeldige tekens bevatten.


### <a name="error-code-4502"></a>Fout code: 4502
- **Bericht**: er zijn belang rijke gelijktijdige MappingDataflow-uitvoeringen die storingen veroorzaken vanwege het beperken van beperkingen onder Integration runtime.
- **Oorzaak**: een groot aantal uitvoeringen van de gegevens stroom activiteit wordt gelijktijdig uitgevoerd op de Integration runtime. Zie [Azure Data Factory limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)voor meer informatie.
- **Aanbeveling**: als u meer gegevens stroom activiteiten parallel wilt uitvoeren, moet u deze verdelen over meerdere integratie-Runtimes.


### <a name="error-code-invalidtemplate"></a>Fout code: InvalidTemplate
- **Bericht**: de pijplijn expressie kan niet worden geëvalueerd.
- **Oorzaak**: de pijplijn expressie die wordt door gegeven in de gegevens stroom activiteit, wordt niet correct verwerkt vanwege een syntaxis fout.
- **Aanbeveling**: Controleer uw activiteiten in activiteiten controleren om de expressie te controleren.

### <a name="error-code-2011"></a>Fout code: 2011
- **Bericht**: de activiteit is uitgevoerd op Azure Integration runtime en het ontsleutelen van de referentie van de gegevens opslag of COMPUTE die is verbonden via een zelf-hosted Integration runtime is mislukt. Controleer de configuratie van gekoppelde services die zijn gekoppeld aan deze activiteit en zorg ervoor dat u het juiste type Integration runtime gebruikt.
- **Oorzaak**: gegevens stroom biedt geen ondersteuning voor gekoppelde services op zelf-hostende Integration Runtimes.
- **Aanbeveling**: gegevens stroom configureren om te worden uitgevoerd op een beheerde Virtual Network Integration runtime.

### <a name="error-code-df-xml-invalidvalidationmode"></a>Fout code: DF-XML-InvalidValidationMode
- **Bericht**: er is een ongeldige XML-validatie modus gegeven.
- **Aanbeveling**: Controleer de waarde van de para meter en geef de juiste validatie modus op.

### <a name="error-code-df-xml-invaliddatafield"></a>Fout code: DF-XML-InvalidDataField
- **Bericht**: het veld voor beschadigde records moet een teken reeks type en Null-waarden zijn.
- **Aanbeveling**: Zorg ervoor dat de kolom `\"_corrupt_record\"` in het bron project een teken reeks gegevens type heeft.

### <a name="error-code-df-xml-malformedfile"></a>Fout code: DF-XML-MalformedFile
- **Bericht**: onjuist gevormd XML-bestand in FailFastMode.
- **Aanbeveling**: werk de inhoud van het XML-bestand naar de juiste indeling.

### <a name="error-code-df-xml-invaliddatatype"></a>Fout code: DF-XML-InvalidDataType
- **Bericht**: XML-element heeft sub-elementen of kenmerken en kan niet worden geconverteerd.

### <a name="error-code-df-xml-invalidreferenceresource"></a>Fout code: DF-XML-InvalidReferenceResource
- **Bericht**: verwijzings bron in het XML-gegevens bestand kan niet worden omgezet.
- **Aanbeveling**: Controleer de verwijzings bron in het XML-gegevens bestand.

### <a name="error-code-df-xml-invalidschema"></a>Fout code: DF-XML-InvalidSchema
- **Bericht**: schema validatie mislukt.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Fout code: DF-XML-UnsupportedExternalReferenceResource
- **Bericht**: externe verwijzings bron in XML-gegevens bestand wordt niet ondersteund.
- **Aanbeveling**: werk de inhoud van het XML-bestand bij wanneer de externe referentie bron momenteel niet wordt ondersteund.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Fout code: DF-GEN2-InvalidAccountConfiguration
- **Bericht**: een van de account sleutel of Tenant/SpnId/SpnCredential/SpnCredentialType of MiServiceUri/miServiceToken moet worden opgegeven.
- **Aanbeveling**: Configureer het juiste account in de gerelateerde GEN2 gekoppelde service.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>Fout code: DF-GEN2-InvalidAuthConfiguration
- **Bericht**: slechts een van de drie verificatie methoden (Key, SERVICEPRINCIPAL en mi) kan worden opgegeven. 
- **Aanbeveling**: Kies het juiste verificatie type in de gerelateerde GEN2 gekoppelde service.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Fout code: DF-GEN2-InvalidServicePrincipalCredentialType
- **Bericht**: ServicePrincipalCredentialType is ongeldig.

### <a name="error-code-df-gen2-invaliddatatype"></a>Fout code: DF-GEN2-InvalidDataType
- **Bericht**: het Cloud type is ongeldig.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>Fout code: DF-BLOB-InvalidAccountConfiguration
- **Bericht**: een van de account sleutel of sas_token moet worden opgegeven.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>Fout code: DF-BLOB-InvalidAuthConfiguration
- **Bericht**: slechts een van de twee verificatie methoden (sleutel, sa's) kan worden opgegeven.

### <a name="error-code-df-blob-invaliddatatype"></a>Fout code: DF-BLOB-InvalidDataType
- **Bericht**: het Cloud type is ongeldig.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>Fout code: DF-Cosmos-PartitionKeyMissed
- **Bericht**: pad voor partitie sleutel moet worden opgegeven voor bijwerk-en verwijder bewerkingen.
- **Aanbeveling**: gebruik de sleutel voor het leveren van partities in Cosmos Sink-instellingen.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Fout code: DF-Cosmos-InvalidPartitionKey
- **Bericht**: het pad van de partitie sleutel mag niet leeg zijn voor bijwerk-en verwijder bewerkingen.
- **Aanbeveling**: gebruik de sleutel voor het leveren van partities in Cosmos Sink-instellingen.

### <a name="error-code-df-cosmos-idpropertymissed"></a>Fout code: DF-Cosmos-IdPropertyMissed
- **Bericht**: de id-eigenschap moet worden toegewezen voor Delete-en update-bewerkingen.
- **Aanbeveling**: Zorg ervoor dat de invoer gegevens een `id` kolom in Cosmos Sink-instellingen bevat. Als dat niet het geval is, gebruikt u **trans formatie selecteren of afleiden** voor het genereren van deze kolom vóór sink.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Fout code: DF-Cosmos-InvalidPartitionKeyContent
- **Bericht**: de partitie sleutel moet beginnen met/.
- **Aanbeveling**: Zorg ervoor dat de partitie sleutel begint met `/` in Cosmos Sink-instellingen, bijvoorbeeld: `/movieId` .

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>Fout code: DF-Cosmos-InvalidPartitionKey
- **Bericht**: partitionKey niet toegewezen in Sink voor Delete-en update-bewerkingen.
- **Aanbeveling**: gebruik in Cosmos Sink-instellingen de partitie sleutel die gelijk is aan de partitie sleutel van uw container.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>Fout code: DF-Cosmos-InvalidConnectionMode
- **Bericht**: ongeldige connectionMode.
- **Aanbeveling**: Controleer of de ondersteunde modus **Gateway** en **DirectHttps** in Cosmos-instellingen is.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Fout code: DF-Cosmos-InvalidAccountConfiguration
- **Bericht**: AccountName of accountEndpoint moet worden opgegeven.

### <a name="error-code-df-github-writenotsupported"></a>Fout code: DF-github-WriteNotSupported
- **Bericht**: github Store staat geen schrijf bewerkingen toe.

### <a name="error-code-df-pgsql-invalidcredential"></a>Fout code: DF-PGSQL-InvalidCredential
- **Bericht**: er moet een gebruiker/wacht woord worden opgegeven.
- **Aanbeveling**: Zorg ervoor dat u de juiste referentie-instellingen hebt in de gerelateerde postgresql gekoppelde service.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Fout code: DF-sneeuw-InvalidStageConfiguration
- **Bericht**: alleen Blob-opslag type kan worden gebruikt als fase in de lees-en schrijf bewerking sneeuw vlokken.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>Fout code: DF-sneeuw-InvalidStageConfiguration
- **Bericht**: eigenschappen van sneeuw-fase moeten worden opgegeven met Azure Blob + SAS-verificatie.

### <a name="error-code-df-snowflake-invaliddatatype"></a>Fout code: DF-sneeuw-InvalidDataType
- **Bericht**: het Spark-type wordt niet ondersteund in sneeuw.
- **Aanbeveling**: gebruik de **afgeleide trans formatie** om de gerelateerde kolom met invoer gegevens te wijzigen in het teken reeks type vóór sneeuw sink. 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Fout code: DF-Hive-InvalidBlobStagingConfiguration
- **Bericht**: er moeten eigenschappen voor de faserings opslag van blobs worden opgegeven.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Fout code: DF-Hive-InvalidGen2StagingConfiguration
- **Bericht**: ADLS Gen2 opslag faseert alleen de referentie van de Service-Principal-sleutel.
- **Aanbeveling**: Controleer of u de referenties voor de Service-Principal-sleutel toepast in de ADLS Gen2 gekoppelde service die wordt gebruikt als fase ring.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Fout code: DF-Hive-InvalidGen2StagingConfiguration
- **Bericht**: er moeten ADLS Gen2 opslag voor de faserings eigenschappen worden opgegeven. Een van de sleutel of Tenant/spnId/spnKey of miServiceUri/miServiceToken is vereist.
- **Aanbeveling**: pas de juiste referentie toe die wordt gebruikt als fase ring in de component in de gerelateerde ADLS Gen2 gekoppelde service. 

### <a name="error-code-df-hive-invaliddatatype"></a>Fout code: DF-Hive-InvalidDataType
- **Bericht**: niet-ondersteunde kolom (men).
- **Aanbeveling**: werk de kolom met invoer gegevens bij zodat deze overeenkomt met het gegevens type dat door de Hive wordt ondersteund.

### <a name="error-code-df-hive-invalidstoragetype"></a>Fout code: DF-Hive-InvalidStorageType
- **Bericht**: het opslag type kan BLOB of Gen2 zijn.

### <a name="error-code-df-delimited-invalidconfiguration"></a>Fout code: DF-Unlimited-InvalidConfiguration
- **Bericht**: een van de lege regels of de aangepaste header moet worden opgegeven.
- **Aanbeveling**: lege regels of aangepaste headers opgeven in CSV-instellingen.

### <a name="error-code-df-delimited-columndelimitermissed"></a>Fout code: DF-Unlimited-ColumnDelimiterMissed
- **Bericht**: kolom scheidings teken is vereist voor parseren.
- **Aanbeveling**: Bevestig dat u het kolom scheidings teken in de CSV-instellingen hebt.

### <a name="error-code-df-mssql-invalidcredential"></a>Fout code: DF-MSSQL-InvalidCredential
- **Bericht**: er moet een gebruiker/PWD of Tenant/SpnId/SpnKey of MiServiceUri/miServiceToken worden opgegeven.
- **Aanbeveling**: juiste referenties in de gerelateerde mssqlve gekoppelde service Toep assen.

### <a name="error-code-df-mssql-invaliddatatype"></a>Fout code: DF-MSSQL-InvalidDataType
- **Bericht**: niet-ondersteund veld (en).
- **Aanbeveling**: Wijzig de invoer gegevens kolom zodat deze overeenkomt met het gegevens type dat door MSSQL wordt ondersteund.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>Fout code: DF-MSSQL-InvalidAuthConfiguration
- **Bericht**: slechts een van de drie verificatie methoden (Key, SERVICEPRINCIPAL en mi) kan worden opgegeven.
- **Aanbeveling**: u kunt slechts een van de drie verificatie methoden (Key, SERVICEPRINCIPAL en mi) opgeven in de gerelateerde mssqlve gekoppelde service.

### <a name="error-code-df-mssql-invalidcloudtype"></a>Fout code: DF-MSSQL-InvalidCloudType
- **Bericht**: het Cloud type is ongeldig.
- **Aanbeveling**: Controleer het type Cloud in de verwante gekoppelde service van MSSQL.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Fout code: DF-SQLDW-InvalidBlobStagingConfiguration
- **Bericht**: er moeten eigenschappen voor de faserings opslag van blobs worden opgegeven.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>Fout code: DF-SQLDW-InvalidStorageType
- **Bericht**: het opslag type kan BLOB of Gen2 zijn.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Fout code: DF-SQLDW-InvalidGen2StagingConfiguration
- **Bericht**: ADLS Gen2 opslag faseert alleen de referentie van de Service-Principal-sleutel.

### <a name="error-code-df-sqldw-invalidconfiguration"></a>Fout code: DF-SQLDW-InvalidConfiguration
- **Bericht**: er moeten ADLS Gen2 opslag voor de faserings eigenschappen worden opgegeven. Een van de sleutels of Tenant/spnId/spnCredential/spnCredentialType of miServiceUri/miServiceToken is vereist.

### <a name="error-code-df-delta-invalidconfiguration"></a>Fout code: DF-DELTA-InvalidConfiguration
- **Bericht**: tijds tempel en versie kunnen niet tegelijk worden ingesteld.

### <a name="error-code-df-delta-keycolumnmissed"></a>Fout code: DF-DELTA-KeyColumnMissed
- **Bericht**: er moeten sleutel kolom (men) worden opgegeven voor niet-invoeg bewerkingen.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>Fout code: DF-DELTA-InvalidTableOperationSettings
- **Bericht**: de opties voor opnieuw maken en afkappen kunnen niet beide worden opgegeven.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>Fout code: DF-Excel-WorksheetConfigMissed
- **Bericht**: er is een Excel-blad naam of-index vereist.
- **Aanbeveling**: Controleer de waarde van de para meter en geef de blad naam of index op om de Excel-gegevens te lezen.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Fout code: DF-Excel-InvalidWorksheetConfiguration
- **Bericht**: er kan niet tegelijkertijd een Excel-blad naam en-index bestaan.
- **Aanbeveling**: Controleer de waarde van de para meter en geef de blad naam of index op om de Excel-gegevens te lezen.

### <a name="error-code-df-excel-invalidrange"></a>Fout code: DF-Excel-InvalidRange
- **Bericht**: er is een ongeldig bereik verstrekt.
- **Aanbeveling**: Controleer de waarde van de para meter en geef het geldige bereik op met behulp van de volgende verwijzing: [Excel-indeling in azure data Factory-Dataset-eigenschappen](./format-excel.md#dataset-properties).

### <a name="error-code-df-excel-worksheetnotexist"></a>Fout code: DF-Excel-WorksheetNotExist
- **Bericht**: Excel-werk blad bestaat niet.
- **Aanbeveling**: Controleer de waarde van de para meter en geef de geldige blad naam of index op om de Excel-gegevens te lezen.

### <a name="error-code-df-excel-differentschemanotsupport"></a>Fout code: DF-Excel-DifferentSchemaNotSupport
- **Bericht**: Excel-bestanden met een ander schema lezen wordt momenteel niet ondersteund.

### <a name="error-code-df-excel-invaliddatatype"></a>Fout code: DF-Excel-InvalidDataType
- **Bericht**: gegevens type wordt niet ondersteund.

### <a name="error-code-df-excel-invalidfile"></a>Fout code: DF-Excel-InvalidFile
- **Bericht**: er is een ongeldig Excel-bestand ingevoerd terwijl alleen. XLSX en. xls worden ondersteund.

### <a name="error-code-df-adobeintegration-invalidmaptofilter"></a>Fout code: DF-AdobeIntegration-InvalidMapToFilter
- **Bericht**: er kan slechts één sleutel/id aan het filter zijn toegewezen voor de aangepaste resource.

### <a name="error-code-df-adobeintegration-invalidpartitionconfiguration"></a>Fout code: DF-AdobeIntegration-InvalidPartitionConfiguration
- **Bericht**: er wordt slechts één partitie ondersteund. Het partitie schema kan RoundRobin of hash zijn.
- **Aanbeveling**: in AdobeIntegration instellingen, bevestig dat u slechts één partitie hebt. Het partitie schema kan RoundRobin of hash zijn.

### <a name="error-code-df-adobeintegration-keycolumnmissed"></a>Fout code: DF-AdobeIntegration-KeyColumnMissed
- **Bericht**: sleutel moet worden opgegeven voor niet-invoeg bewerkingen.
- **Aanbeveling**: Geef uw sleutel kolommen op in de AdobeIntegration-instellingen voor niet-invoeg bewerkingen.

### <a name="error-code-df-adobeintegration-invalidpartitiontype"></a>Fout code: DF-AdobeIntegration-InvalidPartitionType
- **Bericht**: partitie type moet roundRobin zijn.
- **Aanbeveling**: Bevestig dat het partitie type RoundRobin in AdobeIntegration-instellingen is.

### <a name="error-code-df-adobeintegration-invalidprivacyregulation"></a>Fout code: DF-AdobeIntegration-InvalidPrivacyRegulation
- **Bericht**: er wordt momenteel alleen een privacybeleid ondersteund dat is AVG.
- **Aanbeveling**: Bevestig dat de privacyverklaring in AdobeIntegration-instellingen is ingesteld op **' AVG '**.

## <a name="miscellaneous-troubleshooting-tips"></a>Diverse tips voor probleem oplossing
- **Probleem**: er is een onverwachte uitzonde ring opgetreden en de uitvoering is mislukt.
    - **Bericht**: tijdens de uitvoering van de gegevens stroom activiteit: er is een onverwachte uitzonde ring opgetreden en uitvoering mislukt.
    - **Oorzaak**: dit is een fout van de back-end-service. Voer de bewerking opnieuw uit en start de foutopsporingssessie opnieuw.
    - **Aanbeveling**: als u het probleem niet kunt oplossen door het opnieuw te proberen en opnieuw op te starten, neemt u contact op met de klant ondersteuning.

-  **Probleem**: geen uitvoer gegevens tijdens de samen voeging tijdens de preview-versie van fout opsporing.
    - **Bericht**: er zijn een groot aantal null-waarden of ontbrekende waarden die kunnen worden veroorzaakt doordat er te weinig rijen worden steek proeven. Probeer de limiet voor het aantal foutopsporingslogboeken bij te werken en de gegevens te vernieuwen.
    - **Oorzaak**: de samenvoegings voorwaarde komt niet overeen met rijen of resulteert in een groot aantal null-waarden tijdens de voorbeeld weergave van de gegevens.
    - **Aanbeveling**: Verhoog het aantal rijen in de limiet voor de bron rijen in **instellingen voor fout opsporing**. Zorg ervoor dat u een Azure IR selecteert dat een gegevensstroom cluster heeft dat groot genoeg is voor het verwerken van meer gegevens.
    
- **Probleem**: validatie fout bij bron met CSV-bestanden met meerdere regels. 
    - **Bericht**: er wordt mogelijk een van de volgende fout berichten weer gegeven:
        - De laatste kolom is null of ontbreekt.
        - Schema validatie bij bron mislukt.
        - Het importeren van het schema kan niet juist worden weer gegeven in de UX en de laatste kolom heeft een nieuw regel teken in de naam.
    - **Oorzaak**: in de stroom van de toewijzings gegevens werken de CSV-bron bestanden voor meerdere regels momenteel niet wanneer \r\n wordt gebruikt als het scheidings teken voor de rij. Soms kunnen extra regels bij regel terugloop fouten veroorzaken. 
    - **Aanbeveling**: Genereer het bestand in de bron met behulp van \n als scheidings teken in plaats van \r\n. Of gebruik de Kopieer activiteit om het CSV-bestand te converteren om \n als een scheidings teken voor een rij te gebruiken.

## <a name="general-troubleshooting-guidance"></a>Algemene richt lijnen voor probleem oplossing
1. Controleer de status van uw gegevensset-verbindingen. Ga in elke bron-en Sink-trans formatie naar de gekoppelde service voor elke gegevensset die u gebruikt en test de verbindingen.
2. Controleer de status van uw bestands-en tabel verbindingen in de ontwerp functie voor gegevens stromen. Selecteer in de foutopsporingsmodus de optie **gegevens voorbeeld** op de bron transformaties om ervoor te zorgen dat u toegang hebt tot uw gegevens.
3. Als alles goed in de voorbeeld weergave van gegevens voor komt, gaat u naar de ontwerp functie voor pijp lijnen en plaatst u uw gegevens stroom in een pijplijn activiteit. Fout opsporing voor de pijp lijn voor een end-to-end-test.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende bronnen voor meer informatie over het oplossen van problemen:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
