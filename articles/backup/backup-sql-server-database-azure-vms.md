---
title: Back-up maken van meerdere SQL Server-VM's uit de kluis
description: In dit artikel vindt u informatie over het maken van een back-up van SQL Server-data bases op virtuele machines van Azure met Azure Backup van de Recovery Services kluis
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c03b833be6c5e4c352125f31ad8c5ed072674b49
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258466"
---
# <a name="back-up-multiple-sql-server-vms-from-the-recovery-services-vault"></a>Back-ups maken van meerdere SQL Server Vm's vanuit de Recovery Services kluis

SQL Server-data bases zijn kritieke workloads waarvoor een laag herstel punt (RPO) en lange termijn retentie is vereist. U kunt back-ups maken van SQL Server-data bases die worden uitgevoerd op Azure virtual machines (Vm's) met behulp van [Azure backup](backup-overview.md).

In dit artikel wordt beschreven hoe u een back-up maakt van een SQL Server Data Base die wordt uitgevoerd op een virtuele Azure-machine naar een Azure Backup Recovery Services kluis.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een kluis maken en configureren.
> * Ontdek data bases en stel back-ups in.
> * Automatische beveiliging van databases instellen.

## <a name="prerequisites"></a>Vereisten

Controleer de volgende criteria voordat u een back-up maakt van een SQL Server Data Base:

1. Identificeer of maak een [Recovery Services kluis](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) in dezelfde regio en hetzelfde abonnement als de virtuele machine die als host fungeert voor het SQL Server exemplaar.
1. Controleer of de virtuele machine [verbinding](backup-sql-server-database-azure-vms.md#establish-network-connectivity)heeft met het netwerk.
1. Zorg ervoor dat de SQL Server-data bases voldoen [aan de richt lijnen voor de naamgeving van data bases voor Azure backup](#database-naming-guidelines-for-azure-backup).
1. Zorg ervoor dat de gecombineerde lengte van de naam van de SQL Server VM en de naam van de resource groep niet langer is dan 84 tekens voor Azure Resource Manager Vm's (of 77 tekens voor klassieke Vm's). Deze beperking geldt omdat sommige tekens zijn gereserveerd door de service.
1. Controleer of er geen andere back-upoplossingen zijn ingeschakeld voor de data base. Schakel alle andere SQL Server back-ups uit voordat u een back-up van de data base maakt.
1. Wanneer u SQL Server 2008 R2 of SQL Server 2012 gebruikt, kunt u het probleem met de tijd zone voor back-up uitvoeren, zoals [hier](https://support.microsoft.com/help/2697983/kb2697983-fix-an-incorrect-value-is-stored-in-the-time-zone-column-of)wordt beschreven. Zorg ervoor dat u de meest recente cumulatieve updates hebt om te voor komen dat het probleem met de tijd zone die hierboven wordt beschreven. Als het Toep assen van de updates op het SQL Server-exemplaar op de virtuele machine van Azure niet haalbaar is, schakelt u zomertijd (zomer tijd) uit voor de tijd zone op de VM.

> [!NOTE]
> U kunt Azure Backup inschakelen voor een virtuele machine van Azure en ook voor een SQL Server-Data Base die op de virtuele machine wordt uitgevoerd zonder conflict.

### <a name="establish-network-connectivity"></a>Netwerkverbinding tot stand brengen

Voor alle bewerkingen vereist een SQL Server-VM connectiviteit met de Azure Backup-Service, Azure Storage en Azure Active Directory. Dit kan worden bereikt door gebruik te maken van privé-eindpunten of door toegang te verlenen tot de vereiste openbare IP-adressen of FQDN's. Zonder de juiste connectiviteit met de vereiste Azure-services kunnen bewerkingen zoals het detecteren van databases, het configureren van back-ups, het uitvoeren van back-ups en het herstellen van gegevens mislukken.

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

U kunt op vergelijkbare wijze ook uitgaande NSG-beveiligingsregels maken voor Azure Storage en Azure AD.

#### <a name="azure-firewall-tags"></a>Azure Firewall-tags

Als u Azure Firewall gebruikt, maakt u een toepassingsregel met behulp van de [Azure Firewall FQDN-tag](../firewall/fqdn-tags.md) *AzureBackup*. Hiermee wordt alle uitgaande toegang tot Azure Backup toegestaan.

#### <a name="allow-access-to-service-ip-ranges"></a>Toegang tot IP-bereiken van de service toestaan

Als u ervoor kiest om toegang tot IP-adressen van de service toe te staan, raadpleegt u de IP-bereiken in het JSON-bestand dat [hier](https://www.microsoft.com/download/confirmation.aspx?id=56519) beschikbaar is. U moet toegang tot IP-adressen die overeenkomen met Azure Backup, Azure Storage en Azure Active Directory toestaan.

#### <a name="allow-access-to-service-fqdns"></a>Toegang tot FQDN's van de service toestaan

U kunt ook de volgende FQDN's gebruiken om toegang te verlenen tot de vereiste services van uw servers:

| Service    | Domeinnamen waarvoor toegang nodig is                             | Poorten
| -------------- | ------------------------------------------------------------ | ---
| Azure Backup  | `*.backup.windowsazure.com`                             | 443
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` | 443
| Azure AD      | Toegang tot FQDN's toestaan onder de secties 56 en 59 volgens [dit artikel](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) | Indien van toepassing

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Een HTTP-proxyserver gebruiken om verkeer te routeren

Wanneer u een back-up maakt van een SQL Server Data Base op een virtuele machine van Azure, gebruikt de back-upextensie op de VM de HTTPS-Api's om beheer opdrachten te verzenden naar Azure Backup en gegevens naar Azure Storage. De back-upextensie maakt ook gebruik van Azure AD voor verificatie. Leid het verkeer van de back-upextensie voor deze drie services via de HTTP-proxy. Gebruik de lijst met IP-adressen en FQDN's die hierboven worden genoemd om toegang tot de vereiste services toe te staan. Geverifieerde proxyservers worden niet ondersteund.

### <a name="database-naming-guidelines-for-azure-backup"></a>Richt lijnen voor database naamgeving voor Azure Backup

Vermijd het gebruik van de volgende elementen in database namen:

* Afsluitende en voorloop spaties
* Afsluitende uitroep tekens (!)
* Vier Kante haken sluiten (])
* Punt komma '; '
* Slash/

Aliasing is beschikbaar voor niet-ondersteunde tekens, maar we raden u aan om deze te vermijden. Zie [Het gegevensmodel van de tabelservice](/rest/api/storageservices/understanding-the-table-service-data-model) voor meer informatie.

>[!NOTE]
>Het **configureren** van de beveiliging voor data bases met speciale tekens zoals "+" of "&" in hun naam wordt niet ondersteund. U kunt de naam van de data base wijzigen of **automatische beveiliging** inschakelen, waardoor deze data bases kunnen worden beveiligd.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server-databases detecteren

Data bases detecteren die worden uitgevoerd op een virtuele machine:

1. In de [Azure-portal](https://portal.azure.com) opent u de Recovery Services-kluis die u gebruikt om een back-up te maken van de database.

2. Selecteer **back-up** in het dash board van **Recovery Services kluis** .

   ![Backup selecteren om het menu Doel van de back-up te openen](./media/backup-azure-sql-database/open-backup-menu.png)

3. In **back-updoel** stelt u in **waar wordt uw workload uitgevoerd?** naar **Azure**.

4. In **Waarvan wilt u een back-up maken?** selecteert u **SQL Server in Azure-VM**.

    ![SQL Server in Azure VM voor de back-up selecteren](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. In **Doel van de back-up** > **DB's detecteren in VM's** selecteert u **Detectie starten** om te zoeken naar niet-beveiligde VM's in het abonnement. Deze zoek opdracht kan enige tijd duren, afhankelijk van het aantal niet-beveiligde virtuele machines in het abonnement.

   * Niet-beveiligde virtuele machines zouden na detectie in de lijst moeten verschijnen, gesorteerd op naam en resourcegroep.
   * Als een virtuele machine niet wordt vermeld zoals u verwacht, kunt u zien of er al een back-up is gemaakt in een kluis.
   * Meerdere virtuele machines kunnen dezelfde naam hebben, maar ze behoren tot verschillende resource groepen.

     ![Back-up in behandeling tijdens het zoeken naar databases in VM's](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Selecteer in de lijst met virtuele machines de VM waarop de SQL Server-database wordt uitgevoerd > **DB's detecteren**.

7. Spoor database detectie op in **meldingen**. De tijd die nodig is voor deze actie is afhankelijk van het aantal VM-data bases. Wanneer de geselecteerde databases zijn gedetecteerd, wordt er een slagingsbericht weergegeven.

    ![Bericht dat de implementatie is geslaagd](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup detecteert alle SQL Server-databases op de virtuele machine. Tijdens de detectie vinden de volgende elementen plaats op de achtergrond:

    * Azure Backup registreert de virtuele machine met de kluis voor de back-up van de werk belasting. Alleen voor alle data bases op de geregistreerde virtuele machine kan een back-up worden gemaakt naar deze kluis.
    * Azure Backup installeert de extensie AzureBackupWindowsWorkload op de virtuele machine. Er is geen agent geïnstalleerd op een SQL database.
    * Azure Backup maakt het serviceaccount NT Service\AzureWLBackupPluginSvc op de virtuele machine.
      * Het serviceaccount wordt gebruikt voor alle back-up- en herstelbewerkingen.
      * NT Service\AzureWLBackupPluginSvc vereist SQL sysadmin-machtigingen. Op alle SQL Server Vm's die op Marketplace zijn gemaakt, wordt de SqlIaaSExtension geïnstalleerd. De extensie AzureBackupWindowsWorkload maakt gebruik van de extensie SQLIaaSExtension om automatische de benodigde machtigingen op te halen.
    * Als u de virtuele machine niet hebt gemaakt op basis van de Marketplace of als u zich in SQL 2008 en 2008 R2 bevindt, is de virtuele machine mogelijk niet op de SqlIaaSExtension geïnstalleerd en mislukt de detectie bewerking met het fout bericht UserErrorSQLNoSysAdminMembership. Volg de instructies onder [set VM permissions](backup-azure-sql-database.md#set-vm-permissions)om dit probleem op te lossen.

        ![De VM en database selecteren](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Back-up configureren  

1. Selecteer in **back-updoel**  >  **stap 2: back**-up configureren de optie **back-up configureren**.

   ![Back-up configureren selecteren](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

1. Selecteer **resources toevoegen** om alle geregistreerde beschikbaarheids groepen en zelfstandige SQL Server exemplaren weer te geven.

    ![Resources toevoegen selecteren](./media/backup-azure-sql-database/add-resources.png)

1. Selecteer in het scherm **items selecteren voor back-up** de pijl links van een rij om de lijst met alle onbeveiligde data bases in die instantie of AlwaysOn-beschikbaarheids groep uit te vouwen.

    ![Items selecteren waarvan een back-up moet worden gemaakt](./media/backup-azure-sql-database/select-items-to-backup.png)

1. Kies alle data bases die u wilt beveiligen en selecteer vervolgens **OK**.

   ![De database beveiligen](./media/backup-azure-sql-database/select-database-to-protect.png)

   Ter optimalisering van de back-upbelastingen stelt Azure Backup het maximumaantal databases in één back-uptaak in op 50.

     * Om meer dan 50 back-ups te beschermen moet u meerdere databases configureren.
     * Als u de volledige instantie of de AlwaysOn-beschikbaarheids groep wilt [inschakelen](#enable-auto-protection) , selecteert u in de vervolg KEUZELIJST **voor** **beveiliging** en selecteert u vervolgens **OK**.

         > [!NOTE]
         > De functie voor [automatisch beveiligen](#enable-auto-protection) biedt niet alleen beveiliging op alle bestaande data bases tegelijk, maar beveiligt ook automatisch nieuwe data bases die zijn toegevoegd aan het betreffende exemplaar of de beschikbaarheids groep.  

1. Het **back-upbeleid** definiëren. U kunt een van de volgende handelingen uitvoeren:

   * Selecteer het standaard beleid als *HourlyLogBackup*.
   * Een bestaand back-upbeleid kiezen dat u eerder hebt gemaakt voor SQL.
   * Een nieuw beleid definiëren op basis van uw RPO en retentiebereik.

     ![Back-upbeleid selecteren](./media/backup-azure-sql-database/select-backup-policy.png)

1. Selecteer **back-up inschakelen** om de bewerking **Beveiliging configureren** in te dienen en de voortgang van de configuratie in het gebied **meldingen** van de portal bij te houden.

   ![Voortgang van de configuratie bijhouden](./media/backup-azure-sql-database/track-configuration-progress.png)

### <a name="create-a-backup-policy"></a>Maak een back-upbeleid

Een back-upbeleid bepaalt wanneer back-ups worden gemaakt en hoe lang ze worden bewaard.

* Een beleid wordt gemaakt op kluisniveau.
* U kunt hetzelfde back-upbeleid gebruiken voor meerdere kluizen, maar u moet het back-upbeleid toepassen op elke kluis.
* Wanneer u een back-upbeleid maakt, is een dagelijkse volledige back-up de standaardinstelling.
* U kunt een differentiële back-up toevoegen, maar alleen als u een wekelijkse volledige back-up configureert.
* Meer informatie over [verschillende typen back-upbeleid](backup-architecture.md#sql-server-backup-types).

Ga als volgt te werk om een back-upbeleid te maken:

1. Selecteer in de kluis **Back-upbeleid** > **Toevoegen**.
1. In **toevoegen** selecteert u **SQL Server in azure VM** om het beleids type te definiëren.

   ![Een beleidstype voor het nieuwe back-upbeleid kiezen](./media/backup-azure-sql-database/policy-type-details.png)

1. Geef bij **Beleidsnaam** een naam voor het nieuwe beleid op.

    ![Beleids naam invoeren](./media/backup-azure-sql-database/policy-name.png)

1. Selecteer de koppeling **bewerken** die overeenkomt met **volledige back-up** om de standaard instellingen te wijzigen.

   * Selecteer een **back-upfrequentie**. Kies **dagelijks** of **wekelijks**.
   * Als u **Dagelijks** kiest, selecteert u het tijdstip en de tijdzone waarop de back-uptaak moet worden gestart. U kunt geen differentiële back-ups maken voor dagelijkse volledige back-ups.

     ![Velden voor nieuw back-upbeleid](./media/backup-azure-sql-database/full-backup-policy.png)  

1. In **Bewaar termijn** worden alle opties standaard geselecteerd. Wis de limieten voor het Bewaar bereik die u niet wilt en stel vervolgens de intervallen in voor gebruik.

    * De minimale Bewaar periode voor elk type back-up (volledig, differentieel en logboek) is zeven dagen.
    * Herstelpunten worden getagd voor retentie op basis van de bewaarperiode. Als u een dagelijkse volledige back-up selecteert, wordt slechts één volledige back-up per dag geactiveerd.
    * De back-up voor een specifieke dag wordt gelabeld en bewaard op basis van de wekelijkse Bewaar termijn en de instelling voor wekelijkse Bewaar periode.
    * Maandelijkse en jaarlijkse Bewaar perioden gedragen zich op een vergelijk bare manier.

       ![Intervalinstellingen voor bewaarperiode](./media/backup-azure-sql-database/retention-range-interval.png)

1. Selecteer **OK** om de instelling voor volledige back-ups te accepteren.
1. Selecteer de koppeling **bewerken** die overeenkomt met **differentiële back-up** om de standaard instellingen te wijzigen.

    * In **Beleid voor een differentiële back-up** selecteert u **Inschakelen** om de frequentie- en bewaarinstellingen te openen.
    * U kunt slechts één differentiële back-up per dag activeren. Een differentiële back-up kan niet worden geactiveerd op dezelfde dag als een volledige back-up.
    * Differentiële back-ups kunnen maximaal 180 dagen worden bewaard.
    * Differentiële back-up wordt niet ondersteund voor de hoofd database.

      ![Differentiële back-upbeleid](./media/backup-azure-sql-database/differential-backup-policy.png)

1. Selecteer de koppeling **bewerken** die overeenkomt met **logboek back-up** om de standaard instellingen te wijzigen

    * In **Logboekback-up** selecteert u **Inschakelen** en stelt u de frequentie- en bewaarinstellingen in.
    * Logboek back-ups kunnen worden uitgevoerd op elke 15 minuten en kunnen Maxi maal 35 dagen worden bewaard.
    * Als de data base zich in het [eenvoudige herstel model](/sql/relational-databases/backup-restore/recovery-models-sql-server)bevindt, wordt het back-upschema van het logboek voor die data base onderbroken en worden er geen logboek back-ups geactiveerd.
    * Als het herstel model van de data base wordt gewijzigd van **volledig** in **eenvoudig**, worden logboek back-ups binnen 24 uur na de wijziging in het herstel model gepauzeerd. Op dezelfde manier kan, als het herstel model wordt gewijzigd van **eenvoudig**, het impliceren van logboek back-ups voor de data base, de planning back-ups van Logboeken worden ingeschakeld binnen 24 uur na het wijzigen van het herstel model.

      ![Logboek back-upbeleid](./media/backup-azure-sql-database/log-backup-policy.png)

1. Kies in het menu **back-upbeleid** of u **SQL-back-upcompressie** wilt inschakelen of niet. Deze optie is standaard uitgeschakeld. Als deze functie is ingeschakeld, stuurt SQL Server een gecomprimeerde back-upstream naar de VDI. Azure Backup overschrijft de standaard instellingen op exemplaar niveau met compressie/NO_COMPRESSION-component, afhankelijk van de waarde van dit besturings element.

1. Als u klaar bent met het bewerken van het back-upbeleid, selecteert u **OK**.

> [!NOTE]
> Elke logboek back-up wordt gekoppeld aan de vorige volledige back-up om een herstel keten te vormen. Deze volledige back-up wordt bewaard totdat de retentie van de laatste back-up van het logboek is verlopen. Dit kan betekenen dat de volledige back-up gedurende een extra periode wordt bewaard om ervoor te zorgen dat alle logboeken kunnen worden hersteld. We gaan ervan uit dat u een wekelijkse volledige back-up, een dagelijks differentieel en twee uur Logboeken hebt. Deze zijn allemaal 30 dagen bewaard. Maar de wekelijkse volledige kan echter echt worden opgeruimd/verwijderd wanneer de volgende volledige back-up beschikbaar is, dat wil zeggen na 30 en 7 dagen. Een voor beeld: een wekelijkse volledige back-up gebeurt op een zestiende. Volgens het Bewaar beleid moet het worden bewaard tot dec 16de. De laatste keer dat de back-up van het logboek is gemaakt voor deze volledige, wordt de volgende geplande volledige, op nov 22. Totdat dit logboek beschikbaar is tot dec 22, kan de zestien 16de volledig niet worden verwijderd. Tot en met dec 22 wordt dus de ge16dede volledige nov bewaard.

## <a name="enable-auto-protection"></a>Automatische beveiliging inschakelen  

U kunt automatische beveiliging inschakelen om automatisch een back-up te maken van alle bestaande en toekomstige data bases naar een zelfstandig SQL Server exemplaar of naar een AlwaysOn-beschikbaarheids groep.

* Er is geen limiet voor het aantal data bases dat u per keer voor automatische beveiliging kunt selecteren. Detectie wordt doorgaans elke acht uur uitgevoerd. U kunt echter direct nieuwe data bases detecteren en beveiligen als u hand matig een detectie uitvoert door de optie **db's opnieuw detecteren** te selecteren.
* U kunt data bases niet selectief beveiligen of uitsluiten van beveiliging in een exemplaar op het moment dat u automatische beveiliging inschakelt.
* Als uw exemplaar al beveiligde data bases bevat, blijven ze beschermd onder hun respectieve beleid, zelfs nadat u automatische beveiliging hebt ingeschakeld. Alle niet-beveiligde data bases die later worden toegevoegd, hebben slechts één beleid dat u definieert op het moment van het inschakelen van automatische beveiliging, vermeld onder **back-up configureren**. U kunt echter later het beleid wijzigen dat is gekoppeld aan een automatisch beveiligde data base.  

Automatische beveiliging inschakelen:

  1. In **Items voor back-up** selecteert u het exemplaar waarvoor u automatische beveiliging wilt inschakelen.
  2. Selecteer de vervolg keuzelijst onder **AutoProtect**, kies **aan** en selecteer **OK**.

      ![Automatische beveiliging inschakelen voor de beschikbaarheids groep](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Back-up wordt voor alle databases tegelijk getriggerd en kan worden gevolgd in **Back-uptaken**.

Als u automatische beveiliging wilt uitschakelen, selecteert u de naam van het exemplaar onder **back-up configureren** en selecteert u vervolgens automatische **beveiliging uitschakelen** voor het exemplaar. Er wordt nog steeds een back-up van alle data bases gemaakt, maar toekomstige data bases worden niet automatisch beveiligd.

![Automatische beveiliging voor dat exemplaar uitschakelen](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Volgende stappen

Leer hoe u het volgende doet:

* [Back-ups van SQL Server-data bases herstellen](restore-sql-database-azure-vm.md)
* [Back-ups van SQL Server-data bases beheren](manage-monitor-sql-database-backup.md)
