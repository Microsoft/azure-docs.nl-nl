---
title: Listeners voor de beschikbaarheids groep configureren & load balancer (Azure Portal)
description: Stapsgewijze instructies voor het maken van een listener voor een always on-beschikbaarheids groep voor SQL Server op virtuele machines in azure
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e53a6a4875b3dde55d1822daa342d6cde536d1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100096426"
---
# <a name="configure-a-load-balancer-for-a-sql-server-always-on-availability-group-in-azure-virtual-machines"></a>Een load balancer configureren voor een SQL Server AlwaysOn-beschikbaarheids groep in azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


In dit artikel wordt uitgelegd hoe u een load balancer maakt voor een SQL Server AlwaysOn-beschikbaarheids groep in azure Virtual Machines met Azure Resource Manager. Een beschikbaarheids groep vereist een load balancer wanneer de SQL Server exemplaren zich op Azure Virtual Machines bevinden. Met de load balancer wordt het IP-adres voor de beschikbaarheids groep-listener opgeslagen. Als een beschikbaarheids groep meerdere regio's omvat, moet elke regio een load balancer hebben.

Als u deze taak wilt volt ooien, moet u beschikken over een SQL Server AlwaysOn-beschikbaarheids groep die is geïmplementeerd in azure-Vm's die worden uitgevoerd met Resource Manager. Beide SQL Server virtuele machines moeten deel uitmaken van dezelfde beschikbaarheidsset. U kunt de [micro soft-sjabloon](./availability-group-quickstart-template-configure.md) gebruiken om automatisch de beschikbaarheids groep te maken in Resource Manager. Met deze sjabloon wordt automatisch een intern load balancer voor u gemaakt. 

Als u wilt, kunt u [een beschikbaarheids groep hand matig configureren](availability-group-manually-configure-tutorial.md).

Voor dit artikel moet uw beschikbaarheids groepen al zijn geconfigureerd.  

Gerelateerde artikelen weer geven:

