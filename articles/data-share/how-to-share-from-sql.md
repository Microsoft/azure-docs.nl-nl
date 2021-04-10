---
title: Gegevens delen en ontvangen van Azure SQL Database en Azure Synapse Analytics
description: Meer informatie over het delen en ontvangen van gegevens van Azure SQL Database en Azure Synapse Analytics
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 02/24/2021
ms.openlocfilehash: ef8c1a50cd3568c6cec9bdb053b02e6e14741eb0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644679"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Gegevens delen en ontvangen van Azure SQL Database en Azure Synapse Analytics

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure-gegevens share biedt ondersteuning voor het delen Azure SQL Database en Azure Synapse Analytics op basis van moment opnamen. In dit artikel wordt uitgelegd hoe u gegevens uit deze bronnen kunt delen en ontvangen.

De Azure-gegevens share ondersteunt het delen van tabellen en weer gaven van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL DW) en het delen van tabellen vanuit een Azure Synapse Analytics (werk ruimte) toegewezen SQL-groep. Delen vanuit een Azure Synapse Analytics (werk ruimte) SQL-groep zonder server wordt momenteel niet ondersteund. Gegevens gebruikers kunnen ervoor kiezen om de gegevens in Azure Data Lake Storage Gen2 of Azure Blob Storage te accepteren als CSV-of Parquet-bestand, evenals in Azure SQL Database en Azure Synapse Analytics als tabellen.

Wanneer gegevens worden geaccepteerd in Azure Data Lake Store Gen2 of Azure Blob Storage, overschrijven volledige moment opnamen de inhoud van het doel bestand als dit al bestaat.
Wanneer gegevens worden ontvangen in de SQL-tabel en als de doel tabel nog niet bestaat, maakt Azure-gegevens share de SQL-tabel met het bron schema. Als er al een doel tabel met dezelfde naam bestaat, wordt deze verwijderd en overschreven met de laatste volledige moment opname. Incrementele moment opnamen worden momenteel niet ondersteund.

## <a name="share-data"></a>Gegevens delen

### <a name="prerequisites-to-share-data"></a>Vereisten voor het delen van gegevens

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Het e-mailadres voor Azure van de ontvanger (de e-mailalias werkt niet).
* Als de bron-Azure-gegevensopslag zich in een ander Azure-abonnement bevindt dan de opslag die u gebruikt om een Data Share-resource te maken, registreert u de [resourceprovider Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) in het abonnement waarin de Azure-gegevensopslag zich bevindt. 

### <a name="prerequisites-for-sql-source"></a>Vereisten voor SQL-bron
Hieronder ziet u de lijst met vereisten voor het delen van gegevens vanuit een SQL-bron. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Vereisten voor het delen vanuit Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW)


Hier volgt een lijst met vereisten voor het delen van gegevens met behulp van Azure Active Directory-verificatie:

* Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW) met tabellen en weergaven die u wilt delen.
* Machtiging om naar de databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* SQL Server **Azure Active Directory-beheerder**
* Toegang tot SQL Server-firewall. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar SQL-server. Selecteer *Firewalls en virtuele netwerken* in de linkernavigatiebalk.
    1. Klik op **Ja** bij *Toestaan dat Azure-services en -resources toegang tot deze server krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 

Hieronder vindt u een lijst met vereisten voor het delen van gegevens met behulp van SQL-verificatie. U kunt de [demo met stapsgewijze instructies volgen](https://youtu.be/hIE-TjJD8Dc) voor het configureren van vereisten.

* Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW) met tabellen en weergaven die u wilt delen.
* Machtiging om naar de databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* Machtiging voor de beheerde identiteit van de Data Share-resource voor toegang tot de database. U kunt dit doen via de volgende stappen: 
    1. Navigeer in Azure Portal naar de SQL-server en stel uzelf in als de **Azure Active Directory-beheerder**.
    1. Maak verbinding met de Azure SQL-database of het Azure SQL-datawarehouse met behulp van [Queryeditor](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) of SQL Server Management Studio met Azure Active Directory-verificatie. 
    1. Voer het volgende script uit om de beheerde identiteit van de Data Share-resource toe te voegen als een db_datareader. U moet verbinding maken met behulp van Active Directory en niet via SQL Server-verificatie. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.  

* Een Azure SQL Database-gebruiker met **db_datareader**-toegang om door de tabellen en/of weergaven die u wilt delen, te navigeren en ze te selecteren. 

