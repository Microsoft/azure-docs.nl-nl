---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68361542"
---
### <a name="bastion-tier"></a>Bastion-laag

De bastion-host is een optioneel onderdeel dat u kunt gebruiken als een Jump server voor toegang tot de toepassing en de data base-exemplaren. Er kan een openbaar IP-adres aan de Bastion worden toegewezen, hoewel de aanbeveling een ExpressRoute-verbinding of site-to-site VPN met uw on-premises netwerk moet instellen voor beveiligde toegang. Daarnaast moet alleen SSH (poort 22, Linux) of RDP (poort 3389, Windows Server) worden geopend voor inkomend verkeer. Voor hoge Beschik baarheid implementeert u een bastion-host in twee beschikbaarheids zones of in één beschikbaarheidsset.

U kunt ook het door sturen van SSH-agents inschakelen op uw Vm's, waarmee u toegang kunt krijgen tot andere virtuele machines in het virtuele netwerk door de referenties van uw bastion-host door te sturen. U kunt ook SSH-tunneling gebruiken om toegang te krijgen tot andere exemplaren.

Hier volgt een voor beeld van het door sturen van agents:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Met deze opdracht maakt u verbinding met de Bastion en wordt deze vervolgens onmiddellijk `ssh` opnieuw uitgevoerd, zodat u een Terminal op het doel exemplaar krijgt. U moet mogelijk een andere gebruiker dan de hoofdmap opgeven voor het doel exemplaar als uw cluster anders is geconfigureerd. `-A`Met het argument wordt de agent verbinding doorgestuurd zodat uw persoonlijke sleutel op uw lokale computer automatisch wordt gebruikt. Het door sturen van agents is een keten, dus de tweede `ssh` opdracht omvat ook `-A` dat alle volgende ssh-verbindingen die vanuit het doel exemplaar worden gestart ook gebruikmaken van uw lokale persoonlijke sleutel.