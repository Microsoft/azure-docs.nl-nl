---
title: Azure automanage voor virtuele machines
description: Meer informatie over Azure automanage voor virtuele machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: e4e1d22e2e7175135e88a08ed5a6d5ae7f021d49
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491271"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure automanage voor virtuele machines

In dit artikel wordt informatie behandeld over Azure automanage voor virtuele machines, die de volgende voor delen hebben:

- Virtuele machines op intelligente wijze voorbereiden om aanbevolen procedures voor Azure-Services te selecteren
- Configureert automatisch elke service per aanbevolen procedures van Azure
- Controleert op drift en corrigeert deze wanneer ze zijn gedetecteerd
- Biedt een eenvoudige ervaring (punt, klik, instellen, verg eten)


## <a name="overview"></a>Overzicht

Azure automanage voor virtuele machines is een service waarmee u niet hoeft te ontdekken, weet hoe u kunt uitkomen en hoe u bepaalde services in azure kunt configureren die voor deel van uw virtuele machine zijn. Deze services worden beschouwd als Services voor aanbevolen procedures van Azure en helpen de betrouw baarheid, beveiliging en het beheer van virtuele machines te verbeteren. Voor beelden van services zijn [Azure updatebeheer](../automation/update-management/overview.md) en [Azure backup](../backup/backup-overview.md).

Nadat u uw virtuele machines hebt voor bereid op Azure automanage, wordt elke best practice-service geconfigureerd met de aanbevolen instellingen. Aanbevolen procedures verschillen voor elk van de services. Een voor beeld is mogelijk Azure Backup, waarbij de best practice mogelijk eenmaal per dag een back-up van de virtuele machine maakt en een Bewaar periode van zes maanden hebben.

Met Azure auto Manage wordt ook automatisch gecontroleerd op drift en wordt deze gecorrigeerd wanneer ze worden gedetecteerd. Dit betekent dat als uw virtuele machine onboarded is voor Azure automanage, deze niet alleen worden geconfigureerd per Azure best practices, maar we controleren uw machine om te garanderen dat deze de best practices in de hele levens cyclus blijft naleven. Als uw virtuele machine een drift of afwijking van deze procedures heeft (bijvoorbeeld als een service offboarded is), wordt deze gecorrigeerd en wordt de computer weer in de gewenste staat gebracht.

## <a name="prerequisites"></a>Vereisten

Er zijn verschillende vereisten die u moet overwegen voordat u Azure automanage op uw virtuele machines inschakelt.

