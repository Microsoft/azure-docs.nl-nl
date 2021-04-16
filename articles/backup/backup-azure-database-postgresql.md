---
title: Back-up maken van Azure Database for PostgreSQL
description: Meer informatie Azure Database for PostgreSQL back-up met langetermijnretentie (preview)
ms.topic: conceptual
ms.date: 04/12/2021
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 8fd69e016c7f0b175ef49b98add5692743858f62
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480066"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL back-up maken met langetermijnretentie (preview)

Azure Backup en Azure Database Services hebben samen een back-upoplossing van bedrijfsklasse gebouwd voor Azure Database for PostgreSQL-servers die back-ups maximaal tien jaar bewaren.

Naast langetermijnretentie heeft de oplossing ook veel andere mogelijkheden, zoals hieronder wordt vermeld:

- Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) tot de database met behulp van Azure Active Directory en MSI-verificatie (Managed Service Identity).
- Door de klant beheerde geplande en on-demand back-up op het niveau van de individuele database.
- Herstel op databaseniveau naar een Postgres-server of rechtstreeks naar blobopslag.
- Langetermijnretentie.
- Centrale bewaking van alle bewerkingen en taken.
- Back-ups worden opgeslagen in afzonderlijke beveiligings- en foutdomeinen. Dus zelfs als de bronserver zou worden gecompromitteerd of zelfs zou worden uitgeschakeld, zouden de back-ups veilig blijven in de [Backup-kluis](backup-vault-overview.md).
- Het gebruik **pg_dump** biedt meer flexibiliteit in herstels, zodat u kunt herstellen in databaseversies of zelfs slechts een deel van de back-up kunt herstellen.

U kunt deze oplossing onafhankelijk of naast de systeemeigen back-upoplossing van Azure PostgreSQL gebruiken die een bewaarperiode van maximaal 35 dagen biedt. De systeemeigen oplossing is geschikt voor operationele herstelmogelijkheden, bijvoorbeeld wanneer u wilt herstellen van de meest recente back-ups. De Azure Backup helpt u met uw nalevingsbehoeften en gedetailleerdere en flexibele back-up en herstel.

## <a name="support-matrix"></a>Ondersteuningsmatrix

