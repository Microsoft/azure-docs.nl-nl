---
title: 'Azure Data Factory: veelgestelde vragen '
description: Krijg antwoorden op veelgestelde vragen over Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 2d1b9f0e25c460b7f26c31c3d2c6ebe51d958017
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485010"
---
# <a name="azure-data-factory-faq"></a>Veelgestelde vragen over Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Data Factory.  

## <a name="what-is-azure-data-factory"></a>Wat is Azure Data Factory? 
Data Factory is een volledig beheerde ETL-service die is gebaseerd op de Cloud en gegevens integratie waarmee de verplaatsing en trans formatie van gegevens wordt geautomatiseerd. Net als een fabriek die apparatuur uitvoert om onbewerkte materialen te transformeren in gerede producten, Azure Data Factory de bestaande services die onbewerkte gegevens verzamelen en transformeren naar gebruiks klare informatie. 

Met behulp van Azure Data Factory kunt u gegevensgestuurde werk stromen maken om gegevens te verplaatsen tussen on-premises en gegevens opslag in de Cloud. En u kunt gegevens verwerken en transformeren met gegevens stromen. ADF biedt ook ondersteuning voor externe Compute-engines voor trans formaties die zijn gecodeerd met behulp van reken services zoals Azure HDInsight, Azure Databricks en de Integration runtime van SQL Server Integration Services (SSIS). 

Met Data Factory kunt u uw gegevens verwerking uitvoeren op een Azure-Cloud service of in uw eigen gehoste Compute-omgeving, zoals SSIS, SQL Server of Oracle. Nadat u een pijp lijn hebt gemaakt die de actie uitvoert die u nodig hebt, kunt u plannen dat deze regel matig wordt uitgevoerd (bijvoorbeeld elk uur, dagelijks of wekelijks), tijd venster planning of de pijp lijn activeren vanuit een gebeurtenis voorval. Zie voor meer informatie [Inleiding tot Azure Data Factory](introduction.md).

### <a name="control-flows-and-scale"></a>Stroom beheer en schalen 
Ter ondersteuning van de diverse integratie stromen en-patronen in het moderne Data Warehouse biedt Data Factory flexibele gegevens pijplijn modellen. Dit houdt in de volledige controle stroom programmerings modellen, waaronder voorwaardelijke uitvoering, vertakking in gegevens pijplijnen en de mogelijkheid om expliciet para meters te geven binnen en tussen deze stromen. Beheer stroom omvat ook het transformeren van gegevens via activiteiten verzending naar externe uitvoerings engines en gegevens stroom mogelijkheden, inclusief gegevens verplaatsing op schaal, via de Kopieer activiteit.

Data Factory biedt vrijheid om alle stroom stijlen te model leren die vereist zijn voor gegevens integratie en die op aanvraag of herhaaldelijk op basis van een planning kunnen worden verzonden. Enkele veelvoorkomende stromen die dit model mogelijk maakt:   

- Controle stromen:
    - Activiteiten kunnen samen worden gekoppeld in een volg orde binnen een pijp lijn.
    - Activiteiten kunnen in een pijp lijn worden vertakking.
    - Parameters:
        - Para meters kunnen worden gedefinieerd op pijplijn niveau en de argumenten kunnen worden door gegeven terwijl u de pijp lijn op aanvraag of vanuit een trigger aanroept.
        - Activiteiten kunnen de argumenten die zijn doorgegeven aan de pijplijn gebruiken.
    - Aangepaste status door geven:
        - Uitvoer van activiteiten, inclusief status, kunnen worden gebruikt door een volgende activiteit in de pijp lijn.
    - Herhalings containers:
        - Met de foreach-activiteit wordt een opgegeven verzameling activiteiten in een lus herhaald. 
- Op Triggers gebaseerde stromen:
    - Pijp lijnen kunnen op aanvraag worden geactiveerd of op basis van de klok tijd.
- Delta stromen:
    - Para meters kunnen worden gebruikt voor het definiëren van uw bovengrens voor Delta kopieën terwijl dimensie-of verwijzings tabellen uit een relationele archief worden verplaatst, hetzij on-premises als in de Cloud, om de gegevens in het Lake te laden. 

Zie [zelf studie: controle stromen](tutorial-control-flow.md)voor meer informatie.

