---
title: Verbinding maken met SQL Database met behulp van C en C++
description: Gebruik de voorbeeld code in deze Quick Start om een moderne toepassing te bouwen met C++ en te worden ondersteund door een krachtige relationele data base in de Cloud met Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: e891c5797c9ce93e6cab7a07d2f68de1a9157249
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674768"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Verbinding maken met SQL Database met behulp van C en C++
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dit bericht is bedoeld om C-en C++-ontwikkel aars om verbinding te maken met Azure SQL Database. Het wordt onderverdeeld in secties, zodat u naar de sectie kunt gaan die uw interesse het beste vastlegt.

## <a name="prerequisites-for-the-cc-tutorial"></a>Vereisten voor de C/C++-zelf studie

Zorg ervoor dat u over de volgende zaken beschikt:

* Een actief Azure-account. Als u nog geen abonnement hebt, kunt u zich registreren voor een [gratis Azure-proefversie](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). U moet de C++-taal onderdelen installeren om dit voor beeld te bouwen en uit te voeren.
* [Ontwikkeling van Visual Studio Linux](/cpp/linux/?view=vs-2019). Als u op Linux ontwikkelt, moet u ook de Visual Studio Linux-extensie installeren.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database en SQL Server op virtuele machines

Azure SQL Database is gebaseerd op Microsoft SQL Server en is ontworpen om een hoge Beschik baarheid, een krachtige en schaal bare service te bieden. Er zijn veel voor delen voor het gebruik van Azure SQL over uw eigen data base die on-premises wordt uitgevoerd. Met Azure SQL hoeft u uw data base niet te installeren, in te stellen, te onderhouden of te beheren, maar alleen de inhoud en de structuur van uw data base. Typische dingen die u zorgen maakt voor data bases zoals fout tolerantie en redundantie zijn allemaal ingebouwd.

Azure heeft momenteel twee opties voor het hosten van SQL Server-workloads: Azure SQL Database, Database as a Service en SQL Server op Virtual Machines (VM). We zullen geen details over de verschillen tussen deze twee zien, behalve dat Azure SQL Database uw beste treffer is voor nieuwe Cloud toepassingen om te profiteren van de kosten besparingen en prestatie optimalisatie die Cloud Services bieden. Als u overweegt om uw on-premises toepassingen te migreren of uit te breiden naar de Cloud, is SQL Server op Azure virtual machine mogelijk beter voor u. We gaan nu een Azure SQL Database maken om alles te blijven gebruiken voor dit artikel.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Technologieën voor gegevens toegang: ODBC en OLE DB

Verbinding maken met Azure SQL Database is niet anders en er zijn momenteel twee manieren om verbinding te maken met data bases: ODBC (Open Data Base Connectivity) en OLE DB (object linking and Embedding data base). In de afgelopen jaren is micro soft afgestemd op [ODBC voor native relationele gegevens toegang](/archive/blogs/sqlnativeclient/microsoft-is-aligning-with-odbc-for-native-relational-data-access). ODBC is relatief eenvoudig en ook veel sneller dan OLE DB. Het enige voor behoud hier is dat ODBC een oude C-style-API gebruikt.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Stap 1: uw Azure SQL Database maken

Zie de [pagina](single-database-create-quickstart.md) aan de slag voor meer informatie over het maken van een voorbeeld database.  U kunt ook deze [korte video van twee minuten](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) volgen om een Azure SQL database te maken met behulp van de Azure Portal.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Stap 2: connection string ophalen

Nadat uw Azure SQL Database is ingericht, moet u de volgende stappen uitvoeren om verbindings gegevens te bepalen en uw client-IP toe te voegen voor toegang tot de firewall.

