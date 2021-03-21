---
title: BareMetal instantie-eenheden in azure
description: Meer informatie over het identificeren en gebruiken van BareMetal-exemplaar eenheden via de Azure Portal.
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 076e84473a7d067712625dd12a2d5cae42bfa91a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548162"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>BareMetal-instanties beheren via de Azure Portal
 
In dit artikel wordt uitgelegd hoe [BareMetal-exemplaren](baremetal-overview-architecture.md)worden weer gegeven in de [Azure Portal](https://portal.azure.com/) . In dit artikel ziet u ook de activiteiten die u kunt uitvoeren in de Azure Portal met uw geïmplementeerde BareMetal-exemplaar eenheden. 
 
## <a name="register-the-resource-provider"></a>De resourceprovider registreren
Een Azure-resource provider voor BareMetal-instanties biedt zicht baarheid van de instanties in de Azure Portal, momenteel beschikbaar als open bare preview. Het Azure-abonnement dat u gebruikt voor implementaties van BareMetal-exemplaar registreert standaard de resource provider *BareMetalInfrastructure* . Als uw geïmplementeerde instantie-eenheden van BareMetal niet worden weer geven, moet u de resource provider registreren bij uw abonnement. 

U kunt de resource provider voor de BareMetal-instantie registreren met behulp van de Azure Portal of Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
U moet uw abonnement vermelden in de Azure Portal en vervolgens dubbel klikken op het abonnement dat is gebruikt voor het implementeren van uw BareMetal-instantie-eenheden.
 
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Alle services** in het menu van Azure Portal.

1. Voer **abonnement** in het vak **Alle services** in, en selecteer vervolgens **Abonnementen**.

1. Selecteer het abonnement in de lijst met abonnementen om dit weer te geven.

1. Selecteer **resource providers** en voer **BareMetalInfrastructure** in de zoek opdracht in. De resource provider moet worden **geregistreerd**, omdat de afbeelding wordt weer gegeven.
 
>[!NOTE]
>Als de resourceprovider niet is geregistreerd, selecteert u **Registreren**.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="Scherm opname van de geregistreerde BareMetal-instantie-eenheid":::

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gaan gebruiken:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Meld u aan bij het Azure-abonnement dat u gebruikt voor de implementatie van het BareMetal-exemplaar via de Azure CLI. Registreer de `BareMetalInfrastructure` resource provider bij de opdracht [AZ provider REGI ster](/cli/azure/provider#az_provider_register) :

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

U kunt de opdracht [AZ provider List](/cli/azure/provider#az_provider_list) gebruiken om alle beschik bare providers weer te geven.

---

Zie [Azure resource providers en-typen](../../../azure-resource-manager/management/resource-providers-and-types.md)voor meer informatie over resource providers.

## <a name="baremetal-instance-units-in-the-azure-portal"></a>BareMetal instantie-eenheden in de Azure Portal
 
Wanneer u een aanvraag voor een BareMetal-exemplaar verzendt, geeft u het Azure-abonnement op dat u aan de BareMetal-instanties wilt koppelen. Gebruik hetzelfde abonnement dat u gebruikt om de toepassingslaag te implementeren die werkt op basis van de BareMetal-exemplaar-eenheden.
 
Tijdens de implementatie van uw BareMetal-instanties wordt een nieuwe [Azure-resource groep](../../../azure-resource-manager/management/manage-resources-portal.md) gemaakt in het Azure-abonnement dat u in de implementatie aanvraag hebt gebruikt. Deze nieuwe resource groep bevat een lijst met alle BareMetal instantie-eenheden die u in het specifieke abonnement hebt geïmplementeerd.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in het BareMetal-abonnement in de Azure Portal **resource groepen**.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="Scherm opname van de lijst met resource groepen":::

1. Zoek de nieuwe resource groep in de lijst.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="Scherm opname van de BareMetal-instantie-eenheid in een gefilterde lijst met resource groepen" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >U kunt filteren op het abonnement dat u hebt gebruikt voor het implementeren van het BareMetal-exemplaar. Nadat u hebt gefilterd op het juiste abonnement, hebt u mogelijk een lange lijst met resource groepen. Zoek er een met een na correctie van **-TXXX** waarbij xxx drie cijfers is, zoals **-T250**.

1. Selecteer de nieuwe resource groep om de details ervan weer te geven. In de afbeelding ziet u een exemplaar van de BareMetal-instantie die is geïmplementeerd.
   
   >[!NOTE]
   >Als u meerdere BareMetal-exemplaar-tenants hebt geïmplementeerd onder hetzelfde Azure-abonnement, ziet u meerdere Azure-resource groepen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u al uw BareMetal-instanties wilt weer geven, voert u de opdracht [AZ baremetalinstance List](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) uit voor uw resource groep:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> De `--output` para meter is een globale para meter die beschikbaar is voor alle opdrachten. De **tabel** waarde geeft uitvoer met een beschrijvende indeling. Zie [output formats for Azure cli-opdrachten](/cli/azure/format-output-azure-cli)voor meer informatie.

---

## <a name="view-the-attributes-of-a-single-instance"></a>De kenmerken van één exemplaar weer geven

U kunt de details van één eenheid weer geven.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in de lijst van de BareMetal-instantie het ene exemplaar dat u wilt weer geven.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="Scherm opname van de BareMetal exemplaar-eenheids kenmerken van één exemplaar" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
De kenmerken in de installatie kopie kijken niet veel af van de kenmerken van de Azure virtual machine (VM). Aan de linkerkant ziet u de resource groep, de Azure-regio en de naam van het abonnement en de ID. Als u labels hebt toegewezen, worden deze hier ook weer gegeven. Standaard hebben de exemplaar eenheden van BareMetal geen labels toegewezen.
 
Aan de rechter kant ziet u de naam van de eenheid, het besturings systeem (OS), het IP-adres en de SKU, waarin het aantal CPU-threads en het geheugen wordt weer gegeven. U ziet ook de energie status en de hardware-versie (revisie van de BareMetal-instantie stempel). De energie status geeft aan of de hardware-eenheid is ingeschakeld of uitgeschakeld. De details van het besturings systeem geven echter niet aan of het actief is en wordt uitgevoerd.
 
De mogelijke hardware revisies zijn:

* Revisie 3 (Rev 3)

* Revisie 4 (Rev 4)
 
* Revisie 4,2 (Rev 4,2)
 
>[!NOTE]
>Rev 4,2 is de meest recente BareMetal-infra structuur met een eigenlijke rebranding en maakt gebruik van de bestaande Rev 4-architectuur. Rev 4 biedt dichter nabijheid van de Azure virtual machine-hosts (VM). Het heeft aanzienlijke verbeteringen in de netwerk latentie tussen Azure Vm's en BareMetal exemplaar eenheden die zijn geïmplementeerd in Rev 4-stem pels of-rijen. U kunt uw BareMetal-instanties openen en beheren via de Azure Portal. Zie [BareMetal-infra structuur op Azure](baremetal-overview-architecture.md)voor meer informatie.
 
Aan de rechter kant ziet u ook de naam van de [locatie van de Azure nabijheid](../../../virtual-machines/co-location.md) , die automatisch wordt gemaakt voor elke geïmplementeerde BareMetal-exemplaar-eenheid. Verwijs naar de plaatsings groep voor proximity wanneer u de virtuele Azure-machines implementeert die als host fungeren voor de toepassingslaag. Wanneer u de plaatsings groep voor proximity gebruikt die is gekoppeld aan de BareMetal-exemplaar-eenheid, zorgt u ervoor dat de virtuele Azure-machines dicht bij de BareMetal-exemplaar-eenheid worden geïmplementeerd.
 
>[!TIP]
>Als u de toepassingslaag in hetzelfde Azure-Data Center wilt vinden als revisie 4. x, raadpleegt u [Azure proximity placement groups voor optimale netwerk latentie](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de opdracht [AZ baremetalinstance show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) uit om de details van een BareMetal-exemplaar weer te geven:

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Als u niet zeker weet wat de naam van het exemplaar is, voert u de `az baremetalinstance list` opdracht uit, zoals hierboven beschreven.

---
 
## <a name="check-activities-of-a-single-instance"></a>Activiteiten van één exemplaar controleren
 
U kunt de activiteiten van één eenheid controleren. Een van de geregistreerde hoofd activiteiten wordt opnieuw gestart van de eenheid. De weer gegeven gegevens zijn inclusief de status van de activiteit, de tijds tempel die de activiteit heeft geactiveerd, de abonnements-ID en de Azure-gebruiker die de activiteit heeft geactiveerd.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="Scherm opname van de activiteiten van de BareMetal-instantie-eenheid" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Wijzigingen in de meta gegevens van de eenheid in Azure worden ook vastgelegd in het activiteiten logboek. Naast het opnieuw opstarten, ziet u de activiteit van **Write BareMetallnstances**. Met deze activiteit worden geen wijzigingen aangebracht in de BareMetal-exemplaar eenheid zelf, maar worden de wijzigingen in de meta gegevens van de eenheid in azure gedocumenteerd.
 
Wanneer u een [tag](../../../azure-resource-manager/management/tag-resources.md) toevoegt aan of verwijdert uit een-instantie, wordt een andere activiteit die wordt geregistreerd, verwijderd.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Een Azure-tag toevoegen aan en verwijderen uit een exemplaar

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
U kunt Azure-Tags toevoegen aan een BareMetal-instantie-eenheid of ze verwijderen. De manier waarop Tags Get wordt toegewezen, verschilt niet van het toewijzen van tags aan Vm's. Net als bij Vm's bestaan de tags in de meta gegevens van Azure en voor BareMetal-instanties dezelfde beperkingen als de labels voor Vm's.
 
Het verwijderen van Tags werkt op dezelfde manier als met virtuele machines. Het Toep assen en verwijderen van een tag wordt weer gegeven in het activiteiten logboek van het BareMetal-exemplaar.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Het toewijzen van labels aan BareMetal-instanties werkt hetzelfde als voor virtuele machines. De tags bevinden zich in de Azure-meta gegevens en voor BareMetal-instanties hebben ze dezelfde beperkingen als de labels voor Vm's.

Voer de opdracht [AZ baremetalinstance update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) uit om tags toe te voegen aan een BareMetal-exemplaar:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Gebruik dezelfde opdracht om een tag te verwijderen:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>De eigenschappen van een exemplaar controleren
 
Wanneer u de exemplaren aanschaft, kunt u naar de sectie eigenschappen gaan om de gegevens weer te geven die zijn verzameld over de instanties. De verzamelde gegevens zijn onder andere de Azure-connectiviteit, de opslag back-end, de ExpressRoute-circuit-ID, de unieke Resource-ID en de abonnements-ID. U gebruikt deze informatie in ondersteunings aanvragen of bij het instellen van de configuratie van de opslag momentopname.
 
Een ander belang rijk stukje informatie dat u ziet, is het IP-adres van de opslag-NFS. Het isoleert uw opslag in uw **Tenant** in de BareMetal-exemplaar stack. U gebruikt dit IP-adres wanneer u het [configuratie bestand bewerkt voor back-ups van opslag momentopnamen](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="Scherm afbeelding met de instellingen voor de BareMetal-instantie" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Een eenheid opnieuw opstarten via de Azure Portal

Er zijn verschillende situaties waarin het besturings systeem niet opnieuw moet worden opgestart, waardoor de BareMetal-exemplaar-eenheid opnieuw moet worden opgestart.

### <a name="portal"></a>[Portal](#tab/azure-portal)

U kunt een energie opnieuw opstarten van de eenheid rechtstreeks vanuit de Azure Portal:
 
Selecteer **opnieuw opstarten** en vervolgens **Ja** om te bevestigen dat de eenheid opnieuw moet worden opgestart.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="Scherm afbeelding waarin wordt weer gegeven hoe de BareMetal-exemplaar eenheid opnieuw moet worden opgestart":::
 
Wanneer u een BareMetal-instantie-eenheid opnieuw opstart, treden er een vertraging op. Tijdens deze vertraging gaat de energie status van **Start** naar **gestart**, wat betekent dat het besturings systeem volledig is gestart. Als gevolg hiervan kunt u na het opnieuw opstarten zich niet aanmelden bij de eenheid zodra de status overschakelt naar **gestart**.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een BareMetal-exemplaar opnieuw wilt starten, gebruikt u de opdracht [AZ baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) :

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>Afhankelijk van de hoeveelheid geheugen in uw BareMetal-instantie-eenheid, kan het opnieuw opstarten en opnieuw opstarten van de hardware en het besturings systeem een uur duren.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Een ondersteunings aanvraag openen voor BareMetal-instanties
 
U kunt specifieke ondersteunings aanvragen indienen voor een BareMetal-exemplaar.
1. Maak in Azure Portal onder **Help en ondersteuning** een **[nieuwe ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support)** en geef de volgende informatie op voor het ticket:
 
   - **Type probleem:** Selecteer een probleem type
 
   - **Abonnement:** Uw abonnement selecteren
 
   - **Service:** BareMetal-infra structuur
 
   - **Resource:** Geef de naam van het exemplaar op
 
   - **Samen vatting:** Geef een samen vatting van uw aanvraag op
 
   - **Probleem type:** Selecteer een probleem type
 
   - **Subtype van probleem:** Selecteer een subtype voor het probleem

1. Selecteer het tabblad **oplossingen** om een oplossing voor het probleem te vinden. Als u geen oplossing kunt vinden, gaat u naar de volgende stap.

1. Selecteer het tabblad **Details** en selecteer of het probleem met vm's of de BareMetal-exemplaar eenheden bestaat. Met deze informatie kunt u de ondersteunings aanvraag naar de juiste specialisten sturen.

1. Geef aan wanneer het probleem is begonnen en selecteer de exemplaar regio.

1. Geef meer informatie over de aanvraag en upload een bestand als dat nodig is.

1. Selecteer **beoordeling + maken** om de aanvraag in te dienen.
 
Het duurt Maxi maal vijf werk dagen voor een ondersteunings medewerker om uw aanvraag te bevestigen.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over de werk belastingen, raadpleegt u [BareMetal workload types](../../../virtual-machines/workloads/sap/get-started.md).