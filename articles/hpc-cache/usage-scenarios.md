---
title: Scenario's voor de HPC-cache van Azure
description: Hierin wordt beschreven hoe u kunt weten of uw computer taak goed werkt met de Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259958"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>Is uw werk goed geschikt voor de Azure HPC-cache?

Met de Azure HPC-cache kunt u sneller toegang krijgen tot gegevens voor high-performance computing-taken in verschillende disciplines. Maar is niet perfect voor alle typen werk stromen. Dit artikel bevat richt lijnen voor het bepalen of HPC-cache een goede optie is voor uw behoeften.

Het [overzichts](hpc-cache-overview.md) artikel bevat ook een kort overzicht van het gebruik van Azure HPC cache en enkele voor beelden van gebruiks voorbeelden.

Lees ook [dit artikel](nfs-blob-considerations.md) over het uitvoeren van een effectief gebruik van met [NFS gekoppelde Blob-opslag](../storage/blobs/network-file-system-protocol-support.md), die in preview is.

## <a name="nfs-version-30-applications"></a>NFS versie 3,0-toepassingen

Azure HPC cache ondersteunt alleen NFS 3,0-clients.

## <a name="high-read-to-write-ratio"></a>Hoge verhouding voor lezen/schrijven

Werk belastingen waarbij de compute-clients meer kunnen lezen dan ze schrijven, zijn meestal goede kandidaten voor een cache. Als uw Lees-naar-schrijf ratio bijvoorbeeld 80/20 of 70/30 is, kan Azure HPC-cache helpen bij het bezorgen van regel matig aangevraagde bestanden uit de cache in plaats van deze te hoeven ophalen uit externe opslag.

Het ophalen van een bestand en het opslaan in de cache voor de eerste keer heeft een kleine extra latentie via een normale client aanvraag rechtstreeks naar de opslag, waardoor de efficiëntie Boost de volgende keer dat een client hetzelfde bestand aanvraagt. Dit geldt met name voor grote bestanden. Als elke client aanvraag uniek is, is de impact van HPC-cache beperkt. Maar hoe groter het bestand, hoe beter de prestaties na de eerste toegang meer zijn dan de tijd.

## <a name="file-based-analytic-workload"></a>Op bestanden gebaseerde analytische werk belasting

De Azure HPC-cache is ideaal voor een pijp lijn die gebruikmaakt van op bestanden gebaseerde gegevens en wordt uitgevoerd op een groot aantal Compute-clients, met name als de compute-clients virtuele machines van Azure zijn. Dit kan helpen om trage of inconsistente prestaties te herstellen die worden veroorzaakt door lange bestands toegangs tijden.

## <a name="remote-data-access"></a>Externe toegang tot gegevens

Met de Azure HPC-cache kunt u de latentie verminderen als uw werk belasting toegang moet hebben tot externe gegevens die niet dichter bij de computer bronnen kunnen worden geplaatst. Uw records kunnen bijvoorbeeld aan het einde van een WAN-omgeving, in een andere Azure-regio of in een klant Data Center zijn. (Dit wordt ook wel ' file-bursting ' genoemd.)

## <a name="heavy-request-load"></a>Zware belasting van aanvragen

Als een groot aantal clients tegelijkertijd gegevens van de bron aanvraagt, kan de toegang tot het bestand sneller worden gemaakt met Azure HPC-cache. Als u bijvoorbeeld gebruikt met een krachtig Computing Cluster, biedt Azure HPC-cache schaal baarheid voor een groot aantal gelijktijdige aanvragen via de cache.

## <a name="compute-resources-are-located-in-azure"></a>Reken resources bevinden zich in azure

Virtuele Azure-machines zijn een schaalbaar en rendabel antwoord op de werk belasting met High Performance Computing. Met behulp van de Azure HPC-cache kunt u de gegevens die ze dichter bij hen moeten plaatsen, met name als de oorspronkelijke gegevens worden opgeslagen op een extern systeem.

Als een klant hun huidige pijp lijn wil uitvoeren in azure virtual machines, kan Azure HPC cache een op POSIX gebaseerde, gedeelde opslag (of caching)-oplossing bieden voor schaal baarheid.

Met behulp van de HPC-cache van Azure hoeft u de werk pijplijn niet opnieuw te architecten om systeem eigen aanroepen naar Azure Blob-opslag te maken. U hebt toegang tot uw gegevens op het oorspronkelijke systeem of u kunt HPC-cache gebruiken om deze naar een nieuwe BLOB-container te verplaatsen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het plannen en configureren van een cache in de artikelen [overzicht](hpc-cache-overview.md) en [vereisten](hpc-cache-prerequisites.md)
* Lees overwegingen voor het gebruik van [Blob Storage met NFS](nfs-blob-considerations.md) (preview) met Azure HPC cache
