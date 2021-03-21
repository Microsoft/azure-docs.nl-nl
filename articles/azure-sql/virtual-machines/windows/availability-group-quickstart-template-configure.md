---
title: Beschikbaarheids groep configureren (Azure Quick Start-sjabloon)
description: Gebruik de sjablonen van Azure Quick Start om het Windows-failovercluster te maken, SQL Server Vm's toe te voegen aan het cluster, de listener te maken en de interne load balancer in azure te configureren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: d7dfe010a3f4a1559454c49545af81eb14797bf1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359911"
---
# <a name="use-azure-quickstart-templates-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Gebruik sjablonen van Azure Quick Start om een beschikbaarheids groep te configureren voor SQL Server op Azure VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u de Azure Quick Start-sjablonen gebruikt voor het gedeeltelijk automatiseren van de implementatie van een AlwaysOn-beschikbaarheids groep configuratie voor SQL Server virtuele machines (Vm's) in Azure. In dit proces worden twee Azure Quick Start-sjablonen gebruikt: 

   | Template | Beschrijving |
   | --- | --- |
   | [101-SQL-VM-AG-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Hiermee wordt het Windows-failovercluster gemaakt en worden de SQL Server Vm's eraan gekoppeld. |
   | [101-SQL-VM-aglistener-Setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | Hiermee maakt u de beschikbaarheids groep-listener en configureert u de interne load balancer. Deze sjabloon kan alleen worden gebruikt als het Windows-failovercluster is gemaakt met de sjabloon **101-SQL-VM-AG-Setup** . |
   | &nbsp; | &nbsp; |

Andere onderdelen van de configuratie van de beschikbaarheids groep moeten hand matig worden uitgevoerd, zoals het maken van de beschikbaarheids groep en het maken van de interne load balancer. Dit artikel bevat de volg orde van automatische en hand matige stappen.

Hoewel in dit artikel de Azure Quick Start-sjablonen worden gebruikt om de omgeving voor de beschikbaarheids groep te configureren, is het ook mogelijk de [Azure Portal](availability-group-azure-portal-configure.md), [Power shell of de Azure cli](availability-group-az-commandline-configure.md)of ook [hand matig](availability-group-manually-configure-tutorial.md) te gebruiken. 
 

## <a name="prerequisites"></a>Vereisten 
Als u de installatie van een AlwaysOn-beschikbaarheids groep wilt automatiseren met behulp van Quick Start-sjablonen, moet u beschikken over de volgende vereisten: 
- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een resource groep met een domein controller. 
- Een of meer aan een domein gekoppelde [virtuele machines in azure met SQL Server 2016 (of hoger) Enter prise Edition](./create-sql-vm-portal.md) die zich in dezelfde beschikbaarheidsset of beschikbaarheids zone bevinden en die zijn [geregistreerd bij de SQL IaaS agent-extensie](sql-agent-extension-manually-register-single-vm.md).  
- Twee beschik bare (niet gebruikt door een entiteit) IP-adressen: een voor de interne load balancer en één voor de beschikbaarheids groep-listener binnen hetzelfde subnet als de beschikbaarheids groep. Als een bestaande load balancer wordt gebruikt, hebt u slechts één beschikbaar IP-adres nodig.  

## <a name="permissions"></a>Machtigingen
De volgende machtigingen zijn nodig voor het configureren van de always on-beschikbaarheids groep met behulp van Azure Quick Start-sjablonen: 

- Een bestaand domein gebruikers account met een machtiging voor het **maken van computer objecten** in het domein.  Een domein beheerders account heeft bijvoorbeeld doorgaans voldoende machtigingen (bijvoorbeeld: account@domain.com ). _Dit account moet ook lid zijn van de lokale groep Administrators op elke virtuele machine om het cluster te maken._
- Het domein gebruikers account dat SQL Server beheert. 


## <a name="create-cluster"></a>Cluster maken
Nadat uw SQL Server Vm's zijn geregistreerd bij de SQL IaaS agent-extensie, kunt u uw SQL Server Vm's toevoegen aan *SqlVirtualMachineGroups*. Met deze bron worden de meta gegevens van het Windows-failovercluster gedefinieerd. Meta gegevens bevatten de versie, editie, Fully Qualified Domain Name, Active Directory accounts voor het beheren van zowel het cluster als SQL Server en het opslag account als de cloudwitness. 

Het toevoegen van SQL Server Vm's aan de resource groep *SqlVirtualMachineGroups* Boots trapt de Windows failover cluster-service om het cluster te maken en voegt vervolgens deze SQL Server vm's toe aan dat cluster. Deze stap is geautomatiseerd met de sjabloon **101-SQL-VM-AG-Setup** Quick Start. U kunt het implementeren met behulp van de volgende stappen:

1. Ga naar de sjabloon [**101-SQL-VM-AG-Setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) Quick Start. Selecteer vervolgens **implementeren naar Azure** om de Quick Start-sjabloon te openen in de Azure Portal.
1. Vul de vereiste velden in voor het configureren van de meta gegevens voor het Windows-failovercluster. U kunt de optionele velden leeg laten.

   De volgende tabel bevat de vereiste waarden voor de sjabloon: 

   | **Veld** | Waarde |
   | --- | --- |
   | **Abonnement** |  Het abonnement waar uw SQL Server Vm's bestaan. |
   |**Resourcegroep** | De resource groep waar uw SQL Server Vm's zich bevinden. | 
   |**Naam van failovercluster** | De naam die u wilt voor uw nieuwe Windows-failovercluster. |
   | **Bestaande VM-lijst** | De SQL Server Vm's die u wilt deel nemen aan de beschikbaarheids groep en deel uitmaken van dit nieuwe cluster. Scheid deze waarden met een komma en een spatie (bijvoorbeeld: *SQLVM1, SQLVM2*). |
   | **SQL Server versie** | De SQL Server versie van uw SQL Server Vm's. Selecteer deze in de vervolg keuzelijst. Momenteel worden alleen SQL Server 2016-en SQL Server 2017-installatie kopieën ondersteund. |
   | **Bestaande volledig gekwalificeerde domein naam** | De bestaande FQDN voor het domein waarin uw SQL Server Vm's zich bevinden. |
   | **Bestaand domein account** | Een bestaand domein gebruikers account met een machtiging voor het **maken van computer objecten** in het domein als de [CNO](/windows-server/failover-clustering/prestage-cluster-adds) wordt gemaakt tijdens de implementatie van de sjabloon. Een domein beheerders account heeft bijvoorbeeld doorgaans voldoende machtigingen (bijvoorbeeld: account@domain.com ). *Dit account moet ook lid zijn van de lokale groep Administrators op elke virtuele machine om het cluster te maken.*| 
   | **Domein account wachtwoord** | Het wacht woord voor het eerder genoemde domein gebruikers account. | 
   | **Bestaand SQL Service-account** | Het domein gebruikers account dat de [SQL Server-service](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions) beheert tijdens de implementatie van de beschikbaarheids groep (bijvoorbeeld: account@domain.com ). |
   | **Wacht woord van SQL-service** | Het wacht woord dat wordt gebruikt door het domein gebruikers account dat SQL Server beheert. |
   | **Naam van Cloud-Witness** | Een nieuw Azure-opslag account dat wordt gemaakt en gebruikt voor de cloudwitness. U kunt deze naam wijzigen. |
   | **\_Locatie van artefacten** | Dit veld is standaard ingesteld en mag niet worden gewijzigd. |
   | **\_SaS-token voor locatie van artefacten** | Dit veld wordt opzettelijk leeg gelaten. |
   | &nbsp; | &nbsp; |

1. Als u akkoord gaat met de voor waarden, schakelt u het selectie vakje **Ik ga akkoord met de voor waarden die hierboven zijn vermeld** in. Selecteer vervolgens **aankoop** om de implementatie van de Quick Start-sjabloon te volt ooien. 
1. Als u uw implementatie wilt bewaken, selecteert u de implementatie in het pictogram **meldingen** Bell in het bovenste navigatie vaandel of gaat u naar de **resource groep** in de Azure Portal. Selecteer **implementaties** onder **instellingen** en kies de implementatie van **micro soft. Temp late** . 

>[!NOTE]
> De referenties die tijdens de sjabloon implementatie worden gegeven, worden alleen opgeslagen voor de duur van de implementatie. Nadat de implementatie is voltooid, worden deze wacht woorden verwijderd. U wordt gevraagd deze weer te geven als u meer SQL Server Vm's aan het cluster toevoegt. 



## <a name="validate-cluster"></a>Cluster valideren 

Voor een failovercluster dat door micro soft wordt ondersteund, moet de cluster validatie worden door gegeven. Maak verbinding met de virtuele machine met behulp van uw voorkeurs methode, zoals Remote Desktop Protocol (RDP) en valideer of uw cluster valideert voordat u verder gaat. Als u dit niet doet, wordt de status van het cluster niet ondersteund. 

U kunt het cluster valideren met behulp van Failoverclusterbeheer (FCM) of de volgende Power shell-opdracht:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```


## <a name="create-availability-group"></a>Beschikbaarheids groep maken 
Maak de beschikbaarheids groep hand matig zoals u dat gewoonlijk zou doen met behulp van [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [Power shell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)of [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Maak op dit moment *geen* listener, omdat de sjabloon **101-SQL-VM-aglistener-Setup**  Quick Start in stap 4 automatisch wordt uitgevoerd. 

## <a name="create-load-balancer"></a>Load balancer maken

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Voor de always on-beschikbaarheids groep-listener is een intern exemplaar van Azure Load Balancer vereist. De interne load balancer biedt een ' zwevend ' IP-adres voor de beschikbaarheids groep-listener die snellere failover en opnieuw verbinden mogelijk maakt. Als de SQL Server Vm's in een beschikbaarheids groep deel uitmaken van dezelfde beschikbaarheidsset, kunt u een basis load balancer gebruiken. Anders moet u een standaard load balancer gebruiken. 

> [!IMPORTANT]
> De interne load balancer moeten zich in hetzelfde virtuele netwerk bevindt als de VM-exemplaren van SQL Server. 

U hoeft alleen de interne load balancer te maken. In stap 4 wordt de rest van de configuratie (zoals de back-end-pool, de status test en de regels voor taak verdeling) verwerkt met de Snelstartgids **101-SQL-VM-aglistener-Setup** . 

1. Open in Azure-portal de resourcegroep die de virtuele SQL Server-machines omvat. 
2. Selecteer in de resource groep de optie **toevoegen**.
3. Zoeken naar **Load Balancer**. Selecteer in de zoek resultaten **Load Balancer**, die is gepubliceerd door **micro soft**.
4. Selecteer op  de blade Load Balancer **maken**.
5. Configureer de taakverdeler in het dialoogvenster **Taakverdeler maken** als volgt:

   | Instelling | Waarde |
   | --- | --- |
   | **Naam** |Voer een tekst naam in die de load balancer vertegenwoordigt. Voer bijvoorbeeld **sqlLB**. |
   | **Type** |**Intern**: de meeste implementaties gebruiken een interne Load Balancer, waarmee toepassingen binnen hetzelfde virtuele netwerk verbinding kunnen maken met de beschikbaarheids groep.  </br> **Extern**: Hiermee kunnen toepassingen verbinding maken met de beschikbaarheids groep via een open bare Internet verbinding. |
   | **Virtueel netwerk** | Selecteer het virtuele netwerk waarin de SQL Server exemplaren zich bevinden. |
   | **Subnet** | Selecteer het subnet waarin de SQL Server-exemplaren zich bevinden. |
   | **IP-adrestoewijzing** |**Statisch** |
   | **Privé IP-adres** | Geef een beschikbaar IP-adres op in het subnet. |
   | **Abonnement** |Als u meerdere abonnementen hebt, kan dit veld worden weer gegeven. Selecteer het abonnement dat u aan deze resource wilt koppelen. Dit is normaal gesp roken hetzelfde abonnement als alle resources voor de beschikbaarheids groep. |
   | **Resourcegroep** |Selecteer de resourcegroep waarin de SQL Server-exemplaren zich bevinden. |
   | **Locatie** |Selecteer de Azure-locatie waar de SQL Server-exemplaren zich bevinden. |
   | &nbsp; | &nbsp; |

6. Selecteer **Maken**. 


>[!IMPORTANT]
> De open bare IP-resource voor elke SQL Server virtuele machine moet een standaard-SKU hebben om compatibel te zijn met de standaard load balancer. Als u de SKU van de open bare IP-resource van de virtuele machine wilt bepalen, gaat u naar de **resource groep**, selecteert u de resource voor het **open bare IP-adres** voor de SQL Server virtuele machine en zoekt u de waarde onder **SKU** in het deel venster **overzicht** . 

## <a name="create-listener"></a>Listener maken 

Maak de listener voor de beschikbaarheids groep en configureer de interne load balancer automatisch met behulp van de sjabloon **101-SQL-VM-aglistener-Setup**  Quick Start. De sjabloon richt zich op de resource micro soft. SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener. Met de sjabloon  **101-SQL-VM-aglistener-Setup** Quick Start, via de SQL IaaS agent-extensie, worden de volgende acties uitgevoerd:

- Hiermee maakt u een nieuwe frontend-IP-bron (op basis van de IP-adres waarde die tijdens de implementatie is gegeven) voor de listener. 
- Hiermee configureert u de netwerk instellingen voor het cluster en de interne load balancer. 
- Hiermee configureert u de back-end-pool voor de interne load balancer, de status test en de regels voor de taak verdeling.
- Hiermee maakt u de beschikbaarheids groep-listener met de opgegeven IP-adres en naam.
 
>[!NOTE]
> U kunt **101-SQL-VM-aglistener-Setup** alleen gebruiken als het Windows-failovercluster is gemaakt met de sjabloon **101-SQL-VM-AG-Setup** .
   
   
Ga als volgt te werk om de interne load balancer te configureren en de beschikbaarheids groep-listener te maken:
1. Ga naar de sjabloon [101-SQL-VM-aglistener-installatie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) Snelstartgids en selecteer **implementeren naar Azure** om de Quick Start-sjabloon in de Azure portal te starten.
1. Vul de vereiste velden in om de interne load balancer te configureren en maak de listener voor de beschikbaarheids groep. U kunt de optionele velden leeg laten. 

   De volgende tabel bevat de vereiste waarden voor de sjabloon: 

   | **Veld** | Waarde |
   | --- | --- |
   |**Resourcegroep** | De resource groep waar uw SQL Server Vm's en beschikbaarheids groep bestaan. | 
   |**Bestaande naam van het failovercluster** | De naam van het cluster waaraan uw SQL Server-Vm's zijn gekoppeld. |
   | **Bestaande SQL-beschikbaarheids groep**| De naam van de beschikbaarheids groep waarvan uw SQL Server Vm's deel uitmaken. |
   | **Bestaande VM-lijst** | De namen van de SQL Server Vm's die deel uitmaken van de eerder genoemde beschikbaarheids groep. Scheid de namen met een komma en een spatie (bijvoorbeeld: *SQLVM1, SQLVM2*). |
   | **Listener** | De DNS-naam die u wilt toewijzen aan de listener. Met deze sjabloon wordt standaard de naam ' aglistener ' opgegeven, maar u kunt deze wijzigen. De naam mag niet langer zijn dan 15 tekens. |
   | **Listener-poort** | De poort die u door de listener wilt laten gebruiken. Normaal gesp roken moet deze poort de standaard waarde van 1433 zijn. Dit is het poort nummer dat door de sjabloon wordt opgegeven. Maar als uw standaard poort is gewijzigd, moet de listener-poort deze waarde gebruiken in plaats daarvan. | 
   | **Listener-IP** | Het IP-adres dat u wilt gebruiken voor de listener. Dit adres wordt gemaakt tijdens de implementatie van de sjabloon. Geef dus een naam op die nog niet in gebruik is.  |
   | **Bestaand subnet** | De naam van het interne subnet van uw SQL Server Vm's (bijvoorbeeld: *standaard*). U kunt deze waarde bepalen door te gaan naar de **resource groep**, het virtuele netwerk te selecteren, **subnetten** te selecteren in het deel venster **instellingen** en de waarde onder **naam** te kopiëren. |
   | **Bestaande interne Load Balancer** | De naam van de interne load balancer die u hebt gemaakt in stap 3. |
   | **Test poort** | De test poort waarvoor u de interne load balancer wilt gebruiken. De sjabloon maakt standaard gebruik van 59999, maar u kunt deze waarde wijzigen. |
   | &nbsp; | &nbsp; |

1. Als u akkoord gaat met de voor waarden, schakelt u het selectie vakje **Ik ga akkoord met de voor waarden die hierboven zijn vermeld** in. Selecteer **aankoop** om de implementatie van de Quick Start-sjabloon te volt ooien. 
1. Als u uw implementatie wilt bewaken, selecteert u de implementatie in het pictogram **meldingen** Bell in het bovenste navigatie vaandel of gaat u naar de **resource groep** in de Azure Portal. Selecteer **implementaties** onder **instellingen** en kies de implementatie van **micro soft. Temp late** . 

>[!NOTE]
>Als uw implementatie halverwege kan worden uitgevoerd, moet u [de zojuist gemaakte listener](#remove-listener) hand matig verwijderen met behulp van Power shell voordat u de **101-SQL-VM-aglistener-Setup** Quick Start-sjabloon opnieuw implementeert. 

## <a name="remove-listener"></a>Listener verwijderen
Als u later de beschikbaarheids groep wilt verwijderen dat de sjabloon is geconfigureerd, moet u de SQL IaaS agent-extensie door lopen. Omdat de listener is geregistreerd via de SQL IaaS agent-extensie, hoeft u deze niet te verwijderen via SQL Server Management Studio onvoldoende. 

U kunt de methode het beste verwijderen via de SQL IaaS agent-extensie met behulp van het volgende code fragment in Power shell. Hiermee verwijdert u de meta gegevens van de beschikbaarheids groep van de SQL IaaS agent-extensie. Ook wordt de listener fysiek uit de beschikbaarheids groep verwijderd. 

```PowerShell
# Remove the availability group listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>Algemene fouten
In deze sectie worden enkele bekende problemen en mogelijke oplossingen besproken. 

De **beschikbaarheids groep-listener voor de beschikbaarheids groep \<AG-Name> bestaat al** de geselecteerde beschikbaarheids groep die wordt gebruikt in de Azure Quick Start-sjabloon voor de listener van de beschikbaarheids groep bevat al een listener. Het bevindt zich fysiek binnen de beschikbaarheids groep of de meta gegevens blijven binnen de SQL IaaS agent-extensie. Verwijder de listener met behulp van [Power shell](#remove-listener) voordat u de sjabloon **101-SQL-VM-aglistener-Setup** Snelstartgids opnieuw implementeert. 

De **verbinding werkt alleen vanuit de primaire replica** Dit gedrag is waarschijnlijk van een mislukte **101-SQL-VM-aglistener-installatie** sjabloon implementatie die de configuratie van de interne Load Balancer met een inconsistente status heeft verlaten. Controleer of de back-endadresgroep een lijst met beschikbaarheids sets bevat en of er regels bestaan voor de status test en voor de regels voor de taak verdeling. Als er iets ontbreekt, is de configuratie van de interne load balancer een inconsistente status. 

U kunt dit probleem oplossen door de listener te verwijderen met behulp van [Power shell](#remove-listener), de interne Load Balancer te verwijderen via de Azure Portal en opnieuw te beginnen bij stap 3. 

**Alleen-onjuiste aanvraag virtuele-machine lijst kan worden bijgewerkt** Deze fout kan optreden wanneer u de sjabloon **101-SQL-VM-aglistener-Setup** implementeert als de listener is verwijderd via SQL Server Management Studio (SSMS), maar niet is verwijderd uit de SQL IaaS agent-extensie. Als u de listener via SSMS verwijdert, worden de meta gegevens van de listener niet verwijderd uit de SQL IaaS agent-extensie. De listener moet worden verwijderd van de resource provider via [Power shell](#remove-listener). 

Het **domein account bestaat niet** Deze fout kan twee oorzaken hebben. Ofwel het opgegeven domein account bestaat niet of de [UPN-gegevens (User Principal Name)](/windows/desktop/ad/naming-properties#userprincipalname) ontbreken. De sjabloon **101-SQL-VM-AG-Setup** verwacht een domein account in het UPN-formulier (dat wil zeggen user@domain.com ), maar sommige domein accounts ontbreken mogelijk. Dit gebeurt meestal wanneer een lokale gebruiker is gemigreerd als het eerste domein beheerders account wanneer de server is gepromoveerd naar een domein controller of wanneer een gebruiker is gemaakt via Power shell. 

Controleer of het account bestaat. Als dat het geval is, wordt u mogelijk in de tweede situatie uitgevoerd. Ga als volgt te werk om het probleem op te lossen:

1. Open op de domein controller het venster **Active Directory gebruikers en computers** met de optie **extra** in **Serverbeheer**. 
2. Ga naar het account door **gebruikers** te selecteren in het linkerdeel venster.
3. Klik met de rechter muisknop op het account en selecteer **Eigenschappen**.
4. Selecteer het tabblad **account** . Als het vak **aanmeldings naam van gebruiker** leeg is, is dit de oorzaak van de fout. 

    ![Lege gebruikers account geeft ontbrekende UPN aan](./media/availability-group-quickstart-template-configure/account-missing-upn.png)

5. Vul het vak **aanmeldings naam** van de gebruiker in om de naam van de gebruiker te zoeken en selecteer het juiste domein in de vervolg keuzelijst. 
6. Selecteer **Toep assen** om uw wijzigingen op te slaan en sluit het dialoog venster door **OK** te selecteren. 

Nadat u deze wijzigingen hebt aangebracht, kunt u de Azure Quick Start-sjabloon nog één keer implementeren. 


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server Vm's](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server Vm's](frequently-asked-questions-faq.md)
* [Prijs informatie voor SQL Server Vm's](pricing-guidance.md)
* [Release opmerkingen voor SQL Server Vm's](../../database/doc-changes-updates-release-notes.md)
* [Licentie modellen voor een SQL Server VM overschakelen](licensing-model-azure-hybrid-benefit-ahb-change.md)