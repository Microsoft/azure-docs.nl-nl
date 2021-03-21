---
title: Back-up maken van Azure Managed Disks
description: Meer informatie over het maken van een back-up van Azure Managed Disks vanuit de Azure Portal.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: ca86550c4dec4b51c60d9ecdef124e38783a3764
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738149"
---
# <a name="back-up-azure-managed-disks-in-preview"></a>Back-up van Azure Managed Disks (in preview-versie)

>[!IMPORTANT]
>Azure Disk Backup is in Preview zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor de beschik baarheid van regio's.
>
>[Vul dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) in om u aan te melden voor de preview-versie.

In dit artikel wordt uitgelegd hoe u een back-up van [Azure Managed Disk](../virtual-machines/managed-disks-overview.md) maakt vanuit het Azure Portal.

In dit artikel leert u het volgende:

- Een back-upkluis maken

- Maak een back-upbeleid

- Een back-up van een Azure-schijf configureren

- Een back-uptaak op aanvraag uitvoeren

Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor meer informatie over de beschik baarheid van Azure schijf back-upregio, ondersteunde scenario's en beperkingen.

## <a name="create-a-backup-vault"></a>Een back-upkluis maken

Een back-upkluis is een opslag entiteit in azure die back-upgegevens bevat voor verschillende nieuwere werk belastingen die Azure Backup ondersteunt, zoals Azure Database for PostgreSQL servers en Azure-schijven. Back-upkluizen maken het eenvoudig om uw back-upgegevens te organiseren en zo de beheer overhead te minimaliseren. Back-upkluizen zijn gebaseerd op het Azure Resource Manager model van Azure, dat uitgebreide mogelijkheden biedt voor het beveiligen van back-upgegevens.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).
1. Typ **Backup Center** in het zoekvak.
1. Onder **Services** selecteert u **Back-upcentrum**.
1. Selecteer op de pagina **Back-upcentrum** de optie **kluis**.

   ![Kluis in Back-upcentrum selecteren](./media/backup-managed-disks/backup-center.png)

1. Selecteer **back-upkluis** in het scherm **initiëren: kluis maken** en **Ga door**.

   ![Initiëren: kluis maken](./media/backup-managed-disks/initiate-create-vault.png)

