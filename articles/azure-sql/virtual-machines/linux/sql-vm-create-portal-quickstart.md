---
title: 'Snelstart: Een virtuele Linux-machine met SQL Server maken in Azure'
description: Deze zelfstudie laat zien hoe u een virtuele SQL Server 2017-machine voor Linux in Azure Portal kunt maken.
services: virtual-machines-linux
author: MashaMSFT
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: quickstart
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fd5c289f2b441b5862d863d9a390a1cd054acbfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790148"
---
# <a name="provision-a-linux-virtual-machine-running-sql-server-in-the-azure-portal"></a>Een virtuele Linux-machine waarop SQL Server wordt ingevoerd, inrichten in Azure Portal
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Linux](sql-vm-create-portal-quickstart.md)
> * [Windows](../windows/sql-vm-create-portal-quickstart.md)

In deze quickstart-zelfstudie gaat u Azure Portal gebruiken om een virtuele Linux-machine te maken waarop SQL Server 2017 is geïnstalleerd. U leert het volgende: 


* [Een virtuele Linux-machine met SQL Server maken vanuit de galerie](#create)
* [Verbinding te maken met de nieuwe virtuele machine via ssh](#connect)
* [Het SA-wachtwoord wijzigen](#password)
* [Voor externe verbindingen te configureren](#remote)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a> Een virtuele Linux-machine maken waarop SQL Server is geïnstalleerd

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **Een resource maken** in het linkerdeelvenster.

1. Selecteer in het deelvenster **Een resource maken** de optie **Compute**.

1. Selecteer **Alles bekijken** naast de kop **Aanbevolen**.

   ![Alle VM-installatiekopieën bekijken](./media/sql-vm-create-portal-quickstart/azure-compute-blade.png)

1. Typ in het zoekvak **SQL Server 2019** en selecteer **Enter** om met zoeken te beginnen.

1. Beperk de lijst met zoekresultaten door **Besturingssysteem** > **Redhat** te selecteren.

    ![Zoekfilter voor installatiekopieën van virtuele SQL Server 2019-machines](./media/sql-vm-create-portal-quickstart/searchfilter.png)

1. Selecteer een installatiekopie van SQL Server 2019 voor Linux in de zoekresultaten. In deze zelfstudie wordt gebruikgemaakt van **SQL Server 2019 op RHEL74**.

   > [!TIP]
   > Met de Developer-versie kunt u testen of ontwikkelen met de functies van de Enterprise-editie, zonder SQL Server-licentiekosten. U betaalt alleen om met de virtuele Linux-machine te werken.

1. Selecteer **Maken**. 


### <a name="set-up-your-linux-vm"></a>Uw Linux-VM instellen

1. Selecteer in het tabblad **Basisinformatie** uw **Abonnement** en **Resourcegroep**. 

    ![Het venster Basisinformatie](./media/sql-vm-create-portal-quickstart/basics.png)

1. Voer bij **Naam virtuele machine** een naam in voor uw nieuwe Linux-VM.
1. Typ of selecteer vervolgens de volgende waarden:
   * **Regio**: selecteer de Azure-regio die het beste bij u past.
   * **Beschikbaarheidsopties**: kies de beschikbaarheid en redundantie die het meest geschikt zijn voor uw apps en gegevens.
   * **Formaat wijzigen**: selecteer deze optie om een machineformaat te kiezen en kies daarna **Selecteren**. Zie [VM-grootten](../../../virtual-machines/sizes.md) voor meer informatie over de grootte van VM-machines.

     ![Een VM-grootte kiezen](./media/sql-vm-create-portal-quickstart/vmsizes.png)

   > [!TIP]
   > Voor de ontwikkeling en het uitvoeren van functionele tests kunt u het beste een VM-formaat van **DS2** of groter kiezen. Gebruik **DS13** of groter als u prestatietests wilt uitvoeren.

   * **Verificatietype**: selecteer **Openbare SSH-sleutel**.

     > [!Note]
     > U hebt de keuze om voor de verificatie een openbare SSH-sleutel of een wachtwoord te gebruiken. SSH is veiliger. Zie [SSH-sleutels maken in Linux en Mac voor virtuele Linux-machines in Azure](../../../virtual-machines/linux/mac-create-ssh-keys.md) voor instructies over het maken van een SSH-sleutel.

   * **Gebruikersnaam**: voer de naam van de beheerder voor de VM in.
   * **Openbare SSH-sleutel**: voer uw openbare RSA-sleutel in.
   * **Openbare binnenkomende poorten**: selecteer **Geselecteerde poorten toestaan** en kies de poort **SSH (22)** in de lijst **Openbare binnenkomende poorten selecteren**. Deze stap is nodig in deze quickstart om verbinding te maken en de SQL Server-configuratie te voltooien. Als u extern verbinding wilt maken met SQL Server, moet u handmatig verkeer toestaan tot de standaardpoort (1433), die door Microsoft SQL Server wordt gebruikt voor verbindingen via internet nadat de virtuele machine is gemaakt.

     ![Poorten voor inkomend verkeer](./media/sql-vm-create-portal-quickstart/port-settings.png)

1. Maak eventuele wijzigingen in de instellingen in de volgende aanvullende tabbladen of behoud de standaardinstellingen.
    * **Disks**
    * **Netwerken**
    * **Beheer**
    * **Gastconfiguratie**
    * **Tags**

1. Selecteer **Controleren + maken**.
1. Selecteer in het deelvenster **Controleren + maken** de optie **Maken**.

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> Verbinding maken met de virtuele Linux-machine

Als u al een BASH-shell gebruikt, moet u verbinding maken met de virtuele Azure-machine via de opdracht **ssh**. In de volgende opdracht vervangt u de VM-gebruikersnaam en het IP-adres om verbinding te maken met uw virtuele Linux-machine.

```bash
ssh azureadmin@40.55.55.555
```

U vindt het IP-adres van uw virtuele machine in Azure Portal.

![IP-adres in Azure Portal](./media/sql-vm-create-portal-quickstart/vmproperties.png)

Als u Windows gebruikt en geen BASH-shell hebt, installeert u een SSH-client, zoals PuTTY.

1. [PuTTY downloaden en installeren](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

1. Voer PuTTY uit.

1. Voer het openbare IP-adres van de virtuele machine in op het configuratiescherm van PuTTY.

1. Selecteer **Openen** en voer uw gebruikersnaam en wachtwoord in bij de prompts.

Zie [Een virtuele Linux-machine in Azure maken met behulp van de portal](../../../virtual-machines/linux/quick-create-portal.md) voor meer informatie over verbinding maken met virtuele Linux-machines.

> [!NOTE]
> Als er een PuTTY-beveiligingswaarschuwing wordt weergegeven dat de hostsleutel van de server niet in het register wordt opgeslagen, kunt u uit de volgende opties kiezen. Als u deze host vertrouwt, selecteert u **Ja** om de sleutel aan de PuTTy-cache toe te voegen en door te gaan met verbinding maken. Als u eenmalig verbinding wilt maken, zonder de sleutel aan de cache toe te voegen, selecteert u **Nee**. Als u deze host niet vertrouwt, selecteert u **Annuleren** om de verbinding te verbreken.

## <a name="change-the-sa-password"></a><a id="password"></a>Het SA-wachtwoord wijzigen

Op de nieuwe virtuele machine wordt SQL Server geïnstalleerd met een willekeurig SA-wachtwoord. Stel dit wachtwoord opnieuw in voordat u met de SA-aanmeldingsgegevens verbinding met SQL Server maakt.

1. Nadat u verbinding hebt gemaakt met uw virtuele Linux-machine, opent u een nieuwe opdrachtenterminal.

1. Wijzig het SA-wachtwoord met de volgende opdrachten:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Voer een nieuw SA-wachtwoord en een wachtwoordbevestiging in wanneer dat wordt gevraagd.

1. Start de SQL Server-service opnieuw.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>De hulpprogramma's aan uw pad toevoegen (optioneel)

Meerdere SQL-Server-[pakketten](sql-server-on-linux-vm-what-is-iaas-overview.md#packages) worden standaard geïnstalleerd, met inbegrip van het pakket met opdrachtregelprogramma's van SQL Server. Het pakket bevat de hulpprogramma's **sqlcmd** en **bcp**. Voor meer gebruiksgemak kunt u ervoor kiezen het pad naar de hulpprogramma's `/opt/mssql-tools/bin/` toe te voegen aan de omgevingsvariabele **PAD**.

1. Voer de volgende opdrachten uit als u het **PAD** wilt wijzigen voor zowel aanmeldingssessies als interactieve/niet-aanmeldingssessies:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a name="configure-for-remote-connections"></a><a id="remote"></a> Voor externe verbindingen configureren

Als u op afstand verbinding wilt maken met SQL Server op de virtuele Azure-machine, moet u een inkomende regel configureren in de netwerkbeveiligingsgroep. De regel staat verkeer op de poort toe waarop door SQL Server wordt geluisterd (standaard is dat 1433). De volgende stappen laten zien hoe Azure Portal voor deze stap kan worden gebruikt.

> [!TIP]
> Als u de binnenkomende poort **MS SQL (1433)** hebt geselecteerd in de instellingen tijdens het inrichten, zijn deze wijzigingen voor u aangebracht. U kunt verder met de volgende sectie over het configureren van de firewall.

1. Selecteer in de portal **Virtuele machines** en selecteer de virtuele SQL Server-machine.
1. Selecteer in het linkernavigatiedeelvenster onder **Instellingen** de optie **Netwerken**.
1. Selecteer in het venster Netwerken onder **Regels voor binnenkomende poort** de optie **Binnenkomende poort toevoegen**.

   ![Regels voor binnenkomende poort](./media/sql-vm-create-portal-quickstart/networking.png)

1. Selecteer **MS SQL** in de lijst **Service**.

    ![Regel voor MS SQL-beveiligingsgroep](./media/sql-vm-create-portal-quickstart/sqlnsgrule.png)

1. Klik op **OK** om de regel voor uw virtuele machine op te slaan.

### <a name="open-the-firewall-on-rhel"></a>De firewall op RHEL openen

Deze zelfstudie heeft u richtlijnen gegeven om een virtuele RHEL-machine (Red Hat Enterprise Linux) te maken. Als u extern verbinding wilt maken met virtuele RHEL-machines, moet u poort 1433 op de Linux-firewall ook openen.

1. [Maak verbinding](#connect) met uw virtuele RHEL-machine.

1. Voer de volgende opdrachten uit in de BASH-shell:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Volgende stappen

Nu u een virtuele SQL Server 2017-machine in Azure hebt, kunt u met behulp van **sqlcmd** lokaal verbinding maken om Transact-SQL-query's uit te voeren.

Als u de virtuele Azure-machine voor externe verbindingen met SQL Server hebt geconfigureerd, zou u op afstand verbinding moeten kunnen maken. Zie [Use SSMS on Windows to connect to SQL Server on Linux](/sql/linux/sql-server-linux-develop-use-ssms) (SSMS gebruiken op Windows om verbinding te maken met SQL Server op Linux) als u een voorbeeld wilt zien van hoe u extern verbinding kunt maken met SQL Server op Linux vanuit Windows. Zie [Use Visual Studio Code to create and run Transact-SQL scripts for SQL Server](/sql/linux/sql-server-linux-develop-use-vscode) (Visual Studio Code gebruiken om Transact-SQL-scripts voor SQL Server te maken en uit te voeren) als u verbinding wilt maken met Visual Studio Code.

Zie [Overview of SQL Server 2017 on Linux](/sql/linux/sql-server-linux-overview) (Overzicht van SQL Server 2017 op Linux) voor meer algemene informatie over SQL Server op Linux. Zie [Overzicht van virtuele SQL Server 2017-machines in Azure](sql-server-on-linux-vm-what-is-iaas-overview.md) voor meer informatie over het gebruik van virtuele SQL Server 2017-machines voor Linux.