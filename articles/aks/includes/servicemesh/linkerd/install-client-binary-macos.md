---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77593710"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Down load en installeer het binaire bestand voor de Linkerd Linkerd-client

In een op bash gebaseerde shell op MacOS kunt u `curl` de Linkerd-release als volgt downloaden:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

De `linkerd` binaire client wordt uitgevoerd op uw client computer en maakt het mogelijk om te communiceren met het Linkerd-service-net. Gebruik de volgende opdrachten om het binaire bestand van de Linkerd-client te installeren `linkerd` in een op bash gebaseerde shell op MacOS. Met deze opdrachten `linkerd` wordt de binaire client gekopieerd naar de standaard gebruikers programma locatie in uw `PATH` .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Als u de opdracht regel voor het binaire bestand van de Linkerd-client wilt laten volt ooien, moet u `linkerd` deze als volgt instellen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
