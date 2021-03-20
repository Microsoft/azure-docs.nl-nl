---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 71460af42b4da97a578ae5a3e23a714577e71867
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86218142"
---
| Poort nummer| In of uit | Poort bereik| Vereist|   Notities |
|--------|-----|-----|-----------|----------|
| TCP 80 (HTTP)|Uit|WAN |Nee|De uitgaande poort wordt gebruikt voor toegang tot internet om updates op te halen. <br>De uitgaande webproxy kan door de gebruiker worden geconfigureerd. |
| TCP 443 (HTTPS)|Uit|WAN|Ja|De uitgaande poort wordt gebruikt voor toegang tot gegevens in de Cloud.<br>De uitgaande webproxy kan door de gebruiker worden geconfigureerd.|
| UDP 123 (NTP)|Uit|WAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u een NTP-server op Internet gebruikt.  |   
| UDP 53 (DNS)|Uit|WAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u een DNS-server op Internet gebruikt.<br>U kunt het beste een lokale DNS-server gebruiken. |
| TCP 5985 (WinRM)|Uit/in|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is vereist om verbinding te maken met het apparaat via externe Power shell via HTTP.  |
| UDP 67 (DHCP)|Uit|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u een lokale DHCP-server gebruikt.  |
| TCP 80 (HTTP)|Uit/in|LAN|Ja|Deze poort is de binnenkomende poort voor lokale gebruikers interface op het apparaat voor lokaal beheer. <br>Toegang tot de lokale gebruikers interface via HTTP wordt automatisch omgeleid naar HTTPS.  |
| TCP 443 (HTTPS)|Uit/in|LAN|Ja|Deze poort is de binnenkomende poort voor lokale gebruikers interface op het apparaat voor lokaal beheer. |
| TCP 445 (SMB)|In|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u verbinding maakt via SMB. |
| TCP 2049 (NFS)|In|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u verbinding maakt via NFS. |
