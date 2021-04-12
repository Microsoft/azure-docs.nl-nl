---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080227"
---
## <a name="download-and-install-the-osm-client-binary"></a>De binaire OSM-client downloaden en installeren

In een bash shell op Linux of Windows- [subsysteem voor Linux][install-wsl]gebruikt `curl` u om de OSM-release te downloaden en vervolgens `tar` als volgt op te halen:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

De `osm` binaire client wordt uitgevoerd op uw client computer en u kunt OSM beheren in uw AKS-cluster. Gebruik de volgende opdrachten om de OSM- `osm` client binaire bestanden te installeren in een op bash gebaseerde shell op Linux of [Windows-subsysteem voor Linux][install-wsl]. Met deze opdrachten `osm` wordt de binaire client gekopieerd naar de standaard gebruikers programma locatie in uw `PATH` .

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Met de volgende opdracht kunt u controleren of de `osm` client bibliotheek correct is toegevoegd aan uw pad en het versie nummer.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