### <a name="data-transformed-at-scale-with-code-free-pipelines"></a>Gegevens die zijn getransformeerd op schaal met gratis pijp lijnen van code
De nieuwe, op de browser gebaseerde ervaring biedt gratis pijplijn ontwerpen en implementaties met een moderne, interactieve webervaring.

Voor ontwikkel aars en gegevens technici van Visual Data is de Data Factory-webinterface de ontwerp omgeving zonder code die u gaat gebruiken om pijp lijnen te bouwen. Het is volledig geïntegreerd met Visual Studio online Git en biedt integratie voor CI/CD en iteratieve ontwikkeling met opties voor fout opsporing.

### <a name="rich-cross-platform-sdks-for-advanced-users"></a>Uitgebreide platformoverschrijdende Sdk's voor geavanceerde gebruikers
Data Factory v2 biedt een uitgebreide set Sdk's die kan worden gebruikt voor het ontwerpen, beheren en bewaken van pijp lijnen met behulp van uw favoriete IDE, waaronder:
* Python-SDK
* Power shell CLI
* C# SDK

Gebruikers kunnen ook de gedocumenteerde REST-Api's gebruiken voor de interface met Data Factory v2.

### <a name="iterative-development-and-debugging-by-using-visual-tools"></a>Iteratieve ontwikkeling en fout opsporing met behulp van visuele hulp middelen
Azure Data Factory visuele hulp middelen kunnen iteratieve ontwikkeling en fout opsporing. U kunt uw pijp lijnen maken en test uitvoeringen uitvoeren met behulp van de functie voor **fout opsporing** in het pijplijn doek zonder dat u maar één regel code hoeft te schrijven. U kunt de resultaten van uw test uitvoeringen weer geven in het **uitvoer** venster van uw pijplijn doek. Nadat de test uitvoering is voltooid, kunt u meer activiteiten aan uw pijp lijn toevoegen en de fout opsporing op een iteratieve manier voortzetten. U kunt uw test uitvoeringen ook annuleren nadat deze worden uitgevoerd. 

U bent niet verplicht uw wijzigingen te publiceren naar de data factory-service voordat u **debug** selecteert. Dit is handig in scenario's waarin u er zeker van wilt zijn dat de nieuwe toevoegingen of wijzigingen werken zoals verwacht voordat u uw data factory-werk stromen bijwerkt in ontwikkel-, test-of productie omgevingen. 

### <a name="ability-to-deploy-ssis-packages-to-azure"></a>Mogelijkheid om SSIS-pakketten te implementeren in azure 
Als u uw SSIS-workloads wilt verplaatsen, kunt u een Data Factory maken en een Azure SSIS Integration runtime inrichten. Een Azure SSIS Integration runtime is een volledig beheerd cluster met virtuele Azure-machines (knoop punten) die zijn toegewezen om uw SSIS-pakketten uit te voeren in de Cloud. Zie voor stapsgewijze instructies de zelf studie [SSIS-pakketten implementeren in azure](./tutorial-deploy-ssis-packages-azure.md) . 
 
### <a name="sdks"></a>SDK's
Als u een ervaren gebruiker bent en op zoek bent naar een programmatische interface, biedt Data Factory een uitgebreide set Sdk's die u kunt gebruiken om pijp lijnen te ontwerpen, beheren of bewaken met behulp van uw favoriete IDE. Taal ondersteuning omvat .NET, Power shell, python en REST.

### <a name="monitoring"></a>Bewaking
U kunt uw gegevens fabrieken bewaken via Power shell, SDK of de visuele controle Hulpprogramma's in de gebruikers interface van de browser. U kunt op aanvraag, op basis van triggers en op tijd gebaseerde aangepaste stromen op een efficiënte en efficiënte manier bewaken en beheren. Annuleer bestaande taken, Bekijk fouten in een oogopslag, zoom op om gedetailleerde fout berichten op te halen en los de problemen op, in één venster van glas zonder tussen komst te scha kelen of te navigeren tussen schermen. 

### <a name="new-features-for-ssis-in-data-factory"></a>Nieuwe functies voor SSIS in Data Factory
Sinds de eerste open bare preview-versie in 2017, heeft Data Factory de volgende functies voor SSIS toegevoegd:

