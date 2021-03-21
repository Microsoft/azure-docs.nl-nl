---
title: Gegevens migreren van een on-premises Netezza-server naar Azure
description: Gebruik Azure Data Factory om gegevens van een on-premises Netezza-server naar Azure te migreren.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 36ca7b709e0ec945f1fb4a9a8b745d20e6a58fe1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367772"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Azure Data Factory gebruiken om gegevens van een on-premises Netezza-server naar Azure te migreren 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory biedt een krachtig, robuust en rendabel mechanisme voor het migreren van gegevens op schaal van een on-premises Netezza-server naar uw Azure Storage-account of Azure Synapse Analytics-Data Base. 

In dit artikel vindt u de volgende informatie voor gegevens technici en ontwikkel aars:

> [!div class="checklist"]
> * Prestaties 
> * Tolerantie kopiëren
> * Netwerkbeveiliging
> * Architectuur van oplossing op hoog niveau 
> * Aanbevolen procedures voor implementatie  

## <a name="performance"></a>Prestaties

Azure Data Factory biedt een serverloze architectuur die parallellisme op verschillende niveaus mogelijk maakt. Als u een ontwikkelaar bent, betekent dit dat u pijp lijnen kunt maken om zowel netwerk-als database bandbreedte volledig te gebruiken om de door Voer van gegevens verplaatsing voor uw omgeving te maximaliseren.

