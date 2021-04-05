---
title: Resources verplaatsen naar een andere regio met Azure resource-overdrijfing
description: Meer informatie over het verplaatsen van resources binnen een resource groep naar een andere regio met Azure resource-overzetten.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 79224c14fc5182df7a699864af3d78c9be36259f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97797268"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Resources verplaatsen tussen regio's (van resource groep)

In dit artikel vindt u informatie over het verplaatsen van resources in een specifieke resource groep naar een andere Azure-regio. In de resource groep selecteert u de resources die u wilt verplaatsen. Vervolgens verplaatst u deze met [Azure resource](overview.md)-overschakeling.

> [!IMPORTANT]
> Azure Resource Mover is momenteel beschikbaar als openbare preview-versie.


## <a name="prerequisites"></a>Vereisten

- U hebt *eigenaars* toegang nodig voor het abonnement waarin resources zich bevinden die u wilt verplaatsen.
    - De eerste keer dat u een resource toevoegt voor een specifieke bron-en doel toewijzing in een Azure-abonnement, maakt resource-overschakeling een door het [systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (voorheen bekend als managed service identify (MSI)) die wordt vertrouwd door het abonnement.
    - Om de identiteit te maken en deze de juiste rol toe te wijzen (Inzender of Administrator voor gebruikerstoegang in het bronabonnement), moet het account dat u gebruikt om resources toe te voegen *Eigenaars* machtigingen hebben voor het abonnement. [Meer informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over rollen in Azure.
- Het abonnement heeft voldoende quota nodig om de bron resources in de doel regio te maken. Als dat niet het geval is, moet u aanvullende limieten aanvragen. [Meer informatie](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifieer prijzen en kosten voor de doelregio waarnaar u virtuele machines verplaatst. Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om u daarbij te helpen.
- Controleer of de resources die u wilt verplaatsen, worden ondersteund door resource-overschakeling:
    - Azure-VM's en gekoppelde schijven
    - NIC’s
    - Beschikbaarheidssets
    - Virtuele netwerken van Azure.
    - Openbare IP-adressen
    - Netwerkbeveiligingsgroepen (NSG's)
    - Interne en openbare load balancers
    - Azure SQL-databases en elastische pools


## <a name="check-vm-requirements"></a>Vereisten voor virtuele machines controleren

1. Controleer of de virtuele machines die u wilt verplaatsen, worden ondersteund.

    - [Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) ondersteunde virtuele Windows-machines.
    - [Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde virtuele Linux-machines en versies van de kernel.
    - Controleer de ondersteunde [compute-](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag-](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)instellingen.
2. Zorg ervoor dat Vm's de meest recente vertrouwde basis certificaten en een bijgewerkte certificaatintrekkingslijst (CRL) hebben. 
    - Installeer de meest recente Windows-updates op virtuele Azure-machines waarop Windows wordt uitgevoerd.
    - Op Vm's waarop Linux wordt uitgevoerd, volgt u de richt lijnen voor Linux-distributeurs om te controleren of de computer de meest recente certificaten en CERTIFICAATINTREKKINGSLIJST heeft. 
3. Sta uitgaande connectiviteit vanaf VM's toe:
    - Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze [URL's](support-matrix-move-region-azure-vm.md#url-access) toe
    - Als u regels voor netwerk beveiligingsgroepen (NSG) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze [servicetagregels](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Te verplaatsen resources selecteren

Selecteer de resources die u wilt verplaatsen. U kunt resources verplaatsen naar een doel regio in het bron regio abonnement. Als u het abonnement wilt wijzigen, kunt u dat doen nadat de resources zijn verplaatst.

> [!NOTE]
>  Selecteer geen gekoppelde schijven, anders mislukt de bewerking. Gekoppelde schijven worden automatisch opgenomen in een VM-verplaatsing.

1. Open de relevante resource groep in het Azure Portal.
2. Op de pagina resource groep selecteert u de resources die u wilt verplaatsen.
3. Selecteer **verplaatsing verplaatsen**  >  **naar een andere regio**.

    ![Selectie voor het verplaatsen van resources naar een andere regio](./media/move-region-within-resource-group/select-move-region.png)
    
4. Selecteer in **bron + bestemming** de doel regio waarnaar u de resources wilt verplaatsen. Selecteer vervolgens **Volgende**.


    ![Bron-en doel pagina voor het selecteren van de doel regio](./media/move-region-within-resource-group/source-target.png)


7. Selecteer **volgende** in **resources die moeten worden verplaatst**.  
8. Selecteer in **resources selecteren** de resource die u wilt verplaatsen. U kunt alleen resources selecteren waarvoor verplaatsing wordt ondersteund. Selecteer vervolgens **Done**.
9. Selecteer **volgende** bij **resources verplaatsen**. 
10. Controleer de bron-en doel gegevens in **beoordeling + toevoegen**.
11. Controleer of u begrijpt dat meta gegevens over de resources die worden verplaatst, worden opgeslagen in een resource groep die voor dit doel is gemaakt en dat u het verplaatsen van resources toestaat een door het systeem beheerde identiteit te maken voor toegang tot de abonnements bronnen.
1. Selecteer **door gaan** om te beginnen met het toevoegen van de resources.

    ![Overzichts pagina om de details te controleren en door te gaan met verplaatsen](./media/move-region-within-resource-group/summary.png)    

11. De bewerking resource toevoegen wordt gestart. Wanneer de bewerking is voltooid, tonen de meldingen dat de resources zijn toegevoegd en is de implementatie geslaagd.
13. Selecteer in de meldingen **resources toevoegen voor verplaatsen**.

    ![Bericht weer gegeven in meldingen](./media/move-region-within-resource-group/notification.png)    


14. Nadat u de melding hebt geselecteerd, worden de geselecteerde resources toegevoegd aan een verzameling verplaatsen in de Azure resource-overschakeling hub.  Resource-overschakeling helpt u bij het controleren van afhankelijkheden en het verplaatsen van resources naar de doel regio.

## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

Resources die u verplaatst, worden weer gegeven op de pagina **over verschillende regio's** , in *afwachting van voor bereide* status. Begin validatie als volgt:

1. Als resources een bericht voor het *valideren van afhankelijkheden* weer geven in de kolom **kwesties** , selecteert u de knop **afhankelijkheden valideren** . Het validatieproces wordt gestart.

    ![Knop voor het valideren van afhankelijkheden](./media/move-region-within-resource-group/validate-dependencies.png)

2. Als er afhankelijkheden worden gevonden, selecteert u **afhankelijkheden toevoegen**. 
3. Selecteer in **Afhankelijkheden toevoegen** de afhankelijke Resources > **Afhankelijkheden toevoegen**. Controleer de voortgang in de meldingen.

    ![Knop voor het toevoegen van afhankelijkheden](./media/move-region-within-resource-group/add-dependencies.png)

3. Voeg indien nodig extra afhankelijkheden toe en valideer afhankelijkheden als dat nodig is. Selecteer **vernieuwen** om ervoor te zorgen dat resources een actuele status weer geven.

4. Controleer op de pagina **Tussen regio's** of de resources nu de status *Voorbereiding in behandeling* hebben, zonder problemen.

    ![Pagina om de status voor bereiding in behandeling weer te geven voor alle resources](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen 

Voordat u resources kunt voorbereiden en verplaatsen, moet de bron resource groep aanwezig zijn in de doel regio. 

### <a name="prepare-to-move-the-source-resource-group"></a>De bronresourcegroep voorbereiden voor verplaatsing

Bereid als volgt voor:

1. Selecteer in **Tussen regio's** de bronresourcegroep > **Voorbereiden**.
2. In **resources voorbereiden**, Selecteer voor **bereiding**.
1. 
    ![Knop voor het voorbereiden van de bron resource groep](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Tijdens het voorbereidingsproces worden met Resource Mover ARM-sjablonen (Azure Resource Manager) gegenereerd met behulp van de instellingen voor de resourcegroep. Resources in de resourcegroep worden niet beïnvloed.
    
> [!NOTE]
>  Nadat de resourcegroep is voorbereid, heeft deze niet de status *Initiëren verplaatsing in behandeling*. Vernieuwen om de meest recente status weer te geven.

![Status waarin de status initiate in behandeling wordt weer gegeven](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen

Initieer de verplaatsing als volgt:

1. Selecteer in **Tussen regio's** de resourcegroep > **Verplaatsing initiëren**
2. **resources verplaatsen**: Selecteer **initiëren verplaatsen**. De resourcegroep wordt verplaatst naar een status *Initiëren verplaatsing wordt uitgevoerd*.
3. Nadat de verplaatsing is geïnitieerd, wordt de doelresourcegroep gemaakt op basis van de gegenereerde ARM-sjabloon. De bronresourcegroep wordt verplaatst naar een status *Verplaatsing doorvoeren in behandeling*.

![Status van door Voer verplaatsen](./media/move-region-availability-zone/commit-move-pending.png)

Ga als volgt te werk om het verplaatsingsproces door te voeren en te voltooien:

1. In **meerdere regio's** selecteert u de resource groep > **door voeren verplaatsen**
2. **resources verplaatsen**, selecteer **door voeren**.

> [!NOTE]
> Nadat de verplaatsing is doorgevoerd, bevindt de bronresourcegroep zich in de status *Verwijderen bron in behandeling*.

## <a name="modify-target-settings"></a>Doelinstelling wijzigen

Als u een bron resource niet wilt verplaatsen, kunt u een van de volgende handelingen uitvoeren:

- Maak een resource in de doel regio met dezelfde naam en instellingen als de resource in de bron regio.
- Maak een nieuwe equivalente resource in de doel regio. Met uitzonde ring van de instellingen die u opgeeft, wordt de doel resource gemaakt met dezelfde instellingen als de bron.
- Gebruik een bestaande resource in de doel regio.

Wijzig een instelling als volgt:

1. Als u een instelling wilt wijzigen, selecteert u de vermelding in de kolom **doel configuratie** voor de resource.
2. Geef op de pagina **doel configuratie** de doel instellingen op die u wilt gebruiken.
    Wijzigingen worden alleen aangebracht voor de resource die u wilt bewerken. U moet afhankelijke resources afzonderlijk bijwerken.   
    
De exacte instellingen die u wijzigt, zijn afhankelijk van het resource type. Meer [informatie](modify-target-settings.md) over het bewerken van doel instellingen.

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

1. Selecteer in **Tussen regio's** resources met de status *Initiëren verplaatsing in behandeling*. Selecteer vervolgens **initiëren verplaatsen**.
2. In **resources verplaatsen** selecteert u **verplaatsen starten**.

    ![Selecteren voor de knop verplaatsen initiëren](./media/move-region-within-resource-group/initiate-move.png)

3. Controleer de voortgang van de verplaatsing in de meldingenbalk.


> [!NOTE]
> - Voor VM's worden replica-VM's gemaakt in de doelregio. De bron-VM wordt uitgeschakeld en er treedt enige downtime op (meestal een aantal minuten).<br/>
> - Met Resource Mover worden andere resources opnieuw gemaakt met behulp van de ARM-sjablonen die zijn voorbereid. Meestal treedt er geen downtime op.<br/> 
> - Voor load balancers worden NAT-regels niet gekopieerd. Maak ze in de doel regio nadat u de verplaatsing hebt doorgevoerd.
> - Voor open bare IP-adressen wordt het DNS-naam label niet gekopieerd. Maak het label opnieuw nadat u de verplaatsing hebt doorgevoerd.
> - Na het voorbereiden van resources, hebben ze een status *door voeren die in behandeling* is.


## <a name="discard-or-commit"></a>Verwijderen of doorvoeren?

Na de eerste verplaatsing kunt u beslissen of u de verplaatsing wilt doorvoeren of verwijderen. 

- **Verwijderen**: Mogelijk wilt u een verplaatsing verwijderen als u een test uitvoert en de bronresource niet echt wilt verplaatsen. Als u de verplaatsing negeert, wordt de resource teruggezet in de status *Initiëren verplaatsing in behandeling*.
- **Doorvoeren**: Met doorvoeren wordt de verplaatsing naar de doelregio voltooid. Na het doorvoeren heeft een bronresource de status *Verwijderen bron in behandeling* en kunt u besluiten of u deze wilt verwijderen.


## <a name="discard-the-move"></a>De verplaatsing verwijderen 

U kunt de verplaatsing als volgt verwijderen:

1. In **meerdere regio's** selecteert u resources met status *doorvoer verplaatsen in behandeling* en selecteert u **verplaatsen negeren**.
2. Selecteer **negeren** bij **verplaatsen negeren**.
3. Controleer de voortgang van de verplaatsing in de meldingenbalk.
4. Wanneer de meldingen aangeven dat de verplaatsing is geslaagd, selecteert u **vernieuwen**. 

> [!NOTE]
> Voor virtuele machines, na het verwijderen van resources, hebben ze de status *initiëren in behandeling* .

## <a name="commit-the-move"></a>De verplaatsing doorvoeren

Als u het verplaatsingsproces wilt voltooien, moet u de verplaatsing doorvoeren. 


1. In **meerdere regio's** selecteert u resources met status *doorvoer verplaatsen in behandeling* en selecteert u **verplaatsen door voeren**.
2. In **resources door voeren** selecteert u **door voeren**.

    ![Pagina voor het doorvoeren van resources om de verplaatsing te voltooien](./media/move-region-within-resource-group/commit-resources.png)

3. Controleer de voortgang van het doorvoeren in de meldingenbalk.

> [!NOTE]
> - Na het doorvoeren van de verplaatsing, worden VM's niet meer gerepliceerd. De bron-VM wordt door de doorvoer niet beïnvloed.
> - Doorvoeren heeft geen invloed op bronnetwerkresources.
> - Nadat de verplaatsing is doorgevoerd, bevinden de resources zich in de status *Verwijderen bron in behandeling*.

## <a name="configure-settings-after-the-move"></a>Instellingen configureren na de verplaatsing

1. Omdat de DNS-namen van labels niet worden gekopieerd voor open bare IP-adressen, gaat u nadat de verplaatsing is voltooid naar de doel resources en werkt u het label bij. 
2. Voor interne load balancers, omdat NAT-regels niet worden gekopieerd over, navigeert u naar de resources die zijn gemaakt in de doel regio en werkt u de NAT-regels bij.
3. De Mobility-service wordt niet automatisch van VM's verwijderd. Verwijder de service handmatig of laat deze staan als u van plan bent de server opnieuw te verplaatsen.
## <a name="delete-source-resources-after-commit"></a>Bronresources verwijderen na doorvoeren

Na de verplaatsing kunt u desgewenst de resources in de bronregio verwijderen. 

1. In **meerdere regio's** selecteert u de naam van elke bron resource die u wilt verwijderen.
2. Selecteer **Verwijderen** op de eigenschappenpagina van elke resource.

## <a name="delete-additional-resources-created-for-move"></a>Aanvullende resources verwijderen die zijn gemaakt om te worden verplaatst

Na de verplaatsing kunt u handmatig de verzameling voor verplaatsen en de Site Recovery-resources die zijn gemaakt, verwijderen.

- De verzameling voor verplaatsen wordt standaard verborgen. Als u deze wilt zien, moet u verborgen resources inschakelen.
- De cacheopslag heeft een vergrendeling die moet worden verwijderd voordat de cacheopslag kan worden verwijderd.

Verwijder deze als volgt: 

1. Zoek de resources in de resource groep ```RegionMoveRG-<sourceregion>-<target-region>``` in de bron regio.
2. Controleer of alle virtuele machines en andere bron bronnen in de verplaatsings verzameling zijn verplaatst/verwijderd. Zo weet u zeker dat er geen resources in behandeling zijn die hiervan gebruikmaken.
2. De resources verwijderen:

    - De naam van de verzameling voor verplaatsen is ```movecollection-<sourceregion>-<target-region>```.
    - De naam van het cacheopslagaccount is ```resmovecache<guid>```
    - De naam van de kluis is ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Volgende stappen


[Meer informatie over](about-move-process.md) het verplaatsings proces.