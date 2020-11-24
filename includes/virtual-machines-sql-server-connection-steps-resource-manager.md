---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 51dc04fbef8d09878f33d7fda6f15039d3afba3e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553623"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>Een DNS-label configureren voor het openbare IP-adres

Als u vanaf internet verbinding wilt maken met de SQL Server Database Engine, kunt u overwegen om een DNS-label te maken voor uw openbare IP-adres. U kunt ook verbinding maken met behulp van een IP-adres, maar het DNS-label resulteert in een A-record die eenvoudiger te identificeren is en die het onderliggende openbare IP-adres maskeert.

> [!NOTE]
> DNS-labels zijn niet vereist als u alleen verbinding wilt maken met het SQL Server-exemplaar binnen hetzelfde virtuele netwerk of alleen lokaal verbinding wilt maken.

Als u een DNS-label wilt maken, selecteert u eerst **Virtuele machines** in de portal. Selecteer uw SQL Server VM om de eigenschappen op te halen.

1. Selecteer uw **openbare IP-adres** in het overzicht van de virtuele machine.

    ![openbaar ip adres](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. Vouw in de eigenschappen voor uw openbare IP-adres **Configuratie** open.

1. Voer een naam voor het DNS-label in. Deze naam is een A-Record die kan worden gebruikt om verbinding maken met uw SQL Server VM met uw naam in plaats van rechtstreeks met het IP-adres.

1. Klik op de knop **Opslaan**.

    ![dns label](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Verbinding maken met de Database-engine vanaf een andere computer

1. Open SQL Server Management Studio (SSMS) op een computer die is verbonden met internet. Als u niet beschikt over SQL Server Management Studio, kunt u dit programma [hier](/sql/ssms/download-sql-server-management-studio-ssms) downloaden.

1. Bewerk in het dialoogvenster **Verbinding maken met server** of **Verbinding maken met Database-engine** de waarde voor **Servernaam**. Voer het IP-adres of de volledige DNS-naam van de virtuele machine in (zoals bepaald in de vorige taak). U kunt ook een komma toevoegen en de TCP-poort van SQL Server opgeven. Bijvoorbeeld `mysqlvmlabel.eastus.cloudapp.azure.com,1433`.

1. Kies in het vak **Verificatie** **SQL Server-verificatie**.

1. Typ in het vak **Aanmelden** een geldige SQL-aanmeldingsnaam.

1. Typ in het vak **Wachtwoord** het wachtwoord van de aanmelding.

1. Klik op **Verbinding maken**.

    ![ssms verbinden](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)