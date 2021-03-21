---
title: Een Azure-oplossing kiezen voor periodieke gegevens overdracht | Microsoft Docs
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevens overdracht wanneer u periodiek gegevens overbrengt.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: a15ebd43861e2116ddbb2d9055b289645962e203
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96573915"
---
# <a name="solutions-for-periodic-data-transfer"></a>Oplossingen voor periodieke gegevensoverdracht
 
Dit artikel bevat een overzicht van de oplossingen voor gegevens overdracht wanneer u periodiek gegevens overbrengt. Periodieke gegevens overdracht via het netwerk kan worden gecategoriseerd als herhaald met regel matige intervallen of continue gegevens verplaatsing. In het artikel worden ook de aanbevolen opties voor gegevens overdracht en de bijbehorende matrix voor de belangrijkste functionaliteit voor dit scenario beschreven.

Voor een overzicht van alle beschik bare opties voor gegevens overdracht gaat u naar [een Azure-oplossing voor gegevens overdracht kiezen](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Aanbevolen opties

De aanbevolen opties voor periodieke gegevensoverdracht kunnen worden onderverdeeld in twee categorieën, afhankelijk van of de overdracht terugkerend of continu is.

- **Scripted/programmatische hulpprogram ma's** : voor gegevens overdracht die met regel matige tussen pozen plaatsvindt, gebruikt u het script en Program Ma's zoals AzCopy en Azure Storage rest-api's. Deze hulpprogramma's zijn ontworpen voor IT-professionals en ontwikkelaars.

    - **AzCopy** : gebruik dit opdracht regel programma om eenvoudig gegevens te kopiëren van en naar Azure-blobs,-bestanden en-tabel opslag met optimale prestaties. AzCopy biedt ondersteuning voor gelijktijdigheid en parallellisme, en de mogelijkheid om kopieerbewerkingen te hervatten als deze worden onderbroken.
    - **Azure Storage rest api's/sdk's** : wanneer u een toepassing bouwt, kunt u de toepassing ontwikkelen tegen Azure Storage rest api's en de Azure-sdk's gebruiken die in meerdere talen worden aangeboden. De REST-Api's kunnen ook gebruikmaken van de Azure Storage gegevens verplaatsings bibliotheek die speciaal is ontworpen voor het snel kopiëren van gegevens van en naar Azure.

- **Voortdurende hulp middelen voor gegevens opname** : voor continue, doorlopende opname van gegevens kunt u een van data Box apparaat voor online overdracht of Azure Data Factory selecteren. Deze hulpprogramma's worden ingesteld door IT-professionals en kunnen de gegevensoverdracht op transparante wijze automatiseren.

    - **Azure Data Factory** -Data Factory moet worden gebruikt voor het uitschalen van een overdrachts bewerking en als er sprake is van indelings-en bewakings mogelijkheden voor bedrijfs kwaliteit. Gebruik Azure Data Factory om een cloudpijplijn in te stellen waarmee regelmatig bestanden worden overgebracht tussen verschillende Azure-services, on-premises, of een combinatie van deze twee. Met Azure Data Factory kunt u gegevensgestuurde werkstromen indelen waarmee gegevens worden opgenomen uit verschillende gegevensarchieven, en kunt u de verplaatsing en transformatie van gegevens automatiseren.
    - **Azure data Box-familie voor online overdrachten** : Data Box Edge en data Box gateway zijn online netwerk apparaten waarmee gegevens kunnen worden verplaatst van en naar Azure. Data Box Edge maakt gebruik van Edge-computing met AI (kunstmatige intelligentie) om gegevens eerst te verwerken vóór de upload. Data Box Gateway is een virtuele versie van het apparaat met dezelfde mogelijkheden voor gegevensoverdracht.


## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

In de volgende tabel worden de verschillen tussen de belangrijkste mogelijkheden samengevat.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Script-en programmatische netwerk gegevens overdracht

| Mogelijkheid                  | AzCopy                                 | Azure Storage REST API's       |
|-----------------------------|----------------------------------------|-------------------------------|
| Vorm factor                 | Opdracht regel programma van micro soft       | Klanten ontwikkelen op basis van opslag <br> REST-Api's met behulp van Azure-client bibliotheken |
| Eerste eenmalige installatie     | Minimaal                                | Gemiddelde ontwikkelings inspanning van variabele    |
| Gegevensindeling                 | Azure-blobs, Azure Files, Azure-tabellen | Azure-blobs, Azure Files, Azure-tabellen   |
| Prestaties                 | Al geoptimaliseerd                      | Optimaliseren tijdens het ontwikkelen                  |
| Prijzen                     | Er zijn gratis kosten voor het uitbrengen van gegevens van toepassing      | Er zijn gratis kosten voor het uitbrengen van gegevens van toepassing        |

### <a name="continuous-data-ingestion-over-network"></a>Doorlopende gegevens opname via het netwerk

| Functie                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Vorm factor                                   | Virtueel apparaat             | Fysiek apparaat          | Service in Azure Portal, on-premises agent                                                            |
| Hardware                                      | Uw Hyper Visor            | Geleverd door micro soft    | NA                                                            |
| Initiële installatie-inspanning                          | Laag (<30 minuten)            | Gemiddeld (~ paar uur) | Groot (~ dagen)                                                 |
| Gegevensindeling                                   | Azure-blobs, Azure Files   | Azure-blobs, Azure Files | [Ondersteunt 70 en data connectors voor gegevens archieven en-indelingen](../../data-factory/copy-activity-overview.md#supported-data-stores-and-formats)|
| Vooraf verwerkte gegevens                           | Nee                         | Ja, via Edge compute    | Ja                                                           |
| Lokale cache<br>(voor het opslaan van on-premises gegevens)    | Ja                        | Ja                      | Nee                                                            |
| Overdracht van andere Clouds                    | Nee                         | Nee                       | Ja                                                           |
| Prijzen                                       | [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Prijzen](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Volgende stappen

- [Gegevens overdragen met AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Meer informatie over gegevens overdracht met rest-api's voor opslag](/dotnet/api/overview/azure/storage).
- Meer informatie over:
    - [Gegevens overdragen met Data Box Gateway](../../databox-gateway/data-box-gateway-deploy-add-shares.md).
    - [Transformeer gegevens met data Box Edge voordat ze naar Azure worden verzonden](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Meer informatie over het overdragen van gegevens met Azure Data Factory](../../data-factory/tutorial-bulk-copy-portal.md).