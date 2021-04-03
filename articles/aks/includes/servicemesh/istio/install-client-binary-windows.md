---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: a02c17013a205ccc0da85536b491d467ef72fa48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666698"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Down load en installeer het binaire bestand voor de Istio istioctl-client

Gebruik in een Power shell-shell in Windows `Invoke-WebRequest` om de Istio-release te downloaden en vervolgens als volgt op te halen `Expand-Archive` :

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.7.3"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-win.zip" -OutFile "istioctl-$ISTIO_VERSION.zip"
Expand-Archive -Path "istioctl-$ISTIO_VERSION.zip" -DestinationPath .
```

De `istioctl` binaire client wordt uitgevoerd op uw client computer en u kunt Istio installeren en beheren in uw AKS-cluster. Gebruik de volgende opdrachten om de binaire Istio `istioctl` -client te installeren in een Power shell-shell op Windows. Met deze opdrachten kopieert `istioctl` u het binaire bestand van de client naar een Istio-map en maakt u deze zowel onmiddellijk beschikbaar (in de huidige shell) als permanent (tussen het opnieuw opstarten van de shell) via uw `PATH` . U hebt geen verhoogde beheerders bevoegdheden nodig om deze opdrachten uit te voeren en u hoeft de shell niet opnieuw op te starten.

```powershell
# Copy istioctl.exe to C:\Istio
New-Item -ItemType Directory -Force -Path "C:\Istio"
Move-Item -Path .\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```