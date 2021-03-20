---
title: Overzicht van back-upkluizen
description: Een overzicht van back-upkluizen.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: c189997ecc4814917182246b35003649d317ac77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92091284"
---
# <a name="backup-vaults-overview"></a>Overzicht van back-upkluizen

In dit artikel worden de functies van een back-upkluis beschreven. Een back-upkluis is een opslag entiteit in azure waarmee back-upgegevens worden opgeslagen voor bepaalde nieuwere werk belastingen die Azure Backup ondersteunt. U kunt back-upkluizen gebruiken voor het bewaren van back-upgegevens voor verschillende Azure-Services, zoals Azure Database for PostgreSQL servers en nieuwere werk belastingen die Azure Backup zullen ondersteunen. Back-upkluizen maken het eenvoudig om uw back-upgegevens te organiseren en zo de beheer overhead te minimaliseren. Back-upkluizen zijn gebaseerd op het Azure Resource Manager model van Azure, dat functies biedt zoals:

- **Verbeterde mogelijkheden voor het beveiligen van back-upgegevens**: met back-upkluizen biedt Azure backup beveiligings mogelijkheden voor het beveiligen van Cloud back-ups. Met de beveiligings functies kunt u uw back-ups beveiligen en veilig gegevens herstellen, zelfs als er inbreuk is gemaakt op de productie-en back-upservers. [Meer informatie](backup-azure-security-feature.md)

