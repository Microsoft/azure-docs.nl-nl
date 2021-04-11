---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 8ac70027f7483fbf0131c31a5ba3631ed1d4ff9a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080230"
---
## <a name="download-and-install-the-osm-client-binary"></a>De binaire OSM-client downloaden en installeren

Gebruik in een Power shell-shell in Windows `Invoke-WebRequest` om de Istio-release te downloaden en vervolgens als volgt op te halen `Expand-Archive` :

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.8.2"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

De `osm` binaire client wordt uitgevoerd op uw client computer en u kunt de OSM-controller beheren in uw AKS-cluster. Gebruik de volgende opdrachten om de binaire OSM `osm` -client te installeren in een Power shell-shell op Windows. Met deze opdrachten kopieert `osm` u het binaire bestand van de client naar een OSM-map en maakt u deze zowel onmiddellijk beschikbaar (in de huidige shell) als permanent (tussen het opnieuw opstarten van de shell) via uw `PATH` . U hebt geen verhoogde beheerders bevoegdheden nodig om deze opdrachten uit te voeren en u hoeft de shell niet opnieuw op te starten.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