* Toegang tot SQL Server-firewall. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar SQL-server. Selecteer *Firewalls en virtuele netwerken* in de linkernavigatiebalk.
    1. Klik op **Ja** bij *Toestaan dat Azure-services en -resources toegang tot deze server krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Vereisten voor het delen vanuit de SQL-pool in Azure Synapse Analytics (werkruimte)

* Een toegewezen SQL-pool in Azure Synapse Analytics (werkruimte) met tabellen die u wilt delen. Het delen van weergaven wordt momenteel niet ondersteund. Delen vanuit een serverloze SQL-pool wordt momenteel niet ondersteund.
* Machtiging om te schrijven naar de SQL-pool in de Synapse-werkruimte, die zich in *Microsoft.Synapse/workspaces/sqlPools/write* bevindt. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* Machtiging voor de beheerde identiteit van de Data Share-resource voor toegang tot de SQL-pool in de Synapse-werkruimte. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar de Synapse-werkruimte. Selecteer SQL Active Directory-beheerder in de linkernavigatiebalk en stel uzelf in als de **Azure Active Directory-beheerder**.
    1. Open Synapse Studio, selecteer *Beheren* in de linkernavigatiebalk. Selecteer *Toegangsbeheer* onder Beveiliging. Wijs uzelf de rol van **SQL-beheerder** of **werkruimtebeheerder** toe.
    1. Selecteer in Synapse Studio de optie *Beheren* in de linkernavigatiebalk. Voer in de SQL-pool het volgende script uit om de beheerde identiteit van de Data Share-resource toe te voegen als een db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.  

* Toegang tot de firewall van de Synapse-werkruimte. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar de Synapse-werkruimte. Selecteer *Firewalls* in de linkernavigatiebalk.
    1. Klik op **AAN** bij *Toestaan dat Azure-services en -resources toegang tot deze werkruimte krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

### <a name="create-a-data-share-account"></a>Een Data Share-account maken

Maak een Azure Data Share-resource in een Azure-resourcegroep.

1. Selecteer de menuknop in de linkerbovenhoek van de portal, en selecteer vervolgens **Een resource maken** (+).

1. Zoek naar *Data Share*.

1. Selecteer Data Share en selecteer **Maken**.

1. Vul de basisgegevens van uw Azure Data Share-resource in met de volgende informatie. 

     **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    |---|---|---|
    | Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw gegevensshare-account.|
    | Resourcegroep | *test-resource-group* | Gebruik een bestaande resourcegroep of maak een nieuwe. |
    | Locatie | *US - oost 2* | Geef een regio op voor uw gegevensshare-account.
    | Naam | *datashareaccount* | Geef een naam op voor uw gegevensshare-account. |
    | | |

1. Selecteer **Beoordelen en maken** en vervolgens **Maken** om uw gegevensshare-account in te richten. Het inrichten van een nieuw gegevensshare-account duurt doorgaans 2 minuten of minder. 

1. Nadat de implementatie is voltooid, selecteert u **Ga naar resource**.

### <a name="create-a-share"></a>Een share maken

1. Ga naar de overzichtspagina van uw gegevensshare.

    ![Uw gegevens delen](./media/share-receive-data.png "Uw gegevens delen") 

1. Selecteer **Beginnen met het delen van uw gegevens**.

1. Selecteer **Maken**.   

1. Vul de details in voor uw share. Geef een naam, type share, beschrijving van de share-inhoud en gebruiksvoorwaarden (optioneel) op. 

    ![EnterShareDetails](./media/enter-share-details.png "Gegevens van share invoeren") 

1. Selecteer **Doorgaan**.

1. Selecteer **Gegevenssets toevoegen** om gegevenssets toe te voegen aan de share. 

    ![Gegevenssets toevoegen aan de share](./media/datasets.png "Gegevenssets")

1. Selecteer het type gegevensset dat u wilt toevoegen. Welke lijst met typen gegevensset wordt weergegeven, is afhankelijk van het type share (momentopname of in-place) dat u in de vorige stap hebt geselecteerd. 

    ![AddDatasets](./media/add-datasets.png "Gegevenssets toevoegen")    