* [AlwaysOn-beschikbaarheids groepen configureren in azure VM (GUI)](availability-group-manually-configure-tutorial.md)   
* [Een VNet-naar-VNet-verbinding configureren via Azure Resource Manager en PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Door dit artikel te door lopen, maakt en configureert u een load balancer in het Azure Portal. Nadat het proces is voltooid, configureert u het cluster voor het gebruik van het IP-adres uit de load balancer voor de beschikbaarheids groep-listener.

## <a name="create--configure-load-balancer"></a>Load balancer maken & configureren 

In dit gedeelte van de taak voert u de volgende stappen uit:

1. Maak in de Azure Portal de load balancer en configureer het IP-adres.
2. Configureer de back-end-pool.
3. Maak de test. 
4. Stel de regels voor taak verdeling in.

> [!NOTE]
> Als de SQL Server exemplaren zich in meerdere resource groepen en regio's bevinden, voert u elke stap twee keer uit en eenmaal in elke resource groep.
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Stap 1: de load balancer maken en het IP-adres configureren

Maak eerst de load balancer. 

1. Open in Azure-portal de resourcegroep die de virtuele SQL Server-machines omvat. 

2. Selecteer in de resource groep de optie **toevoegen**.

3. Zoeken naar **Load Balancer**. Kies **Load Balancer** (gepubliceerd door **micro soft**) in de zoek resultaten.

4. Selecteer op  de blade Load Balancer **maken**.

5. Configureer de taakverdeler in het dialoogvenster **Taakverdeler maken** als volgt:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam voor de taakverdeler. Bijvoorbeeld: **sqlLB**. |
   | **Type** |**Intern**: de meeste implementaties gebruiken een interne Load Balancer, waarmee toepassingen binnen hetzelfde virtuele netwerk verbinding kunnen maken met de beschikbaarheids groep.  </br> **Extern**: Hiermee kunnen toepassingen verbinding maken met de beschikbaarheids groep via een open bare Internet verbinding. |
   | **SKU** |**Basis**: standaard optie. Alleen geldig als SQL Server exemplaren zich in dezelfde beschikbaarheidsset bevinden. </br> **Standaard**: voor keur. Geldig als SQL Server exemplaren zich in dezelfde beschikbaarheidsset bevinden. Vereist als uw SQL Server exemplaren zich in verschillende beschikbaarheids zones bevinden. |
   | **Virtueel netwerk** |Selecteer het virtuele netwerk waarin de SQL Server exemplaren zich bevinden. |
   | **Subnet** |Selecteer het subnet waarin de SQL Server-exemplaren zich bevinden. |
   | **IP-adrestoewijzing** |**Statisch** |
   | **Privé IP-adres** |Geef een beschikbaar IP-adres op in het subnet. Gebruik dit IP-adres wanneer u een listener op het cluster maakt. In een Power shell-script, verderop in dit artikel, gebruikt u dit adres voor de `$ListenerILBIP` variabele. |
   | **Abonnement** |Als u meerdere abonnementen hebt, kan dit veld worden weer gegeven. Selecteer het abonnement dat u aan deze resource wilt koppelen. Dit is normaal gesp roken hetzelfde abonnement als alle resources voor de beschikbaarheids groep. |
   | **Resourcegroep** |Selecteer de resourcegroep waarin de SQL Server-exemplaren zich bevinden. |
   | **Locatie** |Selecteer de Azure-locatie waar de SQL Server-exemplaren zich bevinden. |

6. Selecteer **Maken**. 

Azure maakt de load balancer. De load balancer hoort bij een specifiek netwerk, subnet, resource groep en locatie. Nadat de taak door Azure is voltooid, controleert u de load balancer-instellingen in Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Stap 2: de back-end-pool configureren

Azure roept de back-endadresgroep *back-endgroep* aan. In dit geval is de back-end-pool de adressen van de twee SQL Server exemplaren in uw beschikbaarheids groep. 

1. Selecteer de load balancer die u hebt gemaakt in de resource groep. 

2. Selecteer bij **instellingen** de optie **back-end-Pools**.

3. Selecteer **toevoegen** op back-end- **Pools** om een back-end-adres groep te maken. 

4. Typ in **Back-endgroep toevoegen**, onder **Naam**, een naam voor de back-endgroep.

5. Selecteer onder **virtuele machines** **een virtuele machine toevoegen**. 

6. Selecteer onder **virtuele machines kiezen** de optie **een beschikbaarheidsset kiezen** en geef vervolgens de beschikbaarheidsset op waarvan de SQL Server virtuele machines deel uitmaken.

7. Nadat u de beschikbaarheidsset hebt gekozen, selecteert u **de virtuele machines kiezen**, selecteert u de twee virtuele machines die als host fungeren voor de SQL Server exemplaren in de beschikbaarheids groep en kiest u **selecteren**. 

8. Selecteer **OK** om de Blades te sluiten voor het **kiezen van virtuele machines** en **Voeg de back-endadresgroep toe**. 

De instellingen voor de back-end-adres groep worden door Azure bijgewerkt. Uw beschikbaarheidsset heeft nu een groep van twee SQL Server exemplaren.

### <a name="step-3-create-a-probe"></a>Stap 3: een test maken

De test definieert hoe Azure verifieert welke van de SQL Server-exemplaren momenteel eigenaar is van de listener voor beschikbaarheidsgroep. Azure test de service op basis van het IP-adres van een poort die u opgeeft wanneer u de test maakt.

1. Selecteer op de Blade load balancer **instellingen** de optie **status controles**. 

2. Selecteer **toevoegen** op de Blade **status controles** .

3. Configureer de test op de blade **Test toevoegen**. Gebruik de volgende waarden om de test te configureren:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Een tekstnaam voor de test. Bijvoorbeeld: **SQLAlwaysOnEndPointProbe**. |
   | **Protocol** |**TCP** |
   | **Poort** |U kunt elke beschikbare poort gebruiken. Bijvoorbeeld: *59999*. |
   | **Interval** |*5* |
   | **Drempelwaarde voor onjuiste status** |*2* |

4.  Selecteer **OK**. 

> [!NOTE]
> Zorg ervoor dat de poort die u opgeeft, is geopend op de firewall van beide exemplaren van SQL Server. Voor beide exemplaren is een binnenkomende regel vereist voor de TCP-poort die u gebruikt. Zie [Add or Edit Firewall Rule](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)) (Firewallregel toevoegen of bewerken) voor meer informatie. 
> 

