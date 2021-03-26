---
title: Veelgestelde vragen over Azure Migrate apparaat
description: Krijg antwoorden op veelgestelde vragen over het Azure Migrate-apparaat.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 059a1888b529487f2b0d17509370897222a20d83
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563018"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate apparaat: veelgestelde vragen

In dit artikel vindt u antwoorden op veelgestelde vragen over het Azure Migrate apparaat. Als u andere vragen hebt, raadpleegt u deze bronnen:

- [Algemene vragen](resources-faq.md) over Azure migrate
- Vragen over de [visualisatie van detectie, beoordeling en afhankelijkheid](common-questions-discovery-assessment.md)
- Vragen over [server migratie](common-questions-server-migration.md)
- Beantwoorde vragen in het [Azure migrate-forum](https://aka.ms/AzureMigrateForum) ontvangen

## <a name="what-is-the-azure-migrate-appliance"></a>Wat is het Azure Migrate apparaat?

Het Azure Migrate apparaat is een licht gewicht apparaat dat door de Azure Migrate: het hulp programma detectie en evaluatie wordt gebruikt om fysieke of virtuele servers te detecteren en te beoordelen van on-premises of een andere cloud. De Azure Migrate: het hulp programma voor server migratie gebruikt ook het apparaat voor de migratie van on-premises servers die in VMware-omgeving worden uitgevoerd.

Hier vindt u meer informatie over het Azure Migrate-apparaat:

- Het apparaat wordt on-premises geïmplementeerd als een fysieke server of een gevirtualiseerde server.
- Het apparaat detecteert on-premises servers en verzendt voortdurend meta gegevens en prestatie gegevens van de server naar Azure Migrate.
- De detectie van apparaten verloopt zonder agent. Er is niets geïnstalleerd op gedetecteerde servers.

Meer [informatie](migrate-appliance.md) over het apparaat.

## <a name="how-can-i-deploy-the-appliance"></a>Hoe kan ik het apparaat implementeren?

Het apparaat kan worden geïmplementeerd met een aantal methoden:

- Het apparaat kan worden geïmplementeerd met een sjabloon voor servers die worden uitgevoerd in de VMware-of Hyper-V-omgeving ([eicellen-sjabloon voor VMware](how-to-set-up-appliance-vmware.md) of [VHD voor hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Als u geen sjabloon wilt gebruiken, kunt u het apparaat voor de VMware-of Hyper-V-omgeving implementeren met behulp van een [Power shell-installatie script](deploy-appliance-script.md).
- In Azure Government moet u het apparaat implementeren met behulp van een Power shell-installatie script. Raadpleeg [hier](deploy-appliance-script-government.md)de stappen voor de implementatie.
- Voor fysieke of gevirtualiseerde servers, on-premises of een andere Cloud, implementeert u het apparaat altijd met behulp van een Power shell-installatie script. Raadpleeg [hier](how-to-set-up-appliance-physical.md)de stappen voor de implementatie.

## <a name="how-does-the-appliance-connect-to-azure"></a>Hoe maakt het apparaat verbinding met Azure?

Het apparaat kan verbinding maken via internet of met behulp van Azure ExpressRoute. 

- Zorg ervoor dat het apparaat verbinding kan maken met deze [Azure-url's](./migrate-appliance.md#url-access). 
- U kunt ExpressRoute gebruiken met micro soft-peering. Open bare peering is afgeschaft en is niet beschikbaar voor nieuwe ExpressRoute-circuits.
- Alleen privé-peering wordt niet ondersteund.


## <a name="does-appliance-analysis-affect-performance"></a>Is de invloed van de apparatuur op de prestaties?

Met de Azure Migrate apparaat worden on-premises servers voortdurend op de prestaties van de prestatie gegevens gemeten. Deze profilering heeft bijna geen invloed op de prestaties van profilede servers.

## <a name="can-i-harden-the-appliance"></a>Kan ik het apparaat harder beharden?

Wanneer u de gedownloade sjabloon gebruikt om het apparaat te maken, kunt u onderdelen (bijvoorbeeld anti virus) aan de sjabloon toevoegen als u de communicatie-en firewall regels die vereist zijn voor het apparaat van de Azure Migrate, hebt geïnstalleerd.

## <a name="what-network-connectivity-is-required"></a>Welke netwerk verbinding is vereist?

Het apparaat moet toegang hebben tot Azure-Url's. [Controleer](migrate-appliance.md#url-access) de lijst met url's.

## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens worden door het apparaat verzameld?

Raadpleeg de volgende artikelen voor informatie over gegevens die het Azure Migrate apparaat op servers verzamelt:

- **Servers in VMware-omgeving**: [Controleer](migrate-appliance.md#collected-data---vmware) verzamelde gegevens.
- **Servers in de Hyper-V-omgeving**: [Controleer](migrate-appliance.md#collected-data---hyper-v) verzamelde gegevens.
- **Fysieke of virtuele servers**:[Controleer](migrate-appliance.md#collected-data---physical) verzamelde gegevens.

## <a name="how-is-data-stored"></a>Hoe worden gegevens opgeslagen?

Gegevens die worden verzameld door het Azure Migrate apparaat, worden opgeslagen op de Azure-locatie waar u het project hebt gemaakt.

Hier vindt u meer informatie over de manier waarop gegevens worden opgeslagen:

- De verzamelde gegevens worden veilig opgeslagen in CosmosDB in een micro soft-abonnement. De gegevens worden verwijderd wanneer u het project verwijdert. Opslag wordt verwerkt door Azure Migrate. U kunt niet specifiek een opslag account voor verzamelde gegevens kiezen.
- Als u [afhankelijkheids visualisatie](concepts-dependency-visualization.md)gebruikt, worden de verzamelde gegevens opgeslagen in een Azure log Analytics-werk ruimte die in uw Azure-abonnement is gemaakt. De gegevens worden verwijderd wanneer u de Log Analytics-werk ruimte in uw abonnement verwijdert.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Hoeveel gegevens worden er geüpload tijdens een doorlopende Profiler?

Het gegevens volume dat naar Azure Migrate wordt verzonden, is afhankelijk van meerdere para meters. Een project met 10 servers (elk met één schijf en één NIC) verzendt bijvoorbeeld ongeveer 50 MB aan gegevens per dag. Deze waarde is bij benadering; de werkelijke waarde varieert, afhankelijk van het aantal gegevens punten voor de schijven en Nic's. Als het aantal servers, schijven of Nic's toeneemt, is de toename van verzonden gegevens niet lineair.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Worden gegevens versleuteld op rest en onderweg?

Ja, voor beide:

- Meta gegevens worden via internet veilig verzonden naar de Azure Migrate-service via HTTPS.
- Meta gegevens worden opgeslagen in een [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) -data base en in [Azure Blob-opslag](../storage/common/storage-service-encryption.md) in een micro soft-abonnement. De meta gegevens zijn versleuteld op rest voor opslag.
- De gegevens voor afhankelijkheids analyse zijn ook versleuteld tijdens de overdracht (door middel van beveiligde HTTPS). Deze wordt opgeslagen in een Log Analytics-werk ruimte in uw abonnement. De gegevens worden versleuteld op rest voor afhankelijkheids analyse.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Hoe maakt het apparaat verbinding met vCenter Server?

In deze stappen wordt beschreven hoe het apparaat verbinding maakt met VMware vCenter Server:

1. Het apparaat maakt verbinding met vCenter Server (poort 443) met behulp van de referenties die u hebt opgegeven bij het instellen van het apparaat.
2. Het apparaat gebruikt VMware PowerCLI om vCenter Server te zoeken om meta gegevens te verzamelen over de servers die worden beheerd door vCenter Server.
3. Het apparaat verzamelt configuratie gegevens over servers (kernen, geheugen, schijven, Nic's) en de prestatie geschiedenis van elke server voor de afgelopen maand.
4. De verzamelde meta gegevens worden verzonden naar de Azure Migrate: hulp programma voor detectie en evaluatie (via internet via HTTPS) voor evaluatie.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Kan het Azure Migrate apparaat verbinding maken met meerdere vCenter-servers?

Nee. Er is een een-op-een-toewijzing tussen een [Azure migrate apparaat](migrate-appliance.md) en vCenter Server. Als u servers op meerdere exemplaren van vCenter Server wilt detecteren, moet u meerdere apparaten implementeren.

## <a name="can-a-project-have-multiple-appliances"></a>Kan een project meerdere toestellen hebben?

Er kunnen meerdere apparaten worden geregistreerd voor een project. Eén apparaat kan echter slechts met één project worden geregistreerd.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Kan de Azure Migrate apparaat/replicatie apparaat verbinding maken met dezelfde vCenter?

Ja. U kunt zowel het Azure Migrate apparaat (gebruikt voor de distributie en de VMware-migratie zonder agent) toevoegen aan dezelfde vCenter-Server (gebruikt voor de migratie op basis van een agent van servers die in VMware worden uitgevoerd). Maar zorg ervoor dat u niet beide apparaten op dezelfde server instelt en die momenteel niet worden ondersteund.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Hoeveel servers kan ik vinden met een apparaat?

U kunt Maxi maal 10.000 servers in de VMware-omgeving detecteren, tot 5.000 servers in de Hyper-V-omgeving en Maxi maal 1000 fysieke servers met één apparaat. Als u meer servers in uw on-premises omgeving hebt, leest u over [het schalen van een Hyper-V-beoordeling](scale-hyper-v-assessment.md), [het schalen van een VMware-evaluatie](scale-vmware-assessment.md)en [het schalen van een fysieke server beoordeling](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Kan ik een apparaat verwijderen?

Het verwijderen van een apparaat uit het project wordt momenteel niet ondersteund.

De enige manier om het apparaat te verwijderen, is door de resource groep te verwijderen die het project bevat dat is gekoppeld aan het apparaat.

Als u echter de resource groep verwijdert, worden ook andere geregistreerde apparaten, de gedetecteerde inventaris, beoordelingen en alle andere Azure-onderdelen in de resource groep die aan het project zijn gekoppeld, verwijderd.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Kan ik het apparaat met een ander abonnement of project gebruiken?

Als u het apparaat met een ander abonnement of project wilt gebruiken, moet u het bestaande apparaat opnieuw configureren door het Power shell-installatie script uit te voeren voor het specifieke scenario (VMware/Hyper-V/fysiek) op het apparaat. Met het script worden de bestaande onderdelen en instellingen van het apparaat opgeruimd voor het implementeren van een nieuw apparaat. Zorg ervoor dat u de browser cache wist voordat u begint met het gebruik van de zojuist geïmplementeerde configuratie Manager voor het apparaat.

U kunt ook een bestaande project sleutel niet opnieuw gebruiken op een opnieuw geconfigureerd apparaat. Zorg ervoor dat u een nieuwe sleutel van het gewenste abonnement/project genereert om de registratie van het apparaat te volt ooien.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Kan ik het apparaat instellen op een virtuele Azure-machine?

Nee. Deze optie wordt momenteel niet ondersteund.

## <a name="can-i-discover-on-an-esxi-host"></a>Kan ik op een ESXi-host ontdekken?

Nee. Als u servers in VMware-omgeving wilt detecteren, moet u beschikken over vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Het apparaat Hoe kan ik bijwerken?

Standaard worden het apparaat en de geïnstalleerde agents automatisch bijgewerkt. Het apparaat controleert elke 24 uur op updates. Updates die niet kunnen worden uitgevoerd. 

Alleen het apparaat en de agents van het apparaat worden bijgewerkt door deze automatische updates. Het besturings systeem wordt niet bijgewerkt door Azure Migrate automatische updates. Windows-updates gebruiken om het besturings systeem up-to-date te houden.

## <a name="can-i-check-agent-health"></a>Kan ik de agent status controleren?

Ja. Ga in de portal naar de pagina **status van agent** voor de Azure migrate: detectie en evaluatie of Azure migrate: hulp programma voor server migratie. Daar kunt u de verbindings status controleren tussen Azure en de detectie-en evaluatie agenten op het apparaat.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Kan ik meerdere Server referenties toevoegen aan VMware-apparaat?

Ja, we ondersteunen nu meerdere Server referenties voor het uitvoeren van software-inventaris (detectie van geïnstalleerde toepassingen), afhankelijkheids analyse zonder agents en detectie van SQL Server instanties en data bases. Meer [informatie](tutorial-discover-vmware.md#provide-server-credentials) over het opgeven van referenties op het configuratie beheer van het apparaat.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Welk type server referenties kan ik toevoegen op het VMware-apparaat?
U kunt referenties voor domein/Windows (niet-domein)/Linux-(non-domain)/SQL Server-verificatie opgeven op het configuratie beheer van het apparaat. Meer [informatie](add-server-credentials.md) over hoe u referenties kunt opgeven en hoe u deze kunt afhandelen.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Welk type SQL Server verbindings eigenschappen worden ondersteund door Azure Migrate voor SQL-detectie?
Azure Migrate versleutelt de communicatie tussen Azure Migrate apparaat en bron SQL Server instanties (waarbij de eigenschap versleutelings verbinding is ingesteld op TRUE). Deze verbindingen worden versleuteld met [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (ingesteld op True). de transportlaag gebruikt SSL om het kanaal te versleutelen en de certificaat keten te omzeilen om de vertrouwens relatie te valideren. De toestel server moet zijn ingesteld om [de basis instantie van het certificaat te vertrouwen](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Als er geen certificaat is ingericht op de server wanneer het wordt gestart, SQL Server genereert een zelfondertekend certificaat dat wordt gebruikt om aanmeldings pakketten te versleutelen. [Meer informatie](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).


## <a name="next-steps"></a>Volgende stappen

Lees het [Azure migrate overzicht](migrate-services-overview.md).