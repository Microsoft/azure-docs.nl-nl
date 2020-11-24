---
title: Virtuele Azure-machines verplaatsen naar beschikbaarheids zones in een andere regio met Azure resource verhuizer
description: Meer informatie over het verplaatsen van Azure-Vm's naar beschikbaarheids zones met Azure resource-overgave.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542997"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Virtuele Azure-machines verplaatsen naar een beschikbaarheids zone in een andere regio

In dit artikel leert u hoe u virtuele Azure-machines (en gerelateerde netwerk-en opslag bronnen) verplaatst naar een beschikbaarheids zone in een andere Azure-regio met behulp van [Azure resource](overview.md)-overstap.

[Azure-beschikbaarheids zones](../availability-zones/az-overview.md#availability-zones) helpen uw Azure-implementatie te beschermen tegen Data Center-fouten. Elke beschikbaarheidszone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke voeding, koeling en netwerken. Om tolerantie te garanderen, zijn er mini maal drie afzonderlijke zones in alle [ingeschakelde regio's](../availability-zones/az-region.md). Met resource verplaatsen kunt u het volgende doen:

- Een virtuele machine met één exemplaar naar een beschikbaarheids zone of beschikbaarheidsset in de doel regio.
- Een virtuele machine in een beschikbaarheidsset met een beschikbaarheids zone/beschikbaarheidsset in de doel regio.
- Een virtuele machine in een beschikbaarheids zone van een bron regio naar een beschikbaarheids zone in de doel regio.


> [!IMPORTANT]
> Azure Resource Mover is momenteel beschikbaar als openbare preview-versie.

Als u Vm's wilt verplaatsen naar een andere beschikbaarheids zone in dezelfde regio, [raadpleegt u dit artikel](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Vereisten

- Toegang voor de *eigenaar* van het abonnement waarin resources zich bevinden die u wilt verplaatsen.
    - De eerste keer dat u een resource toevoegt voor een specifieke bron-en doel toewijzing in een Azure-abonnement, maakt resource-overschakeling een door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (voorheen bekend als managed service identify (MSI)) die wordt vertrouwd door het abonnement.
    - Om de identiteit te maken en deze de juiste rol toe te wijzen (Inzender of Administrator voor gebruikerstoegang in het bronabonnement), moet het account dat u gebruikt om resources toe te voegen *Eigenaars* machtigingen hebben voor het abonnement. [Meer informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over rollen in Azure.
- Het abonnement heeft voldoende quota nodig om de bron resources in de doel regio te maken. Als dat niet het geval is, moet u aanvullende limieten aanvragen. [Meer informatie](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifieer prijzen en kosten voor de doelregio waarnaar u virtuele machines verplaatst. Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om u daarbij te helpen.
    


## <a name="check-vm-requirements"></a>Vereisten voor virtuele machines controleren

1. Controleer of de virtuele machines die u wilt verplaatsen, worden ondersteund.

    - [Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) ondersteunde virtuele Windows-machines.
    - [Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde virtuele Linux-machines en versies van de kernel.
    - Controleer de ondersteunde [compute-](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag-](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)instellingen.
2. Controleer of de virtuele machines die u wilt verplaatsen, zijn ingeschakeld.
3. Zorg ervoor dat Vm's de meest recente vertrouwde basis certificaten en een bijgewerkte certificaatintrekkingslijst (CRL) hebben. 
    - Installeer de meest recente Windows-updates op virtuele Azure-machines waarop Windows wordt uitgevoerd.
    - Op Vm's waarop Linux wordt uitgevoerd, volgt u de richt lijnen voor Linux-distributeurs om te controleren of de computer de meest recente certificaten en CERTIFICAATINTREKKINGSLIJST heeft. 
4. Sta uitgaande connectiviteit vanaf VM's toe:
    - Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze [URL's](support-matrix-move-region-azure-vm.md#url-access) toe
    - Als u regels voor netwerk beveiligingsgroepen (NSG) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze [servicetagregels](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Te verplaatsen resources selecteren

Selecteer de resources die u wilt verplaatsen.

- U kunt elk ondersteund resource type voor alle resource groepen selecteren in de bron regio die u selecteert.
- U kunt resources verplaatsen naar een doel regio in het bron regio abonnement. Als u het abonnement wilt wijzigen, kunt u dat doen nadat de resources zijn verplaatst.

1. Zoek in de Azure-portal naar resource mover. Selecteer vervolgens **Azure Resource Mover** onder **Services**.

    ![Zoeken naar resource verplaatsen](./media/move-region-availability-zone/search.png)

2. Selecteer in **overzicht** aan de **slag**.

    ![Knop om aan de slag te gaan](./media/move-region-availability-zone/get-started.png)

3. Selecteer in **Resources verplaatsen** > **Bron en doel**, het bronabonnement en de regio.
4. Selecteer in **Doel** de regio waarnaar u de virtuele machines wilt verplaatsen. Klik op **Volgende**.

     ![Pagina om het bron-en doel abonnement/de regio in te vullen](./media/move-region-availability-zone/source-target.png)

6. Klik in **Resources die moeten worden verplaatst** op **Resources selecteren**.
7. Selecteer de virtuele machine in **Resources selecteren**. U kunt alleen resources selecteren waarvoor verplaatsing wordt ondersteund. Klik vervolgens op **Gereed**. Klik in **Resources die moeten worden verplaatst** op **Volgende**.

    ![Pagina voor het selecteren van virtuele machines die moeten worden verplaatst](./media/move-region-availability-zone/select-vm.png)
8. Controleer de bron-en doel instellingen in **Controleren en toevoegen**.

    ![Pagina voor het controleren van instellingen en doorgaan met de verplaatsing](./media/move-region-availability-zone/review.png)

9. Klik op **Doorgaan** om te beginnen met toevoegen van de resources.
10. Klik nadat het toevoegen geslaagd is op **Resources toevoegen** in het meldingspictogram.

    ![Bericht in meldingen](./media/move-region-availability-zone/notification.png)

Nadat u op de melding hebt geklikt, worden de resources weer gegeven op de pagina **over verschillende regio's**

> [!NOTE]
> Nadat u op de melding hebt geklikt, worden de resources weer gegeven op de pagina **over verschillende regio's** , in de status in *behandeling voorbereiden* .
> - Als u een resource uit een te verplaatsen verzameling wilt verwijderen, hangt de manier waarop u dat doet af van de plaats waar u zich in het verplaatsingsproces bevindt. [Meer informatie](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

1. Als bij resources het bericht *Afhankelijkheden controleren* staat in de kolom **Problemen**, klikt u op de knop **Afhankelijkheden controleren**. Het validatie proces wordt uitgevoerd.
2. Als er afhankelijkheden worden gevonden, klikt u op **Afhankelijkheden toevoegen**. 
3. Selecteer in **Afhankelijkheden toevoegen** de afhankelijke Resources > **Afhankelijkheden toevoegen**. Controleer de voortgang in de meldingen.

    ![Knop voor het toevoegen van afhankelijkheden](./media/move-region-availability-zone/add-dependencies.png)

3. Voeg indien nodig extra afhankelijkheden toe en valideer afhankelijkheden opnieuw. 

    ![Pagina om aanvullende afhankelijkheden toe te voegen](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Controleer op de pagina **Tussen regio's** of de resources nu de status *Voorbereiding in behandeling* hebben, zonder problemen.

    ![Pagina met resources met de status Voorbereiding in behandeling](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen 

Voordat u Vm's kunt voorbereiden en verplaatsen, moet de bron resource groep aanwezig zijn in de doel regio. 

### <a name="prepare-to-move-the-source-resource-group"></a>De bronresourcegroep voorbereiden voor verplaatsing

Bereid als volgt voor:

1. Selecteer in **Tussen regio's** de bronresourcegroep > **Voorbereiden**.
2. Klik in **Resources voorbereiden** op **Voorbereiden**.

    ![Knop voor het voorbereiden van de bron resource groep](./media/move-region-availability-zone/prepare-resource-group.png)

    Tijdens het voorbereidingsproces worden met Resource Mover ARM-sjablonen (Azure Resource Manager) gegenereerd met behulp van de instellingen voor de resourcegroep. Resources in de resourcegroep worden niet beïnvloed.

> [!NOTE]
>  Nadat de resourcegroep is voorbereid, heeft deze niet de status *Initiëren verplaatsing in behandeling*. 

![Status waarin de status initiate in behandeling wordt weer gegeven](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen

Initieer de verplaatsing als volgt:

1. Selecteer in **Tussen regio's** de resourcegroep > **Verplaatsing initiëren**
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**. De resourcegroep wordt verplaatst naar een status *Initiëren verplaatsing wordt uitgevoerd*.
3. Nadat de verplaatsing is geïnitieerd, wordt de doelresourcegroep gemaakt op basis van de gegenereerde ARM-sjabloon. De bronresourcegroep wordt verplaatst naar een status *Verplaatsing doorvoeren in behandeling*.

![Status van door Voer verplaatsen](./media/move-region-availability-zone/commit-move-pending.png)

Ga als volgt te werk om het verplaatsingsproces door te voeren en te voltooien:

1. In **meerdere regio's** selecteert u de resource groep > **door voeren verplaatsen**
2. Klik in **Resources verplaatsen** op **Doorvoeren**.

> [!NOTE]
> Nadat de verplaatsing is doorgevoerd, bevindt de bronresourcegroep zich in de status *Verwijderen bron in behandeling*.


## <a name="add-a-target-availability-zone"></a>Een doel beschikbaarheids zone toevoegen

Voordat we de rest van de resources verplaatsen, stellen we een doel beschikbaarheids zone in voor de virtuele machine.

1. Klik op de pagina **over verschillende regio's** op de koppeling in de **doel configuratie** kolom van de virtuele machine die u wilt verplaatsen.

    ![VM-eigenschappen](./media/move-region-availability-zone/select-vm-settings.png)

3. Geef in **configuratie-instellingen** de instelling voor de doel-VM op. U kunt als volgt een virtuele machine in de doel regio configureren:
    -  Maak een nieuwe equivalente resource in de doel regio. Met uitzonde ring van de instellingen die u opgeeft, wordt de doel resource gemaakt met dezelfde instellingen als de bron.
    - Selecteer een bestaande virtuele machine in de doel regio. 

4. Selecteer in **zones** de zone waarin u de virtuele machine wilt plaatsen wanneer deze wordt verplaatst.

    Wijzigingen worden alleen aangebracht voor de resource die u wilt bewerken. U moet afhankelijke resources afzonderlijk bijwerken.

5. Geef in **SKU** de [Azure-laag](..//virtual-machines/sizes.md) op die u wilt toewijzen aan de doel-VM.
6. Selecteer in **beschikbaarheidsset** een beschikbaarheidsset als u wilt dat de doel-VM wordt uitgevoerd in een beschikbaarheidsset in de beschikbaarheids zone.
7. Selecteer **Save changes**.

    ![instellingen VM](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Te verplaatsen resources voorbereiden

Nadat de bronresourcegroep is verplaatst, kunt u het verplaatsen van de andere resources voorbereiden.

1. Selecteer in **Tussen regio's** de resources die u wilt voorbereiden. 

    ![Pagina voor het selecteren van andere resources om voor te bereiden](./media/move-region-availability-zone/prepare-other.png)

2. Selecteer **Voorbereiden**. 

> [!NOTE]
> - Tijdens het voorbereidings proces wordt de Azure Site Recovery Mobility-agent geïnstalleerd op virtuele machines, voor replicatie.
> - VM-gegevens worden periodiek naar de doelregio gerepliceerd. Dit heeft geen invloed op de bron-VM.
> - Met Resource Mover worden ARM-sjablonen voor de andere bronresources gegenereerd.
> - Nadat de resources zijn voorbereid, bevinden ze zich in de status *Initiëren verplaatsing in behandeling*.

![Pagina met resources met de status Initiëren verplaatsing in behandeling](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>De verplaatsing initiëren

Nu de resources zijn voorbereid, kunt u de verplaatsing initiëren. 

1. Selecteer in **Tussen regio's** resources met de status *Initiëren verplaatsing in behandeling*. Klik vervolgens op **Start verplaatsen**
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**.

    ![Pagina voor het initiëren van verplaatsen van resources](./media/move-region-availability-zone/initiate-move.png)

3. Controleer de voortgang van de verplaatsing in de meldingenbalk.


> [!NOTE]
> - Voor VM's worden replica-VM's gemaakt in de doelregio. De bron-VM wordt uitgeschakeld en er treedt enige downtime op (meestal een aantal minuten).
> - Met Resource Mover worden andere resources opnieuw gemaakt met behulp van de ARM-sjablonen die zijn voorbereid. Meestal treedt er geen downtime op.
> - Na het voorbereiden van resources, hebben ze een status *door voeren die in behandeling* is.


![Pagina voor het weer geven van resources in de status door Voer verplaatsen in behandeling](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Verwijderen of doorvoeren?

Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt doorvoeren of verwijderen. 

- **Verwijderen**: Mogelijk wilt u een verplaatsing verwijderen als u een test uitvoert en de bronresource niet echt wilt verplaatsen. Als u de verplaatsing negeert, wordt de resource teruggezet in de status *Initiëren verplaatsing in behandeling*.
- **Doorvoeren**: Met doorvoeren wordt de verplaatsing naar de doelregio voltooid. Na het doorvoeren heeft een bronresource de status *Verwijderen bron in behandeling* en kunt u besluiten of u deze wilt verwijderen.

## <a name="discard-the-move"></a>De verplaatsing verwijderen 

U kunt de verplaatsing als volgt verwijderen:

1. Selecteer in **Tussen regio's** resources met de status *Verplaatsing doorvoeren in behandeling* en klik op **Verplaatsing verwijderen**.
2. Klik in **Verplaatsing verwijderen** op **Verwijderen**.
3. Controleer de voortgang van de verplaatsing in de meldingenbalk.
 

> [!NOTE]
> Voor virtuele machines, na het verwijderen van resources, hebben ze de status *initiëren in behandeling* .

## <a name="commit-the-move"></a>De verplaatsing doorvoeren

Als u het verplaatsingsproces wilt voltooien, moet u de verplaatsing doorvoeren. 

1. Selecteer in **Tussen regio's** resources met de status *Verplaatsing doorvoeren in behandeling* en klik op **Verplaatsing doorvoeren**.
2. Klik in **Resources doorvoeren** op **Doorvoeren**.

    ![Pagina voor het doorvoeren van resources om de verplaatsing te voltooien](./media/move-region-availability-zone/commit-resources.png)

3. Controleer de voortgang van het doorvoeren in de meldingenbalk.

> [!NOTE]
> - Na het doorvoeren van de verplaatsing, worden VM's niet meer gerepliceerd. De bron-VM wordt door de doorvoer niet beïnvloed.
> - Doorvoeren heeft geen invloed op bronnetwerkresources.
> - Nadat de verplaatsing is doorgevoerd, bevinden de resources zich in de status *Verwijderen bron in behandeling*.

![Pagina met resources met de status *Verwijderen bron in behandeling*](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Instellingen configureren na de verplaatsing

De Mobility-service wordt niet automatisch van VM's verwijderd. Verwijder de service handmatig of laat deze staan als u van plan bent de server opnieuw te verplaatsen.


## <a name="delete-source-resources-after-commit"></a>Bronresources verwijderen na doorvoeren

Na de verplaatsing kunt u desgewenst de resources in de bronregio verwijderen.

1. Klik in **Tussen regio's** op de naam van elke bronresource die u wilt verwijderen.
2. Selecteer **Verwijderen** op de eigenschappenpagina van elke resource.

## <a name="delete-additional-resources-created-for-move"></a>Aanvullende resources verwijderen die zijn gemaakt om te worden verplaatst

Na de verplaatsing kunt u handmatig de verzameling voor verplaatsen en de Site Recovery-resources die zijn gemaakt, verwijderen.

- De verzameling voor verplaatsen wordt standaard verborgen. Als u deze wilt zien, moet u verborgen resources inschakelen.
- De cacheopslag heeft een vergrendeling die moet worden verwijderd voordat de cacheopslag kan worden verwijderd.

Verwijder deze als volgt: 

1. Zoek de resources in resourcegroep ```RegionMoveRG-<sourceregion>-<target-region>```.
2. Controleer of alle virtuele machines en andere bronresources in de bronregio zijn verplaatst of verwijderd. Zo weet u zeker dat er geen resources in behandeling zijn die hiervan gebruikmaken.
2. De resources verwijderen:

    - De naam van de verzameling voor verplaatsen is ```movecollection-<sourceregion>-<target-region>```.
    - De naam van het cacheopslagaccount is ```resmovecache<guid>```
    - De naam van de kluis is ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over](about-move-process.md) het verplaatsings proces.