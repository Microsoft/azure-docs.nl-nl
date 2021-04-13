---
title: Azure Automanage voor virtuele machines
description: Meer informatie Azure Automanage voor virtuele machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 514f1af2a1b120254840986fc5ceb803dfc24345
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363373"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage voor virtuele machines

Dit artikel bevat informatie over Azure Automanage virtuele machines, die de volgende voordelen hebben:

- Virtuele machines intelligent onboarden om best practices voor Azure-services te selecteren
- Elke service wordt automatisch geconfigureerd volgens de best practices van Azure
- Controleert op drift en corrigeert deze wanneer deze wordt gedetecteerd
- Biedt een eenvoudige ervaring (punt, klik, instellen, vergeten)


## <a name="overview"></a>Overzicht

Azure Automanage voor virtuele machines is een service die de noodzaak om te ontdekken, te leren onboarden en te configureren van bepaalde services in Azure elimineert die uw virtuele machine ten goede komen. Deze services worden beschouwd als best practices van Azure en helpen de betrouwbaarheid, beveiliging en het beheer van virtuele machines te verbeteren. Voorbeeldservices zijn [Azure Updatebeheer](../automation/update-management/overview.md) en [Azure Backup.](../backup/backup-overview.md)

Na het onboarden van uw virtuele machines Azure Automanage, wordt best practice service geconfigureerd met de aanbevolen instellingen. Best practices verschillen voor elk van de services. Een voorbeeld hiervan is Azure Backup, waarbij het best practice is om één keer per dag een back-up van de virtuele machine te maken en een bewaarperiode van zes maanden te hebben.

Azure Automanage controleert ook automatisch op drift en corrigeert deze wanneer deze wordt gedetecteerd. Dit betekent dat als de onboarding van uw virtuele machine naar Azure Automanage is uitgevoerd, we deze niet alleen configureren volgens de best practices van Azure, maar ook uw machine bewaken om ervoor te zorgen dat deze gedurende de hele levenscyclus blijft voldoen aan deze best practices. Als uw virtuele machine afwijkt van deze procedures (bijvoorbeeld als een service is ge-offboarded), wordt deze gecorrigeerd en wordt de machine weer in de gewenste status opgeslagen.

## <a name="prerequisites"></a>Vereisten

Er zijn verschillende vereisten waarmee u rekening moet houden voordat u de Azure Automanage virtuele machines inschakelen.

