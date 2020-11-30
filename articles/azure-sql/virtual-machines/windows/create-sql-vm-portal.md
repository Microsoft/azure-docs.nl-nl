---
title: Richt een virtuele Windows-machine in met de Azure Portal
description: Deze hand leiding bevat opties die beschikbaar zijn voor het inrichten van SQL Server op een virtuele Windows-machine met behulp van de Azure Portal.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: ca632a643215b689a9bbb825959e5c5c052d73cb
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326060"
---
# <a name="how-to-use-the-azure-portal-to-provision-a-windows-virtual-machine-with-sql-server"></a>De Azure Portal gebruiken om een virtuele Windows-machine in te richten met SQL Server

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Deze hand leiding bevat opties voor het inrichten van SQL Server op een virtuele Windows-machine (VM) die beschikbaar is voor het gebruik van de Azure Portal. In dit artikel worden meer configuratie opties behandeld dan de [SQL Server-VM Quick](sql-vm-create-portal-quickstart.md)start, waarmee u meer dieper kunt richten op één configuratie. 

Gebruik deze hand leiding om uw eigen SQL Server-VM te maken. Of gebruik deze als referentie voor de beschik bare opties in de Azure Portal.

> [!TIP]
> Als u vragen hebt over virtuele machines met SQL Server, raadpleegt u [Veelgestelde vragen](frequently-asked-questions-faq.md).

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="sql-server-virtual-machine-gallery-images"></a><a id="select"></a> Installatie kopieën van de galerie met virtuele machines SQL Server

Wanneer u een SQL Server virtuele machine maakt, kunt u een van de verschillende vooraf geconfigureerde installatie kopieën selecteren in de galerie met virtuele machines. De volgende stappen laten zien hoe u een van de SQL Server 2017-installatie kopieën kunt selecteren.

1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de Azure Portal. Als **Azure SQL** niet in de lijst staat, selecteert u **Alle services** en typt u *Azure SQL* in het zoekvak. 

   U kunt ook het sterretje naast **Azure SQL** selecteren om het als favoriet op te slaan en dit als een item toe te voegen aan de linkernavigatiebalk. 

