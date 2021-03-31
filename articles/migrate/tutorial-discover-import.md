---
title: On-premises servers evalueren met een geïmporteerd CSV-bestand met Azure Migrate-serverevaluatie
description: Beschrijft hoe u on-premises servers kunt detecteren voor migratie naar Azure met behulp van een geïmporteerd CSV-bestand in Azure Migrate-serverevaluatie
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: dfa7ee941e2c373b02fe5fb2f2a648a60a677670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96753106"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Zelfstudie: Servers evalueren aan de hand van een geïmporteerd CSV-bestand

Als onderdeel van de migratie naar Azure, detecteert u uw on-premises inventaris en werkbelastingen. 

In deze zelfstudie wordt uitgelegd hoe u on-premises machines kunt beoordelen met Azure Migrate: Serverevaluatie aan de hand van een geïmporteerd CSV-bestand (door komma gescheiden waarden). 

Als u een CSV-bestand gebruikt, hoeft u het Azure Migrate-apparaat niet in te stellen om servers te detecteren en beoordelen. U kunt de gegevens die u in het bestand deelt, beheren en veel van de gegevens zijn optioneel. Deze methode is handig als:

- U een snelle, initiële evaluatie wilt maken voordat u het apparaat implementeert.
- U het Azure Migrate-apparaat niet in uw organisatie kunt implementeren.
- U geen referenties kunt delen die toegang bieden tot on-premises servers.
- Uanwege beveiligingsbeperkingen geen gegevens kunt verzamelen en verzenden die zijn verzameld door het apparaat naar Azure.

