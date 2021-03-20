---
title: 'Zelfstudie: Windows-bestandsservers uitbreiden met Azure File Sync | Microsoft Docs'
description: Informatie over hoe u Windows-bestandsservers kunt uitbreiden met Azure File Sync, van begin tot eind.
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1d2a0d79a5cdd53f8376c088fc986c20908575eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91329450"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>Zelfstudie: Windows-bestandsservers uitbreiden met Azure File Sync

In dit artikel worden de basisstappen getoond voor het uitbreiden van de opslagcapaciteit van een Windows-server met Azure File Sync. Hoewel in deze zelfstudie een Windows Server als een virtuele Azure-machine (VM) wordt gebruikt, voert u dit proces gewoonlijk uit voor uw on-premises servers. Instructies voor het implementeren van Azure File Sync in uw eigen omgeving vindt u in het artikel [Azure File Sync implementeren](storage-sync-files-deployment-guide.md).

> [!div class="checklist"]
> * Opslagsynchronisatieservice implementeren
> * Windows Server voorbereiden voor gebruik met Azure File Sync
> * Azure File Sync-agent installeren
> * Windows Server registreren met de opslagsynchronisatieservice
> * Synchronisatiegroep en cloudeindpunt maken
> * Servereindpunt maken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

Voor deze zelfstudie moet u het volgende doen voordat u Azure File Sync kunt implementeren:

- Een Azure-opslagaccount en een bestandsshare maken
- Een virtuele machine met Windows Server 2016 Datacenter instellen
- De virtuele machine met Windows Server voorbereiden voor Azure File Sync

### <a name="create-a-folder-and-txt-file"></a>Map en TXT-bestand maken

Maak op de lokale computer een nieuwe map met de naam _FilesToSync_ en voeg het tekstbestand _mytestdoc.txt_ toe. Later in deze zelfstudie uploadt u dat bestand naar de bestandsshare.

### <a name="create-a-storage-account"></a>Create a storage account

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>Een bestandsshare maken

Nadat u een Azure-opslagaccount hebt geïmplementeerd, kunt u een bestandsshare maken.

1. Selecteer **Naar de resource gaan** in de Microsoft Azure-portal.
1. Selecteer **Bestanden** in het deelvenster met het opslagaccount.

    ![Bestanden selecteren](./media/storage-sync-files-extend-servers/click-files.png)

