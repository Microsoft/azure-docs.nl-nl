---
title: Gegevens migreren van Amazon S3 naar Azure Storage
description: Gebruik Azure Data Factory om gegevens te migreren van Amazon S3 naar Azure Storage.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/04/2019
ms.openlocfilehash: 2be8a9c7476bda6952ed1eaa15d29fe9c01b59a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371308"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Azure Data Factory gebruiken om gegevens te migreren van Amazon S3 naar Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory biedt een krachtig, robuust en rendabel mechanisme voor het migreren van gegevens op schaal van Amazon S3 naar Azure Blob Storage of Azure Data Lake Storage Gen2.  In dit artikel vindt u de volgende informatie voor gegevens technici en ontwikkel aars: 

> [!div class="checklist"]
> * Prestaties 
> * Tolerantie kopiëren
> * Netwerkbeveiliging
> * Architectuur van oplossing op hoog niveau 
> * Aanbevolen procedures voor implementatie  

## <a name="performance"></a>Prestaties

ADF biedt een serverloze architectuur die parallellisme op verschillende niveaus mogelijk maakt, zodat ontwikkel aars pijp lijnen kunnen bouwen om uw netwerk bandbreedte en opslag-IOPS en band breedte optimaal te benutten voor de door Voer van gegevens verplaatsing voor uw omgeving. 

Klanten hebben PETA bytes van gegevens gemigreerd die bestaan uit honderden miljoenen bestanden van Amazon S3 naar Azure Blob Storage, met een aanhoudende door Voer van 2 GBps en hoger. 

![Diagram toont verschillende bestands partities in een W S S3-opslag met gekoppelde Kopieer acties naar Azure Blob Storage een D L S Gen2.](media/data-migration-guidance-s3-to-azure-storage/performance.png)

In de bovenstaande afbeelding ziet u hoe u met behulp van verschillende niveaus van parallellisme uitstekende gegevens verplaatsings snelheden kunt realiseren:
 
