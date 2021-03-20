---
title: Systeem vereisten voor Microsoft Azure Data Box Heavy | Microsoft Docs
description: Meer informatie over de software-en netwerk vereisten voor uw Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84707749"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Systeem vereisten voor Azure Data Box Heavy

In dit artikel worden de belangrijkste systeem vereisten beschreven voor uw Azure Data Box Heavy apparaat en voor de clients die verbinding maken met het apparaat. We raden u aan de informatie zorgvuldig te bekijken voordat u uw Data Box Heavy implementeert en vervolgens naar de gewenste gegevens te verwijzen tijdens de implementatie en de volgende bewerking.

De systeem vereisten zijn onder andere:

* **Software vereisten voor hosts die verbinding maken met data Box Heavy** : beschrijft de ondersteunde platforms, browsers voor de lokale webgebruikersinterface, SMB-clients en eventuele aanvullende vereisten voor hosts die verbinding kunnen maken met de data box.
* **Netwerk vereisten voor de data Box Heavy** : bevat informatie over de netwerk vereisten voor de optimale werking van het data Box Heavy apparaat.

## <a name="software-requirements"></a>Softwarevereisten

De software vereisten omvatten de informatie over de ondersteunde besturings systemen, ondersteunde browsers voor de lokale web-UI en SMB-clients.

### <a name="supported-operating-systems-for-clients"></a>Ondersteunde besturingssystemen voor clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Ondersteunde bestands systemen voor Linux-clients

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Ondersteunde webbrowsers

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Netwerkvereisten

Uw datacenter moet een netwerk met hoge snelheid hebben. Voor de snelste Kopieer snelheden kunnen 2 40 GbE-verbindingen parallel worden gebruikt (één per knoop punt). Als u 40-GbE niet hebt, kunt u het beste ten minste 2 10 GbE-verbindingen (één per knoop punt) hebben.

### <a name="port-requirements"></a>Poortvereisten

De volgende tabel geeft een lijst van de poorten die in uw firewall moeten worden geopend om SMB-of NFS-verkeer toe te staan. In deze tabel verwijst *naar* of *binnenkomend* naar de richting waarin de inkomende client toegang tot uw apparaat vraagt. *Out* of *uitgaand* verwijst naar de richting waarin uw data Box Heavy apparaat gegevens extern verzendt, naast de implementatie: bijvoorbeeld uitgaand naar Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Data Box implementeren](data-box-deploy-ordered.md)
