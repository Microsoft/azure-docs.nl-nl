---
title: Toegangs beheer instellen voor uw Synapse-werk ruimte
description: In dit artikel leert u hoe u de toegang tot een Synapse-werk ruimte beheert met behulp van Azure-functies, Synapse-functies, SQL-machtigingen en git-machtigingen.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: c6ec84d41d113a38e78ab13404ef19faf625530b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102169"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Toegangs beheer instellen voor uw Synapse-werk ruimte 

In dit artikel leert u hoe u de toegang tot een Synapse-werk ruimte beheert met behulp van Azure-functies, Synapse-functies, SQL-machtigingen en git-machtigingen.   

In deze hand leiding stelt u een werk ruimte in en configureert u een eenvoudig toegangs beheersysteem dat geschikt is voor veel Synapse-projecten.  Vervolgens worden meer geavanceerde opties voor het besturings element nauw keuriger beschreven.  

Synapse Access Control kan worden vereenvoudigd door gebruik te maken van beveiligings groepen die zijn afgestemd op de rollen en personen in uw organisatie.  U hoeft alleen gebruikers uit de beveiligings groepen toe te voegen en te verwijderen om de toegang te beheren.

Voordat u met deze procedure begint, leest u het overzicht van het [toegangs beheer voor Synapse](./synapse-workspace-access-control-overview.md) om vertrouwd te raken met de mechanismen voor toegangs beheer die door Synapse worden gebruikt.   

## <a name="access-control-mechanisms"></a>Toegangs beheer mechanismen

> [!NOTE]
> De aanpak in deze hand leiding is het maken van verschillende beveiligings groepen en het toewijzen van rollen aan deze groepen. Nadat de groepen zijn ingesteld, hoeft u alleen lidmaatschap van de beveiligings groepen te beheren om de toegang tot de werk ruimte te controleren.

Als u een Synapse-werk ruimte wilt beveiligen, volgt u een patroon voor het configureren van de volgende items:

- **Beveiligings groepen** om gebruikers te groeperen met soort gelijke toegangs vereisten.
- **Azure-rollen**, om te bepalen wie SQL-groepen, Apache Spark Pools en integratie-Runtimes en toegang tot ADLS Gen2 opslag kunnen maken en beheren.
- **Synapse-rollen**, voor het beheren van de toegang tot gepubliceerde code artefacten, het gebruik van Apache Spark Compute-resources en Integration Runtimes 
- **SQL-machtigingen** voor het beheren van de toegang tot de beheer-en gegevensvlakten van SQL-groepen. 
- **Git-machtigingen**, om te bepalen wie toegang heeft tot code artefacten in broncode beheer als u Git-ondersteuning voor de werk ruimte configureert 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Stappen voor het beveiligen van een Synapse-werk ruimte

In dit document worden standaard namen gebruikt om de instructies te vereenvoudigen. Vervang ze door de namen van uw keuze.

|Instelling | Standaard naam | Description |
| :------ | :-------------- | :---------- |
| **Synapse-werkruimte** | `workspace1` |  De naam die de Synapse-werk ruimte heeft. |
| **ADLSGEN2-account** | `storage1` | Het ADLS-account dat moet worden gebruikt met uw werk ruimte. |
| **Container** | `container1` | De container in STG1 die standaard door de werk ruimte wordt gebruikt. |
| **Active Directory-Tenant** | `contoso` | de naam van de Active Directory-Tenant.|
||||

## <a name="step-1-set-up-security-groups"></a>STAP 1: beveiligings groepen instellen

>[!Note] 
>Tijdens de preview-periode is het aanbevolen om beveiligings groepen te maken die zijn toegewezen aan de Synapse **Synapse SQL-beheerder** en **Synapse Apache Spark beheerders** rollen.  Met de introductie van nieuwe Synapse RBAC-rollen en-bereiken, is het nu raadzaam deze nieuwe mogelijkheden te gebruiken om de toegang tot uw werk ruimte te beheren.  Deze nieuwe rollen en bereiken bieden meer configuratie flexibiliteit en herkennen dat ontwikkel aars vaak een combi natie van SQL en Spark gebruiken in het maken van Analytics-toepassingen en moeten mogelijk toegang krijgen tot specifieke resources in plaats van de hele werk ruimte. Meer [informatie](./synapse-workspace-synapse-rbac.md) over Synapse RBAC.

Maak de volgende beveiligings groepen voor uw werk ruimte:

