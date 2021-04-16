---
title: Implementeer Azure Monitor voor SAP-oplossingen met de Azure Portal
description: Implementeer Azure Monitor voor SAP-oplossingen met de Azure Portal
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 908de54ee66772f3eb648895529c843675c3bf15
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538635"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Een Azure Monitor for SAP Solutions implementeren met Azure Portal

Azure Monitor for SAP Solutions resources kunnen worden gemaakt via [de Azure Portal.](https://azure.microsoft.com/features/azure-portal) Deze methode biedt een gebruikersinterface op basis van een browser om Azure Monitor for SAP Solutions te implementeren en providers te configureren.

## <a name="sign-in-to-azure-portal"></a>Meld u aan bij Azure Portal

Meld u aan bij Azure Portal op https://portal.azure.com

## <a name="create-monitoring-resource"></a>Bewakingsresource maken

1. Selecteer **Azure Monitor for SAP Solutions** in **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Afbeelding van het selecteren van Azure Monitor aanbieding voor SAP-oplossingen in Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Geef op **het** tabblad Basisinformatie de vereiste waarden op. Indien van toepassing kunt u een bestaande Log Analytics-werkruimte gebruiken.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Weergave van de Azure Portal configuratieopties." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Wanneer u een virtueel netwerk selecteert, moet u ervoor zorgen dat de systemen die u wilt bewaken, bereikbaar zijn vanuit dat VNET. 

   > [!IMPORTANT]
   > Als **u Delen** selecteert voor het delen van gegevens met Microsoft, kunnen onze ondersteuningsteams aanvullende ondersteuning bieden.

## <a name="configure-providers"></a>Providers configureren

### <a name="sap-netweaver-provider"></a>SAP NetWeaver-provider

#### <a name="prerequisites-for-adding-netweaver-provider"></a>Vereisten voor het toevoegen van NetWeaver-provider

De SAP-startservice biedt een groot aantal services, waaronder het controleren van het SAP-systeem. We maken gebruik van de SAPControl, een SOAP-webserviceinterface waarmee deze mogelijkheden worden belicht. Deze SAPControl-webservice-interface maakt onderscheid tussen [beveiligde en onbeveiligde](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) webservicemethoden. Als u specifieke metrische gegevens wilt ophalen, moet u de beveiliging van bepaalde methoden opheffen. Als u de beveiliging van de vereiste methoden voor de huidige versie wilt opheffen, volgt u de onderstaande stappen voor elk SAP-systeem:

1. Een SAP GUI-verbinding met de SAP-server openen
2. Aanmelden met een beheerdersaccount
3. Transactie RZ10 uitvoeren
4. Selecteer het juiste profiel (STANDAARD. PFL)
5. Selecteer Uitgebreid onderhoud en klik op Wijzigen 
6. Wijzig de waarde van de betrokken parameter 'service/protectedwebmethods' in 'SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList' in de aanbevolen instelling en klik op Kopiëren
7. Terug en selecteer Profiel->Opslaan
8. Start het systeem opnieuw op om de parameter van kracht te laten worden

>[!Tip]
> Gebruik een Access Control List (ACL) om de toegang tot een serverpoort te filteren. Raadpleeg zijn [SAP-notitie](https://launchpad.support.sap.com/#/notes/1495075)

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>NetWeaver-provider installeren op de Azure Portal
1.  Zorg ervoor dat de vereiste stappen zijn voltooid en dat de server opnieuw is opgestart
2.  Selecteer op Azure Portal, onder AMS, de optie Provider toevoegen en kies SAP NetWeaver in de vervolgkeuze
3.  Voer de hostnaam van het SAP-systeem en subdomein in (indien van toepassing)
4.  Voer het exemplaarnummer in dat overeenkomt met de ingevoerde hostnaam 
5.  Voer de systeem-id (SID) in
6.  Wanneer u klaar bent, selecteert u Provider toevoegen
7.  Ga door met het toevoegen van extra providers als dat nodig is of selecteer Beoordelen en maken om de implementatie te voltooien

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

### <a name="sap-hana-provider"></a>SAP HANA provider 

1. Selecteer het **tabblad Provider** om de providers toe te voegen die u wilt configureren. U kunt meerdere providers achter elkaar toevoegen of ze toevoegen nadat u de bewakingsresource hebt geïmplementeerd. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Toont het tabblad Provider om extra providers toe te voegen aan uw Azure Monitor for SAP Solutions." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Selecteer **Provider toevoegen** en kies **SAP HANA** in de vervolgkeuzekeuze. 

   > [!IMPORTANT]
   > Zorg ervoor SAP HANA provider is geconfigureerd voor SAP HANA hoofd-knooppunt.

3. Voer het privé-IP-adres voor de HANA-server in.

4. Voer de naam in van de Database-tenant die u wilt gebruiken. U kunt echter elke tenant kiezen. U wordt aangeraden **SYSTEMDB te** gebruiken, omdat dit een bredere matrix van bewakingsgebieden mogelijk maakt. 

5. Voer het SQL-poortnummer in dat is gekoppeld aan uw HANA-database. Het poortnummer moet de notatie **[3]**  +  **[exemplaar#]**  +  **[13] hebben.** Bijvoorbeeld 30013. 

6. Voer de gebruikersnaam van de database in die u wilt gebruiken. Zorg ervoor dat aan de databasegebruiker **leesrollen** voor bewaking **en catalogus** zijn toegewezen. 

7. Wanneer u klaar bent, **selecteert u Provider toevoegen.** Ga door met het toevoegen van meer providers naar behoefte of selecteer **Beoordelen en maken om** de implementatie te voltooien.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Afbeelding van configuratieopties bij het toevoegen van providergegevens." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::
   
### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server provider

1. Voordat u de Microsoft SQL Server-provider toevoegt, moet u het volgende script uitvoeren in SQL Server Management Studio om een gebruiker te maken met de juiste machtigingen die nodig zijn om de provider te configureren.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Selecteer **Provider toevoegen** en kies **Microsoft SQL Server** in de vervolgkeuzekeuze. 

3. Vul de velden in met behulp van gegevens die zijn gekoppeld aan uw Microsoft SQL Server. 

4. Wanneer u klaar bent, **selecteert u Provider toevoegen.** Ga door met het toevoegen van meer providers als dat nodig is of selecteer **Beoordelen en maken om** de implementatie te voltooien.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Afbeelding met informatie met betrekking tot het toevoegen van Microsoft SQL Server provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Provider van cluster met hoge beschikbaarheid (Pacemaker)

1. Selecteer **High-availability cluster (Pacemaker)** in de vervolgkeuzekeuze. 

   > [!IMPORTANT]
   > Als u de Pacemaker-provider (High-Availability Cluster) wilt configureren, moet ha_cluster_provider in elk knooppunt zijn geïnstalleerd. Zie export van [ha-clusters](https://github.com/ClusterLabs/ha_cluster_exporter#installation) voor meer informatie

2. Voer het Prometheus-eindpunt in de vorm van http://IP:9664/metrics in. 
 
3. Voer de systeem-id (SID), hostnaam en clusternaam in.

4. Wanneer u klaar bent, **selecteert u Provider toevoegen.** Ga door met het toevoegen van meer providers als dat nodig is of selecteer **Beoordelen en maken om** de implementatie te voltooien.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Afbeelding met opties met betrekking tot de Pacemaker-provider voor ha-cluster." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::

### <a name="os-linux-provider"></a>Provider van besturingssysteem (Linux) 

1. Selecteer OS (Linux) in de vervolgkeuzekeuze 

   >[!IMPORTANT]
   > Als u de provider van het besturingssysteem (Linux) wilt configureren, moet u ervoor zorgen dat de meest recente versie van Node_Exporter is geïnstalleerd op elke host (BareMetal of VM) die u wilt bewaken. Gebruik deze [koppeling om](https://prometheus.io/download/#node_exporter) de meest recente versie te vinden. Zie voor meer informatie [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Voer een naam in. Dit is de id voor het BareMetal-exemplaar.
3. Voer het Knooppunt-export-eindpunt in de vorm van http://IP:9100/metrics in.

   >[!IMPORTANT]
   >Gebruik het privé-IP-adres van de Linux-host. Zorg ervoor dat host en AMS-resource zich in hetzelfde VNET. 

   >[!Note]
   > Firewallpoort 9100 moet worden geopend op de Linux-host.
   >Als u firewall-cmd gebruikt: firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload If using ufw: ufw allow 9100/tcp ufw reload

    >[!Tip]
    > Als een Linux-host een Azure-VM is, moet u ervoor zorgen dat alle toepasselijke NSG's inkomende verkeer via poort 9100 van VirtualNetwork als bron toestaan.
 
5. Wanneer u klaar bent, **selecteert u Provider toevoegen.** Ga door met het toevoegen van meer providers naar behoefte of selecteer **Beoordelen en maken om** de implementatie te   voltooien. 


## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Monitor for SAP Solutions](azure-monitor-overview.md)
