---
title: 'Zelfstudie: back-ups maken van SAP HANA-databases in virtuele Azure-machines'
description: In deze zelfstudie ontdekt u hoe u een back-up naar een Azure Backup Recovery Services-kluis maakt van SAP HANA-databases die op een virtuele Azure-machine worden uitgevoerd.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 00109de349c1fdfdbaff9de30d18f64d8b986a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587641"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Zelfstudie: Een back-up maken van SAP HANA-databases in een Azure-VM

In deze zelfstudie ziet u hoe u een back-up naar een Azure Backup Recovery Services-kluis maakt van SAP HANA-databases die op een virtuele Azure-machine worden uitgevoerd. In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Databases detecteren
> * Back-ups configureren

[Hier](sap-hana-backup-support-matrix.md#scenario-support) vindt u alle scenario's die we momenteel ondersteunen.

>[!NOTE]
>Per 1 augustus 2020 is SAP HANA backup for RHEL (7.4, 7.6, 7.7 en 8.1) algemeen beschikbaar.

## <a name="prerequisites"></a>Vereisten

Doe het volgende voordat u back-ups gaat configureren:

* Identificeer of maak een [Recovery Services-kluis](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) in dezelfde regio en hetzelfde abonnement als de VM waarop SAP HANA wordt uitgevoerd.
* Sta connectiviteit van de virtuele machine naar internet toe, zodat de virtuele machine Azure kan bereiken. Dit wordt beschreven in de onderstaande procedure [De netwerkverbinding instellen](#set-up-network-connectivity).
* Zorg ervoor dat de gecombineerde lengte van de SAP HANA Server VM-naam en de Resource Group-naam niet langer is dan 84 tekens voor Azure Resource Manager (ARM_ VM's (en 77 tekens voor klassieke VM's). Deze beperking geldt omdat sommige tekens zijn gereserveerd door de service.
* Er moet een sleutel in de **hdbuserstore** bestaan waarmee aan de volgende criteria wordt voldaan:
  * De sleutel moet aanwezig zijn in de standaard-**hdbuserstore**. De standaardwaarde is het `<sid>adm`-account waaronder SAP HANA is geïnstalleerd.
  * Voor MDC moet de sleutel verwijzen naar de SQL-poort van **NAMESERVER**. In het geval van SDC moet worden verwezen naar de SQL-poort van **INDEXSERVER**
  * De sleutel moet over referenties beschikken om gebruikers toe te voegen en te verwijderen
  * Houd er rekening mee dat deze sleutel kan worden verwijderd nadat het pre-registratiescript is uitgevoerd
* Voer als de hoofdgebruiker het SAP HANA-back-upconfiguratiescript uit (script vóór registratie) op de virtuele machine waarop HANA is geïnstalleerd. [Met behulp van dit script](https://aka.ms/scriptforpermsonhana) wordt het HANA-systeem voorbereid op de back-up. Raadpleeg de sectie [Wat doet het script vóór registratie](#what-the-pre-registration-script-does) voor meer informatie over het script vóór registratie.
* Als uw HANA-installatie Private Endpoints gebruikt, voert u het [pre-registratiescript](https://aka.ms/scriptforpermsonhana) uit met de parameter *-sn* of *- skip-network-checks*.

>[!NOTE]
>Het script voor registratie vooraf installeert de **compat-unixODBC234** voor SAP HANA-workloads die worden uitgevoerd op RHEL (7.4, 7.6 en 7.7) en **unixODBC** voor RHEL 8.1. [Dit pakket bevindt zich in de opslagplaats RHEL for SAP HANA (voor RHEL 7 Server) Update Services for SAP Solutions (RPM's)](https://access.redhat.com/solutions/5094721).  Voor een Azure Marketplace RHEL-installatie kopie is de opslagplaats **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>De netwerkverbinding instellen

Voor alle bewerkingen voor een SAP HANA-database die wordt uitgevoerd op een virtuele Azure-machine, is connectiviteit met de Azure Backup-service, Azure Storage en Azure Active Directory vereist. Dit kan worden bereikt door gebruik te maken van privé-eindpunten of door toegang te verlenen tot de vereiste openbare IP-adressen of FQDN's. Zonder de juiste connectiviteit met de vereiste Azure-services kunnen bewerkingen zoals het detecteren van databases, het configureren van back-ups, het uitvoeren van back-ups en het herstellen van gegevens mislukken.

De volgende tabel bevat een lijst van de verschillende alternatieven die u kunt gebruiken om connectiviteit tot stand te brengen:

| **Optie**                        | **Voordelen**                                               | **Nadelen**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Privé-eindpunten                 | Maakt back-ups via privé-eindpunten in het virtuele netwerk mogelijk  <br><br>   Biedt uitgebreide beheermogelijkheden aan de zijde van het netwerk en de kluis | U betaalt standaard [kosten](https://azure.microsoft.com/pricing/details/private-link/) voor privé-eindpunten |
| NSG-servicetags                  | Eenvoudiger om te beheren omdat veranderingen in het bereik automatisch worden samengevoegd   <br><br>   Geen extra kosten | Kan alleen worden gebruikt met NSG's  <br><br>    Biedt toegang tot de gehele service |
| Azure Firewall FQDN-tags          | Eenvoudiger om te beheren omdat de vereiste FQDN's automatisch worden beheerd | Kan alleen worden gebruikt met Azure Firewall                         |
| Toegang tot FQDN's/IP-adressen van de service toestaan | Geen extra kosten   <br><br>  Werkt met alle netwerkbeveiligingsapparaten en firewalls | Er is mogelijk toegang tot een groot aantal IP-adressen of FQDN's vereist   |
| Een HTTP-proxy gebruiken                 | Eén internettoegangspunt voor virtuele machines                       | Extra kosten voor het uitvoeren van een virtuele machine met de proxysoftware         |

Meer informatie over het gebruik van deze opties vindt u hieronder:

### <a name="private-endpoints"></a>Privé-eindpunten

Met privé-eindpunten kunt u veilig verbinding maken tussen servers in een virtueel netwerk en uw Recovery Services-kluis. Het privé eindpunt gebruikt een IP-adres van de VNET-adresruimte voor uw kluis. Het netwerkverkeer tussen uw resources in het virtuele netwerk en de kluis loopt via het virtuele netwerk en een privé-koppeling in het Microsoft-backbonenetwerk. Dit voorkomt blootstelling aan het openbare internet. Meer informatie over privé-eindpunten voor Azure Backup vindt u [hier](./private-endpoints.md).

### <a name="nsg-tags"></a>NSG-tags

Als u netwerkbeveiligingsgroepen (NSG's) gebruikt, gebruikt u de servicetag *AzureBackup* om uitgaande toegang tot Azure Backup toe te staan. Naast de Azure Backup-tag moet u ook connectiviteit voor verificatie en gegevensoverdracht toestaan met behulp van [NSG-regels](../virtual-network/network-security-groups-overview.md#service-tags) voor Azure AD (*AzureActiveDirectory*) en Azure Storage (*Storage*). In de volgende stappen wordt het proces voor het maken van een regel voor de Azure Backup-tag beschreven:

1. In **Alle services** gaat u naar **Netwerkbeveiligingsgroepen** en selecteert u de netwerkbeveiligingsgroep.

1. Selecteer de optie **Uitgaande beveiligingsregels** onder **Instellingen**.

1. Selecteer **Toevoegen**. Voer alle vereiste details in voor het maken van een nieuwe regel, zoals beschreven in de [instellingen voor beveiligingsregels](../virtual-network/manage-network-security-group.md#security-rule-settings). Controleer of de optie **Doel** is ingesteld op *Servicetag* en **Doelservicetag** is ingesteld op *AzureBackup*.

1. Selecteer **Toevoegen** om de zojuist gemaakt uitgaande beveiligingsregel op te slaan.

U kunt op vergelijkbare wijze ook uitgaande [NSG-beveiligingsregels](../virtual-network/network-security-groups-overview.md#service-tags) maken voor Azure Storage en Azure AD. Zie [dit artikel](../virtual-network/service-tags-overview.md) voor meer informatie over servicetags.

### <a name="azure-firewall-tags"></a>Azure Firewall-tags

Als u Azure Firewall gebruikt, maakt u een toepassingsregel met behulp van de [Azure Firewall FQDN-tag](../firewall/fqdn-tags.md) *AzureBackup*. Hiermee wordt alle uitgaande toegang tot Azure Backup toegestaan.

### <a name="allow-access-to-service-ip-ranges"></a>Toegang tot IP-bereiken van de service toestaan

Als u ervoor kiest om toegang tot IP-adressen van de service toe te staan, raadpleegt u de IP-bereiken in het JSON-bestand dat [hier](https://www.microsoft.com/download/confirmation.aspx?id=56519) beschikbaar is. U moet toegang tot IP-adressen die overeenkomen met Azure Backup, Azure Storage en Azure Active Directory toestaan.

### <a name="allow-access-to-service-fqdns"></a>Toegang tot FQDN's van de service toestaan

U kunt ook de volgende FQDN's gebruiken om toegang te verlenen tot de vereiste services van uw servers:

| Service    | Domeinnamen waarvoor toegang nodig is                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Toegang tot FQDN's toestaan onder de secties 56 en 59 volgens [dit artikel](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Een HTTP-proxyserver gebruiken om verkeer te routeren

Wanneer u een back-up maakt van een SAP HANA-database die wordt uitgevoerd op een virtuele Azure-machine, gebruikt de back-upextensie op de virtuele machine de HTTPS-API's voor het verzenden van beheeropdrachten naar Azure Backup en gegevens naar Azure Storage. De back-upextensie maakt ook gebruik van Azure AD voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. Gebruik de lijst met IP-adressen en FQDN's die hierboven worden genoemd om toegang tot de vereiste services toe te staan. Geverifieerde proxyservers worden niet ondersteund.

## <a name="understanding-backup-and-restore-throughput-performance"></a>Meer informatie over de prestaties van back-up en herstel

De back-ups (logboek en niet-logboek) in SAP HANA Azure-Vm's die via Backint worden meegeleverd, zijn stromen naar Azure Recovery Services-kluizen. het is dus belang rijk om deze streaming-methodologie te begrijpen.

Het Backint-onderdeel van HANA biedt de ' sluizen ' (een pipe om te lezen van en een pipe om naar te schrijven), verbonden met onderliggende schijven waar database bestanden zich bevinden, die vervolgens worden gelezen door de Azure Backup-service en worden getransporteerd naar Azure Recovery Services kluis. De Azure Backup-service voert ook een controlesom uit voor het valideren van de stromen, naast de systeem eigen validatie controles van backint. Deze validaties zorgen ervoor dat de gegevens die aanwezig zijn in azure Recovery Services kluis inderdaad betrouwbaar zijn en kunnen worden hersteld.

Aangezien de stromen voornamelijk worden verwerkt met schijven, moet u inzicht hebben in de schijf prestaties om de back-up-en herstel prestaties te meten. Raadpleeg [dit artikel](../virtual-machines/disks-performance.md) voor uitgebreide informatie over de door Voer van schijven en prestaties in azure vm's. Deze zijn ook van toepassing op back-up-en herstel prestaties.

**De Azure backup-service probeert Maxi maal ~ 420 Mbps te behalen voor back-ups van niet-Logboeken (zoals volledig, differentieel en incrementeel) en tot 100 Mbps voor logboek back-ups voor Hana**. Zoals hierboven vermeld, zijn dit niet gegarandeerde snelheden en zijn ze afhankelijk van de volgende factoren:

* Maximale door Voer van schijf in cache van de virtuele machine
* Onderliggend schijf type en de door Voer
* Het aantal processen dat op hetzelfde moment wordt geprobeerd te lezen en te schrijven naar dezelfde schijf.

> [!IMPORTANT]
> In kleinere virtuele machines, waarbij de door Voer van niet-cache geheugen bijna bijna gelijk is aan of kleiner is dan 400 MBps, is het mogelijk dat de gehele schijf-IOPS wordt verbruikt door de back-upservice. Dit kan van invloed zijn op de bewerkingen van SAP HANA die betrekking hebben op lezen/schrijven van de schijven. Als u in dat geval het verbruik van de back-upservice beperkt of beperkt tot de maximum limiet, kunt u naar de volgende sectie verwijzen.

### <a name="limiting-backup-throughput-performance"></a>Prestaties van de back-updoorvoer beperken

Als u het gebruik van de back-upservice-schijf wilt beperken tot een maximum waarde, voert u de volgende stappen uit.

1. Ga naar de map opt/msawb/bin
2. Maak een nieuw JSON-bestand met de naam ' ExtensionSettingOverrides.JSop '
3. Voeg als volgt een sleutel-waardepaar toe aan het JSON-bestand:

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. Wijzig de machtigingen en het eigendom van het bestand als volgt:
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. U hoeft geen service opnieuw op te starten. De Azure Backup-service zal proberen de doorvoer prestaties te beperken zoals vermeld in dit bestand.

## <a name="what-the-pre-registration-script-does"></a>Wat doet het script vóór registratie

Door het script vóór registratie uit te voeren, worden de volgende functies uitgevoerd:

* Afhankelijk van uw Linux-distributie installeert het script alle eventueel benodigde pakketten die vereist zijn door de Azure Backup-agent of werkt deze bij.
* De uitgaande netwerkverbinding met Azure Backup-servers en afhankelijke services zoals Azure Active Directory en Azure Storage wordt gecontroleerd.
* Er worden logboeken vastgelegd in uw HANA-systeem met behulp van de gebruikerssleutel die wordt vermeld als onderdeel van de [vereisten](#prerequisites). De gebruikerssleutel wordt gebruikt om een back-upgebruiker (AZUREWLBACKUPHANAUSER) in het HANA-systeem te maken, en **de gebruikerssleutel kan worden verwijderd nadat het script vóór registratie is uitgevoerd**.
* Deze vereiste rollen en machtigingen worden aan AZUREWLBACKUPHANAUSER toegewezen:
  * Voor MDC: DATABASE ADMIN en BACKUP ADMIN (vanaf HANA 2.0 SPS05 en hoger): om nieuwe databases te maken tijdens herstel.
  * Voor SDC: BACK-UPBEHEERDER: voor het maken van nieuwe databases tijdens herstel.
  * CATALOG READ: om de back-upcatalogus te lezen.
  * SAP_INTERNAL_HANA_SUPPORT: voor toegang tot een aantal privétabellen. Alleen vereist voor dit SDC-en MDC-versies beneden HANA 2.0 SPS04 Rev 46. Dit is niet vereist voor HANA 2.0 SPS04 Rev 46 en hoger, omdat we nu de vereiste gegevens uit openbare tabellen ophalen met de oplossing van het HANA-team.
* Het script voegt een sleutel toe aan **hdbuserstore** voor AZUREWLBACKUPHANAUSER, zodat de HANA-back-upinvoegtoepassing alle bewerkingen (databasequery's, herstelbewerkingen, back-ups configureren en uitvoeren) kan verwerken.

>[!NOTE]
> U kunt de gebruikerssleutel die wordt vermeld als onderdeel van de [vereisten](#prerequisites) expliciet als een parameter aan het script vóór registratie doorgeven: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Gebruik de opdracht `bash msawb-plugin-config-com-sap-hana.sh --help` voor meer informatie over de andere parameters die door het script worden geaccepteerd

Als u het maken van de sleutel wilt bevestigen, voert u de HDBSQL-opdracht uit op de HANA-machine met SIDADM-referenties:

```hdbsql
hdbuserstore list
```

In de uitvoer van de opdracht moet de sleutel {SID}{DBNAME} worden weergegeven en moet de gebruiker als AZUREWLBACKUPHANAUSER worden weergegeven.

>[!NOTE]
> Controleer of u over een unieke set SSFS-bestanden beschikt onder `/usr/sap/{SID}/home/.hdb/`. Als het goed is, bevat dit pad maar één map.

Hier volgt een overzicht van de stappen die nodig zijn voor het volt ooien van de uitvoering van het script dat vooraf wordt geregistreerd.

|Kopers  |Van  |Wat u moet uitvoeren  |Opmerkingen  |
|---------|---------|---------|---------|
|```<sid>```adm (OS)     |  HANA-BESTURINGS SYSTEEM       |   Lees de zelf studie en down load het script dat vooraf wordt geregistreerd      |   Lees de [bovenstaande vereisten hierboven](#prerequisites) voor het [downloaden van het](https://aka.ms/scriptforpermsonhana) script voor vooraf registratie  |
|```<sid>```adm (OS) en systeem gebruiker (HANA)    |      HANA-BESTURINGS SYSTEEM   |   Opdracht hdbuserstore set uitvoeren      |   bijvoorbeeld hdbuserstore set SYSTEM hostname>:3 ```<Instance#>``` 13 systeem ```<password>``` **Opmerking:**  zorg ervoor dat u hostname gebruikt in plaats van IP-adres of FQDN      |
|```<sid>```adm (OS)    |   HANA-BESTURINGS SYSTEEM      |  Opdracht hdbuserstore List uitvoeren       |   Controleer of het resultaat het standaard archief bevat, zoals hieronder wordt beschreven: ```KEY SYSTEM  ENV : <hostname>:3<Instance#>13  USER: SYSTEM```      |
|Hoofdmap (OS)     |   HANA-BESTURINGS SYSTEEM        |    Azure Backup HANA vooraf-registratie script uitvoeren      |    ```./msawb-plugin-config-com-sap-hana.sh -a --sid <SID> -n <Instance#> --system-key SYSTEM```     |
|```<sid>```adm (OS)    |  HANA-BESTURINGS SYSTEEM       |   Opdracht hdbuserstore List uitvoeren      |    Controleer of het resultaat nieuwe regels bevat, zoals hieronder:  ```KEY AZUREWLBACKUPHANAUSER  ENV : localhost: 3<Instance#>13   USER: AZUREWLBACKUPHANAUSER```     |

Nadat het script voor de voorafgaande registratie is uitgevoerd en geverifieerd, kunt u [de connectiviteits vereisten](#set-up-network-connectivity) vervolgens controleren en vervolgens een [back-up](#discover-the-databases) van de Recovery Services-kluis configureren

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Service-kluis is een entiteit waarin de back-ups en herstelpunten worden opgeslagen die in de loop van de tijd zijn gemaakt. De Recovery Service-kluis bevat ook de beleidsregels voor back-up die aan de beveiligde virtuele machines zijn gekoppeld.

Een Recovery Services-kluis maken:

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

2. Selecteer in het menu links **Alle services**.

   ![Alle services selecteren](./media/tutorial-backup-sap-hana-db/all-services.png)

3. In het dialoogvenster **Alle services** voert u **Recovery Services** in. De lijst met resources wordt gefilterd op basis van uw invoer. In de lijst met resources selecteert u **Recovery Service-kluizen**.

   ![Recovery Services-kluizen selecteren](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Op het dashboard van de **Recovery Services**-kluizen selecteert u **Toevoegen**.

   ![Recovery Services-kluis toevoegen](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Het dialoogvenster **Recovery Service-kluis** wordt geopend. Waarden opgeven voor **Naam, Abonnement, Resourcegroep** en **Locatie**

   ![Een Recovery Services-kluis maken](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Naam**: De naam wordt gebruikt om de Recovery Service-kluis te identificeren en deze moet uniek zijn in het Azure-abonnement. Geef een naam op van minimaal 2 en maximaal 50 tekens. De naam moet beginnen met een letter en mag alleen uit letters, cijfers en afbreekstreepjes bestaan. Voor deze zelfstudie hebben we de naam **SAPHanaVault** gebruikt.
   * **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van maar één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere mogelijkheden als uw werk- of schoolaccount is gekoppeld aan meerdere Azure-abonnementen. Hier hebben we het **SAP HANA Solution Lab-abonnement** gebruikt.
   * **Resourcegroep**: Gebruik een bestaande resourcegroep of maak een nieuwe. Hier hebben we **SAPHANADemo** gebruikt.<br>
   Als u de lijst met beschikbare resourcegroepen in uw abonnement wilt weergeven, selecteert u **Bestaande gebruiken** en vervolgens selecteert u een resource uit de vervolgkeuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuwe maken** en voert u de naam in. Zie [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md) voor meer informatie over resourcegroepen.
   * **Locatie**: Selecteer de geografische regio voor de kluis. De kluis moet zich in dezelfde regio bevinden als de virtuele machine waarop SAP HANA wordt uitgevoerd. We hebben **East US 2** gebruikt.

5. Selecteer **Controleren + maken**.

   ![Controleren + maken selecteren](./media/tutorial-backup-sap-hana-db/review-create.png)

De Recovery Service-kluis wordt nu gemaakt.

## <a name="discover-the-databases"></a>De databases detecteren

1. In de kluis bij **Aan de slag** selecteert u **Back-up**. In **Waar wordt uw werkbelasting uitgevoerd?** selecteert u **SAP HANA in Azure-VM**.
2. Selecteer **Detectie starten**. Hiermee start u de detectie van niet-beveiligde virtuele Linux-machines in de regio van de kluis. U zult de virtuele Azure-machine die u wilt beveiligen zien.
3. In **Virtuele machines selecteren** selecteert u de koppeling om het script dat machtigingen biedt voor de Azure Backup-service te downloaden, voor toegang tot de virtuele SAP HANA-machines voor de detectie van databases.
4. Voer het script uit op de virtuele machine waarop de SAP HANA-database(s) wordt/worden gehost waarvan u een back-up wilt maken.
5. Nadat het script op de virtuele machine is uitgevoerd, selecteert u de virtuele machine in **Virtuele machines selecteren**. Selecteer vervolgens **DB's Detecteren**.
6. Azure Backup detecteert alle SAP HANA databases op de virtuele machine. Tijdens het detecteren registreert Azure Backup de virtuele machine met de kluis en wordt een extensie op de virtuele machine geïnstalleerd. Er is geen agent geïnstalleerd op de database.

   ![De databases detecteren](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Back-up configureren

Nu de databases waarvan we een back-up willen maken zijn gedetecteerd, gaan we back-up inschakelen.

1. Selecteer **Backup Configureren**.

   ![Back-up configureren](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. In **Items selecteren waarvan u een back-up wilt maken** selecteert u een of meer databases die u wilt beveiligen en vervolgens selecteert u **OK**.

   ![Items selecteren waarvan u een back-up wilt maken](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. In **Back-upbeleid > Back-upbeleid kiezen** maakt u een nieuw back-upbeleid voor de database(s), in overeenstemming met de instructies in de volgende sectie.

   ![Back-upbeleid kiezen](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Nadat u het beleid heb gemaakt, selecteert u in het **menu Back-up** **Back-up inschakelen**.

   ![Selecteer Back-up inschakelen](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Volg de voortgang van de back-upconfiguratie in het **Systeemvak** van de portal.

## <a name="creating-a-backup-policy"></a>Een back-upbeleid maken

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

Geef als volgt de beleidsinstellingen op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op. Voer in dit geval **SAPHANA** in.

   ![Een naam voor het nieuwe beleid invoeren](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. In **Beleid voor volledige back-ups** selecteert u een **Back-upfrequentie**. U kunt **Dagelijks** of **Wekelijks** kiezen. Voor deze zelfstudie hebben we de **dagelijkse** back-up gekozen.

   ![Een back-upfrequentie selecteren](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. In **Bewaartermijn** configureert u de bewaarinstellingen voor de volledige back-up.
   * Standaard zijn alle opties geselecteerd. Schakel alle bewaartermijnlimieten uit die u niet wilt gebruiken en stel de limieten in die u wilt gebruiken.
   * De minimale bewaarperiode voor elk type back-up (volledig/differentieel/logboek) is zeven dagen.
   * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
   * De back-up voor een specifieke dag wordt getagd en bewaard op basis van de wekelijkse bewaarperiode en uw instellingen.
   * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.
4. In het menu voor het **beleid voor een volledige back-up** selecteert u **OK** om de instellingen te accepteren.
5. Selecteer vervolgens **Differentiële back-up** om een beleid voor differentiële back-ups toe te voegen.
6. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen. We hebben een differentiële back-up ingeschakeld op elke **zondag** om **2.00 uur**. Deze back-up wordt gedurende **30 dagen** bewaard.

   ![Beleid voor differentiële back-ups](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >U kunt kiezen voor een differentieel of een incrementele dagelijkse back-up, maar niet voor beide.

7. In **Beleid voor een incrementele back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.
    * U kunt maximaal één incrementele back-up per dag activeren.
    * Incrementele back-ups kunnen maximaal 180 dagen worden bewaard. Als dat voor u te kort is, moet u volledige back-ups gebruiken.

    ![Beleid voor incrementele back-ups](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.
9. Selecteer **Logboekback-up** als u een back-upbeleid voor een transactielogboek wilt toevoegen;
   * standaard is **Logboekback-up** ingesteld op **Inschakelen**. Dit kan niet worden uitgeschakeld omdat SAP HANA alle logboekback-ups beheert.
   * We hebben **2 uur** als back-upschema ingesteld, en een bewaarperiode van **15 dagen**.

    ![Beleid voor logboekback-ups](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > De stroom van logboekback-ups begint pas nadat één volledige back-up is voltooid.
   >

10. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.
11. Zodra u het back-upbeleid hebt gedefinieerd, selecteert u **OK**.

U hebt nu back-up(s) voor uw SAP HANA-database(s) geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het uitvoeren van on-demand back-ups op SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Meer informatie over [het herstellen van SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines](sap-hana-db-restore.md)
* Meer informatie over [het beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met behulp van Azure Backup](sap-hana-db-manage.md)
* Meer informatie over [het oplossen van algemene problemen tijdens het maken van back-ups van SAP HANA-databases](backup-azure-sap-hana-database-troubleshoot.md)