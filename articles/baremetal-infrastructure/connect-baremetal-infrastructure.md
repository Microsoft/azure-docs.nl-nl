---
title: BareMetal Infrastructure-instanties verbinden in Azure
description: Meer informatie over het identificeren en gebruiken van BareMetal-exemplaren in de Azure Portal of Azure CLI.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: e67ede85608f2a33dcc179005f0090ca2ce6a640
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871647"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>BareMetal Infrastructure-instanties verbinden in Azure

In dit artikel wordt beschreven [Azure Portal](https://portal.azure.com/) [BareMetal-exemplaren worden weergegeven.](concepts-baremetal-infrastructure-overview.md) In dit artikel wordt ook beschreven wat u in de Azure Portal met uw geïmplementeerde BareMetal Infrastructure-exemplaren. 
 
## <a name="register-the-resource-provider"></a>De resourceprovider registreren
Een Azure-resourceprovider voor BareMetal-exemplaren biedt inzicht in de exemplaren in de Azure Portal. Het Azure-abonnement dat u gebruikt voor implementaties van BareMetal-exemplaren registreert standaard de resourceprovider *BareMetalInfrastructure.* Als u uw geïmplementeerde BareMetal-exemplaren niet ziet, moet u de resourceprovider registreren bij uw abonnement. 

U kunt de resourceprovider van het BareMetal-exemplaar registreren met behulp van de Azure Portal of Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
U moet uw abonnement in de lijst Azure Portal dubbelklikken op het abonnement dat is gebruikt om uw BareMetal-exemplaren te implementeren.
 
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Alle services** in het menu van Azure Portal.

1. Voer **abonnement** in het vak **Alle services** in, en selecteer vervolgens **Abonnementen**.

1. Selecteer het abonnement in de lijst met abonnementen.

1. Selecteer **Resourceproviders** en voer **BareMetalInfrastructure in** bij de zoekopdracht. De resourceprovider moet **Geregistreerd zijn,** zoals de afbeelding laat zien.
 
>[!NOTE]
>Als de resourceprovider niet is geregistreerd, selecteert u **Registreren**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Schermopname van de geregistreerde BareMetal-exemplaren.":::

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure CLI wilt gaan gebruiken, gaat u als volgende te werk:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Meld u aan bij het Azure-abonnement dat u gebruikt voor de implementatie van het BareMetal-exemplaar via de Azure CLI. Registreer de `BareMetalInfrastructure` resourceprovider met de [opdracht az provider register:](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

U kunt de opdracht [az provider list gebruiken](/cli/azure/provider#az_provider_list) om alle beschikbare providers weer te geven.

---

Zie Azure-resourceproviders en -typen voor meer informatie [over resourceproviders.](../azure-resource-manager/management/resource-providers-and-types.md)  

## <a name="baremetal-instances-in-the-azure-portal"></a>BareMetal-exemplaren in de Azure Portal
 
Wanneer u een implementatieaanvraag voor een BareMetal-exemplaar indient, geeft u het Azure-abonnement op dat u verbindt met de BareMetal-exemplaren. Gebruik hetzelfde abonnement dat u gebruikt om de toepassingslaag te implementeren die werkt met de BareMetal-exemplaren.
 
Tijdens de implementatie van uw BareMetal-exemplaren wordt een nieuwe [Azure-resourcegroep](../azure-resource-manager/management/manage-resources-portal.md) gemaakt in het Azure-abonnement dat u hebt gebruikt in de implementatieaanvraag. Deze nieuwe resourcegroep bevat alle BareMetal-exemplaren die u in dat abonnement hebt geïmplementeerd.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in het BareMetal-abonnement Azure Portal **resourcegroepen.**
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Schermopname van de lijst met resourcegroepen.":::

1. Zoek de nieuwe resourcegroep in de lijst.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Schermopname van het BareMetal-exemplaar in een lijst met gefilterde resourcegroepen." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >U kunt filteren op het abonnement dat u hebt gebruikt om het BareMetal-exemplaar te implementeren. Nadat u hebt gefilterd op het juiste abonnement, hebt u mogelijk een lange lijst met resourcegroepen. Zoek er een met een post-fix van **-Txxx, waarbij** xxx drie cijfers is, zoals **-T250.**

1. Selecteer de nieuwe resourcegroep om de details ervan weer te geven. In de afbeelding ziet u één geïmplementeerd BareMetal-exemplaar.
   
   >[!NOTE]
   >Als u verschillende tenants van BareMetal-exemplaren hebt geïmplementeerd onder hetzelfde Azure-abonnement, ziet u meerdere Azure-resourcegroepen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u al uw BareMetal-exemplaren wilt zien, voer dan de [opdracht az baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) uit voor uw resourcegroep:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> De `--output` parameter is een globale parameter die beschikbaar is voor alle opdrachten. De **tabelwaarde** geeft uitvoer weer in een gebruiksvriendelijke indeling. Zie Uitvoerindelingen voor [Azure CLI-opdrachten voor meer informatie.](/cli/azure/format-output-azure-cli)

---

## <a name="view-the-attributes-of-a-single-instance"></a>De kenmerken van één exemplaar weergeven

U kunt de details van één exemplaar bekijken.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer in de lijst met BareMetal-exemplaren de instantie die u wilt weergeven.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Schermopname met de kenmerken van het BareMetal-exemplaar van één exemplaar." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
De kenmerken in de afbeelding zien er niet veel anders uit dan de kenmerken van de virtuele Azure-machine (VM). Aan de linkerkant ziet u de resourcegroep, Azure-regio en abonnementsnaam en -id. Als u tags hebt toegewezen, ziet u deze hier ook. Aan de BareMetal-exemplaren zijn standaard geen tags toegewezen.
 
Aan de rechterkant ziet u de naam van het BareMetal-exemplaar, het besturingssysteem, het IP-adres en de SKU die het aantal CPU-threads en het geheugen tonen. U ziet ook de energie- en hardwareversie (revisie van de BareMetal-instantiestempel). De energietoestand geeft aan of de hardware-eenheid is ingeschakeld of uitgeschakeld. De details van het besturingssysteem geven echter niet aan of het actief is.
 
De mogelijke hardwarerevisies zijn:

* Revisie 3 (Rev 3)

* Revisie 4 (Rev 4)
 
* Revisie 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2 is de nieuwste BareMetal-infrastructuur met de bestaande Rev 4-architectuur. Rev 4 is dichter bij de hosts van virtuele Azure-machines (VM's). Het heeft aanzienlijke verbeteringen in de netwerklatentie tussen azure-VM's en SAP HANA instanties. U kunt uw BareMetal-exemplaren openen en beheren via de Azure Portal. Zie [BareMetal Infrastructure on Azure (BareMetal-infrastructuur in Azure) voor meer informatie.](concepts-baremetal-infrastructure-overview.md)

 
Aan de rechterkant vindt u ook [](../virtual-machines/co-location.md) de naam van de Azure-nabijheidsplaatsingsgroep, die automatisch wordt gemaakt voor elk geïmplementeerd BareMetal-exemplaar. Verwijs naar de nabijheidsplaatsingsgroep wanneer u de Azure-VM's implementeert die de toepassingslaag hosten. Wanneer u de nabijheidsplaatsingsgroep gebruikt die is gekoppeld aan het BareMetal-exemplaar, zorgt u ervoor dat de Azure-VM's dicht bij het BareMetal-exemplaar worden geïmplementeerd.
 
>[!TIP]
>Zie Azure proximity placement groups for optimal network latency (Azure-nabijheidsplaatsingsgroepen voor optimale netwerklatentie) om de toepassingslaag te vinden in hetzelfde [Azure-datacenter als](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)Revision 4.x.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Voer de opdracht [az baremetalinstance show](/cli/azure/baremetalinstance#az_baremetalinstance_show) uit om de details van een BareMetal-exemplaar te bekijken:

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Als u niet zeker weet wat de naam van het exemplaar is, moet u de hierboven `az baremetalinstance list` beschreven opdracht uitvoeren.

---
 
## <a name="check-activities-of-a-single-instance"></a>Activiteiten van één exemplaar controleren
 
U kunt de activiteiten van één BareMetal-exemplaar controleren. Een van de vastgelegde hoofdactiviteiten zijn het opnieuw opstarten van het exemplaar. De vermelde gegevens omvatten de status van de activiteit, een tijdstempel van de geactiveerde activiteit, een abonnements-id en de Azure-gebruiker die de activiteit heeft geactiveerd.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Schermopname van de activiteiten van het BareMetal-exemplaar." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Wijzigingen in de metagegevens van het exemplaar in Azure worden ook vastgelegd in het activiteitenlogboek. Naast de gestarte herstart, kunt u de activiteit van **Write BareMetallnstances zien.** Deze activiteit maakt geen wijzigingen in het BareMetal-exemplaar zelf, maar documenteert de wijzigingen in de metagegevens van de eenheid in Azure.
 
Een andere activiteit die wordt vastgelegd, is wanneer u een [tag](../azure-resource-manager/management/tag-resources.md) aan een exemplaar toevoegt of verwijdert.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Een Azure-tag toevoegen aan en verwijderen uit een exemplaar

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
U kunt Azure-tags toevoegen aan een BareMetal-exemplaar of deze verwijderen. Tags worden net als bij het toewijzen van tags aan VM's toegewezen. Net als bij VM's bestaan de tags in de Azure-metagegevens. Tags hebben dezelfde beperkingen voor BareMetal-exemplaren als voor VM's.
 
Het verwijderen van tags werkt ook op dezelfde manier als voor VM's. Het toepassen en verwijderen van een tag wordt vermeld in het activiteitenlogboek van het BareMetal-exemplaar.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Het toewijzen van tags aan BareMetal-exemplaren werkt hetzelfde als het toewijzen van tags voor virtuele machines. Net als bij VM's bestaan de tags in de Azure-metagegevens. Tags hebben dezelfde beperkingen voor BareMetal-exemplaren als voor VM's.

Voer de opdracht [az baremetalinstance update](/cli/azure/baremetalinstance#az_baremetalinstance_update) uit om tags toe te voegen aan een BareMetal-exemplaar:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Gebruik dezelfde opdracht om een tag te verwijderen:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Eigenschappen van een exemplaar controleren
 
Wanneer u de exemplaren verkrijgt, kunt u naar de sectie Eigenschappen gaan om de gegevens weer te geven die over de exemplaren zijn verzameld. Gegevens die worden verzameld, zijn onder andere de Azure-connectiviteit, de opslagback-end, de ExpressRoute-circuit-id, de unieke resource-id en de abonnements-id. U gebruikt deze informatie in ondersteuningsaanvragen of bij het instellen van de configuratie van opslagmomentopnamen.
 
Een ander belangrijk stukje informatie dat u ziet, is het IP-adres van de opslag-NFS. Uw opslag wordt geïsoleerd voor uw **tenant** in de BareMetal-exemplaarstack. U gebruikt dit IP-adres wanneer u het configuratiebestand voor back-ups van [opslagmomentopnamen bewerkt.](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Schermopname van de eigenschapsinstellingen van het BareMetal-exemplaar." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Start een BareMetal-exemplaar opnieuw op via Azure Portal

Er zijn verschillende situaties waarin het besturingssysteem de herstart niet kan voltooien. Hiervoor moet het BareMetal-exemplaar opnieuw worden opgestart.

### <a name="portal"></a>[Portal](#tab/azure-portal)

U kunt het exemplaar rechtstreeks vanuit de Azure Portal:
 
Selecteer **Opnieuw opstarten** en vervolgens Ja **om** het opnieuw opstarten te bevestigen.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Schermopname die laat zien hoe u het BareMetal-exemplaar opnieuw opstart.":::
 
Wanneer u een BareMetal-exemplaar opnieuw opstart, ervaart u een vertraging. Tijdens deze vertraging wordt de energietoestand verplaatst van **Starten** naar **Gestart,** wat betekent dat het besturingssysteem volledig is gestart. Als gevolg hiervan kunt u zich na het opnieuw opstarten alleen aanmelden bij de eenheid zodra de status is overschakelt naar **Gestart.**

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [az baremetalinstance restart](/cli/azure/baremetalinstance#az_baremetalinstance_restart) om een BareMetal-exemplaar opnieuw op te starten:

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>Afhankelijk van de hoeveelheid geheugen in uw BareMetal-exemplaar kan het opnieuw opstarten en opnieuw opstarten van de hardware en het besturingssysteem tot één uur duren.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Een ondersteuningsaanvraag openen voor BareMetal-exemplaren
 
U kunt specifiek ondersteuningsaanvragen indienen voor BareMetal-exemplaren.
1. Maak Azure Portal onder **Help en ondersteuning** een **[nieuwe](https://rc.portal.azure.com/#create/Microsoft.Support)** ondersteuningsaanvraag en geef de volgende informatie voor het ticket op:
 
   - **Type probleem:** Selecteer een type probleem.
 
   - **Abonnement:** Selecteer uw abonnement.
 
   - **Service:** BareMetal-infrastructuur
 
   - **Resource:** Geef de naam van het exemplaar op.
 
   - **Samenvatting:** Geef een samenvatting van uw aanvraag op.
 
   - **Probleemtype:** Selecteer een probleemtype.
 
   - **Subtype van probleem:** Selecteer een subtype voor het probleem.

1. Selecteer het **tabblad Oplossingen** om een oplossing voor uw probleem te vinden. Als u geen oplossing kunt vinden, gaat u naar de volgende stap.

1. Selecteer het **tabblad Details** en selecteer of het probleem te maken heeft met VM's of BareMetal-exemplaren. Deze informatie helpt u de ondersteuningsaanvraag door te sturen naar de juiste specialisten.

1. Geef aan wanneer het probleem is begonnen en selecteer de instantieregio.

1. Geef meer informatie over de aanvraag op en upload indien nodig een bestand.

1. Selecteer **Beoordelen en maken om** de aanvraag in te dienen.
 
Het duurt maximaal vijf werkdagen voordat een ondersteuningsmedewerker uw aanvraag heeft bevestigd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over workloads:

- [Wat is SAP HANA in Azure (Large Instances)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