Ga in [Azure Portal](https://portal.azure.com/)naar uw Azure SQL database ODBC-Connection String met behulp van de **verbindings reeksen Data Base weer geven** die worden weer gegeven als onderdeel van de sectie Overzicht voor uw Data Base:

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

Kopieer de inhoud van de **ODBC (inclusief Node.js) [SQL-verificatie]** teken reeks. We gebruiken deze teken reeks later om verbinding te maken via de opdracht regel-interpreter van de C++ ODBC. Deze teken reeks bevat details zoals het stuur programma, de server en andere database verbindings parameters.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Stap 3: uw IP toevoegen aan de firewall

Ga naar de sectie Firewall voor uw server en voeg het [IP-adres van de client toe aan de firewall met behulp van de volgende stappen](firewall-configure.md) om ervoor te zorgen dat er een geslaagde verbinding kan worden gemaakt:

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

Op dit moment hebt u uw Azure SQL Database geconfigureerd en kunt u verbinding maken vanuit uw C++-code.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Stap 4: verbinding maken vanaf een Windows C/C++-toepassing

U kunt eenvoudig verbinding maken met uw [Azure SQL database met behulp van ODBC in Windows met behulp van dit voor beeld](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) dat is gebaseerd op Visual Studio. Het voor beeld implementeert een ODBC-opdracht regel programma dat kan worden gebruikt om verbinding te maken met onze Azure SQL Database. In dit voor beeld wordt een DSN-bestand (data base source name File) gebruikt als een opdracht regel argument of de uitgebreide connection string die u eerder hebt gekopieerd uit de Azure Portal. Breng de eigenschappen pagina voor dit project en plak de connection string als een opdracht argument, zoals hier wordt weer gegeven:

![DSN-Propsfile](./media/develop-cplusplus-simple/props.png)

Zorg ervoor dat u de juiste verificatie gegevens voor uw data base opgeeft als onderdeel van de data base connection string.

Start de toepassing om deze te bouwen. U ziet nu het volgende venster met de validatie van een geslaagde verbinding. U kunt zelfs een aantal eenvoudige SQL-opdrachten uitvoeren zoals **Create Table** om uw database verbinding te valideren:

![SQL-opdrachten](./media/develop-cplusplus-simple/sqlcommands.png)

U kunt ook een DSN-bestand maken met behulp van de wizard die wordt gestart wanneer er geen opdracht argumenten worden gegeven. U wordt aangeraden deze optie ook te proberen. U kunt dit DSN-bestand voor automatisering gebruiken en uw verificatie-instellingen beschermen:

![DSN-bestand maken](./media/develop-cplusplus-simple/datasource.png)

Gefeliciteerd U hebt nu verbinding gemaakt met Azure SQL via C++ en ODBC in Windows. U kunt ook door gaan met lezen om hetzelfde te doen voor Linux-platform.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Stap 5: verbinding maken vanuit een Linux C/C++-toepassing

Als u het nieuws nog niet hebt gehoord, kunt u met Visual Studio nu ook de C++ Linux-toepassing ontwikkelen. Meer informatie over dit nieuwe scenario vindt u in de [ontwikkel blog van Visual C++ voor Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) . Als u voor Linux wilt bouwen, hebt u een externe computer nodig waarop uw Linux-distributie wordt uitgevoerd. Als u er nog geen hebt, kunt u snel een snelle set instellen met behulp van [virtuele Linux Azure-machines](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Voor deze zelf studie gaan we ervan uit dat u een Ubuntu 16,04 Linux-distributie hebt ingesteld. Deze stappen moeten ook van toepassing zijn op Ubuntu 15,10, Red Hat 6 en Red Hat 7.

In de volgende stappen worden de bibliotheken geïnstalleerd die nodig zijn voor SQL en ODBC voor uw distributie:

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

Start Visual Studio. Voeg onder extra-> opties-> Kruis platform-> verbindings beheer een verbinding toe aan uw Linux-vak:

![Extra opties](./media/develop-cplusplus-simple/tools.png)

Nadat de verbinding via SSH tot stand is gebracht, maakt u een sjabloon voor een leeg project (Linux):

![Nieuwe project sjabloon](./media/develop-cplusplus-simple/template.png)

Vervolgens kunt u een [Nieuw C-bron bestand toevoegen en dit vervangen door deze inhoud](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Met behulp van de ODBC Api's SQLAllocHandle, SQLSetConnectAttr en SQLDriverConnect moet u een verbinding met uw data base kunnen initialiseren en tot stand brengen.
Net als bij het voor beeld van Windows ODBC moet u de SQLDriverConnect-aanroep vervangen door de gegevens uit de data base connection string para meters die u eerder van de Azure Portal hebt gekopieerd.

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

Het laatste wat u moet doen vóór het compileren is om **ODBC** toe te voegen als een bibliotheek afhankelijkheid:

![ODBC toevoegen als een invoer bibliotheek](./media/develop-cplusplus-simple/lib.png)

Als u uw toepassing wilt starten, gaat u naar de Linux-console vanuit het menu **fout opsporing** :

![Linux-console](./media/develop-cplusplus-simple/linuxconsole.png)

Als de verbinding is geslaagd, ziet u nu de huidige database naam in de Linux-console:

![Venster uitvoer Linux-console](./media/develop-cplusplus-simple/linuxconsolewindow.png)

Gefeliciteerd U hebt de zelf studie voltooid en kunt nu verbinding maken met uw Azure SQL Database vanuit C++ op Windows-en Linux-platforms.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>De volledige oplossing C/C++-zelf studie ophalen

U kunt de GetStarted-oplossing vinden die alle voor beelden in dit artikel bevat op GitHub:

* Voor [beeld van ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29): down load het Windows C++ ODBC-voor beeld om verbinding te maken met Azure SQL
* Voor [beeld van ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29): down load het ODBC-voor beeld Linux c++ om verbinding te maken met Azure SQL

## <a name="next-steps"></a>Volgende stappen

* Bekijk het [overzicht](develop-overview.md) van de SQL database-ontwikkeling
* Meer informatie over de [ODBC API-verwijzing](/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Aanvullende bronnen

* [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](saas-tenancy-app-design-patterns.md)
* Bekijk alle [mogelijkheden van SQL Database](https://azure.microsoft.com/services/sql-database/)