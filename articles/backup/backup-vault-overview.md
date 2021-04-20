---
title: Overzicht van Backup-kluizen
description: Een overzicht van Backup-kluizen.
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: e2d720da9474a35870de01559201d22c9e5b567f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739076"
---
# <a name="backup-vaults-overview"></a>Overzicht van Back-upkluizen

In dit artikel worden de functies van een Backup-kluis beschreven. Een Backup-kluis is een opslagentiteit in Azure met back-upgegevens voor bepaalde nieuwere workloads die Azure Backup ondersteunt. U kunt Backup-kluizen gebruiken voor het opslaan van back-upgegevens voor verschillende Azure-services, zoals Azure Database for PostgreSQL-servers en nieuwere workloads die Azure Backup ondersteunen. Met back-upkluizen kunt u uw back-upgegevens eenvoudig ordenen, terwijl de beheeroverhead wordt geminim hetzelfde. Back-upkluizen zijn gebaseerd op Azure Resource Manager model van Azure, dat functies biedt zoals:

- **Verbeterde mogelijkheden voor het beveiligen van back-upgegevens:** met Backup-kluizen biedt Azure Backup beveiligingsmogelijkheden voor het beveiligen van back-ups in de cloud. De beveiligingsfuncties zorgen ervoor dat u uw back-ups kunt beveiligen en gegevens veilig kunt herstellen, zelfs als de productie- en back-upservers zijn aangetast. [Meer informatie](backup-azure-security-feature.md)