-    Ondersteuning voor drie meer configuraties/varianten van Azure SQL Database voor het hosten van de SSIS-data base (SSISDB) van projecten/pakketten:
-    SQL Database met Service-eind punten voor virtueel netwerk
-    SQL Managed Instance
-    Elastische pool
-    Ondersteuning voor een Azure Resource Manager virtueel netwerk boven op een klassiek virtueel netwerk dat u in de toekomst kunt verouderd, waarmee u uw Azure SSIS Integration runtime kunt injecteren/samen voegen met een virtueel netwerk dat is geconfigureerd voor SQL Database met virtuele netwerk service-eind punten/storing/on-premises gegevens toegang. Zie ook [een Azure SSIS Integration runtime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.
-    Ondersteuning voor Azure Active Directory (Azure AD)-verificatie en SQL-verificatie om verbinding te maken met de SSISDB, zodat Azure AD-verificatie mogelijk is met uw Data Factory beheerde identiteit voor Azure-resources
-    Ondersteuning voor het meebrengen van uw bestaande SQL Server-licentie om aanzienlijke kosten besparingen te verdienen via de Azure Hybrid Benefit optie
-    Ondersteuning voor Enter prise Edition van de Azure-SSIS Integration runtime waarmee u geavanceerde/Premium-functies, een aangepaste installatie-interface kunt gebruiken om extra onderdelen/uitbrei dingen en een partner ecosysteem te installeren. Zie ook [Enter prise Edition, Custom Setup en uitbreid baarheid van derden voor SSIS in ADF](https://blogs.msdn.microsoft.com/ssis/2018/04/27/enterprise-edition-custom-setup-and-3rd-party-extensibility-for-ssis-in-adf/)voor meer informatie. 
-    Een diep gaande integratie van SSIS in Data Factory waarmee u eersteklas pakket activiteiten voor het uitvoeren van de eerste klasse kunt aanroepen/activeren in Data Factory pijp lijnen en deze plannen via SSMS. Zie ook [uw ETL/ELT-werk stromen moderniseren en uitbreiden met SSIS-activiteiten in ADF-pijp lijnen](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)voor meer informatie.


## <a name="what-is-the-integration-runtime"></a>Wat is de Integration runtime?
Integration runtime is de reken infrastructuur die Azure Data Factory gebruikt om de volgende mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen te bieden:

- **Gegevens verplaatsing**: voor gegevens verplaatsing verplaatst de Integration runtime de gegevens tussen de bron-en doel gegevens archieven, terwijl er ondersteuning wordt geboden voor ingebouwde connectors, indelings conversie, kolom toewijzing en uitvoering van en schaal bare gegevens overdracht.
- **Verzend activiteiten**: voor trans formatie biedt Integration runtime de mogelijkheid om systeem eigen SSIS-pakketten uit te voeren.
- **SSIS-pakketten uitvoeren**: de Integration runtime voert de systeem eigen SSIS-pakketten uit in een beheerde Azure Compute-omgeving. De Integration runtime biedt ook ondersteuning voor het verzenden en bewaken van transformatie activiteiten die worden uitgevoerd op diverse Compute-Services, zoals Azure HDInsight, Azure Machine Learning, SQL Database en SQL Server.

U kunt een of meer exemplaren van de Integration runtime zo nodig implementeren om gegevens te verplaatsen en te transformeren. De Integration runtime kan worden uitgevoerd op een openbaar netwerk van Azure of op een particulier netwerk (on-premises, Azure Virtual Network, of Amazon Web Services Virtual Private Cloud [VPC]). 

Zie voor meer informatie [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>Wat is de limiet voor het aantal Integration Runtimes?
Er is geen vaste limiet voor het aantal instanties van Integration runtime dat u kunt hebben in een data factory. Er is echter een limiet voor het aantal VM-kernen dat door de Integration runtime per abonnement kan worden gebruikt voor de uitvoering van SSIS-pakketten. Zie [Data Factory limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)voor meer informatie.

## <a name="what-are-the-top-level-concepts-of-azure-data-factory"></a>Wat zijn de belangrijkste concepten van Azure Data Factory?
Een Azure-abonnement kan een of meer Azure Data Factory-exemplaren (oftewel 'data factory's') hebben. Azure Data Factory bevat vier belang rijke onderdelen die samen werken als een platform waarop u gegevensgestuurde werk stromen kunt samen stellen met de stappen voor het verplaatsen en transformeren van gegevens.

### <a name="pipelines"></a>Pijplijnen
Een gegevensfactory kan één of meer pijplijnen hebben. Een pijp lijn is een logische groepering van activiteiten om een werk eenheid uit te voeren. De activiteiten in een pijplijn voeren samen een taak uit. Een pijp lijn kan bijvoorbeeld een groep activiteiten bevatten die gegevens uit een Azure-Blob opnemen en vervolgens een Hive-query uitvoeren op een HDInsight-cluster om de gegevens te partitioneren. Het voor deel is dat u een pijp lijn kunt gebruiken om de activiteiten te beheren als een set, in plaats van elke activiteit afzonderlijk te beheren. U kunt de activiteiten in een pijp lijn koppelen om ze opeenvolgend te laten werken of u kunt ze onafhankelijk van elkaar uitvoeren.

### <a name="data-flows"></a>Gegevensstromen
Gegevens stromen zijn objecten die u visueel bouwt in Data Factory die gegevens op de juiste schaal op backend Spark-Services transformeren. U hoeft geen inzicht te krijgen in Program meren of Spark-interne. U hoeft alleen maar uw gegevens transformatie intentie te ontwerpen met behulp van grafieken (toewijzing) of spread sheets (Wrangling).

### <a name="activities"></a>Activiteiten
Activiteiten vertegenwoordigen een verwerkingsstap in een pijplijn. U kunt bijvoorbeeld een Kopieer activiteit gebruiken om gegevens te kopiëren van het ene gegevens archief naar een ander gegevens archief. Op dezelfde manier kunt u een Hive-activiteit gebruiken, waarmee een Hive-query wordt uitgevoerd op een Azure HDInsight-cluster om uw gegevens te transformeren of te analyseren. Data Factory ondersteunt drie soorten activiteiten: activiteiten voor gegevensverplaatsing, activiteiten voor gegevenstransformatie en controleactiviteiten.

### <a name="datasets"></a>Gegevenssets
Gegevenssets vertegenwoordigen gegevensstructuren in de gegevensarchieven die simpelweg verwijzen naar de gegevens die u in uw activiteiten als in- of uitvoer wilt gebruiken. 

### <a name="linked-services"></a>Gekoppelde services
Gekoppelde services zijn te vergelijken met verbindingsreeksen, die de verbindingsinformatie bevatten die Data Factory nodig heeft om verbinding te maken met externe bronnen. U kunt het op deze manier beschouwen: een gekoppelde service definieert de verbinding met de gegevens bron en een gegevensset vertegenwoordigt de structuur van de gegevens. Een Azure Storage gekoppelde service specificeert bijvoorbeeld de connection string om verbinding te maken met het Azure Storage-account. En een Azure Blob-gegevensset bevat de BLOB-container en de map die de gegevens bevat.

Gekoppelde services hebben twee doelen in Data Factory:

- Voor het vertegenwoordigen van een *gegevens archief* met, maar niet beperkt tot, een SQL Server exemplaar, een Oracle data base-exemplaar, een bestands share of een Azure Blob Storage-account. Zie [activiteit kopiëren in azure Data Factory](copy-activity-overview.md)voor een lijst met ondersteunde gegevens archieven.
- Ter vertegenwoordiging van een *rekenresource* die de uitvoering van een activiteit kan hosten. De HDInsight Hive-activiteit wordt bijvoorbeeld uitgevoerd op een HDInsight Hadoop-cluster. Zie [gegevens transformeren in azure Data Factory](transform-data.md)voor een lijst met transformatie activiteiten en ondersteunde reken omgevingen.

### <a name="triggers"></a>Triggers
Triggers vertegenwoordigen de verwerkings eenheden die bepalen wanneer een pijplijn uitvoering is gestart. Er zijn verschillende soorten triggers voor verschillende soorten gebeurtenissen. 

### <a name="pipeline-runs"></a>Pijplijnuitvoeringen
Een pijplijn uitvoering is een instantie van een pijp lijn. Normaal gesp roken maakt u een pijp lijn-uitvoering door argumenten door te geven aan de para meters die zijn gedefinieerd in de pijp lijn. U kunt de argumenten hand matig of binnen de trigger definitie door geven.

### <a name="parameters"></a>Parameters
Para meters zijn sleutel-waardeparen in een alleen-lezen configuratie.U definieert para meters in een pijp lijn en geeft de argumenten voor de gedefinieerde para meters tijdens de uitvoering door vanuit een uitvoerings context. De uitvoerings context wordt gemaakt door een trigger of vanuit een pijp lijn die u hand matig uitvoert. Activiteiten binnen de pijplijn gebruiken de parameterwaarden.

Een gegevensset is een sterk getypeerde para meter en een entiteit die u opnieuw kunt gebruiken of waarnaar u moet verwijzen. Een activiteit kan verwijzen naar gegevens sets en kan de eigenschappen gebruiken die zijn gedefinieerd in de definitie van de gegevensset.

Een gekoppelde service is ook een sterk getypeerde para meter die verbindings informatie bevat voor een gegevens archief of een reken omgeving. Het is ook een entiteit die u kunt hergebruiken of ernaar verwijzen.

### <a name="control-flows"></a>Controle stromen
Met controle stromen worden pijplijn activiteiten gestroomd, waaronder het koppelen van activiteiten in een reeks, vertakkingen, para meters die u definieert op pijplijn niveau en de argumenten die u doorgeeft als u de pijp lijn op aanvraag of vanuit een trigger aanroept. Controle stromen omvatten ook aangepaste status door geven en herhalen van containers (dat wil zeggen, foreach-iterators).


Zie de volgende artikelen voor meer informatie over Data Factory-concepten:

- [Gegevenssets en gekoppelde services](concepts-datasets-linked-services.md)
- [Pijplijnen en activiteiten](concepts-pipelines-activities.md)
- [Integration Runtime](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Wat is het prijs model voor Data Factory?
Zie [Data Factory prijs informatie](https://azure.microsoft.com/pricing/details/data-factory/)voor Azure Data Factory prijs informatie.

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Hoe kan ik up-to-date blijven met informatie over Data Factory?
Ga voor de meest recente informatie over Azure Data Factory naar de volgende sites:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Start pagina van de documentatie](./index.yml)
- [Start pagina van het product](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Technisch diep gaande 

### <a name="how-can-i-schedule-a-pipeline"></a>Hoe kan ik een pijp lijn plannen? 
U kunt de trigger trigger voor scheduler of tijd venster gebruiken om een pijp lijn te plannen. De trigger maakt gebruik van een agenda schema met een muur klok, waarmee u de pijp lijnen periodiek of in op een kalender gebaseerde periodieke patronen kunt plannen (bijvoorbeeld op maandag om 6:00 uur en donderdag om 9:00 uur). Zie [Pijplijnen uitvoeren en triggers](concepts-pipeline-execution-triggers.md) voor meer informatie.

### <a name="can-i-pass-parameters-to-a-pipeline-run"></a>Kan ik para meters door geven aan de uitvoering van een pijp lijn?
Ja, para meters zijn een concept op het hoogste niveau in Data Factory. U kunt para meters definiëren op het niveau van de pijp lijn en argumenten door geven wanneer u de pijplijn uitvoering op aanvraag uitvoert of een trigger gebruikt.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>Kan ik standaard waarden voor de pijplijn parameters definiëren? 
Ja. U kunt standaard waarden definiëren voor de para meters in de pijp lijnen. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>Kan een activiteit in een pijp lijn argumenten gebruiken die worden door gegeven aan een pijplijn uitvoering? 
Ja. Elke activiteit in de pijp lijn kan de parameter waarde gebruiken die wordt door gegeven aan de pijp lijn en wordt uitgevoerd met de `@parameter` Construct. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Kan een uitvoer eigenschap van een activiteit worden gebruikt in een andere activiteit? 
Ja. De uitvoer van een activiteit kan worden gebruikt in een volgende activiteit met de `@activity` Construct.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Hoe kan ik worden Null-waarden in een uitvoer van een activiteit zonder problemen verwerkt? 
U kunt de `@coalesce` construct in de expressies gebruiken om null-waarden correct te verwerken. 

## <a name="mapping-data-flows"></a>Toewijzing gegevensstromen

### <a name="i-need-help-troubleshooting-my-data-flow-logic-what-info-do-i-need-to-provide-to-get-help"></a>Ik heb hulp nodig bij het oplossen van problemen met de gegevens stroom logica. Welke informatie heb ik nodig om hulp te krijgen?

Wanneer micro soft Help of probleem oplossing biedt voor gegevens stromen, kunt u het script voor de gegevens stroom opgeven. Dit is het code-behind script uit de gegevens stroom grafiek. Open uw gegevens stroom vanuit de ADF-gebruikers interface en klik vervolgens op de knop ' script ' in de rechter bovenhoek. Kopieer en plak dit script of sla het op in een tekst bestand.

### <a name="how-do-i-access-data-by-using-the-other-90-dataset-types-in-data-factory"></a>Hoe kan ik toegang tot gegevens met behulp van de andere typen van 90-gegevensset in Data Factory?

Met de functie voor het toewijzen van gegevens stroom kunnen op dit moment Azure SQL Database, Azure Synapse Analytics, tekst bestanden met scheidings tekens worden gescheiden van Azure Blob-opslag of Azure Data Lake Storage Gen2, en Parquet bestanden van Blob Storage of Data Lake Storage Gen2 systeem eigen voor bron en Sink. 

Gebruik de Kopieer activiteit om gegevens te stage van een van de andere connectors en voer vervolgens een gegevens stroom activiteit uit om gegevens te transformeren nadat deze is klaargezet. Uw pijp lijn wordt bijvoorbeeld eerst gekopieerd naar de Blob-opslag en vervolgens gebruikt een gegevens stroom activiteit een gegevensset in de bron om die gegevens te transformeren.

### <a name="is-the-self-hosted-integration-runtime-available-for-data-flows"></a>Is de zelf-hostende Integration runtime beschikbaar voor gegevens stromen?

Zelf-hostende IR is een ADF-pijp lijn constructie die u met de Kopieer activiteit kunt gebruiken om gegevens te verkrijgen of te verplaatsen naar en van on-premises of op virtuele machines gebaseerde gegevens bronnen en Sinks. Faseer de gegevens eerst met een kopie, vervolgens de gegevens stroom voor trans formatie en vervolgens een volgende kopie als u de getransformeerde gegevens terug naar de on-premises opslag wilt verplaatsen.

### <a name="does-the-data-flow-compute-engine-serve-multiple-tenants"></a>Worden er meerdere tenants voor de compute-engine voor gegevens stromen gebruikt?
Clusters worden nooit gedeeld. We garanderen isolatie voor elke uitvoering van een taak in productie-uitvoeringen. In het geval van fout opsporing kan één persoon een cluster ophalen en worden alle fouten naar dat cluster geleid die door die gebruiker worden geïnitieerd.

## <a name="wrangling-data-flows"></a>Wrangling-gegevens stromen

### <a name="what-are-the-supported-regions-for-wrangling-data-flow"></a>Wat zijn de ondersteunde regio's voor wrangling data flow?

Wrangling-gegevens stroom wordt momenteel ondersteund in gegevens fabrieken die in de volgende regio's zijn gemaakt:

* Australië - oost
* Canada - midden
* India - centraal
* VS - oost
* VS - oost 2
* Japan - oost
* Europa - noord
* Azië - zuidoost
* VS - zuid-centraal
* Verenigd Koninkrijk Zuid
* VS - west-centraal
* Europa -west
* VS - west
* VS - west 2

### <a name="what-are-the-limitations-and-constraints-with-wrangling-data-flow"></a>Wat zijn de beperkingen en beperkingen met wrangling-gegevens stroom?

Namen van gegevensset mogen alleen alfanumerieke tekens bevatten. De volgende gegevens archieven worden ondersteund:

* DelimitedText-gegevensset in Azure Blob Storage met account sleutel verificatie
* DelimitedText-gegevensset in Azure Data Lake Storage Gen2 met account sleutel of Service-Principal-verificatie
* DelimitedText-gegevensset in Azure Data Lake Storage gen1 met Service-Principal-verificatie
* Azure SQL Database en data warehouse met behulp van SQL-verificatie. Zie ondersteunde SQL-typen hieronder. Er is geen poly base-of staging-ondersteuning voor Data Warehouse.

Op dit moment wordt de integratie van de gekoppelde service Key Vault niet ondersteund in de wrangling-gegevens stromen.

### <a name="what-is-the-difference-between-mapping-and-wrangling-data-flows"></a>Wat is het verschil tussen de gegevens stromen toewijzing en wrangling?

Het toewijzen van gegevens stromen biedt een manier om gegevens op schaal te transformeren zonder dat hiervoor benodigde code hoeft te worden uitgevoerd. U kunt een gegevens transformatie taak ontwerpen in het canvas voor gegevens stromen door een reeks trans formaties te maken. Begin met een wille keurig aantal bron transformaties gevolgd door de stappen voor gegevens transformatie. Voltooi uw gegevens stroom met een Sink om uw resultaten in een doel te brengen. Toewijzing van gegevens stroom is handig bij het toewijzen en transformeren van gegevens met zowel bekende als onbekende schema's in de sinks en bronnen.

Wrangling-gegevens stromen bieden u de mogelijkheid om flexibele gegevens voor te bereiden en te verkennen met behulp van de online Mashup-Editor van Power Query op schaal via Spark-uitvoering. Met de verhoging van de gegevens-meren hoeft u alleen maar een gegevensset te verkennen of in het Lake-gegevens verzameling te maken. U bent niet toegewezen aan een bekend doel. Wrangling-gegevens stromen worden gebruikt voor minder formele en op modellen gebaseerde analyse scenario's.

### <a name="what-is-the-difference-between-power-platform-dataflows-and-wrangling-data-flows"></a>Wat is het verschil tussen het Power platform gegevens stromen-en wrangling-gegevens stromen?

Met de gegevens stromen van het Power-platform kunnen gebruikers gegevens uit een breed scala aan gegevens bronnen importeren en transformeren in de Common Data Service en Azure Data Lake om PowerApps-toepassingen te bouwen, Power BI rapporten of stroom automatisering. Power platform gegevens stromen gebruiken de gemaakte Power Query gegevens voorbereidings ervaring, vergelijkbaar met Power BI en Excel. Het gegevens stromen van het Power platform kan ook eenvoudig hergebruik binnen een organisatie inschakelen en de indeling automatisch afhandelen (bijvoorbeeld door het automatisch vernieuwen van gegevens stromen die afhankelijk zijn van een andere gegevens stroom wanneer de voormalige versie wordt vernieuwd).

Azure Data Factory (ADF) is een beheerde service voor gegevens integratie waarmee gegevens technici en burger data integrator complexe (ETL) en werk stromen voor het ophalen van een load-trans formatie (. Wrangling data flow in ADF biedt gebruikers een vrije, serverloze omgeving die het voorbereiden van gegevens in de Cloud vereenvoudigt en kan worden geschaald naar een wille keurige gegevens grootte zonder dat er infrastructuur beheer nodig is. Er wordt gebruikgemaakt van de Power Query gegevens voorbereidings technologie (ook gebruikt in Power platform gegevens stromen, Excel, Power BI) om de gegevens voor te bereiden en te vorm te geven. Wrangling-gegevens stromen maken het voor de verwerking van alle complexiteiten en de schaal uitdagingen van big data integratie, zodat gebruikers snel gegevens op schaal kunnen voorbereiden via Spark-uitvoering. Gebruikers kunnen flexibele gegevens pijplijnen bouwen in een toegankelijke visuele omgeving met onze browser interface en de complexiteit van Spark-uitvoering laten afhandelen met ADF. Bouw planningen voor uw pijp lijnen en bewaak de uitvoeringen van uw gegevens stroom vanuit de ADF-bewakings Portal. Beheer de beschik baarheid van gegevens beschikbaarheid met de uitgebreide Beschik baarheid en waarschuwingen van de ADF en profiteer van ingebouwde continue integratie-en implementatie mogelijkheden om uw stromen in een beheerde omgeving op te slaan en te beheren. Stel waarschuwingen in en Bekijk uitvoerings plannen om te valideren dat uw logica wordt uitgevoerd zoals gepland wanneer u uw gegevens stromen afstemt.

### <a name="supported-sql-types"></a>Ondersteunde SQL-typen

Wrangling-gegevens stroom ondersteunt de volgende gegevens typen in SQL. U krijgt een validatie fout voor het gebruik van een gegevens type dat niet wordt ondersteund.

* korte
* double
* werkelijk
* float
* char
* nchar
* varchar
* nvarchar
* geheel getal
* int
* bit
* booleaans
* smallint
* tinyint
* bigint
* long
* tekst
* datum
* datum/tijd
* datetime2
* smalldatetime
* tijdstempel
* uniqueidentifier
* xml

Andere gegevens typen zullen in de toekomst worden ondersteund.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende zelf studies voor stapsgewijze instructies voor het maken van een data factory:

- [Snelstartgids: een data factory maken](quickstart-create-data-factory-dot-net.md)
- [Zelf studie: gegevens kopiëren in de Cloud](tutorial-copy-data-dot-net.md)