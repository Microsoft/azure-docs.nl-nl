---
title: Een listener voor beschikbaarheidsgroep voor SQL Server configureren op virtuele RHEL-machines in Azure - virtuele Linux-machines | Microsoft Docs
description: Meer informatie over het instellen van een listener voor beschikbaarheidsgroep in SQL Server op virtuele RHEL-machines in Azure
ms.service: virtual-machines-linux
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 83fde9e957cb2011ce585603e51d331be171bc08
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586208"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Zelfstudie: Een listener voor beschikbaarheidsgroep configureren voor SQL Server in virtuele RHEL-machines in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Deze zelfstudie bevindt zich in **openbare preview**. 
>
> In deze zelfstudie gebruiken we SQL Server 2017 met RHEL 7.6, maar u kunt ook SQL Server 2019 in RHEL 7 of RHEL 8 gebruiken om hoge beschikbaarheid te configureren. De opdrachten voor het configureren van resources van beschikbaarheidsgroep zijn gewijzigd in RHEL 8. In het artikel [Resource voor beschikbaarheidsgroep maken](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) en RHEL 8-resources vindt u meer informatie over de juiste opdrachten.

In deze zelfstudie worden de stappen uitgelegd voor het maken van een listener voor beschikbaarheidsgroep voor uw SQL-servers op virtuele RHEL-machines (VM's) in Azure. U leert het volgende:

> [!div class="checklist"]
> - Een taakverdeler (load balancer) maken in Azure Portal
> - De back-endgroep configureren voor de taakverdeler
> - Een test maken voor de taakverdeler
> - Taakverdelingsregels instellen
> - Resource voor de taakverdeler maken in het cluster
> - De listener voor beschikbaarheidsgroep maken
> - Verbinding met listener testen
> - Een failover testen

## <a name="prerequisite"></a>Vereiste

Afgeronde [Zelfstudie: Beschikbaarheidsgroepen configureren voor SQL Server op virtuele RHEL-machines in Azure](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Een taakverdeler maken in Azure-portal

De volgende instructies leiden u door stappen 1 tot en met 4 van de sectie [Een taakverdeler maken en configureren in Azure-portal](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) van het artikel [Taakverdeler - Azure-portal](../windows/availability-group-load-balancer-portal-configure.md).

### <a name="create-the-load-balancer"></a>Load balancer maken

1. Open in Azure-portal de resourcegroep die de virtuele SQL Server-machines omvat. 

2. Klik op **Toevoegen** in de resourcegroep.

3. Zoek naar **taakverdeler** en selecteer vervolgens in de zoekresultaten **Taakverdeler** die is gepubliceerd door **Microsoft**.

4. Klik op de blade **Taakverdeler** op de optie **Maken**.

5. Configureer de taakverdeler in het dialoogvenster **Taakverdeler maken** als volgt:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam voor de taakverdeler. Bijvoorbeeld: **sqlLB**. |
   | **Type** |**Intern** |
   | **Virtueel netwerk** |Het standaard-virtuele netwerk dat is gemaakt moet de naam **VM1VNET** hebben. |
   | **Subnet** |Selecteer het subnet waarin de SQL Server-exemplaren zich bevinden. De standaardwaarde is **VM1Subnet**.|
   | **IP-adrestoewijzing** |**Statisch** |
   | **Privé IP-adres** |Gebruik het `virtualip`-IP-adres dat in het cluster is gemaakt. |
   | **Abonnement** |Gebruik het abonnement dat voor uw resourcegroep is gebruikt. |
   | **Resourcegroep** |Selecteer de resourcegroep waarin de SQL Server-exemplaren zich bevinden. |
   | **Locatie** |Selecteer de Azure-locatie waar de SQL Server-exemplaren zich bevinden. |

### <a name="configure-the-back-end-pool"></a>De back-endgroep configureren
Azure roept de back-endadresgroep *back-endgroep* aan. In dit geval bestaat de back-endgroep uit de adressen van de drie SQL Server-exemplaren in uw beschikbaarheidsgroep. 

1. Klik in uw resourcegroep op de taakverdeler die u hebt gemaakt. 

2. Klik in **Instellingen** op **Back-endgroepen**.

3. Klik in **Back-endgroepen** op **Toevoegen** om een back-endadresgroep te maken. 

4. Typ in **Back-endgroep toevoegen**, onder **Naam**, een naam voor de back-endgroep.

5. Selecteer onder **Gekoppeld aan**, de optie **Virtuele machine**. 

6. Selecteer elke virtuele machine in de omgeving en koppel het juiste IP-adres aan elke selectie.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Back-endgroep toevoegen":::

7. Klik op **Add**. 

### <a name="create-a-probe"></a>Een test maken

De test definieert hoe Azure verifieert welke van de SQL Server-exemplaren momenteel eigenaar is van de listener voor beschikbaarheidsgroep. Azure test de service op basis van het IP-adres van een poort die u opgeeft wanneer u de test maakt.

1. Klik op de taakverdelerblade **Instellingen** op **Statuscontroles**. 

2. Klik op de blade **Statuscontroles** op **Toevoegen**.

3. Configureer de test op de blade **Test toevoegen**. Gebruik de volgende waarden om de test te configureren:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam voor de test. Bijvoorbeeld: **SQLAlwaysOnEndPointProbe**. |
   | **Protocol** |**TCP** |
   | **Poort** |U kunt elke beschikbare poort gebruiken. Bijvoorbeeld: *59999*. |
   | **Interval** |*5* |
   | **Drempelwaarde voor onjuiste status** |*2* |

4.  Klik op **OK**. 

5. Meld u aan bij al uw virtuele machines en open de testpoort met de volgende opdrachten:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

In Azure wordt de test gemaakt en vervolgens gebruikt om te testen welk SQL Server-exemplaar de listener voor beschikbaarheidsgroep heeft.

### <a name="set-the-load-balancing-rules"></a>Taakverdelingsregels instellen

De taakverdelingsregels bepalen hoe de taakverdeling verkeer routeert naar de SQL Server-exemplaren. Voor deze taakverdeler schakelt u Direct Server Return in, omdat maar een van de drie SQL Server-exemplaren tegelijkertijd eigenaar is van de listener voor beschikbaarheidsgroep.

1. Klik op de taakverdelerblade **Instellingen** op **Taakverdelingsregels**. 

2. Klik op de blade **Taakverdelingsregels** op **Toevoegen**.

3. Configureer de taakverdelingsregel op de blade **Taakverdelingsregels toevoegen**. Gebruik de volgende instellingen: 

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam voor de taakverdelersregels. Bijvoorbeeld: **SQLAlwaysOnEndPointListener**. |
   | **Protocol** |**TCP** |
   | **Poort** |*1433* |
   | **Poort back-end** |*1433*. Deze waarde wordt genegeerd, omdat deze regel **Zwevend IP (Direct Server Return)** gebruikt. |
   | **Test** |Gebruik de naam van de test die u voor deze taakverdeler hebt gemaakt. |
   | **Sessiepersistentie** |**Geen** |
   | **Time-out voor inactiviteit (minuten)** |*4* |
   | **Zwevend IP (Direct Server Return)** |**Ingeschakeld** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Taakverdelingsregels toevoegen":::

4. Klik op **OK**. 
5. In Azure wordt de taakverdelingsregel geconfigureerd. De taakverdeler is nu geconfigureerd om verkeer naar het SQL Server-exemplaar te routeren dat de listener voor beschikbaarheidsgroep host. 

Op dit moment heeft de resourcegroep een taakverdeler die verbinding maakt met alle SQL Server-machines. De taakverdeler bevat ook een IP-adres voor de listener voor SQL Server AlwaysOn-beschikbaarheidsgroepen, zodat elke machine op aanvragen voor beschikbaarheidsgroepen kan reageren.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Resource maken voor de taakverdeler in het cluster

1. Meld u aan bij de primaire virtuele machine. U moet de resource maken om de poort voor de Azure-taakverdelertest in te schakelen (in ons voorbeeld woord 59999 gebruikt). Voer de volgende opdracht uit:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Maak een groep die de resources `virtualip` en `azure_load_balancer` bevat:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Beperkingen toevoegen

1. U moet een co-locatiebeperking configureren om er zeker van te zijn dat het IP-adres van de Azure-taakverdeler en de resource van de beschikbaarheidsgroep op hetzelfde knooppunt worden uitgevoerd. Voer de volgende opdracht uit:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Maak een volgordebeperking om ervoor te zorgen dat de resource van de beschikbaarheidsgroep actief is en wordt uitgevoerd vóór het IP-adres van de Azure-taakverdeler. Hoewel de co-locatiebeperking een volgordebeperking impliceert, wordt deze hiermee afgedwongen.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Voer de volgende opdracht uit om de beperkingen te controleren:

    ```bash
    sudo pcs constraint list --full
    ```

    In dat geval moet de volgende uitvoer worden weergegeven:

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>De listener voor beschikbaarheidsgroep maken

1. Voer op het primaire knooppunt de volgende opdracht uit in SQLCMD of SSMS:

    - Vervang het onderstaande IP-adres met het `virtualip`-IP-adres.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Meld u aan bij elk VM-knooppunt. Gebruik de volgende opdracht om het hosts-bestand te openen en de omzetting van hostnamen voor de `ag1-listener` in te stellen op elke machine.

    ```
    sudo vi /etc/hosts
    ```

    Voer in de **vi**-editor `i` in om tekst in te voegen en typ op een lege regel het IP-adres van de `ag1-listener`. Voeg vervolgens een spatie en `ag1-listener` toe achter het IP-adres.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Als u de **vi**-editor wilt afsluiten, drukt u eerst op de **ESC**-toets en voert u vervolgens de opdracht `:wq` in om het bestand te schrijven en af te sluiten. Doe dit op elk knooppunt.

## <a name="test-the-listener-and-a-failover"></a>De listener en een failover testen

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Aanmelden bij SQL Server met de listener voor beschikbaarheidsgroep testen

1. Gebruik SQLCMD om u aan te melden bij het primaire knooppunt van SQL Server met de naam van de listener voor beschikbaarheidsgroep:

    - Gebruik een eerder gemaakte aanmelding en vervang `<YourPassword>` met het juiste wachtwoord. Onderstaande voorbeeld gebruikt de `sa`-aanmelding die is gemaakt met de SQL-server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Controleer de naam van de server waarmee u verbinding hebt. Voer de volgende opdracht uit in SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    Uw uitvoer moet het huidige primaire knooppunt weergeven. Als u nooit eerder een failover hebt getest, moet dit `VM1` zijn.

    Verlaat de SQL Server-sessie door de opdracht `exit` te typen.

### <a name="test-a-failover"></a>Een failover testen

1. Voer de volgende opdracht uit om handmatig een failover van de primaire replica uit te voeren naar `<VM2>` of een andere replica. Vervang `<VM2>` door de waarde van de servernaam.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Als u de beperkingen opnieuw controleert, ziet u dat er vanwege de handmatige failover een andere beperking is toegevoegd:

    ```bash
    sudo pcs constraint list --full
    ```

    U ziet dat er een beperking met ID `cli-prefer-ag_cluster-master` is toegevoegd.

1. Verwijder de beperking met ID `cli-prefer-ag_cluster-master` met behulp van de volgende opdracht:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Als u uw clusterresources controleert met de opdracht `sudo pcs resource`, ziet u dat het primaire exemplaar nu `<VM2>` is.

    > [!NOTE]
    > Dit artikel bevat verwijzingen naar de term slave, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Gebruik SQLCMD om u aan te melden bij uw primaire replica met de naam van de listener:

    - Gebruik een eerder gemaakte aanmelding en vervang `<YourPassword>` met het juiste wachtwoord. Onderstaande voorbeeld gebruikt de `sa`-aanmelding die is gemaakt met de SQL-server.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Controleer de server waarmee u verbinding hebt. Voer de volgende opdracht uit in SQLCMD:

    ```sql
    SELECT @@SERVERNAME
    ```

    U zou nu moeten zien dat u verbinding hebt met de VM waarnaar u de failover hebt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over taakverdelers in Azure:

> [!div class="nextstepaction"]
> [Een taakverdeler configureren voor een beschikbaarheidsgroep op SQL Server op Azure-VM's](../windows/availability-group-load-balancer-portal-configure.md)