- Eén Kopieer activiteit kan profiteren van schaal bare reken bronnen: wanneer u Azure Integration Runtime gebruikt, kunt u [Maxi maal 256 DIUs](./copy-activity-performance.md#data-integration-units) voor elke Kopieer activiteit op serverloze wijze opgeven. Wanneer u zelf-hostende Integration Runtime gebruikt, kunt u de machine hand matig opschalen of uitschalen naar meerdere machines ([Maxi maal 4 knoop punten](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)), en met één Kopieer activiteit wordt de bestands set gepartitioneerd op alle knoop punten. 
- Eén Kopieer activiteit leest van en schrijft naar het gegevens archief met behulp van meerdere threads. 
- De controle stroom van ADF kan meerdere Kopieer activiteiten parallel starten, bijvoorbeeld [voor elke lus](./control-flow-for-each-activity.md). 

## <a name="resilience"></a>Tolerantie

Binnen één exemplaar van de Kopieer activiteit heeft ADF een ingebouwd mechanisme voor opnieuw proberen, zodat het een bepaald niveau van tijdelijke fouten in de gegevens archieven of in het onderliggende netwerk kan verwerken. 

Bij het binaire kopiëren van S3 naar Blob en van S3 naar ADLS Gen2, voert ADF automatisch controle punten uit.  Als het uitvoeren van een Kopieer activiteit is mislukt of er een time-out is opgetreden, wordt de kopie hervat vanaf het laatste fout punt in plaats van vanaf het begin. 

## <a name="network-security"></a>Netwerkbeveiliging 

Standaard brengt ADF gegevens van Amazon S3 over naar Azure Blob Storage of Azure Data Lake Storage Gen2 met behulp van een versleutelde verbinding via het HTTPS-protocol.  HTTPS biedt gegevens versleuteling tijdens de overdracht en voor komt het afluis teren en man-in-the-middle-aanvallen. 

Als u niet wilt dat gegevens worden overgedragen via het open bare Internet, kunt u de beveiliging verbeteren door gegevens over een persoonlijke peering-koppeling tussen AWS Direct Connect en Azure Express route over te brengen.  Raadpleeg de onderstaande oplossings architectuur voor meer informatie over hoe dit kan worden bereikt. 

## <a name="solution-architecture"></a>Architectuur voor de oplossing

Gegevens migreren via het open bare Internet:

![In het diagram ziet u de migratie via internet per uur T/m P van een W S S3-archief via Azure Integration Runtime in een D F Azure naar Azure Storage. De runtime heeft een besturings kanaal met Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- In deze architectuur worden gegevens veilig overgedragen met behulp van HTTPS via open bare Internet. 
- Zowel de bron Amazon S3 als de doel-Azure-Blob Storage of Azure Data Lake Storage Gen2 zijn zodanig geconfigureerd dat verkeer van alle IP-adressen van het netwerk wordt toegestaan.  Raadpleeg de tweede architectuur hieronder voor informatie over hoe u de netwerk toegang tot een specifiek IP-bereik kunt beperken. 
- U kunt eenvoudig de hoeveelheid brand kracht op serverloze wijze schalen om uw netwerk-en opslag bandbreedte volledig te benutten zodat u de beste door Voer voor uw omgeving kunt krijgen. 
- U kunt zowel de initiële migratie van moment opnamen als de migratie van Delta gegevens bereiken met behulp van deze architectuur. 

Gegevens migreren via een persoonlijke koppeling: 

![Diagram toont de migratie via een particuliere peering-verbinding van een W S S3-opslag via zelf-hostende Integration runtime op Azure virtual machines naar de eind punten van de V net-service naar Azure Storage. De runtime heeft een besturings kanaal met Data Factory.](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- In deze architectuur wordt de gegevens migratie uitgevoerd via een koppeling voor een privé-peering tussen AWS Direct Connect en Azure Express route zodat de gegevens nooit via het open bare Internet worden gepasseerd.  Hiervoor is het gebruik van AWS VPC en een virtueel Azure-netwerk vereist. 
- U moet de zelf-hostende Integration runtime van ADF installeren op een Windows-VM in uw virtuele Azure-netwerk om deze architectuur te verzorgen.  U kunt uw zelf-hostende IR-Vm's hand matig schalen of uitschalen naar meerdere Vm's (Maxi maal 4 knoop punten) om uw netwerk en opslag-IOPS/band breedte volledig te benutten. 
- Als het acceptabel is om gegevens over te dragen, maar u de netwerk toegang tot de bron-S3 wilt vergren delen voor een specifiek IP-bereik, kunt u een variatie van deze architectuur aannemen door AWS VPC te verwijderen en de persoonlijke koppeling te vervangen door HTTPS.  U wilt Azure Virtual en zelf-hostende IR op Azure VM blijven gebruiken, zodat u een statisch, openbaar routeerbaar IP-adres kunt hebben voor filter doeleinden. 
- U kunt zowel de initiële momentopname gegevens migratie als de migratie van de Delta gegevens bereiken met behulp van deze architectuur. 

## <a name="implementation-best-practices"></a>Aanbevolen procedures voor implementatie 

### <a name="authentication-and-credential-management"></a>Verificatie en referentie beheer 

- Als u zich wilt verifiëren bij het Amazon S3-account, moet u de [toegangs sleutel voor het IAM-account](./connector-amazon-simple-storage-service.md#linked-service-properties)gebruiken. 
- Meerdere verificatie typen worden ondersteund om verbinding te maken met Azure Blob Storage.  Het gebruik van [beheerde identiteiten voor Azure-resources](./connector-azure-blob-storage.md#managed-identity) wordt ten zeerste aanbevolen: gebouwd op basis van een automatisch beheerde ADF-zoek opdracht in azure AD, kunt u pijp lijnen configureren zonder dat er referenties worden opgegeven in de definitie van de gekoppelde service.  U kunt ook verifiëren bij Azure Blob Storage met behulp van de [Service-Principal](./connector-azure-blob-storage.md#service-principal-authentication), de [hand tekening voor gedeelde toegang](./connector-azure-blob-storage.md#shared-access-signature-authentication)of de sleutel van het [opslag account](./connector-azure-blob-storage.md#account-key-authentication). 
- Meerdere verificatie typen worden ook ondersteund om verbinding te maken met Azure Data Lake Storage Gen2.  Het gebruik van [beheerde identiteiten voor Azure-resources](./connector-azure-data-lake-storage.md#managed-identity) wordt sterk aanbevolen, hoewel de sleutel van de [Service-Principal](./connector-azure-data-lake-storage.md#service-principal-authentication) of het [opslag account](./connector-azure-data-lake-storage.md#account-key-authentication) ook kan worden gebruikt. 
- Wanneer u geen beheerde identiteiten voor Azure-resources gebruikt, wordt [het opslaan van de referenties in azure Key Vault](./store-credentials-in-key-vault.md) ten zeerste aanbevolen, zodat het eenvoudiger is om sleutels te beheren en te draaien zonder de gekoppelde services van ADF aan te passen.  Dit is ook een van de [Aanbevolen procedures voor CI/cd](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Initiële momentopname gegevens migratie 

Gegevens partities worden vooral aanbevolen bij het migreren van meer dan 100 TB aan gegevens.  Als u de gegevens wilt partitioneren, maakt u gebruik van de instelling voor voegsel om de mappen en bestanden in Amazon S3 op naam te filteren, waarna elke taak voor het kopiëren van de ADF één partitie per keer kan kopiëren.  U kunt gelijktijdig meerdere ADF-Kopieer taken uitvoeren voor een betere door voer. 

Als een van de Kopieer taken mislukt als gevolg van een probleem met de tijdelijke netwerk-of gegevens opslag, kunt u de mislukte Kopieer taak opnieuw uitvoeren om deze specifieke partitie opnieuw te laden vanuit AWS S3.  Alle andere Kopieer taken voor het laden van andere partities worden niet beïnvloed. 

### <a name="delta-data-migration"></a>Delta gegevens migratie 

De meest krachtige manier om nieuwe of gewijzigde bestanden van AWS S3 te identificeren, is door gebruik te maken van tijdgebonden naamgevings conventies – wanneer uw gegevens in AWS S3 zijn gepartitioneerd met tijds segment informatie in het bestand of de mapnaam (bijvoorbeeld/yyyy/mm/dd/file.csv), kan de pijp lijn eenvoudig bepalen welke bestanden/mappen er incrementeel moeten worden gekopieerd. 

Als uw gegevens in AWS S3 niet gepartitioneerd zijn, kan ADF nieuwe of gewijzigde bestanden identificeren met hun LastModifiedDate.   Zoals u ziet, scant de ADF alle bestanden van AWS S3 en kopieert alleen het nieuwe en bijgewerkte bestand waarvan het tijdstip waarop de laatste wijziging is aangebracht, groter is dan een bepaalde waarde.  Houd er rekening mee dat als u een groot aantal bestanden in S3 hebt, de eerste scan van het bestand veel tijd kan duren, ongeacht het aantal bestanden dat overeenkomt met de filter voorwaarde.  In dit geval wordt u geadviseerd om eerst de gegevens te partitioneren met dezelfde prefix instelling voor de eerste migratie van moment opnamen, zodat het scannen van bestanden parallel kan plaatsvinden.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Voor scenario's waarvoor een zelf-hostende Integration runtime op de Azure-VM is vereist 

Of u nu gegevens migreert via een privé koppeling of een specifiek IP-adres bereik wilt toestaan op de Amazon S3-firewall, moet u zelf-hostende Integration runtime installeren op Azure Windows VM. 

- De aanbevolen configuratie voor elke virtuele machine van Azure is Standard_D32s_v3 met 32 vCPU en 128 GB geheugen.  U kunt het CPU-en geheugen gebruik van de IR-VM tijdens de gegevens migratie blijven bewaken om te zien of u de virtuele machine verder moet opschalen voor betere prestaties of de virtuele machine kunt verlagen om kosten te besparen. 
- U kunt ook uitschalen door Maxi maal 4 VM-knoop punten te koppelen aan één zelf-hostende IR.  Een enkele Kopieer taak die wordt uitgevoerd op een zelf-hostende IR, partitioneert automatisch de set bestanden en maakt gebruik van alle VM-knoop punten om de bestanden parallel te kopiëren.  Voor maximale Beschik baarheid kunt u het beste beginnen met 2 VM-knoop punten om Single Point of Failure te voor komen tijdens de gegevens migratie. 

### <a name="rate-limiting"></a>Snelheidsbeperking 

Als best practice, voert u een prestatie test uit met een representatieve voor beeld-gegevensset, zodat u de juiste partitie grootte kunt bepalen. 

Begin met één partitie en één Kopieer activiteit met de standaard instelling voor DIU.  Verhoog de DIU-instelling geleidelijk totdat u de bandbreedte limiet bereikt van uw netwerk of IOPS/bandbreedte limiet van de gegevens archieven, of u hebt het maximale aantal van 256 DIU bereikt dat is toegestaan voor één Kopieer activiteit. 

Verhoog vervolgens geleidelijk het aantal gelijktijdige Kopieer activiteiten tot u de limieten van uw omgeving bereikt. 

Wanneer u vertragings fouten ondervindt die worden gerapporteerd door de ADF-Kopieer activiteit, verlaagt u de gelijktijdigheids-of DIU instelling in ADF of neemt u de limieten voor band breedte/IOPS van het netwerk en de gegevens opslag op.  

### <a name="estimating-price"></a>Prijs schatten 

> [!NOTE]
> Dit is een voor beeld van een hypothetische prijs.  Uw werkelijke prijs is afhankelijk van de werkelijke door Voer in uw omgeving.

Bekijk de volgende pijp lijn die is gebouwd voor het migreren van gegevens van S3 naar Azure Blob Storage: 

![Diagram toont een pijp lijn voor het migreren van gegevens, met hand matige trigger stroom om te zoeken, Flowing naar ForEach, stromend naar een subpijplijn voor elke partitie die Kopieer stroom naar opgeslagen procedure bevat. Buiten de pijp lijn worden opgeslagen procedures doorstromen naar Azure SQL D B, die stromen om te zoeken en een W S S3-stroom om te kopiëren, die stromen naar Blob Storage.](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

We nemen het volgende uit: 

- Het totale gegevens volume is 2 PB 
- Gegevens migreren via HTTPS met de eerste architectuur van de oplossing 
- 2 PB is onderverdeeld in 1 K-partities en elke kopie verplaatst één partitie 
- Elk exemplaar is geconfigureerd met DIU = 256 en een maximale door Voer van 1 GBps 
- Gelijktijdigheid van ForEach is ingesteld op 2 en een geaggregeerde door Voer is 2 GBps 
- In totaal duurt het 292 uur om de migratie te volt ooien 

Dit is de geschatte prijs op basis van de bovenstaande hypo Thesen: 

![Scherm afbeelding van een tabel toont een geschatte prijs.](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)

### <a name="additional-references"></a>Aanvullende naslaginformatie 
- [Amazon Simple Storage-service connector](./connector-amazon-simple-storage-service.md)
- [Azure Blob Storage-connector](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2-connector](./connector-azure-data-lake-storage.md)
- [Gids voor het afstemmen van de activiteit prestaties kopiëren](./copy-activity-performance.md)
- [Zelf-hostende Integration Runtime maken en configureren](./create-self-hosted-integration-runtime.md)
- [Zelf-hostende runtime HA en schaal baarheid](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [Beveiligingsoverwegingen bij het verplaatsen van gegevens](./data-movement-security-considerations.md)
- [Referenties opslaan in Azure Key Vault](./store-credentials-in-key-vault.md)
- [Bestand incrementeel kopiëren op basis van een gepartitioneerde bestands naam](./tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)
- [Nieuwe en gewijzigde bestanden kopiëren op basis van LastModifiedDate](./tutorial-incremental-copy-lastmodified-copy-data-tool.md)
- [Pagina met prijzen voor ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="template"></a>Sjabloon

Dit is de [sjabloon](solution-template-migration-s3-azure.md) waarmee u begint met het migreren van PETA bytes aan gegevens van honderden miljoenen bestanden van Amazon S3 naar Azure data Lake Storage Gen2.

## <a name="next-steps"></a>Volgende stappen

- [Bestanden van meerdere containers met Azure Data Factory kopiëren](solution-template-copy-files-multiple-containers.md)