![Prestatie diagram](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

Het voor gaande diagram kan als volgt worden geïnterpreteerd:

- Eén Kopieer activiteit kan profiteren van schaal bare reken resources. Wanneer u Azure Integration Runtime gebruikt, kunt u voor elke Kopieer activiteit op serverloze wijze [Maxi maal 256 DIUs](./copy-activity-performance.md#data-integration-units) opgeven. Met een zelf-hostende Integration runtime (zelf-hostende IR) kunt u de machine hand matig opschalen of uitschalen naar meerdere machines ([Maxi maal vier knoop punten](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)), en met één Kopieer activiteit wordt de partitie over alle knoop punten gedistribueerd. 

- Eén Kopieer activiteit leest van en schrijft naar het gegevens archief met behulp van meerdere threads. 

- Azure Data Factory controle stroom kan meerdere Kopieer activiteiten parallel starten. Het kan bijvoorbeeld worden gestart met behulp van een [voor elke lus](./control-flow-for-each-activity.md). 

Zie voor meer informatie kopiëren van de [prestaties en schaal baarheid](./copy-activity-performance.md)van de activiteit.

## <a name="resilience"></a>Tolerantie

Binnen één exemplaar van de Kopieer activiteit heeft Azure Data Factory een ingebouwd mechanisme voor opnieuw proberen, waarmee het een bepaald niveau van tijdelijke fouten in de gegevens archieven of in het onderliggende netwerk kan verwerken.

Wanneer u gegevens kopieert tussen bron-en Sink-gegevens opslag, kunt u met Azure Data Factory Kopieer activiteit op twee manieren incompatibele rijen verwerken. U kunt de Kopieer activiteit afbreken en mislukken of de rest van de gegevens blijven kopiëren door de niet-compatibele gegevens rijen over te slaan. Daarnaast kunt u, om de oorzaak van de fout te achterhalen, de niet-compatibele rijen in Azure Blob-opslag of Azure Data Lake Store vastleggen, de gegevens op de gegevens bron herstellen en de Kopieer activiteit opnieuw proberen.

## <a name="network-security"></a>Netwerkbeveiliging 

Azure Data Factory brengt standaard gegevens over van de on-premises Netezza-server naar een Azure-opslag account of Azure Synapse Analytics-Data Base door gebruik te maken van een versleutelde verbinding via Hypertext Transfer Protocol Secure (HTTPS). HTTPS biedt gegevens versleuteling tijdens de overdracht en voor komt het afluis teren en man-in-the-middle-aanvallen.

Als u niet wilt dat gegevens worden overgedragen via het open bare Internet, kunt u een betere beveiliging bieden door gegevens over een koppeling met een privé-peering over te brengen via Azure Express route. 

In de volgende sectie wordt beschreven hoe u betere beveiliging kunt krijgen.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In deze sectie worden twee manieren beschreven om uw gegevens te migreren.

### <a name="migrate-data-over-the-public-internet"></a>Gegevens migreren via het open bare Internet

![Gegevens migreren via het open bare Internet](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

Het voor gaande diagram kan als volgt worden geïnterpreteerd:

- In deze architectuur kunt u gegevens veilig overdragen met behulp van HTTPS via het open bare Internet.

- Als u deze architectuur wilt realiseren, moet u de Azure Data Factory Integration runtime (zelf-hostend) installeren op een Windows-computer achter een bedrijfs firewall. Zorg ervoor dat deze Integration runtime rechtstreeks toegang krijgt tot de Netezza-server. Als u uw netwerk-en gegevens opslag bandbreedte volledig wilt gebruiken om gegevens te kopiëren, kunt u de machine hand matig schalen of uitschalen naar meerdere machines.

- Met deze architectuur kunt u zowel initiële momentopname gegevens als Delta gegevens migreren.

### <a name="migrate-data-over-a-private-network"></a>Gegevens migreren via een particulier netwerk 

![Gegevens migreren via een particulier netwerk](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

Het voor gaande diagram kan als volgt worden geïnterpreteerd:

- In deze architectuur migreert u gegevens via een koppeling met een persoonlijke peering via Azure Express route en worden gegevens nooit via het open bare Internet gepasseerd. 

- Voor deze architectuur moet u de Azure Data Factory Integration runtime (zelf-hostend) installeren op een virtuele Windows-machine (VM) in uw virtuele Azure-netwerk. Als u uw netwerk-en gegevens opslag bandbreedte volledig wilt gebruiken om gegevens te kopiëren, kunt u de virtuele machine hand matig schalen of uitschalen naar meerdere Vm's.

- Met deze architectuur kunt u zowel initiële momentopname gegevens als Delta gegevens migreren.

## <a name="implement-best-practices"></a>Best practices implementeren 

### <a name="manage-authentication-and-credentials"></a>Verificatie en referenties beheren 

- U kunt [ODBC-verificatie via Connection String](./connector-netezza.md#linked-service-properties)gebruiken om te verifiëren bij Netezza. 

- Verificatie bij Azure Blob-opslag: 

   - Het is raadzaam [beheerde identiteiten te gebruiken voor Azure-resources](./connector-azure-blob-storage.md#managed-identity). Beheerde identiteiten zijn gebaseerd op een automatisch beheerde Azure Data Factory identiteit in Azure Active Directory (Azure AD), zodat u pijp lijnen kunt configureren zonder dat er referenties moeten worden opgegeven in de definitie van de gekoppelde service.  

   - U kunt ook verifiëren voor Azure Blob Storage met behulp van de [Service-Principal](./connector-azure-blob-storage.md#service-principal-authentication), een [hand tekening voor gedeelde toegang](./connector-azure-blob-storage.md#shared-access-signature-authentication)of een sleutel voor het [opslag account](./connector-azure-blob-storage.md#account-key-authentication). 

- Verifiëren bij Azure Data Lake Storage Gen2: 

   - Het is raadzaam [beheerde identiteiten te gebruiken voor Azure-resources](./connector-azure-data-lake-storage.md#managed-identity).
   
   - U kunt ook een sleutel voor de [Service-Principal](./connector-azure-data-lake-storage.md#service-principal-authentication) of een [opslag account](./connector-azure-data-lake-storage.md#account-key-authentication)gebruiken. 

- Verificatie bij Azure Synapse Analytics:

   - Het is raadzaam [beheerde identiteiten te gebruiken voor Azure-resources](./connector-azure-sql-data-warehouse.md#managed-identity).
   
   - U kunt ook [Service-Principal](./connector-azure-sql-data-warehouse.md#service-principal-authentication) of [SQL-verificatie](./connector-azure-sql-data-warehouse.md#sql-authentication)gebruiken.

- Wanneer u geen beheerde identiteiten voor Azure-resources gebruikt, raden we u ten zeerste aan [de referenties op te slaan in azure Key Vault](./store-credentials-in-key-vault.md) om het eenvoudiger te maken om sleutels te beheren en te draaien zonder dat u Azure Data Factory gekoppelde services hoeft te wijzigen. Dit is ook een van de [Aanbevolen procedures voor CI/cd](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="migrate-initial-snapshot-data"></a>Initiële momentopname gegevens migreren 

Voor kleine tabellen (dat wil zeggen: tabellen met een volume kleiner dan 100 GB of die binnen twee uur naar Azure kunnen worden gemigreerd), kunt u elke taak voor het laden van gegevens per tabel maken. Voor een hogere door Voer kunt u meerdere Azure Data Factory Kopieer taken uitvoeren om afzonderlijke tabellen gelijktijdig te laden. 

U kunt binnen elke Kopieer taak parallelle query's uitvoeren en gegevens kopiëren per partitie, maar ook een zekere mate van parallellisme bereiken met behulp van de instelling van de [ `parallelCopies` eigenschap](./copy-activity-performance.md#parallel-copy) met een van de volgende opties voor de gegevens partitie:

- Voor een grotere efficiëntie kunt u het beste beginnen met een gegevens segment.  Zorg ervoor dat de waarde in de `parallelCopies` instelling lager is dan het totale aantal gegevens segment partities in de tabel op de Netezza-server.  

- Als het volume van elke gegevens segment partitie nog steeds groot is (bijvoorbeeld 10 GB of meer), raden we u aan om over te scha kelen naar een dynamische bereik partitie. Deze optie biedt meer flexibiliteit voor het definiëren van het aantal partities en het volume van elke partitie per partitie kolom, bovengrens en ondergrens.

Voor grotere tabellen (dat wil zeggen: tabellen met een volume van 100 GB of meer of die binnen twee uur niet naar Azure *kunnen* worden gemigreerd), raden we u aan om de gegevens te partitioneren op basis van een aangepaste query en vervolgens elke kopie-taak een partitie per keer te kopiëren. Voor een betere door Voer kunt u meerdere Azure Data Factory kopie taken gelijktijdig uitvoeren. Voor elk Kopieer taak doel van het laden van één partitie door een aangepaste query kunt u de door Voer verhogen door middel van parallellisme via gegevens segment of dynamisch bereik. 

Als een Kopieer taak mislukt vanwege een tijdelijk probleem in het netwerk of het gegevens archief, kunt u de mislukte Kopieer taak opnieuw uitvoeren om deze specifieke partitie uit de tabel opnieuw te laden. Andere Kopieer taken die andere partities laden, worden niet beïnvloed.

Wanneer u gegevens laadt in een Azure Synapse Analytics-Data Base, wordt u aangeraden poly base in de Kopieer taak met Azure Blob Storage in te scha kelen als fase ring.

### <a name="migrate-delta-data"></a>Delta gegevens migreren 

Als u de nieuwe of bijgewerkte rijen in de tabel wilt identificeren, gebruikt u een time stamp-kolom of een incrementele sleutel in het schema. U kunt de nieuwste waarde vervolgens opslaan als een bovengrens in een externe tabel en deze vervolgens gebruiken om de Delta gegevens te filteren wanneer u gegevens de volgende keer laadt. 

Elke tabel kan een andere watermerk kolom gebruiken om de nieuwe of bijgewerkte rijen te identificeren. We raden u aan om een tabel met externe controle te maken. In de tabel vertegenwoordigt elke rij één tabel op de Netezza-server met de specifieke naam van de watermerk kolom en de bovengrens waarde. 

### <a name="configure-a-self-hosted-integration-runtime"></a>Een zelf-hostende Integration runtime configureren

Als u gegevens migreert van de Netezza-server naar Azure, of de server on-premises achter uw Corporation-firewall is of binnen een virtuele netwerk omgeving, moet u een zelf-hostende IR installeren op een Windows-computer of-VM. Dit is de engine die wordt gebruikt om gegevens te verplaatsen. Wanneer u de zelf-hostende IR installeert, raden we u aan de volgende aanpak te volgen:

- Begin met een configuratie van 32 vCPU en 128-GB geheugen voor elke Windows-machine of-VM. U kunt het CPU-en geheugen gebruik van de IR-machine tijdens de gegevens migratie blijven bewaken om te zien of u de machine verder moet opschalen voor betere prestaties of de machine omlaag kunt schalen om kosten te besparen.

- U kunt ook uitschalen door Maxi maal vier knoop punten te koppelen aan één zelf-hostende IR. Voor één Kopieer taak die wordt uitgevoerd op een zelf-hostende IR, worden automatisch alle VM-knoop punten toegepast om de gegevens parallel te kopiëren. Voor maximale Beschik baarheid begint u met vier VM-knoop punten om te voor komen dat een Single Point of Failure tijdens de gegevens migratie.

### <a name="limit-your-partitions"></a>Uw partities beperken

Als best practice moet u een test omgeving voor de prestaties uitvoeren met een representatieve voor beeld-gegevensset, zodat u de juiste partitie grootte kunt bepalen voor elke Kopieer activiteit. We raden u aan elke partitie binnen twee uur naar Azure te laden.  

Als u een tabel wilt kopiëren, begint u met één Kopieer activiteit met één zelf-hostende IR-computer. Verhoog de `parallelCopies` instelling geleidelijk op basis van het aantal gegevens segment partities in de tabel. Bekijk of de volledige tabel binnen twee uur kan worden geladen in azure, afhankelijk van de door Voer die het resultaat is van de Kopieer taak. 

Als deze niet binnen twee uur kan worden geladen in azure, en de capaciteit van het zelf-hostende IR-knoop punt en het gegevens archief niet volledig worden gebruikt, verhoogt u het aantal gelijktijdige Kopieer activiteiten geleidelijk totdat u de limiet bereikt van het netwerk of de bandbreedte limiet van de gegevens archieven. 

Bewaak het CPU-en geheugen gebruik op de zelf-hostende IR-computer en bereid u voor om de machine te schalen of uit te breiden naar meerdere machines wanneer u ziet dat de CPU en het geheugen volledig worden gebruikt. 

Wanneer u vertragings fouten ondervindt, zoals gerapporteerd door Azure Data Factory Kopieer activiteit, verlaagt u de gelijktijdigheid of `parallelCopies` instelling in azure Data Factory, of overweegt u de limieten voor de band breedte of I/O-bewerkingen per seconde (IOPS) van het netwerk en de gegevens opslag te verg Roten. 


### <a name="estimate-your-pricing"></a>Uw prijzen ramen 

Bekijk de volgende pijp lijn, die is gebouwd om gegevens te migreren van de on-premises Netezza-server naar een Azure Synapse Analytics-Data Base:

![De prijs pijplijn](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

We gaan ervan uit dat de volgende instructies waar zijn: 

- Het totale gegevens volume is 50 terabytes (TB). 

- Gegevens worden gemigreerd met behulp van de eerste oplossings architectuur (de Netezza-server is on-premises, achter de firewall).

- Het volume van 50 TB is onderverdeeld in 500-partities en elke Kopieer activiteit verplaatst één partitie.

- Elke Kopieer activiteit wordt geconfigureerd met één zelf-hostende IR op vier computers en een door Voer van 20 Mega bytes per seconde (MBps). (In de Kopieer activiteit `parallelCopies` is ingesteld op 4 en elke thread voor het laden van gegevens uit de tabel behaalt een door Voer van 5 Mbps.)

- De gelijktijdigheid van ForEach is ingesteld op 3 en de geaggregeerde door Voer is 60 MBps.

- In totaal duurt het 243 uur om de migratie te volt ooien.

Op basis van de voor gaande hypo theses is dit de geschatte prijs: 

![De tabel prijzen](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> De prijzen die in de voor gaande tabel worden weer gegeven, zijn hypothetisch. Uw werkelijke prijs is afhankelijk van de werkelijke door Voer in uw omgeving. De prijs voor de Windows-computer (met de zelf-hostende IR) is niet opgenomen. 

### <a name="additional-references"></a>Aanvullende naslaginformatie

Raadpleeg de volgende artikelen en hand leidingen voor meer informatie:

- [Gegevens migreren van een on-premises relationele data warehouse-Data Base naar Azure met behulp van Azure Data Factory](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza-connector](./connector-netezza.md)
- [ODBC-connector](./connector-odbc.md)
- [Azure Blob-opslag connector](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-connector](./connector-azure-data-lake-storage.md)
- [Azure Synapse Analytics-connector](./connector-azure-sql-data-warehouse.md)
- [Gids voor het afstemmen van de activiteit prestaties kopiëren](./copy-activity-performance.md)
- [Zelf-hostende Integration Runtime maken en configureren](./create-self-hosted-integration-runtime.md)
- [Zelf-hostende runtime HA en schaal baarheid](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Beveiligingsoverwegingen bij het verplaatsen van gegevens](./data-movement-security-considerations.md)
- [Referenties opslaan in Azure Key Vault](./store-credentials-in-key-vault.md)
- [Gegevens stapsgewijs uit een tabel kopiëren](./tutorial-incremental-copy-portal.md)
- [Gegevens stapsgewijs uit meerdere tabellen kopiëren](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Pagina met Azure Data Factory prijzen](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Volgende stappen

- [Bestanden van meerdere containers kopiëren met behulp van Azure Data Factory](solution-template-copy-files-multiple-containers.md)