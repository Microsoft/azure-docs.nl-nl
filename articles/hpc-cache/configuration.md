---
title: Instellingen voor de Azure HPC-cache configureren
description: In dit artikel wordt uitgelegd hoe u aanvullende instellingen voor de cache configureert, zoals MTU en no-root-Squash, en hoe u toegang krijgt tot de Express-moment opnamen van Azure Blob-opslag doelen.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 12/21/2020
ms.author: v-erkel
ms.openlocfilehash: 02bf862cdc3b20ef3e5fdb024f474267efa0c70d
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760500"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Aanvullende instellingen voor de Azure HPC-cache configureren

De **configuratie** pagina in de Azure Portal bevat opties voor het aanpassen van verschillende instellingen. De meeste gebruikers hoeven deze instellingen niet te wijzigen van hun standaard waarden.

In dit artikel wordt ook beschreven hoe u de functie snap shot gebruikt voor Azure Blob Storage-doelen. De momentopname functie heeft geen Configureer bare instellingen.

Als u de instellingen wilt zien, opent u de pagina **configuratie** van de cache in de Azure Portal.

![scherm afbeelding van de configuratie pagina in Azure Portal](media/configuration.png)

> [!TIP]
> De [video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) voor het beheren van de HPC-cache van Azure bevat de configuratie pagina en de bijbehorende instellingen.

## <a name="adjust-mtu-value"></a>MTU-waarde aanpassen
<!-- linked from troubleshoot-nas article -->

U kunt de maximale grootte van de verzend eenheid voor de cache selecteren met behulp van de vervolg keuzelijst met de naam **MTU-grootte**.

De standaard waarde is 1500 bytes, maar u kunt deze wijzigen in 1400.

> [!NOTE]
> Als u de MTU-grootte van de cache verlaagt, moet u ervoor zorgen dat de clients en opslag systemen die met de cache communiceren dezelfde MTU-instelling of een lagere waarde hebben.

Het verlagen van de cache-MTU-waarde kan u helpen de beperkingen voor pakket grootte te omzeilen in de rest van het netwerk van de cache. Sommige Vpn's kunnen bijvoorbeeld geen 1500-byte-pakketten van de volledige grootte verzenden. Het verminderen van de grootte van pakketten die via de VPN-verbinding worden verzonden, kan dat probleem mogelijk niet oplossen. Houd er echter rekening mee dat een lagere MTU-instelling voor de cache betekent dat elk ander onderdeel dat communiceert met de cache-inclusief-clients en-opslag systemen, ook een lagere MTU-instelling moet hebben om communicatie problemen te voor komen.

Als u de MTU-instellingen op andere systeem onderdelen niet wilt wijzigen, moet u de MTU-instelling van de cache niet verlagen. Er zijn andere oplossingen om beperkingen voor de grootte van VPN-pakketten te omzeilen. Lees de beperkingen voor de [grootte van VPN-pakketten aanpassen](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) in het artikel over het oplossen van problemen met NAS voor meer informatie over het diagnosticeren en oplossen van dit probleem.

Lees meer over MTU-instellingen in virtuele netwerken van Azure door het [afstemmen van TCP/IP-prestaties voor Azure-vm's](../virtual-network/virtual-network-tcpip-performance-tuning.md)te lezen.

## <a name="configure-root-squash"></a>Basis-Squash configureren
<!-- linked from troubleshoot and from access policies -->

De instelling **basis Squash inschakelen** bepaalt hoe Azure HPC cache aanvragen van de hoofd gebruiker op client computers verwerkt.

Wanneer root Squash is ingeschakeld, worden hoofd gebruikers van een client automatisch toegewezen aan de gebruiker ' niemand ' wanneer ze aanvragen verzenden via de Azure HPC-cache. Ook wordt voor komen dat client aanvragen gebruikmaken van Set-UID permissions-machtigingen.

Als hoofdmap Squash is uitgeschakeld, wordt een aanvraag van de client root user (UID 0) door gegeven aan een back-end-NFS-opslag systeem als root. Deze configuratie kan ongepaste bestands toegang toestaan.

Met het instellen van basis-squash in de cache kunt u de vereiste ``no_root_squash`` instelling compenseren op NAS-systemen die worden gebruikt als opslag doelen. (Lees meer over de [vereisten voor NFS-opslag doel](hpc-cache-prerequisites.md#nfs-storage-requirements).) Het kan ook de beveiliging verbeteren wanneer deze wordt gebruikt met Azure Blob Storage-doelen.

De standaard instelling is **Ja**. (Caches die vóór april 2020 zijn gemaakt, kunnen de standaard instelling **Nee** hebben.)

> [!TIP]
> U kunt ook root Squash instellen voor specifieke opslag exports door de beleids regels voor [client toegang](access-policies.md#root-squash)aan te passen.

## <a name="view-snapshots-for-blob-storage-targets"></a>Moment opnamen voor Blob-opslag doelen weer geven

Met Azure HPC cache worden opslag momentopnamen voor Azure Blob-opslag doelen automatisch opgeslagen. Moment opnamen bieden een snel referentie punt voor de inhoud van de back-end-opslag container.

Moment opnamen zijn geen vervanging voor back-ups van gegevens en ze bevatten geen informatie over de status van gegevens in de cache.

> [!NOTE]
> Deze functie voor moment opnamen wijkt af van de momentopname functie die is opgenomen in de opslag software van NetApp of Isilon. Deze moment opname-implementaties nemen wijzigingen van de cache over naar het back-end-opslag systeem voordat de moment opname wordt gemaakt.
>
> Voor efficiëntie worden de wijzigingen in de Azure HPC-cache momentopname niet eerst leeg gemaakt en worden alleen gegevens vastgelegd die naar de BLOB-container zijn geschreven. Deze moment opname vertegenwoordigt niet de status van de gegevens in de cache, waardoor er mogelijk geen recente wijzigingen zijn.

Deze functie is alleen beschikbaar voor Azure Blob-opslag doelen en de configuratie kan niet worden gewijzigd.

Moment opnamen worden elke acht uur genomen, op UTC 0:00, 08:00 en 16:00.

Met de Azure HPC-cache worden dagelijkse, wekelijkse en maandelijkse moment opnamen opgeslagen, totdat deze worden vervangen door nieuwe sjablonen. De limieten zijn:

* Maxi maal 20 dagelijkse moment opnamen
* Maxi maal 8 wekelijkse moment opnamen
* Maxi maal 3 maandelijkse moment opnamen

Open de moment opnamen van de `.snapshot` map in uw Blob Storage-doel naam ruimte.