- **Toegangs beheer op basis van rollen (Azure RBAC)** van Azure: Azure RBAC biedt nauw keurig beheer van toegangs beheer in Azure. [Azure biedt verschillende ingebouwde rollen](../role-based-access-control/built-in-roles.md)en Azure backup heeft drie [ingebouwde rollen voor het beheren van herstel punten](backup-rbac-rs-vault.md). Back-upkluizen zijn compatibel met Azure RBAC, waarmee back-up-en herstel toegang wordt beperkt tot de gedefinieerde set gebruikers rollen. [Meer informatie](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>Opslag instellingen in de back-upkluis

Een back-upkluis is een entiteit waarin de back-ups en herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt. De back-upkluis bevat ook het back-upbeleid dat is gekoppeld aan de beveiligde virtuele machines.

- Azure Backup beheert automatisch de opslag voor de kluis. Kies de opslag redundantie die overeenkomt met uw bedrijfs behoeften bij het maken van de back-upkluis.

- Zie deze artikelen over [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) en [lokale](../storage/common/storage-redundancy.md#locally-redundant-storage) redundantie voor meer informatie over opslag redundantie.

## <a name="encryption-settings-in-the-backup-vault"></a>Versleutelings instellingen in de back-upkluis

In deze sectie worden de opties beschreven die beschikbaar zijn voor het versleutelen van uw back-upgegevens die zijn opgeslagen in de back-upkluis.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Versleuteling van back-upgegevens met door het platform beheerde sleutels

Standaard worden al uw gegevens versleuteld met sleutels die door het platform worden beheerd. U hoeft geen expliciete actie van uw end uit te voeren om deze versleuteling in te scha kelen. Dit is van toepassing op alle werk belastingen waarvan een back-up wordt gemaakt naar uw back-upkluis.

## <a name="create-a-backup-vault"></a>Een back-upkluis maken

Een back-upkluis is een beheer entiteit waarmee herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt en die een interface biedt voor het uitvoeren van back-ups. Dit omvat het maken van back-ups op aanvraag, het uitvoeren van herstelbewerkingen en het maken van back-upbeleid.

Voer de volgende stappen uit om een back-upkluis te maken.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op <https://portal.azure.com>.

### <a name="create-backup-vault"></a>Back-upkluis maken

1. Typ **back-upkluizen** in het zoekvak.
1. Selecteer bij **Services** **back-upkluizen**.
1. Selecteer **toevoegen** op de pagina **back-upkluizen** .
1. Controleer op het **tabblad basis beginselen** onder **Project Details** of het juiste abonnement is geselecteerd en kies nieuwe resource groep **maken** . Typ *myResourceGroup* als de naam.

  ![Nieuwe resource groep maken](./media/backup-vault-overview/new-resource-group.png)

1. Typ *myVault* voor de naam van de **back-upkluis** onder Details van het **exemplaar** en kies uw keuze gebied, in dit geval *VS-Oost* voor uw **regio**.
1. Kies nu uw **opslag redundantie**. Opslag redundantie kan niet worden gewijzigd na het beveiligen van items in de kluis.
1. Als u Azure als een primair eind punt voor back-upopslag gebruikt, wordt u aangeraden de standaard **geo-redundante** instelling te gebruiken.
1. Als Azure niet uw primaire eindpunt is voor back-upopslag, kiest u **Lokaal redundant**, zodat u de kosten voor Azure-opslag verlaagt.
1. Meer informatie over [geo](../storage/common/storage-redundancy.md#geo-redundant-storage) en [lokale](../storage/common/storage-redundancy.md#locally-redundant-storage) redundantie.

  ![Opslag redundantie kiezen](./media/backup-vault-overview/storage-redundancy.png)

1. Selecteer de knop controleren + maken onder aan de pagina.

    ![Selecteer controleren + maken](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Een back-upkluis verwijderen

In deze sectie wordt beschreven hoe u een back-upkluis verwijdert. Het bevat instructies voor het verwijderen van afhankelijkheden en het verwijderen van een kluis.

### <a name="before-you-start"></a>Voordat u begint

U kunt een back-upkluis niet verwijderen met een van de volgende afhankelijkheden:

- U kunt geen kluis verwijderen die beveiligde gegevens bronnen bevat (bijvoorbeeld Azure data base for PostgreSQL-servers).
- U kunt geen kluis verwijderen die back-upgegevens bevat.

Als u probeert de kluis te verwijderen zonder de afhankelijkheden te verwijderen, ontvangt u de volgende fout berichten:

>Kan de back-upkluis niet verwijderen omdat er bestaande back-upexemplaarën of back-upbeleid in de kluis zijn. Verwijder alle back-upinstanties en back-upbeleiden die aanwezig zijn in de kluis en verwijder vervolgens de kluis.

### <a name="proper-way-to-delete-a-vault"></a>Juiste manier om een kluis te verwijderen

>[!WARNING]
De volgende bewerking is destructief en kan niet ongedaan worden gemaakt. Alle back-upgegevens en back-upitems die aan de beveiligde server zijn gekoppeld, worden definitief verwijderd. Ga zorgvuldig te werk.

Als u een kluis goed wilt verwijderen, moet u de stappen in deze volg orde volgen:

- U moet controleren of er beveiligde items zijn:
  - Ga naar **back-Upinstanties** in de linkernavigatiebalk. Alle items die hier worden vermeld, moeten eerst worden verwijderd.

Nadat u deze stappen hebt voltooid, kunt u door gaan met het verwijderen van de kluis.

### <a name="delete-the-backup-vault"></a>Back-upkluis verwijderen

Als er zich geen items meer in de kluis bevinden, selecteert u **verwijderen** op het kluis dashboard. U ziet een bevestigings tekst waarin wordt gevraagd of u de kluis wilt verwijderen.

![Kluis verwijderen](./media/backup-vault-overview/delete-vault.png)

1. Selecteer **Ja** om te controleren of u de kluis wilt verwijderen. De kluis wordt verwijderd. De portal keert terug naar het menu **nieuwe** service.

## <a name="monitor-and-manage-the-backup-vault"></a>De back-upkluis bewaken en beheren

In deze sectie wordt uitgelegd hoe u het **overzichts** dashboard back-upkluis gebruikt om uw back-upkluizen te controleren en te beheren. Het deel venster overzicht bevat twee tegels: **taken** en **exemplaren**.

![Overzichts dashboard](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>Back-upinstanties beheren

In de tegel **taken** ziet u een overzicht van alle back-ups en het herstellen van gerelateerde taken in uw back-upkluis. Als u een van de getallen in deze tegel selecteert, kunt u meer informatie over taken weer geven voor een bepaald gegevens bron type, bewerkings type en status.

![Back-upinstanties](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Back-uptaken beheren

In de tegel **back-Upinstanties** ziet u een overzicht van alle back-upinstanties in uw back-upkluis. Als u een van de getallen in deze tegel selecteert, kunt u meer informatie over back-upinstanties voor een bepaald gegevens bron type en beveiligings status weer geven.

![Back-uptaken](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>Volgende stappen

- [Back-ups configureren in azure PostgreSQL-data bases](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