In Azure wordt de test gemaakt en vervolgens gebruikt om te testen welk SQL Server-exemplaar de listener voor beschikbaarheidsgroep heeft.

### <a name="step-4-set-the-load-balancing-rules"></a>Stap 4: de regels voor taak verdeling instellen

De taakverdelingsregels bepalen hoe de taakverdeling verkeer routeert naar de SQL Server-exemplaren. Voor deze load balancer schakelt u Direct Server Return in, omdat slechts een van de twee SQL Server exemplaren de listener-resource van de beschikbaarheids groep tegelijk heeft.

1. Selecteer op de Blade load balancer **instellingen** de optie **taakverdelings regels**. 

2. Selecteer **toevoegen** op de Blade **regel voor taak verdeling** .

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

   > [!NOTE]
   > Mogelijk moet u omlaag schuiven op de Blade om alle instellingen weer te geven.
   > 

4. Selecteer **OK**. 

5. In Azure wordt de taakverdelingsregel geconfigureerd. De taakverdeler is nu geconfigureerd om verkeer naar het SQL Server-exemplaar te routeren dat de listener voor beschikbaarheidsgroep host. 

Op dit moment heeft de resource groep een load balancer die verbinding maakt met beide SQL Server machines. De load balancer bevat ook een IP-adres voor de SQL Server always on-beschikbaarheids groep, zodat de computer kan reageren op aanvragen voor de beschikbaarheids groepen.

> [!NOTE]
> Als uw SQL Server exemplaren zich in twee verschillende regio's bevinden, herhaalt u de stappen in de andere regio. Voor elke regio is een load balancer vereist. 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Het cluster configureren voor het gebruik van het load balancer IP-adres

De volgende stap is het configureren van de listener op het cluster en het online brengen van de listener. Voer de volgende stappen uit: 

1. Maak de beschikbaarheids groep-listener op het failovercluster. 

2. De listener online zetten.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Stap 5: de beschikbaarheids groep-listener maken op het failovercluster

In deze stap maakt u hand matig de beschikbaarheids groep-listener in Failoverclusterbeheer en SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>De configuratie van de listener controleren

Als de cluster bronnen en afhankelijkheden correct zijn geconfigureerd, kunt u de listener in SQL Server Management Studio weer geven. Voer de volgende stappen uit om de listener-poort in te stellen:

1. Start SQL Server Management Studio en maak vervolgens verbinding met de primaire replica.

2. Ga naar AlwaysOn-beschikbaarheids groepen voor de beschikbaarheids groep met **hoge Beschik baarheid**  >    >  .  

    U ziet nu de naam van de listener die u hebt gemaakt in Failoverclusterbeheer. 

3. Klik met de rechter muisknop op de naam van de listener en selecteer vervolgens **Eigenschappen**.

4. Geef in het vak **poort** het poort nummer voor de beschikbaarheids groep-listener op met behulp van de $EndpointPort die u eerder hebt gebruikt (1433 was de standaard instelling) en selecteer vervolgens **OK**.

U hebt nu een beschikbaarheids groep in azure virtual machines die worden uitgevoerd in de modus Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>De verbinding met de listener testen

Test de verbinding door de volgende stappen uit te voeren:

1. Gebruik RDP (Remote Desktop Protocol) om verbinding te maken met een SQL Server-exemplaar dat zich in hetzelfde virtuele netwerk bevindt, maar geen eigenaar is van de replica. Deze server kan het andere SQL Server-exemplaar in het cluster zijn.

