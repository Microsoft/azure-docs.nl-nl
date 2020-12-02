---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999732"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>De sessie voor het ruimtelijk anker in de cloud configureren

De configuratie van de sessie voor het ruimtelijk anker in de cloud wordt later behandeld. Op de eerste regel wordt de sensorprovider voor de sessie ingesteld. Voortaan wordt elk anker dat tijdens de sessie wordt gemaakt, aan een reeks sensoraflezingen gekoppeld. Vervolgens worden voorwaarden voor het lokaliseren van nabije apparaten geïnstantieerd en vervolgens geïnitialiseerd zodat ze aan de toepassingsvereisten voldoen. Ten slotte krijgt de sessie de opdracht om sensorgegevens te gebruiken bij het lokaliseren van ankers door een watcher te maken op basis van de voorwaarden voor nabije apparaten.