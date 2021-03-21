---
title: 'Quickstart: Een werkstroom uitvoeren - Microsoft Genomics'
description: In de snelstartgids wordt uitgelegd hoe u invoergegevens kunt laden in Azure Blob Storage en een werkstroom kunt uitvoeren via de service Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.custom: devx-track-python
ms.openlocfilehash: 6daec7658c6db6c18bd198b9aff0b8993f27ac22
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177244"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Quickstart: Een werkstroom uitvoeren via de Microsoft Genomics-service

In deze quickstart uploadt u invoergegevens naar een Azure Blob Storage-account en voert u een werkstroom uit via de Microsoft Genomics-service met behulp van de Python Genomics-client. Microsoft Genomics is een schaalbare, veilige service voor secundaire analyse waarbij genomen snel kunnen worden verwerkt. Hierbij wordt begonnen met onbewerkte leesbewerkingen en worden vervolgens uitgelijnde leesbewerkingen en variant-aanroepen geproduceerd. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Python 2.7.12 +](https://www.python.org/downloads/release/python-2714/) met `pip` geïnstalleerd en `python` in het systeempad. De Microsoft Genomics-client is niet compatibel met Python 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Instellen: Een Microsoft Genomics-account maken in de Azure-portal

Ga voor het maken van een Microsoft Genomics-account naar [Een Genomics-account maken](https://portal.azure.com/#create/Microsoft.Genomics) in Azure Portal. Als u nog geen Azure-abonnement hebt, maakt u er een voordat u een Microsoft Genomics-account maakt. 

![Microsoft Genomics in Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics in Azure Portal")

Configureer uw Genomics-account met de volgende informatie, zoals weergegeven in de vorige afbeelding. 

 |**Instelling**          |  **Voorgestelde waarde**  | **Beschrijving van veld** |
 |:-------------       |:-------------         |:----------            |
 |Abonnement         | De naam van uw abonnement|Dit is de factureringseenheid voor uw Azure-services; zie [Abonnementen](https://account.azure.com/Subscriptions) voor meer informatie over uw abonnement. |      
 |Resourcegroep       | MyResourceGroup       |  Met resourcegroepen kunt u meerdere Azure-resources (opslagaccount, Genomics-account enzovoort) in één groep indelen voor eenvoudig beheer. Zie [Resourcegroepen](../azure-resource-manager/management/overview.md#resource-groups) voor meer informatie. Zie [Naamgevingsregels](/azure/architecture/best-practices/resource-naming) voor geldige resourcegroepnamen. |
 |Accountnaam         | MyGenomicsAccount     |Kies een unieke account-id. Zie [Naamgevingsregels](/azure/architecture/best-practices/resource-naming) voor geldige namen. |
 |Locatie                   | VS - west 2                    |    De service is beschikbaar in VS - west 2, Europa - west en Azië - zuidoost |

U kunt in de bovenste menubalk **Meldingen** selecteren om het implementatieproces te controleren.

![Meldingen](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Meldingen")

Zie [Wat is Microsoft Genomics?](overview-what-is-genomics.md) voor meer informatie over Microsoft Genomics.

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Instellen: De Python-client voor Microsoft Genomics installeren

U moet zowel Python als de Python-client voor Microsoft Genomics `msgen` in uw lokale omgeving installeren. 

### <a name="install-python"></a>Python installeren

De Python-client voor Microsoft Genomics is compatibel met Python 2.7.12 of een nieuwere 2.7.xx-versie. 2.7.14 is de voorgestelde versie. U vindt de download [hier](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Python 3.x is niet compatibel met Python 2.7.xx.  `msgen` is een Python 2.7-app. Zorg er bij het uitvoeren van `msgen` voor dat uw actieve Python-omgeving een 2.7.xx-versie van Python gebruikt. Er treden mogelijk fouten op bij het gebruik van `msgen` met een 3.x-versie van Python.

### <a name="install-the-microsoft-genomics-python-client-msgen"></a>De Python-client voor Microsoft Genomics `msgen` installeren

Gebruik Python-`pip` om de Microsoft Genomics-client `msgen` te installeren. Bij de volgende instructies wordt ervan uitgegaan dat Python 2.x al in het systeempad staat. Als de installatie van `pip` niet wordt herkend, moet u Python en de submap voor scripts aan het systeempad toe te voegen.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Als u `msgen` niet wilt installeren als een systeembreed binair bestand en systeembrede Python-pakketten niet wilt wijzigen, gebruikt u de markering `–-user` met `pip`.
Als u de installatie op basis van een pakket of setup.py gebruikt, worden alle vereiste pakketten geïnstalleerd.

### <a name="test-msgen-python-client"></a>Test de Python-client `msgen`
Download het configuratiebestand in uw Genomics-account om de client voor Microsoft Genomics te testen. Navigeer in Azure Portal naar uw Genomics-account door links bovenin **Alle services** te selecteren en vervolgens Genomics-accounts te zoeken en te selecteren.

![Microsoft Genomics opzoeken in Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Microsoft Genomics opzoeken in Azure Portal")

Selecteer het Genomics-account dat u zojuist hebt gemaakt, ga naar **Toegangssleutels** en download het configuratiebestand.

![Het configuratiebestand downloaden uit Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Het configuratiebestand downloaden uit Microsoft Genomics")

Test of de Python-client voor Microsoft Genomics werkt met de volgende opdracht

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Een Microsoft Azure Storage-account maken 
De service Microsoft Genomics verwacht dat invoeren worden opgeslagen als blok-blobs in een Azure-opslagaccount. Ook schrijft de service uitvoerbestanden als blok-blobs naar een door de gebruiker opgegeven container in een Azure-opslagaccount. De invoeren en uitvoeren kunnen zich in verschillende opslagaccounts bevinden.
Als u uw gegevens al in een Azure-opslagaccount hebt, hoeft u alleen ervoor te zorgen dat deze zich op dezelfde locatie bevinden als uw Genomics-account. Anders worden er uitvoerkosten gemaakt wanneer de Microsoft Genomics-service wordt uitgevoerd. Als u nog geen Azure-opslagaccount hebt, dient u er een te maken en uw gegevens te uploaden. U vindt [hier](../storage/common/storage-account-create.md) meer informatie over Azure-opslagaccounts, met inbegrip van wat een opslagaccount is en welke services het biedt. Ga naar [Opslagaccount maken](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) in Azure Portal om een Azure-opslagaccount te maken.  

![De pagina voor opslagaccount maken](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "De pagina voor het opslagaccount maken")

Configureer uw opslagaccount met de volgende informatie, zoals weergegeven in de vorige afbeelding. Gebruik de meeste van de standaardopties voor een opslagaccount en geef alleen op dat het account BlobStorage is en niet algemeen. Blob-opslag kan twee tot vijf keer sneller zijn voor downloads en uploads.  Het standaardimplementatiemodel, Azure Resource Manager, wordt aanbevolen.  

 |**Instelling**          |  **Voorgestelde waarde**  | **Beschrijving van veld** |
 |:-------------------------       |:-------------         |:----------            |
 |Abonnement         | Uw Azure-abonnement |Zie [Abonnementen](https://account.azure.com/Subscriptions) voor meer informatie over uw abonnement. |      
 |Resourcegroep       | MyResourceGroup       |  U kunt dezelfde resourcegroep als voor uw Genomics-account selecteren. Zie [Naamgevingsregels](/azure/architecture/best-practices/resource-naming) voor geldige resourcegroepnamen |
 |Naam van opslagaccount         | MyStorageAccount     |Kies een unieke account-id. Zie [Naamgevingsregels](/azure/architecture/best-practices/resource-naming) voor geldige namen |
 |Locatie                  | VS - west 2                  | Gebruik dezelfde locatie als de locatie van uw Genomics-account om uitvoerkosten te verminderen en latentie te beperken.  | 
 |Prestaties                  | Standard                   | Standard is de standaardinstelling. Zie [Kennismaking met Microsoft Azure Storage](../storage/common/storage-introduction.md) voor meer informatie over standaard- en premium-opslagaccounts    |
 |Soort account       | BlobStorage       |  Blob-opslag kan twee tot vijf keer sneller zijn dan algemeen gebruik voor downloads en uploads. |
 |Replicatie                  | Lokaal redundante opslag                  | Lokaal redundante opslag repliceert uw gegevens in het datacenter in de regio waarin u uw opslagaccount hebt gemaakt. Zie [Azure Storage-replicatie](../storage/common/storage-redundancy.md) voor meer informatie.    |
 |Toegangslaag                  | Warm                   | De toegangslaag Hot geeft aan dat de objecten in het opslagaccount regelmatig worden gebruikt.    |

Selecteer daarna **Beoordelen en maken** om uw opslagaccount te maken. Net als bij het maken van uw Genomics-account, kunt u in de bovenste menubalk **Meldingen** selecteren om het implementatieproces te controleren. 

## <a name="upload-input-data-to-your-storage-account"></a>De invoergegevens naar uw opslagaccount uploaden

De Microsoft Genomics-service verwacht leesbewerkingen voor eindsequenties (FASTQ- of BAM-bestanden) als invoerbestanden. U kunt uw eigen gegevens uploaden, of openbare voorbeeldgegevens verkennen die voor u beschikbaar zijn gesteld. De openbare voorbeeldgegevens worden hier gehost:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

U dient binnen uw opslagaccount een blob-container te maken voor uw invoergegevens en een tweede blob-container voor de uitvoergegevens.  Upload de invoergegevens naar uw blob-container voor invoer. U kunt hiervoor verschillende hulpprogramma's gebruiken, waaronder [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter) en [AzCopy](../storage/common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-msgen-python-client"></a>Een werkstroom uitvoeren via de Microsoft Genomics-service met behulp van de Python-client `msgen`

Als u een werkstroom wilt uitvoeren via de service Microsoft Genomics, bewerkt u het bestand *config.txt* om de invoer- en uitvoeropslagcontainer voor uw gegevens op te geven.
Open het bestand *config.txt* dat u van uw Genomics-account hebt gedownload. De gedeelten die u dient op te geven zijn de abonnementssleutel en de zes items onderaan, de naam van het opslagaccount, de sleutel en de naam van de container voor zowel invoer als uitvoer. U kunt deze informatie vinden door in Azure Portal naar **Toegangssleutels** voor uw opslagaccount te navigeren of rechtstreeks vanuit Azure Storage Explorer.  

![Genomics-configuratie](./media/quickstart-run-genomics-workflow-portal/genomics-config.PNG "Genomics-configuratie")

Als u graag GATK4 wilt uitvoeren, stelt u de parameter `process_name` in op `gatk4`.

De Genomics-service voert standaard VCF-bestanden uit. Als u graag gVCF-uitvoer wilt in plaats van VCF-uitvoer (gelijk aan `-emitRefConfidence` in GATK 3.x en `emit-ref-confidence` in GATK 4.x), voegt u de parameter `emit_ref_confidence` toe aan uw *config.txt* en stelt u deze in op `gvcf`, zoals wordt weergegeven in de vorige afbeelding.  Als u weer terug wilt naar VCF-uitvoer, verwijdert u deze uit het bestand *config.txt* of stelt u de parameter `emit_ref_confidence` in op `none`. 

`bgzip` is een hulpprogramma waarmee het VCF- of gVCF-bestand wordt gecomprimeerd en `tabix` een index voor het gecomprimeerde bestand maakt. De Genomics-service voert standaard `bgzip` gevolgd door `tabix` uit voor '.g.vcf'-uitvoer, maar voert deze hulpprogramma's niet standaard uit voor '.vcf '-uitvoer. Wanneer u de service uitvoert, resulteert dit in '.gz'- (bgzip-uitvoer) en 'tbi'-bestanden (tabix-uitvoer). Het argument is een Booleaanse waarde, die standaard is ingesteld op False voor '.vcf'-uitvoer en standaard is ingesteld op True voor '.g.vcf'-uitvoer. Als u dit wilt gebruiken op de opdrachtregel, geeft u `-bz` of `--bgzip-output` op als `true` (voer bgzip en tabix uit) of `false`. Als u dit argument wilt gebruiken in het bestand *config.txt*, voegt u `bgzip_output: true` of `bgzip_output: false` toe aan het bestand.

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-using-the-msgen-python-client"></a>Uw werkstroom verzenden naar de Microsoft Genomics-service met behulp van de Python-client `msgen`

Gebruik de Python-client voor Microsoft Genomics om uw werkstroom te verzenden met de volgende opdracht:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

U kunt de status van uw werkstromen bekijken met de volgende opdracht: 
```python
msgen list -f c:\temp\config.txt 
```

Nadat de werkstroom is voltooid, kunt u de uitvoerbestanden weergeven in uw Azure-opslagaccount in de uitvoercontainer die u hebt geconfigureerd. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u voorbeeldinvoergegevens geüpload naar Azure Storage en een werkstroom verzonden naar de Microsoft Genomics-service via de Python-client voor `msgen`. Ga voor meer informatie over andere invoerbestandstypen die kunnen worden gebruikt met de Microsoft Genomics-service naar de volgende pagina's: [paired FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ or BAM](quickstart-input-multiple.md) (gekoppelde FASTQ/BAM/Meerdere FASTQ of BAM). 