1. Selecteer uw SQL Server-of Synapse-werk ruimte. Schakel het selectie vakje in als u AAD-verificatie gebruikt en het selectie vakje **gegevens delen toestaan voor het uitvoeren van de bovenstaande gebruiker SQL-script maken in mijn naam** wordt weer gegeven. Als u SQL-verificatie gebruikt, geeft u referenties op en volgt u de stappen in de vereisten voor het uitvoeren van het script worden weer gegeven in het scherm. Hiermee beschikt u over een machtiging voor het delen van gegevens vanuit uw SQL-data base. 

   Selecteer **volgende** om naar het object te navigeren dat u wilt delen en selecteer gegevens sets toevoegen. U kunt tabellen en weer gaven selecteren op basis van Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL DW) of tabellen uit een toegewezen SQL-groep in azure Synapse Analytics (workspace). 

    ![SelectDatasets](./media/select-datasets-sql.png "Gegevenssets selecteren")    

1. Voer op het tabblad Ontvangers de e-mailadressen in van uw gegevensgebruiker door + Ontvanger toevoegen te selecteren. Het e-mail adres moet de Azure-aanmeldings-e-mail van de ontvanger zijn.

    ![AddRecipients](./media/add-recipient.png "Ontvangers toevoegen") 

1. Selecteer **Doorgaan**.

1. Als u het type share momentopname hebt geselecteerd, kunt u het momentopnameschema configureren om updates van uw gegevens naar uw gegevensgebruiker te sturen. 

    ![EnableSnapshots](./media/enable-snapshots.png "Momentopnamen inschakelen") 

1. Selecteer een begintijd en een herhalingsinterval. 

1. Selecteer **Doorgaan**.

1. Controleer op het tabblad Beoordelen en maken de inhoud, instellingen, ontvangers en synchronisatie-instellingen van uw pakket. Selecteer **Maken**.

Uw Azure-gegevensshare is nu gemaakt en de ontvanger van uw gegevensshare kan nu uw uitnodiging accepteren. 

## <a name="receive-data"></a>Gegevens ontvangen

### <a name="prerequisites-to-receive-data"></a>Vereisten voor het ontvangen van gegevens
Voordat u een uitnodiging voor gegevensshares kunt accepteren, moet u een aantal Azure-resources inrichten. Deze worden hieronder weergegeven. 

Zorg ervoor dat alle vereisten zijn voltooid voordat u een uitnodiging voor gegevensshares accepteert. 

* Azure-abonnement: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een Data Share-uitnodiging: Een uitnodiging van Microsoft Azure met een onderwerp met de titel: Azure Data Share-uitnodiging van **<yourdataprovider@domain.com>** .
* Registreer de [Microsoft.DataShare-resourceprovider](concepts-roles-permissions.md#resource-provider-registration) in het Azure-abonnement waarin u een Data Share-resource gaat maken, en in het Azure-abonnement waarin uw Azure-doelgegevensarchieven zich bevinden.

### <a name="prerequisites-for-target-storage-account"></a>Vereisten voor het doel-opslag account
Als u ervoor kiest om gegevens te ontvangen in Azure Storage, hieronder volgt de lijst met vereisten.

* Een Azure Storage-account: Hier kunt u een [Azure Storage-account](../storage/common/storage-account-create.md) maken als u nog geen account hebt. 
* Machtiging om naar het opslagaccount te schrijven, aanwezig in *Microsoft.Storage/storageAccounts/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**. 
* Machtiging voor het toevoegen van de roltoewijzing van de gegevens share bron beheerde identiteit aan het opslag account dat aanwezig is in *micro soft. autorisatie/roltoewijzingen/schrijven*. Deze machtiging maakt onderdeel uit van de rol **Eigenaar**.  

### <a name="prerequisites-for-sql-target"></a>Vereisten voor SQL-doel
Als u ervoor kiest om gegevens te ontvangen in Azure SQL Database of Azure Synapse Analytics, vindt u hieronder de lijst met vereisten. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Vereisten voor het ontvangen van gegevens in Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW)

Als u gegevens wilt ontvangen op een SQL-server waarop u de **Azure Active Directory beheerder** van de SQL-server bent, is hier een lijst met vereisten:

* Een instantie van Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW).
* Machtiging om naar de databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* Toegang tot SQL Server-firewall. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar SQL-server. Selecteer *Firewalls en virtuele netwerken* in de linkernavigatiebalk.
    1. Klik op **Ja** bij *Toestaan dat Azure-services en -resources toegang tot deze server krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 
    
