---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 5dc9686e4a9994a085cc9f4a4631e66b05d7949d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666699"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Down load en installeer het binaire bestand voor de Istio istioctl-client

In een op bash gebaseerde shell op macOS kunt u `curl` de Istio-release downloaden en vervolgens `tar` als volgt ophalen:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

De `istioctl` binaire client wordt uitgevoerd op uw client computer en u kunt Istio installeren en beheren in uw AKS-cluster. Gebruik de volgende opdrachten om het binaire bestand van de Istio-client te installeren `istioctl` in een op bash gebaseerde shell op macOS. Met deze opdrachten `istioctl` wordt de binaire client gekopieerd naar de standaard gebruikers programma locatie in uw `PATH` .

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Als u de opdracht regel voor het binaire bestand van de Istio-client wilt laten volt ooien, moet u `istioctl` deze als volgt instellen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```