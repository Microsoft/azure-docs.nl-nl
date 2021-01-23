---
title: Hulp programma voor migratie van het platform dat wordt ondersteund.
description: Technisch diep gaande kennis over door het platform ondersteunde migratie van bronnen van het klassieke implementatie model naar Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: tagore
ms.openlocfilehash: bc12d626d8a331981cbbad015b376b826c617209
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735129"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager

> [!IMPORTANT]
> Nu gebruiken we op ongeveer 90% IaaS Vm's [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Vanaf 28 februari 2020 zijn klassieke Vm's afgeschaft en worden ze volledig buiten gebruik gesteld op 1 maart 2023. Meer [informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe dit van invloed is op u](./classic-vm-deprecation.md#how-does-this-affect-me).

Laten we een grondige kennis nemen over het migreren van het klassieke Azure-implementatie model naar het Azure Resource Manager-implementatie model. We kijken naar resources op een resource-en functie niveau om inzicht te krijgen in de manier waarop het Azure-platform bronnen migreert tussen de twee implementatie modellen. Lees voor meer informatie het artikel over service aankondiging: [door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md).


## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>IaaS-resources van het klassieke implementatie model migreren naar Azure Resource Manager
Ten eerste is het belang rijk dat u begrijpt wat het verschil is tussen bewerkingen op het vlak van gegevens vlak en beheer op de IaaS-resources (Infrastructure as a Service).

* Met het *beheer-en besturings vlak* worden de aanroepen beschreven die in het beheer-of besturings vlak staan of de API voor het wijzigen van resources. Bewerkingen zoals het maken van een VM, opnieuw starten van een VM en het bijwerken van een virtueel netwerk met een nieuw subnet beheren de actieve resources. Ze hebben geen directe invloed op het verbinden met de Vm's.
* *Data-vlak* (toepassing) beschrijft de runtime van de toepassing zelf en maakt gebruik van interactie met exemplaren die niet via de Azure API gaan. Als u bijvoorbeeld toegang hebt tot uw website of gegevens uit een actief SQL Server exemplaar of een MongoDB-server, zijn gegevens vlak of toepassings interacties. Andere voor beelden zijn onder andere het kopiëren van een BLOB van een opslag account en het openen van een openbaar IP-adres voor het gebruik van Remote Desktop Protocol (RDP) of Secure Shell (SSH) naar de virtuele machine. Deze bewerkingen zorgen ervoor dat de toepassing uitgevoerd blijft worden bij het berekenen, het netwerken en de opslag.

Het gegevens vlak is hetzelfde als het klassieke implementatie model en Resource Manager-stacks. Het verschil is dat tijdens het migratie proces micro soft de weer gave van de resources uit het klassieke implementatie model vertaalt naar die in de Resource Manager-stack. Als gevolg hiervan moet u nieuwe hulpprogram ma's, Api's en Sdk's gebruiken om uw resources te beheren in de Resource Manager-stack.

![Diagram waarin het verschil tussen het beheer-en besturings vlak en het gegevens vlak wordt weer gegeven](./media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> In sommige scenario's voor migratie stopt het Azure-platform, maakt de toewijzingen ongedaan en start uw virtuele machines opnieuw op. Dit veroorzaakt een korte downtime van het gegevens vlak.
>

## <a name="the-migration-experience"></a>De migratie-ervaring
Voordat u de migratie start:

* Zorg ervoor dat de resources die u wilt migreren geen niet-ondersteunde functies of configuraties gebruiken. Meestal detecteert het platform deze problemen en genereert een fout.
* Als u virtuele machines hebt die zich niet in een virtueel netwerk bevinden, worden ze gestopt en de toewijzing ongedaan gemaakt als onderdeel van de voorbereidings bewerking. Als u het open bare IP-adres niet wilt kwijt raken, kunt u het IP-adres reserveren voordat u de voorbereidings bewerking start. Als de Vm's zich in een virtueel netwerk bevinden, worden ze niet gestopt en wordt de toewijzing ongedaan gemaakt.
* Plan de migratie buiten kantooruren zodat u de tijd hebt om onverwachte fouten die kunnen ontstaan tijdens de migratie op te lossen.
* Download de huidige configuratie van uw virtuele machines met behulp van PowerShell, CLI-opdrachten (opdrachtregelinterface) of REST API's om de validatie eenvoudiger te maken nadat de voorbereidingsstap is voltooid.
* Werk uw Automation-en uitoefening-scripts bij om het Resource Manager-implementatie model te verwerken voordat u de migratie start. Desgewenst kunt u GET-bewerkingen uitvoeren wanneer de resources de status Voorbereid hebben.
* Evalueer de Azure RBAC-beleids regels (op rollen gebaseerd toegangs beheer) die zijn geconfigureerd op de IaaS-resources in het klassieke implementatie model, en plan na de migratie.

De migratie werk stroom is als volgt:

![Diagram waarin de migratie werk stroom wordt weer gegeven](./media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> De bewerkingen die in de volgende secties worden beschreven, zijn alle idempotent. Als er een ander probleem is dan een niet-ondersteunde functie of een configuratie fout, voert u de bewerking voorbereiden, afbreken of door voeren opnieuw uit. Azure probeert de actie opnieuw uit te stellen.
>
>

### <a name="validate"></a>Valideren
De bewerking Valideren is de eerste stap in het migratieproces. Het doel van deze stap is het analyseren van de status van de resources die u wilt migreren in het klassieke implementatie model. De bewerking evalueert of de bronnen kunnen worden gemigreerd (geslaagd of mislukt).

U selecteert het virtuele netwerk of een Cloud service (als deze zich niet in een virtueel netwerk Bewaar) dat u voor migratie wilt valideren. Als de resource niet kan worden gemigreerd, vermeldt Azure de redenen waarom.

#### <a name="checks-not-done-in-the-validate-operation"></a>Controles die niet zijn uitgevoerd in de bewerking validate

De bewerking validate analyseert alleen de status van de resources in het klassieke implementatie model. Het kan controleren op alle fouten en niet-ondersteunde scenario's vanwege verschillende configuraties in het klassieke implementatie model. Het is niet mogelijk om te controleren of er problemen zijn die de Azure Resource Manager stack tijdens de migratie op de bronnen kan opleggen. Deze problemen worden alleen gecontroleerd wanneer de resources trans formatie ondergaan in de volgende stap van de migratie (de bewerking voorbereiden). De volgende tabel bevat alle problemen die niet zijn gecontroleerd in de bewerking validate:


|Netwerk controles niet in de bewerking validate|
|-|
|Een virtueel netwerk met zowel er-als VPN-gateways.|
|Een virtuele netwerk gateway verbinding met een verbroken status.|
|Alle er-circuits zijn vooraf gemigreerd naar Azure Resource Manager-stack.|
|Azure Resource Manager quotum controleert op netwerk bronnen. Bijvoorbeeld: statisch openbaar IP-adres, dynamische open bare Ip's, load balancer, netwerk beveiligings groepen, route tabellen en netwerk interfaces. |
| Alle load balancer regels zijn geldig in de implementatie en het virtuele netwerk. |
| Er zijn conflicterende privé Ip's tussen virtuele machines die niet zijn toegewezen in hetzelfde virtuele netwerk. |

### <a name="prepare"></a>Voorbereiden
De bewerking Voorbereiden is de tweede stap in het migratieproces. Het doel van deze stap is het simuleren van de trans formatie van de IaaS-resources van het klassieke implementatie model naar Resource Manager-resources. De voor bereide bewerking geeft deze kant-en-klaar om u te visualiseren.

> [!NOTE] 
> Uw resources in het klassieke implementatie model worden tijdens deze stap niet gewijzigd. Het is een veilige stap die moet worden uitgevoerd als u de migratie uitvoert. 

U selecteert het virtuele netwerk of de Cloud service (als dit geen virtueel netwerk is) dat u wilt voorbereiden voor migratie.

* Als de resource niet kan worden gemigreerd, stopt Azure het migratie proces en wordt een lijst weer gegeven met de reden waarom de voorbereidings bewerking is mislukt.
* Als de resource kan worden gemigreerd, vergrendelt Azure de beheer bewerkingen voor de resources die worden gemigreerd. U kunt bijvoorbeeld geen gegevensschijf toevoegen aan een virtuele machine die wordt gemigreerd.

Azure start de migratie van meta gegevens van het klassieke implementatie model naar Resource Manager voor de migratie van resources.

Nadat de voor bereiding is voltooid, hebt u de mogelijkheid om de resources te visualiseren in het klassieke implementatie model en in Resource Manager. Voor elke cloudservice in het klassieke implementatiemodel maakt het Azure-platform een resourcegroepnaam met de indeling `cloud-service-name>-Migrated`.

> [!NOTE]
> Het is niet mogelijk om de naam te selecteren van een resource groep die is gemaakt voor gemigreerde resources (dat wil zeggen, "-gemigreerd"). Nadat de migratie is voltooid, kunt u de functie verplaatsen van Azure Resource Manager echter gebruiken om resources te verplaatsen naar een resource groep die u wilt. Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Met de volgende twee scherm afbeeldingen wordt het resultaat weer gegeven na een geslaagde voor bereiding. De eerste bevat een resource groep die de oorspronkelijke Cloud service bevat. De tweede bevat de nieuwe '-gemigreerde ' resource groep die de equivalente Azure Resource Manager resources bevat.

![Scherm afbeelding waarin de oorspronkelijke Cloud service wordt weer gegeven](./media/migration-classic-resource-manager/portal-classic.png)

![Scherm opname van Azure Resource Manager resources in de voorbereidings bewerking](./media/migration-classic-resource-manager/portal-arm.png)

Hier ziet u een voor achtergrond van uw resources na het volt ooien van de voorbereidings fase. Houd er rekening mee dat de resource in het gegevens vlak hetzelfde is. Deze wordt weer gegeven in het beheer vlak (klassiek implementatie model) en het besturings vlak (Resource Manager).

![Diagram van de voorbereidings fase](./media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Vm's die zich niet in een virtueel netwerk in het klassieke implementatie model bevinden, worden in deze fase van de migratie gestopt en opgeheven.
>

### <a name="check-manual-or-scripted"></a>Controleren (handmatig of gepland)
In de stap controleren hebt u de optie om de configuratie te gebruiken die u eerder hebt gedownload om te controleren of de migratie correct lijkt. U kunt zich ook aanmelden bij de portal en de eigenschappen en resources controleren om te valideren of de migratie van de meta gegevens goed lijkt.

Als u een virtueel netwerk migreert, wordt de configuratie van virtuele machines meestal niet opnieuw opgestart. Voor toepassingen op die virtuele machines kunt u valideren of de toepassing nog wordt uitgevoerd.

U kunt uw bewakings-en operationele scripts testen om te zien of de virtuele machines werken zoals verwacht en of uw bijgewerkte scripts correct werken. Alleen GET-bewerkingen worden ondersteund wanneer de resources de status Voorbereid hebben.

Er is geen set voor het instellen van de tijd die nodig is voor het door voeren van de migratie. U kunt zoveel tijd nemen als u wilt in deze fase. Het beheervlak is echter wel vergrendeld voor deze resources totdat u de migratie afbreekt of doorvoert.

Als er problemen zijn, kunt u altijd de migratie altijd afbreken en terugkeren naar het klassieke implementatiemodel. Wanneer u terugkeert, opent Azure de bewerkingen op het beheer vlak voor de resources, zodat u normale bewerkingen op deze Vm's in het klassieke implementatie model kunt hervatten.

### <a name="abort"></a>Afbreken
Dit is een optionele stap als u de wijzigingen in het klassieke implementatie model wilt terugdraaien en de migratie wilt stoppen. Met deze bewerking worden de meta gegevens van Resource Manager (gemaakt in de stap voorbereiden) voor uw resources verwijderd. 

![Diagram van de stap afbreken](media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Deze bewerking kan niet worden uitgevoerd nadat u de doorvoer bewerking hebt geactiveerd.     
>

### <a name="commit"></a>Doorvoeren
Nadat de validatie is voltooid, kunt u de migratie doorvoeren. Resources worden niet meer weer gegeven in het klassieke implementatie model en zijn alleen beschikbaar in het Resource Manager-implementatie model. De gemigreerde resources kunnen alleen in de nieuwe portal worden beheerd.

> [!NOTE]
> Dit is een idempotente bewerking. Als dit mislukt, voert u de bewerking opnieuw uit. Als het probleem blijft optreden, maakt u een ondersteunings ticket of maakt u een forum op [micro soft Q&a](/answers/index.html)
>
>

![Diagram van commit-stap](media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Migratie stroomdiagram

Hier volgt een stroom diagram dat laat zien hoe u kunt door gaan met de migratie:

![Schermafbeelding van de migratiestappen](media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Vertaling van het klassieke implementatie model naar Resource Manager-resources
U vindt het klassieke implementatie model en de Resource Manager-weer gave van de resources in de volgende tabel. Andere functies en resources worden momenteel niet ondersteund.

| Klassieke weergave | Weergave van de Resource Manager | Notities |
| --- | --- | --- |
| Naam van Cloud service (gehoste service naam) |DNS-naam |Tijdens de migratie wordt een nieuwe resourcegroep gemaakt voor elke cloudservice met het naamgevingspatroon `<cloudservicename>-migrated`. Deze resourcegroep bevat al uw resources. De naam van de cloudservice wordt een DNS-naam die is gekoppeld aan het openbare IP-adres. |
| Virtuele machine |Virtuele machine |VM-specifieke eigenschappen worden ongewijzigd gemigreerd. Bepaalde osProfile-gegevens, zoals computer naam, worden niet opgeslagen in het klassieke implementatie model en blijven leeg na de migratie. |
| Schijfresources die zijn gekoppeld aan de VM |Impliciete schijven die zijn gekoppeld aan de VM |Schijven worden niet gemodelleerd als resources op het hoogste niveau in het Resource Manager-implementatiemodel. Ze worden gemigreerd als impliciete schijven onder de virtuele machine. Alleen de schijven die zijn gekoppeld aan een virtuele machine worden momenteel ondersteund. Resource Manager-Vm's kunnen nu opslag accounts gebruiken in het klassieke implementatie model, waardoor de schijven eenvoudig kunnen worden gemigreerd zonder dat er updates zijn. |
| VM-extensies |VM-extensies |Alle de resource-extensies, met uitzondering van XML-extensies, worden gemigreerd vanuit het klassieke implementatiemodel. |
| Certificaten van virtuele machine |Certificaten in Azure Key Vault |Als een Cloud service service certificaten bevat, maakt de migratie een nieuwe Azure-sleutel kluis per Cloud service en verplaatst de certificaten naar de sleutel kluis. De virtuele machines worden bijgewerkt zodat ze verwijzen naar de certificaten van de sleutelkluis. <br><br> Verwijder de sleutel kluis niet. Dit kan ervoor zorgen dat de VM de status Mislukt heeft. |
| WinRM-configuratie |WinRM-configuratie in osProfile |De Windows Remote Management-configuratie wordt ongewijzigd verplaatst als onderdeel van de migratie. |
| Eigenschap van beschikbaarheidsset |Resource van beschikbaarheidsset | Specificatie van Beschik baarheid-set is een eigenschap op de virtuele machine in het klassieke implementatie model. Beschikbaarheidssets worden een resource op het hoogste niveau als onderdeel van de migratie. De volgende configuraties worden niet ondersteund: meerdere beschikbaarheidssets per cloudservice of één of meer beschikbaarheidssets samen met de virtuele machines die zich niet in een beschikbaarheidsset in een cloudservice bevinden. |
| Netwerkconfiguratie op een virtuele machine |Primaire netwerkinterface |De netwerkconfiguratie op een virtuele machine wordt weergegeven als de resource primaire netwerkinterface na de migratie. Het interne IP-adres van virtuele machines die zich niet in een virtueel netwerk bevinden, wordt gewijzigd tijdens de migratie. |
| Meerdere netwerkinterfaces in een VM |Netwerkinterfaces |Als aan een virtuele machine meerdere netwerk interfaces zijn gekoppeld, wordt elke netwerk interface een resource op het hoogste niveau als onderdeel van de migratie, samen met alle eigenschappen. |
| Eindpuntset met gelijke taakverdeling |Load balancer |In het klassieke implementatiemodel wees het platform een impliciete load balancer toe aan elke cloudservice. Tijdens de migratie wordt een nieuwe load-balancerresource gemaakt. De eindpuntset met gelijke taakverdeling wordt omgezet in load-balancerregels. |
| Inkomende NAT-regels |Inkomende NAT-regels |Invoereindpunten die zijn gedefinieerd op de virtuele machine worden tijdens de migratie geconverteerd naar binnenkomende vertaalregels voor netwerkadressen onder de load balancer. |
| VIP-adres |Openbaar IP-adres met DNS-naam |Het virtuele IP-adres wordt een openbaar IP-adres en is gekoppeld aan de load balancer. Een virtueel IP-adres kan alleen worden gemigreerd als er een invoereindpunt aan is toegewezen. |
| Virtueel netwerk |Virtueel netwerk |Het virtuele netwerk wordt met alle eigenschappen gemigreerd naar het Resource Manager-implementatiemodel. Er wordt een nieuwe resourcegroep gemaakt met de naam `-migrated`. |
| Gereserveerde IP-adressen |Openbaar IP-adres met een statische toewijzingsmethode |Gereserveerd IP-adressen die zijn gekoppeld aan de load balancer worden gemigreerd, samen met de migratie van de cloudservice of de virtuele machine. Niet-gekoppelde gereserveerde Ip's kunnen worden gemigreerd met behulp van [Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip).  |
| Openbaar IP-adres per VM |Openbaar IP-adres met een dynamische toewijzingsmethode |Het openbare IP-adres dat is gekoppeld aan de virtuele machine wordt geconverteerd naar een openbare IP-adresresource. De toewijzingsmethode wordt ingesteld op statisch. |
| NSG's |NSG's |Netwerkbeveiligingsgroepen die zijn gekoppeld aan een subnet worden gekloond als onderdeel van de migratie naar het Resource Manager-implementatiemodel. De NSG in het klassieke implementatiemodel wordt niet verwijderd tijdens de migratie. De bewerkingen op het beheervlak voor de NSG worden echter geblokkeerd wanneer de migratie wordt uitgevoerd. Niet-gekoppelde Nsg's kunnen worden gemigreerd met behulp van [Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup).|
| DNS-servers |DNS-servers |DNS-servers die zijn gekoppeld aan een virtueel netwerk of de virtuele machine worden gemigreerd als onderdeel van de bijbehorende resourcemigratie, samen met alle eigenschappen. |
| UDR's |UDR's |Door de gebruiker gedefinieerde routes die zijn gekoppeld aan een subnet worden gekloond als onderdeel van de migratie naar het Resource Manager-implementatiemodel. De UDR in het klassieke implementatiemodel wordt niet verwijderd tijdens de migratie. De bewerkingen op het beheervlak voor de UDR worden geblokkeerd wanneer de migratie wordt uitgevoerd. Niet-gekoppelde Udr's kunnen worden gemigreerd met behulp van [Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable). |
| De eigenschap Doorsturen via IP op de netwerkconfiguratie van een virtuele machine |De eigenschap Doorsturen via IP op de NIC |De eigenschap Doorsturen via IP op een virtuele machine wordt tijdens de migratie geconverteerd naar een eigenschap op de netwerkinterface. |
| Load balancer met meerdere IP-adressen |Load balancer met meerdere openbare IP-resources |Elk openbaar IP-adres dat is gekoppeld aan de load balancer, wordt geconverteerd naar een open bare IP-resource en gekoppeld aan de load balancer na de migratie. |
| Interne DNS-namen op de virtuele machine |Interne DNS-namen op de NIC |Tijdens de migratie worden de interne DNS-achtervoegsels voor de virtuele machines gemigreerd naar een alleen-lezeneigenschap met de naam 'InternalDomainNameSuffix' op de NIC. Het achtervoegsel blijft ongewijzigd na de migratie en de VM-oplossing moet eerder blijven werken. |
| Gateway van een virtueel netwerk |Gateway van een virtueel netwerk |De eigenschappen van de virtuele netwerk gateway worden ongewijzigd gemigreerd. Het VIP dat is gekoppeld aan de gateway wordt ook niet gewijzigd. |
| Lokale netwerksite |Lokale netwerkgateway |De eigenschappen van de lokale netwerk site worden ongewijzigd gemigreerd naar een nieuwe bron met de naam een lokale netwerk gateway. Dit vertegenwoordigt on-premises adres voorvoegsels en de externe gateway-IP. |
| Verwijzingen naar verbindingen |Verbinding |Verbindings referenties tussen de gateway en de lokale netwerk site in de netwerk configuratie worden aangeduid met een nieuwe resource met de naam verbinding. Alle eigenschappen van de connectiviteits verwijzing in netwerk configuratie bestanden worden ongewijzigd naar de verbindings bron gekopieerd. De connectiviteit tussen virtuele netwerken in het klassieke implementatie model wordt bereikt door twee IPsec-tunnels te maken voor lokale netwerk sites die de virtuele netwerken vertegenwoordigen. Dit wordt getransformeerd naar het verbindings type virtueel-netwerk-naar-virtueel-netwerk in het Resource Manager-model, zonder dat hiervoor lokale netwerk gateways zijn vereist. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Wijzigingen aanbrengen in uw automatisering en hulpprogramma’s na de migratie
Als onderdeel van het migreren van uw resources vanuit het klassieke implementatie model naar het Resource Manager-implementatie model, moet u uw bestaande automatisering of hulpprogram ma's bijwerken om ervoor te zorgen dat deze na de migratie blijft werken.


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Power shell gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [CLI gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Migratie van klassieke en Resource Manager VPN Gateway](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [ExpressRoute-circuits en gekoppelde virtuele netwerken van het klassieke naar het Resource Manager-implementatie model migreren](../expressroute/expressroute-migration-classic-resource-manager.md)
* [Community tools voor hulp bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md)
* [Bekijk de veelgestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md)