Hieronder vindt u een lijst met vereisten voor het ontvangen van gegevens in een SQL server waarvoor u niet de **Azure Active Directory-beheerder** bent. U kunt de [demo met stapsgewijze instructies volgen](https://youtu.be/aeGISgK1xro) voor het configureren van vereisten.

* Een instantie van Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW).
* Machtiging om naar databases op de SQL-server te schrijven, aanwezig in *Microsoft.Sql/servers/databases/write*. Deze machtiging maakt onderdeel uit van de rol **Inzender**. 
* Machtiging voor de beheerde identiteit van de Data Share-resource voor toegang tot Azure SQL Database of Azure Synapse Analytics. U kunt dit doen via de volgende stappen: 
    1. Navigeer in Azure Portal naar de SQL-server en stel uzelf in als de **Azure Active Directory-beheerder**.
    1. Maak verbinding met de Azure SQL-database of het Azure SQL-datawarehouse met behulp van [Queryeditor](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) of SQL Server Management Studio met Azure Active Directory-verificatie. 
    1. Voer het volgende script uit om de beheerde Data Share-identiteit toe te voegen als db_datareader, db_datawriter, db_ddladmin. U moet verbinding maken met behulp van Active Directory en niet via SQL Server-verificatie. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.         

* Toegang tot SQL Server-firewall. U kunt dit doen via de volgende stappen: 
    1. Ga in SQL Server in de Azure-portal naar *Firewalls en virtuele netwerken*
    1. Klik op **Ja** bij *Toestaan dat Azure-services en -resources toegang tot deze server krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Vereisten voor het ontvangen van gegevens in de SQL-pool in Azure Synapse Analytics (werkruimte)

* Een toegewezen SQL-pool in de Synapse Analytics-werkruimte. Het ontvangen van gegevens in een serverloze SQL-pool wordt momenteel niet ondersteund.
* Machtiging om te schrijven naar de SQL-pool in de Synapse-werkruimte, die zich in *Microsoft.Synapse/workspaces/sqlPools/write* bevindt. Deze machtiging maakt onderdeel uit van de rol **Inzender**.
* Machtiging voor de beheerde identiteit van de Data Share-resource voor toegang tot de SQL-pool in de Synapse-werkruimte. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar de Synapse-werkruimte. Selecteer SQL Active Directory-beheerder in de linkernavigatiebalk en stel uzelf in als de **Azure Active Directory-beheerder**.
    1. Open Synapse Studio, selecteer *Beheren* in de linkernavigatiebalk. Selecteer *Toegangsbeheer* onder Beveiliging. Wijs uzelf de rol van **SQL-beheerder** of **werkruimtebeheerder** toe.
    1. Selecteer in Synapse Studio de optie *Beheren* in de linkernavigatiebalk. Voer in de SQL-pool het volgende script uit om de beheerde identiteit van de Data Share-resource toe te voegen als een db_datareader, db_datawriter, db_ddladmin. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       U ziet dat *<share_acc_name>* de naam is van uw Data Share-resource. Als u nog geen Data Share-resource hebt gemaakt, kunt u later aan deze vereiste voldoen.  

* Toegang tot de firewall van de Synapse-werkruimte. U kunt dit doen via de volgende stappen: 
    1. Ga in Azure Portal naar de Synapse-werkruimte. Selecteer *Firewalls* in de linkernavigatiebalk.
    1. Klik op **AAN** bij *Toestaan dat Azure-services en -resources toegang tot deze werkruimte krijgen*.
    1. Klik op **+IP van client toevoegen**. Het IP-adres van de client kan worden gewijzigd. Dit proces moet mogelijk worden herhaald de volgende keer dat u SQL-gegevens deelt vanuit de Azure-portal. U kunt ook een IP-bereik toevoegen.
    1. Klik op **Opslaan**. 

### <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/).

### <a name="open-invitation"></a>Uitnodiging openen

1. U kunt de uitnodiging openen vanuit uw e-mail of rechtstreeks vanuit de Azure-portal. 

   Als u de uitnodiging wilt openen vanuit uw e-mail, gaat u in uw Postvak IN naar de uitnodiging van de gegevensprovider. De uitnodiging is van Microsoft Azure en heeft de titel **Azure Data Share-uitnodiging van <yourdataprovider@domain.com>** . Klik op **Uitnodiging weergeven** om de uitnodiging te bekijken in Azure. 

   Als u de uitnodiging rechtstreeks vanuit de Azure-portal wilt openen, gaat u in de Azure-portal naar **Data Share-uitnodigingen**. Hiermee kunt u de lijst met Data Share-uitnodigingen openen.

   ![Lijst met uitnodigingen](./media/invitations.png "Lijst met uitnodigingen") 

1. Selecteer de share die u wilt bekijken. 

