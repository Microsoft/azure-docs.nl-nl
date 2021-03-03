---
title: Back-ups maken van een SAP HANA Data Base naar Azure met Azure Backup
description: In dit artikel vindt u informatie over het maken van een back-up van een SAP HANA Data Base naar Azure virtual machines met de Azure Backup-service.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: e7735c4240529cc6fc9bb6470934dd335d22aa77
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719607"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Back-ups maken van SAP HANA-databases in virtuele Azure-machines

SAP HANA-data bases zijn kritieke workloads waarvoor een laag herstel punt (RPO) en lange termijn retentie is vereist. U kunt back-ups maken van SAP HANA-data bases die worden uitgevoerd op Azure virtual machines (Vm's) met behulp van [Azure backup](backup-overview.md).

In dit artikel wordt beschreven hoe u een back-up maakt van SAP HANA-data bases die worden uitgevoerd op virtuele Azure-machines naar een Azure Backup Recovery Services kluis.

In dit artikel leert u het volgende:
> [!div class="checklist"]
>
> * Een kluis maken en configureren
> * Databases detecteren
> * Back-ups configureren
> * Een back-uptaak op aanvraag uitvoeren

>[!NOTE]
>Per 1 augustus 2020 is SAP HANA backup for RHEL (7.4, 7.6, 7.7 en 8.1) algemeen beschikbaar.

>[!NOTE]
>**Voorlopig verwijderen voor SQL Server in azure VM en voorlopig verwijderen voor SAP Hana in azure VM-workloads** is nu beschikbaar als preview-versie.<br>
>Als u zich wilt aanmelden voor de preview, kunt u een e-mail sturen naar ons [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Vereisten

Raadpleeg de [vereisten](tutorial-backup-sap-hana-db.md#prerequisites) en de [betekenis van het script dat voorafgaat](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) aan het registratie gedeelte voor het instellen van de Data Base voor back-up.

### <a name="establish-network-connectivity"></a>Netwerkverbinding tot stand brengen

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

#### <a name="private-endpoints"></a>Privé-eindpunten

Met privé-eindpunten kunt u veilig verbinding maken tussen servers in een virtueel netwerk en uw Recovery Services-kluis. Het privé eindpunt gebruikt een IP-adres van de VNET-adresruimte voor uw kluis. Het netwerkverkeer tussen uw resources in het virtuele netwerk en de kluis loopt via het virtuele netwerk en een privé-koppeling in het Microsoft-backbonenetwerk. Dit voorkomt blootstelling aan het openbare internet. Meer informatie over privé-eindpunten voor Azure Backup vindt u [hier](./private-endpoints.md).

#### <a name="nsg-tags"></a>NSG-tags

Als u netwerkbeveiligingsgroepen (NSG's) gebruikt, gebruikt u de servicetag *AzureBackup* om uitgaande toegang tot Azure Backup toe te staan. Naast de Azure Backup-tag moet u ook connectiviteit voor verificatie en gegevensoverdracht toestaan met behulp van [NSG-regels](../virtual-network/network-security-groups-overview.md#service-tags) voor Azure AD (*AzureActiveDirectory*) en Azure Storage (*Storage*).  In de volgende stappen wordt het proces voor het maken van een regel voor de Azure Backup-tag beschreven:

1. In **Alle services** gaat u naar **Netwerkbeveiligingsgroepen** en selecteert u de netwerkbeveiligingsgroep.

1. Selecteer de optie **Uitgaande beveiligingsregels** onder **Instellingen**.

1. Selecteer **Toevoegen**. Voer alle vereiste details in voor het maken van een nieuwe regel, zoals beschreven in de [instellingen voor beveiligingsregels](../virtual-network/manage-network-security-group.md#security-rule-settings). Controleer of de optie **Doel** is ingesteld op *Servicetag* en **Doelservicetag** is ingesteld op *AzureBackup*.

1. Selecteer **Toevoegen** om de zojuist gemaakt uitgaande beveiligingsregel op te slaan.

U kunt op vergelijkbare wijze ook uitgaande NSG-beveiligingsregels maken voor Azure Storage en Azure AD. Zie [dit artikel](../virtual-network/service-tags-overview.md) voor meer informatie over servicetags.

#### <a name="azure-firewall-tags"></a>Azure Firewall-tags

Als u Azure Firewall gebruikt, maakt u een toepassingsregel met behulp van de [Azure Firewall FQDN-tag](../firewall/fqdn-tags.md) *AzureBackup*. Hiermee wordt alle uitgaande toegang tot Azure Backup toegestaan.

#### <a name="allow-access-to-service-ip-ranges"></a>Toegang tot IP-bereiken van de service toestaan

Als u ervoor kiest om toegang tot IP-adressen van de service toe te staan, raadpleegt u de IP-bereiken in het JSON-bestand dat [hier](https://www.microsoft.com/download/confirmation.aspx?id=56519) beschikbaar is. U moet toegang tot IP-adressen die overeenkomen met Azure Backup, Azure Storage en Azure Active Directory toestaan.

#### <a name="allow-access-to-service-fqdns"></a>Toegang tot FQDN's van de service toestaan

U kunt ook de volgende FQDN's gebruiken om toegang te verlenen tot de vereiste services van uw servers:

| Service    | Domeinnamen waarvoor toegang nodig is                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Toegang tot FQDN's toestaan onder de secties 56 en 59 volgens [dit artikel](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Een HTTP-proxyserver gebruiken om verkeer te routeren

Wanneer u een back-up maakt van een SAP HANA-database die wordt uitgevoerd op een virtuele Azure-machine, gebruikt de back-upextensie op de virtuele machine de HTTPS-API's voor het verzenden van beheeropdrachten naar Azure Backup en gegevens naar Azure Storage. De back-upextensie maakt ook gebruik van Azure AD voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. Gebruik de lijst met IP-adressen en FQDN's die hierboven worden genoemd om toegang tot de vereiste services toe te staan. Geverifieerde proxyservers worden niet ondersteund.

> [!NOTE]
> Er is geen proxy ondersteuning op service niveau. Dat wil zeggen dat verkeer via de proxy van slechts enkele of geselecteerde services (Azure backup services) niet wordt ondersteund. De volledige gegevens of het verkeer kan worden gerouteerd door een proxy of niet.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>De databases detecteren

1. In de kluis bij **Aan de slag** selecteert u **Back-up**. In **Waar wordt uw werkbelasting uitgevoerd?** selecteert u **SAP HANA in Azure-VM**.
2. Selecteer **Detectie starten**. Hiermee start u de detectie van niet-beveiligde virtuele Linux-machines in de regio van de kluis.

   * Na detectie worden onbeveiligde Vm's weer gegeven in de portal, vermeld op naam en resource groep.
   * Als een virtuele machine niet naar verwachting wordt vermeld, controleert u of er al een back-up is gemaakt in een kluis.
   * Meerdere virtuele machines kunnen dezelfde naam hebben, maar ze behoren tot verschillende resource groepen.

3. In **Virtuele machines selecteren** selecteert u de koppeling om het script dat machtigingen biedt voor de Azure Backup-service te downloaden, voor toegang tot de virtuele SAP HANA-machines voor de detectie van databases.
4. Voer het script uit op elke VM die als host fungeert voor SAP HANA-data bases waarvan u een back-up wilt maken.
5. Nadat u het script op de virtuele machines hebt uitgevoerd, selecteert u de virtuele machines in **virtual machines selecteren**. Selecteer vervolgens **DB's Detecteren**.
6. Azure Backup detecteert alle SAP HANA databases op de virtuele machine. Tijdens het detecteren registreert Azure Backup de virtuele machine met de kluis en wordt een extensie op de virtuele machine geïnstalleerd. Er is geen agent geïnstalleerd op de database.

    ![SAP HANA-data bases ontdekken](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Back-up configureren  

Schakel nu back-up in.

1. Selecteer in stap 2 de optie **back-up configureren**.

    ![Configureer back-up](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Selecteer in **items selecteren waarvan u een back**-up wilt maken de data bases die u wilt beveiligen > **OK**.

    ![Items selecteren waarvan u een back-up wilt maken](./media/backup-azure-sap-hana-database/select-items.png)
3. Kies in **back-upbeleid**  >  **back-upbeleid**, maak een nieuw back-upbeleid voor de data bases, in overeenstemming met de onderstaande instructies.

    ![Back-upbeleid kiezen](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Nadat u het beleid hebt gemaakt, selecteert u in het menu **back-** up de optie **back-up inschakelen**.

    ![Back-up inschakelen](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Volg de voortgang van de back-upconfiguratie in het **Systeemvak** van de portal.

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.

>[!NOTE]
>Azure Backup past de tijd niet automatisch aan de zomertijd aan wanneer u een back-up van een SAP HANA-database maakt die op een virtuele Azure-machine wordt uitgevoerd.
>
>Pas het beleid waar nodig handmatig aan.

Geef als volgt de beleidsinstellingen op:

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.

   ![Beleids naam invoeren](./media/backup-azure-sap-hana-database/policy-name.png)
1. In **Beleid voor een volledige back-up** selecteert u een **back-upfrequentie** en kiest u **Dagelijks** of **Wekelijks**.
   * **Dagelijks**: Selecteer het uur en de tijd zone waarin de back-uptaak wordt gestart.
       * U moet een volledige back-up uitvoeren. U kunt deze optie niet uitschakelen.
       * Selecteer **Volledige back-up** om het beleid te bekijken.
       * U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.
   * **Wekelijks**: Selecteer de dag van de week, het uur en de tijd zone waarin de back-uptaak wordt uitgevoerd.

   ![Back-upfrequentie selecteren](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. In **Bewaartermijn** configureert u de bewaarinstellingen voor de volledige back-up.
    * Alle opties zijn standaard geselecteerd. Wis de limieten voor het Bewaar bereik die u niet wilt gebruiken en stel de beperkingen in die u doet.
    * De minimale bewaarperiode voor elk type back-up (volledig/differentieel/logboek) is zeven dagen.
    * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    * De back-up voor een specifieke dag wordt getagd en bewaard op basis van de wekelijkse bewaarperiode en uw instellingen.
    * De maandelijkse en jaarlijkse bewaarperioden werken op soortgelijke wijze.

1. In het menu voor het **beleid voor een volledige back-up** selecteert u **OK** om de instellingen te accepteren.
1. Selecteer **differentiële back-up** om een differentieel beleid toe te voegen.
1. In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.
    * U kunt maximaal één differentiële back-up per dag activeren.
    * Differentiële back-ups kunnen maximaal 180 dagen worden bewaard. Als dat voor u te kort is, moet u volledige back-ups gebruiken.

    ![Beleid voor differentiële back-ups](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > U kunt kiezen voor een differentieel of een incrementele dagelijkse back-up, maar niet voor beide.
1. In **Beleid voor een incrementele back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.
    * U kunt maximaal één incrementele back-up per dag activeren.
    * Incrementele back-ups kunnen maximaal 180 dagen worden bewaard. Als dat voor u te kort is, moet u volledige back-ups gebruiken.

    ![Beleid voor incrementele back-ups](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.
1. Selecteer **Logboekback-up** als u een back-upbeleid voor een transactielogboek wilt toevoegen;
    * Selecteer in **logboek back-up** de optie **inschakelen**.  Dit kan niet worden uitgeschakeld omdat SAP HANA alle logboek back-ups beheert.
    * Stel de besturings elementen voor de frequentie en de retentie in.

    > [!NOTE]
    > Logboek back-ups gaan alleen naar de stroom nadat een geslaagde volledige back-up is voltooid.

1. Selecteer **OK** om het beleid op te slaan en terug te gaan naar het hoofdmenu **Back-upbeleid**.
1. Zodra u het back-upbeleid hebt gedefinieerd, selecteert u **OK**.

> [!NOTE]
> Elke logboek back-up wordt gekoppeld aan de vorige volledige back-up om een herstel keten te vormen. Deze volledige back-up wordt bewaard totdat de retentie van de laatste back-up van het logboek is verlopen. Dit kan betekenen dat de volledige back-up gedurende een extra periode wordt bewaard om ervoor te zorgen dat alle logboeken kunnen worden hersteld. We gaan ervan uit dat een gebruiker een wekelijkse volledige back-up, een dagelijks differentieel en twee uur logboeken heeft. Deze zijn allemaal 30 dagen bewaard. Maar de wekelijkse volledige kan echter echt worden opgeruimd/verwijderd wanneer de volgende volledige back-up beschikbaar is, dat wil zeggen na 30 en 7 dagen. Een voor beeld: een wekelijkse volledige back-up gebeurt op een zestiende. Volgens het Bewaar beleid moet het worden bewaard tot dec 16de. De laatste keer dat de back-up van het logboek is gemaakt voor deze volledige, wordt de volgende geplande volledige, op nov 22. Totdat dit logboek beschikbaar is tot dec 22, kan de zestien 16de volledig niet worden verwijderd. Tot en met dec 22 wordt dus de ge16dede volledige nov bewaard.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

Back-ups worden uitgevoerd volgens het beleids schema. U kunt als volgt een back-up op aanvraag uitvoeren:

1. Selecteer **Back-upitems** in het menu kluis.
2. Selecteer in **Back-upitems** de virtuele machine waarop de SAP Hana-data base wordt uitgevoerd en selecteer **Nu back-up maken**.
3. Kies in **Nu back-up** het type back-up dat u wilt uitvoeren. Selecteer vervolgens **OK**. Deze back-up wordt bewaard op basis van het beleid dat aan dit back-upitem is gekoppeld.
4. De portal meldingen bewaken. U kunt de voortgang van de taak in het kluis dashboard controleren > **back-uptaken** worden  >  **uitgevoerd**. Afhankelijk van de grootte van de data base kan het maken van de eerste back-up enige tijd duren.

Standaard is het bewaren van back-ups op aanvraag 45 dagen.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>SAP HANA Studio-back-up uitvoeren op een Data Base met Azure Backup ingeschakeld

Ga als volgt te werk als u een lokale back-up wilt maken (met behulp van HANA Studio) van een Data Base waarvan een back-up wordt gemaakt met Azure Backup:

1. Wacht totdat alle volledige of logboek back-ups voor de Data Base zijn voltooid. Controleer de status in SAP HANA Studio/cockpit.
1. Schakel logboek back-ups uit en stel de back-catalogus in op het bestands systeem voor de relevante data base.
1. U doet dit door te dubbel klikken op **SystemDB**-  >  **configuratie**  >  **database**  >  **filter (logboek)**.
1. Stel **enable_auto_log_backup** in op **Nee**.
1. Stel **log_backup_using_backint** in op **False**.
1. Stel **catalog_backup_using_backint** in op **False**.
1. Maak een volledige back-up op aanvraag van de data base.
1. Wacht tot de volledige back-up en catalogus back-up zijn voltooid.
1. De vorige instellingen herstellen voor Azure:
    * Stel **enable_auto_log_backup** in op **Ja**.
    * Stel **log_backup_using_backint** in op **waar**.
    * Stel **catalog_backup_using_backint** in op **waar**.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het herstellen van SAP HANA-databases die worden uitgevoerd op virtuele Azure-machines](./sap-hana-db-restore.md)
* Meer informatie over [het beheren van SAP HANA-databases waarvan een back-up wordt gemaakt met behulp van Azure Backup](./sap-hana-db-manage.md)
