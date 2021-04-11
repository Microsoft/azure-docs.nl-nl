---
title: Aan de slag met TmaxSoft open frame op Azure Virtual Machines
description: Host uw IBM z/OS mainframe-workloads met behulp van de open-frame omgeving van TmaxSoft op Azure Virtual Machines (Vm's).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 346c331596f984cbb068c03110997c2478499488
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950668"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Aan de slag met TmaxSoft open frame op Azure

Neem uw bestaande mainframe-assets mee en verplaats ze naar Microsoft Azure met openTmaxSofte OpenFrame. Deze populaire oplossing voor opnieuw hosten maakt een emulatie omgeving in azure, zodat u snel toepassingen kunt migreren. Opnieuw Format teren is niet vereist.

## <a name="openframe-rehosting-environment"></a>Opnieuw hosting omgeving van OpenFrame

Stel een open-frame omgeving in op Azure voor ontwikkelings-, demonstratie-, test-of productie werkbelastingen. Zoals in de volgende afbeelding wordt weer gegeven, bevat OpenFrame meerdere onderdelen die de mainframe-emulatie omgeving maken in Azure. OpenFrame onlineservices bijvoorbeeld de mainframe-middleware vervangen zoals het IBM-klant Information Control System (CICS). OpenFrame batch, met het TJES-onderdeel, vervangt het subsysteem voor taak invoer van IBM mainframe (JES). 

![Proces voor opnieuw hosten van OpenFrame](media/openframe-01.png)

> [!NOTE]
> Als u de open frame-omgeving in azure wilt uitvoeren, moet u een geldige product licentie of een licentie voor een proef versie van TmaxSoft hebben.

## <a name="openframe-components"></a>OpenFrame-onderdelen

De volgende onderdelen maken deel uit van de open frame-omgeving in Azure:

- **Migratie hulpprogramma's** , waaronder OFMiner, een oplossing voor het analyseren van de mainframe-assets en vervolgens naar Azure worden gemigreerd.
- **Compilers**, waaronder OFCOBOL, een compiler die de COBOL-Program ma's van het mainframe interpreteert; OFPLI, die de PL/I-Program ma's van het mainframe interpreteert; en OFASM: een compiler waarmee de assembler-program ma's van het mainframe worden geïnterpreteerd.
- **Front-end** onderdelen, waaronder Java Enter prise User Solution (JEUS), een webtoepassingsserver die is gecertificeerd voor Java Enter prise Edition 6. OFGW en het onderdeel OpenFrame gateway dat een 3270-listener biedt.
- **Toepassings** omgeving. OpenFrame Base is de middleware die het hele systeem beheert. De OpenFrame-server type C (OSC) vervangt de middleware van het mainframe en IBM CICS.
- **Relationele data base**, zoals Tibero (weer gegeven), Oracle Database, Microsoft SQL Server, IBM Db2 of MySQL. Het Open Database Connectivity-protocol (OpenFrame-toepassingen) gebruiken om te communiceren met de data base.
- **Beveiliging** via TACF, een service module waarmee gebruikers toegang tot systemen en bronnen wordt beheerd. 
- **OFManager** is een oplossing voor het bewerkings-en beheer functies van OpenFrame in de webomgeving.

![Architectuur van OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Volgende stappen

- [TmaxSoft open frame installeren op Azure](./install-openframe-azure.md)