1. Op het tabblad **basis beginselen** geeft u het abonnement, de resource groep, de naam van de back-upkluis, de regio en de redundantie opslag voor back-ups op. Ga door **en selecteer controleren + maken**. Meer informatie over het [maken van een back-upkluis](./backup-vault-overview.md#create-a-backup-vault).

   ![Een kluis controleren en maken](./media/backup-managed-disks/review-and-create.png)

## <a name="create-backup-policy"></a>Back-upbeleid maken

1. Ga in de *DemoVault* **-back-upkluis** die u in de vorige stap hebt gemaakt naar **back-upbeleid** en selecteer **toevoegen**.

   ![Back-upbeleid toevoegen](./media/backup-managed-disks/backup-policies.png)

1. Geef op het tabblad **basis beginselen** beleids naam op, selecteer **gegevens bron type** als **Azure-schijf**. De kluis is al vooraf ingevuld en de geselecteerde kluis eigenschappen worden weer gegeven.

   >[!NOTE]
   > Hoewel de geselecteerde kluis de instelling voor globale redundantie kan hebben, ondersteunt momenteel alleen de gegevens opslag van moment opnamen van Azure-schijven. Alle back-ups worden opgeslagen in een resource groep in uw abonnement en worden niet gekopieerd naar backup-kluis opslag.

   ![Gegevens bron type selecteren](./media/backup-managed-disks/datasource-type.png)

1. Selecteer op het tabblad **back-upbeleid** de frequentie van het back-upschema.

   ![Frequentie van back-upschema selecteren](./media/backup-managed-disks/backup-schedule-frequency.png)

   Azure Disk Backup biedt meerdere back-ups per dag. Als u vaker back-ups nodig hebt, kiest u de frequentie van de back-up **per uur** met de mogelijkheid om back-ups te maken met intervallen van elke 4, 6, 8 of 12 uur. De back-ups worden gepland op basis van het geselecteerde **tijds** interval. Als u bijvoorbeeld **elke vier uur** selecteert, worden de back-ups op ongeveer de interval van elke 4 uur genomen, zodat de back-ups gelijkmatig over de hele dag worden gedistribueerd. Als een back-up van één dag voldoende is, kiest u de frequentie van **dagelijkse** back-ups. In de dagelijkse back-upfrequentie kunt u het tijdstip van de dag opgeven wanneer uw back-ups worden gemaakt. Het is belang rijk te weten dat de tijd van de dag de begin tijd van de back-up aangeeft en niet de tijd waarop de back-up is voltooid. De tijd die nodig is voor het volt ooien van de back-upbewerking is afhankelijk van verschillende factoren, zoals de grootte van de schijf en het verloop frequentie tussen opeenvolgende back-ups. Azure Disk Backup is echter een back-up zonder agent die gebruikmaakt van [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md), wat geen invloed heeft op de prestaties van de productie-toepassing.

1. Op het tabblad **back-upbeleid** selecteert u instellingen voor retentie die voldoen aan de vereiste voor de Recovery Point OBJECTIVE (RPO).

   De standaard Bewaar regel is van toepassing als er geen andere Bewaar regel is opgegeven. De standaard Bewaar regel kan worden gewijzigd om de Bewaar duur te wijzigen, maar kan niet worden verwijderd. U kunt een nieuwe Bewaar regel toevoegen door een **Bewaar regel toevoegen** te selecteren.

   ![Een Bewaar regel toevoegen](./media/backup-managed-disks/add-retention-rule.png)

   U kunt kiezen voor de **eerste geslaagde back-up** die dagelijks of wekelijks is uitgevoerd, en de Bewaar periode opgeven dat de specifieke back-ups moeten worden bewaard voordat ze worden verwijderd. Deze optie is handig om specifieke back-ups van de dag of week gedurende een langere periode te bewaren. Alle andere frequente back-ups kunnen voor een kortere duur worden bewaard.

   ![Bewaar instellingen](./media/backup-managed-disks/retention-settings.png)

   >[!NOTE]
   >Azure Backup voor Managed Disks gebruikt incrementele moment opnamen die zijn beperkt tot 200 moment opnamen per schijf. Als u back-ups op aanvraag wilt maken, afgezien van geplande back-ups, beperkt het back-upbeleid de totale back-ups tot 180. Meer informatie over [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md#restrictions) voor beheerde schijven.

1. Voltooi het back-upbeleid door op **revisie + maken** te klikken.

## <a name="configure-backup"></a>Back-up configureren

Backup-kluis maakt gebruik van beheerde identiteit voor toegang tot andere Azure-resources. Voor het configureren van een back-up van beheerde schijven, moet de beheerde identiteit van de back-upkluis een set machtigingen hebben op de bron schijven en resource groepen waarop moment opnamen worden gemaakt en beheerd.

Een door het systeem toegewezen beheerde identiteit is beperkt tot één per resource en is verbonden met de levens cyclus van deze resource. U kunt machtigingen verlenen aan de beheerde identiteit door gebruik te maken van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Beheerde identiteit is een service-principal van een speciaal type dat alleen kan worden gebruikt met Azure-resources. Meer informatie over [beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md).

De volgende vereisten zijn vereist voor het configureren van een back-up van beheerde schijven:

1. Wijs de rol van **schijf back-uplezer** toe aan de beheerde identiteit van de back-upkluis op de bron schijf waarvan een back-up moet worden gemaakt.

   1. Ga naar de schijf waarvan een back-up moet worden gemaakt.

   1. Ga naar **toegangs beheer (IAM)** en selecteer **roltoewijzingen toevoegen**

   1. Selecteer in het rechterdeel venster de optie **schijf back-uplezer** in de vervolg keuzelijst **rol** . Selecteer de beheerde identiteit van de back-upkluis en **Sla** deze op.

   >[!TIP]
   >Typ de naam van de back-upkluis om de beheerde identiteit van de kluis te selecteren.

   ![De functie lezer voor schijf back-ups toevoegen](./media/backup-managed-disks/disk-backup-reader-role.png)

1. Wijs de rol **Inzender voor schijf momentopname** toe aan de beheerde identiteit van de back-upkluis in de resource groep waar back-ups worden gemaakt en beheerd door de Azure backup-service. De moment opnamen van de schijf worden opgeslagen in een resource groep in uw abonnement. Als u Azure Backup-service wilt toestaan om moment opnamen te maken, op te slaan en te beheren, moet u machtigingen voor de back-upkluis opgeven.

   **Resource groep kiezen voor het opslaan en beheren van moment opnamen:**

   - Selecteer niet dezelfde resource groep als die van de bron schijf.

   - Als richt lijn is het raadzaam om een speciale resource groep te maken als een gegevens Archief voor moment opnamen dat door de Azure Backup-service moet worden gebruikt. Als u een speciale resource groep hebt, kunt u toegangs machtigingen voor de resource groep beperken, waardoor de back-upgegevens veilig en eenvoudig kunnen worden beheerd.

   - U kunt deze resource groep gebruiken voor het opslaan van moment opnamen op meerdere schijven waarvan een back-up wordt gemaakt (of gepland).  

   - U kunt geen incrementele moment opname maken voor een bepaalde schijf buiten het abonnement van de schijf. Kies de resource groep in hetzelfde abonnement als die van de schijf waarvan een back-up moet worden gemaakt. Meer informatie over [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md#restrictions) voor beheerde schijven.

   Voer de volgende stappen uit om de rol toe te wijzen:

   1. Ga naar de resource groep. De resource groep is bijvoorbeeld *SnapshotRG*, die zich in hetzelfde abonnement bevindt als de schijf waarvan een back-up moet worden gemaakt.

   1. Ga naar **toegangs beheer (IAM)** en selecteer **roltoewijzingen toevoegen**.

   1. Selecteer in het rechterdeel venster de optie voor de **schijf momentopname Inzender** in de vervolg keuzelijst met **rollen** . Selecteer de beheerde identiteit van de back-upkluis en **Sla** deze op.

   >[!TIP]
   >Typ de naam van de back-upkluis om de beheerde identiteit van de kluis te selecteren.

   ![Rol Inzender voor schijf momentopname toevoegen](./media/backup-managed-disks/disk-snapshot-contributor-role.png)

1. Controleer of de beheerde identiteit van de back-upkluis de juiste set roltoewijzingen heeft op de bron schijf en de resource groep die fungeert als moment opname van de gegevens opslag.

   1. Ga naar **back-upkluis-> identiteit** en selecteer **Azure-roltoewijzingen**.

      ![Azure-roltoewijzingen selecteren](./media/backup-managed-disks/azure-role-assignments.png)

   1. Controleer of de rol, de resource naam en het resource type correct worden weer gegeven.

      ![De rol, resource naam en resource type controleren](./media/backup-managed-disks/verify-role.png)

   >[!NOTE]
   >Terwijl de roltoewijzingen op de juiste manier worden weer gegeven in de portal, kan het ongeveer 15 minuten duren voordat de machtiging wordt toegepast op de beheerde identiteit van de back-upkluis.

1. Als aan de vereisten wordt voldaan, gaat u naar **back-upkluis-> overzicht** en selecteert u **back-up** om het configureren van de back-up van de schijven te starten.

   ![Back-up selecteren](./media/backup-managed-disks/select-backup.png)

1. Op het tabblad **basis beginselen** selecteert u **Azure Disk** als het type gegevens bron.

   ![Azure-schijf selecteren](./media/backup-managed-disks/select-azure-disk.png)

   >[!NOTE]
   >Azure Backup gebruikt [incrementele moment opnamen](../virtual-machines/disks-incremental-snapshots.md#restrictions) van Managed disks, waarbij alleen de Delta wijzigingen op de schijf worden opgeslagen sinds de laatste moment opname op Standard-HDD opslag, ongeacht het opslag type van de bovenliggende schijf. Voor extra betrouw baarheid worden incrementele moment opnamen standaard op zone redundant Storage (ZRS) opgeslagen in regio's die ondersteuning bieden voor ZRS. Op dit moment ondersteunt Azure Disk Backup een operationele back-up van beheerde schijven waarbij de back-ups niet worden gekopieerd naar back-upkluis. Daarom is de instelling back-upopslaglocatie van back-ups van backup-kluis niet van toepassing op de herstel punten.

1. Kies op het tabblad **back-upbeleid** een back-upbeleid.

   ![Back-upbeleid kiezen](./media/backup-managed-disks/choose-backup-policy.png)

1. Selecteer op het tabblad **resources** de optie **Azure-schijf selecteren**. Selecteer in het rechterdeel venster de schijven waarvan u een back-up wilt maken.

   ![Schijven selecteren waarvan u een back-up wilt maken](./media/backup-managed-disks/select-disks-to-backup.png)

   >[!NOTE]
   >In de portal kunt u meerdere schijven selecteren en een back-up configureren, elke schijf is een afzonderlijk back-upexemplaar. Momenteel ondersteunt Azure-schijf back-ups alleen back-ups van afzonderlijke schijven. Punt-in-time-back-ups van meerdere schijven die zijn gekoppeld aan een virtuele schijf, worden niet ondersteund.
   >
   >Wanneer u de portal gebruikt, bent u beperkt tot het selecteren van schijven binnen hetzelfde abonnement. Als u een back-up wilt maken van meerdere schijven of als de schijven over een ander abonnement zijn verdeeld, kunt u scripts gebruiken om te automatiseren.
   >
   >Zie de [ondersteunings matrix](disk-backup-support-matrix.md)voor meer informatie over de beschik baarheid van Azure schijf back-upregio, ondersteunde scenario's en beperkingen.

1. Selecteer een **resource groep voor moment opnamen** en selecteer **valideren**. Dit is de resource groep waar de Azure Backup-service is gemaakt en de incrementele moment opnamen voor de back-upkluis worden beheerd. De beheerde identiteit wordt toegewezen aan de vereiste rolmachtigingen als onderdeel van de vereisten.

   ![Resource groep voor moment opnamen selecteren](./media/backup-managed-disks/select-snapshot-resource-group.png)

   >[!NOTE]
   >Het kan enkele minuten duren voordat de validatie is voltooid voordat u de configuratie van de back-up kunt volt ooien. De validatie kan mislukken als:
   >
   > - een schijf wordt niet ondersteund. Raadpleeg de [ondersteunings matrix](disk-backup-support-matrix.md) voor niet-ondersteunde scenario's.
   > - de beheerde identiteit van de back-upkluis heeft geen geldige roltoewijzingen op de **schijf** waarvan een back-up moet worden gemaakt of voor de **resource groep voor moment opnamen** waar incrementele moment opnamen worden opgeslagen.

1. Nadat de validatie is voltooid, selecteert u **controleren en configureren** om de back-up van de geselecteerde schijven te configureren.

## <a name="run-an-on-demand-backup"></a>Een on-demand back-up uitvoeren

1. Ga in de *DemoVault* **-back-upkluis** die u in de vorige stap hebt gemaakt naar **back-ups** en selecteer een back-upexemplaar.

   ![Back-upexemplaar selecteren](./media/backup-managed-disks/select-backup-instance.png)

1. In het scherm **back-upinstanties** vindt u het volgende:

   - **essentiële** informatie, waaronder de naam van de bron schijf, de resource groep voor moment opnamen waarin incrementele moment opnamen worden opgeslagen, de back-upkluis en het back-upbeleid.
   - **Taak status** met een samen vatting van de back-up-en herstel bewerkingen en de bijbehorende status in de afgelopen zeven dagen.
   - Een lijst met **herstel punten** voor de geselecteerde tijds periode.

1. Selecteer **back-up** om een back-up op aanvraag te initiëren.

   ![Selecteer nu back-up](./media/backup-managed-disks/backup-now.png)

1. Selecteer een van de Bewaar regels die zijn gekoppeld aan het back-upbeleid. Met deze Bewaar regel wordt de Bewaar duur van deze back-up op aanvraag bepaald. Selecteer **Nu back-up** om de back-up te starten.

   ![Back-up initiëren](./media/backup-managed-disks/initiate-backup.png)

## <a name="track-a-backup-operation"></a>Een back-upbewerking volgen

De Azure Backup-service maakt een taak voor geplande back-ups of als u een back-upbewerking op aanvraag voor het bijhouden van gegevens uitvoert. De status van de back-uptaak weer geven:

1. Ga naar het scherm voor het maken van een **back-upexemplaar** . Het dash board taken wordt weer gegeven met de werking en status van de afgelopen zeven dagen.

   ![Taken dashboard](./media/backup-managed-disks/jobs-dashboard.png)

1. Als u de status van de back-upbewerking wilt weer geven, selecteert u **alles weer** geven om de lopende en eerdere taken van deze back-upinstantie weer te geven.

   ![Selecteer alles weer geven](./media/backup-managed-disks/view-all.png)

1. Bekijk de lijst met back-up-en herstel taken en hun status. Selecteer een taak in de lijst met taken om de taak details weer te geven.

   ![Taak selecteren om details weer te geven](./media/backup-managed-disks/select-job.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Managed Disks herstellen](restore-managed-disks.md)