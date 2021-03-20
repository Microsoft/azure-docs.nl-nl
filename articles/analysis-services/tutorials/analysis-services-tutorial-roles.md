---
title: Zelfstudie - Azure Analysis Services-rollen configureren | Microsoft Docs
description: In deze zelfstudie krijgt u meer informatie over het configureren van beheerders- en gebruikersrollen in Azure Analysis Services, met behulp van de Azure-portal of SQL Server Management Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: aea2196671a136145671b977a6d925849b635b73
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92018692"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>Zelfstudie: Serverbeheerder en gebruikersrollen configureren

 In deze zelfstudie gebruikt u SQL Server Management Studio (SSMS) om verbinding te maken met uw server in Azure voor het configureren van de serverbeheerderrol en de modeldatabaserol. U maakt ook kennis met [Tabular Model Scripting Language (TMSL)](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200). TMSL is een scripttaal op basis van JSON voor tabelvormige modellen op het compatibiliteitsniveau van 1200 en hoger. Het kan worden gebruikt om veel in tabelvormige modelleertaken te automatiseren. TMSL wordt vaak gebruikt met PowerShell, maar in deze zelfstudie gebruikt u de XMLA-query-editor in SSMS. In deze zelfstudie voert u de volgende taken uit: 
  
> [!div class="checklist"]
> * Uw servernaam ophalen uit de portal
> * Verbinding maken met de server met behulp van SSMS
> * Een gebruikers- of groepsaccount toevoegen aan de rol van serverbeheerder 
> * Een gebruikers- of groepsaccount toevoegen aan de rol van modeldatabasebeheerder
> * Een nieuwe modeldatabaserol toevoegen en een gebruiker of groep toevoegen

Zie [Verificatie en gebruikersmachtigingen](../analysis-services-manage-users.md) voor meer informatie over gebruikersbeveiliging in Azure Analysis Services. 

## <a name="prerequisites"></a>Vereisten

