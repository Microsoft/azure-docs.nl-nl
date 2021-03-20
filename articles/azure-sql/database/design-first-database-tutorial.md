---
title: 'Zelfstudie: Uw eerste relationele database ontwerpen met SSMS'
description: Leer hoe u uw eerste relationele database in Azure SQL Database kunt ontwerpen met behulp van SQL Server Management Studio.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: ae7baeac6cee2a692928642e3e38ce0adad17d1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674889"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>Zelfstudie: Een relationele database ontwerpen in Azure SQL Database met SSMS
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL Database is een relationele DBaaS (database-as-a-service) in Microsoft Cloud (Azure). In deze zelfstudie leert u hoe u met Azure Portal en [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) de volgende taken uitvoert:

> [!div class="checklist"]
>
> - Een database maken met behulp van de Azure-portal*
> - Een IP-firewallregel op serverniveau instellen met de Azure-portal
> - Verbinding maken met de database via SQL Server Management Studio
> - Tabellen maken met SSMS
> - Gegevens bulksgewijs laden met BCP
> - Query uitvoeren op gegevens met SSMS

*Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!TIP]
> In de volgende Microsoft-leermodule leert u gratis [Een ASP.NET-toepassing ontwikkelen en configureren die een query uitvoert op een Azure SQL Database](/learn/modules/develop-app-that-queries-azure-sql/), met inbegrip van het maken van een eenvoudige database.
> [!NOTE]
> Voor deze zelfstudie wordt gebruikgemaakt van Azure SQL Database. U kunt ook een gepoolde database in een elastische pool of een met SQL beheerd exemplaar gebruiken. Zie voor connectiviteit naar een met SQL beheerd exemplaar deze snelstartgidsen over met SQL beheerde exemplaren: [Snelstart: Azure VM configureren om verbinding te maken met een met Azure SQL beheerd exemplaar](../managed-instance/connect-vm-instance-configure.md) en [quickstart: Een punt-naar-site-verbinding configureren naar een met Azure SQL beheerd exemplaar van on-premises](../managed-instance/point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (nieuwste versie)
- [BCP en SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (nieuwste versie)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

## <a name="create-a-blank-database-in-azure-sql-database"></a>Een lege database maken in Azure SQL Database

Een Azure SQL-database wordt gemaakt met een gedefinieerde set reken- en opslagresources. De database wordt gemaakt in een [Azure-resourcegroep](../../active-directory-b2c/overview.md) en wordt beheerd met een [logische SQL-server](logical-servers.md).

Volg deze stappen om een lege database te maken.

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
2. Selecteer op de pagina **Nieuw** **Databases** in de sectie Azure Marketplace en klik vervolgens op **SQL Database** in de sectie **Aanbevolen**.

   ![lege database maken](./media/design-first-database-tutorial/create-empty-database.png)

3. Vul het formulier **SQL Database** in met de volgende informatie, zoals in de voorgaande afbeelding wordt weergegeven:

    | Instelling       | Voorgestelde waarde | Beschrijving |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Databasenaam** | *yourDatabase* | Zie [Database-id's](/sql/relational-databases/databases/database-identifiers) voor geldige databasenamen. |
    | **Abonnement** | *yourSubscription*  | Zie [Abonnementen](https://account.windowsazure.com/Subscriptions) voor meer informatie over uw abonnementen. |
    | **Resourcegroep** | *yourResourceGroup* | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige resourcegroepnamen. |
    | **Bron selecteren** | Lege database | Hiermee geeft u aan dat er een lege database moet worden gemaakt. |

4. Klik op **Server** als u een bestaande server wilt gebruiken of een nieuwe server wilt maken en configureren. Selecteer een bestaande server of klik op **Een nieuwe server maken** en vul het formulier **Nieuwe server** in met de volgende gegevens:

    | Instelling       | Voorgestelde waarde | Beschrijving |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Servernaam** | Een wereldwijd unieke naam | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige servernamen. |
    | **Aanmeldgegevens van serverbeheerder** | Een geldige naam | Zie [Database-id's](/sql/relational-databases/databases/database-identifiers) voor geldige aanmeldingsnamen. |
    | **Wachtwoord** | Een geldig wachtwoord | Uw wachtwoord moet uit minstens acht tekens bestaan en moet tekens bevatten uit drie van de volgende categorieën: hoofdletters, kleine letters, cijfers en niet-alfanumerieke tekens. |
    | **Locatie** | Een geldige locatie | Zie [Azure-regio's](https://azure.microsoft.com/regions/) voor informatie over regio's. |

    ![database-server maken](./media/design-first-database-tutorial/create-database-server.png)

5. Klik op **Selecteren**.
6. Klik op **Prijscategorie** om de servicelaag, het aantal DTU's of vCores en de hoeveelheid opslag op te geven. U kunt de opties bekijken voor de hoeveelheid DTU's/vCores en opslag die voor elke servicelaag beschikbaar zijn.

    Als u de servicelaag, het aantal DTU's of vCores en de hoeveelheid opslagruimte hebt geselecteerd, klikt u op **Toepassen**.

7. Voer een **sortering** in voor de lege database (gebruik de standaardwaarde in deze zelfstudie). Zie [Collations](/sql/t-sql/statements/collations) (Sorteringen) voor meer informatie over sorteringen

8. Nu u het **SQL Database**-formulier hebt ingevuld, klikt u op **Maken** om de database in te richten. Deze stap kan enkele minuten duren.

9. Klik op de werkbalk op **Meldingen** om het implementatieproces te bewaken.

   ![Schermopname van het menu Meldingen met De implementatie wordt uitgevoerd.](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Een IP-firewallregel op serverniveau maken

Azure SQL Database maakt een IP-firewall op serverniveau. De firewall voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server, tenzij een firewallregel hun IP via de firewall toestaat. Als u externe connectiviteit voor uw database wilt inschakelen, moet u eerst een IP-firewallregel voor uw IP-adres (of IP-adresbereik) toevoegen. Volg deze stappen als u een [IP-firewallregel op serverniveau](firewall-configure.md) wilt maken.

> [!IMPORTANT]
> Azure SQL Database communiceert via poort 1433. Als u verbinding met deze service probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 1433 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u geen verbinding maken met uw database, tenzij de beheerder poort 1433 openstelt.

1. Selecteer nadat de implementatie is voltooid **SQL databases** in het Azure Portal-menu of zoek ernaar en selecteer *SQL databases* in een andere pagina.  

1. Selecteer *yourDatabase* in de pagina **SQL databases**. De overzichtspagina voor de database wordt geopend, met de volledig gekwalificeerde **Servernaam** (bijvoorbeeld `contosodatabaseserver01.database.windows.net`) en opties voor verdere configuratie.

   ![servernaam](./media/design-first-database-tutorial/server-name.png)

1. Kopieer vanuit SQL Server Management Studio deze volledig gekwalificeerde servernaam om verbinding te maken met de server en de databases.

1. Klik op de werkbalk op **Serverfirewall instellen**. De pagina **Firewallinstellingen** voor de server wordt geopend.

   ![IP-firewallregel op serverniveau](./media/design-first-database-tutorial/server-firewall-rule.png)

1. Klik op **IP van client toevoegen** op de werkbalk om uw huidige IP-adres aan een nieuwe IP-firewallregel toe te voegen. Een IP-firewallregel kan poort 1433 openen voor een afzonderlijk IP-adres of voor een aantal IP-adressen.

1. Klik op **Opslaan**. Er wordt een IP-firewallregel op serverniveau gemaakt voor uw huidige IP-adres waarbij poort 1433 wordt geopend op de server.

1. Klik op **OK** en sluit de pagina **Firewallinstellingen**.

Uw IP-adres wordt niet meer geblokkeerd via de IP-firewall. U kunt nu verbinding maken met uw database met SQL Server Management Studio of een ander hulpprogramma naar keuze. Gebruik het beheerdersaccount voor de server dat u eerder hebt gemaakt.

> [!IMPORTANT]
> Voor alle Azure-services is toegang via de IP-firewall van SQL Database standaard ingeschakeld. Klik op **UIT** op deze pagina om dit voor alle Azure-services uit te schakelen.

## <a name="connect-to-the-database"></a>Verbinding maken met de database

Gebruik [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) om verbinding te maken met uw database.

1. Open SQL Server Management Studio.
2. Voer in het dialoogvenster **Verbinding maken met server** de volgende informatie in:

   | Instelling       | Voorgestelde waarde | Beschrijving |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Servertype** | Database-engine | Deze waarde is verplicht. |
   | **Servernaam** | De volledig gekwalificeerde servernaam | Bijvoorbeeld *yourserver.database.windows.net*. |
   | **Verificatie** | SQL Server-verificatie | SQL-verificatie is het enige verificatietype dat we in deze zelfstudie hebben geconfigureerd. |
   | **Aanmelding** | Het beheerdersaccount voor de server | Het account dat u hebt opgegeven tijdens het maken van de server. |
   | **Wachtwoord** | Het wachtwoord voor het beheerdersaccount voor de server | Het wachtwoord dat u hebt opgegeven tijdens het maken van de server. |

   ![verbinding maken met server](./media/design-first-database-tutorial/connect.png)

3. Klik op **Opties** in het dialoogvenster **Verbinding maken met server**. Voer in de sectie **Verbinding maken met database** *yourDatabase* in om verbinding te maken met deze database.

    ![verbinding maken met database op server](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. Klik op **Verbinden**. Het venster **Objectverkenner** wordt geopend in SSMS.

5. In **Objectverkenner** vouwt u **Databases** en daarna *yourDatabase* uit om de objecten in de voorbeelddatabase weer te geven.

   ![databaseobjecten](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>Tabellen maken in uw database

Maak met [Transact-SQL](/sql/t-sql/language-reference) een databaseschema met vier tabellen die overeenkomen met belangrijke gegevens uit een studenteninformatiesysteem voor universiteiten:

- Person
- Course
- Student
- Tegoed

Het volgende diagram laat zien hoe deze tabellen aan elkaar zijn gerelateerd. Sommige van deze tabellen verwijzen naar kolommen in andere tabellen. Zo verwijst de tabel *Student* naar de kolom *PersonId* van de tabel *Person*. Bekijk het diagram goed om te begrijpen hoe de tabellen in deze zelfstudie aan elkaar zijn gerelateerd. Zie dit Engelstalige artikel over het [maken van effectieve databasetabellen](/previous-versions/tn-archive/cc505842(v=technet.10)) voor uitgebreide informatie over het maken van effectieve databasetabellen. Zie [Gegevenstypen](/sql/t-sql/data-types/data-types-transact-sql) voor informatie over het kiezen van gegevenstypen.

> [!NOTE]
> U kunt ook de functie voor het [ontwerpen van tabellen in SQL Server Management Studio](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) gebruiken om tabellen te maken en ontwerpen.

![Relaties tussen tabellen](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. Klik in **Objectverkenner** met de rechtermuisknop op *yourDatabase* en selecteer **Nieuwe query**. Er wordt een leeg queryvenster geopend dat is verbonden met uw database.

2. Voer in het queryvenster de volgende query uit om vier tabellen te maken in uw database:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Tabellen maken](./media/design-first-database-tutorial/create-tables.png)

3. Vouw in de **Objectverkenner**, onder *yourDatabase*, het knooppunt **Tables** uit om de tabellen te zien die u hebt gemaakt.

   ![Tabellen gemaakt in SSMS](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Gegevens laden in de tabellen

1. Maak een map *sampleData* in de map Downloads voor het opslaan van voorbeeldgegevens voor uw database.

2. Klik met de rechtermuisknop op de volgende koppelingen en sla deze op in de map *sampleData*.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Open een venster met een opdrachtprompt en ga naar de map *sampleData*.

4. Voer de volgende opdrachten uit om voorbeeldgegevens toe te voegen aan de tabellen. Vervang hierbij de waarden voor *server*, *database*, *user* en *password* door de waarden voor uw omgeving.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

U hebt nu voorbeeldgegevens geladen in de tabellen die u eerder hebt gemaakt.

## <a name="query-data"></a>Querygegevens

Voer de volgende query's uit om gegevens op te halen uit de databasetabellen. Zie [Write SQL queries](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90)) (SQL-query's schrijven) voor meer informatie over het schrijven van SQL-query's. Met de eerste query worden alle vier de tabellen gekoppeld om de studenten te vinden die 'Dominick Pope' als docent hebben en een rang hoger dan 75% hebben. Met de tweede query worden alle vier de tabellen gekoppeld en worden de cursussen gevonden waarvoor 'Noe Coleman' zich ooit heeft ingeschreven.

1. Voer in een venster van SQL Server Management Studio de volgende query uit:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. Voer in een queryvenster de volgende query uit:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een aantal eenvoudige databasetaken geleerd. U hebt geleerd hoe u:

> [!div class="checklist"]
>
> - Een database maken met behulp van de Azure-portal*
> - Een IP-firewallregel op serverniveau instellen met de Azure-portal
> - Verbinding maken met de database via SQL Server Management Studio
> - Tabellen maken met SSMS
> - Gegevens bulksgewijs laden met BCP
> - Query uitvoeren op gegevens met SSMS

Ga naar de volgende zelfstudie als u alles wilt weten over het ontwerpen van een database met Visual Studio en C#.

> [!div class="nextstepaction"]
> [Een relationele database ontwerpen in Azure SQL Database C# en ADO.NET](design-first-database-csharp-tutorial.md)