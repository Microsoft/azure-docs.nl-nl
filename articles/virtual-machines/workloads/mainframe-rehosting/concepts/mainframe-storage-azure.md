---
title: Mainframe-opslag verplaatsen naar Azure Storage
description: Zeer schaal bare Azure storage-resources kunnen op mainframe gebaseerde organisaties helpen bij het migreren en moderniseren van IBM z14-toepassingen.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 27f625e7fced5ff710a6fa0e8b2045a8c5434258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954357"
---
# <a name="move-mainframe-storage-to-azure"></a>Mainframe-opslag verplaatsen naar Azure

Als u mainframe-workloads op Microsoft Azure wilt uitvoeren, moet u weten hoe de functies van uw mainframe met Azure worden vergeleken. De zeer schaal bare opslag bronnen kunnen organisaties helpen te moderniseren zonder de toepassingen die ze nodig hebben, te verlaten.

Azure biedt mainframe achtige functies en opslag capaciteit die vergelijkbaar is met IBM z14-systemen (het meest actuele model op dit moment). In dit artikel leest u hoe u vergelijk bare resultaten krijgt op Azure.

## <a name="mainframe-storage-at-a-glance"></a>Mainframe opslag in één oogopslag

De IBM mainframe kenmerkt de opslag op twee manieren. De eerste is een opslag apparaat direct toegang (DASD). De tweede is sequentiële opslag. Voor het beheren van opslag biedt de mainframe het DFSMS (Storage Management Subsystem) van de gegevens faciliteit. Hiermee wordt de gegevens toegang tot de verschillende opslag apparaten beheerd.

[Dasd](https://en.wikipedia.org/wiki/Direct-access_storage_device) verwijst naar een afzonderlijk apparaat voor secundaire opslag (niet in het geheugen) waarmee een uniek adres kan worden gebruikt voor rechtstreekse toegang tot gegevens. Oorspronkelijk is de term DASD toegepast op draaiende schijven, magnetische vaten of gegevens cellen. De term kan echter ook worden toegepast op apparaten met een Ssd's (Storage Area Network), San's (Network Attached Storage) en optische stations. Voor de doel einden van dit document verwijst DASD naar draaiende schijven, San's en Ssd's.

In tegens telling tot DASD-opslag verwijst sequentiële opslag op een mainframe naar apparaten zoals tape stations waar gegevens vanaf een begin punt worden benaderd en vervolgens in een regel worden gelezen of geschreven.

Opslag apparaten worden meestal gekoppeld met behulp van een glasvezel verbinding (FICON) of worden rechtstreeks geopend op de i/o-bus van het mainframe met behulp van [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), een IBM-technologie voor snelle communicatie tussen partities op een server met een Hyper Visor.

De meeste mainframe systemen scheiden opslag in twee typen:

- *Online opslag* (ook wel bekend als Hot Storage) is nodig voor dagelijkse bewerkingen. DASD-opslag wordt doorgaans gebruikt voor dit doel einde. Sequentiële opslag, zoals dagelijkse tape back-ups (logische of fysieke), kan echter ook worden gebruikt voor dit doel einde.

- *Archief opslag* (ook wel koude opslag genoemd) is niet gegarandeerd om op een bepaald moment te koppelen. In plaats daarvan wordt het gekoppeld en toegankelijk als dat nodig is. Archief opslag wordt vaak geïmplementeerd met behulp van sequentiële tape back-ups (logische of fysieke) voor opslag.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe versus IO-latentie en IOPS

Mainframes worden vaak gebruikt voor toepassingen waarvoor hoge prestaties en een lage IO-latentie vereist zijn. Ze kunnen dit doen met behulp van de FICON-verbindingen met i/o-apparaten en HiperSockets. Wanneer HiperSockets worden gebruikt om toepassingen en apparaten rechtstreeks te verbinden met het i/o-kanaal van een mainframe, kan de micro seconden een latentie hebben.

## <a name="azure-storage-at-a-glance"></a>Azure-opslag in één oogopslag

Azure Infrastructure-as-a-Service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) opties voor opslag bieden vergelijk bare mainframe capaciteit.

Micro soft biedt PETA bytes opslag voor toepassingen die worden gehost in azure, en u hebt verschillende opslag opties. Dit bereik van SSD-opslag voor hoge prestaties tot een goedkope Blob-opslag voor massa opslag en archieven. Daarnaast biedt Azure een optie voor gegevens redundantie voor opslag: iets wat meer inspanning in beslag neemt bij het instellen van een mainframe omgeving.

