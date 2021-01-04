---
title: Azure automanage voor virtuele machines
description: Meer informatie over Azure automanage voor virtuele machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: ab056e0685264b03d35ee6b95afad7c6362f9db6
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695796"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure automanage voor virtuele machines

In dit artikel wordt informatie behandeld over Azure automanage voor virtuele machines, die de volgende voor delen hebben:

- Virtuele machines op intelligente wijze voorbereiden om aanbevolen procedures voor Azure-Services te selecteren
- Configureert automatisch elke service per aanbevolen procedures van Azure
- Controleert op drift en corrigeert deze wanneer ze zijn gedetecteerd
- Biedt een eenvoudige ervaring (punt, klik, instellen, verg eten)


## <a name="overview"></a>Overzicht

Azure automanage voor virtuele machines is een service waarmee u niet hoeft te ontdekken, weet hoe u kunt uitkomen en hoe u bepaalde services in azure kunt configureren die voor deel van uw virtuele machine zijn. Met deze services kunt u de betrouw baarheid, de beveiliging en het beheer van virtuele machines verbeteren en worden beschouwd als Services voor aanbevolen procedures van Azure, zoals [azure updatebeheer](../automation/update-management/overview.md) en [Azure backup](../backup/backup-overview.md) -slechts een van de namen.

Nadat u uw virtuele machines hebt voor het automatisch beheren van Azure, wordt elke best practice-service op de aanbevolen instellingen geconfigureerd. Aanbevolen procedures verschillen voor elk van de services. Een voor beeld is mogelijk Azure Backup, waarbij de best practice mogelijk eenmaal per dag een back-up van de virtuele machine maakt en een Bewaar periode van zes maanden hebben.

Met Azure auto Manage wordt ook automatisch gecontroleerd op drift en wordt deze gecorrigeerd wanneer ze worden gedetecteerd. Dit betekent dat als uw virtuele machine onboarded is voor Azure automanage, deze niet alleen worden geconfigureerd per Azure best practices, maar we controleren uw machine om te garanderen dat deze de best practices in de hele levens cyclus blijft naleven. Als uw virtuele machine een drift of afwijking van deze procedures heeft, wordt deze gecorrigeerd en wordt de computer weer in de gewenste staat gebracht.

Ten slotte is de ervaring erg eenvoudig.


## <a name="prerequisites"></a>Vereisten

Er zijn verschillende vereisten die u moet overwegen voordat u Azure automanage op uw virtuele machines inschakelt.

- Alleen Windows Server Vm's
- Vm's moeten worden uitgevoerd
- Vm's moeten zich in een ondersteunde regio bevinden
- De gebruiker moet over de juiste machtigingen beschikken (Zie de onderstaande alinea)
- Automanage biedt momenteel geen ondersteuning voor sandbox-abonnementen

U moet de rol **Inzender** hebben voor de resource groep die uw vm's bevat om het gebruik van een bestaande automanage-account in te scha kelen op vm's. Als u automanage inschakelt met een nieuw automanage-account, hebt u de volgende machtigingen nodig voor uw abonnement: rol van **eigenaar** of **Inzender** samen met beheerders rollen van **gebruikers toegang** . 

> [!NOTE]
> Als u automanage wilt gebruiken op een virtuele machine die is verbonden met een werk ruimte in een ander abonnement, moet u de hiervoor vermelde machtigingen hebben voor elk abonnement.

Het is ook belang rijk te weten dat automanage alleen virtuele Windows-machines ondersteunt die zich in de volgende regio's bevinden: Europa-west, VS-Oost, VS-West 2, Canada-centraal, West-Centraal VS, Japan-Oost.

## <a name="participating-services"></a>Deelnemende Services

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="Services op intelligente wijze voorbereiden.":::