- Een Azure Active Directory in uw abonnement.
- Een [Azure Analysis Services-server](../analysis-services-create-server.md) gemaakt in uw abonnement.
- [Machtigingen voor serverbeheerder](../analysis-services-server-admins.md).
- [Voeg het voorbeeldmodel Adventure Works toe](../analysis-services-create-sample-model.md) aan uw server.
- [Installeer de nieuwste versie van SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [portal](https://portal.azure.com/).

## <a name="get-server-name"></a>De servernaam ophalen
Als eerste hebt u de servernaam nodig om verbinding te maken met uw server vanuit SSMS. U kunt de servernaam ophalen uit de portal.

In **Azure Portal** > server > **Overview** > **Servernaam**,kopieer de servernaam.
   
   ![Servernaam bepalen in Azure](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>Verbinding maken in SSMS

Voor de resterende taken gebruikt u SSMS voor het maken van verbinding met en het beheren van uw server.

1. Klik in SSMS > **Objectverkenner** op **Verbinding maken** > **Analysis Services**.

    ![Verbinding maken](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. In het dialoogvenster **Verbinden met server** plakt u in **Servernaam** de servernaam die u hebt gekopieerd uit de portal. In **Verificatie** kiest u **Active Directory Universal met ondersteuning voor MFA**, voert u uw gebruikersaccount en drukt vervolgens op **Verbinden**.
   
    ![Verbinding maken in SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > Aangeraden wordt Active Directory Universal met ondersteuning voor MFA te kiezen. Dit type verificatie ondersteunt [niet-interactieve en multi-factor authentication](../../azure-sql/database/authentication-mfa-ssms-overview.md). 

3. In **Objectverkenner** vouwt u de weergave uit om serverobjecten te zien. Klik met de rechtermuisknop om servereigenschappen te bekijken.
   
    ![Objectverkenner in SSMS](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>Een gebruikersaccount toevoegen aan de rol van serverbeheerder

In deze taak voegt u een gebruikers- of groepsaccount uit uw Azure AD toe aan de serverbeheerderrol. Als u een beveiligingsgroep opgeeft, gebruikt u `obj:groupid@tenantid`.

1. Klik in **Objectverkenner** met de rechtermuisknop op de naam van uw server en klik vervolgens op **Eigenschappen**. 
2. In het **eigenschappenvenster van de analyseserver** klikt u op **Beveiliging** > **Toevoegen**.
3. In het venster **Een gebruiker of groep selecteren** voert u een gebruikers- of groepsaccount van uw Azure AD in en klikt u op **Toevoegen**. 
   
     ![Serverbeheerder toevoegen](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. Klik op **OK** om het **eigenschappenvenster van analyseserver** te sluiten.

    > [!TIP]
    > U kunt ook serverbeheerders toevoegen met behulp van **Analysis Services-beheerders** op de portal. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>Een gebruiker toevoegen aan de rol van modeldatabasebeheerder

In deze taak u gaat u een gebruikers- of groepsaccount toevoegen aan de rol Internet Sales-beheerder die al in het model bestaat. Deze rol heeft de machtigingen voor volledig beheer (beheerder) voor de modeldatabase met het AdventureWorks-voorbeeld. Deze taak maakt gebruik van de TMSL-opdracht [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) in een script dat voor u is gemaakt.

1. In **Objectverkenner** vouwt u **Databases** > **adventureworks** > **Rollen** uit. 
2. Klik met de rechtermuisknop op **Internet Sales-beheerder**, klik vervolgens op **Scriptrol as** > **MAKEN OF VERVANGEN Naar** > **venster Nieuwe query-editor**.

    ![Venster Nieuwe query-editor](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. In **XMLAQuery** wijzigt u de waarde van **'memberName':** in een gebruikers- of groepsaccount in uw Azure AD. Het account waarmee u bent aangemeld wordt standaard vermeld. U hoeft echter niet uw eigen account toe te voegen, omdat u al een serverbeheerder bent.

    ![TMSL-script in XMLA-query](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. Druk op **F5** om het script uit te voeren.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>Een nieuwe modeldatabaserol toevoegen en een gebruiker of groep toevoegen

In deze taak gebruikt u de opdracht [Maken](/analysis-services/tmsl/create-command-tmsl) in een TMSL-script om een nieuwe Globale internetverkoop-rol te maken, *leesmachtigingen* voor de rol op te geven en een gebruikers- of groepsaccount uit uw Azure AD toe te voegen.

1. Klik in **Objectverkenner** met de rechtermuisknop op **adventureworks** en klik dan op **Nieuwe query** > **XMLA**. 
2. Kopieer en plak het volgende TMSL-script in de query-editor:

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. Wijzig de objectwaarde `"memberName": "globalsales@adventureworks.com"` in een gebruikers- of groepsaccount in uw Azure AD.
4. Druk op **F5** om het script uit te voeren.

## <a name="verify-your-changes"></a>Uw wijzigingen verifiëren

1. Klik in **Objectverkenner** op uw servernaam en klik vervolgens op **Vernieuwen** of druk op **F5**.
2. Vouw **Databases** > **adventureworks** > **Rollen** uit. Controleer of het gebruikersaccount en de rolwijzigingen die u hebt toegevoegd in de voorgaande taken, worden weergegeven.   

    ![Verifiëren in Objectverkenner](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de accounts en rollen voor gebruikers of groepen wanneer u deze niet meer nodig hebt. Wanneer u gebruikersaccounts wilt verwijderen, gebruikt u  **Roleigenschappen** > **Lidmaatschap** of klikt u met de rechtermuisknop op een rol en klikt u vervolgens op **verwijderen**.


## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe verbinding kunt maken met uw Azure AS-server en de modeldatabases voor het AdventureWorks-voorbeeld en eigenschappen in SSMS kunt verkennen. U hebt ook geleerd hoe SSMS- en TMSL-scripts kunt gebruiken om gebruikers of groepen toe te voegen aan bestaande en nieuwe rollen. Nu u de gebruikersmachtigingen hebt geconfigureerd voor uw server en het voorbeeld van de modeldatabase, kunnen u en andere gebruikers er verbinding mee maken met behulp van clienttoepassingen zoals Power BI. Ga verder met de volgende zelfstudie voor meer informatie. 

> [!div class="nextstepaction"]
> [Zelfstudie: Verbinden met Power BI Desktop](analysis-services-tutorial-pbid.md)