Azure Storage is beschikbaar als [Azure-schijven](../../../managed-disks-overview.md), [Azure files](../../../../storage/files/storage-files-introduction.md)en [Azure-blobs](../../../../storage/blobs/storage-blobs-overview.md) , zoals in de volgende tabel wordt weer gegeven. Meer informatie over [Wanneer u deze gebruikt](../../../../storage/common/storage-introduction.md).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Description</th><th>Gebruiken wanneer u het volgende wilt doen:</th></tr>
</thead>
<tbody>
<tr><td>Azure Files
</td>
<td>
Biedt een SMB-interface,-client bibliotheken en een <a href="/rest/api/storageservices/file-service-rest-api">rest</a> -interface waarmee u overal toegang kunt krijgen tot opgeslagen bestanden.
</td>
<td><ul>
<li>Til en verplaats een toepassing naar de Cloud wanneer de toepassing gebruikmaakt van de systeem eigen Api's van het systeem voor het delen van gegevens tussen IT en andere toepassingen die worden uitgevoerd in Azure.</li>
<li>Bewaar hulpprogram ma's voor ontwikkeling en fout opsporing die toegankelijk moeten zijn vanaf veel Vm's.</li>
</ul>
</td>
</tr>
<tr><td>Azure-blobs
</td>
<td>Biedt client bibliotheken en een <a href="/rest/api/storageservices/blob-service-rest-api">rest</a> -interface waarmee ongestructureerde gegevens kunnen worden opgeslagen en geopend op een enorme schaal in blok-blobs. Biedt ook ondersteuning voor <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure data Lake Storage Gen2</a> voor oplossingen voor enter prise Big Data Analytics.
</td>
<td><ul>
<li>Ondersteuning voor streaming en scenario's voor wille keurige toegang in een toepassing.</li>
<li>Vanaf elke locatie toegang hebben tot toepassings gegevens.</li>
<li>Bouw een Enter prise data Lake on Azure en voer big data Analytics uit.</li>
</ul></td>
</tr>
<tr><td>Azure-schijven
</td>
<td>Biedt client bibliotheken en een <a href="/rest/api/compute/disks">rest</a> -interface waarmee gegevens permanent kunnen worden opgeslagen en geopend vanaf een gekoppelde virtuele harde schijf.
</td>
<td><ul>
<li>Til en verplaats toepassingen die gebruikmaken van systeem eigen bestandssysteem-Api's om gegevens te lezen en te schrijven naar permanente schijven.</li>
<li>Sla gegevens op die niet hoeven te worden geopend van buiten de virtuele machine waaraan de schijf is gekoppeld.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure Hot (online) en koude opslag (archief)

Het type opslag voor een bepaald systeem is afhankelijk van de vereisten van het systeem, met inbegrip van de opslag grootte, de door Voer en de IOPS. Voor DASD opslag op een mainframe gebruiken toepassingen in azure doorgaans Azure disks-opslag in plaats daarvan. Voor mainframe archief opslag wordt Blob-opslag gebruikt in Azure.

Ssd's bieden de hoogste opslag prestaties op Azure. De volgende opties zijn beschikbaar (vanaf het schrijven van dit document):

| Type         | Grootte           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra - SSD    | 4 GB tot 64 TB  | 1.200 tot 160.000 IOPS |
| Premium SSD  | 32 GB tot 32 TB | 12 tot 15.000 IOPS     |
| Standard SSD | 32 GB tot 32 TB | 12 tot 2.000 IOPS      |

Blob-opslag biedt de grootste hoeveelheid opslag ruimte op Azure. Naast de opslag grootte biedt Azure zowel beheerde als onbeheerde opslag. Met beheerde opslag neemt Azure zorg voor het beheren van de onderliggende opslag accounts. Met niet-beheerde opslag neemt de gebruiker verantwoordelijk voor het instellen van Azure-opslag accounts van de juiste grootte om te voldoen aan de opslag vereisten.

## <a name="next-steps"></a>Volgende stappen

- [Mainframe migratie](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe herhosten op Azure Virtual Machines](../overview.md)
- [Mainframe Compute verplaatsen naar Azure](mainframe-compute-Azure.md)
- [Bepalen wanneer u Azure-blobs, Azure Files of Azure-schijven wilt gebruiken](../../../../storage/common/storage-introduction.md)
- [Standard-SSD Managed Disks voor Azure VM-workloads](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>IBM-bronnen

- [Parallelle Sysplex op IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS en de koppelings faciliteit: meer dan de basis beginselen](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [De vereiste gebruikers voor een installatie van een Db2 pureScale-functie maken](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [De opdracht Db2icrt-instantie maken](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Geclusterde database oplossing voor Db2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government Cloud voor mainframe-toepassingen](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Micro soft en FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Meer migratie resources

- [Azure Virtual Data Center lift-en Shift-gids](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)