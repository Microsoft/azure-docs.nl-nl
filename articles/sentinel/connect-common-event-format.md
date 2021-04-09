---
title: CEF-gegevens verbinden met Azure Sentinel preview | Microsoft Docs
description: Verbind een externe oplossing die CEF-berichten (common Event Format) naar Azure Sentinel verzendt met behulp van een Linux-machine als een logboek doorstuur server.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 54fd6c0c085c0055f3114fde606f8f7d2f2e055e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772056"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Verbind uw externe oplossing met de algemene gebeurtenis indeling

Wanneer u verbinding maakt met een externe oplossing die CEF berichten verzendt, zijn er drie stappen om verbinding te maken met Azure Sentinel:

STAP 1: [verbinding maken met CEF met behulp van een syslog/CEF-doorstuur server](connect-cef-agent.md) stap 2: [oplossingen uitvoeren-specifieke stappen](connect-cef-solution-config.md) stap 3: de [verbinding controleren](connect-cef-verify.md)

In dit artikel wordt beschreven hoe de verbinding werkt, een lijst met vereisten en de stappen voor het implementeren van een mechanisme voor beveiligings oplossingen om algemene Event Format-berichten (CEF) boven op syslog te verzenden. 

> [!NOTE] 
> Gegevens worden opgeslagen op de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

Als u deze verbinding wilt maken, moet u een syslog-doorstuur server implementeren ter ondersteuning van de communicatie tussen het apparaat en de onderverklikker van Azure.  De server bestaat uit een toegewezen Linux-machine (VM of on-premises) met de Log Analytics-agent voor Linux geïnstalleerd. 

In het volgende diagram wordt de installatie beschreven in het geval van een virtuele Linux-machine in Azure:

 ![CEF in azure](./media/connect-cef/cef-syslog-azure.png)

Deze installatie bestaat ook als u een virtuele machine in een andere Cloud of op een on-premises computer gebruikt: 

 ![CEF on-premises](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Beveiligingsoverwegingen

Zorg ervoor dat u de beveiliging van de computer configureert op basis van het beveiligings beleid van uw organisatie. U kunt bijvoorbeeld uw netwerk zodanig configureren dat het wordt uitgelijnd met het beveiligings beleid van uw bedrijfs netwerk en de poorten en protocollen in de daemon wijzigen om af te stemmen op uw vereisten. U kunt de volgende instructies gebruiken om de beveiligings configuratie van uw computer te verbeteren:  [Beveilig de virtuele machine in azure](../virtual-machines/security-policy.md), [Aanbevolen procedures voor netwerk beveiliging](../security/fundamentals/network-best-practices.md).

Als u TLS-communicatie tussen de syslog-bron en de syslog-doorstuur server wilt gebruiken, moet u de syslog-daemon (rsyslog of syslog-ng) configureren om te communiceren in TLS: [syslog-verkeer met TLS-rsyslog versleutelen](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [logboek berichten versleutelen met TLS-syslog-aardgas](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).
 
## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat de Linux-machine die u als een logboek-doorstuur server gebruikt, een van de volgende besturings systemen wordt uitgevoerd:

- 64-bits
  - CentOS 7 en 8, inclusief secundaire versies (niet 6)
  - Amazon Linux 2017,09
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 en 8, inclusief secundaire versies (niet 6)
  - Debian GNU/Linux 8 en 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS en 18,04 LTS
  - SUSE Linux Enterprise Server 12, 15

- 32-bits
  - CentOS 7 en 8, inclusief secundaire versies (niet 6)
  - Oracle Linux 7
  - Red Hat Enterprise Linux (RHEL) Server 7 en 8, inclusief secundaire versies (niet 6)
  - Debian GNU/Linux 8 en 9
  - Ubuntu Linux 14,04 LTS en 16,04 LTS
 
- Daemon-versies
  - Syslog-ng: 2,1-3.22.1
  - Rsyslog: V8
  
- Ondersteuning voor syslog-Rfc's
  - Syslog RFC 3164
  - Syslog RFC 5424
 
Zorg ervoor dat uw computer ook aan de volgende vereisten voldoet: 

- Capaciteit
  - Uw computer moet mini maal **4 CPU-kernen en 8 GB RAM-geheugen** hebben.

    > [!NOTE]
    > - Een single-doorstuur machine voor logboek registratie met behulp van de **rsyslog** -daemon heeft een ondersteunde capaciteit van **Maxi maal 8500 gebeurtenissen per seconde (EPS)** verzameld.

- Machtigingen
  - U moet over verhoogde machtigingen (sudo) beschikken op uw computer. 

- Softwarevereisten
  - Zorg ervoor dat python 2,7 of 3 op uw computer wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe Azure Sentinel CEF-logboeken verzamelt van beveiligings oplossingen en-apparaten. Raadpleeg de volgende artikelen voor meer informatie over het verbinden van uw oplossing met Azure Sentinel:

- STAP 1: [verbinding maken met CEF door een syslog/CEF-doorstuur server te implementeren](connect-cef-agent.md)
- STAP 2: [Voer oplossingen uit die specifiek zijn](connect-cef-solution-config.md) voor de oplossing
- STAP 3: de [connectiviteit controleren](connect-cef-verify.md)

Raadpleeg de volgende artikelen voor meer informatie over wat u moet doen met de gegevens die u hebt verzameld in azure Sentinel:

- Meer informatie over de [veld toewijzing CEF en CommonSecurityLog](cef-name-mapping.md).
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).