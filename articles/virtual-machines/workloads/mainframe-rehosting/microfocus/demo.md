---
title: Micro Focus CICS BankDemo instellen voor micro focus Enter prise Developer 4,0 op Azure Virtual Machines
description: Voer de micro focus BankDemo-toepassing uit op Azure Virtual Machines (Vm's) voor meer informatie over het gebruik van micro focus Enter prise server en Enter prise Developer.
author: sread
ms.author: sread
ms.date: 03/30/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7fb72b9a7d0d655f99d1e5cf194f7c6f26976a37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95976190"
---
# <a name="set-up-micro-focus-cics-bankdemo-for-micro-focus-enterprise-developer-40-on-azure"></a>Micro Focus CICS BankDemo instellen voor micro focus Enter prise Developer 4,0 op Azure

Wanneer u micro focus Enter prise Server 4,0 en Enter prise Developer 4,0 op Azure instelt, kunt u implementaties van IBM z/OS-workloads testen. In dit artikel wordt beschreven hoe u CICS BankDemo instelt, een voorbeeld toepassing die wordt geleverd met Enter prise Developer.

CICs staat voor het besturings systeem van klant gegevens, het transactie platform dat door veel van de online mainframe-toepassingen wordt gebruikt. De BankDemo-toepassing is geweldig voor het leren van de werking van zakelijke servers en bedrijfs ontwikkelaars en het beheren en implementeren van een daad werkelijke toepassing die is voltooid met groene scherm terminals.

> [!NOTE]
> Binnenkort beschikbaar: instructies voor het instellen van [micro focus Enter prise Server 5,0](https://techcommunity.microsoft.com/t5/azurecat/micro-focus-enterprise-server-5-0-quick-start-template-on-azure/ba-p/1160110) op Azure-vm's.

## <a name="prerequisites"></a>Vereisten

- Een virtuele machine met [Enter prise Developer](set-up-micro-focus-azure.md). Denk eraan dat bedrijfs ontwikkelaars een volledig exemplaar van ENTER prise server op IT hebben voor ontwikkelings-en test doeleinden. Dit exemplaar is het exemplaar van de Enter prise-server dat wordt gebruikt voor de demo.

- [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). Down load en installeer dit op de Enter prise Developer-VM. Voor Enter prise server is een Data Base vereist voor het beheer van CICS-regio's en de toepassing BankDemo gebruikt ook een SQL Server-Data Base met de naam BANKDEMO. In deze demo wordt ervan uitgegaan dat u SQL Server Express gebruikt voor beide data bases. Wanneer u installeert, selecteert u de basis installatie.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). SSMS wordt gebruikt voor het beheren van de data bases en het uitvoeren van een T-SQL-script. Down load en installeer dit op de Enter prise Developer-VM.

- [Visual Studio 2019](https://azure.microsoft.com/downloads/) met het meest recente Service Pack of de nieuwste [Visual Studio-Community](https://visualstudio.microsoft.com/vs/community/), die u gratis kunt downloaden.

- Rumba desktop of een andere 3270-emulator.

## <a name="configure-the-windows-environment"></a>De Windows-omgeving configureren

Nadat u Enter prise Developer 4,0 hebt geïnstalleerd op de VM, moet u het exemplaar van de Enter prise-server configureren. Hiervoor moet u een aantal extra Windows-onderdelen installeren.

1. Gebruik RDP om u aan te melden bij de Enter prise Server 4,0-VM die u hebt gemaakt.

2. Klik op het **Zoek** pictogram naast de knop **Start** en typ **Windows-onderdelen**. De Serverbeheer wizard functies en onderdelen toevoegen wordt geopend.

3. Selecteer de **rol webserver (IIS)** en controleer vervolgens de volgende opties:

    - Hulpprogram Ma's voor webbeheer
    - Compatibiliteit met IIS 6-beheer (alle beschik bare functies selecteren)
    - IIS-beheerconsole
    - Scripts en Hulpprogram Ma's voor IIS-beheer
    - IIS-beheer service

4. Selecteer **World Wide Web Services** en controleer de volgende opties:

     Functies voor het ontwikkelen van toepassingen:
    - .NET-uitbreidbaarheid
    - ASP.NET
    - Veelvoorkomende HTTP-functies: alle beschik bare functies toevoegen
    - Status en diagnose: alle beschik bare functies toevoegen
    - Beveiligingsprincipal
        - Basisverificatie
        - Windows-verificatie

5. Selecteer **Windows Process Activation Service** en alle bijbehorende onderliggende items.

6. Schakel **Microsoft .NET Framework 3.5.1** in voor **functies** en controleer de volgende opties:

    - HTTP-activering Windows Communication Foundation
    - Windows Communication Foundation niet-HTTP-activering

7. Schakel **Microsoft .net framework 4,6** in voor **functies** en controleer de volgende opties:

   - Named pipe-activering
   - TCP-activering
   - TCP-poort delen

     ![Wizard functies en onderdelen toevoegen: functie Services](media/01-demo-roles.png)

8. Wanneer u alle opties hebt geselecteerd, klikt u op **volgende** om te installeren.

9. Na de Windows-functies gaat u naar **configuratie scherm \> systeem-en beveiligings \> beheer Programma's** en selecteert u **Services**. Schuif omlaag en controleer of de volgende services worden uitgevoerd en stel deze in op **automatisch**:

    - **NetTcpPortSharing**
    - **Listener-adapter net. pipe**
    - **Net. TCP listener-adapter**

10. Als u IIS en ondersteuning wilt configureren, gaat u in het menu naar **micro focus Enter prise Developer command prompt (64-bits)** en voert u als **Administrator** uit.

11. Typ **wassetup – i** en druk op **Enter**.

12. Nadat het script is uitgevoerd, kunt u het venster sluiten.

## <a name="configure-the-local-system-account-for-sql-server"></a>Het lokale systeem account configureren voor SQL Server

Sommige ondernemings server processen moeten kunnen worden aangemeld SQL Server en data bases en andere objecten worden gemaakt. Deze processen gebruiken het lokale systeem account, dus u moet de sysadmin-instantie voor dat account opgeven.

1. Start de **SSMS** en klik op **verbinden** om verbinding te maken met de lokale SQLEXPRESS-server met behulp van Windows-verificatie. Deze moet beschikbaar zijn in de lijst **Server naam** .

2. Vouw aan de linkerkant de map **beveiliging** uit en selecteer **aanmeldingen**.

3. Selecteer **NT Authority \\ System** en selecteer **Eigenschappen**.

4. Selecteer **Server functies** en controleer **sysadmin**.

     ![SSMS-Objectverkenner venster: aanmeldings eigenschappen](media/02-demo-explorer.png)

## <a name="create-the-bankdemo-database-and-all-its-objects"></a>De BankDemo-data base en alle bijbehorende objecten maken

1. Open **Windows Verkenner** en ga naar **C: \\ gebruikers \\ Public- \\ documenten \\ micro focus \\ Enter prise Developer \\ samples, \\ mainframe \\ CICS \\ DotNet \\ BankDemo \\ SQL**.

2. Kopieer de inhoud van het bestand **BankDemoCreateAll. SQL** naar het klem bord.

3. Open **SSMS**. Klik aan de rechter kant op **Server** en selecteer **nieuwe query**.

4. Plak de inhoud van het klem bord in het vak **nieuwe query** .

5. Voer de SQL-instructie uit door te klikken op **uitvoeren** op het tabblad **opdracht** boven de query.

De query moet zonder fouten worden uitgevoerd. Wanneer deze is voltooid, hebt u de voorbeeld database voor de toepassing BankDemo.

![SQLQuery1. SQL-uitvoer](media/03-demo-query.png)

## <a name="verify-that-the-database-tables-and-objects-have-been-created"></a>Controleren of de database tabellen en objecten zijn gemaakt

1. Klik met de rechter muisknop op de **BANKDEMO** -data base en selecteer **vernieuwen**.

2. Vouw de **Data Base** uit en selecteer **tabellen**. U ziet nu het volgende:

     ![BANKDEMO-tabel uitgevouwen Objectverkenner](media/04-demo-explorer.png)

## <a name="build-the-application-in-enterprise-developer"></a>De toepassing bouwen in Enter prise Developer

1. Open Visual Studio en meld u aan.

2. Selecteer in het menu **bestand** de optie **project/oplossing openen**, ga naar **C: \\ gebruikers \\ open bare \\ documenten \\ micro focus \\ Enter prise Developer \\ samples \\ mainframe \\ CICS \\ DotNet \\ BankDemo** en selecteer het **SLN** -bestand.

3. Neem even de tijd om de objecten te onderzoeken. COBOL-Program ma's worden weer gegeven in Solution Explorer met de CBL-extensie samen met CopyBooks (CPY) en JCL.

4. Klik met de rechter muisknop op het **BankDemo2** -project en selecteer **set as Startup project**.

    > [!NOTE]
    > Het BankDemo-project maakt gebruik van HCOSS (host Compatibility option for SQL Server), die niet wordt gebruikt voor deze demo.

5. Klik in **Solution Explorer** met de rechter muisknop op het project **BankDemo2** en selecteer **Build**.

    > [!NOTE]
    > Het bouwen op het niveau van de oplossing resulteert in fouten, omdat HCOSS niet is geconfigureerd.

6. Wanneer het project is gebouwd, controleert u het **uitvoer** venster. Het zou eruit moeten zien als in de onderstaande afbeelding.

     ![Uitvoer venster met geslaagde build](media/05-demo-output.png)

## <a name="deploy-the-bankdemo-application-into-the-region-database"></a>De BankDemo-toepassing implementeren in de regio database

1. Open een Enter prise Developer-opdracht prompt (64-bits) als Administrator.

2. Navigeer naar het **% Public% \\ Documents \\ micro focus \\ Enter prise Developer \\ samples \\ mainframe \\ CICS \\ DotNet \\ BankDemo**.

3. Voer bij de opdracht prompt **bankdemodbdeploy** uit en neem de para meter voor de Data Base op die u wilt implementeren, bijvoorbeeld:

    ```
    bankdemodbdeploy (local)/sqlexpress
    ```

> [!NOTE]
> Zorg ervoor dat u een slash (/) gebruikt als geen back slash ( \\ ). Dit script wordt enige tijd uitgevoerd.

![Beheer: Enter prise Developer-opdracht prompt venster](media/06-demo-cmd.png)

## <a name="create-the-bankdemo-region-in-enterprise-administrator-for-net"></a>De regio BankDemo maken in de Enter prise-beheerder voor .NET

1. Open de gebruikers interface **van de Enter prise-server voor .net-beheer** .

2. Als u de MMC-module wilt starten, klikt u in het menu **Start** van Windows op **micro focus Enter prise Developer \> Configuration \> Enter prise server voor .net admin**. (Voor Windows Server kiest u **micro focus Enter prise Developer \> Enter prise server voor .net-beheer**).

3. Vouw de container **regio's** in het linkerdeel venster uit en klik vervolgens met de rechter muisknop op **CICS**.

4. Selecteer **regio definiëren** om een nieuwe CICS-regio te maken met de naam **BANKDEMO**, die wordt gehost in de (lokale) data base.

5. Geef het Database Server exemplaar op, klik op **volgende** en voer vervolgens de naam van de regio in **BANKDEMO**.

     ![Het dialoog venster regio definiëren](media/07-demo-cics.png)

6. Als u het definitie bestand van de regio wilt selecteren voor de data base met meerdere regio's, zoekt u **regio \_ bankdemo \_db.config** in **C: \\ gebruikers \\ Public- \\ documenten \\ micro focus \\ Enter prise Developer \\ samples \\ mainframe \\ CICS \\ DotNet \\ bankdemo**.

     ![Regio-regio naam definiëren: BANKDEMO](media/08-demo-cics.png)

7. Klik op **Voltooien**.

## <a name="create-xa-resource-definitions"></a>XA-resource definities maken

1. In het linkerdeel venster van de gebruikers interface van de **Enter prise-server voor .net-beheer** , vouwt u **System** en **XA-resource definities** uit. Met deze instelling wordt gedefinieerd hoe de regio samenwerkt met de Enter prise-server en de toepassings databases.

2. Klik met de rechter muisknop op **XA-resource definities** en selecteer **Server exemplaar toevoegen**.

3. Selecteer in de vervolg keuzelijst **Data Base service-exemplaar**. Dit is de lokale computer SQLEXPRESS.

4. Selecteer het exemplaar in de container **XA-resource definities (MachineName \\ )** en klik op **toevoegen**.

5. Selecteer de **Data Base XA-resource definitie** en typ **BANKDEMO** voor de **naam** en **regio**.

     ![Het scherm nieuwe data base XA-resource definitie](media/09-demo-xa.png)

6. Klik op de weglatings tekens (**...**) om de wizard verbindings reeks te openen. Typ **(lokaal) \\ SQLEXPRESS** voor **Server naam**. Selecteer voor **Aanmelden** **Windows-verificatie**. Typ **BANKDEMO** voor database naam

     ![Scherm verbindings reeks bewerken](media/10-demo-string.png)

7. Test de verbinding.

## <a name="start-the-bankdemo-region"></a>De BANKDEMO-regio starten

> [!NOTE]
> De eerste stap is belang rijk: u moet de regio instellen op het gebruik van de XA-resource definitie die u zojuist hebt gemaakt.

1. Ga naar de **BANDEMO CICS-regio** onder de **container regio's** en selecteer vervolgens **regio opstart bestand bewerken** in het deel venster **acties** . Schuif omlaag naar de SQL-eigenschappen en voer **bankdemo** in als de naam van de **XA-resource**, of gebruik de beletsel tekens om deze te selecteren.

2. Klik op het pictogram **Opslaan** om uw wijzigingen op te slaan.

3. Klik met de rechter muisknop op de **BANKDEMO CICS-regio** in het **console** venster en selecteer **Start/Stop-regio**.

4. Selecteer **Start** in het vak **Start/Stop regio** dat wordt weer gegeven in het middelste deel venster. Na een paar seconden begint de regio.

     ![Het vak SQL starten/stoppen](media/11-demo-sql.png)

     ![CICS-regio BANKDEMO-Start scherm](media/12-demo-cics.png)

## <a name="create-a-listener"></a>Een listener maken

Maak een listener voor de TN3270-sessies die toegang hebben tot de BankDemo-toepassing.

1. Vouw in het linkerdeel venster **configuratie-editors** uit en selecteer **listener**.

2. Klik op het pictogram **bestand openen** en selecteer het **seelistener.exe.config** bestand. Dit bestand wordt bewerkt en wordt geladen elke keer dat de Enter prise-server wordt gestart.

3. Let op de twee eerder gedefinieerde regio's (ESDEMO en JCLDEMO).

4. Als u een nieuwe regio voor BANKDEMO wilt maken, klikt u met de rechter muisknop op **regio's** en selecteert u **regio toevoegen**.

5. Selecteer de **BANKDEMO-regio**.

6. Voeg een TN3270-kanaal toe door met de rechter muisknop op **BANKDEMO-regio** te klikken en **kanaal toevoegen** te selecteren.

7. Voer bij **naam** **TN3270** in. Voer voor **poort** **9024** in. De ESDEMO-toepassing gebruikt poort 9230, dus u moet een andere poort gebruiken.

8. Als u het bestand wilt opslaan, klikt u op het pictogram **Opslaan** of kiest u **bestand** \> **Opslaan**.

9. Als u de listener wilt starten, klikt u op het pictogram **listener starten** of kiest u de **optie** \> **listener starten**.

     ![Windows-configuratie-editor voor listener](media/13-demo-listener.png)


## <a name="configure-rumba-to-access-the-bankdemo-application"></a>Rumba configureren voor toegang tot de BankDemo-toepassing

Het laatste wat u moet doen, is het configureren van een 3270-sessie met behulp van Rumba, een 3270-emulator. Met deze stap kunt u toegang krijgen tot de BankDemo-toepassing via de listener die u hebt gemaakt.

1. Start Rumba desktop in het menu **Start** van Windows.

2. Selecteer in het menu-item **Connections** de optie **TN3270**.

3. Klik op **Invoegen** en typ **127.0.0.1** voor het IP-adres en **9024** voor de door de gebruiker gedefinieerde poort.

4. Klik onder aan het dialoog venster op **verbinden**. Er wordt een zwart CICS-scherm weer gegeven.

5. Typ **Bank** om het eerste scherm van 3270 weer te geven voor de toepassing BankDemo.

6. Voor gebruikers-ID typt u **B0001** en voert u een wille keurig wacht woord in. Het eerste scherm BANK20 wordt geopend.

![Mainframe weer geven welkomst scherm ](media/14-demo.png)
 ![ -weer gave-Rumba-subsysteem demonstratie scherm](media/15-demo.png)

Gefeliciteerd U voert nu een CICS-toepassing uit in azure met micro focus Enter prise server.

## <a name="next-steps"></a>Volgende stappen

- [Enter prise server uitvoeren in docker-containers in azure](run-enterprise-server-container.md)
- [Mainframe-migratie-Portal](/archive/blogs/azurecat/mainframe-migration-to-azure-portal)
- [Virtual Machines](../../../linux/overview.md)
- [Problemen oplossen](../../../troubleshooting/index.yml)
- [Ontrafelen mainframe naar Azure-migratie](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)