Zie [Azure automanage voor virtual machines best practices](virtual-machines-best-practices.md) voor de volledige lijst met deelnemende Azure-Services, evenals hun ondersteunde configuratie profielen.

 U wordt automatisch geadviseerd voor deze deelnemende Services. Ze zijn essentieel voor het technische document van best practices, dat u kunt vinden in ons [Cloud adoptie Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Voor al deze services worden automatisch de voor-en onderhouds-en berekenings-en berekenings-en herstel bewerking voor het detecteren van drift gedetecteerd.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Automanage inschakelen voor Vm's in Azure Portal

In de Azure Portal kunt u automatisch beheer inschakelen op een bestaande virtuele machine of wanneer u een nieuwe virtuele machine maakt. Bekijk de Snelstartgids voor het [autobeheren van virtuele machines](quick-create-virtual-machines-portal.md)voor beknopte stappen voor dit proces.

Als dit de eerste keer is dat u automatisch beheer voor uw virtuele machine inschakelt, kunt u zoeken in de Azure Portal voor de aanbevolen procedures voor het automatisch **beheren van virtuele Azure-machines**. Klik op **inschakelen op bestaande virtuele machine**, selecteer de virtuele machines die u wilt vrijmaken, klik op **selecteren**, klik op **inschakelen** en u bent klaar.

De enige keer dat u moet kunnen communiceren met deze virtuele machine om deze services te beheren, is in het geval dat u de virtuele machine hebt geprobeerd te herstellen, maar dit is niet gelukt. Als uw virtuele machine is hersteld, wordt deze weer in overeenstemming gebracht zonder dat u wordt gewaarschuwd.


## <a name="configuration-profiles"></a>Configuratieprofielen

Wanneer u automatisch beheer voor uw virtuele machine inschakelt, is een configuratie profiel vereist. Configuratie profielen vormen de basis van deze service. Ze definiëren precies op welke services we uw machines opwaarderen, en wat de omvang van de configuratie van die services zou zijn.

### <a name="default-configuration-profiles"></a>Standaard configuratie profielen

Er zijn momenteel twee configuratie profielen beschikbaar.

- **Aanbevolen procedures voor de virtuele machine van Azure-dev/test-** configuratie profiel is ontworpen voor ontwikkel-en test machines.
- **Aanbevolen procedures voor virtuele Azure-machines: het productie** configuratie profiel is voor productie.

De reden voor deze onderscheid is dat bepaalde services worden aanbevolen op basis van de uitgevoerde werk belasting. Zo wordt in een productie machine automatisch op de Azure Backup. Voor een dev/test-machine is een back-upservice echter een overbodige kosten, aangezien ontwikkel-en test machines doorgaans een lagere impact op het bedrijf hebben.

### <a name="customizing-a-configuration-profile-using-preferences"></a>Een configuratie profiel aanpassen met voor keuren

Naast de standaard services die wij u voor u hebben, kunt u een bepaalde subset met voor keuren configureren. Deze voor keuren zijn toegestaan binnen een reeks configuratie opties die geen inbreuk maken op onze aanbevolen procedures. In het geval van Azure Backup, kunt u bijvoorbeeld de frequentie van de back-up definiëren en op welke dag van de week deze zich bevindt. Het is echter *niet* mogelijk om Azure backup volledig uit te scha kelen.

> [!NOTE]
> In het configuratie profiel voor het ontwikkelen en testen wordt helemaal geen back-up gemaakt van de VM.

U kunt de instellingen van een standaard configuratie profiel aanpassen via voor keuren. Meer informatie over het maken van [een voor keur](virtual-machines-custom-preferences.md).

> [!NOTE]
> U kunt het configuratie profiel op uw virtuele machine niet wijzigen terwijl automanage is ingeschakeld. U moet automatisch beheer voor die virtuele machine uitschakelen en vervolgens automanage opnieuw inschakelen met het gewenste configuratie profiel en voor keuren.


## <a name="automanage-account"></a>Account voor automanage

Het account voor automatisch beheer is de beveiligings context of de identiteit waaronder de geautomatiseerde bewerkingen plaatsvinden. Normaal gesp roken hoeft u de optie voor het automatisch beheren van het account niet te selecteren, maar als er sprake is van een overdrachts scenario waarbij u het geautomatiseerd beheer (mogelijk tussen twee systeem beheerders) wilt verdelen, kunt u met deze optie een Azure-identiteit definiëren voor elk van deze beheerders.

Wanneer u automatisch beheer op uw virtuele machines inschakelt, is er een geavanceerde vervolg keuzelijst op de Blade **Azure VM-best practice inschakelen** waarmee u het automatisch beheer account kunt toewijzen of hand matig maken. Azure Portal

> [!NOTE]
> U moet de rol **Inzender** hebben voor de resource groep die uw virtuele machines bevat voor het inschakelen van automanage op vm's met behulp van een bestaand automanage-account. Als u automanage inschakelt met een nieuw automanage-account, hebt u de volgende machtigingen nodig voor uw abonnement: rol van **eigenaar** of **Inzender** samen met beheerders rollen van **gebruikers toegang** .


## <a name="status-of-vms"></a>Status van virtuele machines

Ga in het Azure Portal naar de pagina **Aanbevolen procedures voor het automatisch beheren van virtuele Azure-machines** , waarin al uw door u beheerde vm's worden weer gegeven. Hier ziet u de algemene status van elke virtuele machine.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lijst met geconfigureerde virtuele machines.":::

Voor elke vermelde VM worden de volgende gegevens weer gegeven: naam, configuratie profiel, configuratie voorkeur, status, account, abonnement en resource groep.

In de kolom **status** kunnen de volgende statussen worden weer gegeven:
- *In uitvoering* : de VM is zojuist ingeschakeld en wordt geconfigureerd
- *Geconfigureerd* : de virtuele machine is geconfigureerd en er is geen drift gedetecteerd
- *Mislukt* : de VM is gedrift en kan niet worden hersteld

Als u de **status** als *mislukt* ziet, kunt u problemen met de implementatie oplossen via de resource groep waar uw VM zich bevindt. Ga naar **resource groepen**, selecteer uw resource groep, klik op **implementaties** en Bekijk de status *mislukt* samen met de fout Details.


## <a name="disabling-automanage-for-vms"></a>Automanage voor Vm's uitschakelen

U kunt een dag kiezen om automanage op bepaalde Vm's uit te scha kelen. Op uw computer wordt bijvoorbeeld een zeer gevoelige veilige werk belasting uitgevoerd en u moet deze zelfs nog verder vergren delen dan Azure zou hebben moeten worden vergrendeld, dus u moet de computer configureren buiten de best practices van Azure.

Ga hiervoor in het Azure Portal naar de pagina aanbevolen procedures voor het automatisch **beheren van virtuele Azure-machines** met een lijst met alle automatische beheerde vm's. Schakel het selectie vakje in naast de virtuele machine die u wilt uitschakelen voor automatisch beheer en klik vervolgens op de knop **automanagement uitschakelen** .

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Automanage uitschakelen op een virtuele machine.":::

Lees aandachtig door de berichten in het pop-upvenster voordat u akkoord gaat met het **uitschakelen**.

> [!NOTE]
> Het uitschakelen van het automanagement in een VM resulteert in het volgende gedrag:
>
> - De configuratie van de virtuele machine en de services waarop de VM wordt uitgevoerd, worden niet gewijzigd.
> - Alle kosten die door deze services worden gemaakt, blijven Factureerbaar en blijven worden gemaakt.
> - Alle gedragingen voor het direct beheren worden onmiddellijk gestopt.


In eerste instantie is de virtuele machine niet uit een van de services die we hebben opgedaan bij en geconfigureerd. Alle kosten die door deze services worden gemaakt, blijven Factureerbaar. Als dat nodig is, moet u uit de weg. Elk automatisch beheer gedrag wordt direct gestopt. De virtuele machine wordt bijvoorbeeld niet langer bewaakt voor drift.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd dat automatisch beheer voor virtuele machines een manier heeft waarop u de nood zaak voor het voor komen van, het voorbereiden op en het configureren van aanbevolen procedures voor Azure-Services kunt elimineren. Als een computer waarop u hebt gewaakt voor het automatisch beheren van de drift van virtuele machines van de configuratie profielen, wordt er bovendien automatisch weer aan de vereisten voldaan.

Probeer automanage in te scha kelen voor virtuele machines in de Azure Portal.

> [!div class="nextstepaction"]
> [Schakel automanage in voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)