### <a name="accept-invitation"></a>Uitnodiging accepteren
1. Zorg ervoor dat u alle velden controleert, met inbegrip van de **Gebruiksvoorwaarden**. Als u akkoord gaat met de gebruiksvoorwaarden, moet u het vakje inschakelen om aan te geven dat u akkoord gaat. 

   ![Gebruiksvoorwaarden](./media/terms-of-use.png "Gebruiksvoorwaarden") 

1. Selecteer onder *Data Share-doelaccount* het abonnement en de resourcegroep waarin u de Data Share-gegevens wilt implementeren. 

   Selecteer voor het veld **Data Share-account** de optie **Nieuwe maken** als u geen bestaand Data Share-account hebt. Anders selecteert u een bestaand Data Share-account waarin u de informatie uit uw gegevensshare wilt accepteren. 

   In het veld **Ontvangen sharenaam** kunt u de standaardwaarde laten staan die is opgegeven door de gegevensprovider. U kunt er ook voor kiezen een nieuwe naam op te geven voor de ontvangen share. 

   Zodra u akkoord bent gegaan met de gebruiksvoorwaarden en een Data Share-account hebt opgegeven voor het beheren van de ontvangen share, selecteert u **Accepteren en configureren**. Er wordt een shareabonnement gemaakt. 

   ![Opties voor accepteren](./media/accept-options.png "Opties voor accepteren") 

   Hiermee gaat u naar de ontvangen share in uw gegevens share-account. 

   Als u de uitnodiging niet wilt accepteren, selecteert u *Weigeren*. 

### <a name="configure-received-share"></a>Ontvangen share configureren
Volg de onderstaande stappen om te configureren waar u gegevens wilt ontvangen.

1. Selecteer het tabblad **Gegevenssets**. Schakel het selectievakje in van de gegevensset waaraan u een bestemming wilt toewijzen. Selecteer **+ Toewijzen aan doel** om een doelgegevensarchief te kiezen. 

   ![Toewijzen aan doel](./media/dataset-map-target.png "Toewijzen aan doel") 

1. Selecteer een doel gegevens archief waarvan u wilt dat de gegevens binnenkomen. Alle gegevensbestanden of tabellen in het doelgegevensarchief met hetzelfde pad en dezelfde naam worden overschreven. Schakel het selectie vakje in als u gegevens ontvangt in het SQL-doel en het selectie vakje **gegevens delen voor het maken van een gebruiker kan worden uitgevoerd** . Als dat niet het geval is, volgt u de instructie in vereisten voor het uitvoeren van het script op het scherm. Hiermee krijgt u schrijf machtiging voor de gegevens share bron voor uw doel-SQL-data base.

   ![Doelopslagaccount](./media/dataset-map-target-sql.png "Doel gegevens archief") 

1. Als u wilt delen met behulp van momentopnamen, en de gegevensprovider een schema heeft opgesteld voor het maken van momentopnamen om de gegevens regelmatig bij te werken, kunt u het schema voor het maken van momentopnamen ook inschakelen door het tabblad **Schema voor momentopnamen** te selecteren. Schakel het selectievakje naast het schema voor momentopnamen in en selecteer **+ Inschakelen**. Houd er rekening mee dat de eerste geplande moment opname begint binnen één minuut van de plannings tijd en volgende moment opnamen worden gestart binnen enkele seconden van de geplande tijd.

   ![Schema voor momentopnamen inschakelen](./media/enable-snapshot-schedule.png "Schema voor momentopnamen inschakelen")

### <a name="trigger-a-snapshot"></a>Een momentopname activeren
Deze stappen zijn alleen van toepassing bij delen op basis van momentopnamen.

1. U kunt een momentopname activeren door **Momentopname activeren** te selecteren op het tabblad **Details**. Hier kunt u een volledige of incrementele moment opname van uw gegevens activeren. Als dit de eerste keer is dat u gegevens van uw gegevensprovider ontvangt, selecteert u volledig kopiëren. Voor SQL-bronnen wordt alleen volledige moment opname ondersteund. Wanneer een moment opname wordt uitgevoerd, worden volgende moment opnamen niet gestart totdat de vorige is voltooid.

   ![Momentopname activeren](./media/trigger-snapshot.png "Momentopname activeren") 

1. Wanneer de status van de laatste uitvoering *Geslaagd* is, gaat u naar het doelgegevensarchief om de ontvangen gegevens te bekijken. Selecteer **Gegevenssets** en klik op de koppeling in het doelpad. 

   ![Gegevenssets van consumenten](./media/consumer-datasets.png "Toewijzing voor gegevenssets van consumenten") 

