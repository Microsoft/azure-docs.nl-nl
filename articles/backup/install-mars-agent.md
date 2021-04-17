---
title: De MARS Microsoft Azure agent (Recovery Services) installeren
description: Meer informatie over het installeren van de MARS Microsoft Azure agent (Recovery Services) om een back-up te maken van Windows-machines.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 3ea48aaa6aad4a51463c4c028ead22f31163f810
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519218"
---
# <a name="install-the-azure-backup-mars-agent"></a>De MARS Azure Backup agent installeren

In dit artikel wordt uitgelegd hoe u de MARS Microsoft Azure agent (Recovery Services) installeert. MARS wordt ook wel de Azure Backup agent genoemd.

## <a name="about-the-mars-agent"></a>Over de MARS-agent

Azure Backup marsagent gebruikt om back-up te maken van bestanden, mappen en systeemtoestanden van on-premises machines en Azure-VM's. Deze back-ups worden opgeslagen in een Recovery Services-kluis in Azure. U kunt de agent uitvoeren:

* Rechtstreeks op on-premises Windows-machines. Deze machines kunnen rechtstreeks een back-up maken naar een Recovery Services-kluis in Azure.
* Op Azure-VM's met Windows naast de azure VM-back-upextensie. De agent back-up van specifieke bestanden en mappen op de VM.
* Op een Microsoft Azure Backup Server-exemplaar (MABS) of een System Center Data Protection Manager (DPM)-server. In dit scenario maken machines en workloads een back-up naar MABS of Data Protection Manager. MABS of Data Protection Manager vervolgens de MARS-agent om een back-up te maken naar een kluis in Azure.

De gegevens die beschikbaar zijn voor back-up zijn afhankelijk van waar de agent is geïnstalleerd.

