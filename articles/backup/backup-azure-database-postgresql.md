---
title: Back-up maken van Azure Database for PostgreSQL
description: Meer informatie over Azure Database for PostgreSQL back-up met lange termijn retentie (preview-versie)
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: 628f43788c11558185a7b8a735373f0a663fdad1
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569484"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL back-up met lange termijn retentie (preview-versie)

Azure Backup-en Azure Data Base-Services hebben samen een back-upoplossing op bedrijfs niveau gemaakt voor Azure Database for PostgreSQL servers die back-ups gedurende Maxi maal tien jaar bewaren.

Naast lange termijn retentie heeft de oplossing ook veel andere mogelijkheden, zoals hieronder wordt weer gegeven:

- Toegangs beheer op basis van rollen (Azure RBAC) van Azure naar de data base met behulp van Azure Active Directory en Managed Service Identity (MSI)-verificatie.
- Door klanten gecontroleerde geplande en on-demand back-ups op het niveau van de afzonderlijke data base.
- Herstel op database niveau naar een wille keurige post gres-server of rechtstreeks naar de Blob-opslag.
- Langetermijnretentie.
- Centrale bewaking van alle bewerkingen en taken.
- Back-ups worden opgeslagen in afzonderlijke beveiligings-en fout domeinen. Zelfs als de bron server zou kunnen worden aangetast of zelfs moeten worden gedood, blijven de back-ups veilig in de [back-upkluis](backup-vault-overview.md).
- Het gebruik van **pg_dump** biedt meer flexibiliteit in herstel bewerkingen, zodat u kunt herstellen over database versies of zelfs slechts een deel van de back-up hoeft te herstellen.

U kunt deze oplossing afzonderlijk of in aanvulling op de systeem eigen back-upoplossing die wordt aangeboden door Azure PostgreSQL. Dit biedt een Bewaar periode van Maxi maal 35 dagen. De systeem eigen oplossing is geschikt voor operationele herstel bewerkingen, zoals wanneer u wilt herstellen van de meest recente back-ups. Met de Azure Backup oplossing kunt u voldoen aan uw nalevings behoeften en nauw keurigere en flexibele back-ups en herstel bewerkingen.

## <a name="support-matrix"></a>Ondersteuningsmatrix

|Ondersteuning  |Details  |
|---------|---------|
|Ondersteunde implementaties   |  [Azure Database for PostgreSQL - één server](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server)     |
|Ondersteunde Azure-regio's |  VS-Oost, VS-Oost 2, VS-centraal, Zuid-Centraal VS, VS-West, VS-West 2, West-Centraal VS, Brazilië-zuid, Canada-centraal, Europa-noord, Europa-west, UK-zuid, UK-west, Duitsland-west-centraal, Zwitserland-noord, Zwitserland-west, Azië-oost, Zuid-Azië-oost, Japan-Oost, Japan-West, Korea-centraal, Korea-Zuid, India centraal, Australië-oost, Australië-centraal, Australië-centraal 2, UAE-Noord  |
|Ondersteunde Azure PostgreSQL-versies    |   9,5, 9,6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Overwegingen en beperkingen van functies

- Alle bewerkingen worden alleen ondersteund vanuit het Azure Portal.
- De aanbevolen limiet voor de maximale database grootte is 400 GB.
- De back-up tussen regio's wordt niet ondersteund. Dit betekent dat u geen back-up kunt maken van een Azure PostgreSQL-server naar een kluis in een andere regio. Op dezelfde manier kunt u een back-up alleen herstellen naar een server binnen dezelfde regio als de kluis.
- Alleen de gegevens worden hersteld op het moment van herstel. ' Rollen ' zijn niet hersteld.
- In de preview-versie raden we u aan de oplossing alleen op uw test omgeving uit te voeren.

## <a name="backup-process"></a>Back-upproces

1. Deze oplossing maakt gebruik van **pg_dump** om back-ups te maken van uw Azure postgresql-data bases.

2. Zodra u de Azure PostgreSQL-data bases opgeeft waarvan u een back-up wilt maken, controleert de service of deze de juiste set machtigingen en toegang heeft om de back-upbewerking op de server en de data base uit te voeren.

3. Azure Backup draait een rol van een werk nemer op waarop een back-upextensie is geïnstalleerd. Deze uitbrei ding communiceert met de post gres-server.

