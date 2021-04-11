---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: 1c7dbe9bb6cf28f4dee2c4e9aa036410fadbbddf
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080235"
---
## <a name="download-and-install-the-osm-client-binary"></a>De binaire OSM-client downloaden en installeren

In een bash shell, gebruikt `curl` u om de OSM-release te downloaden en vervolgens als volgt op te halen `tar` :

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

De `osm` binaire client wordt uitgevoerd op uw client computer en u kunt OSM beheren in uw AKS-cluster. Gebruik de volgende opdrachten om het binaire bestand van de OSM-client te installeren `osm` in een bash-shell. Met deze opdrachten `osm` wordt de binaire client gekopieerd naar de standaard gebruikers programma locatie in uw `PATH` .

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Met de volgende opdracht kunt u controleren of de `osm` client bibliotheek correct is toegevoegd aan uw pad en het versie nummer.

```
osm version
```
