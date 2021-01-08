---
title: Beheerde identiteiten gebruiken om toegang te krijgen tot Azure SQL Database of Azure Synapse Analytics-Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren voor Azure SQL Database of Azure Synapse Analytics-uitvoer.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 244f8a446e33102e018881b3ec8f32ba9d8cbcc6
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014142"
---
# <a name="use-managed-identities-to-access-azure-sql-database-or-azure-synapse-analytics-from-an-azure-stream-analytics-job-preview"></a>Beheerde identiteiten gebruiken om toegang te krijgen tot Azure SQL Database of Azure Synapse Analytics vanuit een Azure Stream Analytics-taak (preview)

Azure Stream Analytics ondersteunt [beheerde identiteits verificatie](../active-directory/managed-identities-azure-resources/overview.md) voor Azure SQL database en Azure Synapse Analytics-uitvoer Sinks. Beheerde identiteiten elimineren de beperkingen van verificatie methoden op basis van gebruikers, zoals de nood zaak om opnieuw te worden geverifieerd als gevolg van wachtwoord wijzigingen of verlopen van gebruikers tokens die elke 90 dagen worden uitgevoerd. Wanneer u de nood zaak om hand matig te verifiëren verwijdert, kunnen uw Stream Analytics-implementaties volledig worden geautomatiseerd.

Een beheerde identiteit is een beheerde toepassing die is geregistreerd in Azure Active Directory die een bepaalde Stream Analytics taak vertegenwoordigt. De beheerde toepassing wordt gebruikt om te verifiëren bij een doel resource. Dit artikel laat u zien hoe u beheerde identiteit kunt inschakelen voor een Azure SQL Database of een Azure Synapse Analytics-uitvoer (en) van een Stream Analytics taak via de Azure Portal.

## <a name="prerequisites"></a>Vereisten

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Het volgende is vereist voor het gebruik van deze functie:

- Een Azure Stream Analytics taak.

- Een Azure SQL Database resource.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Het volgende is vereist voor het gebruik van deze functie:

- Een Azure Stream Analytics taak.

- Een Azure Synapse Analytics SQL-groep.

- Een Azure Storage-account dat is [geconfigureerd voor uw stream Analytics taak](azure-synapse-analytics-output.md).

---

## <a name="create-a-managed-identity"></a>Een beheerde identiteit maken

Eerst maakt u een beheerde identiteit voor uw Azure Stream Analytics-taak.

