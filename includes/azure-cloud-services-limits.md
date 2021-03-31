---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85838848"
---
| Resource | Limiet |
| --- | --- |
| [Web- of werkrollen per implementatie](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Exemplaar van invoereindpunten](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) per implementatie |25 |
| [Invoereindpunten](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) per implementatie |25 |
| [Interne invoereindpunten](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) per implementatie |25 |
| [Gehoste servicecertificaten](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) per implementatie |199 |

<sup>1</sup>Elke Azure-cloudservice met web- of werkrollen kan twee implementaties hebben: één voor productie en één voor fasering. Deze limiet verwijst naar het aantal afzonderlijke rollen, dat wil zeggen,de configuratie. Deze limiet verwijst niet naar het aantal exemplaren per rol, dat wil zeggen, schalen.

