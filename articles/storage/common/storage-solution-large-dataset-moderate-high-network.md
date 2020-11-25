---
title: Opties voor Azure-gegevens overdracht voor grote gegevens sets, gemiddeld tot hoge netwerk bandbreedte | Microsoft Docs
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevens overdracht wanneer u een hoge netwerk bandbreedte in uw omgeving hebt, en u van plan bent om grote gegevens sets over te dragen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 962bb7a4484f28d52ffd4f0cae985140ec2f0d28
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006027"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Gegevensoverdracht voor grote gegevenssets met gemiddelde tot grote netwerkbandbreedte
 
Dit artikel bevat een overzicht van de oplossingen voor gegevens overdracht wanneer u een hoge netwerk bandbreedte in uw omgeving hebt en u van plan bent grote gegevens sets over te dragen. In het artikel worden ook de aanbevolen opties voor gegevens overdracht en de bijbehorende matrix voor de belangrijkste functionaliteit voor dit scenario beschreven.

Voor een overzicht van alle beschik bare opties voor gegevens overdracht gaat u naar [een Azure-oplossing voor gegevens overdracht kiezen](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeschrijving

Grote gegevens sets verwijzen naar de grootte in de volg orde van TBs naar PBs. Gemiddeld tot hoge netwerk bandbreedte verwijst naar 100 Mbps tot 10 Gbps.

## <a name="recommended-options"></a>Aanbevolen opties

De opties die worden aanbevolen in dit scenario, zijn afhankelijk van of u beschikt over de gemiddelde netwerkbandbreedte of de hoge netwerkbandbreedte.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Gemiddelde netwerkbandbreedte (100 Mbps - 1 Gbps)

Met een gemiddelde netwerk bandbreedte moet u de tijd voor gegevens overdracht via het netwerk projecteren.

Gebruik de volgende tabel om de tijd te schatten en op basis van die te kiezen tussen een offline overdracht of via de netwerk overdracht. De tabel bevat de geschatte tijd voor het verzenden van netwerk gegevens, voor verschillende beschik bare netwerk bandbreedte (uitgaande van 90% gebruik).  

![Netwerk overdracht of offline overdracht](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Als de netwerk overdracht te langzaam wordt geprojecteerd, moet u een fysiek apparaat gebruiken. De aanbevolen opties in dit geval zijn de apparaten voor offline overdracht van Azure Data Box serie of Azure import/export met uw eigen schijven.

    - **Azure data Box-serie voor offline overdrachten** : gebruik apparaten van door micro soft geleverde data Box apparaten om grote hoeveel heden gegevens naar Azure te verplaatsen wanneer u beperkt bent door tijd, netwerk beschikbaarheid of kosten. On-premises gegevens kopiëren met behulp van hulpprogramma's zoals Robocopy. Afhankelijk van de grootte van de gegevens die u wilt overdragen, kunt u kiezen uit Data Box Disk, Data Box of Data Box Heavy.
    - **Azure import/export** : gebruik Azure import/export-service door uw eigen schijf stations te verzenden om grote hoeveel heden gegevens veilig te importeren naar Azure Blob-opslag en Azure files. Deze service kan ook worden gebruikt om gegevens over te dragen van Azure Blob-opslag naar schijfstations en te verzenden naar uw on-premises-locaties.

- Als de netwerk overdracht redelijk wordt geprojecteerd, kunt u een van de volgende hulpprogram ma's gebruiken die worden beschreven in [hoge netwerk bandbreedte](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Hoge netwerkbandbreedte (1 Gbps - 100 Gbps)

Als de beschik bare netwerk bandbreedte hoog is, gebruikt u een van de volgende hulpprogram ma's.

- **AzCopy** : gebruik dit opdracht regel programma om eenvoudig gegevens te kopiëren van en naar Azure-blobs,-bestanden en-tabel opslag met optimale prestaties. AzCopy biedt ondersteuning voor gelijktijdigheid en parallellisme, en de mogelijkheid om kopieerbewerkingen te hervatten als deze worden onderbroken.
- **Azure Storage rest api's/sdk's** : wanneer u een toepassing bouwt, kunt u de toepassing ontwikkelen tegen Azure Storage rest api's en de Azure-sdk's gebruiken die in meerdere talen worden aangeboden.
- **Azure data Box-familie voor online overdrachten** : Data Box Edge en data Box gateway zijn online netwerk apparaten waarmee gegevens kunnen worden verplaatst van en naar Azure. Gebruik een fysiek Data Box Edge-apparaat wanneer continue opname en vooraf verwerken van de gegevens vóór de upload, even belangrijk zijn. Data Box Gateway is een virtuele versie van het apparaat met dezelfde mogelijkheden voor gegevensoverdracht. De gegevensoverdracht wordt altijd beheerd met het apparaat.
- **Azure Data Factory** -Data Factory moet worden gebruikt voor het uitschalen van een overdrachts bewerking en als er sprake is van indelings-en bewakings mogelijkheden voor bedrijfs kwaliteit. Gebruik Data Factory om regelmatig bestanden over te brengen tussen verschillende Azure-services, on-premises, of een combinatie van deze twee. Met Data Factory kunt u gegevensgestuurde werkstromen (pijplijnen genoemd) maken en plannen die gegevens uit verschillende gegevensarchieven opnemen, en die de verplaatsing en transformatie van gegevens automatiseren.

## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

In de volgende tabellen vindt u een overzicht van de verschillen in de belangrijkste mogelijkheden voor de aanbevolen opties.

### <a name="moderate-network-bandwidth"></a>Gemiddelde netwerk bandbreedte

Als u offline gegevens overdracht gebruikt, gebruikt u de volgende tabel om inzicht te krijgen in de verschillen in de belangrijkste mogelijkheden.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Gegevens grootte**                    |    Maxi maal 35 TBs                 |    Maxi maal 80 TBs per apparaat                       |    Tot 800 TB per apparaat               |    Variabele                            |
|    **Gegevenstype**                    |    Azure-blobs                  |    Azure-blobs<br>Azure Files                    |    Azure-blobs<br>Azure Files            |    Azure-blobs<br>Azure Files          |
|    **Vorm factor**                  |    5 Ssd's per bestelling             |    1 X 50 kg. apparaat op computer formaat per bestelling    |    1 X ~ 500 kg. groot apparaat per order    |    Maxi maal 10 Hdd's/Ssd's per bestelling        |
|    **Eerste instel tijd**               |    Beperkt <br>(15 minuten)            |    Laag tot gemiddeld <br> (<30 minuten)               |    Matig<br>(1-2 uur)               |    Gemiddeld tot moeilijk<br>variabeletype |
|    **Gegevens verzenden naar Azure**           |    Ja                          |    Ja                                           |    Ja                                   |    Ja                                 |
|    **Gegevens importeren uit Azure**           |    Nee                           |    Nee                                            |    Nee                                    |    Ja                                 |
|    **Versleuteling**                   |    AES 128-bits                  |    AES 256-bits                                   |    AES 256-bits                           |    AES 128-bits                         |
|    **Hardware**                     |     Geleverd door micro soft          |    Geleverd door micro soft                            |    Geleverd door micro soft                    |    Klant verstrekt                   |
|    **Netwerkinterface**            |    USB 3.1/SATA                 |    RJ 45, SFP +                                   |    RJ45, QSFP +                           |    SATA II/SATA III                    |
|    **Partnerintegratie**          |    Enkele                         |    [Hoog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Hoog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Enkele                                |
|    **Verzending**                     |    Door micro soft beheerd            |    Door micro soft beheerd                             |    Door micro soft beheerd                     |    Door de klant beheerd                    |
| **Gebruiken wanneer gegevens worden verplaatst**     |Binnen een Commerce grens|Binnen een Commerce grens|Binnen een Commerce grens|Over geografische grenzen, bijvoorbeeld VS naar EU|
|    **Prijzen**                          |    [Prijzen](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Prijzen](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Als u online gegevens overdracht gebruikt, gebruikt u de tabel in de volgende sectie voor een hoge netwerk bandbreedte.

### <a name="high-network-bandwidth"></a>Hoge netwerk bandbreedte

|                                     |    Hulpprogram ma's AzCopy, <br>Azure PowerShell <br>Azure CLI             |    Azure Storage REST Api's, Sdk's                   |    Data Box Gateway of Data Box Edge          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    **Gegevenstype**              |    Azure-blobs, Azure Files, Azure-tabellen    |    Azure-blobs, Azure Files, Azure-tabellen    |    Azure-blobs, Azure Files                           |   Ondersteunt 70 en data connectors voor gegevens archieven en-indelingen    |
|    **Vorm factor**            |    Opdrachtregelprogramma's                        |    Programmatische interface                    |    Micro soft levert een virtueel <br>of fysiek apparaat     |    Service in Azure Portal                                            |
|    **Eerste eenmalige installatie** |    Probleem               |    Matig                       |    Eenvoudig (<30 minuten) tot gemiddeld (1-2 uur)            |    Dergaan                                                          |
|    **Vooraf verwerkte gegevens**          |    Nee                                        |    Nee                                        |    Ja (met Edge Compute)                               |    Yes                                                                |
|    **Overdracht van andere Clouds**   |    Nee                                        |    Nee                                        |    Nee                                                    |    Ja                                                                |
|    **Gebruikers type**                    |    IT-professionals of dev                                       |    Dev                                       |    IT pro                                                |    IT pro                                                             |
|    **Prijzen**                      |    Er zijn gratis kosten voor het uitbrengen van gegevens van toepassing         |    Er zijn gratis kosten voor het uitbrengen van gegevens van toepassing         |    [Prijzen](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Prijzen](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het overdragen van gegevens met import/export](./storage-import-export-data-to-blobs.md).
- Begrijpen hoe u

    - [Gegevens overdragen met data Box Disk](../../databox/data-box-disk-quickstart-portal.md).
    - [Gegevens overdragen met data Box](../../databox/data-box-quickstart-portal.md).
- [Gegevens overdragen met AzCopy](./storage-use-azcopy-v10.md).
- Meer informatie over:
    - [Gegevens overdragen met Data Box Gateway](../../databox-online/data-box-gateway-deploy-add-shares.md).
    - [Transformeer gegevens met data Box Edge voordat ze naar Azure worden verzonden](../../databox-online/azure-stack-edge-deploy-configure-compute.md).
- [Meer informatie over het overdragen van gegevens met Azure Data Factory](../../data-factory/quickstart-create-data-factory-portal.md).
- De REST-Api's gebruiken om gegevens over te dragen

    - [In .NET](/dotnet/api/overview/azure/storage)
    - [In Java](/java/api/overview/azure/storage)