2. Gebruik het **Sqlcmd** -hulp programma om de verbinding te testen. Met het volgende script wordt bijvoorbeeld met Windows-verificatie een **sqlcmd**-verbinding met de primaire replica tot stand gebracht via de listener:

    ```console
    sqlcmd -S <listenerName> -E
    ```

De SQLCMD-verbinding maakt automatisch verbinding met het SQL Server-exemplaar dat als host fungeert voor de primaire replica. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Een IP-adres maken voor een extra beschikbaarheids groep

Elke beschikbaarheids groep maakt gebruik van een afzonderlijke listener. Elke listener heeft een eigen IP-adres. Gebruik hetzelfde load balancer om het IP-adres voor extra listeners te bewaren. Nadat u een beschikbaarheids groep hebt gemaakt, voegt u het IP-adres toe aan de load balancer en configureert u vervolgens de listener.

Voer de volgende stappen uit om een IP-adres toe te voegen aan een load balancer met de Azure Portal:

1. Open in de Azure Portal de resource groep met de load balancer en selecteer vervolgens de load balancer. 

2. Selecteer bij **instellingen** de optie **frontend IP-adres groep** en selecteer vervolgens **toevoegen**. 

3. Wijs onder **IP-adres van frontend toevoegen** een naam toe aan de front-end. 

4. Controleer of het **virtuele netwerk** en het **subnet** hetzelfde zijn als de SQL Server exemplaren.

5. Stel het IP-adres in voor de listener. 
   
   >[!TIP]
   >U kunt het IP-adres instellen op statisch en een adres typen dat momenteel niet wordt gebruikt in het subnet. U kunt het IP-adres ook instellen op dynamisch en de nieuwe front-end-IP-groep opslaan. Wanneer u dit doet, wijst de Azure Portal automatisch een beschikbaar IP-adres toe aan de groep. U kunt de front-end-IP-adres groep vervolgens opnieuw openen en de toewijzing wijzigen in statisch. 

6. Sla het IP-adres voor de listener op. 

7. Voeg een status test toe met behulp van de volgende instellingen:

   |Instelling |Waarde
   |:-----|:----
   |**Naam** |Een naam om de test te identificeren.
   |**Protocol** |TCP
   |**Poort** |Een ongebruikte TCP-poort, die op alle virtuele machines beschikbaar moet zijn. Het kan niet voor andere doel einden worden gebruikt. Er kunnen niet twee listeners gebruikmaken van dezelfde test poort. 
   |**Interval** |De hoeveelheid tijd tussen de test pogingen. Gebruik de standaard waarde (5).
   |**Drempelwaarde voor onjuiste status** |Het aantal opeenvolgende drempel waarden dat moet mislukken voordat een virtuele machine als een slechte status wordt beschouwd.

8. Selecteer **OK** om de test op te slaan. 

9. Een regel voor taak verdeling maken. Selecteer **regels voor taak verdeling** en selecteer vervolgens **toevoegen**.

10. Configureer de nieuwe regel voor taak verdeling met behulp van de volgende instellingen:

    |Instelling |Waarde
    |:-----|:----
    |**Naam** |Een naam om de taakverdelings regel te identificeren. 
    |**Front-end-IP-adres** |Selecteer het IP-adres dat u hebt gemaakt. 
    |**Protocol** |TCP
    |**Poort** |Gebruik de poort die de SQL Server exemplaren gebruiken. Een standaard exemplaar gebruikt poort 1433, tenzij u deze hebt gewijzigd. 
    |**Poort back-end** |Gebruik dezelfde waarde als **poort**.
    |**Back-endpool** |De groep die de virtuele machines bevat met de SQL Server exemplaren. 
    |**Statustest** |Kies de test die u hebt gemaakt.
    |**Sessiepersistentie** |Geen
    |**Time-out voor inactiviteit (minuten)** |Standaard (4)
    |**Zwevend IP (Direct Server Return)** | Ingeschakeld

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>De beschikbaarheids groep configureren voor het gebruik van het nieuwe IP-adres