- **`workspace1_SynapseAdministrators`**, voor gebruikers die volledige controle over de werk ruimte moeten hebben.  Voeg uzelf ten minste eerst toe aan deze beveiligings groep.
- **`workspace1_SynapseContributors`** voor ontwikkel aars die code moeten ontwikkelen, fout opsporing en publiceren naar de service.   
- **`workspace1_SynapseComputeOperators`**, voor gebruikers die Apache Spark Pools en integratie-Runtimes moeten beheren en bewaken.
- **`workspace1_SynapseCredentialUsers`** voor gebruikers die fout opsporing en het uitvoeren van Orchestration-pijp lijnen met behulp van de werk ruimte MSI (beheerde service-identiteit) referentie en het annuleren van pijplijn uitvoeringen.   

U wijst binnenkort Synapse-rollen toe aan deze groepen in het bereik van de werk ruimte.  

Deze beveiligings groep ook maken: 
- **`workspace1_SQLAdmins`**, groep voor gebruikers die SQL Active Directory-beheer instantie nodig hebben binnen SQL-groepen in de werk ruimte. 

De `workspace1_SQLAdmins` groep wordt gebruikt wanneer u SQL-machtigingen in SQL-groepen configureert terwijl u deze maakt. 

Deze vijf groepen zijn voldoende voor een eenvoudige installatie. Later kunt u beveiligings groepen toevoegen voor het afhandelen van gebruikers die meer gespecialiseerde toegang nodig hebben of om gebruikers alleen toegang te geven tot specifieke bronnen.

> [!NOTE]
>- Meer informatie over het maken van een beveiligingsgroep vindt u in [dit artikel](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).
>- Meer informatie over het toevoegen van een beveiligings groep vanuit een andere beveiligings groep vindt u in [dit artikel](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).

>[!Tip]
>Individuele Synapse gebruikers kunnen Azure Active Directory in de Azure Portal gebruiken om hun groepslid maatschappen te bekijken om te bepalen welke functies ze hebben verleend.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>STAP 2: uw ADLS Gen2 Storage-account voorbereiden

Een Synapse-werk ruimte gebruikt een standaard opslag container voor:
  - Opslaan van de back-upgegevens bestanden voor Spark-tabellen
  - Uitvoerings logboeken voor Spark-taken
  - Bibliotheken beheren die u wilt installeren

Bepaal de volgende informatie over uw opslag:

- Het ADLS Gen2-account dat moet worden gebruikt voor uw werk ruimte. Dit document wordt aangeroepen `storage1` . `storage1` wordt beschouwd als het ' primaire ' opslag account voor uw werk ruimte.
- De container in `workspace1` uw Synapse-werk ruimte wordt standaard gebruikt. Dit document wordt aangeroepen `container1` . 

- Wijs de volgende Azure-rollen toe `container1` aan de beveiligings groepen met behulp van de Azure portal: 

  - Wijs de rol **Inzender voor BLOB-gegevens opslag** toe aan `workspace1_SynapseAdmins` 
  - Wijs de rol **Inzender voor BLOB-gegevens opslag** toe aan `workspace1_SynapseContributors`
  - Wijs de rol **Inzender voor BLOB-gegevens opslag** toe aan `workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>STAP 3: uw Synapse-werk ruimte maken en configureren

Maak in het Azure Portal een Synapse-werk ruimte:

- Selecteer uw abonnement
- Selecteer of maak een resource groep waarvoor u de rol Azure- **eigenaar** hebt.
- De werk ruimte een naam Geef `workspace1`
- Kiezen `storage1` voor het opslag account
- Kies `container1` voor de container die wordt gebruikt als het "Bestands systeem".
- Open WS1 in Synapse Studio
- Ga naar **Manage**  >  **Access Control** en wijs de volgende Synapse-rollen toe aan de beveiligings groepen in het *werkruimte bereik* :
  - Wijs de beheerdersrol **Synapse** toe aan `workspace1_SynapseAdministrators` 
  - Wijs de rol **Inzender voor Synapse** toe aan `workspace1_SynapseContributors` 
  - Wijs de rol **Synapse Compute operator** toe aan `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>STAP 4: de werk ruimte-MSI toegang verlenen tot de standaard opslag container

Voor het uitvoeren van pijp lijnen en het uitvoeren van systeem taken vereist Synapse dat de beheerde service-id (MSI) van de werk ruimte toegang moet hebben tot `container1` in het standaard ADLS Gen2-account.

- Open de Azure Portal
- Zoek het opslag account, `storage1` en klik vervolgens `container1`
- Gebruik **Access Control (IAM)** om ervoor te zorgen dat de rol **BLOB gegevensinzender voor opslag** is toegewezen aan het MSI-bestand van de werk ruimte
  - Als deze niet is toegewezen, wijst u deze toe.
  - Het MSI-bestand heeft dezelfde naam als de werk ruimte. In dit artikel zou het zijn `workspace1` .

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>STAP 5: Ken Synapse Administrators de rol Azure contributor toe aan de werk ruimte 