1. Selecteer **+ Bestandsshare**.

    ![De knop Bestandsshare toevoegen selecteren](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. Geef de nieuwe bestandsshare de naam _afsfileshare_. Voer '1' voor het **Quotum** en selecteer vervolgens **Maken**. Het quotum kan maximaal 5 TiB zijn, maar voor deze zelfstudie hebt u slechts 1 GB nodig.

    ![Een naam en een quotum opgeven voor de nieuwe bestandsshare](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. Selecteer de nieuwe bestandsshare. Selecteer **Uploaden** op de bestandssharelocatie.

    ![Bestand uploaden](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. Ga naar de map _FilesToSync_, waar u het TXT-bestand hebt gemaakt, selecteer _mytestdoc.txt_ en selecteer **Uploaden**.

    ![In bestandsshare bladeren](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

U hebt nu een opslagaccount gemaakt en een bestandsshare met één bestand. U gaat nu een Azure-VM met Windows Server 2016 Datacenter maken, die de on-premises server in deze zelfstudie weergeeft.

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>Een VM implementeren en een gegevensschijf koppelen

1. Ga naar de Microsoft Azure-portal en vouw het menu aan de linkerkant uit. Kies in de linkerbovenhoek **Een resource maken**.
1. Zoek via het zoekvak boven de lijst met **Microsoft Azure Marketplace**-resources naar **Windows Server 2016 Datacenter** en selecteer dit in de resultaten. Kies **Maken**.
1. Ga naar het tabblad **Basis**. Selecteer onder **Projectdetails** de resourcegroep die u voor deze zelfstudie hebt gemaakt.

   ![Basisinformatie over uw virtuele machine invoeren op de portalblade](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. Geef onder **Exemplaardetails** een naam op voor de VM. Gebruik bijvoorbeeld _myVM_.
1. Behoud de standaardinstellingen voor **Regio**, **Beschikbaarheidsopties**, **Installatiekopie** en **Grootte**.
1. Geef onder **Administrator-account** een **gebruikersnaam** en een **wachtwoord** voor de VM op.
1. Onder **Regels voor binnenkomende poort** kiest u **​​Geselecteerde poorten toestaan** en selecteert u **RDP (3389)** en **HTTP** in het vervolgkeuzemenu.

1. Voordat u de VM kunt maken, dient u en gegevensschijf te maken.

   1. Selecteer **Volgende: schijven**.

      ![Gegevensschijven toevoegen](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

   1. Behoud op het tabblad **Schijven**, onder **Schijfopties**, de standaardinstellingen.
   1. Selecteer onder **GEGEVENSSCHIJVEN** de optie **Een nieuwe schijf maken en koppelen**.

   1. Behoud de standaardinstellingen, behalve voor **Grootte (GiB)** , die u voor deze zelfstudie kunt wijzigen in **1 GB**.

      ![Details van gegevensschijf](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

   1. Selecteer **OK**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

   U kunt het pictogram **Meldingen** selecteren om de **implementatievoortgang** zien. Het duurt enkele minuten voordat de nieuwe VM is voltooid.

1. Nadat de implementatie van de VM is voltooid, selecteert u **Ga naar resource**.

   ![Ga naar resource](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

U hebt nu een nieuwe virtuele machine gemaakt en een gegevensschijf gekoppeld. Maak vervolgens verbinding met de VM.

### <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

1. Selecteer in de Microsoft Azure-portal, op de eigenschappenpagina van de virtuele machine, de optie **Verbinding maken**.

   ![Verbinding maken met een Azure VM vanaf de portal](./media/storage-sync-files-extend-servers/connect-vm.png)

1. Laat op de pagina **Verbinding maken met virtuele machine** de standaardopties staan om verbinding te maken met een **IP-adres** via poort 3389. Selecteer **RDP-bestand downloaden**.

   ![Het RDP-bestand downloaden](./media/storage-sync-files-extend-servers/download-rdp.png)

1. Open het gedownloade RDP-bestand en selecteer **Verbinden** wanneer dit wordt gevraagd.
1. Selecteer in het venster **Windows-beveiliging** **Meer opties** en vervolgens **Een ander account gebruiken**. Typ de gebruikersnaam als *localhost\username*, voer het wachtwoord in dat u voor de virtuele machine hebt gemaakt en selecteer vervolgens **OK**.

   ![Meer keuzes](./media/storage-sync-files-extend-servers/local-host2.png)

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Selecteer **Ja** of **Doorgaan** om de verbinding te maken.

### <a name="prepare-the-windows-server"></a>Windows Server voorbereiden

Schakel Verbeterde beveiliging van Internet Explorer uit voor de Windows Server 2016 Datacenter-server. Deze step is alleen noodzakelijk als u de server voor het eerst registreert. U kunt de optie opnieuw inschakelen nadat de server is geregistreerd.

Op de virtuele machine met Windows Server 2016 Datacenter wordt Serverbeheer automatisch geopend.  Als Serverbeheer niet standaard wordt geopend, kunt u er naar zoeken in het menu Start.

1. Selecteer in **Serverbeheer** de optie **Lokale server**.

   ![Lokale server aan de linkerkant van de gebruikersinterface van Serverbeheer](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. Selecteer in het deelvenster **Eigenschappen** de koppeling naar **Verbeterde beveiliging van Internet Explorer**.  

    ![Het deelvenster Verbeterde beveiliging van Internet Explorer in de gebruikersinterface van Serverbeheer](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. Selecteer in het dialoogvenster **Verbeterde beveiliging van Internet Explorer** de optie **Uit** voor **Administrators** en **Gebruikers**.

    ![Het pop-upvenster Verbeterde beveiliging van Internet Explorer met de optie Uit geselecteerd](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

U kunt nu de gegevensschijf aan de VM toevoegen.

### <a name="add-the-data-disk"></a>De gegevensschijf toevoegen

1. Terwijl u nog steeds in **Windows Server 2016 Datacenter** aanwezig bent, selecteert u **Bestanden en opslagservices** > **Volumes** > **Schijven**.

    ![Gegevensschijf](media/storage-sync-files-extend-servers/your-disk.png)

1. Klik met de rechtermuisknop op de 1 GB-schijf met de naam **Msft Virtual Disk** en selecteer **Nieuw volume**.
1. Voltooi de wizard. Gebruik de standaardinstellingen en noteer de toegewezen stationsletter.
1. Selecteer **Maken**.
1. Selecteer **Sluiten**.

   U hebt nu de schijf online gebracht en een volume gemaakt. Open Verkenner op de virtuele machine met Windows Server om de aanwezigheid van de onlangs toegevoegde gegevensschijf te bevestigen.

1. Vouw in Verkenner op de VM **Deze pc** uit en open het nieuwe station. In dit voorbeeld is dat het F-station.
1. Klik er met de rechtermuisknop op en selecteer **Nieuw** > **Map**. Geef de map de naam _FilesToSync_.
1. Open de map **FilesToSync**.
1. Klik er met de rechtermuisknop op en selecteer **Nieuw** > **Tekstdocument**. Geef het tekstbestand de naam _MyTestFile_.

    ![Een nieuw tekstbestand toevoegen](media/storage-sync-files-extend-servers/new-file.png)

1. Sluit **Verkenner** en **Serverbeheer**.

### <a name="download-the-azure-powershell-module"></a>Azure PowerShell-module downloaden

Installeer vervolgens op de virtuele machine met Windows Server 2016 Datacenter de Azure PowerShell-module op de server.

1. Open op de virtuele machine een PowerShell-venster met verhoogde bevoegdheid.
1. Voer de volgende opdracht uit:

   ```powershell
   Install-Module -Name Az
   ```

   > [!NOTE]
   > Als u een NuGet-versie hebt die ouder is dan 2.8.5.201, wordt u gevraagd om de meest recente versie van NuGet te downloaden en installeren.

   PowerShell Gallery is standaard niet geconfigureerd als een vertrouwde opslagplaats voor PowerShellGet. De eerste keer dat u PSGallery gebruikt, ziet u het volgende bericht:

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. Antwoord **Ja** of **Ja op alles** om door te gaan met de installatie.

De `Az`-module is een updatepakketmodule voor de Azure PowerShell-cmdlets. Wanneer u deze installeert, worden alle beschikbare Azure Resource Manager-modules gedownload en kunnen de cmdlets uit deze modules worden gebruikt.

U hebt nu uw omgeving voor de zelfstudie ingesteld. U bent er klaar voor om de opslagsynchronisatieservice implementeren.

## <a name="deploy-the-service"></a>Implementeer de service

Om Azure File Sync te implementeren, plaatst u eerst een resource van een **opslagsynchronisatieservice** in een resourcegroep voor uw geselecteerde abonnement. De opslagsynchronisatieservice neemt toegangsmachtigingen over van het abonnement en de resourcegroep.

1. Selecteer in de Microsoft Azure-portal de optie **Een resource maken** en zoek naar **Azure File Sync**.
1. Selecteer **Azure File Sync** in de lijst met zoekresultaten.
1. Selecteer **Maken** om het tabblad **Opslagsynchronisatie implementeren** te openen.

   ![Opslagsynchronisatie implementeren](media/storage-sync-files-extend-servers/afs-info.png)

   Voer de volgende gegevens in in het deelvenster dat verschijnt:

   | Waarde | Beschrijving |
   | ----- | ----- |
   | **Naam** | Een unieke naam (per abonnement) voor opslagsynchronisatieservice.<br><br>Gebruik voor deze zelfstudie _afssyncservice02_. |
   | **Abonnement** | Het Azure-abonnement dat u gebruikt voor deze zelfstudie. |
   | **Resourcegroep** | De resourcegroep die de opslagsynchronisatieservice bevat.<br><br>Gebruik voor deze zelfstudie _afsresgroup101918_. |
   | **Locatie** | VS - oost |

1. Als u klaar bent, selecteert u **Maken** om de **opslagsynchronisatieservice** te implementeren.
1. Selecteer het tabblad **Meldingen** > **Naar de resource gaan**.

## <a name="install-the-agent"></a>De agent installeren

De Azure File Sync-agent is een downloadbaar pakket waardoor Windows Server met een Azure-bestandsshare kan worden gesynchroniseerd.

1. Open op de VM **Windows Server 2016 Datacenter** het programma **Internet Explorer**.
1. Ga naar het [Microsoft Downloadcentrum](https://go.microsoft.com/fwlink/?linkid=858257). Schuif omlaag naar de sectie **Azure File Sync-agent** en selecteer **Downloaden**.

   ![Downloaden van synchronisatieagent](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. Selecteer het selectievakje voor **StorageSyncAgent_V3_WS2016.EXE** en selecteer **Volgende**.

   ![Agent selecteren](media/storage-sync-files-extend-servers/select-agent.png)

1. Selecteer **Eenmaal toestaan** > **Uitvoeren** > **Openen**.
1. Sluit het PowerShell-venster als u dat nog niet hebt gedaan.
1. Accepteer de standaardwaarden in de **Storage Sync Agent Setup Wizard**.
1. Selecteer **Installeren**.
1. Selecteer **Finish**.

U hebt de Azure Sync-service geïmplementeerd en de agent geïnstalleerd op de VM met Windows Server 2016 Datacenter. U moet de VM nu registreren bij de opslagsynchronisatieservice.

## <a name="register-windows-server"></a>Windows Server registreren

Als u Windows Server registreert voor een opslagsynchronisatieservice, wordt er een vertrouwensrelatie ingesteld tussen uw server (of cluster) en de opslagsynchronisatieservice. Een server kan maar bij één opslagsynchronisatieservice worden geregistreerd. Deze kan worden gesynchroniseerd met andere servers en Azure-bestandsshares die aan deze opslagsynchronisatieservice zijn gekoppeld.

De gebruikersinterface van de serverregistratie moet automatisch worden geopend nadat u de Azure File Sync-agent hebt geïnstalleerd. Als dat niet zo is, kunt u de interface handmatig openen vanaf de bestandslocatie: `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe.`

1. Als de gebruikersinterface van de serverregistratie in de VM wordt geopend, selecteert u **OK**.
1. Selecteer **Aanmelden** om te beginnen.
1. Meld u aan met uw Azure-accountreferenties en selecteer **Aanmelden**.
1. Geef de volgende informatie op:

   ![Schermafbeelding van de gebruikersinterface van de serverregistratie](media/storage-sync-files-extend-servers/signin.png)

   | Waarde | Beschrijving |
   | ----- | ----- |
   | **Azure-abonnement** | Het abonnement dat de opslagsynchronisatieservice voor deze zelfstudie bevat. |
   | **Resourcegroep** | De resourcegroep die de opslagsynchronisatieservice bevat. Gebruik voor deze zelfstudie _afsresgroup101918_. |
   | **Opslagsynchronisatieservice** | De naam van de opslagsynchronisatieservice. Gebruik voor deze zelfstudie _afssyncservice02_. |

1. Selecteer **Registreren** om de serverregistratie te voltooien.
1. Als onderdeel van het registratieproces wordt u gevraagd om u nogmaals aan te melden. Meld u aan en selecteer **Volgende**.
1. Selecteer **OK**.

## <a name="create-a-sync-group"></a>Synchronisatiegroep maken

Een synchronisatiegroep definieert de synchronisatietopologie voor een verzameling bestanden. Een synchronisatiegroep moet één cloudeindpunt bevatten, dat een Azure-bestandsshare representeert. Een synchronisatiegroep moet ook een of meer servereindpunten bevatten. Een servereindpunt representeert een pad naar een geregistreerde server. Ga als volgt te werk om een synchronisatiegroep te maken:

1. Selecteer in de [Microsoft Azure-portal](https://portal.azure.com/) de optie **+ Synchronisatiegroep** vanuit de opslagsynchronisatieservice. Gebruik voor deze zelfstudie *afssyncservice02*.

   ![Een nieuwe synchronisatiegroep in de Azure-portal maken](media/storage-sync-files-extend-servers/add-sync-group.png)

1. Voer de volgende gegevens in om een synchronisatiegroep met een cloudeindpunt te maken:

   | Waarde | Beschrijving |
   | ----- | ----- |
   | **Naam synchronisatiegroep** | Deze naam moet uniek zijn binnen de opslagsynchronisatieservice, maar het mag een willekeurige naam zijn die u makkelijk kunt onthouden. Gebruik voor deze zelfstudie *afssyncgroup*.|
   | **Abonnement** | Het abonnement waar u de opslagsynchronisatieservice voor deze zelfstudie hebt geïmplementeerd. |
   | **Opslagaccount** | Kies **Opslagaccount selecteren**. Selecteer in het deelvenster dat verschijnt het opslagaccount met de Azure-bestandsshare die u hebt gemaakt. Gebruik voor deze zelfstudie *afsstoracct101918*. |
   | **Azure-bestandsshare** | De naam van de Azure-bestandsshare die u hebt gemaakt. Gebruik voor deze zelfstudie *afsfileshare*. |

1. Selecteer **Maken**.

Als u uw synchronisatiegroep selecteert, ziet u dat u nu één **cloudeindpunt** hebt.

## <a name="add-a-server-endpoint"></a>Servereindpunt toevoegen

Een servereindpunt vertegenwoordigt een specifieke locatie op een geregistreerde server. Bijvoorbeeld een map op een servervolume. Ga als volgt te werk om een servereindpunt toe te voegen:

1. Selecteer de nieuw gemaakte synchronisatiegroep te selecteren en selecteer vervolgens **Servereindpunt toevoegen**.

   ![Nieuw servereindpunt toevoegen in het deelvenster met de synchronisatiegroep](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. Voer in het deelvenster **Servereindpunt toevoegen** de volgende gegevens in om een servereindpunt te maken:

   | Waarde | Beschrijving |
   | ----- | ----- |
   | **Geregistreerde server** | De naam van de server die u hebt gemaakt. Gebruik voor deze zelfstudie *afsvm101918*. |
   | **Pad** | Het Windows Server-pad naar het station dat u hebt gemaakt. Gebruik in deze zelfstudie *f:\filestosync*. |
   | **Cloudopslaglagen** | Voor deze zelfstudie uitgeschakeld laten. |
   | **Beschikbare volumeruimte** | Voor deze zelfstudie leeg laten. |

1. Selecteer **Maken**.

Uw bestanden zijn nu gesynchroniseerd met uw Azure-bestandsshare en Windows Server.

![Azure Storage is gesynchroniseerd](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources wilt opschonen die u in deze zelfstudie hebt gemaakt, verwijdert u eerst de eindpunten uit de opslagsynchronisatieservice. Maak vervolgens de registratie van de server bij de opslagsynchronisatieservice ongedaan, verwijder de synchronisatiegroepen en verwijder de synchronisatieservice.

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de basisstappen geleerd voor het uitbreiden van de opslagcapaciteit van een Windows-server met Azure File Sync. Zie voor meer inzicht in het plannen van een Azure File Sync-implementatie:

> [!div class="nextstepaction"]
> [Planning voor de implementatie van Azure File Sync](./storage-sync-files-planning.md)
