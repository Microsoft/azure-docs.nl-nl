---
title: De Microsoft Azure Recovery Services-agent (MARS) installeren
description: Meer informatie over het installeren van de Microsoft Azure Recovery Services-agent (MARS) voor het maken van back-ups van Windows-machines.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: acf38fdf338fcdd0cd7902d4295f0f03310543a8
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986867"
---
# <a name="install-the-azure-backup-mars-agent"></a>Installeer de Azure Backup MARS-agent

In dit artikel wordt uitgelegd hoe u de Microsoft Azure Recovery Services-agent (MARS) installeert. MARS wordt ook wel de Azure Backup-Agent genoemd.

## <a name="about-the-mars-agent"></a>Over de MARS-agent

Azure Backup maakt gebruik van de MARS-agent om een back-up te maken van bestanden, mappen en de systeem status van on-premises machines en Azure-Vm's. Deze back-ups worden opgeslagen in een Recovery Services kluis in Azure. U kunt de agent uitvoeren:

* Rechtstreeks op de on-premises Windows-computers. Deze machines kunnen rechtstreeks een back-up maken van een Recovery Services kluis in Azure.
* Op virtuele Azure-machines waarop Windows naast de Azure VM-back-upextensie wordt uitgevoerd. De agent maakt een back-up van specifieke bestanden en mappen op de VM.
* Op een MABS-exemplaar (Microsoft Azure Backup Server) of een System Center Data Protection Manager (DPM)-server. In dit scenario maken machines en workloads een back-up naar MABS of Data Protection Manager. MABS of Data Protection Manager maakt gebruik van de MARS-agent om een back-up te maken naar een kluis in Azure.

De gegevens die beschikbaar zijn voor back-up, zijn afhankelijk van waar de agent is geïnstalleerd.