Als u de configuratie van het cluster wilt volt ooien, herhaalt u de stappen die u hebt gevolgd tijdens het maken van de eerste beschikbaarheids groep. Dat wil zeggen dat u het [cluster moet configureren voor het gebruik van het nieuwe IP-adres](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Nadat u een IP-adres voor de listener hebt toegevoegd, configureert u de extra beschikbaarheids groep door de volgende stappen uit te voeren: 

1. Controleer of de test poort voor het nieuwe IP-adres op beide SQL Server virtuele machines is geopend. 

2. [Voeg in Cluster beheer het client toegangs punt toe](#addcap).

3. [Configureer de IP-resource voor de beschikbaarheids groep](#congroup).

   >[!IMPORTANT]
   >Wanneer u het IP-adres maakt, gebruikt u het IP-adres dat u hebt toegevoegd aan de load balancer.  

4. [Zorg ervoor dat de resource van de SQL Server-beschikbaarheids groep afhankelijk is van het client toegangs punt](#dependencyGroup).

5. [Maak de bron van het client toegangs punt afhankelijk van het IP-adres](#listname).
 
6. [Stel de cluster parameters in Power shell in](#setparam).

Nadat u de beschikbaarheids groep hebt geconfigureerd voor het gebruik van het nieuwe IP-adres, configureert u de verbinding met de listener. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Taakverdelings regel toevoegen voor gedistribueerde beschikbaarheids groep

Als een beschikbaarheids groep deelneemt aan een gedistribueerde beschikbaarheids groep, heeft de load balancer een extra regel nodig. Deze regel slaat de poort op die wordt gebruikt door de gedistribueerde beschikbaarheids groep-listener.

>[!IMPORTANT]
>Deze stap is alleen van toepassing als de beschikbaarheids groep deelneemt aan een [gedistribueerde beschikbaarheids groep](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Maak op elke server die deel uitmaakt van de gedistribueerde beschikbaarheids groep, een regel voor binnenkomend verkeer op de TCP-poort van de gedistribueerde beschikbaarheids groep. In veel voor beelden maakt documentatie gebruik van 5022. 

1. Selecteer in de Azure Portal op de load balancer en selecteer **taakverdelings regels** en selecteer vervolgens **+ toevoegen**. 

1. Maak de taakverdelings regel met de volgende instellingen:

   |Instelling |Waarde
   |:-----|:----
   |**Naam** |Een naam voor het identificeren van de taakverdelings regel voor de gedistribueerde beschikbaarheids groep. 
   |**Front-end-IP-adres** |Gebruik hetzelfde frontend-IP-adres als de beschikbaarheids groep.
   |**Protocol** |TCP
   |**Poort** |5022-de poort voor de [eind punt-listener van de gedistribueerde beschikbaarheids groep](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Dit kan elke beschik bare poort zijn.  
   |**Poort back-end** | 5022-gebruik dezelfde waarde als **poort**.
   |**Back-endpool** |De groep die de virtuele machines bevat met de SQL Server exemplaren. 
   |**Statustest** |Kies de test die u hebt gemaakt.
   |**Sessiepersistentie** |Geen
   |**Time-out voor inactiviteit (minuten)** |Standaard (4)
   |**Zwevend IP (Direct Server Return)** | Ingeschakeld

Herhaal deze stappen voor de load balancer voor de andere beschikbaarheids groepen die deel uitmaken van de gedistribueerde beschikbaarheids groepen.

Als u een Azure-netwerk beveiligings groep hebt om de toegang te beperken, moet u ervoor zorgen dat de regels voor toestaan bevatten:
- De back-end-SQL Server VM-IP-adressen
- Het load balancer zwevende IP-adressen voor de AG-listener
- Het basis-IP-adres van het cluster, indien van toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Een SQL Server always on-beschikbaarheids groep configureren op virtuele machines van Azure in verschillende regio's](availability-group-manually-configure-multiple-regions.md)