- **Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)**: Azure RBAC biedt een fijner toegangsbeheerbeheer in Azure. [Azure biedt verschillende ingebouwde rollen en](../role-based-access-control/built-in-roles.md)Azure Backup drie ingebouwde rollen voor [het beheren van herstelpunten.](backup-rbac-rs-vault.md) Back-upkluizen zijn compatibel met Azure RBAC, waarmee back-up- en hersteltoegang wordt beperkt tot de gedefinieerde set gebruikersrollen. [Meer informatie](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Opslaginstellingen in de Back-upkluis

Een Backup-kluis is een entiteit die de back-ups en herstelpunten op slaat die in de tijd zijn gemaakt. De Backup-kluis bevat ook het back-upbeleid dat is gekoppeld aan de beveiligde virtuele machines.

- Azure Backup verwerkt automatisch de opslag voor de kluis. Kies de opslag redundantie die overeenkomt met de behoeften van uw bedrijf bij het maken van de Backup-kluis.

- Zie deze artikelen over geografische en [](../storage/common/storage-redundancy.md#geo-redundant-storage) lokale redundantie voor meer informatie over opslag [redundantie.](../storage/common/storage-redundancy.md#locally-redundant-storage)

## <a name="encryption-settings-in-the-backup-vault"></a>Versleutelingsinstellingen in de Back-upkluis

In deze sectie worden de beschikbare opties besproken voor het versleutelen van uw back-upgegevens die zijn opgeslagen in de Back-upkluis. Azure Backup-service gebruikt de **app Backup Management Service** voor toegang tot Azure Key Vault, maar niet de beheerde identiteit van de Backup-kluis.


### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Versleuteling van back-upgegevens met behulp van door het platform beheerde sleutels

Standaard worden al uw gegevens versleuteld met behulp van door het platform beheerde sleutels. U hoeft geen expliciete actie van uw kant uit te voeren om deze versleuteling in teschakelen. Dit is van toepassing op alle workloads van waar een back-up van wordt gemaakt in uw Backup-kluis.

## <a name="create-a-backup-vault"></a>Een Backup-kluis maken

Een Backup-kluis is een beheerentiteit die herstelpunten op slaat die in de tijd zijn gemaakt en een interface biedt voor het uitvoeren van back-upgerelateerde bewerkingen. Dit omvat het maken van back-ups op aanvraag, het uitvoeren van herstelbewerkingen en het maken van back-upbeleid.

Volg deze stappen om een Backup-kluis te maken.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Back-upkluis maken

1. Typ **Backup-kluizen** in het zoekvak.
1. Selecteer **back-upkluizen onder** **Services.**
1. Selecteer op **de pagina Back-upkluizen** de optie **Toevoegen.**
1. Zorg ervoor **dat op het tabblad** Basisinformatie onder **Projectdetails** het juiste abonnement is geselecteerd en kies **vervolgens Nieuwe** resourcegroep maken. Typ *myResourceGroup* als de naam.

  ![Nieuwe resourcegroep maken](./media/backup-vault-overview/new-resource-group.png)

1. Onder **Exemplaardetails** typt u *myVault* als naam van de Back-upkluis en kiest u de regio van uw keuze, in dit geval VS - oost *voor* uw **regio**. 
1. Kies nu uw **Opslag redundantie**. Opslag redundantie kan niet worden gewijzigd na het beveiligen van items in de kluis.
1. Als u Azure als primair eindpunt voor **back-upopslag** gebruikt, kunt u het beste de standaardinstelling Geografisch redundant blijven gebruiken.
1. Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt.
1. Meer informatie over [geografische](../storage/common/storage-redundancy.md#geo-redundant-storage) en [lokale](../storage/common/storage-redundancy.md#locally-redundant-storage) redundantie.

  ![Opslag redundantie kiezen](./media/backup-vault-overview/storage-redundancy.png)

1. Selecteer de knop Beoordelen en maken onderaan de pagina.

    ![Selecteer Beoordelen en maken](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Een Backup-kluis verwijderen

In deze sectie wordt beschreven hoe u een Backup-kluis verwijdert. Het bevat instructies voor het verwijderen van afhankelijkheden en vervolgens het verwijderen van een kluis.

### <a name="before-you-start"></a>Voordat u begint

U kunt een Backup-kluis met een van de volgende afhankelijkheden niet verwijderen:

- U kunt een kluis met beveiligde gegevensbronnen (bijvoorbeeld Azure Database for PostgreSQL-servers) niet verwijderen.
- U kunt een kluis met back-upgegevens niet verwijderen.

Als u de kluis probeert te verwijderen zonder de afhankelijkheden te verwijderen, worden de volgende foutberichten weergegeven:

>Kan de Backup-kluis niet verwijderen omdat er bestaande back-up-exemplaren of back-upbeleidsregels in de kluis zijn. Verwijder alle back-up-exemplaren en back-upbeleidsregels die aanwezig zijn in de kluis en verwijder vervolgens de kluis.

### <a name="proper-way-to-delete-a-vault"></a>De juiste manier om een kluis te verwijderen

>[!WARNING]
De volgende bewerking is destructief en kan niet ongedaan worden gemaakt. Alle back-upgegevens en back-upitems die aan de beveiligde server zijn gekoppeld, worden permanent verwijderd. Ga zorgvuldig te werk.

Als u een kluis op de juiste manier wilt verwijderen, moet u de stappen in deze volgorde volgen:

- Controleer of er beveiligde items zijn:
  - Ga in de linkernavigatiebalk naar **Backup Instances.** Alle items die hier worden vermeld, moeten eerst worden verwijderd.

Nadat u deze stappen hebt voltooid, kunt u doorgaan met het verwijderen van de kluis.

### <a name="delete-the-backup-vault"></a>De Backup-kluis verwijderen

Wanneer er geen items meer in de kluis staan, selecteert u **Verwijderen** op het kluisdashboard. U ziet een bevestigingstekst waarin u wordt gevraagd of u de kluis wilt verwijderen.

![Kluis verwijderen](./media/backup-vault-overview/delete-vault.png)

1. Selecteer **Ja** om te controleren of u de kluis wilt verwijderen. De kluis wordt verwijderd. De portal gaat terug naar het menu **Nieuwe** service.

## <a name="monitor-and-manage-the-backup-vault"></a>De Backup-kluis bewaken en beheren

In deze sectie wordt uitgelegd  hoe u het overzichtsdashboard van backupkluis gebruikt om uw Backup-kluizen te bewaken en te beheren. Het overzichtsdeelvenster bevat twee tegels: **Taken** **en Exemplaren.**

![Overzichtsdashboard](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Back-up-exemplaren beheren

Op de **tegel Taken** ziet u een overzicht van alle back-up- en hersteltaken in uw Backup-kluis. Als u een van de getallen in deze tegel selecteert, kunt u meer informatie bekijken over taken voor een bepaald gegevensbrontype, bewerkingstype en status.

![Back-up-exemplaren](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Back-uptaken beheren

Op de **tegel Back-up-exemplaren** ziet u een overzicht van alle back-up-exemplaren in uw Backup-kluis. Als u een van de getallen in deze tegel selecteert, kunt u meer informatie weergeven over back-up-exemplaren voor een bepaald type gegevensbron en de beveiligingsstatus.

![Back-uptaken](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Volgende stappen

- [Back-up configureren in Azure PostgreSQL-databases](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
