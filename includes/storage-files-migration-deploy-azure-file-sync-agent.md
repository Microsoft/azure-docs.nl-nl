---
title: De Azure File Sync-agent implementeren
description: Implementeer de Azure File Sync-agent. Een gemeen schappelijk tekst blok, gedeeld via migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: f038392f03b94aa2c2450531c9da4a11d9900295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93043128"
---
In deze sectie installeert u de Azure File Sync-agent op uw Windows Server-exemplaar.

In de [implementatie handleiding](../articles/storage/files/storage-sync-files-deployment-guide.md) ziet u dat u de **Verbeterde beveiliging van Internet Explorer** moet uitschakelen. Deze beveiligings maatregel is niet van toepassing op Azure File Sync. Als u deze functie uitschakelt, kunt u zich zonder problemen verifiëren bij Azure.

Open Power shell en installeer de vereiste Power shell-modules met behulp van de volgende opdrachten. Zorg ervoor dat u de volledige module en de NuGet-provider installeert wanneer u hierom wordt gevraagd.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Als er problemen zijn met het Internet vanaf uw server, is dit nu de tijd om deze op te lossen. Azure File Sync gebruikt een beschik bare netwerk verbinding met het internet. Het vereisen van een proxy server voor het bereiken van Internet wordt ook ondersteund. U kunt een proxy voor alle computers nu configureren of een proxy opgeven die alleen door Azure File Sync zal worden gebruikt tijdens de installatie van de agent.

Als u een proxy configureert, moet u de firewalls voor deze server openen. deze benadering is mogelijk acceptabel voor u. Aan het einde van de server installatie, nadat u de server registratie hebt voltooid, toont een netwerk connectiviteits rapport u de exacte eindpunt-Url's in azure waarmee Azure File Sync moet communiceren met voor de regio die u hebt geselecteerd. Het rapport geeft ook de reden aan waarom communicatie nodig is. U kunt het rapport gebruiken om de firewalls rond deze server op specifieke Url's te vergren delen.

U kunt ook een meer conservatieve benadering volgen waarbij u de firewalls Wide niet opent, maar de server wordt beperkt tot communicatie met een DNS-naam ruimte op een hoger niveau. Zie [Azure file sync proxy-en Firewall instellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)voor meer informatie. Volg uw aanbevolen procedures voor netwerk gebruik.

Aan het einde van de wizard Server-installatie wordt de wizard Server registratie geopend. Registreer de-server bij de Azure-resource van de opslag synchronisatie service vanaf een eerdere locatie.

Deze stappen worden uitgebreid beschreven in de implementatie handleiding, met inbegrip van de Power shell-modules die u eerst moet installeren: [Azure file sync agent-installatie](../articles/storage/files/storage-sync-files-deployment-guide.md).

Gebruik de nieuwste agent. U kunt deze downloaden van het micro soft Download centrum: [Azure file sync-agent](https://aka.ms/AFS/agent "Downloaden van Azure File Sync-agent").

Na een geslaagde installatie en Server registratie kunt u controleren of u deze stap hebt voltooid. Ga naar de bron van de opslag synchronisatie service in de Azure Portal. Ga in het menu links naar **geregistreerde servers**. U ziet dat uw server daar wordt vermeld.
