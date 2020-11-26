---
title: Virtuele Azure-machines met Azure Resource Mover tussen regio's verplaatsen
description: Leer hoe u virtuele Azure-machines naar een andere regio kunt verplaatsen met Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 6f21db00ecc9ff2668698f53a4d20f5bae525721
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95520438"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Zelfstudie: Virtuele Azure-machines tussen regio's verplaatsen

In dit artikel leert u hoe u virtuele Azure-machines en gerelateerde netwerk-/opslagresources kunt verplaatsen naar een andere Azure-regio met behulp van [Azure Resource Mover](overview.md).

> [!NOTE]
> Azure Resource Mover is momenteel beschikbaar als openbare preview-versie.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De vereisten controleren.
> * De resources selecteren die u wilt verplaatsen.
> * Resourceafhankelijkheden oplossen.
> * De bronresourcegroep voorbereiden en verplaatsen. 
> * De overige resources voorbereiden en verplaatsen.
> * Beslissen of u de verplaatsing wilt doorvoeren of niet. 
> * Desgewenst resources in de bronregio verwijderen na de verplaatsing.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint. Meld u vervolgens aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

-  Controleer of u *Eigenaar*-toegang hebt voor het abonnement dat de resources bevat die u wilt verplaatsen.
    - De eerste keer dat u een resource toevoegt voor een specifiek bron- en doelpaar in een Azure-abonnement maakt Resource Mover een [door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (vroeger MSI genoemd (Managed Service Identity)) die door het abonnement wordt vertrouwd.
    - Om de identiteit te maken en deze de juiste rol toe te wijzen (Inzender of Administrator voor gebruikerstoegang in het bronabonnement), moet het account dat u gebruikt om resources toe te voegen *Eigenaars* machtigingen hebben voor het abonnement. [Meer informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over rollen in Azure.
- Het abonnement moet voldoende quota hebben om de resources die u verplaatst in de doelregio te maken. Als de quota onvoldoende zijn, moet u [hogere limieten aanvragen](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifieer prijzen en kosten voor de doelregio waarnaar u virtuele machines verplaatst. Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om u daarbij te helpen.
    

## <a name="check-vm-requirements"></a>Vereisten voor virtuele machines controleren

1. Controleer of de virtuele machines die u wilt verplaatsen, worden ondersteund.

    - [Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) ondersteunde virtuele Windows-machines.
    - [Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde virtuele Linux-machines en versies van de kernel.
    - Controleer de ondersteunde [compute-](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag-](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)instellingen.
2. Controleer of de virtuele machines die u wilt verplaatsen, zijn ingeschakeld.
3. Zorg ervoor dat VM's de meest recente vertrouwde basiscertificaten en een bijgewerkte certificaatintrekkingslijst hebben. Om dit te doen:
    - Installeert u de meest recente Windows-updates op virtuele Windows-machines.
    - Volgt u de richtlijnen van de distributeurs op virtuele Linux-machines zodat computers over de nieuwste certificaten en certificaatintrekkingslijst beschikken. 
4. Sta uitgaande connectiviteit vanaf VM's toe:
    - Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze [URL's](support-matrix-move-region-azure-vm.md#url-access) toe
    - Als u regels voor netwerk beveiligingsgroepen (NSG) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze [servicetagregels](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Resources selecteren 

Selecteer de resources die u wilt verplaatsen.

- Alle ondersteunde resourcetypen in resourcegroepen binnen de geselecteerde bronregio worden weergegeven.
- Resources die al zijn toegevoegd voor het verplaatsen tussen regio's worden niet weergegeven.
- U verplaatst resources naar een doelregio in hetzelfde abonnement als de bronregio. Als u het abonnement wilt wijzigen, kunt u dat doen nadat de resources zijn verplaatst.

1. Zoek in de Azure-portal naar *resource mover*. Selecteer vervolgens **Azure Resource Mover** onder **Services**.

    ![Zoekresultaten voor resource mover in de Azure-portal](./media/tutorial-move-region-virtual-machines/search.png)

2. Klik in **Overzicht** op **Aan de slag**.

    ![Knop voor het toevoegen van naar een andere regio te verplaatsen resources](./media/tutorial-move-region-virtual-machines/get-started.png)

3. Selecteer in **Resources verplaatsen** > **Bron en doel**, het bronabonnement en de regio.
4. Selecteer in **Doel** de regio waarnaar u de virtuele machines wilt verplaatsen. Klik op **Volgende**.

    ![Pagina voor het selecteren van bron- en doelregio](./media/tutorial-move-region-virtual-machines/source-target.png)

6. Klik in **Resources die moeten worden verplaatst** op **Resources selecteren**.
7. Selecteer de virtuele machine in **Resources selecteren**. U kunt alleen resources selecteren [waarvoor verplaatsing wordt ondersteund](#check-vm-requirements). Klik vervolgens op **Gereed**.

    ![Pagina voor het selecteren van virtuele machines die moeten worden verplaatst](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  Klik in **Resources die moeten worden verplaatst** op **Volgende**.
9. Controleer de bron-en doel instellingen in **Controleren en toevoegen**. 

    ![Pagina voor het controleren van instellingen en doorgaan met de verplaatsing](./media/tutorial-move-region-virtual-machines/review.png)
10. Klik op **Doorgaan** om te beginnen met toevoegen van de resources.
11. Klik nadat het toevoegen geslaagd is op **Resources toevoegen** in het meldingspictogram.
12. Controleer nadat u op de melding hebt geklikt de resources op de pagina **Tussen regio's**.

> [!NOTE]
> - Toegevoegde resources hebben de status *Voorbereiding in behandeling*.
> - Als u een resource uit een te verplaatsen verzameling wilt verwijderen, hangt de manier waarop u dat doet af van de plaats waar u zich in het verplaatsingsproces bevindt. [Meer informatie](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

1. Als bij resources het bericht *Afhankelijkheden controleren* staat in de kolom **Problemen**, klikt u op de knop **Afhankelijkheden controleren**. Het validatieproces wordt gestart.
2. Als er afhankelijkheden worden gevonden, klikt u op **Afhankelijkheden toevoegen**. 
3. Selecteer in **Afhankelijkheden toevoegen** de afhankelijke Resources > **Afhankelijkheden toevoegen**. Controleer de voortgang in de meldingen.

    ![Afhankelijkheden toevoegen](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Voeg indien nodig extra afhankelijkheden toe en valideer afhankelijkheden opnieuw. 
    ![Pagina om aanvullende afhankelijkheden toe te voegen](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)

4. Controleer op de pagina **Tussen regio's** of de resources nu de status *Voorbereiding in behandeling* hebben, zonder problemen.

    ![Pagina met resources met de status Voorbereiding in behandeling](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

> [!NOTE]
> Als u de doelinstellingen wilt bewerken voordat u begint met verplaatsen, selecteert u de koppeling in de kolom **Doelconfiguratie** voor de resource en bewerkt u de instellingen. Als u de instellingen van de doel-VM bewerkt, mag de grootte van de doel-VM niet kleiner zijn dan de grootte van de bron-VM.  

## <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen 

Voordat u virtuele machines kunt voorbereiden en verplaatsen, moet de resourcegroep van de virtuele machine aanwezig zijn in de doelregio. 

### <a name="prepare-to-move-the-source-resource-group"></a>De bronresourcegroep voorbereiden voor verplaatsing

Tijdens het voorbereidingsproces worden met Resource Mover ARM-sjablonen (Azure Resource Manager) gegenereerd met behulp van de instellingen voor de resourcegroep. Resources in de resourcegroep worden niet beïnvloed.

Bereid als volgt voor:

1. Selecteer in **Tussen regio's** de bronresourcegroep > **Voorbereiden**.
2. Klik in **Resources voorbereiden** op **Voorbereiden**.

    ![Resourcegroep voorbereiden](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Nadat de resourcegroep is voorbereid, heeft deze niet de status *Initiëren verplaatsing in behandeling*. 

 
### <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen

Initieer de verplaatsing als volgt:

1. Selecteer in **Tussen regio's** de resourcegroep > **Verplaatsing initiëren**
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**. De resourcegroep wordt verplaatst naar een status *Initiëren verplaatsing wordt uitgevoerd*.
3. Nadat de verplaatsing is geïnitieerd, wordt de doelresourcegroep gemaakt op basis van de gegenereerde ARM-sjabloon. De bronresourcegroep wordt verplaatst naar een status *Verplaatsing doorvoeren in behandeling*.

    ![Klik op de knop Verplaatsing initiëren](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Ga als volgt te werk om het verplaatsingsproces door te voeren en te voltooien:

1. Selecteer in **Tussen regio's** de resourcegroep > **Verplaatsing doorvoeren**.
2. Klik in **Resources verplaatsen** op **Doorvoeren**.

> [!NOTE]
> Nadat de verplaatsing is doorgevoerd, bevindt de bronresourcegroep zich in de status *Verwijderen bron in behandeling*.

## <a name="prepare-resources-to-move"></a>Te verplaatsen resources voorbereiden

Nadat de bronresourcegroep is verplaatst, kunt u het verplaatsen van de andere resources voorbereiden.

1. Selecteer in **Tussen regio's** de resources die u wilt voorbereiden. 

    ![Pagina voor het selecteren van andere resources om voor te bereiden](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Selecteer **Voorbereiden**. 

> [!NOTE]
> - Tijdens het voorbereidingsproces wordt de Azure Site Recovery Mobility-agent op VM's geïnstalleerd om ze te repliceren.
> - VM-gegevens worden periodiek naar de doelregio gerepliceerd. Dit heeft geen invloed op de bron-VM.
> - Met Resource Mover worden ARM-sjablonen voor de andere bronresources gegenereerd.
> - Nadat de resources zijn voorbereid, bevinden ze zich in de status *Initiëren verplaatsing in behandeling*.

![Pagina met resources met de status Initiëren verplaatsing in behandeling](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>De verplaatsing initiëren

Nu de resources zijn voorbereid, kunt u de verplaatsing initiëren. 

1. Selecteer in **Tussen regio's** resources met de status *Initiëren verplaatsing in behandeling*. Klik op **Verplaatsing initiëren**.
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**.

    ![Klik op de knop Verplaatsen initiëren](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Controleer de voortgang van de verplaatsing in de meldingenbalk.

> [!NOTE]
> - Voor VM's worden replica-VM's gemaakt in de doelregio. De bron-VM wordt uitgeschakeld en er treedt enige downtime op (meestal een aantal minuten).
> - Met Resource Mover worden andere resources opnieuw gemaakt met behulp van de ARM-sjablonen die zijn voorbereid. Meestal treedt er geen downtime op.
> - Nadat de resources zijn verplaatst, bevinden ze zich in de status *Verplaatsing doorvoeren in behandeling*.

![Pagina met resources met de status *Verwijderen bron in behandeling*](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


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
> Nadat de resources zijn verwijderd, bevinden de VM's zich in de status *Initiëren verplaatsing in behandeling*.

## <a name="commit-the-move"></a>De verplaatsing doorvoeren

Als u het verplaatsingsproces wilt voltooien, moet u de verplaatsing doorvoeren. 

1. Selecteer in **Tussen regio's** resources met de status *Verplaatsing doorvoeren in behandeling* en klik op **Verplaatsing doorvoeren**.
2. Klik in **Resources doorvoeren** op **Doorvoeren**.

    ![Pagina voor het doorvoeren van resources om de verplaatsing te voltooien](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Controleer de voortgang van het doorvoeren in de meldingenbalk.

> [!NOTE]
> - Na het doorvoeren van de verplaatsing, worden VM's niet meer gerepliceerd. De bron-VM wordt door de doorvoer niet beïnvloed.
> - Doorvoeren heeft geen invloed op bronnetwerkresources.
> - Nadat de verplaatsing is doorgevoerd, bevinden de resources zich in de status *Verwijderen bron in behandeling*.

![Pagina met resources met de status *Verwijderen bron in behandeling*](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Instellingen configureren na de verplaatsing

- De Mobility-service wordt niet automatisch van VM's verwijderd. Verwijder de service handmatig of laat deze staan als u van plan bent de server opnieuw te verplaatsen.
- Wijzig de regels voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) na de verplaatsing.

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

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Virtuele Azure-machines naar een andere Azure-regio verplaatst.
> * De resources die zijn gekoppeld aan VM's naar een andere regio verplaatst.

We gaan nu proberen de Azure SQL-databases en elastische pools naar een andere regio te verplaatsen.

> [!div class="nextstepaction"]
> [Azure SQL-resources verplaatsen](./tutorial-move-region-sql.md)