- Ondersteunde [Windows Server-versies](automanage-windows-server.md#supported-windows-server-versions) en [Linux-distributies](automanage-linux.md#supported-linux-distributions-and-versions)
- Vm's moeten zich in een ondersteunde regio bevinden (zie hieronder)
- De gebruiker moet over de juiste machtigingen beschikken (zie hieronder)
- Automanage biedt momenteel geen ondersteuning voor sandbox-abonnementen

### <a name="supported-regions"></a>Ondersteunde regio’s
Automanage ondersteunt alleen Vm's die zich in de volgende regio's bevinden:
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

### <a name="required-rbac-permissions"></a>Vereiste RBAC-machtigingen
Voor uw account zijn enigszins verschillende RBAC-rollen vereist, afhankelijk van het feit of u automatisch beheer met een nieuw account voor automatisch beheer inschakelt.

Als u automanage inschakelt met een nieuw account voor automanage:
* De rol van **eigenaar** van het abonnement (en) met uw vm's _**of**_
* Beheerders-en **gebruikers toegangs** rollen op het abonnement **(en)** die uw vm's bevatten

Als u automanage inschakelt met een bestaand automanage-account:
* Rol **Inzender** voor de resource groep die uw vm's bevat

Voor het account voor automatisch beheer worden **Inzender en** machtigings machtigingen voor het **resource beleid** verleend voor het uitvoeren van acties op automatisch beheerde computers.

> [!NOTE]
> Als u automanage wilt gebruiken op een virtuele machine die is verbonden met een werk ruimte in een ander abonnement, moet u de hiervoor vermelde machtigingen hebben voor elk abonnement.

## <a name="participating-services"></a>Deelnemende Services

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Services op intelligente wijze voorbereiden.":::

Voor de volledige lijst met deelnemende Azure-Services en de ondersteunde omgeving raadpleegt u het volgende:
- [Automanage voor Linux](automanage-linux.md)
- [Automanage voor Windows Server](automanage-windows-server.md)

 U wordt automatisch geadviseerd voor deze deelnemende Services. Ze zijn essentieel voor het technische document van best practices, dat u kunt vinden in ons [Cloud adoptie Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Voor al deze services worden automatisch de voor bereid, automatisch geconfigureerd, bewaken, controleren en verholpen als er een drift wordt gedetecteerd.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Automanage inschakelen voor Vm's in Azure Portal

In de Azure Portal kunt u automatisch beheer inschakelen op een bestaande virtuele machine of wanneer u een nieuwe virtuele machine maakt. Bekijk de Snelstartgids voor het [autobeheren van virtuele machines](quick-create-virtual-machines-portal.md)voor beknopte stappen voor dit proces.

Als dit de eerste keer is dat u automatisch beheer voor uw virtuele machine inschakelt, kunt u zoeken in de Azure Portal voor de aanbevolen procedures voor het automatisch **beheren van virtuele Azure-machines**. Klik op **inschakelen op bestaande virtuele machine**, selecteer de virtuele machines die u wilt vrijmaken, klik op **selecteren**, klik op **inschakelen** en u bent klaar.

De enige keer dat u moet kunnen communiceren met deze virtuele machine om deze services te beheren, is in het geval dat u de virtuele machine hebt geprobeerd te herstellen, maar dit is niet gelukt. Als uw virtuele machine is hersteld, wordt deze weer in overeenstemming gebracht zonder dat u wordt gewaarschuwd. Zie [status van virtuele machines](#status-of-vms)voor meer informatie.

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Inschakelen van automanage voor Vm's met behulp van Azure Policy
U kunt automatisch beheer op Vm's op schaal inschakelen met behulp van de ingebouwde Azure Policy. Het beleid heeft een DeployIfNotExists-effect, wat betekent dat alle in aanmerking komende Vm's die zich binnen het bereik van het beleid bevinden automatisch worden doorgevoerd om aanbevolen procedures voor het Automate van vm's te beheren.

Een directe koppeling naar het beleid is [hier](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3).

### <a name="how-to-apply-the-policy"></a>Het beleid Toep assen
1. Klik op de knop **toewijzen** bij het weer geven van de beleids definitie
1. Selecteer het bereik waarop u het beleid wilt Toep assen (kan beheer groep, abonnement of resource groep zijn)
1. Onder **para meters** geeft u para meters op voor het account voor automatische beheer, het configuratie profiel en het effect (het effect moet meestal worden DeployIfNotExists)
    1. Als u geen automatisch beheer account hebt, moet u er [een maken](#create-an-automanage-account).
1. Schakel onder **herstel** het selectie vakje ' Klik op een herstel taak ' in. Hiermee wordt onboarding voor automatisch beheer uitgevoerd.
1. Klik op **controleren + maken** en zorg ervoor dat alle instellingen goed zijn.
1. Klik op **Create**.

## <a name="environment-configuration"></a>Omgevingsconfiguratie

Wanneer u automatisch beheer voor uw virtuele machine inschakelt, is een omgeving vereist. Omgevingen vormen de basis van deze service. Ze bepalen op welke services uw machines worden uitgevoerd en wat de omvang van de configuratie van die services zou zijn.

### <a name="default-environments"></a>Standaard omgevingen

Er zijn momenteel twee omgevingen beschikbaar.

- **Dev/test-** omgeving is ontworpen voor ontwikkel-en test machines.
- **Productie** omgeving is voor productie.

De reden voor deze onderscheid is dat bepaalde services worden aanbevolen op basis van de uitgevoerde werk belasting. Zo wordt in een productie machine automatisch op de Azure Backup. Voor een dev/test-machine is een back-upservice echter een overbodige kosten, aangezien ontwikkel-en test machines doorgaans een lagere impact op het bedrijf hebben.

### <a name="customizing-an-environment-using-preferences"></a>Een omgeving aanpassen met voor keuren

Naast de standaard services die wij u voor u hebben, kunt u een bepaalde subset met voor keuren configureren. Deze voor keuren zijn toegestaan binnen verschillende configuratie opties. In het geval van Azure Backup, kunt u bijvoorbeeld de frequentie van de back-up definiëren en op welke dag van de week deze zich bevindt.

> [!NOTE]
> In de ontwikkel-en test omgeving wordt helemaal geen back-up gemaakt van de VM.

U kunt de instellingen van een standaard omgeving aanpassen via voor keuren. Meer informatie over het maken van [een voor keur](virtual-machines-custom-preferences.md).

> [!NOTE]
> U kunt de enivonrment-configuratie op uw virtuele machine niet wijzigen terwijl automanage is ingeschakeld. U moet automatisch beheer voor die virtuele machine uitschakelen en vervolgens automanage opnieuw inschakelen met de gewenste omgeving en voor keuren.

Zie hier voor de volledige lijst met deelnemende Azure-Services en als deze voor keuren worden ondersteund:
- [Automanage voor Linux](automanage-windows-server.md)
- [Automanage voor Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Account voor automanage

Het account voor automatisch beheer is de beveiligings context of de identiteit waaronder de geautomatiseerde bewerkingen plaatsvinden. Normaal gesp roken hoeft u de optie voor het automatisch beheren van het account niet te selecteren, maar als er sprake is van een overdrachts scenario waarbij u het geautomatiseerde beheer van uw resources (mogelijk tussen twee systeem beheerders) wilt verdelen, kunt u met deze optie een Azure-identiteit definiëren voor elk van deze beheerders.

Wanneer u automatisch beheer op uw virtuele machines inschakelt, is er een geavanceerde vervolg keuzelijst op de Blade **Azure VM-best practice inschakelen** waarmee u het automatisch beheer account kunt toewijzen of hand matig maken. Azure Portal

Voor het account voor automatisch beheer worden Inzender **rollen en functies** voor het **resource beleid** worden verleend aan de abonnementen met de machine (s) die u wilt beheren. U kunt hetzelfde account voor automatisch beheer op machines op meerdere abonnementen gebruiken, waarmee de **Inzender** en de Inzender machtigingen voor het **resource beleid** voor alle abonnementen worden verleend.

Als uw virtuele machine is verbonden met een Log Analytics-werk ruimte in een ander abonnement, wordt aan de automanage- **account zowel Inzender** als **Inzender voor resource beleid** verleend in dat andere abonnement.

Als u automanage inschakelt met een nieuw automanage-account, hebt u de volgende machtigingen nodig voor uw abonnement: rol van **eigenaar** of **Inzender** samen met beheerders rollen van **gebruikers toegang** .

Als u automanage met een bestaand automanage-account inschakelt, moet u de rol **Inzender** hebben voor de resource groep die uw virtuele machines bevat.

> [!NOTE]
> Wanneer u aanbevolen procedures voor automatisch beheer uitschakelt, blijven de machtigingen voor het automatisch beheren van het account voor gekoppelde abonnementen behouden. Verwijder de machtigingen hand matig door naar de IAM-pagina van het abonnement te gaan of door het account voor automatisch beheer te verwijderen. Het automanage-account kan niet worden verwijderd als er nog computers worden beheerd.

### <a name="create-an-automanage-account"></a>Een automanage-account maken
U kunt een account voor automanage maken via de portal of met een ARM-sjabloon.

#### <a name="portal"></a>Portal
1. Ga naar de Blade **automanage** in de portal
1. Klik op **inschakelen op bestaande machine**
1. Klik onder **Geavanceerd** op ' een nieuw account maken '
1. Vul de vereiste velden in en klik op **maken**

#### <a name="arm-template"></a>ARM-sjabloon
Sla de volgende ARM-sjabloon op als `azuredeploy.json` en voer de volgende opdracht uit: `az deployment group create --resource-group <resource group name> --template-file azuredeploy.json`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```

## <a name="status-of-vms"></a>Status van virtuele machines

Ga in het Azure Portal naar de pagina **Aanbevolen procedures voor het automatisch beheren van virtuele Azure-machines** , waarin al uw door u beheerde vm's worden weer gegeven. Hier ziet u de algemene status van elke virtuele machine.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lijst met geconfigureerde virtuele machines.":::

Voor elke vermelde VM worden de volgende gegevens weer gegeven: naam, omgeving, configuratie voorkeur, status, besturings systeem, account, abonnement en resource groep.

In de kolom **status** kunnen de volgende statussen worden weer gegeven:
- *In uitvoering* : de VM is zojuist ingeschakeld en wordt geconfigureerd
- *Geconfigureerd* : de virtuele machine is geconfigureerd en er is geen drift gedetecteerd
- *Mislukt* : de VM is gedrift en kan niet worden hersteld
- *In behandeling* : de virtuele machine wordt op dit moment niet uitgevoerd en er wordt geprobeerd om de VM op te lossen of te herstellen wanneer deze de volgende keer wordt uitgevoerd

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

In dit artikel hebt u geleerd dat automatisch beheer voor virtuele machines een manier heeft waarop u de nood zaak voor het voor komen van, het voorbereiden op en het configureren van aanbevolen procedures voor Azure-Services kunt elimineren. Als een door u geplaatste machine voor het automatisch beheren van virtuele machines van het instellen van de omgeving is opgebruikt, zullen we er bovendien voor zorgen dat de computer niet meer compatibel is.

Probeer automanage in te scha kelen voor virtuele machines in de Azure Portal.

> [!div class="nextstepaction"]
> [Schakel automanage in voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)