Voor het maken van SQL-groepen, Apache Spark Pools en integratie-Runtimes moeten gebruikers ten minste toegang hebben tot de Azure-Inzender voor de werk ruimte. Met de rol Inzender kunnen deze gebruikers ook de resources beheren, met inbegrip van onderbreken en schalen.

- Open de Azure Portal
- Zoek de werk ruimte, `workspace1`
- Wijs de rol **Azure Inzender** toe aan `workspace1` aan `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>STAP 6: SQL Active Directory beheerdersrol toewijzen

De maker van het werk station wordt automatisch ingesteld als de SQL Active Directory-beheerder voor de werk ruimte.  Deze rol kan slechts aan één gebruiker of groep worden toegewezen. In deze stap wijst u de SQL Active Directory-beheerder in de werk ruimte toe aan de `workspace1_SQLAdmins` beveiligings groep.  Als u deze rol toewijst, krijgt deze groep uiterst bevoegde beheerders toegang tot alle SQL-groepen en data bases in de werk ruimte.   

- Open de Azure Portal
- Ga naar `workspace1`
- Selecteer onder **instellingen** de optie **SQL Active Directory-beheerder**
- Selecteer **beheerder instellen** en kies **`workspace1_SQLAdmins`**

>[!Note]
>Stap 6 is optioneel.  U kunt ervoor kiezen om de `workspace1_SQLAdmins` groep een minder privileged-rol te geven. Als `db_owner` u of andere SQL-rollen wilt toewijzen, moet u scripts uitvoeren op elke SQL database. 

## <a name="step-7-grant-access-to-sql-pools"></a>STAP 7: toegang verlenen aan SQL-groepen

Standaard wordt aan alle gebruikers die de Synapse-beheerdersrol toegewezen, ook de SQL-functie toegewezen aan `db_owner` de serverloze SQL-groep, ingebouwde en alle bijbehorende data bases.

Toegang tot SQL-groepen voor andere gebruikers en voor de werk ruimte MSI wordt beheerd met behulp van SQL-machtigingen.  Voor het toewijzen van SQL-machtigingen moet u SQL-scripts uitvoeren op elke SQL database na het maken.  Er zijn drie gevallen waarin u deze scripts moet uitvoeren:
1. Andere gebruikers toegang verlenen tot de serverloze SQL-groep, ingebouwde en data bases
2. Gebruikers toegang verlenen tot specifieke pool databases
3. Het verlenen van de werk ruimte-MSI toegang tot een SQL-groeps database om pijp lijnen in te scha kelen waarvoor toegang tot de SQL-groep is vereist.

Hieronder vindt u een voor beeld van SQL-scripts.

Om toegang te verlenen aan een toegewezen SQL-groeps database, kunnen de scripts worden uitgevoerd door de maker van de werk ruimte of een lid van de `workspace1_SQLAdmins` groep.  

Om toegang te verlenen tot de serverloze SQL-groep, ' ingebouwd ', kunnen de scripts worden uitgevoerd door elk lid van de `workspace1_SQLAdmins` groep of de  `workspace1_SynapseAdministrators` groep. 

> [!TIP]
> De onderstaande stappen moeten worden uitgevoerd voor **elke** SQL-groep om gebruikers toegang te verlenen tot alle SQL-data bases, met uitzonde ring van de sectie [werkruimte machtigingen](#workspace-scoped-permission) waar u een gebruiker kunt toewijzen aan een sysadmin-rol op het niveau van de werk ruimte.

### <a name="step-71-serverless-sql-pool-built-in"></a>STAP 7,1: SQL-groep zonder server, ingebouwd

In deze sectie vindt u voorbeeld scripts waarin wordt getoond hoe een gebruiker toegang krijgt tot een bepaalde data base of aan alle data bases in de serverloze SQL-groep, ' ingebouwd '.

> [!NOTE]
> Vervang in de script voorbeelden de *alias* door de alias van de gebruiker of groep waaraan toegang is verleend en *domein* met het bedrijfs domein dat u gebruikt.

#### <a name="database-scoped-permission"></a>Data base-scoped permission

Als u toegang wilt verlenen aan een gebruiker aan **één** serverloze SQL database, volgt u de stappen in dit voor beeld:

1. AANMELDING maken

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. GEBRUIKER maken

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. GEBRUIKER toevoegen aan leden van de opgegeven rol

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Machtigingen in de werk ruimte-bereik

Gebruik het script in dit voor beeld om volledige toegang te verlenen aan **alle** SERVERloze SQL-groepen in de werk ruimte:

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>STAP 7,2: toegewezen SQL-groepen

Als u toegang wilt verlenen aan **één** exclusieve SQL-groeps database, volgt u deze stappen in de Synapse SQL script-editor:

1. Maak de gebruiker in de-data base door de volgende opdracht uit te voeren op de doel database, die is geselecteerd met de vervolg keuzelijst *verbinding maken* :

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Zo kent u de gebruiker een rol toe om toegang te krijgen tot de database:

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* en *db_datawriter* kunnen worden gebruikt voor lees-en schrijf machtigingen als het verlenen van *db_owner* machtiging niet gewenst is.
> *Db_owner* machtiging is vereist voor een Spark-gebruiker om rechtstreeks vanuit Spark naar of vanuit een SQL-groep te lezen en te schrijven.

Nadat u de gebruikers hebt gemaakt, voert u query's uit om te controleren of de serverloze SQL-groep een query kan uitvoeren voor het opslag account.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>STAP 7,3: SQL-toegangs beheer voor Synapse-pijplijn uitvoeringen

### <a name="workspace-managed-identity"></a>Beheerde identiteit van werk ruimte

> [!IMPORTANT]
> Als u pijp lijnen met gegevens sets of activiteiten wilt uitvoeren die verwijzen naar een SQL-groep, moet de werk ruimte-id toegang krijgen tot de SQL-groep.

Voer de volgende opdrachten uit op elke SQL-groep zodat de door de werk ruimte beheerde systeem identiteit pijp lijnen kan uitvoeren op de data base (s) van de SQL-groep:  

>[!note]
>In de onderstaande scripts, voor een toegewezen SQL-groeps database, is DATABASENAME hetzelfde als de naam van de groep.  Voor een data base in de serverloze SQL-groep ' ingebouwd ' is DATABASENAME de naam van de data base.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

Deze machtiging kan worden verwijderd door in dezelfde SQL-pool het volgende script uit te voeren:

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>STAP 8: gebruikers toevoegen aan beveiligings groepen

De initiële configuratie voor uw toegangs beheer systeem is voltooid.

Als u de toegang wilt beheren, kunt u gebruikers toevoegen aan en verwijderen uit de beveiligings groepen die u hebt ingesteld.  Hoewel u gebruikers hand matig aan Synapse-rollen kunt toewijzen, worden de machtigingen niet consistent geconfigureerd. U kunt in plaats daarvan alleen gebruikers toevoegen aan of verwijderen uit de beveiligings groepen.

## <a name="step-9-network-security"></a>STAP 9: netwerk beveiliging

Als laatste stap om uw werk ruimte te beveiligen, moet u de netwerk toegang beveiligen met behulp van:
- [Werkruimte firewall](./synapse-workspace-ip-firewall.md)
- [Beheerd virtueel netwerk](./synapse-workspace-managed-vnet.md) 
- [Privé-eind punten](./synapse-workspace-managed-private-endpoints.md)
- [Private Link](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>STAP 10: voltooiing

Uw werk ruimte is nu volledig geconfigureerd en beveiligd.

## <a name="supporting-more-advanced-scenarios"></a>Ondersteuning voor geavanceerdere scenario's

Deze hand leiding is gericht op het instellen van een basis toegangscontrole systeem. U kunt meer geavanceerde scenario's ondersteunen door extra beveiligings groepen te maken en deze groepen uitgebreidere rollen toe te wijzen aan specifiekere bereiken. Houd rekening met de volgende gevallen:

**Git-ondersteuning inschakelen** voor de werk ruimte voor geavanceerdere ontwikkel scenario's, inclusief CI/cd.  In de Git-modus wordt met git-machtigingen bepaald of een gebruiker wijzigingen kan door voeren in hun werk vertakking.  Publiceren naar de service vindt alleen plaats vanuit de vertakking voor samen werking.  Overweeg het maken van een beveiligings groep voor ontwikkel aars die updates moeten ontwikkelen en fout opsporing in een werkende vertakking, maar geen wijzigingen hoeven te publiceren naar de Live-service.

Beperk de toegang tot specifieke bronnen voor **ontwikkel aars** .  Maak extra nauw keurige beveiligings groepen voor ontwikkel aars die alleen toegang tot specifieke bronnen nodig hebben.  Wijs deze groepen geschikte Synapse-rollen toe die zijn afgestemd op specifieke Spark-Pools, Integration Runtimes of referenties.

**Beperk de toegang tot code artefacten met Opera tors**.  Maak beveiligings groepen voor Opera tors die de operationele status van Synapse Compute-resources moeten bewaken en logboeken kunnen weer geven, maar geen toegang tot code nodig hebben of geen updates voor de service kunnen publiceren. Wijs deze groepen toe aan de rol reken operator binnen het bereik van specifieke Spark-Pools en integratie-Runtimes.  

## <a name="next-steps"></a>Volgende stappen

Meer informatie [over het beheren van Synapse RBAC-roltoewijzingen](./how-to-manage-synapse-rbac-role-assignments.md) een [Synapse-werk ruimte](../quickstart-create-workspace.md) maken