---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/06/2020
ms.author: alkohli
ms.openlocfilehash: b9ff5968b4bb406f1a96780985b5c6fe64ca976c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89505908"
---
| Poort nummer| In of uit | Poort bereik| Vereist| Notities |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|In|LAN|Ja|Deze poort wordt gebruikt om verbinding te maken met Data Box Blob Storage REST-Api's via HTTP. Als er geen verbinding wordt gemaakt met REST-Api's, wordt deze automatisch omgeleid naar de lokale web-UI via 8443. |
| TCP 443 (HTTPS)|In|LAN|Ja|Deze poort wordt gebruikt om via HTTPS verbinding te maken met Data Box Blob Storage REST Api's. Als er geen verbinding wordt gemaakt met REST-Api's, wordt deze automatisch omgeleid naar de lokale web-UI via 8443. |
| TCP 8443 (HTTPS-ALT)|In|LAN|Ja|Dit is een alternatieve poort voor HTTPS en wordt gebruikt om verbinding te maken met een lokale web-UI voor Apparaatbeheer. |
| TCP 445 (SMB)|Uit/in|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u verbinding maakt via SMB. |
| TCP 2049 (NFS)|Uit/in|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u verbinding maakt via NFS. |
| TCP 111 (NFS)|Uit/in|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort wordt gebruikt voor rpcbind/Port Mapping en is alleen vereist als u verbinding maakt via NFS.  |