4. Deze uitbrei ding bestaat uit een coördinator en een Azure post gres-invoeg toepassing. Hoewel de coördinator verantwoordelijk is voor het activeren van werk stromen voor verschillende bewerkingen, zoals het configureren van back-ups, back-ups en herstellen, is de invoeg toepassing verantwoordelijk voor de daad werkelijke gegevens stroom.
  
5. Zodra u de beveiliging configureren voor de geselecteerde data bases hebt geactiveerd, stelt de back-upservice de coördinator in met de back-upschemaën en andere beleids gegevens.

6. Op de geplande tijd communiceert de coördinator met de invoeg toepassing en begint deze met het streamen van de back-upgegevens van de post gres-server met behulp van **pg_dump**.

7. De invoeg toepassing verzendt de gegevens rechtstreeks naar de back-upkluis, waardoor er geen staging-locatie nodig is. De gegevens worden versleuteld met door micro soft beheerde sleutels en opgeslagen door de Azure Backup-service in opslag accounts.

8. Wanneer de gegevens overdracht is voltooid, bevestigt de coördinator de door Voer met de back-upservice.

    ![Back-upproces](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Back-ups configureren in azure PostgreSQL-data bases

U kunt back-ups configureren op meerdere data bases op meerdere Azure PostgreSQL-servers. Zorg ervoor dat de vereiste [machtigingen](#prerequisite-permissions-for-configure-backup-and-restore) die de service nodig heeft om een back-up te maken van de post gres-servers al zijn geconfigureerd.

De volgende instructies zijn een stapsgewijze hand leiding voor het configureren van back-ups in de Azure PostgreSQL-data bases met behulp van Azure Backup:

1. Er zijn twee manieren om het proces te starten:

    1. Ga naar [Backup Center](backup-center-overview.md)-overzicht van back-  ->    ->  **UPS**.

        ![Ga naar Back-upcentrum](./media/backup-azure-database-postgresql/backup-center.png)

        Onder **initiëren: back-up configureren** selecteert u het **type gegevens bron** als **Azure database for PostgreSQL**.

        ![In initiëren: back-up configureren selecteert u gegevens bron type](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. U kunt ook rechtstreeks naar [back-upkluizen](backup-vault-overview.md)gaan  ->  .

        ![Ga naar back-upkluizen](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Back-up in back-upkluis selecteren](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. Selecteer onder **back-up configureren** de **back-upkluis** waarvan u een back-up wilt maken van uw post gres-data bases. Deze informatie wordt vooraf ingevuld als u zich al in de kluis context bevindt.

    ![Back-upkluis selecteren in back-up configureren](./media/backup-azure-database-postgresql/configure-backup.png)

1. Selecteer of maak een **back-upbeleid**.

    ![Back-upbeleid kiezen](./media/backup-azure-database-postgresql/backup-policy.png)

1. Selecteer resources of post gres-data bases waarvan u een back-up wilt maken.

    ![Resources selecteren waarvan u een back-up wilt maken](./media/backup-azure-database-postgresql/select-resources.png)

1. U kunt kiezen uit de lijst met alle Azure PostgreSQL-servers in abonnementen als deze zich in dezelfde regio bevinden als de kluis. Vouw de pijl uit om de lijst met data bases op een server weer te geven.

    ![Servers kiezen](./media/backup-azure-database-postgresql/choose-servers.png)

1. Deze controles worden uitgevoerd op de geselecteerde data bases om te controleren of de kluis machtigingen heeft voor het maken van een back-up van de geselecteerde post gres-servers en-data bases.
    1. Voor de voor **bereiding van back-ups** voor alle data bases moet het **volgende** zijn gelezen om door te gaan.
    1. Als er een fout optreedt, **verhelpt** u de fout en **valideert** of verwijdert u de data base uit de selecties.

    ![Validatie fouten om op te lossen](./media/backup-azure-database-postgresql/validation-errors.png)

1. Bevestig alle details onder **controleren en configureren** en selecteer **back-up configureren** om de bewerking te verzenden.

    ![Details in de revisie en configuratie bevestigen](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Zodra de **back-** upbewerking voor configureren is geactiveerd, wordt er een back-upexemplaar gemaakt. U kunt de status van de bewerking volgen in het deel venster [back-Upinstanties](backup-center-monitor-operate.md#backup-instances) in het Back-upcentrum of de kluis weergave.

    ![Back-upinstanties](./media/backup-azure-database-postgresql/backup-instances.png)

1. De back-ups worden geactiveerd volgens het back-upschema dat in het beleid is gedefinieerd. De taken kunnen worden bijgehouden onder [back-uptaken](backup-center-monitor-operate.md#backup-jobs). Op dit moment kunt u taken weer geven voor de afgelopen zeven dagen.

    ![Back-uptaken](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Back-upbeleid maken

1. Ga naar **Backup Center**  ->  **back-upbeleid**  ->  **toevoegen**. U kunt ook naar **back-upkluis** back-  ->  **upbeleid**  ->  **toevoegen**.

    ![Back-upbeleid toevoegen](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Voer een **naam** in voor het nieuwe beleid.

    ![Beleids naam invoeren](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Definieer het back-upschema. We ondersteunen momenteel **wekelijkse** back-ups. U kunt de back-ups plannen op een of meer dagen van de week.

    ![Het back-upschema definiëren](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Instellingen voor **retentie** definiëren. U kunt een of meer Bewaar regels toevoegen. Bij elke Bewaar regel wordt uitgegaan van invoer voor specifieke back-ups, en voor de gegevens opslag en de Bewaar periode voor deze back-ups.

1. U kunt ervoor kiezen om uw back-ups op te slaan in een van de twee gegevens archieven (of-lagen): **back-upgegevens archief** (Standard-laag) of **Archief gegevens archief** (in preview-versie). U kunt kiezen uit **twee laag opties** om te definiëren wanneer de back-ups trapsgewijs worden verdeeld over de twee gegevens sets:

    - Kies **onmiddellijk** kopiëren als u liever een back-up in zowel back-ups maken en Archief gegevens archieven hebt.
    - Klik **op verlopen** als u de back-up wilt verplaatsen om de gegevens opslag te archiveren tijdens het verlopen van het back-upgegevens archief.

1. De **standaard Bewaar regel** wordt toegepast wanneer er geen andere Bewaar regel is en heeft een standaard waarde van drie maanden.

    - De Bewaar duur ligt tussen zeven dagen en tien jaar in de **back-upgegevens opslag**.
    - De Bewaar duur ligt tussen zes maanden en tien jaar in de archief **gegevens opslag**.

    ![Bewaar duur bewerken](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>De Bewaar regels worden geëvalueerd in een vooraf bepaalde volg orde van prioriteit. De prioriteit is het hoogst voor de **jaar** regel, gevolgd door de **maand** en vervolgens de **wekelijkse** regel. Standaard instellingen voor bewaren worden toegepast wanneer er geen andere regels in aanmerking komen. Hetzelfde herstel punt kan bijvoorbeeld de eerste geslaagde back-up zijn die elke week wordt gemaakt, evenals de eerste geslaagde back-up die elke maand wordt gemaakt. Omdat de maandelijkse regel prioriteit echter hoger is dan die van de wekelijkse regel, geldt de Bewaar periode die overeenkomt met de eerste geslaagde back-up die elke maand wordt gemaakt.

## <a name="restore"></a>Herstellen

U kunt een Data Base herstellen naar een Azure PostgreSQL-server binnen hetzelfde abonnement, als de service de juiste set machtigingen heeft op de doel server. Zorg ervoor dat de vereiste [machtigingen](#prerequisite-permissions-for-configure-backup-and-restore) die de service nodig heeft om een back-up te maken van de post gres-servers al zijn geconfigureerd.

Volg deze stapsgewijze hand leiding voor het activeren van een herstel bewerking:

1. Er zijn twee manieren om het herstel proces te starten:
    1. Ga naar [Back-upcentrum](backup-center-overview.md)  ->  **overzicht**  ->  **herstellen**.

    ![Herstellen in Back-upcentrum selecteren](./media/backup-azure-database-postgresql/backup-center-restore.png)

    Onder **initiëren: herstellen** selecteert u het **type gegevens bron** als **Azure database for PostgreSQL**. Selecteer het **back-upexemplaar**.

    ![Gegevens bron type selecteren in initiëren: herstellen](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. U kunt ook rechtstreeks naar back-   ->  **upinstanties** van backup-kluis gaan. Selecteer een **back-upexemplaar** dat overeenkomt met de data base die u wilt herstellen.

    ![Back-upinstanties voor herstellen](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Lijst met back-upinstanties](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Selecteer herstellen](./media/backup-azure-database-postgresql/select-restore.png)

1. **Selecteer herstel punt** in de lijst met alle volledige back-ups die beschikbaar zijn voor het geselecteerde back-upexemplaar. Het meest recente herstel punt is standaard geselecteerd.

    ![Herstel punt selecteren](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Lijst met herstel punten](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Invoer **parameters herstellen**. Op dit moment kunt u kiezen uit twee soorten **herstel bewerkingen: herstellen als data base** en **herstellen als bestanden**.

1. **Herstellen als data base**: herstel de back-upgegevens om een nieuwe Data Base te maken in de doel postgresql-server.

    - De doel server kan hetzelfde zijn als de bron server. Het overschrijven van de oorspronkelijke Data Base wordt echter niet ondersteund.
    - U kunt kiezen tussen de server en alle abonnementen, maar in dezelfde regio als de kluis.
    - Selecteer **controleren + herstellen**. Hiermee wordt de validatie geactiveerd om te controleren of de service de juiste herstel machtigingen heeft op de doel server.

    ![Herstellen als data base](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Herstellen als bestanden**: de back-upbestanden dumpen naar het doel-opslag account (blobs).

    - U kunt kiezen uit de opslag accounts voor alle abonnementen, maar in dezelfde regio als de kluis.
    - Selecteer de doel container in de container lijst die is gefilterd op het geselecteerde opslag account.
    - Selecteer **controleren + herstellen**. Hiermee wordt de validatie geactiveerd om te controleren of de service de juiste herstel machtigingen heeft op de doel server.

    ![Herstellen als bestanden](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Controleer de informatie en selecteer **herstellen**. Hiermee wordt een bijbehorende herstel taak geactiveerd die kan worden gevolgd onder **back-uptaken**.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Vereiste machtigingen voor het configureren van back-up en herstel

Azure Backup volgt strikte beveiligings richtlijnen. Hoewel het een systeem eigen Azure-service is, worden de machtigingen voor de resource niet aangenomen en moeten ze expliciet door de gebruiker worden opgegeven.  Zo worden de referenties voor het maken van een verbinding met de data base niet opgeslagen. Dit is belang rijk om uw gegevens te beveiligen. In plaats daarvan gebruiken we Azure Active Directory-verificatie.

[Down load dit document](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) om een geautomatiseerd script en gerelateerde instructies op te halen. Er wordt een juiste set machtigingen verleend aan een Azure PostgreSQL-server voor back-up en herstel.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>De back-ups van Azure PostgreSQL-data bases beheren

Dit zijn de beheer bewerkingen die u kunt uitvoeren op de **back-Upinstanties**:

### <a name="on-demand-backup"></a>Back-ups op aanvraag

Als u een back-up wilt activeren die niet in het schema is opgegeven, gaat u naar **back-upexemplaaren**  ->  **Nu back-** up maken.
Kies uit de lijst met Bewaar regels die in het bijbehorende back-upbeleid zijn gedefinieerd.

![Nu back-up activeren](./media/backup-azure-database-postgresql/backup-now.png)

![Kies uit de lijst met Bewaar regels](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Beveiliging stoppen

U kunt de beveiliging van een back-upitem stoppen. Hierdoor worden ook de bijbehorende herstel punten voor dat back-upitem verwijderd. We bieden nog niet de mogelijkheid om de beveiliging te stoppen terwijl de bestaande herstel punten behouden blijven.

![Beveiliging stoppen](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Beleid wijzigen

U kunt het bijbehorende beleid wijzigen met een back-upexemplaar.

1. Selecteer het beleid voor het wijzigen van het **back-upexemplaar**  ->  .

    ![Beleid wijzigen](./media/backup-azure-database-postgresql/change-policy.png)

1. Selecteer het nieuwe beleid dat u op de Data Base wilt Toep assen.

    ![Beleid opnieuw toewijzen](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Problemen oplossen

In deze sectie vindt u informatie over het oplossen van back-ups van Azure PostgreSQL-data bases met Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

**Lees** toegang tot de back-Upkluis-MSI op de PG-server waarvan u een back-up wilt maken of die u wilt herstellen:

Azure Backup maakt gebruik van het verificatie model [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) om een beveiligde verbinding met de postgresql-data base tot stand te brengen. Dit betekent dat de back-upkluis alleen toegang heeft tot de resources die expliciet toestemming hebben gekregen van de gebruiker.

Er wordt automatisch een systeem-MSI toegewezen aan de kluis op het moment dat deze wordt gemaakt. U moet deze kluis MSI gebruiken om toegang te krijgen tot de PostgreSQL-servers waarvan u een back-up van data bases wilt maken.

Stappen:

1. Ga in de post gres-server naar het deel venster **Access Control (IAM)** .

    ![Access Control deel venster](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Selecteer **een roltoewijzing toevoegen**.

    ![Roltoewijzing toevoegen](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. In het rechterdeel venster dat wordt geopend, voert u het volgende in:<br>

    **Rol:** Gelezen<br>
    **Toegang toewijzen aan:** **Back-upkluis** kiezen<br>
    Als u de optie **back-upkluis** niet kunt vinden in de vervolg keuzelijst, kiest u de **optie Azure AD-gebruiker,-groep of Service-Principal**<br>

    ![Rol selecteren](./media/backup-azure-database-postgresql/select-role.png)

    **Selecteer:** Voer de naam van de back-upkluis in waarvoor u een back-up wilt maken van deze server en de bijbehorende data bases.<br>

    ![Naam van de back-upkluis invoeren](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Maak een database back-upgebruiker die kan worden geverifieerd met Azure Active Directory:

Deze fout kan optreden als er geen Azure Active Directory beheerder voor de PostgreSQL-server is of als er geen back-upgebruiker is die kan worden geverifieerd met behulp van Azure Active Directory.

Stappen:

Een Active Directory beheerder toevoegen aan de OSS-server:

Deze stap is vereist om verbinding te maken met de data base via een gebruiker die kan worden geverifieerd met Azure Active Directory in plaats van een wacht woord. De gebruiker van de Azure AD-beheerder in Azure Database for PostgreSQL heeft de rol **azure_ad_admin**. Alleen een **azure_ad_admin** rol kan nieuwe database gebruikers maken die kunnen worden geverifieerd met Azure AD.

1. Ga naar het tabblad Active Directory beheer in het navigatie deel venster links van de server weergave en voeg uzelf (of iemand anders) toe als Active Directory beheerder.

    ![Active Directory beheerder instellen](./media/backup-azure-database-postgresql/set-admin.png)

1. Zorg ervoor dat u de gebruikers instelling voor AD-beheerders **opslaat** .

    ![Gebruikers instelling voor Active Directory beheerder opslaan](./media/backup-azure-database-postgresql/save-admin-setting.png)

Raadpleeg [dit document](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) voor een lijst met stappen die u moet uitvoeren om de benodigde stappen te volt ooien.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Stel de netwerk regel in door de vlag **toegang tot Azure-Services toestaan** in te scha kelen in de server weergave. Stel in de server weergave, onder het deel venster **verbindings beveiliging** , de vlag **toegang tot Azure-Services toestaan** in op **Ja**.

![Toegang tot Azure-services toestaan](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Machtiging voor het herstellen van een opslag account container bij het herstellen als bestanden

1. Geef de back-upkluis-MSI de machtiging om toegang te krijgen tot de containers van het opslag account met behulp van de Azure Portal.
    1. Ga naar het **opslag account**  ->  **Access Control**  ->  **roltoewijzing toe te voegen**.
    1. Wijs de rol **gegevensinzender voor opslag BLOB** toe aan het MSI-bestand van de back-upkluis.

    ![Rol Inzender gegevens voor BLOB toewijzen](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. U kunt ook gedetailleerde machtigingen geven voor de specifieke container waarnaar u wilt herstellen met behulp van de opdracht Azure CLI [AZ Role Assignment maken](/cli/azure/role/assignment) .

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Vervang de para meter assigned door de **toepassings-id** van het MSI-bestand van de kluis en de bereik parameter om naar uw specifieke container te verwijzen.
    1. Als u de **toepassings-id** van de kluis-MSI wilt ophalen, selecteert u **alle toepassingen** onder **toepassings type**:

        ![Alle toepassingen selecteren](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Zoek de naam van de kluis en kopieer de toepassings-ID:

        ![Zoeken naar kluis naam](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van back-upkluizen](backup-vault-overview.md)
