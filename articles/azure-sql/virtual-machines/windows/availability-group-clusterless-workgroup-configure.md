---
title: Een domein onafhankelijke groep beschikbaarheids groep configureren
description: Meer informatie over het configureren van een Active Directory domein onafhankelijke werk groep altijd on-beschikbaarheids groep op een SQL Server virtuele machine in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 0f194101720481f71434709c467d0e3130a0f1f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359452"
---
# <a name="configure-a-workgroup-availability-group"></a>Een beschikbaarheids groep voor werk groepen configureren 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel worden de stappen beschreven die nodig zijn om een Active Directory domein onafhankelijk cluster te maken met een always on-beschikbaarheids groep. Dit wordt ook wel een werkgroeps cluster genoemd. Dit artikel is gericht op de stappen die relevant zijn voor het voorbereiden en configureren van de werk groep en de beschikbaarheids groep, en glanzende stappen die in andere artikelen worden behandeld, zoals het maken van het cluster of het implementeren van de beschikbaarheids groep. 


## <a name="prerequisites"></a>Vereisten

Als u een beschikbaarheids groep voor werk groepen wilt configureren, hebt u het volgende nodig:
- Ten minste twee virtuele machines met Windows Server 2016 (of hoger) met SQL Server 2016 (of hoger), die zijn geïmplementeerd in dezelfde beschikbaarheidsset, of verschillende beschikbaarheids zones, met behulp van statische IP-adressen. 
- Een lokaal netwerk met mini maal 4 vrije IP-adressen in het subnet. 
- Een account op elke computer in de groep Administrators die ook sysadmin-rechten heeft in SQL Server. 
- Open poorten: TCP 1433, TCP 5022, TCP 59999. 

Ter referentie worden de volgende para meters in dit artikel gebruikt, maar kunnen ze zo nodig worden gewijzigd: 

| **Naam** | **Parameter** |
| :------ | :---------------------------------- |
| **Knooppunt1**   | AGNode1 (10.0.0.4) |
| **Knooppunt2**   | AGNode2 (10.0.0.5) |
| **Cluster naam** | AGWGAG (10.0.0.6 ALS) |
| **Listener** | AGListener (10.0.0.7) | 
| **DNS-achtervoegsel** | ag.wgcluster.example.com | 
| **Naam van werk groep** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>Een DNS-achtervoegsel instellen 

In deze stap configureert u het DNS-achtervoegsel voor beide servers. Bijvoorbeeld `ag.wgcluster.example.com`. Zo kunt u de naam van het object gebruiken waarmee u verbinding wilt maken als een volledig gekwalificeerd adres binnen uw netwerk, zoals `AGNode1.ag.wgcluster.example.com` . 

Voer de volgende stappen uit om het DNS-achtervoegsel te configureren:

1. RDP in op het eerste knoop punt en Open Serverbeheer. 
1. Selecteer **lokale server** en selecteer vervolgens de naam van uw virtuele machine onder **computer naam**. 
1. Selecteer **wijzigen...** onder **om de naam van deze computer te wijzigen...**. 
1. Wijzig de naam van de werkgroepnaam zodat deze een zinvol is, zoals `AGWORKGROUP` : 

   ![Naam van werk groep wijzigen](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Selecteer **meer...** om het dialoog venster **DNS-achtervoegsel en NetBIOS-computer naam** te openen. 
1. Typ de naam van het DNS-achtervoegsel onder **primair DNS-achtervoegsel van deze computer**, zoals `ag.wgcluster.example.com` en selecteer **OK**: 

   ![Scherm afbeelding toont het dialoog venster D N S en NetBIOS-computer naam waarin u de waarde kunt invoeren.](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Controleer of de **volledige computer naam** nu het DNS-achtervoegsel wordt weer gegeven en selecteer **OK** om uw wijzigingen op te slaan: 

   ![Scherm afbeelding laat zien waar de volledige computer naam wordt weer gegeven.](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Start de server opnieuw op wanneer u hierom wordt gevraagd. 
1. Herhaal deze stappen op alle andere knoop punten die moeten worden gebruikt voor de beschikbaarheids groep. 

## <a name="edit-a-host-file"></a>Een hostbestand bewerken

Omdat er geen Active Directory is, is er geen manier om Windows-verbindingen te verifiëren. Wijs als zodanig vertrouwen toe door het hostbestand te bewerken met een tekst editor. 

Voer de volgende stappen uit om het hostbestand te bewerken:

1. RDP in op de virtuele machine. 
1. Ga naar in **Verkenner** `c:\windows\system32\drivers\etc` . 
1. Klik met de rechter muisknop op het bestand **hosts** en open het bestand met **Klad blok** (of een andere tekst editor).
1. Voeg aan het einde van het bestand een vermelding toe voor elk knoop punt, de beschikbaarheids groep en de listener in de vorm van `IP Address, DNS Suffix #comment` als: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Vermeldingen voor het IP-adres, het cluster en de listener toevoegen aan het hostbestand](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Machtigingen instellen

Omdat er geen Active Directory zijn om machtigingen te beheren, moet u een niet-ingebouwd lokale beheerders account hand matig toestaan om het cluster te maken. 

U doet dit door de volgende Power shell-cmdlet uit te voeren in een Power shell-sessie op elk knoop punt: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Het failovercluster maken

In deze stap maakt u het failovercluster. Als u niet bekend bent met deze stappen, kunt u ze volgen vanuit de [zelf studie](failover-cluster-instance-storage-spaces-direct-manually-configure.md)over het failovercluster.

Er zijn belang rijke verschillen tussen de zelf studie en wat er moet gebeuren voor een werkgroepen cluster:
- Schakel de **opslag ruimte** uit en **opslagruimten direct** bij het uitvoeren van de cluster validatie. 
- Wanneer u de knoop punten toevoegt aan het cluster, moet u de volledig gekwalificeerde naam toevoegen, zoals:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Schakel **de optie alle in aanmerking komende opslag toevoegen aan het cluster** uit. 

Nadat het cluster is gemaakt, wijst u een statisch cluster-IP-adres toe. Voer hiervoor de volgende stappen uit:

1. Open **Failoverclusterbeheer** op een van de knoop punten, selecteer het cluster, klik met de rechter muisknop op de **naam: \<ClusterNam>** onder **cluster kern resources** en selecteer vervolgens **Eigenschappen**. 

   ![Eigenschappen voor de cluster naam starten](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Selecteer het IP-adres onder **IP-adressen** en selecteer **bewerken**. 
1. Selecteer **statische gebruiken**, geef het IP-adres van het cluster op en selecteer vervolgens **OK**: 

   ![Geef een statisch IP-adres voor het cluster op](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Controleer of de instellingen juist zijn en selecteer **OK** om ze op te slaan:

   ![Cluster eigenschappen verifiëren](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Een cloudwitness maken 

In deze stap configureert u een Cloud share-Witness. Als u niet bekend bent met de stappen, raadpleegt u [een Cloudwitness implementeren voor een failovercluster](/windows-server/failover-clustering/deploy-cloud-witness). 

## <a name="enable-the-availability-group-feature"></a>De functie beschikbaarheids groep inschakelen 

In deze stap schakelt u de functie beschikbaarheids groep in. Als u niet bekend bent met de stappen, raadpleegt u de [zelf studie over de beschikbaarheids groep](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Sleutels en certificaten maken

In deze stap maakt u certificaten die een SQL-aanmelding op het versleutelde eind punt gebruikt. Maak op elk knoop punt een map om de certificaat back-ups op te slaan, zoals `c:\certs` . 

Voer de volgende stappen uit om het eerste knoop punt te configureren: 

1. Open **SQL Server Management Studio** en maak verbinding met uw eerste knoop punt, bijvoorbeeld `AGNode1` . 
1. Open een **Nieuw query** venster en voer de volgende Transact-SQL-instructie (T-SQL) uit na het bijwerken naar een complex en veilig wacht woord:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Maak vervolgens het HADR-eind punt en gebruik het certificaat voor verificatie door de volgende Transact-SQL-instructie (T-SQL) uit te voeren:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Ga in **Verkenner** naar de bestands locatie waar uw certificaat zich bevindt, zoals `c:\certs` . 
1. Maak hand matig een kopie van het certificaat, bijvoorbeeld `AGNode1Cert.crt` van het eerste knoop punt, en zet het over naar dezelfde locatie op het tweede knoop punt. 

Voer de volgende stappen uit om het tweede knoop punt te configureren: 

1. Maak verbinding met het tweede knoop punt met **SQL Server Management Studio**, zoals `AGNode2` . 
1. Voer in een **Nieuw query** venster de volgende Transact-SQL-instructie (T-SQL) uit na het bijwerken naar een complex en veilig wacht woord: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Maak vervolgens het HADR-eind punt en gebruik het certificaat voor verificatie door de volgende Transact-SQL-instructie (T-SQL) uit te voeren:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Ga in **Verkenner** naar de bestands locatie waar uw certificaat zich bevindt, zoals `c:\certs` . 
1. Maak hand matig een kopie van het certificaat, bijvoorbeeld `AGNode2Cert.crt` van het tweede knoop punt, en zet het over naar dezelfde locatie op het eerste knoop punt. 

Als er andere knoop punten in het cluster zijn, herhaalt u deze stappen ook om de betreffende certificaat namen te wijzigen. 

## <a name="create-logins"></a>Aanmeldingen maken

Certificaat verificatie wordt gebruikt voor het synchroniseren van gegevens tussen knoop punten. Als u dit wilt toestaan, maakt u een aanmelding voor het andere knoop punt, maakt u een gebruiker voor de aanmelding, maakt u een certificaat voor de aanmelding om het back-upcertificaat te gebruiken en verleent u vervolgens Connect op het mirroring-eind punt. 

Hiervoor moet u eerst de volgende Transact-SQL (T-SQL)-query uitvoeren op het eerste knoop punt, zoals `AGNode1` : 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Voer vervolgens de volgende Transact-SQL-query (T-SQL) uit op het tweede knoop punt, bijvoorbeeld `AGNode2` : 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Als er andere knoop punten in het cluster zijn, herhaalt u deze stappen ook om de betreffende certificaten en gebruikers namen te wijzigen. 

## <a name="configure-an-availability-group"></a>Een beschikbaarheidsgroep configureren

In deze stap configureert u uw beschikbaarheids groep en voegt u de data bases hieraan toe. Maak op dit moment geen listener. Als u niet bekend bent met de stappen, raadpleegt u de [zelf studie over de beschikbaarheids groep](availability-group-manually-configure-tutorial.md#create-the-availability-group). Zorg ervoor dat u een failover en failback initieert om te controleren of alles werkt zoals het zou moeten zijn. 

   > [!NOTE]
   > Als er een fout optreedt tijdens het synchronisatie proces, moet u mogelijk `NT AUTHORITY\SYSTEM` sysadmin-rechten verlenen voor het maken van cluster bronnen op het eerste knoop punt, zoals `AGNode1` tijdelijk. 

## <a name="configure-a-load-balancer"></a>Een load balancer configureren

In deze laatste stap configureert u de load balancer met behulp van de [Azure Portal](availability-group-load-balancer-portal-configure.md) of [Power shell](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Volgende stappen

U kunt ook [AZ SQL VM cli](./availability-group-az-commandline-configure.md) gebruiken om een beschikbaarheids groep te configureren.