---
title: Systeem vereisten voor Microsoft Azure Data Box | Microsoft Docs
description: Meer informatie over belang rijke systeem vereisten voor uw Azure Data Box en voor clients die verbinding maken met de Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 03/25/2021
ms.author: alkohli
ms.openlocfilehash: 7f999dbf7a4e0262e36181a98560931d32d3296b
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612497"
---
# <a name="azure-data-box-system-requirements"></a>Systeem vereisten voor Azure Data Box

In dit artikel worden de belangrijkste systeem vereisten beschreven voor uw Microsoft Azure Data Box en voor clients die verbinding maken met de Data Box. We raden u aan de informatie zorgvuldig te bekijken voordat u uw Data Box implementeert en raadpleegt u deze wanneer u tijdens de implementatie en de bewerking moet worden uitgevoerd.

De systeem vereisten zijn onder andere:

* **Software vereisten:** Voor hosts die verbinding maken met de Data Box, worden ondersteunde besturings systemen, protocollen voor bestands overdracht, opslag accounts, opslag typen en browsers voor de lokale webgebruikersinterface beschreven.
* **Netwerk vereisten:** Voor de Data Box worden de vereisten voor netwerk verbindingen en poorten beschreven voor de beste werking van de Data Box.


## <a name="software-requirements"></a>Softwarevereisten

De software vereisten zijn onder andere ondersteunde besturings systemen, protocollen voor bestands overdracht, opslag accounts, opslag typen en browsers voor de lokale webgebruikersinterface.

### <a name="supported-operating-systems-for-clients"></a>Ondersteunde besturingssystemen voor clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Ondersteunde protocollen voor bestands overdracht voor clients

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Verbinding met Data Box shares wordt niet ondersteund via REST voor export orders.

### <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Ondersteunde webbrowsers

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Netwerkvereisten

Uw datacenter moet een netwerk met hoge snelheid hebben. Wij raden u ten zeerste aan om ten minste 1 10-GbE-verbinding te hebben. Als er geen 10 GbE-verbinding beschikbaar is, kunt u een 1-GbE-gegevens koppeling gebruiken om gegevens te kopiëren, maar de Kopieer snelheden worden beïnvloed.

### <a name="port-requirements"></a>Poortvereisten

De volgende tabel geeft een lijst van de poorten die in uw firewall moeten worden geopend om SMB-of NFS-verkeer toe te staan. In deze tabel verwijst *(* *Inkomend*) naar de richting waarin de inkomende client toegang tot uw apparaat vraagt. *Uit* (of *uitgaand*) verwijst naar de richting waarin uw data Box apparaat gegevens extern verzendt, buiten de implementatie. Zo kunnen gegevens bijvoorbeeld uitgaand zijn voor Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Data Box implementeren](data-box-deploy-ordered.md)