|Ondersteuning  |Details  |
|---------|---------|
|Ondersteunde implementaties   |  [Azure Database for PostgreSQL - één server](../postgresql/overview.md#azure-database-for-postgresql---single-server)     |
|Ondersteunde Azure-regio's |  VS - oost, VS - oost 2, VS - centraal, VS - zuid-centraal, VS - west, VS - west 2, VS - west-centraal, Brazilië - zuid, Canada - centraal, Europa - noord, Europa - west, VK - zuid, VK - west Duitsland - west-centraal, Zwitserland - noord, Zwitserland - west, Azië - oost, South Azië - oost, Japan - oost, Japan - west, Korea - centraal, Korea - zuid, India - centraal, Australië - oost, Australië - centraal, Australië - centraal 2, VAE - noord  |
|Ondersteunde Versies van Azure PostgreSQL    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Overwegingen en beperkingen voor functies

- Alle bewerkingen worden alleen vanuit de Azure Portal ondersteund.
- Aanbevolen limiet voor de maximale databasegrootte is 400 GB.
- Back-ups tussen regio's worden niet ondersteund. Dit betekent dat u geen back-up kunt maken van een Azure PostgreSQL-server naar een kluis in een andere regio. Op dezelfde manier kunt u alleen een back-up herstellen naar een server in dezelfde regio als de kluis.
- Alleen de gegevens worden hersteld op het moment van herstellen. 'Rollen' worden niet hersteld.
- In de preview-versie wordt u aangeraden de oplossing alleen in uw testomgeving uit te voeren.

## <a name="backup-process"></a>Back-upproces

1. Deze oplossing gebruikt **pg_dump** back-ups van uw Azure PostgreSQL-databases te maken.

2. Zodra u de Azure PostgreSQL-databases hebt opgegeven van wie u een back-up wilt maken, controleert de service of deze de juiste set machtigingen en toegang heeft om de back-upbewerking uit te voeren op de server en de database.

3. Azure Backup een werkrol met een back-upextensie geïnstalleerd. Deze extensie communiceert met de Postgres-server.

4. Deze extensie bestaat uit een coördinator en een Azure Postgres-invoegvoegplaats. Hoewel de coördinator verantwoordelijk is voor het activeren van werkstromen voor verschillende bewerkingen, zoals het configureren van back-ups, back-ups en herstel, is de invoegoplossing verantwoordelijk voor de werkelijke gegevensstroom.
  
5. Zodra u de beveiliging voor de geselecteerde databases hebt geconfigureerd, stelt de back-upservice de coördinator in met de back-upschema's en andere beleidsdetails.

6. Op het geplande tijdstip communiceert de coördinator met de invoegserver en begint deze met het streamen van de back-upgegevens van de Postgres-server met behulp **van pg_dump**.

7. De gegevens worden rechtstreeks naar de Backup-kluis verzendt, waardoor een faseringslocatie niet meer nodig is. De gegevens worden versleuteld met door Microsoft beheerde sleutels en opgeslagen door de Azure Backup-service in opslagaccounts.

8. Wanneer de gegevensoverdracht is voltooid, bevestigt de coördinator de doorvoer met de back-upservice.

    ![Back-upproces](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Back-up configureren in Azure PostgreSQL-databases

U kunt back-ups configureren op meerdere databases op meerdere Azure PostgreSQL-servers. Zorg ervoor dat [de vereiste machtigingen die](#prerequisite-permissions-for-configure-backup-and-restore) de service nodig heeft om een back-up te maken van de Postgres-servers al zijn geconfigureerd.

De volgende instructies zijn een stapsgewijse handleiding voor het configureren van back-ups op de Azure PostgreSQL-databases met behulp van Azure Backup:

1. Er zijn twee manieren om het proces te starten:

    1. Ga naar Backup [Center](backup-center-overview.md)  ->  **Overview**  ->  **Backup**.

        ![Ga naar Back-upcentrum](./media/backup-azure-database-postgresql/backup-center.png)

        Selecteer **onder Initiëren: Back-up** configureren het **gegevensbrontype** **als Azure Database for PostgreSQL**.

        ![Selecteer in Initiëren: Back-up configureren de optie Gegevensbrontype](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. U kunt ook rechtstreeks naar Back-upkluizen [Back-up](backup-vault-overview.md)  ->  **gaan.**

        ![Ga naar Back-upkluizen](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Selecteer Back-up in Back-upkluis](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. Selecteer **onder Back-up** configureren de **Back-upkluis** waarop u een back-up van uw Postgres-databases wilt maken. Deze informatie is vooraf ingevuld als u zich al in de context van de kluis hebt.

    ![Back-upkluis selecteren in Back-up configureren](./media/backup-azure-database-postgresql/configure-backup.png)

1. Selecteer of maak een **back-upbeleid.**

    ![Back-upbeleid kiezen](./media/backup-azure-database-postgresql/backup-policy.png)

1. Selecteer resources of Postgres-databases om een back-up van te maken.

    ![Resources selecteren om een back-up van te maken](./media/backup-azure-database-postgresql/select-resources.png)

1. U kunt kiezen uit de lijst met alle Azure PostgreSQL-servers in abonnementen als deze zich in dezelfde regio als de kluis hebben. Vouw de pijl uit om de lijst met databases op een server weer te geven.

    ![Servers kiezen](./media/backup-azure-database-postgresql/choose-servers.png)

1. De service voert deze controles uit op de geselecteerde databases om te controleren of de kluis machtigingen heeft om een back-up te maken van de geselecteerde Postgres-servers en -databases.
    1. **De gereedheid voor back-ups** voor alle databases moet **Geslaagd zijn** om door te gaan.
    1. Als er een fout is opgetreden, **lost** u de fout op en **bevestigt** u de database opnieuw of verwijdert u de database uit de selecties.

    ![Validatiefouten die moeten worden opgelost](./media/backup-azure-database-postgresql/validation-errors.png)

1. Bevestig alle details onder Controleren **en configureren en** selecteer **Back-up configureren om** de bewerking te verzenden.

    ![Details bevestigen in Controleren en configureren](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Zodra de **back-upbewerking configureren is** geactiveerd, wordt er een back-up-exemplaar gemaakt. U kunt de status van de bewerking volgen onder het deelvenster [Back-up-exemplaren](backup-center-monitor-operate.md#backup-instances) in de weergave Back-upcentrum of kluis.

    ![Back-up-exemplaren](./media/backup-azure-database-postgresql/backup-instances.png)

1. De back-ups worden geactiveerd volgens het back-upschema dat is gedefinieerd in het beleid. De taken kunnen worden bijgespoord onder [Back-uptaken.](backup-center-monitor-operate.md#backup-jobs) Op dit moment kunt u taken weergeven voor de afgelopen zeven dagen.

    ![Back-uptaken](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Back-upbeleid maken

1. Ga naar **Back-upcentrum**  ->  **back-upbeleid**  ->  **Toevoegen.** U kunt ook naar Back-upbeleid **back-upbeleid voor**  ->  **back-upkluis**  ->  **Toevoegen gaan.**

    ![Back-upbeleid toevoegen](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Voer een **naam in** voor het nieuwe beleid.

    ![Voer de naam van het beleid in](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Definieer het back-upschema. Momenteel wordt wekelijkse **back-up** ondersteund. U kunt de back-ups op een of meer dagen van de week plannen.

    ![Het back-upschema definiëren](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. **Retentie-instellingen** definiëren. U kunt een of meer retentieregels toevoegen. Elke bewaarregel gaat uit van invoer voor specifieke back-ups en de gegevensopslag en bewaarduur voor deze back-ups.

1. U kunt ervoor kiezen om uw back-ups op te slaan in een van de twee gegevensarchieven (of **lagen):** Back-upgegevensarchief (Standard-laag) of Archief met **gegevensarchief** archiveren (in preview).

   U kunt ervoor kiezen **de back-up na** verloop van de vervaldatum te verplaatsen naar een archief met gegevensarchieven in het gegevensarchief van de back-up.

1. De **standaardretentieregel** wordt toegepast als er geen andere bewaarregel is en heeft een standaardwaarde van drie maanden.

    - De bewaarduur varieert van zeven dagen tot tien jaar in het **gegevensopslag voor back-ups.**
    - De bewaarduur varieert van zes maanden tot tien jaar in **archiefgegevensarchief**.

    ![Bewaarperiode bewerken](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>De retentieregels worden geëvalueerd in een vooraf bepaalde volgorde van prioriteit. De prioriteit is het hoogst voor **de jaarlijkse** regel, gevolgd door de **maandelijkse** en vervolgens de **wekelijkse** regel. Standaardretentie-instellingen worden toegepast wanneer er geen andere regels in aanmerking komen. Hetzelfde herstelpunt kan bijvoorbeeld de eerste geslaagde back-up zijn die elke week wordt gemaakt, evenals de eerste geslaagde back-up die elke maand wordt gemaakt. Omdat de prioriteit van de maandelijkse regel echter hoger is dan die van de wekelijkse regel, geldt de retentie die overeenkomt met de eerste geslaagde back-up die elke maand wordt gemaakt.

## <a name="restore"></a>Herstellen

U kunt een database herstellen naar een Azure PostgreSQL-server binnen hetzelfde abonnement, als de service de juiste set machtigingen op de doelserver heeft. Zorg ervoor [dat de vereiste machtigingen](#prerequisite-permissions-for-configure-backup-and-restore) die de service nodig heeft om een back-up te maken van de Postgres-servers al zijn geconfigureerd.

Volg deze stapsgewijse handleiding om een herstel te activeren:

1. Er zijn twee manieren om het herstelproces te starten:
    1. Ga naar [Overzicht van Back-upcentrum](backup-center-overview.md)  ->    ->  **herstellen.**

    ![Selecteer Herstellen in het Back-upcentrum](./media/backup-azure-database-postgresql/backup-center-restore.png)

    Selecteer **onder Initiëren:** herstellen het **gegevensbrontype** **als Azure Database for PostgreSQL**. Selecteer het **back-up-exemplaar**.

    ![Selecteer Gegevensbrontype in Initiëren:herstellen](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. U kunt ook rechtstreeks naar Backup **Vault**  ->  **Backup Instances gaan.** Selecteer **Back-up-exemplaar** dat overeenkomt met de database die u wilt herstellen.

    ![Back-up-exemplaren voor herstel](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Lijst met back-up-exemplaren](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Herstellen selecteren](./media/backup-azure-database-postgresql/select-restore.png)

1. **Selecteer herstelpunt in** de lijst met alle volledige back-ups die beschikbaar zijn voor het geselecteerde back-up-exemplaar. Standaard is het meest recente herstelpunt geselecteerd.

    ![Herstelpunt selecteren](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Lijst met herstelpunten](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Parameters **voor het herstellen van invoer.** Op dit moment kunt u kiezen uit twee soorten herstel: **Herstellen als database** en Herstellen als **bestanden.**

1. **Herstellen als database:** herstel de back-upgegevens om een nieuwe database te maken op de PostgreSQL-doelserver.

    - De doelserver kan hetzelfde zijn als de bronserver. Het overschrijven van de oorspronkelijke database wordt echter niet ondersteund.
    - U kunt kiezen uit de server in alle abonnementen, maar in dezelfde regio als de kluis.
    - Selecteer **Controleren en herstellen.** Hierdoor wordt validatie uitgevoerd om te controleren of de service over de juiste herstelmachtigingen beschikt op de doelserver.

    ![Herstellen als database](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Herstellen als bestanden:** dump de back-upbestanden naar het doelopslagaccount (blobs).

    - U kunt kiezen uit de opslagaccounts in alle abonnementen, maar in dezelfde regio als de kluis.
    - Selecteer de doelcontainer in de lijst met containers die zijn gefilterd voor het geselecteerde opslagaccount.
    - Selecteer **Controleren en herstellen.** Hierdoor wordt validatie uitgevoerd om te controleren of de service over de juiste herstelmachtigingen beschikt op de doelserver.

    ![Herstellen als bestanden](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Als het herstelpunt zich in de archieflaag, moet u het herstelpunt rehydrateren voordat u het herstelpunt herstelt.
   
   ![Rehydratatie-instellingen](./media/backup-azure-database-postgresql/rehydration-settings.png)
   
   Geef de volgende aanvullende parameters op die vereist zijn voor rehydratatie:
   - **Rehydratatieprioriteit:** De standaardwaarde is **Standard.**
   - **Rehydratatieduur:** De maximale rehydratatieduur is 30 dagen en de minimale rehydratatieduur is 10 dagen. De standaardwaarde is **15**.
   
   Het herstelpunt wordt opgeslagen in het **back-upgegevensopslag** voor de opgegeven rehydratatieduur.


1. Controleer de informatie en selecteer **Herstellen.** Hiermee wordt een bijbehorende hersteltaken triggeren die kunnen worden bijgespoord onder **Back-uptaken.**

>[!NOTE]
>Archiefondersteuning voor Azure Database for PostgreSQL is in beperkte openbare preview.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Vereiste machtigingen voor het configureren van back-up en herstel

Azure Backup volgt strikte beveiligingsrichtlijnen. Hoewel het een native Azure™service is, wordt er niet uitgegaan van machtigingen voor de resource en moeten deze expliciet worden gegeven door de gebruiker.  Referenties om verbinding te maken met de database worden ook niet opgeslagen. Dit is belangrijk om uw gegevens te beveiligen. In plaats daarvan gebruiken we Azure Active Directory verificatie.

[Download dit document om](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) een geautomatiseerd script en gerelateerde instructies op te halen. Er wordt een juiste set machtigingen verleend aan een Azure PostgreSQL-server, voor back-up en herstel.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>De back-up van Azure PostgreSQL-databases beheren

Dit zijn de beheerbewerkingen die u kunt uitvoeren op **back-up-exemplaren:**

### <a name="on-demand-backup"></a>Back-up op aanvraag

Als u een back-up wilt activeren die niet volgens de planning is opgegeven in het beleid, gaat u naar **Back-up-exemplaren**  ->  **Nu back-up maken.**
Kies uit de lijst met bewaarregels die zijn gedefinieerd in het bijbehorende back-upbeleid.

![Nu back-up activeren](./media/backup-azure-database-postgresql/backup-now.png)

![Kies uit de lijst met retentieregels](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Beveiliging stoppen

U kunt de beveiliging van een back-upitem stoppen. Hiermee worden ook de bijbehorende herstelpunten voor dat back-upitem verwijderd. Als herstelpunten minimaal zes maanden niet in de archieflaag staan, worden voor het verwijderen van deze herstelpunten kosten in het begin van de verwijdering in behandeling. We bieden nog geen optie om de beveiliging te stoppen terwijl de bestaande herstelpunten behouden blijven.

![Beveiliging stoppen](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Beleid wijzigen

U kunt het bijbehorende beleid wijzigen met een back-up-exemplaar.

1. Selecteer het beleid **voor het wijzigen van het**  ->  **back-up-exemplaar.**

    ![Beleid wijzigen](./media/backup-azure-database-postgresql/change-policy.png)

1. Selecteer het nieuwe beleid dat u wilt toepassen op de database.

    ![Beleid opnieuw toewijzen](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Problemen oplossen

Deze sectie bevat informatie over probleemoplossing voor het maken van back-up van Azure PostgreSQL-databases met Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Geef Backup Vault MSI **leestoegang op** de PG-server waar u een back-up van wilt maken of wilt herstellen.

Voor het tot stand brengen van een beveiligde verbinding met de PostgreSQL-database maakt Azure Backup gebruik van het [MSI-verificatiemodel (Managed Service Identity).](../active-directory/managed-identities-azure-resources/overview.md) Dit betekent dat de back-upkluis alleen toegang heeft tot de resources die expliciet zijn verleend door de gebruiker.

Een systeem-MSI wordt automatisch toegewezen aan de kluis op het moment dat deze wordt gemaakt. U moet deze kluis-MSI toegang geven tot de PostgreSQL-servers van waar u een back-up van databases wilt maken.

Stappen:

1. Ga op de Postgres-server naar het **deelvenster Access Control (IAM).**

    ![Access Control deelvenster](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Selecteer **Roltoewijzingen toevoegen.**

    ![Roltoewijzing toevoegen](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. Voer in het rechtercontextvenster dat wordt geopend het volgende in:<br>

   - **Rol:** Kies de **rol** Lezer in de vervolgkeuzelijst.<br>
   - **Toegang toewijzen aan:** Kies de **optie Gebruiker, groep of service-principal** in de vervolgkeuzelijst.<br>
   - **Selecteer:** Voer de naam van de Back-upkluis in waarvan u een back-up wilt maken van deze server en de databases ervan.<br>

    ![Rol selecteren](./media/backup-azure-database-postgresql/select-role-and-enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Maak een back-upgebruiker voor de database die kan worden geverifieerd met Azure Active Directory:

Deze fout kan worden veroorzaakt door een afwezigheid van een Azure Active Directory-beheerder voor de PostgreSQL-server of als er geen back-upgebruiker is die kan worden geverifieerd met behulp van Azure Active Directory.

Stappen:

Voeg een Active Directory-beheerder toe aan de OSS-server:

Deze stap is vereist om verbinding te maken met de database via een gebruiker die kan verifiëren met Azure Active Directory in plaats van een wachtwoord. De Azure AD-beheerder in Azure Database for PostgreSQL heeft de rol **azure_ad_admin**. Alleen een **azure_ad_admin** kan nieuwe databasegebruikers maken die kunnen worden geverifieerd met Azure AD.

1. Ga naar het tabblad Active Directory-beheerder in het linkernavigatievenster van de serverweergave en voeg uzelf (of iemand anders) toe als de Active Directory-beheerder.

    ![Active Directory-beheerder instellen](./media/backup-azure-database-postgresql/set-admin.png)

1. Zorg ervoor dat u **de gebruikersinstelling** van de AD-beheerder op kunt slaan.

    ![Gebruikersinstelling voor Active Directory-beheerder opslaan](./media/backup-azure-database-postgresql/save-admin-setting.png)

Raadpleeg dit [document voor de](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) lijst met stappen die u moet uitvoeren om de stappen voor het verlenen van machtigingen te voltooien.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Stel de netwerkverbinding tot stand door de vlag **Toegang tot Azure-services toestaan** in te stellen in de serverweergave. Stel in de serverweergave onder het deelvenster **Verbindingsbeveiliging** de vlag **Toegang tot Azure-services** toestaan in op **Ja.**

![Toegang tot Azure-services toestaan](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Machtiging voor het herstellen naar een opslagaccountcontainer bij het herstellen als bestanden

1. Geef de Backup Vault MSI toegang tot de opslagaccountcontainers met behulp van de Azure Portal.
    1. Ga naar **Opslagaccount**  ->  **Access Control**  ->  **Roltoewijzing toevoegen.**
    1. Wijs **de rol Inzender voor** opslagblobgegevens toe aan de MSI van de Back-upkluis.

    ![De rol Inzender voor opslagblobgegevens toewijzen](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. U kunt ook gedetailleerde machtigingen verlenen voor de specifieke container waar u naar wilt herstellen met behulp van de Azure [CLI-opdracht az role assignment create.](/cli/azure/role/assignment)

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Vervang de parameter assignee door de **toepassings-id** van de MSI van de kluis en de bereikparameter die naar uw specifieke container moet verwijzen.
    1. Als u de **toepassings-id van** de kluis-MSI wilt op halen, selecteert u **Alle toepassingen onder** **Toepassingstype:**

        ![Selecteer Alle toepassingen](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Zoek de naam van de kluis en kopieer de toepassings-id:

        ![Zoeken naar kluisnaam](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Back-upkluizen](backup-vault-overview.md)