- Ondersteunde [Versies van Windows Server en](automanage-windows-server.md#supported-windows-server-versions) [Linux-distributies](automanage-linux.md#supported-linux-distributions-and-versions)
- VM's moeten zich in een ondersteunde regio (zie hieronder)
- De gebruiker moet de juiste machtigingen hebben (zie hieronder)
- Automanage biedt op dit moment geen ondersteuning voor Sandbox-abonnementen

### <a name="supported-regions"></a>Ondersteunde regio’s
Automanage ondersteunt alleen VM's in de volgende regio's:
* Europa -west
* Europa - noord
* VS - centraal
* VS - oost
* VS - oost 2
* VS - west
* VS - west 2
* Canada - midden
* VS - west-centraal
* VS - zuid-centraal
* Japan East
* Verenigd Koninkrijk Zuid
* AU - oost
* Australië - zuidoost
* Azië - zuidoost

### <a name="required-rbac-permissions"></a>Vereiste RBAC-machtigingen
Uw account heeft iets andere RBAC-rollen nodig, afhankelijk van het inschakelen van Automanage met een nieuw Automanage-account.

Als u Automanage inschakelen met een nieuw Automanage-account:
* **De** rol eigenaar van de abonnementen die uw VM's bevatten, _**of**_
* **De** rollen **Inzender en Gebruikerstoegangbeheerder** voor de abonnementen die uw VM's bevatten

Als u Automanage inschakelen met een bestaand Automanage-account:
* **De** rol Inzender voor de resourcegroep die uw VM's bevat

Aan het Automanage-account worden de machtigingen **Inzender** en **Inzender voor resourcebeleid** verleend om acties uit te voeren op automatisch beheerde machines.

> [!NOTE]
> Als u Automatisch beheren wilt gebruiken op een VM die is verbonden met een werkruimte in een ander abonnement, moet u de hierboven beschreven machtigingen voor elk abonnement hebben.

## <a name="participating-services"></a>Deelnemende services

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Intelligente onboarding van services.":::

Zie de volgende informatie voor de volledige lijst met deelnemende Azure-services en hun ondersteunde omgeving:
- [Automanage voor Linux](automanage-linux.md)
- [Automatisch beheren voor Windows Server](automanage-windows-server.md)

 We onboarden u automatisch voor deze deelnemende services. Ze zijn essentieel voor onze whitepaper met best practices, die u kunt vinden in onze [Cloud Adoption Framework.](/azure/cloud-adoption-framework/manage/azure-server-management)

Voor al deze services wordt automatisch onboarden, automatisch geconfigureerd, gecontroleerd op drift en gemediateerd als er drift wordt gedetecteerd.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Automanage inschakelen voor VM's in Azure Portal

In de Azure Portal kunt u Automatischmanage inschakelen op een bestaande virtuele machine of wanneer u een nieuwe virtuele machine maakt. Raadpleeg de [quickstart Automanage voor virtuele machines](quick-create-virtual-machines-portal.md)voor beknopte stappen voor dit proces.

Als het de eerste keer is dat u Automanage inschakelen voor uw VM, kunt u in de Azure Portal zoeken naar De best practices voor virtuele **Azure-machines.** Klik op Inschakelen op bestaande **VM,** selecteer de VM's die u wilt onboarden, klik op **Selecteren,** klik op **Inschakelen** en u bent klaar.

De enige tijd die u mogelijk nodig hebt om met deze VM te communiceren om deze services te beheren, is in het geval we hebben geprobeerd uw VM te herstellen, maar dit is mislukt. Als we uw VM hebben kunnen herstellen, brengen we deze weer in overeenstemming zonder u te waarschuwen. Zie Status van [VM's voor meer informatie.](#status-of-vms)

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Automanage inschakelen voor VM's met Azure Policy
U kunt automanage ook op VM's op schaal inschakelen met behulp van de ingebouwde Azure Policy. Het beleid heeft een DeployIfNotExists-effect, wat betekent dat alle in aanmerking komende VM's die zich binnen het bereik van het beleid bevinden, automatisch worden onboarding voor best practices voor virtuele VM's automatisch worden geïmplementeerd.

U kunt hier een directe koppeling naar het [beleid maken.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)

### <a name="how-to-apply-the-policy"></a>Het beleid toepassen
1. Klik op de **knop Toewijzen** wanneer u de beleidsdefinitie bekijkt
1. Selecteer het bereik waarop u het beleid wilt toepassen (kan beheergroep, abonnement of resourcegroep zijn)
1. Geef **onder Parameters** parameters op voor het Automanage-account, Configuratieprofiel en Effect (het effect moet meestal DeployIfNotExists zijn)
    1. Als u geen Automanage-account hebt, moet u er [een maken.](./automanage-account.md)
1. Onder **Herstel, selectievakje**"Klik op een hersteltaak" selectievakje. Hiermee wordt onboarding naar Automanage uitvoeren.
1. Klik **op Controleren en maken** en zorg ervoor dat alle instellingen er goed uitzien.
1. Klik op **Create**.

## <a name="environment-configuration"></a>Omgevingsconfiguratie

Wanneer u Automanage inschakelen voor uw virtuele machine, is een omgeving vereist. Omgevingen vormen de basis van deze service. Ze definiëren welke services we onboarden voor uw machines en tot op zekere hoogte wat de configuratie van deze services zou zijn.

### <a name="default-environments"></a>Standaardomgevingen

Er zijn momenteel twee omgevingen beschikbaar.

- **Dev/Test-omgeving** is ontworpen voor Dev/Test-machines.
- **De** productieomgeving is voor productie.

De reden voor deze differentiator is dat bepaalde services worden aanbevolen op basis van de workload die wordt uitgevoerd. In een productiemachine onboarden we u bijvoorbeeld automatisch naar Azure Backup. Voor een Dev/Test-machine zou een back-upservice echter onnodige kosten met zich mee te maken hebben, omdat Dev/Test-machines doorgaans minder bedrijfsimpact hebben.

### <a name="customizing-an-environment-using-preferences"></a>Een omgeving aanpassen met behulp van voorkeuren

Naast de standaardservices waarmee we u onboarden, kunt u een bepaalde subset van voorkeuren configureren. Deze voorkeuren zijn toegestaan binnen een bereik van configuratieopties. In het geval van een Azure Backup kunt u bijvoorbeeld de frequentie van de back-up definiëren en op welke dag van de week deze plaatsvindt.

> [!NOTE]
> In de Dev/Test-omgeving maken we helemaal geen back-up van de VM.

U kunt de instellingen van een standaardomgeving aanpassen via voorkeuren. Meer informatie over het maken van een [voorkeur hier.](virtual-machines-custom-preferences.md)

> [!NOTE]
> U kunt de configuratie van de inschrijving op uw VM niet wijzigen terwijl Automanage is ingeschakeld. U moet Automanage uitschakelen voor die VM en vervolgens Automatischmanage opnieuw inschakelen met de gewenste omgeving en voorkeuren.

Zie hier voor de volledige lijst met deelnemende Azure-services en als deze ondersteuning bieden voor voorkeuren:
- [Automanage voor Linux](automanage-windows-server.md)
- [Automatisch beheren voor Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Automanage-account

Het Automanage-account is de beveiligingscontext of de identiteit waaronder de geautomatiseerde bewerkingen worden uitgevoerd. Normaal gesproken is de optie Account automatisch beheren niet nodig om te selecteren, maar als er een delegeringsscenario is waarin u het geautomatiseerde beheer van uw resources wilt verdelen (mogelijk tussen twee systeembeheerders), kunt u met de optie Account automatisch beheren in de inschakelenstroom een Azure-identiteit definiëren voor elk van deze beheerders.

Ga naar het document Automanage Account voor meer informatie over het [Automanage-account](./automanage-account.md)en het maken van een account.

## <a name="status-of-vms"></a>Status van VM's

Ga in Azure Portal naar de pagina Met best practices voor virtuele **Azure-machines automanage – Azure** waarop al uw automatisch beheerde VM's worden vermeld. Hier ziet u de algemene status van elke virtuele machine.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lijst met geconfigureerde virtuele machines.":::

Voor elke vermelde VM worden de volgende gegevens weergegeven: Naam, Omgeving, Configuratievoorkeur, Status, Besturingssysteem, Account, Abonnement en Resourcegroep.

In **de kolom Status** kunnen de volgende statussen worden weergegeven:
- *In uitvoering:* de VM is zojuist ingeschakeld en wordt geconfigureerd
- *Geconfigureerd:* de VM is geconfigureerd en er wordt geen afwijking gedetecteerd
- *Mislukt:* de VM is gedrift en kan niet worden herstellen
- *In* behandeling: de VM wordt momenteel niet uitgevoerd en Automanage probeert de VM te onboarden of te herstellen wanneer deze de volgende keer wordt uitgevoerd

Als u de **status Mislukt** *ziet,* kunt u problemen met de implementatie oplossen via de resourcegroep waarin uw VM zich bevindt. Ga naar **Resourcegroepen,** selecteer uw resourcegroep, klik op **Implementaties** en bekijk de *status* Mislukt, samen met foutdetails.


## <a name="disabling-automanage-for-vms"></a>Automanage voor VM's uitschakelen

U kunt op een dag besluiten om Automanage op bepaalde VM's uit te schakelen. Op uw computer wordt bijvoorbeeld een supergevoelige beveiligde workload uitgevoerd en moet u deze nog verder vergrendelen dan azure op een natuurlijke manier zou hebben gedaan. Daarom moet u de machine configureren buiten de best practices van Azure.

Als u dit wilt doen in de Azure Portal, gaat u naar de pagina Best practices voor virtuele **Azure-machines** automatisch beheer met een overzicht van al uw automatisch beheerde VM's. Schakel het selectievakje in naast de virtuele machine die u wilt uitschakelen in Automanage en klik vervolgens op de knop **Automatischemanagment uitschakelen.**

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Automanage uitschakelen op een virtuele machine.":::

Lees aandachtig door de berichten in het pop-upvenster voordat u akkoord gaat met het **uitschakelen**.

> [!NOTE]
> Het uitschakelen van automatisch beheer in een VM resulteert in het volgende gedrag:
>
> - De configuratie van de VM en de services die worden onboarden om niet te wijzigen.
> - Alle kosten die door deze services worden gemaakt, blijven factureerbaar en worden nog steeds in rekening gebracht.
> - Controle van drift automatisch wordt onmiddellijk gestopt.


Ten eerste zullen we de virtuele machine niet off-boarden van een van de services waar we de onboarding voor hebben gemaakt en geconfigureerd. Alle kosten die door deze services worden gemaakt, blijven dus factureerbaar. U moet indien nodig off-boarden. Automatisch gedrag wordt onmiddellijk gestopt. We controleren bijvoorbeeld de VM niet langer op drift.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd dat Automanage voor virtuele machines een manier biedt waarmee u de noodzaak voor u om te weten te komen, onboarden naar en configureren van best practices voor Azure-services kunt elimineren. Als een machine die u hebt ge onboardd voor Automanage voor virtuele machines afdrijf van de installatie van de omgeving, wordt deze automatisch weer conform gemaakt.

Probeer Automanage in te stellen voor virtuele machines in Azure Portal.

> [!div class="nextstepaction"]
> [Automanage inschakelen voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)