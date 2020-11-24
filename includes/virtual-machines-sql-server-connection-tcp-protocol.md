---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: f7af6962c3343cd9d3e35e96d88650aa6a42c6b3
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555612"
---
1. Zoek naar **Configuration Manager** terwijl u via Extern bureaublad bent verbonden met de virtuele machine:

    ![SSCM openen](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. Breid in het consolevenster van SQL Server Configuration Manager het gedeelte **SQL Server-netwerkconfiguratie** uit.

1. Klik in het console venster op **protocollen voor MSSQLSERVER** (de naam van het standaard exemplaar). Klik in het detail venster met de rechter muisknop op **TCP** en klik op inschakelen als deze **optie** nog niet is ingeschakeld.

    ![TCP inschakelen](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. Klik in het consolevenster op **SQL Server-services**. Klik in het detail venster met de rechter muisknop op **SQL Server (*exemplaar naam*)** (het standaard exemplaar is **SQL Server (MSSQLSERVER)**) en klik vervolgens op **opnieuw opstarten** om het exemplaar van SQL Server te stoppen en opnieuw op te starten.

    ![Database-engine opnieuw starten](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Sluit SQL Server Configuration Manager.

Zie [Enable or Disable a Server Network Protocol](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) (Een servernetwerkprotocol in- of uitschakelen) voor meer informatie over het inschakelen van protocollen voor de database-engine van SQL Server.