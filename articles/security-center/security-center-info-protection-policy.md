---
title: SQL Information Protection-beleid in Azure Security Center
description: Meer informatie over het aanpassen van het beleid voor gegevens beveiliging in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: 34d2e8116b7b914803d1bb68b350c9aadd78439f
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792062"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>SQL Information Protection-beleid in Azure Security Center
 
Het [mechanisme voor gegevens detectie en-classificatie](../azure-sql/database/data-discovery-and-classification-overview.md) van SQL Information Protection biedt geavanceerde mogelijkheden voor het detecteren, classificeren, labelen en rapporteren van gevoelige gegevens in uw data bases. Het is ingebouwd in [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md), [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)en [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Het classificatie mechanisme is gebaseerd op de volgende twee elementen:

- **Labels** : de belangrijkste classificatie kenmerken, die worden gebruikt voor het definiëren *van het gevoeligheids niveau van de gegevens* die zijn opgeslagen in de kolom. 
- **Informatie typen** : biedt een extra granulatie voor het *type gegevens dat* in de kolom wordt opgeslagen.

De opties voor het Information Protection-beleid in Security Center bieden een vooraf gedefinieerde set labels en gegevens typen die als standaard waarden voor de classificatie-engine dienen. U kunt het beleid aanpassen op basis van de behoeften van uw organisatie, zoals hieronder wordt beschreven.

> [!IMPORTANT]
> Als u het Information Protection-beleid voor uw Azure-Tenant wilt aanpassen, moet u beheerders rechten hebben voor de hoofd beheer groep van de Tenant. Meer informatie vindt u in de [zicht baarheid van de hele Tenant voor Azure Security Center](security-center-management-groups.md).

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="De pagina met uw SQL Information Protection-beleid":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Hoe kan ik toegang tot het SQL Information Protection-beleid?

Er zijn drie manieren om toegang te krijgen tot het Information Protection-beleid:

- **(Aanbevolen)** Op de pagina prijs en instellingen van Security Center
- Uit de beveiligings aanbeveling moeten gevoelige gegevens in uw SQL-data bases worden geclassificeerd.
- Op de pagina Azure SQL DB-gegevens detectie

Elk van deze wordt weer gegeven op het relevante tabblad hieronder.



### <a name="from-security-centers-settings"></a>[**Van instellingen van Security Center**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Toegang tot het beleid via de pagina prijs en instellingen van Security Center <a name="sqlip-tenant"></a>

Selecteer op de pagina **prijzen en instellingen** van Security Center de optie **SQL Information Protection**.

> [!NOTE]
> Deze optie wordt alleen weer gegeven voor gebruikers met machtigingen op Tenant niveau. [Verleen machtigingen voor de hele Tenant](https://docs.microsoft.com/azure/security-center/security-center-management-groups#grant-tenant-wide-permissions-to-yourself).

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Toegang tot het SQL Information Protection-beleid op de pagina prijzen en instellingen van Azure Security Center":::



### <a name="from-security-centers-recommendation"></a>[**Van aanbeveling van Security Center**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Toegang tot het beleid vanuit de Security Center aanbeveling <a name="sqlip-db"></a>

Gebruik de aanbeveling van Security Center, ' gevoelige gegevens in uw SQL-data bases moeten worden geclassificeerd ', om de pagina gegevens detectie en-classificatie voor uw Data Base weer te geven. Daar ziet u ook de gedetecteerde kolommen die informatie bevatten die wij u aanraden.

1. Zoek op de pagina **aanbevelingen** van Security Center de aanbeveling **gevoelige gegevens in uw SQL-data bases moeten worden geclassificeerd**.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="De aanbeveling zoeken die toegang biedt tot het SQL Information Protection-beleid":::

1. Selecteer op de pagina aanbevelings Details een Data Base op **de tabbladen goed** of niet in **orde** .

1. De pagina **gegevens detectie & classificatie** wordt geopend. Selecteer **Configureren**.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Het SQL Information Protection-beleid wordt geopend vanuit de relevante aanbeveling in de Azure Security Center":::



### <a name="from-azure-sql"></a>[**Vanuit Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Toegang tot het beleid vanuit Azure SQL <a name="sqlip-azuresql"></a>

1. Open Azure SQL vanuit het Azure Portal.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Azure SQL openen vanuit de Azure Portal":::

1. Selecteer een wille keurige data base.

1. Open in het gebied **beveiliging** van het menu de pagina **gegevens detectie & classificatie** (1) en selecteer **configureren** (2).

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Het SQL Information Protection-beleid openen vanuit Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Uw gegevens typen aanpassen

Informatie typen beheren en aanpassen:

1. Selecteer **gegevens typen beheren**.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Informatie typen voor uw Information Protection-beleid beheren":::

1. Selecteer **gegevens type maken** om een nieuw type toe te voegen. U kunt een naam, beschrijving en zoek patroon teken reeksen voor het informatie type configureren. Zoek patroon teken reeksen kunnen optioneel tref woorden gebruiken met Joker tekens (met het teken%), die de geautomatiseerde detectie-engine gebruikt om gevoelige gegevens in uw data bases te identificeren, op basis van de meta gegevens van de kolommen.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Een nieuw gegevens type voor uw Information Protection-beleid configureren":::

1. U kunt ook de ingebouwde typen wijzigen door extra Zoek patronen toe te voegen, een deel van de bestaande teken reeksen uit te scha kelen of door de beschrijving te wijzigen. 

    > [!TIP]
    > U kunt geen ingebouwde typen verwijderen of hun namen wijzigen. 

1. **Informatie typen** worden weer gegeven in volg orde van oplopende detectie classificatie, wat betekent dat de typen die hoger in de lijst staan, worden geprobeerd eerst te komen. Als u de rang schikking tussen gegevens typen wilt wijzigen, sleept u de typen naar de juiste plaats in de tabel of gebruikt u de knoppen **omhoog** en **omlaag** om de volg orde te wijzigen. 

1. Selecteer **OK** wanneer u klaar bent.

1. Nadat u klaar bent met het beheren van uw gegevens typen, moet u de relevante typen koppelen aan de relevante labels door te klikken op **configureren** voor een bepaald label en zo nodig informatie typen toe te voegen of te verwijderen.

1. Als u uw wijzigingen wilt Toep assen, selecteert u **Opslaan** op de pagina hoofd **etiketten** .
 

## <a name="exporting-and-importing-a-policy"></a>Een beleid exporteren en importeren

U kunt een JSON-bestand met uw gedefinieerde labels en informatie typen downloaden, het bestand bewerken in de editor van uw keuze en het bijgewerkte bestand vervolgens importeren. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Uw Information Protection-beleid exporteren en importeren":::

> [!NOTE]
> U hebt machtigingen op Tenant niveau nodig om een beleids bestand te importeren. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>SQL Information Protection beheren met Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): haalt het effectief beveiligings beleid voor TENANT-SQL information op.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): Hiermee stelt u het effectief beveiligings beleid voor TENANT-SQL-gegevens in.
 

## <a name="next-steps"></a>Volgende stappen
 
In dit artikel hebt u geleerd over het definiëren van een Information Protection-beleid in Azure Security Center. Zie [Azure SQL database gegevens detectie en-classificatie](../azure-sql/database/data-discovery-and-classification-overview.md)voor meer informatie over het gebruik van SQL Information Protection voor het classificeren en beveiligen van gevoelige gegevens in uw SQL-data bases.

Raadpleeg de volgende artikelen voor meer informatie over beveiligings beleid en gegevens beveiliging in Security Center:
 
- [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md): meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen
- [Azure Security Center gegevens beveiliging](security-center-data-security.md): meer informatie over het Security Center beheren en beveiligen van gegevens