> [!NOTE]
> Over het algemeen maakt u een back-up van een azure-VM met behulp van een Azure Backup-extensie op de VM. Met deze methode maakt u een back-up van de hele VM. Als u een back-up wilt maken van specifieke bestanden en mappen op de VM, installeert en gebruikt u de MARS-agent naast de extensie. Zie Architectuur van een ingebouwde Azure [VM-back-up](backup-architecture.md#architecture-built-in-azure-vm-backup)voor meer informatie.

![Stappen voor back-upproces](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Voordat u begint

* Meer informatie over [hoe Azure Backup MARS-agent gebruikt om back-up te maken van Windows-machines.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
* Meer informatie over de [back-uparchitectuur](backup-architecture.md#architecture-back-up-to-dpmmabs) die de MARS-agent op een secundaire MABS- of Data Protection Manager server.
* Bekijk [wat er wordt ondersteund en waar u een back-up van kunt maken](backup-support-matrix-mars-agent.md) met de MARS-agent.
* Zorg ervoor dat u een Azure-account hebt als u een back-up van een server of client naar Azure wilt maken. Als u geen account hebt, kunt u binnen [een](https://azure.microsoft.com/free/) paar minuten een gratis account maken.
* Controleer de internettoegang op de computers van wie u een back-up wilt maken.
* Zorg ervoor dat de gebruiker die de installatie en configuratie van de MARS-agent moet uitvoeren, lokale beheerdersbevoegdheden heeft op de server die moet worden beveiligd.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Opslagreplicatie wijzigen

Kluizen maken standaard gebruik van [geografisch redundante opslag (GRS).](../storage/common/storage-redundancy.md#geo-redundant-storage)

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.
* U kunt lokaal [redundante opslag (LRS) gebruiken om](../storage/common/storage-redundancy.md#locally-redundant-storage) de kosten voor Azure-opslag te verlagen.

Het opslagreplicatietype wijzigen:

1. Selecteer eigenschappen in de nieuwe kluis **onder** de **sectie** Instellingen.

1. Selecteer op **de pagina** Eigenschappen onder **Back-upconfiguratie** de optie **Bijwerken.**

1. Selecteer het type opslagreplicatie en selecteer **Opslaan.**

    ![Back-upconfiguratie bijwerken](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> U kunt het type opslagreplicatie niet wijzigen nadat de kluis is ingesteld en back-upitems bevat. Als u dit wilt doen, moet u de kluis opnieuw maken.
>

### <a name="verify-internet-access"></a>Internettoegang controleren

Als uw computer beperkte internettoegang heeft, moet u ervoor zorgen dat de firewallinstellingen op de computer of proxy de volgende URL's en IP-adressen toestaan:

* URL's
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP-adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Gebruik Azure ExpressRoute

U kunt een back-up maken van uw gegevens via Azure ExpressRoute met behulp van openbare peering (beschikbaar voor oude circuits) en Microsoft-peering. Back-up via persoonlijke peering wordt niet ondersteund.

Als u openbare peering wilt gebruiken, moet u eerst toegang tot de volgende domeinen en adressen garanderen:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* IP-adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

Als u Microsoft-peering wilt gebruiken, selecteert u de volgende services, regio's en relevante communitywaarden:

* Azure Active Directory (12076:5060)
* Azure-regio, op basis van de locatie van uw Recovery Services-kluis
* Azure Storage, op basis van de locatie van uw Recovery Services-kluis

Zie Routeringsvereisten voor [ExpressRoute voor meer informatie.](../expressroute/expressroute-routing.md)

> [!NOTE]
> Openbare peering is afgeschaft voor nieuwe circuits.

Alle voorgaande URL's en IP-adressen gebruiken het HTTPS-protocol op poort 443.

### <a name="private-endpoints"></a>Privé-eindpunten

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>De MARS-agent downloaden

Download de MARS-agent zodat u deze kunt installeren op de computers van wie u een back-up wilt maken.

Als u de agent al op een machine hebt geïnstalleerd, moet u ervoor zorgen dat u de nieuwste versie van de agent gebruikt. Zoek de nieuwste versie in de portal of ga rechtstreeks naar de [download](https://aka.ms/azurebackup_agent).

1. Selecteer in de kluis onder **Aan de slag** de optie **Back-up**.

    ![Het doel van de back-up openen](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Selecteer **onder Waar wordt uw workload uitgevoerd?** de optie **On-premises.** Selecteer deze optie, zelfs als u de MARS-agent wilt installeren op een Azure-VM.
1. Selecteer **onder Waar wilt u een back-up van maken?** de optie Bestanden en **mappen.** U kunt ook **Systeemtoestand selecteren.** Er zijn veel andere opties beschikbaar, maar deze opties worden alleen ondersteund als u een secundaire back-upserver gebruikt. Selecteer **Infrastructuur voorbereiden.**

    ![Bestanden en mappen configureren](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Download **voor Infrastructuur voorbereiden** onder Recovery **Services-agent installeren** de MARS-agent.

    ![De infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. Selecteer Opslaan in het **downloadmenu.** Standaard wordt het bestand *MARSagentinstaller.exe* opgeslagen in de map Downloads.

1. Selecteer **Al downloaden of de meest recente Recovery Services-agent gebruiken** en download vervolgens de kluisreferenties.

    ![Kluisreferenties downloaden](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selecteer **Opslaan**. Het bestand wordt gedownload naar de map Downloads. U kunt het bestand met kluisreferenties niet openen.

## <a name="install-and-register-the-agent"></a>De agent installeren en registreren

1. Voer het *MARSagentinstaller.exe* uit op de machines van wie u een back-up wilt maken.
1. Selecteer in de installatiewizard MARS-agent **de optie Installatie-instellingen.** Kies daar waar u de agent wilt installeren en kies een locatie voor de cache. Selecteer vervolgens **Volgende**.
   * Azure Backup cache gebruikt om momentopnamen van gegevens op te slaan voordat ze naar Azure worden verzonden.
   * De cachelocatie moet vrije ruimte hebben die gelijk is aan ten minste 5 procent van de grootte van de gegevens waar u een back-up van wilt maken.

    ![Kies installatie-instellingen in de marsagent installatiewizard](./media/backup-configure-vault/mars1.png)

1. Geef **voor Proxyconfiguratie** op hoe de agent die wordt uitgevoerd op de Windows-computer verbinding maakt met internet. Selecteer vervolgens **Volgende**.

   * Als u een aangepaste proxy gebruikt, geeft u de benodigde proxy-instellingen en referenties op.
   * Houd er wel voor dat de agent toegang nodig heeft [tot specifieke URL's.](#before-you-start)

    ![Internettoegang instellen in de MARS-wizard](./media/backup-configure-vault/mars2.png)

1. Controleer **bij Installatie** de vereisten en selecteer **Installeren.**
1. Nadat de agent is geïnstalleerd, selecteert **u Doorgaan naar registratie.**
1. Blader **in Register Server Wizard Vault** Identification naar en selecteer het  >  referentiebestand dat u hebt gedownload. Selecteer vervolgens **Volgende**.

    ![Kluisreferenties toevoegen met behulp van de wizard Server registreren](./media/backup-configure-vault/register1.png)

1. Geef op **de pagina Versleutelingsinstelling** een wachtwoordzin op die wordt gebruikt voor het versleutelen en ontsleutelen van back-ups voor de machine. [Kijk hier](backup-azure-file-folder-backup-faq.yml#what-characters-are-allowed-for-the-passphrase-) voor meer informatie over toegestane wachtwoordzintekens.

    * Sla de wachtwoordzin op een veilige locatie op. U hebt deze nodig om een back-up te herstellen.
    * Als u de wachtwoordzin verliest of vergeet, kan Microsoft u niet helpen bij het herstellen van de back-upgegevens.

1. Selecteer **Finish**. De agent is nu geïnstalleerd en uw computer is geregistreerd bij de kluis. U kunt nu uw back-up configureren en plannen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een back-up van Windows-machines met behulp van Azure Backup MARS-agent](backup-windows-with-mars-agent.md)
