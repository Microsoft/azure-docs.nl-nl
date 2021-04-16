---
title: Gebruik Azure Backup Server om back-up te maken van workloads
description: In dit artikel leert u hoe u uw omgeving voorbereidt om workloads te beveiligen en er een back-up van te maken met Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: b13eb22ad11535114b1cb82630bc1b490a03173f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517569"
---
# <a name="install-and-upgrade-azure-backup-server"></a>De Azure Backup Server

> [!div class="op_single_selector"]
>
> * [Azure Backup-server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Van toepassing op: MABS v3. (MABS v2 wordt niet meer ondersteund. Als u een eerdere versie dan MABS v3 gebruikt, moet u een upgrade uitvoeren naar de nieuwste versie.)

In dit artikel wordt uitgelegd hoe u uw omgeving voorbereidt voor het maken van back-up van workloads met Microsoft Azure Backup Server (MABS). Met Azure Backup Server kunt u toepassingsworkloads zoals Hyper-V-VM's, Microsoft SQL Server, SharePoint Server, Microsoft Exchange en Windows-clients vanuit één console beveiligen.

> [!NOTE]
> Azure Backup Server kunnen nu VMware-VM's beveiligen en biedt verbeterde beveiligingsmogelijkheden. Installeer het product zoals uitgelegd in de onderstaande secties en de meest recente Azure Backup Agent. Zie het artikel Use Azure Backup Server to back up a VMware server (Een back-up maken van een [VMware-server)](backup-azure-backup-server-vmware.md)voor meer informatie over het maken van een back-up van VMware-servers met Azure Backup Server. Raadpleeg de documentatie over beveiligingsfuncties Azure Backup over beveiligingsfuncties voor meer informatie over [beveiligingsmogelijkheden.](backup-azure-security-feature.md)
>
>

MABS die is geïmplementeerd in een Azure-VM, kan een back-up maken van VM's in Azure, maar ze moeten zich in hetzelfde domein houden om back-upbewerkingen mogelijk te maken. Het proces voor het maken van een back-up van een virtuele Azure-VM blijft hetzelfde als het maken van back-up van on-premises VM's, maar het implementeren van MABS in Azure heeft enkele beperkingen. Zie DPM als een virtuele [Azure-machine voor meer informatie over beperkingen](/system-center/dpm/install-dpm#setup-prerequisites)

> [!NOTE]
> Azure heeft twee implementatiemodellen voor het maken en werken met resources: [Resource Manager en klassieke](../azure-resource-manager/management/deployment-models.md). Dit artikel bevat de informatie en procedures voor het herstellen van VM's die zijn geïmplementeerd met het Resource Manager model.
>
>

Azure Backup Server neemt een groot deel van de back-upfunctionaliteit van de werkbelasting over van Data Protection Manager (DPM). In dit artikel wordt een koppeling naar DPM-documentatie toegevoegd om een deel van de gedeelde functionaliteit uit te leggen. Hoewel Azure Backup Server veel van dezelfde functionaliteit als DPM deelt, maakt Azure Backup Server geen back-up naar tape en wordt deze niet geïntegreerd met System Center.

## <a name="choose-an-installation-platform"></a>Een installatieplatform kiezen

De eerste stap om de Azure Backup Server te krijgen, is het instellen van een Windows Server. Uw server kan zich in Azure of on-premises.

* Om on-premises workloads te beveiligen, moet de MABS-server zich on-premises bevinden.
* Om workloads te beveiligen die worden uitgevoerd op virtuele Azure-VM's, moet de MABS-server zich in Azure bevinden en worden uitgevoerd als een Azure-VM.

### <a name="using-a-server-in-azure"></a>Een server gebruiken in Azure

Wanneer u een server kiest voor het uitvoeren Azure Backup Server, is het raadzaam om te beginnen met een galerie-afbeelding van Windows Server 2016 Datacenter of Windows Server 2019 Datacenter. Het artikel Create your first Windows virtual machine in the Azure Portal (Uw eerste virtuele [Windows-machine](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)maken in de Azure Portal) bevat een zelfstudie om aan de slag te gaan met de aanbevolen virtuele machine in Azure, zelfs als u Azure nog nooit eerder hebt gebruikt. De aanbevolen minimale vereisten voor de virtuele machine (VM) van de server moeten zijn: Standard_A4_v2 met vier kernen en 8 GB RAM-geheugen.

Workloads beveiligen met Azure Backup Server kent veel nuances. De [beveiligingsmatrix voor MABS](./backup-mabs-protection-matrix.md) helpt deze nuances uit te leggen. Lees dit artikel volledig voordat u de machine implementeert.

### <a name="using-an-on-premises-server"></a>Een on-premises server gebruiken

Als u de basisserver niet wilt uitvoeren in Azure, kunt u de server uitvoeren op een Hyper-V-VM, een VMware-VM of een fysieke host. De aanbevolen minimale vereisten voor de serverhardware zijn twee kernen en 8 GB RAM-geheugen. De ondersteunde besturingssystemen worden vermeld in de volgende tabel:

| Besturingssysteem | Platform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2016 en de meest recente SPs |64 bits |Standard, Datacenter, Essentials  |

U kunt de DPM-opslag ontdubbelen met behulp van Windows Server-ontdubbeling. Meer informatie over hoe [DPM en ontdubbeling](/system-center/dpm/deduplicate-dpm-storage) samenwerken wanneer ze worden geïmplementeerd in Hyper-V-VM's.

> [!NOTE]
> Azure Backup Server is ontworpen om te worden uitgevoerd op een toegewezen server met één doel. U kunt de volgende Azure Backup Server installeren:
>
> * Een computer die wordt uitgevoerd als een domeincontroller
> * Een computer waarop de toepassingsserverfunctie is geïnstalleerd
> * Een computer die een System Center Operations Manager is
> * Een computer waarop Exchange Server wordt uitgevoerd
> * Een computer die een knooppunt van een cluster is
>
> Het installeren Azure Backup Server wordt niet ondersteund op Windows Server Core of Microsoft Hyper-V Server.

Voeg altijd Azure Backup Server toe aan een domein. Als u van plan bent om de server naar een ander domein te verplaatsen, installeert Azure Backup Server eerst en voegt u de server vervolgens toe aan het nieuwe domein. Het verplaatsen van Azure Backup Server machine naar een nieuw domein na de implementatie *wordt niet ondersteund.*

Of u nu back-upgegevens naar Azure verzendt of lokaal opgeslagen, Azure Backup Server moeten worden geregistreerd bij een Recovery Services-kluis.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Opslagreplicatie instellen

U kunt met de optie voor opslagreplicatie kiezen tussen geografisch redundante opslag en lokaal redundante opslag. Recovery Services-kluizen maken standaard gebruik van geografisch redundante opslag. Als deze kluis uw primaire kluis is, laat u de opslagoptie ingesteld op geografisch redundante opslag. Kies lokaal redundante opslag als u een goedkopere optie wilt die niet zo duurzaam is. Meer informatie over [geografisch redundante,](../storage/common/storage-redundancy.md#geo-redundant-storage) [lokaal redundante](../storage/common/storage-redundancy.md#locally-redundant-storage)en [zone-redundante opslagopties](../storage/common/storage-redundancy.md#zone-redundant-storage) vindt u in [Azure Storage overzicht van replicatie.](../storage/common/storage-redundancy.md)

De instelling voor opslagreplicatie bewerken:

1. Selecteer in **het deelvenster Recovery Services-kluizen** de nieuwe kluis. Selecteer eigenschappen **in** de sectie **Instellingen.**
2. Selecteer **in Eigenschappen** onder **Back-upconfiguratie** de optie **Bijwerken.**

3. Selecteer het type opslagreplicatie en selecteer **Opslaan.**

     ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Softwarepakket

### <a name="downloading-the-software-package"></a>Het softwarepakket downloaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Als u al een Recovery Services-kluis hebt geopend, gaat u verder met stap 3. Als u geen Recovery Services-kluis hebt geopend, maar wel in de Azure Portal, selecteert u bladeren in het **hoofdmenu.**

   * Typ in de lijst met resources **Recovery Services**.
   * Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Wanneer u **Recovery Services-kluizen ziet,** selecteert u deze.

     ![Stap 1 van Recovery Services-kluis maken](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     De lijst met Recovery Services-kluizen wordt weergegeven.
   * Selecteer in de lijst met Recovery Services-kluizen een kluis.

     Het geselecteerde kluisdashboard wordt geopend.

     ![Kluisdashboard](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Het **deelvenster** Instellingen wordt standaard geopend. Als de app is gesloten, selecteert **u Instellingen om** het deelvenster Instellingen te openen.

    ![Deelvenster Instellingen](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Selecteer **Back-up** om de wizard Aan de slag openen.

    ![Aan de slag met back-up](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    In het **Aan de slag met het back-upvenster** dat wordt **geopend,** worden back-updoelen automatisch geselecteerd.

    ![Backup-goals-default-opened](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Selecteer in **het deelvenster Doel** van de back-up in het menu Waar wordt uw **workload** **uitgevoerd? de optie On-premises.**

    ![on-premises en workloads als doelstellingen](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Selecteer in **de vervolgkeuzelijst** Waar wilt u een back-up van maken? de workloads die u wilt beveiligen met Azure Backup Server en selecteer **vervolgens OK.**

    Met **Aan de slag wizard Back-up** schakelt u over naar **de optie** Infrastructuur voorbereiden om een back-up te maken van workloads naar Azure.

   > [!NOTE]
   > Als u alleen een back-up wilt maken van bestanden en mappen, raden we u aan de Azure Backup-agent te gebruiken en de richtlijnen in het artikel Eerste [blik: back-up](./backup-windows-with-mars-agent.md)maken van bestanden en mappen te volgen. Als u meer dan bestanden en mappen wilt beveiligen, of als u van plan bent om de beveiligingsbehoeften in de toekomst uit te breiden, selecteert u deze workloads.
   >
   >

    ![Aan de slag wizard wijzigen](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Selecteer in **het deelvenster** Infrastructuur voorbereiden dat wordt geopend de **koppelingen Downloaden** voor Azure Backup Server en Kluisreferenties downloaden. U gebruikt de kluisreferenties tijdens de registratie van Azure Backup Server bij de Recovery Services-kluis. Via de koppelingen gaat u naar het Downloadcentrum waar het softwarepakket kan worden gedownload.

    ![Infrastructuur voorbereiden voor Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selecteer alle bestanden en selecteer **Volgende.** Download alle bestanden die afkomstig zijn van de Microsoft Azure Backup downloadpagina en plaats alle bestanden in dezelfde map.

    ![Downloadcentrum 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Omdat de downloadgrootte van alle bestanden samen > 3 GB is, kan het bij een downloadkoppeling van 10 Mbps tot 60 minuten duren voordat het downloaden is voltooid.

### <a name="extracting-the-software-package"></a>Het softwarepakket uitpakken

Nadat u alle bestanden hebt gedownload, selecteert **uMicrosoftAzureBackupInstaller.exe**. Hiermee start u de **Microsoft Azure Backup installatiewizard** de installatiebestanden uit te extraheren naar een locatie die u hebt opgegeven. Ga door met de wizard en selecteer de **knop Extraheren** om het extractieproces te starten.

> [!WARNING]
> Er is ten minste 4 GB vrije ruimte nodig om de installatiebestanden te extraheren.
>
>

![Bestanden extraheren voor installatie](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Nadat het extractieproces is voltooid, schakel  u het selectievakje in om de zojuist geëxtraheerdesetup.exete starten om te beginnen met de installatie van Microsoft Azure Backup Server en selecteert u **de knop** Voltooien.

### <a name="installing-the-software-package"></a>Het softwarepakket installeren

1. Selecteer **Microsoft Azure Backup** om de installatiewizard te starten.

    ![Microsoft Azure Backup installatiewizard](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Selecteer op het welkomstscherm de **knop Volgende.** Hiermee gaat u naar de *sectie Controles van* vereisten. Selecteer in dit scherm Controleren **om te** bepalen of aan de hardware- en softwarevoorwaarden voor Azure Backup Server is voldaan. Als aan alle vereisten is voldaan, ziet u een bericht dat aangeeft dat de machine aan de vereisten voldoet. Selecteer de knop **Volgende**.

    ![Azure Backup Server: welkom en controle op vereisten](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Het Azure Backup Server-installatiepakket wordt geleverd met de juiste SQL Server die nodig zijn. Wanneer u een nieuwe Azure Backup Server start, kiest u de optie Nieuw exemplaar van SQL Server **installeren** met deze installatie en selecteert u de knop **Controleren en** installeren. Zodra de vereisten zijn geïnstalleerd, selecteert u **Volgende.**

    >[!NOTE]
    >Als u uw eigen SQL-server wilt gebruiken, zijn de ondersteunde SQL Server-versies SQL Server 2014 SP1 of hoger, 2016 en 2017.  Alle SQL Server moeten Standard- of Enterprise 64-bits zijn.
    >Azure Backup Server werkt niet met een extern SQL Server exemplaar. Het exemplaar dat wordt gebruikt door Azure Backup Server moet lokaal zijn. Als u een bestaande SQL-server voor MABS gebruikt, ondersteunt de MABS-installatie alleen het gebruik van *benoemde exemplaren* van SQL Server.

    ![Azure Backup Server - SQL-controle](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Als er een fout optreedt met een aanbeveling om de computer opnieuw op te starten, doet u dit en selecteert **u Opnieuw controleren.** Als er problemen zijn met de SQL-configuratie, configureren we SQL opnieuw volgens de SQL-richtlijnen en proberen MABS opnieuw te installeren/upgraden met behulp van het bestaande exemplaar van SQL.

   **Handmatige configuratie**

   Wanneer u uw eigen exemplaar van SQL gebruikt, moet u builtin\Administrators toevoegen aan de rol sysadmin aan de hoofd-DB.

    **SSRS-configuratie met SQL 2017**

    Wanneer u uw eigen exemplaar van SQL 2017 gebruikt, moet u SSRS handmatig configureren. Controleer na de SSRS-configuratie of *de eigenschap IsInitialized* van SSRS is ingesteld op *True.* Wanneer dit is ingesteld op True, gaat MABS ervan uit dat SSRS al is geconfigureerd en slaat de SSRS-configuratie over.

    Gebruik de volgende waarden voor SSRS-configuratie:
    * Serviceaccount: 'Ingebouwd account gebruiken' moet Netwerkservice zijn
    * WEBSERVICE-URL: 'Virtual Directory' moet ReportServer_\<SQLInstanceName>
    * Database: DatabaseName moet ReportServer$ zijn\<SQLInstanceName>
    * URL van webportal: 'Virtuele map' moet Reports_\<SQLInstanceName>

    [Meer informatie over](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) SSRS-configuratie.

    > [!NOTE]
    > Licenties voor SQL Server die worden gebruikt als de database voor MABS, worden beheerd door [Microsoft Voorwaarden voor Online Diensten](https://www.microsoft.com/licensing/product-licensing/products) (OST). Volgens OST kunnen SQL Server met MABS alleen worden gebruikt als de database voor MABS.

4. Geef een locatie op voor de installatie van Microsoft Azure Backup serverbestanden en selecteer **Volgende.**

    ![Geef de locatie op voor de installatie van bestanden](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    De scratchlocatie is een vereiste voor back-up naar Azure. Zorg ervoor dat de scratchlocatie ten minste 5% van de gegevens is die zijn gepland om een back-up te maken naar de cloud. Voor schijfbeveiliging moeten afzonderlijke schijven worden geconfigureerd zodra de installatie is voltooid. Zie Gegevensopslag voorbereiden voor meer informatie [over opslaggroepen.](/system-center/dpm/plan-long-and-short-term-data-storage)

    Capaciteitsvereisten voor schijfopslag zijn voornamelijk afhankelijk van de grootte van de beveiligde gegevens, de dagelijkse herstelpuntgrootte, verwachte groeisnelheid van volumegegevens en bewaartermijndoelstellingen. U wordt aangeraden de schijfopslag twee keer zo groot te maken als de beveiligde gegevens. Hierbij wordt uitgegaan van een dagelijkse herstelpuntgrootte van 10% van de beveiligde gegevensgrootte en een bewaartermijn van tien dagen. Als u een goede schatting van de grootte wilt maken, bekijkt u [de DPM-Capacity Planner.](https://www.microsoft.com/download/details.aspx?id=54301) 

5. Geef een sterk wachtwoord op voor beperkte lokale gebruikersaccounts en selecteer **Volgende.**

    ![Geef een sterk wachtwoord op](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecteer of u een Microsoft Update *om* te controleren op updates en selecteer **Volgende.**

   > [!NOTE]
   > U wordt aangeraden Windows Update om te leiden naar Microsoft Update, die beveiliging en belangrijke updates biedt voor Windows en andere producten, zoals Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Update Opt-In](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Controleer de *Samenvatting van instellingen en* selecteer **Installeren.**

    ![Overzicht van instellingen](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. De installatie vindt in fasen plaats. In de eerste fase wordt de Microsoft Azure Recovery Services-agent geïnstalleerd op de server. De wizard controleert ook op internetverbinding. Als er een internetverbinding beschikbaar is, kunt u doorgaan met de installatie. Zo niet, dan moet u proxygegevens verstrekken om verbinding te maken met internet.

    De volgende stap is het configureren van Microsoft Azure Recovery Services-agent. Als onderdeel van de configuratie moet u uw kluisreferenties opgeven om de computer te registreren bij de Recovery Services-kluis. U geeft ook een wachtwoordzin op voor het versleutelen/ontsleutelen van de gegevens die tussen Azure en uw locatie worden verzonden. U kunt automatisch een wachtwoordzin genereren of uw eigen minimale wachtwoordzin van 16 tekens geven. Ga door met de wizard totdat de agent is geconfigureerd.

    ![Wizard Server registreren](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Zodra de registratie van de Microsoft Azure Backup-server is voltooid, gaat de wizard algemene installatie verder met de installatie en configuratie van SQL Server en Azure Backup Server onderdelen. Zodra de SQL Server is voltooid, worden de Azure Backup Server geïnstalleerd.

    ![Azure Backup Server setup-voortgang](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Wanneer de installatiestap is voltooid, zijn ook de bureaubladpictogrammen van het product gemaakt. Dubbelklik op het pictogram om het product te starten.

### <a name="add-backup-storage"></a>Back-upopslag toevoegen

De eerste back-upkopie wordt bewaard in de opslag die is gekoppeld aan de Azure Backup Server machine. Zie Opslaggroepen en schijfopslag configureren voor meer informatie over het toevoegen [van schijven.](./backup-mabs-add-storage.md)

> [!NOTE]
> U moet back-upopslag toevoegen, zelfs als u van plan bent om gegevens naar Azure te verzenden. In de huidige architectuur van Azure Backup Server bevat de Azure Backup-kluis de tweede kopie van de gegevens terwijl de lokale opslag de eerste (en verplichte) back-up bevat. 
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>De beveiligingsagent voor Data Protection Manager installeren en bijwerken

MABS gebruikt de System Center Data Protection Manager-beveiligingsagent. [Hier volgen de stappen voor](/system-center/dpm/deploy-dpm-protection-agent) het installeren van de beveiligingsagent op uw beveiligingsservers.

In de volgende secties wordt beschreven hoe u beveiligingsagents voor clientcomputers bij kunt werken.

1. Selecteer beheeragents in de Administrator-console van **de back-upserver.**  >  

2. Selecteer in het weergavevenster de clientcomputers waarvoor u de beveiligingsagent wilt bijwerken.

   > [!NOTE]
   > De **kolom Agentupdates** geeft aan wanneer een beveiligingsagentupdate beschikbaar is voor elke beveiligde computer. In het **deelvenster** Acties is de **actie Bijwerken** alleen beschikbaar wanneer een beveiligde computer is geselecteerd en updates beschikbaar zijn.
   >
   >

3. Als u bijgewerkte beveiligingsagents op de geselecteerde computers wilt installeren, selecteert u in **het deelvenster** Acties de **optie Bijwerken.**

4. Voor een clientcomputer die niet is verbonden met het netwerk, wordt in de kolom **Agentstatus** de status Bijwerken in behandeling weergeven totdat de computer is **verbonden met het netwerk.**

   Nadat een clientcomputer is verbonden met het netwerk, toont de kolom **AgentUpdates** voor de clientcomputer de status **Bijwerken.**

## <a name="move-mabs-to-a-new-server"></a>MABS verplaatsen naar een nieuwe server

Hier volgen de stappen als u MABS naar een nieuwe server wilt verplaatsen terwijl u de opslag behoudt. Dit kan alleen worden gedaan als alle gegevens zich op de Modern Backup Storage.

  > [!IMPORTANT]
  >
  > * De naam van de nieuwe server moet dezelfde naam hebben als de oorspronkelijke Azure Backup Server instantie. U kunt de naam van het nieuwe Azure Backup Server-exemplaar niet wijzigen als u de vorige opslaggroep en MABS Database (DPMDB) wilt gebruiken om herstelpunten te behouden.
  > * U moet een back-up van de MABS-database (DPMDB) hebben. U hebt deze nodig om de database te herstellen.

1. Selecteer in het weergavevenster de clientcomputers waarvoor u de beveiligingsagent wilt bijwerken.
2. Sluit de oorspronkelijke Azure Backup of offline.
3. Stel het computeraccount in Active Directory opnieuw in.
4. Installeer Server 2016 op een nieuwe computer en geef deze dezelfde computernaam als de oorspronkelijke Azure Backup server.
5. Word lid van het domein.
6. Installeer Azure Backup Server V3 of hoger (VERPLAATS MABS Storage-poolschijven van een oude server en importeer).
7. Herstel de DPMDB die u in stap 1 heeft gemaakt.
8. Koppel de opslag van de oorspronkelijke back-upserver aan de nieuwe server.
9. Herstel de DPMDB vanuit SQL.
10. Voer CMD (als beheerder) uit op de nieuwe server. Ga naar de Microsoft Azure Backup installatielocatie en bin-map

    Voorbeeld van pad: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Voer uit om verbinding te Azure Backup met de Azure Backup `DPMSYNC -SYNC`

    Als u nieuwe schijven aan **de** DPM-opslaggroep hebt toegevoegd in plaats van de oude schijven te verplaatsen, voer dan `DPMSYNC -Reallocatereplica` uit.

## <a name="network-connectivity"></a>Netwerkverbinding

Azure Backup Server vereist connectiviteit met de Azure Backup service om het product goed te laten werken. Als u wilt controleren of de machine verbinding heeft met Azure, gebruikt u de ```Get-DPMCloudConnection``` cmdlet in Azure Backup Server PowerShell-console. Als de uitvoer van de cmdlet TRUE is, bestaat er connectiviteit, anders is er geen connectiviteit.

Tegelijkertijd moet het Azure-abonnement een goede status hebben. Als u de status van uw abonnement wilt weten en wilt beheren, meld u zich aan bij de [abonnementsportal.](https://account.windowsazure.com/Subscriptions)

Zodra u de status van de Azure-connectiviteit en het Azure-abonnement weet, kunt u de onderstaande tabel gebruiken om de impact op de aangeboden back-up-/herstelfunctionaliteit te ontdekken.

| Connectiviteitstoestand | Azure-abonnement | Back-up naar Azure | Back-up naar schijf | Herstellen vanuit Azure | Herstellen uit een schijf |
| --- | --- | --- | --- | --- | --- |
| Verbonden |Actief |Toegestaan |Toegestaan |Toegestaan |Toegestaan |
| Verbonden |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbonden |Deprovisioned |Gestopt |Gestopt |Gestopt en Azure-herstelpunten verwijderd |Gestopt |
| Verbindingsverbinding > 15 dagen verloren |Actief |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbindingsverbinding > 15 dagen verloren |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbindingsverbinding > 15 dagen verloren |Deprovisioned |Gestopt |Gestopt |Gestopt en Azure-herstelpunten verwijderd |Gestopt |

### <a name="recovering-from-loss-of-connectivity"></a>Herstellen van verlies van connectiviteit

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

Als u ExpressRoute Microsoft-peering gebruikt, selecteert u de volgende services/regio's:

* Azure Active Directory (12076:5060)
* Microsoft Azure regio (op basis van de locatie van uw Recovery Services-kluis)
* Azure Storage (op basis van de locatie van uw Recovery Services-kluis)

Ga naar [ExpressRoute-routeringsvereisten voor meer informatie.](../expressroute/expressroute-routing.md)

Zodra de verbinding met Azure is hersteld naar de Azure Backup Server machine, worden de bewerkingen die kunnen worden uitgevoerd, bepaald door de status van het Azure-abonnement. De bovenstaande tabel heeft details over de bewerkingen die zijn toegestaan zodra de machine verbonden is.

### <a name="handling-subscription-states"></a>Abonnements states verwerken

Het is mogelijk om een Azure-abonnement van *de* status Verlopen of *Deprovisioned* over te zetten naar *de status* Actief. Dit heeft echter enkele gevolgen voor het gedrag van het product wanneer de status niet Actief *is:*

* Een *abonnement dat is uit deprovisioning* is verwijderd, verliest de functionaliteit voor de periode dat het wordt uitprovisioned. Bij het *in-/uitschakelen* van Actief wordt de productfunctionaliteit van back-up/herstel hersteld. De back-upgegevens op de lokale schijf kunnen ook worden opgehaald als deze zijn bewaard met een voldoende lange bewaarperiode. De back-upgegevens in Azure gaan echter onherstelbaar verloren zodra het abonnement de *status Deprovisioned heeft.*
* Een *verlopen abonnement* verliest alleen functionaliteit voor totdat het weer Actief *is* gemaakt. Back-ups die zijn gepland voor de periode dat het abonnement is *verlopen,* worden niet uitgevoerd.

## <a name="upgrade-mabs"></a>MABS upgraden

Gebruik de volgende procedures om MABS bij te werken.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgraden van MABS V2 naar V3

> [!NOTE]
>
> MABS V2 is geen vereiste voor het installeren van MABS V3. U kunt echter alleen vanuit MABS V2 upgraden naar MABS V3.

Gebruik de volgende stappen om MABS bij te upgraden:

1. Als u een upgrade wilt uitvoeren van MABS V2 naar MABS V3, moet u indien nodig uw besturingssysteem upgraden naar Windows Server 2016 of Windows Server 2019.

2. Upgrade uw server. De stappen zijn vergelijkbaar met [de installatie](#install-and-upgrade-azure-backup-server). Voor SQL-instellingen krijgt u echter een optie om uw SQL-exemplaar te upgraden naar SQL 2017 of om uw eigen exemplaar van SQL Server 2017 te gebruiken.

   > [!NOTE]
   >
   > Sluit niet af terwijl uw SQL-exemplaar wordt bijgewerkt. Als u afsluitent, wordt het SQL-rapportage-exemplaar verwijderd, waardoor een poging om MABS opnieuw te upgraden mislukt.

   > [!IMPORTANT]
   >
   >  Als onderdeel van de SQL 2017-upgrade maken we een back-up van de SQL-versleutelingssleutels en verwijderen we de Reporting Services. Na de upgrade van de SQL-server wordt reporting service(14.0.6827.4788) geïnstalleerd & versleutelingssleutels worden hersteld.
   >
   > Als u SQL 2017 handmatig configureert, raadpleegt u *de sectie SSRS-configuratie met SQL 2017* onder Installatie-instructies.

3. Werk de beveiligingsagents op de beveiligde servers bij.
4. Back-ups moeten worden voortgezet zonder dat u uw productieservers opnieuw hoeft op te starten.
5. U kunt nu beginnen met het beveiligen van uw gegevens. Als u een upgrade naar Modern Backup Storage, kunt u tijdens het beveiligen ook de volumes kiezen waarin u de back-ups wilt opslaan en controleren op onder de inrichten ruimte. [Meer informatie](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Problemen oplossen

Als Microsoft Azure Backup-server mislukt met fouten tijdens de installatiefase (of back-up of herstel), raadpleegt u dit document met [foutcodes](https://support.microsoft.com/kb/3041338)  voor meer informatie.
U kunt ook verwijzen [naar Azure Backup veelgestelde vragen](backup-azure-backup-faq.yml)

## <a name="next-steps"></a>Volgende stappen

U vindt hier gedetailleerde informatie over het [voorbereiden van uw omgeving voor DPM.](/system-center/dpm/prepare-environment-for-dpm) Het bevat ook informatie over ondersteunde configuraties Azure Backup Server kunnen worden geïmplementeerd en gebruikt. U kunt een reeks [PowerShell-cmdlets gebruiken voor](/powershell/module/dataprotectionmanager/) het uitvoeren van verschillende bewerkingen.

U kunt deze artikelen gebruiken om meer inzicht te krijgen in de beveiliging van workloads met behulp Microsoft Azure Backup server.

* [SQL Server back-up maken](backup-azure-backup-sql.md)
* [Back-up van SharePoint-server](backup-azure-backup-sharepoint.md)
* [Alternatieve serverback-up](backup-azure-alternate-dpm-server.md)