1. Selecteer **+ Toevoegen** om de pagina **SQL-implementatieoptie selecteren** te openen. U kunt aanvullende informatie weer geven door **Details weer geven** te selecteren. 
1. Typ *2017* in het zoekvak voor de SQL Server-installatie kopie op de tegel **virtuele SQL-machines** en selecteer vervolgens **gratis SQL Server licentie: SQL Server 2017-ontwikkelaar op Windows Server 2016** in de vervolg keuzelijst. 

   ![SQL VM-installatie kopie selecteren](./media/create-sql-vm-portal/select-sql-vm-image-portal.png)

   > [!TIP]
   > De Developer-editie wordt in dit artikel gebruikt omdat het een volledig functionele, gratis versie van SQL Server voor ontwikkel tests is. U betaalt alleen voor de kosten van het uitvoeren van de virtuele machine. U kunt echter kiezen welke installatie kopieën u in deze walkthrough wilt gebruiken. Zie [SQL Server Windows virtual machines Overview](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)(Engelstalig) voor een beschrijving van de beschik bare installatie kopieën.

   > [!TIP]
   > De licentie kosten voor SQL Server zijn opgenomen in de prijzen per seconde van de virtuele machine die u maakt, en is afhankelijk van editie en kernen. SQL Server Developer Edition is echter gratis voor ontwikkeling en testen, niet voor productie. Daarnaast is SQL Express gratis voor lichte werk belastingen (minder dan 1 GB geheugen, minder dan 10 GB opslag ruimte). U kunt ook uw eigen licentie (BYOL) meenemen en alleen betalen voor de virtuele machine. De namen van de installatiekopieën worden voorafgegaan door {BYOL}. 
   >
   > Zie [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Prijsrichtlijnen voor SQL Server Azure VM's) voor meer informatie over deze opties.


1. Selecteer **Maken**.


## <a name="1-configure-basic-settings"></a>1. basis instellingen configureren

Geef op het tabblad **Basisbeginselen** u de volgende gegevens op:

* Controleer onder **Project Details** of het juiste abonnement is geselecteerd. 
* In de sectie **resource groep** selecteert u een bestaande resource groep in de lijst of kiest u **nieuwe maken** om een nieuwe resource groep te maken. Een resourcegroep is een verzameling verwante resources in Azure (virtuele machines, opslagaccounts, virtuele netwerken enz.). 

  ![Abonnement](./media/create-sql-vm-portal/basics-project-details.png)

  > [!NOTE]
  > Het is een goed idee om een nieuwe resourcegroep te maken als u het gebruik van SQL Server in Azure alleen wilt testen of hier meer over te weten wilt komen. Als u klaar bent met testen, verwijdert u gewoon de resourcegroep. De virtuele machine en alle resources die aan de resourcegroep zijn gekoppeld, worden dan automatisch verwijderd. Zie [Azure Resource Manager Overview](../../../active-directory-b2c/overview.md)voor meer informatie over resource groepen.


* Geef onder **Exemplaardetails** het volgende op:

    1. Voer een unieke **naam voor de virtuele machine** in.  
    1. Kies een locatie voor uw **Regio**. 
    1. Voor het doel van deze hand leiding, behoud de **beschikbaarheids opties** ingesteld op _geen infra structuur-redundantie vereist_. Zie [Beschikbaarheid](../../../virtual-machines/availability.md) voor meer informatie over beschikbaarheidsopties. 
    1. Selecteer in de lijst **Installatiekopie** de optie _Gratis SQL Server-licentie: SQL Server 2017 Developer op Windows Server 2016_.  
    1. Kies **Grootte wijzigen** voor de **Grootte** van de virtuele machine en selecteer de aanbieding **A2 Basic**. Zorg dat u uw resources opschoont wanneer u ze niet meer nodig hebt, om te voorkomen dat er onverwachte kosten in rekening worden gebracht. Zie [Performance best practices for SQL Server in Azure Virtual Machines (Best practices voor optimale prestaties van SQL Server in Azure Virtual Machines)](performance-guidelines-best-practices.md) voor de aanbevolen machinegrootten en configuratie voor productieworkloads.

    ![Exemplaardetails](./media/create-sql-vm-portal/basics-instance-details.png)

> [!IMPORTANT]
> De geschatte maandelijkse kosten die worden weergegeven in het venster **Grootte kiezen**, zijn niet inclusief de kosten voor SQL Server-licentieverlening. Deze schatting is alleen de kosten van de virtuele machine. Voor de Express-en Developer-edities van SQL Server is deze schatting de totale geschatte kosten. Zie de [pagina met prijzen voor virtuele Windows-machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) en selecteer de gewenste editie van SQL server. Zie ook de [richt lijnen voor prijzen voor SQL Server Azure-vm's](pricing-guidance.md) en- [grootten voor virtuele machines](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Geef een gebruikers naam en wacht woord onder **Administrator-account** op. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

   ![Administrator-account](./media/create-sql-vm-portal/basics-administrator-account.png)

* Kies onder **Regels voor binnenkomende poort** de optie **​​Geselecteerde poorten toestaan** en selecteer **RDP (3389)** in de vervolgkeuzelijst. 

   ![Regels voor binnenkomende poort](./media/create-sql-vm-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. optionele functies configureren

### <a name="disks"></a>Disks

Configureer uw schijf opties op het tabblad **schijven** . 

* Selecteer onder **type besturingssysteem schijf** het type schijf dat u wilt voor het besturings systeem in de vervolg keuzelijst. Premium wordt aanbevolen voor productie systemen, maar is niet beschikbaar voor een basis-VM. Als u een Premium-SSD wilt gebruiken, wijzigt u de grootte van de virtuele machine. 
* Onder **Geavanceerd** selecteert u **ja** onder gebruik **Managed disks**.

   > [!NOTE]
   > Microsoft raadt Managed Disks aan voor SQL Server. Managed Disks verwerken de opslag achter de schermen. Bovendien distribueert Azure de opslagresources zodat voldoende redundantie wordt geboden wanneer virtuele machines met Managed Disks zich in dezelfde beschikbaarheidsset bevinden. Zie [overzicht van Azure Managed disks](../../../virtual-machines/managed-disks-overview.md)voor meer informatie. Zie [Managed Disks gebruiken schijven voor virtuele machines in een beschikbaarheidsset](../../../virtual-machines/manage-availability.md) voor meer informatie over Managed Disks in een beschikbaarheidsset.

![SQL-VM-schijf instellingen](./media/create-sql-vm-portal/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>Networking

Configureer uw netwerk opties op het tabblad **netwerk** . 

* Maak een nieuw **virtueel netwerk** of gebruik een bestaand virtueel netwerk voor uw SQL Server-VM. Wijs ook een **subnet** toe. 

* Onder **NIC-netwerk beveiligings groep** selecteert u een basis beveiligings groep of de geavanceerde beveiligings groep. Als u de optie basis selecteert, kunt u binnenkomende poorten voor de SQL Server VM selecteren, die dezelfde waarden hebben die zijn geconfigureerd op het tabblad **basis** . Als u de optie Geavanceerd selecteert, kunt u een bestaande netwerk beveiligings groep kiezen of een nieuwe maken. 

* U kunt andere wijzigingen aanbrengen in de netwerk instellingen of de standaard waarden blijven gebruiken.

![SQL VM-netwerk instellingen](./media/create-sql-vm-portal/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>Bewaking

Configureer op het tabblad **controle** de optie bewaking en automatisch afsluiten. 

* Azure schakelt **Diagnostische gegevens over opstarten** standaard in met hetzelfde opslag account dat is opgegeven voor de virtuele machine. Op dit tabblad kunt u deze instellingen wijzigen en **Diagnostische gegevens van het besturings systeem** inschakelen. 
* U kunt ook door het **systeem toegewezen beheerde identiteit** en **automatisch afsluiten** inschakelen op dit tabblad. 

![Instellingen voor SQL-VM-beheer](./media/create-sql-vm-portal/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. Configureer de SQL Server instellingen

Configureer op het tabblad **SQL Server instellingen** specifieke instellingen en optimalisaties voor SQL Server. U kunt de volgende instellingen configureren voor SQL Server:

- [Connectiviteit](#connectivity)
- [Verificatie](#authentication)
- [Integratie van Azure Key Vault](#azure-key-vault-integration)
- [Opslagconfiguratie](#storage-configuration)
- [Automatische patching](#automated-patching)
- [Automatische back-up](#automated-backup)
- [Machine Learning Services](#machine-learning-services)


### <a name="connectivity"></a>Connectiviteit

Geef onder **SQL-connectiviteit** het gewenste type toegang tot het SQL Server-exemplaar op deze virtuele machine op. Voor de doel einden van deze walkthrough selecteert u **openbaar (Internet)** om verbindingen toe te staan met SQL Server van computers of services op internet. Als deze optie is geselecteerd, configureert Azure automatisch de firewall en de netwerk beveiligings groep om verkeer op de geselecteerde poort toe te staan.

> [!TIP]
> Standaard gebruikt SQL Server een bekende poort, namelijk **1433**. Wijzig de poort in het vorige dialoogvenster om een andere poort, zoals 1401, te gebruiken voor betere beveiliging. Als u de poort wijzigt, moet u via die poort verbinding maken via elke client hulpprogramma's, zoals SQL Server Management Studio (SSMS).

![SQL VM-beveiliging](./media/create-sql-vm-portal/azure-sqlvm-security.png)

Voor verbinding met SQL Server via internet moet u ook SQL Server-verificatie inschakelen. Dit wordt beschreven in het volgende gedeelte.

Als u de mogelijkheid om via internet verbinding te maken met de database-engine liever niet inschakelt, kiest u een van de volgende opties:

* **Lokaal (alleen binnen VM)** om alleen verbindingen met SQL Server vanuit de virtuele machine toe te staan.
* **Privé (binnen virtueel netwerk)** om verbindingen met SQL Server toe te staan vanaf machines of services in hetzelfde virtuele netwerk.

In het algemeen kunt u de beveiliging verbeteren door te kiezen voor de meest beperkende connectiviteit die voor uw scenario mogelijk is. Maar alle opties kunnen worden beveiligd met NSG-regels (netwerk beveiligings groep) en SQL/Windows-verificatie. U kunt de NSG bewerken nadat de virtuele machine is gemaakt. Zie [Veiligheidsoverwegingen voor SQL Server in virtuele Azure-machines](security-considerations-best-practices.md) voor meer informatie.

### <a name="authentication"></a>Verificatie

Als u SQL Server-verificatie nodig hebt, selecteert u **inschakelen** onder **SQL-verificatie** op het tabblad **SQL Server instellingen** .

![SQL Server-verificatie](./media/create-sql-vm-portal/azure-sqlvm-authentication.png)

> [!NOTE]
> Als u van plan bent SQL Server via internet (de open bare connectiviteits optie) te gebruiken, moet u hier SQL-verificatie inschakelen. Voor open bare toegang tot de SQL Server is SQL-verificatie vereist.

Als u SQL Server-verificatie inschakelt, geeft u een **Aanmeldingsnaam** en een **Wachtwoord** op. Deze aanmeldings naam is geconfigureerd als een SQL Server authenticatie aanmelding en een lid van de vaste serverrol **sysadmin** . Zie [Een verificatiemodus kiezen](/sql/relational-databases/security/choose-an-authentication-mode) voor meer informatie over de verificatiemodi.

Als u geen SQL Server-verificatie wilt inschakelen, kunt u het lokale Administrator-account op de virtuele machine gebruiken om verbinding te maken met het SQL Server exemplaar.

### <a name="azure-key-vault-integration"></a>Integratie van Azure Sleutelkluis

Als u beveiligings geheimen wilt opslaan in azure voor versleuteling, selecteert u **SQL Server instellingen** en schuift u omlaag naar  **Azure Key kluis-integratie**. Selecteer **inschakelen** en vul de aangevraagde informatie in. 

![Integratie van Azure Sleutelkluis](./media/create-sql-vm-portal/azure-sqlvm-akv.png)

De volgende tabel bevat de para meters die vereist zijn voor het configureren van de integratie van Azure Key Vault (Azure).

| PARAMETER | BESCHRIJVING | VOORBEELD |
| --- | --- | --- |
| **Sleutelkluis-URL** |De locatie van de sleutelkluis. |`https://contosokeyvault.vault.azure.net/` |
| **Principal-naam** |Principal-naam voor de Azure Active Directory-service. Deze naam wordt ook wel aangeduid als de Client-ID. |`fde2b411-33d5-4e11-af04eb07b669ccf2` |
| **Principal-geheim** |Principal-geheim voor de Azure Active Directory-service. Dit geheim wordt ook wel aangeduid als het Clientgeheim. |`9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=` |
| **Referentienaam** |**Referentie naam**: Azure-integratie maakt een referentie binnen SQL Server en staat de VM toe om toegang te krijgen tot de sleutel kluis. Kies een naam voor deze referentie. |`mycred1` |

Zie voor meer informatie [Integratie van Azure Sleutelkluis configureren voor SQL Server op Azure-VM's](azure-key-vault-integration-configure.md).

### <a name="storage-configuration"></a>Opslagconfiguratie

Selecteer op het tabblad **SQL Server instellingen** onder **opslag configuratie** de optie **configuratie wijzigen** om de configuratie pagina prestaties geoptimaliseerd voor opslag te openen en de opslag vereisten op te geven.

![Scherm opname van de locatie waar u de opslag configuratie kunt wijzigen.](./media/create-sql-vm-portal/sql-vm-storage-configuration-provisioning.png)

Selecteer onder **Opslag geoptimaliseerd voor** een van de volgende opties:

* **Algemeen** is de standaardinstelling en ondersteunt de meeste workloads.
* **Transactionele verwerking** optimaliseert de opslag voor traditionele OLTP-workloads van data bases.
* **Gegevensopslag** optimaliseert de opslag voor analyse- en rapportageworkloads.

![Configuratie van SQL-VM-opslag](./media/create-sql-vm-portal/sql-vm-storage-configuration.png)

U kunt ervoor kiezen de waarden standaard te laten staan of u kunt de opslag topologie hand matig aanpassen aan uw IOPS-behoeften. Zie [opslag configuratie](storage-configuration.md)voor meer informatie. 

### <a name="sql-server-license"></a>SQL Server licentie

Als u een Software Assurance-klant bent, kunt u de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) gebruiken om uw eigen SQL Server licentie te nemen en op te slaan op resources. 

![SQL VM-licentie](./media/create-sql-vm-portal/azure-sqlvm-license.png)

### <a name="automated-patching"></a>Automatisch patchen

**Automatisch patchen** is standaard ingeschakeld. Met automatisch patchen kan Azure automatisch een patch uitvoeren voor SQL Server en het besturingssysteem. Geef een dag van de week, een tijd en een periode op voor een onderhoudssessie. Azure voert de patch uit tijdens deze onderhoudssessie. De planning van het onderhouds venster maakt gebruik van de VM-land instelling. Als u niet wilt dat Azure automatisch patches SQL Server en het besturings systeem, selecteert u **uitschakelen**.  

![Automatische patching van SQL-VM](./media/create-sql-vm-portal/azure-sqlvm-automated-patching.png)

Zie voor meer informatie [Automatisch patchen voor SQL Server in Azure Virtual Machines](automated-patching.md).

### <a name="automated-backup"></a>Automatische back-up

Schakel automatische databaseback-ups in voor alle databases onder **Automatische back-up**. Automatische back-up is standaard uitgeschakeld.

Als u automatische back-up van SQL inschakelt, kunt u de volgende instellingen configureren:

* De retentieperiode (dagen) voor back-ups
* Het opslagaccount dat voor back-ups moet worden gebruikt
* Versleutelingsoptie en wachtwoord voor back-ups
* Back-up maken van systeemdatabases
* Back-upschema configureren

Selecteer **inschakelen** om de back-up te versleutelen. Geef het **Wachtwoord** op. Azure maakt een certificaat voor het versleutelen van de back-ups en gebruikt het opgegeven wachtwoord om dit certificaat te beschermen. Standaard wordt het schema automatisch ingesteld, maar u kunt een hand matige planning maken door **hand matig** te selecteren. 

![Automatische back-ups van SQL-VM](./media/create-sql-vm-portal/automated-backup.png)

Zie voor meer informatie [Automatische back-up voor SQL Server in Azure Virtual Machines](automated-backup-sql-2014.md).


### <a name="machine-learning-services"></a>Machine Learning Services

U hebt de mogelijkheid om [Machine Learning Services](/sql/advanced-analytics/)in te scha kelen. Met deze optie kunt u machine learning gebruiken met python en R in SQL Server 2017. Selecteer **inschakelen** in het venster **SQL Server instellingen** .


## <a name="4-review--create"></a>4. controleren en maken

Op het tabblad **controleren en maken** :
1. Bekijk de samenvatting.
1. Selecteer **maken** om de SQL Server, de resource groep en de resources te maken die zijn opgegeven voor deze virtuele machine.

U kunt de implementatie bewaken vanuit Azure Portal. Met de knop **Meldingen** boven aan het scherm kunt u de algemene status van de implementatie weergeven.

> [!NOTE]
> Een voor beeld van een periode voor Azure om een SQL Server virtuele machine te implementeren: een test SQL Server VM die is ingericht voor de regio VS Oost met de standaard instellingen duurt ongeveer 12 minuten. U kunt snellere of langzamere implementatie tijden ondervinden op basis van uw regio en geselecteerde instellingen.

## <a name="open-the-vm-with-remote-desktop"></a><a id="remotedesktop"></a> Open de virtuele machine met Extern bureaublad

Gebruik de volgende stappen om verbinding te maken met de SQL Server virtuele machine met Remote Desktop Protocol (RDP):

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Nadat u verbinding hebt gemaakt met de virtuele SQL Server-machine, kunt u SQL Server Management Studio starten en verbinding maken met Windows-verificatie met behulp van de lokale beheerdersreferenties. Als u SQL Server-verificatie inschakelt, kunt u ook verbinding maken met SQL-verificatie. Dit kan met behulp van de SQL-aanmeldingsnaam en het wachtwoord die u hebt geconfigureerd tijdens het inrichten.

Met toegang tot de machine kunt u rechtstreeks de instellingen voor de machine en de SQL Server wijzigen op basis van uw vereisten. U kunt bijvoorbeeld de firewallinstellingen configureren of de SQL Server-configuratie-instellingen wijzigen.

## <a name="connect-to-sql-server-remotely"></a><a id="connect"></a> Extern verbinding maken met SQL Server

In dit scenario hebt u **open bare** toegang geselecteerd voor de virtuele machine en **SQL Server-verificatie**. Door deze instellingen is de virtuele machine automatisch geconfigureerd voor het toestaan van SQL Server-verbindingen vanaf elke client via internet (ervan uitgaande dat deze beschikken over de juiste SQL-aanmeldgegevens).

> [!NOTE]
> Als u tijdens het inrichten niet de optie Openbaar hebt geselecteerd, kunt u de instellingen voor SQL-verbindingen achteraf wijzigen via de portal. Zie [Change your SQL connectivity settings](ways-to-connect-to-sql.md#change) (SQL-verbindingsinstellingen wijzigen) voor meer informatie.

In de volgende secties ziet u hoe u via Internet verbinding maakt met uw SQL Server VM-exemplaar.

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > In dit voor beeld wordt gebruikgemaakt van de algemene poort 1433. Deze waarde moet echter worden gewijzigd als er een andere poort (zoals 1401) is opgegeven tijdens de implementatie van de SQL Server VM. 


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van SQL Server in Azure [SQL Server in Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md) en de [Veelgestelde vragen](frequently-asked-questions-faq.md).