> [!NOTE]
> U kunt geen geïmporteerde servers migreren met behulp van een CSV-bestand.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Azure-account instellen
> * Een Azure Migrate-project instellen
> * Een CSV-bestand voorbereiden
> * Het bestand importeren
> * Servers evalueren

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken waar mogelijk de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- U kunt maximaal 20.000 servers in een Azure Migrate-project toevoegen met behulp van één CSV-bestand. 
- De namen van besturingssystemen die in het CSV-bestand zijn opgegeven, moeten [ondersteunde namen](#supported-operating-system-names) bevatten en er mee overeenkomen.


## <a name="prepare-an-azure-user-account"></a>Een Azure-gebruikersaccount voorbereiden

Om een Azure Migrate-project te maken, hebt u een account nodig met:
- Machtigingen op inzender- of eigenaarniveau voor een Azure-abonnement.
- Machtigingen verlenen om Azure Active Directory-apps te registreren.

Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement. Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de volgende machtigingen toe te wijzen:

1. Zoek in de Azure Portal naar 'Abonnementen' en selecteer onder **Services** **Abonnementen**.

    ![Zoekvak om te zoeken naar het Azure-abonnement](./media/tutorial-discover-import/search-subscription.png)

2. Selecteer op de pagina **Abonnementen** het abonnement waarin u een Azure Migrate-project wilt maken. 
3. Selecteer onder het abonnement de optie **Toegangsbeheer (IAM)**  > **Toegang controleren**.
4. Zoek onder **Toegang controleren** naar het relevante gebruikersaccount.
5. Klik onder **Een roltoewijzing toevoegen** op **Toevoegen**.

    ![Een gebruikersaccount zoeken om toegang te controleren en een rol toe te wijzen](./media/tutorial-discover-import/azure-account-access.png)

6. Selecteer onder **Roltoewijzing toevoegen** de rol Inzender of Eigenaar en selecteer account (azmigrateuser in ons voorbeeld). Klik vervolgens op **Opslaan**.

    ![Hiermee opent u de pagina Roltoewijzing toevoegen om een rol aan het account toe te wijzen](./media/tutorial-discover-import/assign-role.png)

7. Zoek in de portal naar gebruikers en selecteer onder **Services** **Gebruikers**.
8. Controleer onder **Gebruikersinstellingen** of Azure AD-gebruikers toepassingen kunnen registreren (standaard ingesteld op **Ja**).

    ![Verifiëren onder Gebruikersinstellingen of gebruikers Active Directory-apps kunnen registreren](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Een project instellen

Stel een nieuw Azure Migrate-project in als u dat nog niet hebt.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer onder **Overzicht** de optie **Project maken**.
5. Selecteer onder **Project maken** uw Azure-abonnement en resourcegroep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Vakken voor projectnaam en regio](./media/tutorial-discover-import/new-project.png)

7. Selecteer **Maken**.
8. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd.

Het hulpprogramma **Azure Migrate: Serverevaluatie** wordt standaard toegevoegd aan het nieuwe project.

![Pagina waarop wordt weergegeven dat het hulpprogramma Serverevaluatie standaard wordt toegevoegd](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>De CSV voorbereiden

De CSV-sjabloon downloaden en er servergegevens aan toevoegen.

### <a name="download-the-template"></a>De sjabloon downloaden

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. Selecteer **Importeren met behulp van CSV-bestand** in **Computers detecteren**.
3. Selecteer **Download** om de CSV-sjabloon te downloaden. U kunt deze ook [direct downloaden](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Download de CSV-sjabloon](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Serverinformatie toevoegen

Verzamel servergegevens en voeg deze toe aan het CSV-bestand.

- Om gegevens te verzamelen, kunt u deze exporteren vanuit hulpprogramma's die u gebruikt voor het beheer van uw on-premises servers, zoals VMware vSphere of uw CMDB (configuratiebeheerdatabase).
- Als u voorbeeldgegevens wilt bekijken, downloadt u ons [voorbeeldbestand](https://go.microsoft.com/fwlink/?linkid=2108405).

De volgende tabel bevat een overzicht van de bestandsvelden die moeten worden ingevuld:

**Veldnaam** | **Verplicht** | **Details**
--- | --- | ---
**Servernaam** | Ja | U kunt het beste de Fully Qualified Domain Name (FQDN) opgeven.
**IP-adres** | Nee | Serveradres.
**Kernen** | Ja | Het aantal processorkernen dat aan de server is toegewezen.
**Geheugen** | Ja | Het totale RAM-geheugen (in MB) dat aan de server is toegewezen.
**Naam van besturingssysteem** | Ja | Besturingssysteem van de server. <br/> Namen van besturingssystemen die overeenkomen met de namen in [deze](#supported-operating-system-names) lijst worden herkend door de evaluatie.
**Versie van het besturingssysteem** | Nee | Versie van serverbesturingssysteem.
**Architectuur van besturingssysteem** | Nee | Architectuur van serverbesturingssysteem <br/> Geldige waarden zijn: x64, x86, amd64, 32-bits of 64-bits
**Aantal schijven** | Nee | Niet nodig als er details van de individuele schijf worden gegeven.
**Grootte van schijf 1**  | Nee | De maximale grootte van de schijf, in GB.<br/>U kunt gegevens voor meer schijven toevoegen door in de sjabloon [kolommen toe te voegen](#add-multiple-disks). U kunt maximaal acht schijven toevoegen.
**Schijf 1 bewerkingen per seconde lezen** | Nee | Schijf gelezen bewerkingen per seconde.
**Schijf 1 bewerkingen per seconde schrijven** | Nee | Schijf geschreven bewerkingen per seconde.
**Schijf 1 leesdoorvoer** | Nee | Gegevens die per seconde van de schijf worden gelezen, in MB per seconde.
**Schijf 1 schrijfdoorvoer** | Nee | Gegevens die per seconde naar de schijf worden geschreven, in MB per seconde.
**Gebruikspercentage van CPU** | Nee | Gebruikt percentage CPU-gebruik.
**Percentage voor geheugengebruik** | Nee | Gebruikt percentage RAM-gebruik.
**Totaal door schijf gelezen bewerkingen per seconde** | Nee | Door schijf gelezen bewerkingen per seconde.
**Totaal door schijf geschreven bewerkingen per seconde** | Nee | Door schijf geschreven bewerkingen per seconde.
**Totaal door schijf gelezen doorvoer** | Nee | Gegevens die van de schijf worden gelezen, in MB per seconde.
**Totaal door schijf geschreven doorvoer** | Nee | Gegevens die naar de schijf worden geschreven, in MB per seconde.
**Netwerk in doorvoer** | Nee | Gegevens ontvangen door de server, in MB per seconde.
**Netwerk uitgaande doorvoer** | Nee | Gegevens verzonden door de server, in MB per seconde.
**Type firmware** | Nee | Serverfirmware. Waarden kunnen "BIOS" of "UEFI" zijn.
**MAC-adres**| Nee | MAC-adres van server.


### <a name="add-operating-systems"></a>Besturingssystemen toevoegen

Evaluatie herkent specifieke namen van besturingssystemen. Een naam die u opgeeft, moet exact overeenkomen met een van de tekenreeksen in de [lijst met ondersteunde namen](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Meerdere schijven toevoegen

De sjabloon biedt standaardvelden voor de eerste schijf. U kunt vergelijkbare kolommen voor maximaal acht schijven toevoegen.

Als u bijvoorbeeld alle velden voor een tweede schijf wilt opgeven, voegt u deze kolommen toe:

- Schijf 2
- Schijf 2 bewerkingen per seconde lezen
- Schijf 2 bewerkingen per seconde schrijven
- Schijf 2 leesdoorvoer
- Schijf 2 schrijfdoorvoer


## <a name="import-the-server-information"></a>De servergegevens importeren

Nadat u informatie aan de CSV-sjabloon hebt toegevoegd, importeert u het CSV-bestand in de Serverevaluatie.

1. Ga in Azure Migrate naar **Computers detecteren** naar de voltooide sjabloon.
2. Selecteer **Importeren**.
3. De importstatus wordt weergegeven.
    - Als er waarschuwingen worden weergegeven in de status, kunt u deze herstellen of doorgaan zonder ze te adresseren.
    - Als u de nauwkeurigheid van de evaluatie wilt verbeteren, kunt u de servergegevens verbeteren, zoals wordt voorgesteld in waarschuwingen.
    - Als u waarschuwingen wilt weergeven en herstellen, selecteert u **Waarschuwingsdetails downloaden. CSV**. Met deze bewerking wordt het CSV-bestand gedownload met de opgenomen waarschuwingen. Bekijk de waarschuwingen en los zo nodig problemen op.
    - Als fouten worden weergegeven in de status zodat de status van de import **Mislukt**, moet u deze fouten oplossen voordat u door kunt gaan met het importeren:
        1. Download de CSV, die nu foutgegevens bevat.
        1. Controleer de fouten en los ze op. 
        1. Upload het gewijzigde bestand opnieuw.
4. Wanneer de status van de import **Voltooid** is, zijn de gegevens van de server geïmporteerd. Vernieuw als het importproces niet lijkt te zijn voltooid.

## <a name="update-server-information"></a>Serverinformatie updaten

U kunt de gegevens voor een server bijwerken door de gegevens voor de server opnieuw te importeren met dezelfde **Servernaam**. U kunt het veld **Servernaam** niet wijzigen. Servers verwijderen wordt momenteel niet ondersteund.

## <a name="verify-servers-in-the-portal"></a>Verifieer servers in de portal

U kunt controleren of de servers worden weergegeven in de Azure-portal na de detectie:

1. Open het Azure Migrate-dashboard.
2. Op de **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie**-pagina, selecteert u het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.
3. Selecteer het tabblad **Gebaseerd op importeren**.



## <a name="supported-operating-system-names"></a>Ondersteunde namen van besturingssysteem

Namen van besturingssystemen in het CSV-bestand moeten overeenkomen. Als dat niet het geval is, kunt u ze niet beoordelen. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Drempelwaarde voor Windows Server<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Migrate-project gemaakt 
> * Servers geëvalueerd aan de hand van een geïmporteerd CSV-bestand. Voer nu een evaluatie uit voor de [migratie van virtuele VMware-machines naar virtuele Azure-machines](./tutorial-assess-vmware-azure-vm.md).