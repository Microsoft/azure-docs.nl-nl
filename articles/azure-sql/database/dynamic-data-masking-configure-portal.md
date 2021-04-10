---
title: 'Azure Portal: dynamische gegevens maskering'
description: Hoe u aan de slag gaat met Azure SQL Database dynamische gegevens maskering in de Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: e551925bbd1426256a81ac2ca3f245af7697245b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028009"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Aan de slag met SQL Database dynamische gegevens maskering met de Azure Portal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel wordt beschreven hoe u [dynamische gegevens maskering](dynamic-data-masking-overview.md) implementeert met de Azure Portal. U kunt ook dynamische gegevens maskering implementeren met behulp van [Azure SQL database-cmdlets](/powershell/module/az.sql/) of de [rest API](/rest/api/sql/).

> [!NOTE]
> Deze functie kan niet worden ingesteld met behulp van de portal voor SQL Managed instance (gebruik Power shell of REST API). Zie [dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking)voor meer informatie.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Dynamische gegevens maskering instellen voor uw data base met behulp van de Azure Portal

1. Start de Azure Portal op [https://portal.azure.com](https://portal.azure.com) .
2. Ga naar de pagina instellingen van de data base die de gevoelige gegevens bevat die u wilt maskeren.
3. Klik op de Blade **dynamische gegevens maskering** onder het gedeelte **beveiliging** van uw data base.

   ![Scherm afbeelding met de sectie Beveiliging met dynamische gegevens maskering gemarkeerd.](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. In de configuratie pagina voor **dynamische gegevens maskering** worden mogelijk enkele database kolommen weer gegeven die door de aanbevelingen engine voor maskering zijn gemarkeerd. Als u de aanbevelingen wilt accepteren, klikt u op **masker toevoegen** voor een of meer kolommen en wordt er een masker gemaakt op basis van het standaard type voor deze kolom. U kunt de maskerings functie wijzigen door te klikken op de maskerings regel en de indeling van het masker veld te bewerken naar een andere indeling van uw keuze. Zorg ervoor dat u op **Opslaan** klikt om uw instellingen op te slaan.

    ![Scherm opname van de configuratie pagina voor dynamische gegevens maskering.](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. Als u een masker voor een kolom in uw Data Base wilt toevoegen, klikt u boven aan de configuratie pagina voor **dynamische gegevens maskeren** op **masker toevoegen** om de configuratie pagina **maskerings regel toevoegen** te openen.

    ![Scherm opname van de configuratie pagina voor het toevoegen van een Maskerings regel.](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. Selecteer het **schema**, de **tabel** en de **kolom** om het aangewezen veld voor maskering te definiëren.
7. **Selecteer hoe u wilt maskeren** in de lijst met gevoelige gegevens maskerings categorieën.

    ![Scherm opname van de categorieën gevoelige gegevens maskering onder de sectie Selecteer hoe u wilt maskeren.](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. Klik op **toevoegen** op de pagina gegevens maskerings regel om de set maskerings regels in het beleid voor dynamische gegevens maskering bij te werken.
9. Typ de identiteiten van de SQL-gebruikers of de Azure Active Directory (Azure AD) die moeten worden uitgesloten van maskering en toegang hebben tot de niet-gemaskerde gevoelige gegevens. Dit moet een door punt komma's gescheiden lijst met gebruikers zijn. Gebruikers met beheerders bevoegdheden hebben altijd toegang tot de oorspronkelijke niet-gemaskeerde gegevens.

    ![Navigatievenster](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > Om ervoor te zorgen dat de toepassingslaag gevoelige gegevens kan weer geven voor gebruikers met toepassings bevoegdheden, voegt u de SQL-gebruiker of Azure AD-identiteit toe die door de toepassing wordt gebruikt om een query uit te voeren op de data base. Het wordt ten zeerste aanbevolen dat deze lijst een mini maal aantal bevoegde gebruikers bevat om de bloot stelling van gevoelige gegevens te minimaliseren.

10. Klik op **Opslaan** op de configuratie pagina voor gegevens maskering om het nieuwe of bijgewerkte maskerings beleid op te slaan.

## <a name="next-steps"></a>Volgende stappen

- Zie [dynamische gegevens maskering](dynamic-data-masking-overview.md)voor een overzicht van dynamische gegevens maskering.
- U kunt ook dynamische gegevens maskering implementeren met behulp van [Azure SQL database-cmdlets](/powershell/module/az.sql/) of de [rest API](/rest/api/sql/).
