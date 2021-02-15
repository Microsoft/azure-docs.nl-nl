---
title: Gegevens migreren van een on-premises Hadoop-cluster naar Azure Storage
description: Meer informatie over het gebruik van Azure Data Factory voor het migreren van gegevens van een on-premises Hadoop-cluster naar Azure Storage.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 9959a37d9b68d756437a3b4f0d75a2d63385758e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367789"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Azure Data Factory gebruiken om gegevens van een on-premises Hadoop-cluster te migreren naar Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory biedt een krachtige, robuuste en kosteneffectieve methode voor het migreren van gegevens op schaal van on-premises HDFS naar Azure Blob-opslag of Azure Data Lake Storage Gen2. 

Data Factory biedt twee basis benaderingen voor het migreren van gegevens van on-premises HDFS naar Azure. U kunt de aanpak selecteren op basis van uw scenario. 

- **Data Factory DistCp-modus** (aanbevolen): in Data Factory kunt u [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (gedistribueerde kopie) gebruiken om bestanden te kopiëren naar Azure Blob Storage (inclusief [gefaseerde kopie](./copy-activity-performance.md#staged-copy)) of Azure data Lake Store Gen2. Gebruik Data Factory geïntegreerd met DistCp om te profiteren van een bestaand krachtig cluster om de beste Kopieer doorvoer te halen. U profiteert ook van het voor deel van flexibele planning en een uniforme bewakings ervaring van Data Factory. Afhankelijk van de configuratie van uw Data Factory maakt Kopieer activiteit automatisch een DistCp-opdracht, worden de gegevens naar uw Hadoop-cluster verzonden en wordt de Kopieer status gecontroleerd. We raden u aan Data Factory DistCp-modus voor het migreren van gegevens van een on-premises Hadoop-cluster naar Azure.
- **Data Factory systeem eigen integratie runtime modus**: DistCp is geen optie in alle scenario's. In een omgeving met virtuele netwerken van Azure biedt het DistCp-hulp programma bijvoorbeeld geen ondersteuning voor Azure ExpressRoute private-peering met een Azure Storage eind punt van een virtueel netwerk. In sommige gevallen wilt u uw bestaande Hadoop-cluster niet gebruiken als een engine voor het migreren van gegevens, zodat u geen zware belasting op het cluster plaatst. Dit kan van invloed zijn op de prestaties van bestaande ETL-taken. In plaats daarvan kunt u de systeem eigen mogelijkheid van de Data Factory Integration runtime gebruiken als de engine waarmee gegevens worden gekopieerd van on-premises HDFS naar Azure.

In dit artikel vindt u de volgende informatie over beide benaderingen:
> [!div class="checklist"]
> * Prestaties 
> * Tolerantie kopiëren
> * Netwerkbeveiliging
> * Architectuur van oplossing op hoog niveau 
> * Aanbevolen procedures voor implementatie  

## <a name="performance"></a>Prestaties

In Data Factory modus DistCp is de door Voer hetzelfde als wanneer u het hulp programma DistCp onafhankelijk gebruikt. Data Factory DistCp-modus maximaliseert de capaciteit van uw bestaande Hadoop-cluster. U kunt DistCp gebruiken voor het kopiëren van grote hoeveel heden tussen clusters of binnen het cluster. 

DistCp maakt gebruik van MapReduce om de distributie, het afhandelen en herstellen van fouten en rapportage te laten ingaan. Hiermee wordt een lijst met bestanden en mappen uitgebreid naar invoer voor taak toewijzing. Elke taak kopieert een bestands partitie die is opgegeven in de lijst Bron. U kunt Data Factory geïntegreerd met DistCp gebruiken om pijp lijnen te bouwen om uw netwerk bandbreedte, opslag-IOPS en band breedte optimaal te benutten om de door Voer van gegevens verplaatsing voor uw omgeving te maximaliseren.  

Data Factory native Integration runtime modus maakt ook parallelle uitvoering op verschillende niveaus mogelijk. U kunt parallelisme gebruiken om uw netwerk bandbreedte, opslag-IOPS en band breedte volledig te benutten om de door Voer van gegevens verplaatsing te maximaliseren:

- Eén Kopieer activiteit kan profiteren van schaal bare reken resources. Met een zelf-hostende Integration runtime kunt u de machine hand matig schalen of uitschalen naar meerdere machines ([Maxi maal vier knoop punten](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)). Met één Kopieer activiteit worden de bestanden die zijn ingesteld op alle knoop punten gepartitioneerd. 
- Eén Kopieer activiteit leest van en schrijft naar het gegevens archief met behulp van meerdere threads. 
- Data Factory controle stroom kan meerdere Kopieer activiteiten parallel starten. U kunt bijvoorbeeld een [voor elke lus](./control-flow-for-each-activity.md)gebruiken. 

Zie de [prestatie handleiding Kopieer activiteit](./copy-activity-performance.md)voor meer informatie.

## <a name="resilience"></a>Tolerantie

In Data Factory DistCp-modus kunt u verschillende DistCp-opdracht regel parameters gebruiken (bijvoorbeeld `-i` , fouten negeren of `-update` gegevens schrijven wanneer het bron bestand en het doel bestand een andere grootte hebben) voor verschillende tolerantie niveaus.

In de Data Factory native Integration runtime-modus, in één exemplaar van een Kopieer activiteit, heeft Data Factory een ingebouwd mechanisme voor opnieuw proberen. Het kan een bepaald niveau van tijdelijke fouten in de gegevens archieven of in het onderliggende netwerk verwerken. 

Wanneer binaire kopieën worden gekopieerd van on-premises HDFS naar Blob-opslag en van on-premises HDFS naar Data Lake Store Gen2, voert Data Factory automatisch controle punten in voor een groot gebied. Als het uitvoeren van een Kopieer activiteit mislukt of als er een time-out optreedt, wordt er bij een volgende poging (Controleer of het aantal pogingen is > 1) het kopiëren hervat vanaf het laatste fout punt in plaats van vanaf het begin.

## <a name="network-security"></a>Netwerkbeveiliging 

Standaard brengt Data Factory gegevens over van on-premises HDFS naar Blob-opslag of Azure Data Lake Storage Gen2 met behulp van een versleutelde verbinding via het HTTPS-protocol. HTTPS biedt gegevens versleuteling tijdens de overdracht en voor komt het afluis teren en man-in-the-middle-aanvallen. 

Als u niet wilt dat gegevens worden overgedragen via het open bare Internet, kunt u voor betere beveiliging gegevens over een persoonlijke peering-koppeling overbrengen via ExpressRoute. 

## <a name="solution-architecture"></a>Architectuur voor de oplossing

In deze afbeelding ziet u hoe u gegevens migreert via het open bare Internet:

![Diagram waarin de architectuur van de oplossing voor het migreren van gegevens via een openbaar netwerk wordt weer gegeven](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- In deze architectuur worden gegevens veilig overgedragen met behulp van HTTPS via het open bare Internet. 
- U kunt het beste Data Factory DistCp-modus gebruiken in een open bare netwerk omgeving. U kunt profiteren van een krachtig bestaand cluster om de beste Kopieer doorvoer te halen. U profiteert ook van het voor deel van de flexibele planning en de geïntegreerde bewakings ervaring van Data Factory.
- Voor deze architectuur moet u de Data Factory zelf-hostende Integration runtime installeren op een Windows-computer achter een bedrijfs firewall om de DistCp-opdracht naar uw Hadoop-cluster te verzenden en de Kopieer status te controleren. Omdat de machine niet de engine is die gegevens verplaatst (alleen voor controle doeleinden), is de capaciteit van de computer niet van invloed op de door Voer van gegevens verplaatsing.
- Bestaande para meters van de opdracht DistCp worden ondersteund.

In deze afbeelding ziet u hoe u gegevens migreert via een privé-koppeling: 

![Diagram waarin de architectuur van de oplossing voor het migreren van gegevens via een particulier netwerk wordt weer gegeven](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- In deze architectuur worden gegevens gemigreerd via een koppeling voor persoonlijke peering via Azure ExpressRoute. Gegevens passeren nooit via het open bare Internet.
- Het DistCp-hulp programma biedt geen ondersteuning voor ExpressRoute-persoonlijke peering met een Azure Storage eind punt van een virtueel netwerk. U wordt aangeraden om de gegevens van Data Factory te migreren met behulp van de Integration runtime.
- Voor deze architectuur moet u de Data Factory zelf-hostende Integration runtime installeren op een Windows-VM in uw virtuele Azure-netwerk. U kunt uw virtuele machine hand matig schalen of uitschalen naar meerdere Vm's om uw netwerk-en opslag-IOPS of-band breedte volledig te benutten.
- De aanbevolen configuratie om te beginnen voor elke Azure VM (waarbij de Data Factory zelf-hostende Integration runtime is geïnstalleerd) is Standard_D32s_v3 met een 32-vCPU en 128 GB geheugen. U kunt het CPU-en geheugen gebruik van de virtuele machine tijdens de gegevens migratie bewaken om te zien of u de virtuele machine moet opschalen voor betere prestaties of dat u de virtuele machine omlaag kunt schalen om de kosten te verlagen.
- U kunt ook uitschalen door Maxi maal vier VM-knoop punten te koppelen aan één zelf-hostende Integration runtime. Een enkele Kopieer taak die wordt uitgevoerd op een zelf-hostende Integration runtime partitioneert automatisch de bestandenset en maakt gebruik van alle VM-knoop punten om de bestanden parallel te kopiëren. Voor maximale Beschik baarheid raden we u aan om te beginnen met twee VM-knoop punten om te voor komen dat een scenario met één punt wordt veroorzaakt tijdens de gegevens migratie.
- Wanneer u deze architectuur gebruikt, zijn initiële momentopname gegevens migratie en Delta gegevens migratie voor u beschikbaar.

## <a name="implementation-best-practices"></a>Aanbevolen procedures voor implementatie

U wordt aangeraden deze aanbevolen procedures te volgen wanneer u uw gegevens migratie implementeert.

### <a name="authentication-and-credential-management"></a>Verificatie en referentie beheer 

- Als u wilt verifiëren op HDFS, kunt u [Windows (Kerberos) of anoniem](./connector-hdfs.md#linked-service-properties)gebruiken. 
- Meerdere verificatie typen worden ondersteund om verbinding te maken met Azure Blob Storage.  Het is raadzaam [beheerde identiteiten te gebruiken voor Azure-resources](./connector-azure-blob-storage.md#managed-identity). Beheerde identiteiten zijn gebaseerd op een automatisch beheerde Data Factory identiteit in Azure Active Directory (Azure AD), zodat u pijp lijnen kunt configureren zonder dat er referenties worden opgegeven in de definitie van de gekoppelde service. U kunt ook verifiëren met Blob Storage met behulp van een [Service-Principal](./connector-azure-blob-storage.md#service-principal-authentication), een [hand tekening voor gedeelde toegang](./connector-azure-blob-storage.md#shared-access-signature-authentication)of een sleutel voor een [opslag account](./connector-azure-blob-storage.md#account-key-authentication). 
- Meerdere verificatie typen worden ook ondersteund om verbinding te maken met Data Lake Storage Gen2.  We raden u ten zeerste aan [beheerde identiteiten voor Azure-resources](./connector-azure-data-lake-storage.md#managed-identity)te gebruiken, maar u kunt ook een [Service-Principal](./connector-azure-data-lake-storage.md#service-principal-authentication) of een [opslag account sleutel](./connector-azure-data-lake-storage.md#account-key-authentication)gebruiken. 
- Wanneer u geen beheerde identiteiten voor Azure-resources gebruikt, raden we u ten zeerste aan [de referenties op te slaan in azure Key Vault](./store-credentials-in-key-vault.md) om het eenvoudiger te maken om sleutels te beheren en te draaien zonder Data Factory gekoppelde services te wijzigen. Dit is ook een [Best Practice voor CI/cd](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Initiële momentopname gegevens migratie 

In Data Factory DistCp-modus kunt u één Kopieer activiteit maken om de DistCp-opdracht te verzenden en verschillende para meters te gebruiken voor het beheren van het initiële gegevens migratie gedrag. 

In Data Factory native Integration runtime-modus wordt de gegevens partitie aanbevolen, met name wanneer u meer dan 10 TB aan gegevens migreert. Als u de gegevens wilt partitioneren, gebruikt u de mapnamen op HDFS. Elke Data Factory Kopieer taak kan vervolgens één mappen partitie tegelijk kopiëren. U kunt gelijktijdig meerdere Data Factory Kopieer taken uitvoeren voor een betere door voer.

Als een van de Kopieer taken mislukt als gevolg van tijdelijke problemen met het netwerk of het gegevens archief, kunt u de mislukte Kopieer taak opnieuw uitvoeren om die specifieke partitie opnieuw te laden op basis van HDFS. Andere Kopieer taken die andere partities laden, worden niet beïnvloed.

### <a name="delta-data-migration"></a>Delta gegevens migratie 

In Data Factory DistCp-modus kunt u de opdracht regel parameter DistCp gebruiken `-update` om gegevens te schrijven wanneer het bron bestand en het doel bestand verschillen qua grootte, voor Delta gegevens migratie.

In Data Factory systeem eigen integratie modus is de meest krachtige manier om nieuwe of gewijzigde bestanden van HDFS te identificeren met behulp van een tijdgebonden naamgevings Conventie. Als uw gegevens in HDFS zijn gepartitioneerd met tijds segmenten in de naam van het bestand of de map (bijvoorbeeld */yyyy/mm/dd/file.csv*), kan uw pijp lijn eenvoudig bepalen welke bestanden en mappen incrementeel moeten worden gekopieerd.

Als uw gegevens in HDFS niet tijdgebonden zijn, kunt Data Factory nieuwe of gewijzigde bestanden identificeren met behulp van de **LastModifiedDate** -waarde. Data Factory scant alle bestanden van HDFS en kopieert alleen nieuwe en bijgewerkte bestanden met een tijds tempel laatst gewijzigd dat groter is dan een ingestelde waarde. 

Als u een groot aantal bestanden in HDFS hebt, kan het scannen van bestanden veel tijd in beslag nemen, ongeacht het aantal bestanden dat overeenkomt met de filter voorwaarde. In dit scenario raden we u aan om de gegevens eerst te partitioneren met behulp van dezelfde partitie die u hebt gebruikt voor de initiële migratie van de moment opname. Vervolgens kan het scannen van bestanden parallel plaatsvinden.

### <a name="estimate-price"></a>Prijs ramen 

Bekijk de volgende pijp lijn voor het migreren van gegevens van HDFS naar Azure Blob-opslag: 

![Diagram waarin de prijs pijplijn wordt weer gegeven](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Laten we uitgaan van de volgende informatie: 

- Het totale gegevens volume is 1 PB.
- U migreert gegevens met behulp van de Data Factory native Integration runtime-modus.
- 1 PB is onderverdeeld in 1.000-partities en elke kopie verplaatst één partitie.
- Elke Kopieer activiteit is geconfigureerd met één zelf-hostende Integration runtime die is gekoppeld aan vier computers en die de door Voer van 500 MBps verzorgt.
- Gelijktijdigheid van ForEach is ingesteld op **4** en een geaggregeerde door Voer is 2 Gbps.
- In totaal duurt het 146 uur om de migratie te volt ooien.

Dit is de geschatte prijs op basis van onze veronderstellingen: 

![Tabel waarin de prijs berekeningen worden weer gegeven](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Dit is een voor beeld van een hypothetische prijs. Uw werkelijke prijs is afhankelijk van de werkelijke door Voer in uw omgeving.
> De prijs van een Azure Windows-VM (met zelf-hostende Integration runtime) is niet opgenomen.

### <a name="additional-references"></a>Aanvullende naslaginformatie

- [HDFS-connector](./connector-hdfs.md)
- [Azure Blob-opslag connector](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-connector](./connector-azure-data-lake-storage.md)
- [Gids voor het afstemmen van de activiteit prestaties kopiëren](./copy-activity-performance.md)
- [Zelf-hostende Integration Runtime maken en configureren](./create-self-hosted-integration-runtime.md)
- [Zelf-hostende Integration runtime hoge Beschik baarheid en schaal baarheid](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Beveiligingsoverwegingen bij het verplaatsen van gegevens](./data-movement-security-considerations.md)
- [Referenties opslaan in Azure Key Vault](./store-credentials-in-key-vault.md)
- [Een bestand incrementeel kopiëren op basis van een gepartitioneerde bestands naam](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Nieuwe en gewijzigde bestanden kopiëren op basis van LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Pagina met Data Factory prijzen](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Volgende stappen

- [Bestanden van meerdere containers kopiëren met behulp van Azure Data Factory](solution-template-copy-files-multiple-containers.md)