---
title: Zelf studie voor het configureren van een Azure SSIS Integration runtime om lid te worden van een virtueel netwerk
description: Meer informatie over het toevoegen aan een Azure-SSIS Integration runtime om lid te worden van een virtueel Azure-netwerk.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3dbbce4adc44696fdd12f6ce948b48b34caaed75
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391232"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Een Azure-SQL Server Integration Services (SSIS) Integration runtime (IR) configureren om lid te worden van een virtueel netwerk

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Deze zelf studie bevat basis stappen voor het gebruik van de Azure Portal om een Azure-SQL Server Integration Services (SSIS) Integration runtime (IR) te configureren om lid te worden van een virtueel netwerk.

De stappen zijn onder andere:

- Configureer een virtueel netwerk.
- Voeg de Azure-SSIS IR toe aan een virtueel netwerk via Azure Data Factory Portal.

## <a name="prerequisites"></a>Vereisten

- **Azure-SSIS Integration runtime**. Als u geen Azure SSIS Integration runtime hebt, moet u [een Azure SSIS Integration runtime in azure Data Factory inrichten](tutorial-deploy-ssis-packages-azure.md) voordat u begint.

- **Gebruikers machtiging**. De gebruiker die de Azure-SSIS IR maakt, moet beschikken over de [roltoewijzing](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) ten minste op Azure Data Factory resource met een van de volgende opties:

    - Gebruik de ingebouwde rol netwerk bijdrager. Deze rol wordt geleverd met het _micro soft. \* Network/-_ machtiging, met een veel groter bereik dan nodig is.
    - Maak een aangepaste rol die alleen de benodigde machtigingen voor _micro soft. Network/virtualNetworks/ \* /join/Action_ bevat. Als u ook uw eigen open bare IP-adressen voor Azure-SSIS IR wilt gebruiken terwijl u deze toevoegt aan een Azure Resource Manager virtueel netwerk, moet u ook de machtiging _micro soft. Network/publicIPAddresses/*/join/Action_ opnemen in de rol.

- **Virtueel netwerk**.

    - Als u geen virtueel netwerk hebt, maakt u [een virtueel netwerk met behulp van de Azure Portal](../virtual-network/quick-create-portal.md).

    - Zorg ervoor dat de resource groep van het virtuele netwerk bepaalde Azure-netwerk resources kan maken en verwijderen.
    
        Het Azure-SSIS IR moet bepaalde netwerk bronnen maken onder dezelfde resource groep als het virtuele netwerk. Deze resources omvatten:
        - Een Azure-load balancer met de naam *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - Een netwerk beveiligings groep, met de naam * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Een openbaar IP-adres van Azure, met de naam-azurebatch-cloudservicepublicip
    
        Deze resources worden gemaakt wanneer uw Azure-SSIS IR wordt gestart. Ze worden verwijderd wanneer uw Azure-SSIS IR wordt gestopt. Als u wilt voor komen dat uw Azure-SSIS IR worden gestopt, moet u deze netwerk bronnen niet opnieuw gebruiken in uw andere resources.

    - Zorg ervoor dat er geen [resource vergrendeling](../azure-resource-manager/management/lock-resources.md) is voor de resource groep/het abonnement waartoe het virtuele netwerk behoort. Als u een alleen-lezen/Verwijder vergrendeling configureert, mislukt het starten en stoppen van de Azure-SSIS IR, of reageert het niet meer.

    - Zorg ervoor dat u geen Azure Policy toewijzing hebt waarmee wordt voor komen dat de volgende resources worden gemaakt onder de resource groep/het abonnement waartoe het virtuele netwerk behoort:
        - Micro soft. Network/LoadBalancers
        - Micro soft. Network/NetworkSecurityGroups

- Onder **netwerk configuratie** scenario's vallen niet onder deze zelf studie:
    - Als u uw eigen open bare IP-adressen voor de Azure-SSIS IR meebrengt.
    - Als u uw eigen Domain Name System (DNS)-server gebruikt.
    - Als u een netwerk beveiligings groep (NSG) gebruikt in het subnet.
    - Als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt.
    - Als u aangepaste Azure-SSIS IR gebruikt.
    
    Controleer de configuratie van het [virtuele netwerk](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)voor meer informatie.

## <a name="configure-a-virtual-network"></a>Een virtueel netwerk configureren

Gebruik de Azure Portal om een virtueel netwerk te configureren voordat u probeert een Azure-SSIS IR te koppelen.

1. Start micro soft Edge of Google Chrome. Momenteel ondersteunen alleen deze webbrowsers de Data Factory-gebruikers interface.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **meer services**. Filter voor en selecteer **virtuele netwerken**.

1. Filter en selecteer uw virtuele netwerk in de lijst.

1. Selecteer op de pagina **virtueel netwerk** **Eigenschappen**.

1. Selecteer de knop kopiëren voor de **resource-id** om de resource-id voor het virtuele netwerk naar het klem bord te kopiëren. Sla de ID op in het klem bord in OneNote of in een bestand.

1. Selecteer **subnetten** in het menu links.

    - Zorg ervoor dat het door u geselecteerde subnet voldoende beschik bare adres ruimte heeft om de Azure-SSIS IR te gebruiken. Houd de beschik bare IP-adressen voor ten minste twee keer het IR-knooppunt nummer. Azure reserveert een aantal IP-adressen binnen elk subnet. Deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit en er worden nog drie adressen voor Azure-Services gebruikt. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie.
    - Selecteer de GatewaySubnet voor het implementeren van een Azure-SSIS IR niet. Het is toegewezen voor virtuele netwerk gateways.
    - Gebruik geen subnet dat uitsluitend wordt ingen Omen door andere Azure-Services (bijvoorbeeld SQL Database SQL Managed instance, App Service, enzovoort).

1. Controleer of de Azure Batch provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of Registreer de Azure Batch-provider. Als u al een Azure Batch account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u de Azure-SSIS IR maakt in de Data Factory Portal, wordt de Azure Batch provider automatisch voor u geregistreerd.)

   1. Selecteer in het Azure Portal in het linkermenu **abonnementen**.

   1. Selecteer uw abonnement.

   1. Selecteer aan de linkerkant **resource providers** en controleer of **Microsoft.BatCH** een geregistreerde provider is.

   ![Bevestiging van de status ' geregistreerd '](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Als **Microsoft.BatCH** niet in de lijst wordt weer geven, [maakt u een leeg Azure batch-account](../batch/batch-account-create-portal.md) in uw abonnement om het te registreren. U kunt deze later verwijderen.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>De Azure-SSIS IR toevoegen aan een virtueel netwerk

Nadat u uw Azure Resource Manager virtuele netwerk of het klassieke virtuele netwerk hebt geconfigureerd, kunt u de Azure-SSIS IR toevoegen aan het virtuele netwerk:

1. Start micro soft Edge of Google Chrome. Momenteel ondersteunen alleen deze webbrowsers de Data Factory-gebruikers interface.

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het linkermenu **gegevens fabrieken**. Als er geen **gegevens fabrieken** worden weer gegeven in het menu, selecteert u **meer services** en selecteert u in de sectie **Intelligence en analyse** **gegevens fabrieken**.

   ![Lijst met gegevens fabrieken](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecteer uw data factory met de Azure-SSIS IR in de lijst. U ziet de start pagina voor uw data factory. Selecteer de tegel **Maken en bewaken**. U ziet de Data Factory gebruikers interface op een afzonderlijk tabblad.

   ![Startpagina van de gegevensfactory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Ga in de Data Factory-gebruikers interface naar het tabblad **bewerken** , selecteer **verbindingen** en schakel over naar het tabblad **Integration Runtimes** .

   ![Tabblad Integration Runtimes](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Als uw Azure-SSIS IR wordt uitgevoerd, selecteert u in de kolom **acties** van de lijst met **integratie-Runtimes** de knop **stoppen** voor uw Azure-SSIS IR. U kunt uw Azure-SSIS IR pas bewerken nadat u het hebt gestopt.

   ![De IR stoppen](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Selecteer de knop **bewerken** voor uw Azure-SSIS IR in de kolom **acties** van de lijst **Integration Runtimes** .

   ![De Integration runtime bewerken](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Ga in het configuratie venster voor Integration runtime naar de secties **algemene instellingen** en **SQL-instellingen** en selecteer de knop **volgende** .

1. In het gedeelte **Geavanceerde instellingen** :
   1. Selecteer **een VNet selecteren voor uw Azure-SSIS Integration runtime om toe te voegen, ADF toestaan om bepaalde netwerk resources te maken en eventueel uw eigen statische open bare IP-adressen** in te stellen.

   1. Selecteer bij **abonnement** het Azure-abonnement met uw virtuele netwerk.

   1. Voor **locatie** is dezelfde locatie van de Integration runtime geselecteerd.

   1. Selecteer bij **type** het type van uw virtuele netwerk: klassiek of Azure Resource Manager. We raden u aan om een Azure Resource Manager virtueel netwerk te selecteren, omdat klassieke virtuele netwerken binnenkort worden afgeschaft.

   1. Selecteer voor **VNet-naam** de naam van het virtuele netwerk. Dit moet hetzelfde zijn als de SQL Database met virtuele netwerk service-eind punten of een SQL Managed instance met een persoonlijk eind punt om SSISDB te hosten. Of het moet hetzelfde zijn als het account dat is verbonden met uw on-premises netwerk. Anders kan het een virtueel netwerk zijn om uw eigen statische open bare IP-adressen te maken voor Azure-SSIS IR.

   1. Selecteer bij **subnet naam** de naam van het subnet voor het virtuele netwerk. Dit moet hetzelfde zijn als die voor SQL-datbase met virtuele netwerk service-eind punten om SSISDB te hosten. Of het moet een ander subnet zijn dat wordt gebruikt voor uw SQL Managed instance met een persoonlijk eind punt om SSISDB te hosten. Anders kan het een wille keurige subnet zijn om uw eigen statische open bare IP-adressen voor Azure-SSIS IR te brengen.

   1. Selecteer **VNet-validatie**. Als de validatie is geslaagd, selecteert u **door gaan**.

   ![Geavanceerde instellingen met een virtueel netwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Controleer in de sectie **samen vatting** alle instellingen voor uw Azure-SSIS IR. Selecteer vervolgens **bijwerken**.

1. Start uw Azure-SSIS IR door de knop **Start** te selecteren in de kolom **acties** voor uw Azure-SSIS IR. Het duurt ongeveer 20 tot 30 minuten om de Azure-SSIS IR die aan een virtueel netwerk zijn gekoppeld, te starten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het toevoegen van Azure-SSIS IR aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md).