1. Open de Azure Stream Analytics-taak in de [Azure Portal](https://portal.azure.com).

1. Selecteer in het navigatie menu links **beheerde identiteit** onder **configureren**. Schakel vervolgens het selectie vakje naast door **systeem toegewezen beheerde identiteit gebruiken** in en selecteer **Opslaan**.

   ![Door het systeem toegewezen beheerde identiteit selecteren](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)

   Een service-principal voor de identiteit van de Stream Analytics-taak wordt gemaakt in Azure Active Directory. De levens cyclus van de zojuist gemaakte identiteit wordt beheerd door Azure. Wanneer de Stream Analytics taak wordt verwijderd, wordt de bijbehorende identiteit (de service-principal) automatisch verwijderd door Azure.

1. Wanneer u de configuratie opslaat, wordt de object-ID (OID) van de Service-Principal vermeld als de principal-ID zoals hieronder wordt weer gegeven: 

   ![Object-ID weer gegeven als Principal-ID](./media/sql-db-output-managed-identity/principal-id.png)

   De service-principal heeft dezelfde naam als de Stream Analytics taak. Als de naam van uw taak bijvoorbeeld *MyASAJob* is, is de naam van de Service-Principal ook *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Een Active Directory beheerder selecteren

Nadat u een beheerde identiteit hebt gemaakt, selecteert u een Active Directory beheerder.

1. Navigeer naar uw Azure SQL Database of Azure Synapse Analytics-resource en selecteer de SQL Server waarvan de data base zich bevindt. U vindt de SQL Server naam naast *Server naam* op de pagina overzicht van resources.

1. Selecteer **Active Directory beheerder** onder **instellingen**. Selecteer vervolgens **beheerder instellen**.

   ![Active Directory-beheer pagina](./media/sql-db-output-managed-identity/active-directory-admin-page.png)

1. Zoek op de pagina Active Directory-beheer naar een gebruiker of groep om een beheerder voor de SQL Server te zijn en klik op **selecteren**.

   ![Active Directory beheerder toevoegen](./media/sql-db-output-managed-identity/add-admin.png)

   Op de pagina Active Directory beheer worden alle leden en groepen van uw Active Directory weer gegeven. Gebruikers of groepen die grijs worden weer gegeven, kunnen niet worden geselecteerd, omdat ze niet worden ondersteund als Azure Active Directory-beheerders. Zie de lijst met ondersteunde beheerders in de sectie **Azure Active Directory functies en beperkingen**   van het [gebruik van Azure Active Directory authenticatie voor verificatie met SQL database of Azure Synapse](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations). Azure RBAC (op rollen gebaseerd toegangs beheer) is alleen van toepassing op de portal en wordt niet door gegeven aan SQL Server. De geselecteerde gebruiker of groep is ook de gebruiker die de **Inge sloten database gebruiker** in de volgende sectie kan maken.

1. Selecteer **Opslaan** op de pagina **beheer van Active Directory** . Het proces voor het wijzigen van de beheerder duurt enkele minuten.

   Wanneer u de Azure Active Directory-beheerder instelt, kan de nieuwe beheerders naam (gebruiker of groep) niet aanwezig zijn in de virtuele primaire Data Base als SQL Server-verificatie gebruiker. Als dit het geval is, mislukt de Azure Active Directory-beheerders installatie en wordt het maken ervan ongedaan gemaakt, wat aangeeft dat er al een beheerder (naam) bestaat. Omdat de gebruiker van SQL Server authenticatie geen deel uitmaakt van Azure Active Directory, is er geen enkele poging om verbinding te maken met de server met behulp van Azure Active Directory-verificatie als die gebruiker mislukt. 

## <a name="create-a-contained-database-user"></a>Een Inge sloten database gebruiker maken

Vervolgens maakt u een Inge sloten database gebruiker in uw Azure SQL-of Azure Synapse-data base die is toegewezen aan de Azure Active Directory-identiteit. De Inge sloten database gebruiker heeft geen aanmelding voor de primaire data base, maar wordt toegewezen aan een identiteit in de map die is gekoppeld aan de data base. De identiteit van de Azure Active Directory kan een afzonderlijk gebruikers account of een groep zijn. In dit geval wilt u een Inge sloten database gebruiker voor uw Stream Analytics-taak maken. 

1. Maak verbinding met uw Azure SQL-of Azure Synapse-data base met behulp van SQL Server Management Studio. De **gebruikers naam** is een Azure Active Directory gebruiker met de machtiging voor het **wijzigen van een gebruiker** . De beheerder die u instelt op de SQL Server is een voor beeld. Gebruik **Azure Active Directory-Universal met MFA-** verificatie. 

   ![Verbinding maken met SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   De naam van de server `<SQL Server name>.database.windows.net` kan verschillen in verschillende regio's. Bijvoorbeeld, de regio China moet gebruiken `<SQL Server name>.database.chinacloudapi.cn` .
 
   U kunt een specifieke Azure SQL-of Azure Synapse-data base opgeven door te gaan naar **opties > verbindings eigenschappen > verbinding maken met de data base**.  

   ![Eigenschappen van SQL Server-verbinding](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. Wanneer u voor het eerst verbinding maakt, kunt u het volgende venster tegen komen:

   ![Venster nieuwe firewall regel](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. Als dit het geval is, gaat u naar uw SQL Server-Resource op de Azure Portal. Open de pagina **firewalls en virtuele netwerken** onder het gedeelte **beveiliging** . 
   1. Voeg een nieuwe regel toe met een regel naam.
   1. Gebruik het IP-adres *uit* het venster **nieuwe firewall regel** voor het *begin-IP*.
   1. Gebruik het *IP-adres in het* venster **nieuwe firewall regel** voor het *eind-IP*. 
   1. Selecteer **Opslaan** en probeer opnieuw verbinding te maken vanuit SQL Server Management Studio. 

1. Wanneer u bent verbonden, maakt u de Inge sloten database gebruiker. Met de volgende SQL-opdracht maakt u een Inge sloten database gebruiker die dezelfde naam heeft als uw Stream Analytics-taak. Zorg ervoor dat u de haken rond de *ASA_JOB_NAME* plaatst. Gebruik de volgende T-SQL-syntaxis en voer de query uit. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

1. Voor de Azure Active Directory van micro soft om te controleren of de Stream Analytics-taak toegang heeft tot de SQL Database, moeten we Azure Active Directory toestemming geven om te communiceren met de data base. Als u dit wilt doen, gaat u naar de pagina firewalls en virtueel netwerk in Azure Portal en schakelt u ' Azure-Services en-bronnen toestaan voor toegang tot deze server ' in. 

   ![Firewall en virtueel netwerk](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Stream Analytics taak machtigingen verlenen

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Zodra u een Inge sloten database gebruiker hebt gemaakt en toegang hebt gekregen tot Azure-Services in de portal, zoals beschreven in de vorige sectie, is uw Stream Analytics-taak gemachtigd van beheerde identiteit om **verbinding te maken** met uw Azure SQL database-Resource via een beheerde identiteit. U wordt aangeraden de machtigingen voor selecteren en invoegen toe te kennen aan de Stream Analytics-taak, omdat deze later in de Stream Analytics werk stroom nodig zijn. Met de machtiging **selecteren** kunt u de verbinding met de tabel in de Azure-SQL database testen. Met de machtiging voor **Invoegen** kunt u end-to-end-stream Analytics query's testen zodra u een invoer en de Azure SQL database-uitvoer hebt geconfigureerd.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Zodra u een Inge sloten database gebruiker hebt gemaakt en toegang hebt gekregen tot Azure-Services in de portal, zoals beschreven in de vorige sectie, is uw Stream Analytics-taak gemachtigd van beheerde identiteit om **verbinding te maken** met uw Azure Synapse-database Resource via een beheerde identiteit. We raden u aan om de machtigingen voor het maken, invoegen en beheren van data BASEs te verfijnen aan de Stream Analytics-taak, omdat deze later nodig zijn in de Stream Analytics werk stroom. Met de machtiging **selecteren** kunt u de verbinding van de taak naar de tabel in de Azure Synapse-data base testen. Met de machtigingen voor het **Invoegen** en beheren van de **Data Base** kunt u end-to-end-stream Analytics query's testen zodra u een invoer en de Azure Synapse-data base-uitvoer hebt geconfigureerd.

Als u de machtiging voor het beheren van meerdere data BASEs wilt verlenen, moet u alle machtigingen verlenen die als **besturings element** worden aangeduid, onder [impliciet door de machtiging voor de data base](/sql/t-sql/statements/grant-database-permissions-transact-sql?view=azure-sqldw-latest#remarks) voor de stream Analytics taak. U hebt deze machtiging nodig omdat de Stream Analytics-taak de instructie COPY uitvoert, waarvoor het beheren van de [Data base in bulk bewerkingen en invoegen](/sql/t-sql/statements/copy-into-transact-sql)vereist is.

---

U kunt deze machtigingen toewijzen aan de Stream Analytics-taak met behulp van SQL Server Management Studio. Zie de referentie GRANT (Transact-SQL) voor meer informatie.

Als u alleen machtigingen wilt verlenen voor een bepaalde tabel of object in de data base, gebruikt u de volgende T-SQL-syntaxis en voert u de query uit. 

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

```sql
GRANT [PERMISSION NAME] OBJECT::TABLE_NAME TO ASA_JOB_NAME;
```

---

U kunt ook met de rechter muisknop op uw Azure SQL-of Azure Synapse-data base in SQL Server Management Studio klikken en **eigenschappen > machtigingen** selecteren. In het menu machtigingen ziet u de Stream Analytics-taak die u eerder hebt toegevoegd. u kunt ook hand matig machtigingen verlenen of weigeren zoals u dat wilt.

## <a name="create-an-azure-sql-database-or-azure-synapse-output"></a>Een Azure SQL Database-of Azure Synapse-uitvoer maken

#### <a name="azure-sql-database"></a>[Azure SQL Database](#tab/azure-sql)

Nu uw beheerde identiteit is geconfigureerd, kunt u een Azure SQL Database-of Azure Synapse-uitvoer aan uw Stream Analytics-taak toevoegen.

Zorg ervoor dat u een tabel in uw SQL Database hebt gemaakt met het juiste uitvoer schema. De naam van deze tabel is een van de vereiste eigenschappen die moeten worden ingevuld wanneer u de SQL Database uitvoer toevoegt aan de Stream Analytics taak. Zorg er ook voor dat de taak de machtigingen **selecteren** en **Invoegen** heeft om de verbinding te testen en stream Analytics query's uit te voeren. Raadpleeg de sectie [machtigingen stream Analytics taak machtiging verlenen](#grant-stream-analytics-job-permissions) als u dit nog niet hebt gedaan. 

1. Ga terug naar uw Stream Analytics-taak en navigeer naar de pagina **uitvoer** onder **taak topologie**. 

1. Selecteer **> SQL database toevoegen**. Selecteer in het venster uitvoer eigenschappen van de SQL Database uitvoer Sink **beheerde identiteit** in de vervolg keuzelijst verificatie modus.

1. Vul de rest van de eigenschappen in. Zie [Create a SQL database output with stream Analytics](sql-database-output.md)voor meer informatie over het maken van een SQL database-uitvoer. Wanneer u klaar bent, selecteert u **Opslaan**.

#### <a name="azure-synapse-analytics"></a>[Azure Synapse Analytics](#tab/azure-synapse)

Nu uw beheerde identiteits-en opslag account zijn geconfigureerd, kunt u een Azure SQL Database-of Azure Synapse-uitvoer aan uw Stream Analytics-taak toevoegen.

Zorg ervoor dat u in uw Azure Synapse-Data Base een tabel hebt gemaakt met het juiste uitvoer schema. De naam van deze tabel is een van de vereiste eigenschappen die moeten worden ingevuld wanneer u de Azure Synapse-uitvoer aan de Stream Analytics-taak toevoegt. Zorg er ook voor dat de taak de machtigingen **selecteren** en **Invoegen** heeft om de verbinding te testen en stream Analytics query's uit te voeren. Raadpleeg de sectie [machtigingen stream Analytics taak machtiging verlenen](#grant-stream-analytics-job-permissions) als u dit nog niet hebt gedaan.

1. Ga terug naar uw Stream Analytics-taak en navigeer naar de pagina **uitvoer** onder **taak topologie**.

1. Selecteer **> Azure Synapse Analytics toevoegen**. Selecteer in het venster uitvoer eigenschappen van de SQL Database uitvoer Sink **beheerde identiteit** in de vervolg keuzelijst verificatie modus.

1. Vul de rest van de eigenschappen in. Zie [Azure Synapse Analytics output van Azure stream Analytics](azure-synapse-analytics-output.md)voor meer informatie over het maken van een Azure Synapse-uitvoer. Wanneer u klaar bent, selecteert u **Opslaan**.

---

## <a name="remove-managed-identity"></a>Beheerde identiteit verwijderen

De beheerde identiteit die is gemaakt voor een Stream Analytics taak wordt alleen verwijderd wanneer de taak wordt verwijderd. Het is niet mogelijk om de beheerde identiteit te verwijderen zonder de taak te verwijderen. Als u de beheerde identiteit niet meer wilt gebruiken, kunt u de verificatie methode voor de uitvoer wijzigen. De beheerde identiteit blijft bestaan totdat de taak is verwijderd en wordt gebruikt als u ervoor kiest beheerde identiteits verificatie opnieuw te gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de uitvoer van Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics uitvoer naar Azure SQL Database](stream-analytics-sql-output-perf.md)
* [Azure Synapse Analytics-uitvoer van Azure Stream Analytics](azure-synapse-analytics-output.md)
