---
title: Stel een lab in om te beheren en te ontwikkelen met Azure SQL Database | Azure Lab Services
description: Meer informatie over het instellen van een lab om te beheren en te ontwikkelen met Azure SQL Database.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: fb1b9e3458d08b8387c7f3978ff83c097fad2375
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644029"
---
# <a name="set-up-a-lab-to-manage-and-develop-with-sql-server"></a>Een Lab instellen om te beheren en te ontwikkelen met SQL Server

In dit artikel wordt beschreven hoe u een Lab instelt voor een basis SQL Server-en ontwikkelings klasse in Azure Lab Services.  Database concepten zijn een van de in de meeste computer wetenschappen geschoolde cursussen. Structured Query Language (SQL) is een internationale standaard.  SQL is de standaard taal voor het beheer van relationele data bases, zoals het toevoegen, openen en beheren van inhoud in een Data Base.  Het wordt het meest vermeld voor de snelle verwerking, bewezen betrouw baarheid, het gemak en de flexibiliteit van gebruik.

In dit artikel laten we zien hoe u een virtuele-machine sjabloon kunt instellen in een Lab met [Visual Studio 2019](https://visualstudio.microsoft.com/vs/), [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)en [Azure Data Studio](https://github.com/microsoft/azuredatastudio).  Voor dit lab wordt één gedeelde [SQL Server-Data Base](../azure-sql/database/sql-database-paas-overview.md) gebruikt voor het hele Lab. [Azure SQL database](../azure-sql/database/sql-database-paas-overview.md) is platform as a Service (PaaS) data base engine-aanbieding van Azure.

## <a name="lab-configuration"></a>Lab-configuratie

Als u dit Lab wilt instellen, hebt u een Azure-abonnement en een Lab-account nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services. Zie [zelf studie voor het instellen van een Lab-account](./tutorial-setup-lab-account.md)voor meer informatie over het maken van een nieuw Lab-account. U kunt ook een bestaand Lab-account gebruiken.

### <a name="lab-account-settings"></a>Instellingen van Lab-account

Schakel de instellingen die worden beschreven in de onderstaande tabel voor het lab-account in. Zie [Marketplace-installatie kopieën die beschikbaar zijn voor Lab-makers opgeven](specify-marketplace-images.md)voor meer informatie over het inschakelen van Marketplace-installatie kopieën.

| Account instelling Lab | Instructies |
| ------------------- | ------------ |
| Marketplace-installatiekopie | Schakel de installatie kopie van de Visual Studio 2019-Community (meest recente versie) op Windows 10 Enter prise N (x64) in voor gebruik binnen uw Lab-account. |

### <a name="shared-resource-configuration"></a>Configuratie van gedeelde bronnen

Als u een gedeelde bron in Lab-Services wilt gebruiken, moet u eerst het virtuele netwerk en de resources zelf maken.  Als u het virtuele netwerk wilt maken en verbinding wilt maken met het lab, volgt u [hoe u een Lab maakt met een gedeelde bron in Azure Lab Services](how-to-create-a-lab-with-shared-resource.md).  Houd er rekening mee dat alle resources die extern zijn voor Lab-services afzonderlijk worden gefactureerd en niet worden opgenomen in schattingen voor de kosten van het lab.

>[!WARNING]
>Gedeelde bronnen voor een lab moeten worden ingesteld voordat het lab wordt gemaakt.  Als het vnet niet is gekoppeld [aan het lab-account](how-to-connect-peer-virtual-network.md) *voordat* het lab wordt gemaakt, heeft het Lab geen toegang tot de gedeelde bron.

Nu de netwerk zijde van dingen wordt verwerkt, kunt u een SQL Server Data Base maken.  We gaan een [afzonderlijke data base](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal) maken, omdat dit de snelste implementatie optie is voor Azure SQL database.  Voor andere implementatie opties maakt u een [elastische pool](../azure-sql/database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal), een [beheerd exemplaar](../azure-sql/managed-instance/instance-create-quickstart.md)of een [virtuele SQL-machine](../azure-sql/virtual-machines/windows/sql-vm-create-portal-quickstart.md).

1. Kies in het menu Azure Portal de optie **nieuwe resource maken**.
2. Kies **SQL database** en klik op de knop **maken** .
3. Selecteer op het tabblad **basis beginselen** van het formulier **Create SQL database** de resource groep voor de data base.  We gebruiken *sqldb-RG*.
4. Voer *classlabdb* in bij **database naam**.
5. Klik onder de **Server** instelling op **Nieuw maken** om een nieuwe server te maken voor de data base.
6. Voer de server naam in de flyout **nieuwe server** in.  We gebruiken *classlabdbserver*.  De server naam moet wereld wijd uniek zijn.
7. Voer *azureuser* in voor de aanmelding van de **Server beheerder**.
8. Voer een wacht woord in dat u wilt onthouden.  Het wacht woord moet ten minste acht tekens lang zijn en speciale tekens bevatten.
9. Kies een regio voor de **locatie**.  Voer, indien mogelijk, dezelfde locatie in als het lab-account en het gepeerde vnet om latentie te minimaliseren.
10. Klik op **OK** om terug te keren naar het formulier **SQL database maken** .
11. Klik op **Data Base** -koppeling configureren onder de waarde voor **berekening en opslag** .
12. Wijzig indien nodig de data base-instellingen voor de klasse.  U kunt kiezen tussen ingerichte en Serverloze opties.  Voor dit voor beeld gebruiken we de optie voor automatisch geschaalde Serverloze met een maximale vCores van 4, min vCores van 1. De instelling voor autopause wordt ten minste 1 uur bewaard. Klik op **Toepassen**.
13. Klik op **volgende: netwerk** knop.
14. Kies op het tabblad netwerk de optie persoonlijk eind punt voor de **verbindings methode**.
15. Klik onder de sectie **privé-eind punten** op **persoonlijk eind punt toevoegen**.
16. Kies in de flyout **persoonlijk eind punt maken** dezelfde resource groep als uw virtuele netwerk dat is gekoppeld aan het lab-account.
17. Kies bij **locatie** dezelfde locatie als het virtuele netwerk.
18. Voer bij **naam** *labsql-endpt* in.
19. Zorg ervoor dat de subresource van de doel groep is ingesteld op SqlServer.
20. Kies voor **virtueel netwerk** hetzelfde virtuele netwerk dat is gekoppeld aan het lab-account.
21. Kies bij **subnet** het subnet waarvoor u het eind punt wilt hosten.  Het IP-adres dat is toegewezen aan het eind punt, is afkomstig uit het bereik dat is toegewezen aan dat subnet.
22. Stel **integratie met privé-DNS** in op **Nee**. Ter vereenvoudiging gebruiken we de DNS-server van Azure via de eigen privé-DNS-zone of onze eigen DNS-servers.
23. Klik op **OK**.
24. Klik op **volgende: aanvullende instellingen**.
25. Kies voor de instelling **bestaande gegevens gebruiken** de optie voor **beeld**.  De gegevens uit de AdventureWorksLT-Data Base worden gebruikt wanneer de data base wordt gemaakt.
26. Klik op **Controleren + maken**.
27. Klik op **Create**.

Zodra de SQL Database-implementatie is voltooid, kunnen we de test omgeving maken en software installeren op de test sjabloon machine.

### <a name="lab-settings"></a>Lab-instellingen

Gebruik de instellingen in de onderstaande tabel bij het instellen van een leslokaal Lab. Voor meer informatie over het maken van een leslokaal Lab raadpleegt u [een klassikale Lab-zelf studie instellen](tutorial-setup-classroom-lab.md).

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ |
| Grootte van de virtuele machine | Gemiddeld. Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse. |
| Installatie kopie van virtuele machine | Visual Studio 2019-Community (meest recente versie) op Windows 10 Enter prise N (x64) |

Nu het lab is gemaakt, gaan we de sjabloon machine wijzigen met de software die we nodig hebben.

## <a name="visual-studio"></a>Visual Studio

De hierboven gekozen afbeelding bevat [Visual Studio 2019 Community](https://visualstudio.microsoft.com/vs/community/).  Alle werk belastingen en gereedschappen sets zijn al geïnstalleerd op de installatie kopie.  Gebruik het installatie programma van Visual Studio om [eventueel optionele hulpprogram ma's te installeren](/visualstudio/install/modify-visual-studio?view=vs-2019&preserve-view=true) .  [Meld u aan bij Visual Studio](/visualstudio/ide/signing-in-to-visual-studio?view=vs-2019&preserve-view=true#how-to-sign-in-to-visual-studio) om de Community-editie te ontgrendelen.

Visual Studio bevat het hulp programma voor het **opslaan en verwerken van gegevens** , dat SQL Server Data Tools bevat (SSDT).  Zie [SQL Server Data Tools Overview](/sql/ssdt/sql-server-data-tools)voor meer informatie over de mogelijkheden van SSDT.  Zie [verbinding maken met een Data Base en bladeren door bestaande objecten](/sql/ssdt/how-to-connect-to-a-database-and-browse-existing-objects)om te controleren of de verbinding met de gedeelde SQL Server voor de klasse is geslaagd. Als u wordt gevraagd om de sjabloon computer-IP toe te voegen aan de [lijst met toegestane computers](../azure-sql/database/firewall-configure.md) die verbinding kunnen maken met uw SQL Server-exemplaar.

Visual Studio biedt ondersteuning voor verschillende werk belastingen, inclusief **Web-& Cloud** -en **Desktop & mobiele** werk belastingen.  Beide werk belastingen ondersteunen SQL Server als een gegevens bron. Zie [een ASP.net core-en SQL database-app bouwen in azure app service](../app-service/tutorial-dotnetcore-sqldb-app.md) zelf studie voor meer informatie over het gebruik van ASP.NET Core naar SQL Server.  Gebruik de [System. data. SqlClient](/dotnet/api/system.data.sqlclient) -bibliotheek om verbinding te maken met een SQL database vanuit een [Xamarin](/xamarin) -app.

## <a name="install-azure-data-studio"></a>Installeer Azure Data Studio

[Azure Data Studio](https://github.com/microsoft/azuredatastudio) is een multi-data base, platform omgeving voor meerdere platforms voor data professionals die gebruikmaken van de familie van on-premises en Cloud gegevens platformen in Windows, MacOS en Linux.

1. Down load het [installatie programma voor het Azure Data Studio *systeem* voor Windows](https://go.microsoft.com/fwlink/?linkid=2127432). Ga naar de download pagina van [Azure Data Studio](/sql/azure-data-studio/download) om installatie Programma's te vinden voor andere ondersteunde besturings systemen.
2. Selecteer op de pagina **gebruiksrecht overeenkomst** **de optie Ik ga akkoord met de overeenkomst**. Klik op **Volgende**.
3. Klik op de pagina **Bestemmingslocatie selecteren** op **Volgende**.
4. Klik op de pagina **Map van het menu Start selecteren** op **Volgende**.
5. Schakel op de pagina **extra taken selecteren** het **pictogram bureau blad maken** in als u een bureaublad pictogram wilt.  Klik op **Volgende**.
6. Klik op **volgende** wanneer **u klaar bent om te installeren**.
7. Wacht totdat het installatie programma wordt uitgevoerd.  Klik op **Voltooien**.

Nu u Azure Data Studio hebt geïnstalleerd, kunt u de verbinding met Azure SQL Database instellen.

1. Klik op de **welkomst** pagina voor Azure Data Studio op de koppeling **nieuwe verbinding** .
2. Vul in het vak **verbindings gegevens** de benodigde gegevens in.
    - **Server** instellen op *classlabdbserver.database.Windows.net*
    - **Gebruikers** naam instellen op *azureuser*
    - Stel het **wacht woord** in voor het wacht woord dat wordt gebruikt om de data base te maken.
    - Controleer **wacht woord onthouden**.
    - Selecteer voor de **Data Base** *classlabdb*.
3. Klik op **Verbinden**.

## <a name="install-sql-server-management-studio"></a>Installeer SQL Server Management Studio

[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) is een geïntegreerde omgeving voor het beheren van een SQL-infra structuur.  SSMS is een hulp programma dat door database beheerders wordt gebruikt om de gegevens infrastructuur te implementeren, te bewaken en bij te werken.

1. [Down load SQL Server Management Studio](https://aka.ms/ssmsfullsetup). Start het installatie programma zodra het is gedownload.
2. Klik op de pagina **Welkom** op **installeren**.
3. Klik op de pagina **Setup is voltooid** op **sluiten**.
4. Start SQL Server-Management Studio.  
5. Klik op de pagina **afhankelijkheids configuratie proces** op **sluiten**.

Niet dat SSMS is geïnstalleerd, kunt u [verbinding maken en query's uitvoeren op een SQL Server](/sql/ssms/tutorials/connect-query-sql-server). Gebruik de volgende waarden bij het instellen van de verbinding:

- Server type: data base-engine
- Server naam: *classlabdbserver.database.Windows.net*
- Verificatie: SQL Server-verificatie
- Aanmelding: *azureuser*
- Wacht woord: het wacht woord dat wordt gebruikt om de data base te maken.

## <a name="cost-estimate"></a>Kosten raming

Laten we een mogelijke schatting van de kosten voor deze klasse beslaan. De schatting omvat niet de kosten voor het uitvoeren van de SQL Server.  Zie [SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database) voor actuele Details over de database prijzen.

We gebruiken een klasse van 25 studenten. Er zijn 20 uur geplande tijd voor de klasse. Daarnaast krijgt elke student tien uur quota voor huis werk of toewijzingen buiten een geplande klasse tijd. Het formaat van de virtuele machine die we hebben gekozen is gemiddeld, dat wil zeggen 42 Lab-eenheden.

Hier volgt een voor beeld van een mogelijke schatting van de kosten voor deze klasse:

25 studenten \* (20 geplande uren \+ 10 quotum uren) * 0,42 USD per uur = 315,00 USD

>[!IMPORTANT]
>Kosten raming is alleen bedoeld als voor beeld. Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor actuele Details over prijzen.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken, beheren en publiceren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)