### <a name="view-history"></a>Geschiedenis weergeven
Deze stap geldt alleen voor delen op basis van momentopnamen. Selecteer het tabblad **Geschiedenis** om de geschiedenis van uw momentopnamen weer te geven. Hier vindt u een geschiedenis van alle momentopnamen die zijn gegenereerd voor de afgelopen dertig dagen. 

## <a name="supported-data-types"></a>Ondersteunde gegevenstypen
Wanneer u gegevens uit de SQL-bron deelt, wordt de volgende toewijzing gebruikt vanuit SQL Server gegevens typen naar tussenliggende gegevens typen van Azure data share tijdens het momentopname proces. 

| SQL Server gegevens type | Tussentijds gegevens type van Azure-gegevens share |
|:--- |:--- |
| bigint |Int64 |
| binair |Byte [] |
| bit |Booleaans |
| char |Teken reeks, char [] |
| date |DateTime |
| Datum/tijd |DateTime |
| datetime2 |DateTime |
| Date time offset |Date time offset |
| Decimaal |Decimaal |
| FILESTREAM-kenmerk (varbinary (max)) |Byte [] |
| Float |Dubbel |
| image |Byte [] |
| int |Int32 |
| money |Decimaal |
| nchar |Teken reeks, char [] |
| ntext |Teken reeks, char [] |
| numeriek |Decimaal |
| nvarchar |Teken reeks, char [] |
| werkelijk |Enkelvoudig |
| rowversion |Byte [] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimaal |
| sql_variant |Object |
| tekst |Teken reeks, char [] |
| tijd |TimeSpan |
| tijdstempel |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte [] |
| varchar |Teken reeks, char [] |
| xml |Tekenreeks |

>[!NOTE]
> 1. Voor gegevens typen die worden toegewezen aan het type van de decimale waarde, wordt de moment opname met de precisie Maxi maal 28 ondersteund. Als u gegevens hebt die een grotere nauw keurigheid dan 28 vereisen, kunt u overwegen om te converteren naar een teken reeks. 
> 1.  Als u gegevens deelt van Azure SQL database naar Azure Synapse Analytics, worden niet alle gegevens typen ondersteund. Raadpleeg de [tabel gegevens typen in een exclusieve SQL-groep](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-data-types.md) voor meer informatie. 

## <a name="sql-always-encrypted-or-dynamic-data-masking"></a>SQL Always Encrypted of dynamische gegevens maskering
Azure-gegevens share biedt momenteel geen ondersteuning voor Azure SQL-data bases met Always Encrypted geconfigureerd. 

Voor SQL-bron tabellen met dynamische gegevens maskering worden gegevens gemaskeerd weer gegeven aan de kant van de ontvanger.

## <a name="sql-snapshot-performance"></a>SQL-momentopname prestaties
De prestaties van SQL-moment opnamen worden beïnvloed door een aantal factoren. Het wordt altijd aanbevolen uw eigen prestatie tests uit te voeren. Hieronder ziet u enkele voor beelden van factoren die invloed hebben op de prestaties.

* Hardwareconfiguratie (bijvoorbeeld vCores, geheugen, DWU) van de bron-en doel-SQL-gegevens opslag. 
* Gelijktijdige toegang tot de bron-en doel gegevens archieven. Als u meerdere tabellen en weer gaven uit hetzelfde SQL-gegevens archief deelt of meerdere tabellen en weer gaven in hetzelfde SQL-gegevens archief ontvangt, worden de prestaties beïnvloed.   
* Locatie van bron-en doel gegevens archieven. 

## <a name="troubleshoot-sql-snapshot-failure"></a>Problemen met SQL-momentopname fout oplossen
De meest voorkomende oorzaak van het mislukken van de moment opname is dat de gegevens share geen machtiging heeft voor het bron-of doel gegevens archief. Als u de machtiging gegevens share wilt verlenen aan de bron-of doel Azure SQL Database of Azure Synapse Analytics (voorheen Azure SQL DW), moet u het meegeleverde SQL-script uitvoeren wanneer u verbinding maakt met de SQL database met behulp van Azure Active Directory-verificatie. Raadpleeg [problemen met de moment opname oplossen](data-share-troubleshoot.md#snapshots)voor het oplossen van een extra SQL-momentopname fout.

## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u gegevens kunt delen en ontvangen van SQL-bronnen met behulp van de Azure data share-service. Voor meer informatie over het delen van andere gegevens bronnen gaat u verder met [ondersteunde gegevens archieven](supported-data-stores.md).