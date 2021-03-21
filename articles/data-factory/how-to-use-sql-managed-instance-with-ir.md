---
title: Azure SQL Managed instance met Azure-SQL Server Integration Services (SSIS) gebruiken in Azure Data Factory
description: Meer informatie over het gebruik van Azure SQL Managed instance met SQL Server Integration Services (SSIS) in Azure Data Factory.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 62bd5d2e70d3a66998907305fecee4dcc87cdb23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451724"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Azure SQL Managed instance met SQL Server Integration Services (SSIS) gebruiken in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

U kunt nu uw SQL Server Integration Services (SSIS)-projecten, pakketten en workloads naar de Azure-Cloud verplaatsen. U kunt SSIS-projecten en-pakketten implementeren, uitvoeren en beheren op Azure SQL Database of een door SQL beheerd exemplaar met vertrouwde hulpprogram ma's zoals SQL Server Management Studio (SSMS). In dit artikel worden de volgende specifieke gebieden voor het gebruik van Azure SQL Managed instance met Azure-SSIS Integration runtime (IR) gemarkeerd.

- [Een Azure-SSIS IR inrichten met een SSIS-catalogus (SSISDB) die wordt gehost door een door Azure SQL beheerd exemplaar](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [SSIS-pakketten uitvoeren met Azure SQL Managed instance agent-taak](how-to-invoke-ssis-package-managed-instance-agent.md)
- [SSISDB-logboeken opschonen door de Azure SQL Managed instance agent-taak](#clean-up-ssisdb-logs)
- [Azure-SSIS IR failover met Azure SQL Managed instance](configure-bcdr-azure-ssis-integration-runtime.md)
- [On-premises SSIS-workloads migreren naar SSIS in ADF met Azure SQL Managed instance as data base workload Destination](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Azure-SSIS IR inrichten met SSISDB die door Azure SQL Managed instance worden gehost

### <a name="prerequisites"></a>Vereisten

1. [Schakel Azure Active Directory (Azure AD) in op Azure SQL Managed instance](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)bij het kiezen van Azure Active Directory-verificatie.

1. Kies hoe u verbinding wilt maken met SQL Managed instance, via een privé-eind punt of via een openbaar eind punt:

    - Over privé-eind punt (voor keur)

        1. Kies het virtuele netwerk waarmee Azure-SSIS IR moet worden toegevoegd:
            - Binnen hetzelfde virtuele netwerk als het beheerde exemplaar, met een **ander subnet**.
            - Binnen een ander virtueel netwerk dan het beheerde exemplaar, via virtuele netwerk peering (die beperkt is tot dezelfde regio als gevolg van globale VNet-peering-beperkingen) of een verbinding van een virtueel netwerk met een virtueel netwerk.

            Zie [uw toepassing verbinden met Azure SQL Managed instance](/azure/sql-database/sql-database-managed-instance-connect-app)(Engelstalig) voor meer informatie over de connectiviteit van SQL Managed instance.

        1. [Configureer het virtuele netwerk](#configure-virtual-network).

    - Via open bare eind punt

        Azure SQL Managed instances kunnen connectiviteit bieden via [open bare eind punten](../azure-sql/managed-instance/public-endpoint-configure.md). Voor inkomende en uitgaande vereisten moet worden voldaan om verkeer tussen SQL Managed instance en Azure-SSIS IR toe te staan:

        - Als Azure-SSIS IR in een virtueel netwerk (voor keur)

            **Inkomende vereiste van SQL Managed instance** om binnenkomend verkeer van Azure-SSIS IR toe te staan.

            | Transport Protocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel |
            |---|---|---|---|---|
            |TCP|Azure Cloud service-tag|*|VirtualNetwork|3342|

            Zie [openbaar eindpunt verkeer toestaan voor de netwerk beveiligings groep](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)voor meer informatie.

        - Wanneer Azure-SSIS IR binnen een virtueel netwerk

            Er is een speciaal scenario wanneer een SQL Managed instance deel uitmaakt van een regio die Azure-SSIS IR niet wordt ondersteund. Azure-SSIS IR bevindt zich in een virtueel netwerk zonder VNet-peering vanwege een beperking van globale VNet-peering. In dit scenario verbindt **Azure-SSIS IR in een virtueel netwerk** SQL Managed instance **via een openbaar eind punt**. Gebruik de onderstaande regels voor netwerk beveiligings groepen (NSG) om verkeer tussen SQL Managed instance en Azure-SSIS IR toe te staan:

            1. **Inkomende vereiste van SQL Managed instance** om binnenkomend verkeer van Azure-SSIS IR toe te staan.

                | Transport Protocol | Bron | Poortbereik van bron | Doel |Poortbereik van doel |
                |---|---|---|---|---|
                |TCP|Statisch IP-adres van Azure-SSIS IR <br> Zie [uw eigen open bare IP-adres voor Azure-SSIS IR meenemen](join-azure-ssis-integration-runtime-virtual-network.md#publicIP)voor meer informatie.|*|VirtualNetwork|3342|

             1. **Uitgaande vereiste van Azure-SSIS IR** om uitgaand verkeer naar een door SQL beheerd exemplaar toe te staan.

                | Transport Protocol | Bron | Poortbereik van bron | Doel |Poortbereik van doel |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[IP-adres van openbaar eind punt voor SQL-beheerd exemplaar](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Virtueel netwerk configureren

1. **Gebruikers machtiging**. De gebruiker die de Azure-SSIS IR maakt, moet beschikken over de [roltoewijzing](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) ten minste op Azure Data Factory resource met een van de volgende opties:

    - Gebruik de ingebouwde rol netwerk bijdrager. Deze rol wordt geleverd met het _micro soft. \* Network/-_ machtiging, met een veel groter bereik dan nodig is.
    - Maak een aangepaste rol die alleen de benodigde machtigingen voor _micro soft. Network/virtualNetworks/ \* /join/Action_ bevat. Als u ook uw eigen open bare IP-adressen voor Azure-SSIS IR wilt gebruiken terwijl u deze toevoegt aan een Azure Resource Manager virtueel netwerk, moet u ook de machtiging _micro soft. Network/publicIPAddresses/*/join/Action_ in de rol opnemen.

1. **Virtueel netwerk**.

    1. Zorg ervoor dat de resource groep van het virtuele netwerk bepaalde Azure-netwerk resources kan maken en verwijderen.

        Het Azure-SSIS IR moet bepaalde netwerk bronnen maken onder dezelfde resource groep als het virtuele netwerk. Deze resources omvatten:
        - Een Azure-load balancer met de naam *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - Een netwerk beveiligings groep, met de naam * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Een openbaar IP-adres van Azure, met de naam-azurebatch-cloudservicepublicip

        Deze resources worden gemaakt wanneer uw Azure-SSIS IR wordt gestart. Ze worden verwijderd wanneer uw Azure-SSIS IR wordt gestopt. Als u wilt voor komen dat uw Azure-SSIS IR worden gestopt, moet u deze netwerk bronnen niet opnieuw gebruiken in uw andere resources.

    1. Zorg ervoor dat er geen [resource vergrendeling](../azure-resource-manager/management/lock-resources.md) is voor de resource groep/het abonnement waartoe het virtuele netwerk behoort. Als u een alleen-lezen/Verwijder vergrendeling configureert, mislukt het starten en stoppen van de Azure-SSIS IR, of reageert het niet meer.

    1. Zorg ervoor dat u geen Azure-beleid hebt waarmee wordt voor komen dat de volgende resources worden gemaakt onder de resource groep/het abonnement waartoe het virtuele netwerk behoort:
        - Micro soft. Network/LoadBalancers
        - Micro soft. Network/NetworkSecurityGroups

    1. Verkeer op de NSG-regel (netwerk beveiligings groep) toestaan om verkeer tussen door SQL beheerd exemplaar en Azure-SSIS IR, en verkeer dat nodig is voor Azure-SSIS IR toe te staan.
        1. **Inkomende vereiste van SQL Managed instance** om binnenkomend verkeer van Azure-SSIS IR toe te staan.

            | Transport Protocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel | Opmerkingen |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Als uw SQL Database Server-verbindings beleid is ingesteld op **proxy** in plaats van de **omleiding**, is alleen poort 1433 vereist.|

        1. **Uitgaande vereiste van Azure-SSIS IR** om uitgaand verkeer naar SQL Managed instance en ander verkeer dat door Azure-SSIS IR is vereist, toe te staan.

        | Transport Protocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel | Opmerkingen |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Uitgaand verkeer naar SQL beheerd exemplaar toestaan. Als het verbindings beleid is ingesteld op **proxy** in plaats van de **omleiding**, is alleen poort 1433 vereist. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort voor toegang tot Azure-Services, zoals Azure Storage en Azure Event Hubs. |
        | TCP | VirtualNetwork | * | Internet | 80 | Beschrijving De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort om een certificaatintrekkingslijst van Internet te downloaden. Als u dit verkeer blokkeert, kan dit leiden tot prestatie downgrade bij het starten van IR en verliest u de mogelijkheid om de intrekkings lijst voor certificaten te controleren op het gebruik van certificaten. Zie [Azure ExpressRoute of door de gebruiker gedefinieerde route (UDR) gebruiken](./join-azure-ssis-integration-runtime-virtual-network.md#route)als u de bestemming verder wilt beperken tot bepaalde FQDN-verwijzingen.|
        | TCP | VirtualNetwork | * | Storage | 445 | Beschrijving Deze regel is alleen vereist als u SSIS-pakket wilt uitvoeren dat is opgeslagen in Azure Files. |
        |||||||

        1. **Binnenkomende vereiste van Azure-SSIS IR** om verkeer toe te staan dat door Azure-SSIS IR wordt vereist.

        | Transport Protocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel | Opmerkingen |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (als u de IR koppelt aan een virtueel netwerk van Resource Manager) <br/><br/>10100, 20100, 30100 (als u de IR koppelt aan een klassiek virtueel netwerk)| De Data Factory-service gebruikt deze poorten om te communiceren met de knoop punten van uw Azure-SSIS IR in het virtuele netwerk. <br/><br/> Of u nu een NSG op subnetniveau maakt, Data Factory altijd een NSG configureert op het niveau van de netwerk interface kaarten (Nic's) die zijn gekoppeld aan de virtuele machines die de Azure-SSIS IR hosten. Alleen binnenkomend verkeer van Data Factory IP-adressen op de opgegeven poorten is toegestaan door deze NSG op NIC-niveau. Zelfs als u deze poorten opent op het Internet verkeer op subnetniveau, wordt verkeer van IP-adressen die niet Data Factory IP-adressen worden geblokkeerd op het niveau van de NIC. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Beschrijving Deze regel is alleen vereist wanneer micro soft-ondersteunings team klanten vraagt om te openen voor geavanceerde probleem oplossing en direct kan worden gesloten na het oplossen van het probleem. De **CorpNetSaw** -service code staat alleen beveiligde toegang op werk stations in het bedrijfs netwerk van micro soft toe om extern bureau blad te gebruiken. En deze servicetag kan niet worden geselecteerd vanuit de portal en is alleen beschikbaar via Azure PowerShell of Azure CLI. <br/><br/> Op NIC-niveau NSG is poort 3389 standaard geopend en kunt u poort 3389 op subnetniveau NSG beheren, terwijl Azure-SSIS IR niet is toegestaan poort 3389 uitgaande van de Windows Firewall regel op elk IR-knoop punt voor beveiliging. |
        |||||||

    1. Zie [configuratie van het virtuele netwerk](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) voor meer informatie:
        - Als u uw eigen open bare IP-adressen voor de Azure-SSIS IR
        - Als u uw eigen Domain Name System (DNS)-server gebruikt
        - Als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt
        - Als u aangepaste Azure-SSIS IR gebruikt

### <a name="provision-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime inrichten

1. Selecteer een privé-eind punt of open bare eind punt voor het beheerde exemplaar van SQL.

    Gebruik bij het [inrichten van Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) in de Azure Portal/ADF-app op de pagina SQL-instellingen het **persoonlijke eind punt** van het SQL beheerde exemplaar of het **open bare eind punt** bij het maken van een SSIS-catalogus (SSISDB).

    De hostnaam van het open bare eind punt bevat de indeling <mi_name>. public. <dns_zone>. database.windows.net en dat de poort die voor de verbinding wordt gebruikt, 3342 is.  

    ![In de scherm opname wordt de installatie van de Integration runtime weer gegeven met Create S S I S-catalogus geselecteerd en het eind punt van de Catalog-database server](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Selecteer Azure AD-verificatie wanneer van toepassing is.

    ![catalogus-openbaar-eind punt](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Zie [Azure AD inschakelen op Azure SQL Managed instance](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)voor meer informatie over het inschakelen van Azure AD-verificatie.

1. Voeg Azure-SSIS IR toe aan het virtuele netwerk wanneer dit van toepassing is.

    Selecteer op de pagina Geavanceerde instellingen de Virtual Network en het subnet dat u wilt toevoegen.
    
    Kies in hetzelfde virtuele netwerk als een door SQL beheerd exemplaar een **ander subnet** dan het SQL Managed instance. 

    Zie [een Azure SSIS Integration runtime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie over het toevoegen van Azure-SSIS IR aan een virtueel netwerk.

    ![Scherm afbeelding toont de geavanceerde instellingen voor Integration runtime Setup, waarin u een virtueel netwerk voor uw runtime kunt selecteren om lid te worden.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Zie [een Azure SSIS Integration runtime in azure Data Factory maken](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime)voor meer informatie over het maken van een Azure-SSIS IR.

## <a name="clean-up-ssisdb-logs"></a>SSISDB-logboeken opschonen

Het Bewaar beleid voor SSISDB-Logboeken wordt door onderstaande eigenschappen in [Catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database)gedefinieerd:

- OPERATION_CLEANUP_ENABLED

    Als de waarde TRUE is, worden de bewerkings Details en bewerkings berichten ouder dan RETENTION_WINDOW (dagen) verwijderd uit de catalogus. Wanneer de waarde ONWAAR is, worden alle bewerkings gegevens en bewerkings berichten opgeslagen in de catalogus. Opmerking: een SQL Server taak voert het opschonen van de bewerking uit.

- RETENTION_WINDOW

    Het aantal dagen dat bewerkings Details en bewerkings berichten worden opgeslagen in de catalogus. Als de waarde-1 is, is het Bewaar venster oneindig. Opmerking: als er geen opschoning gewenst is, stelt u OPERATION_CLEANUP_ENABLED in op ONWAAR.

Als u SSISDB-logboeken wilt verwijderen die zich buiten het Bewaar venster bevinden dat door de beheerder is ingesteld, kunt u de opgeslagen procedure activeren `[internal].[cleanup_server_retention_window_exclusive]` . U kunt eventueel de taak uitvoering van SQL Managed instance agent plannen om de opgeslagen procedure te activeren.

## <a name="next-steps"></a>Volgende stappen

- [SSIS-pakketten uitvoeren met Azure SQL Managed instance agent-taak](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Bedrijfs continuïteit en herstel na nood gevallen instellen (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [On-premises SSIS-workloads migreren naar SSIS in ADF](scenario-ssis-migration-overview.md)