> [!NOTE]
> Over het algemeen maakt u een back-up van een virtuele Azure-machine met behulp van een Azure Backup-extensie op de VM. Met deze methode maakt u een back-up van de volledige VM. Als u een back-up wilt maken van specifieke bestanden en mappen op de VM, installeert en gebruikt u de MARS-agent naast de extensie. Zie [architectuur van een ingebouwde Azure VM-back-up](backup-architecture.md#architecture-built-in-azure-vm-backup)voor meer informatie.

![Stappen voor back-upproces](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Voordat u begint

* Meer informatie over hoe [Azure backup de Mars-agent gebruikt om een back-up te maken van Windows-machines](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Meer informatie over de [back-uparchitectuur](backup-architecture.md#architecture-back-up-to-dpmmabs) voor het uitvoeren van de Mars-agent op een secundaire MABS of Data Protection Manager-server.
* Bekijk [wat wordt ondersteund en waarvan u een back-up kunt maken](backup-support-matrix-mars-agent.md) door de Mars-agent.
* Zorg ervoor dat u een Azure-account hebt als u een back-up van een server of client naar Azure wilt maken. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis versie](https://azure.microsoft.com/free/) maken.
* Controleer de Internet toegang op de computers waarvan u een back-up wilt maken.
* Zorg ervoor dat de gebruiker die de installatie en configuratie van de MARS-agent uitvoert, lokale beheerders rechten heeft op de server die moet worden beveiligd.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Opslag replicatie wijzigen

Standaard gebruiken kluizen de [geo-redundante opslag (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Als de kluis uw primaire back-upmechanisme is, raden we u aan GRS te gebruiken.
* U kunt [lokaal redundante opslag (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) gebruiken om de kosten voor Azure Storage te reduceren.

Het type opslag replicatie wijzigen:

1. Selecteer in de nieuwe kluis **Eigenschappen** onder de sectie **instellingen** .

1. Selecteer op de pagina **Eigenschappen** onder **back-upconfiguratie** de optie **bijwerken**.

1. Selecteer het type opslag replicatie en selecteer **Opslaan**.

    ![Back-upconfiguratie bijwerken](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> U kunt het type opslag replicatie niet wijzigen nadat de kluis is ingesteld en back-upitems bevat. Als u dit wilt doen, moet u de kluis opnieuw maken.
>

### <a name="verify-internet-access"></a>Internettoegang controleren

Als uw computer beperkte internet toegang heeft, moet u ervoor zorgen dat de firewall instellingen op de computer of de proxy de volgende Url's en IP-adressen toestaan:

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

### <a name="use-azure-expressroute"></a>Azure-ExpressRoute gebruiken

U kunt een back-up van uw gegevens via Azure ExpressRoute maken met behulp van open bare peering (beschikbaar voor oude circuits) en micro soft-peering. Back-up via privé-peering wordt niet ondersteund.

Als u open bare peering wilt gebruiken, zorg er dan voor dat u toegang hebt tot de volgende domeinen en adressen:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`
* IP-adressen
  * 20.190.128.0/18
  * 40.126.0.0/18

Als u micro soft-peering wilt gebruiken, selecteert u de volgende services, regio's en relevante Community-waarden:

* Azure Active Directory (12076:5060)
* Azure-regio, op basis van de locatie van uw Recovery Services kluis
* Azure Storage, op basis van de locatie van uw Recovery Services kluis

Zie [ExpressRoute Routing requirements](../expressroute/expressroute-routing.md)(Engelstalig) voor meer informatie.

> [!NOTE]
> Open bare peering is afgeschaft voor nieuwe circuits.

Alle voor gaande Url's en IP-adressen gebruiken het HTTPS-protocol op poort 443.

### <a name="private-endpoints"></a>Privé-eindpunten

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>De MARS-agent downloaden

Down load de MARS-agent zodat u deze kunt installeren op de computers waarvan u een back-up wilt maken.

Als u de agent al op alle computers hebt geïnstalleerd, moet u ervoor zorgen dat u de nieuwste versie van de agent uitvoert. Zoek de meest recente versie in de portal of ga rechtstreeks naar de [down load](https://aka.ms/azurebackup_agent).

1. Selecteer in de kluis, onder **aan** de slag, **back-up maken**.

    ![Het doel van de back-up openen](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Selecteer onder **waar wordt uw workload uitgevoerd? de** optie **on-premises**. Selecteer deze optie, zelfs als u de MARS-agent wilt installeren op een virtuele Azure-machine.
1. Selecteer **bestanden en mappen** onder **waarvan wilt u een back-up maken?** U kunt ook **systeem status** selecteren. Er zijn veel andere opties beschikbaar, maar deze opties worden alleen ondersteund als u een secundaire back-upserver uitvoert. Selecteer **infra structuur voorbereiden**.

    ![Bestanden en mappen configureren](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Voor het voorbereiden van de **infra structuur**, onder **Installeer Recovery Services agent**, downloadt u de Mars-agent.

    ![De infrastructuur voorbereiden](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. Selecteer in het menu downloaden de optie **Opslaan**. Standaard wordt het bestand *MARSagentinstaller.exe* opgeslagen in de map Downloads.

1. Selecteer **al downloaden of gebruik de nieuwste Recovery Services-agent** en down load vervolgens de kluis referenties.

    ![Kluisreferenties downloaden](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selecteer **Opslaan**. Het bestand wordt gedownload naar de map down loads. Het kluis referentie bestand kan niet worden geopend.

## <a name="install-and-register-the-agent"></a>De agent installeren en registreren

1. Voer het *MARSagentinstaller.exe* -bestand uit op de computers waarvan u een back-up wilt maken.
1. In de installatie wizard van de MARS-agent selecteert u **installatie-instellingen**. Hier kunt u kiezen waar u de agent wilt installeren en kiest u een locatie voor de cache. Selecteer vervolgens **Volgende**.
   * Azure Backup gebruikt de cache om moment opnamen van gegevens op te slaan voordat ze naar Azure worden verzonden.
   * De cache locatie moet vrije ruimte hebben die gelijk is aan ten minste 5 procent van de grootte van de gegevens waarvan u een back-up maakt.

    ![Installatie-instellingen kiezen in de installatie wizard van de MARS-agent](./media/backup-configure-vault/mars1.png)

1. Geef voor **proxy configuratie** op hoe de agent die wordt uitgevoerd op de Windows-computer, verbinding maakt met internet. Selecteer vervolgens **Volgende**.

   * Als u een aangepaste proxy gebruikt, geeft u de benodigde proxy-instellingen en referenties op.
   * Houd er rekening mee dat de agent toegang moet hebben tot [specifieke url's](#before-you-start).

    ![Internet toegang instellen in de MARS-wizard](./media/backup-configure-vault/mars2.png)

1. Controleer de vereisten voor de **installatie** en selecteer **installeren**.
1. Nadat de agent is geïnstalleerd, selecteert u **door gaan naar registratie**.
1. In de **wizard Server registreren**  >  **kluis-id**, bladert u naar en selecteert u het referentie bestand dat u hebt gedownload. Selecteer vervolgens **Volgende**.

    ![Kluis referenties toevoegen met behulp van de wizard Server registreren](./media/backup-configure-vault/register1.png)

1. Geef op de pagina **versleutelings instelling** een wachtwoordzin op die wordt gebruikt voor het versleutelen en ontsleutelen van back-ups voor de machine. [Zie hier](backup-azure-file-folder-backup-faq.md#what-characters-are-allowed-for-the-passphrase) voor meer informatie over toegestane wachtwoordzin tekens.

    * Sla de wachtwoordzin op een veilige locatie op. U hebt deze nodig om een back-up te herstellen.
    * Als u de wachtwoordzin kwijtraakt of vergeet, kan micro soft u niet helpen bij het herstellen van de back-upgegevens.

1. Selecteer **Finish**. De agent is nu geïnstalleerd en de computer is geregistreerd bij de kluis. U kunt nu uw back-up configureren en plannen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het maken van een back-up van Windows-machines met behulp van de Azure backup Mars-agent](backup-windows-with-mars-agent.md)
