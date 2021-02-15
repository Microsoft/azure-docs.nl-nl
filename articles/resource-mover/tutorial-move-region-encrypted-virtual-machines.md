---
title: Versleutelde Azure-Vm's verplaatsen naar verschillende regio's met Azure resource-overdrijfing
description: Meer informatie over het verplaatsen van versleutelde virtuele Azure-machines naar een andere regio met Azure resource verhuizer
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361006"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Zelf studie: versleutelde Azure-Vm's verplaatsen over regio's

In dit artikel vindt u informatie over het verplaatsen van versleutelde virtuele Azure-machines naar een andere Azure-regio met [Azure resource verhuizer](overview.md). Wat betekent het volgende:

- Vm's met schijven waarvoor Azure Disk Encryption is ingeschakeld. [Meer informatie](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Of Vm's die gebruikmaken van door de klant beheerde sleutels (CMKs) voor versleuteling-at-rest (versleuteling aan server zijde). [Meer informatie](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleer de vereisten. 
> * Voor Vm's met Azure Disk Encryption is ingeschakeld, kopieer sleutels en geheimen van de sleutel kluis van het bron gebied naar de sleutel kluis van de doel regio.
> * Bereid Vm's voor door ze te verplaatsen en selecteer resources in de bron regio die u wilt verplaatsen.
> * Resourceafhankelijkheden oplossen.
> * Wijs de doel sleutel kluis hand matig toe voor Vm's waarvoor Azure Disk Encryption is ingeschakeld. Voor vm's die versleuteling aan de server zijde gebruiken met door de klant beheerde sleutels, wijst u hand matig een schijf versleutelings in de doel regio toe.
> * Verplaats de sleutel kluis en/of de schijf versleutelings.
> * De bronresourcegroep voorbereiden en verplaatsen. 
> * De overige resources voorbereiden en verplaatsen.
> * Beslissen of u de verplaatsing wilt doorvoeren of niet. 
> * Desgewenst resources in de bronregio verwijderen na de verplaatsing.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken de standaardopties. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint. Meld u vervolgens aan bij de [Azure-portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

**Vereiste** |**Details**
--- | ---
**Abonnements machtigingen** | Controleer of u *Eigenaar*-toegang hebt voor het abonnement dat de resources bevat die u wilt verplaatsen.<br/><br/> **Waarom heb ik de eigenaar toegang nodig?** De eerste keer dat u een resource toevoegt voor een specifiek bron- en doelpaar in een Azure-abonnement maakt Resource Mover een [door het systeem toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (vroeger MSI genoemd (Managed Service Identity)) die door het abonnement wordt vertrouwd. Als u de identiteit wilt maken en de vereiste rol (Inzender en beheerder voor gebruikers toegang in het bron abonnement) wilt toewijzen, moet het account dat u gebruikt om resources toe te voegen *eigenaars* machtigingen voor het abonnement hebben. [Meer informatie](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) over rollen in Azure.
**VM-ondersteuning** | Controleer of de virtuele machines die u wilt verplaatsen, worden ondersteund.<br/><br/> - [Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) de ondersteunde Windows-vm's.<br/><br/> - [Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde vm's en kernel-versies van Linux.<br/><br/> -Ondersteunde instellingen voor [Compute](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) controle.
**Vereisten voor de sleutel kluis (Azure Disk Encryption)** | Als u Azure Disk Encryption voor virtuele machines hebt ingeschakeld, moet u naast de sleutel kluis in de bron regio een sleutel kluis in de doel regio hebben. [Een sleutel kluis maken](../key-vault/general/quick-create-portal.md).<br/><br/> Voor de sleutel kluizen in de bron-en doel regio hebt u de volgende machtigingen nodig:<br/><br/> -Belangrijkste machtigingen: bewerkingen voor sleutel beheer (Get, List); Cryptografische bewerkingen (ontsleutelen en versleutelen).<br/><br/> -Geheime machtigingen: geheime beheer bewerkingen (Get, List en set)<br/><br/> -Certificaat (list en Get).
**Schijf encryptie set (versleuteling aan server zijde met CMK)** | Als u virtuele machines gebruikt met versleuteling aan de server zijde met behulp van een CMK, moet u naast de schijf versleuteling die in de bron regio is ingesteld, een schijf versleuteling instellen in de doel regio. [Maak een schijf versleutelings](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Verplaatsen tussen regio's wordt niet ondersteund als u HSM-sleutels gebruikt voor door de klant beheerde sleutels.
**Quotum voor doel regio's** | Het abonnement moet voldoende quota hebben om de resources die u verplaatst in de doelregio te maken. Als de quota onvoldoende zijn, moet u [hogere limieten aanvragen](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Kosten voor de doel regio** | Verifieer prijzen en kosten voor de doelregio waarnaar u virtuele machines verplaatst. Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om u daarbij te helpen.


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>Gebruikers machtigingen voor de sleutel kluis voor VM'S controleren met behulp van Azure Disk Encryption (ADE)

Als u virtuele machines verplaatst waarvoor Azure Disk Encryption is ingeschakeld, moet u een script uitvoeren zoals [hieronder](#copy-the-keys-to-the-destination-key-vault) wordt vermeld, waarvoor de gebruiker die het script uitvoert, over de juiste machtigingen moet beschikken. Raadpleeg de onderstaande tabel voor meer informatie over de benodigde machtigingen. De opties voor het wijzigen van de machtigingen kunt u vinden door te navigeren naar de sleutel kluis in de Azure Portal onder **instellingen** de optie **toegangs beleid**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Om het toegangs beleid voor sleutel kluizen te openen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Als er geen gebruikers machtigingen zijn, selecteert u **toegangs beleid toevoegen** en geeft u de machtigingen op. Als het gebruikers account al een beleid heeft, stelt u onder **gebruiker** de machtigingen in volgens de onderstaande tabel.

Virtuele Azure-machines die gebruikmaken van ADE kunnen de volgende variaties hebben en de machtigingen moeten dienovereenkomstig worden ingesteld voor relevante onderdelen.
- Standaard optie waarbij de schijf wordt versleuteld met alleen geheimen
- Beveiliging is toegevoegd met behulp van [Key Encryption Key](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-keyvault"></a>Sleutel kluis bron regio

De onderstaande machtigingen moeten worden ingesteld voor de gebruiker die het script uitvoert 

**Onderdeel** | **Machtiging vereist**
--- | ---
Geheimen|  Machtiging verkrijgen <br> </br> Selecteer in geheime **machtigingen** >   **geheime beheer bewerkingen** **ophalen** 
Sleutels <br> </br> Als u een sleutel versleutelings sleutel (KEK) gebruikt, moet u naast geheimen ook de volgende machtigingen hebben| Machtiging voor ophalen en ontsleutelen <br> </br> Selecteer bij **sleutel machtigingen**  >  **sleutel beheer bewerkingen** **ophalen**. In **cryptografische bewerkingen** selecteert u **decoderen**.

### <a name="destination-region-keyvault"></a>Sleutel kluis voor het doel gebied

Zorg er in **toegangs beleid** voor dat **Azure Disk Encryption voor volume versleuteling** is ingeschakeld. 

De onderstaande machtigingen moeten worden ingesteld voor de gebruiker die het script uitvoert 

**Onderdeel** | **Machtiging vereist**
--- | ---
Geheimen|  Machtiging instellen <br> </br> Selecteer in geheime **machtigingen** >   **geheime beheer bewerkingen** **instellen** 
Sleutels <br> </br> Als u een sleutel versleutelings sleutel (KEK) gebruikt, moet u naast geheimen ook de volgende machtigingen hebben| Machtigingen ophalen, maken en versleutelen <br> </br> Selecteer in **sleutel machtigingen**  >  **sleutel beheer bewerkingen** **ophalen** en **maken** . Selecteer **versleutelen** in **cryptografische bewerkingen**.

Naast de bovenstaande machtigingen moet u in de doel sleutel kluis machtigingen toevoegen voor de beheerde systeem identiteit die door resource- [bewerkings beheer](./common-questions.md#how-is-managed-identity-used-in-resource-mover) wordt gebruikt om namens u toegang te krijgen tot de Azure-resources. 

1. Onder **instellingen** selecteert u **toegangs beleid toevoegen**. 
2. Zoek in de **Principal selecteren** naar het MSI-bestand. De MSI-naam is ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
3. De onderstaande machtigingen voor het MSI toevoegen

**Onderdeel** | **Machtiging vereist**
--- | ---
Geheimen|  Machtiging ophalen en weer geven <br> </br> Selecteer in geheime **machtigingen** voor geheime >   **beheer bewerkingen** **ophalen** en **lijst** 
Sleutels <br> </br> Als u een sleutel versleutelings sleutel (KEK) gebruikt, moet u naast geheimen ook de volgende machtigingen hebben| Get, lijst machtiging <br> </br> Selecteer in **sleutel machtigingen**  >  **sleutel beheer bewerkingen** **ophalen** en **lijst**



### <a name="copy-the-keys-to-the-destination-key-vault"></a>De sleutels naar de doel sleutel kluis kopiëren

U moet de versleutelings geheimen en sleutels van de bron sleutel kluis kopiëren naar de doel sleutel kluis met behulp van een script dat wij bieden.

- U voert het script uit in Power shell. We raden u aan de meest recente Power shell-versie uit te voeren.
- Met name het script vereist deze modules:
    - Az.Compute
    - AZ. sleutel kluis (versie 3.0.0
    - AZ. accounts (versie 2.2.3)

Voer het bestand als volgt uit:

1. Navigeer naar het [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) in github.
2. Kopieer de inhoud van het script naar een lokaal bestand en geef het de naam *Copy-keys.ps1*.
3. Voer het script uit.
4. Meld u aan bij Azure.
5. Selecteer in het pop-upvenster **gebruikers invoer** het bron abonnement, de resource groep en de bron-VM. Selecteer vervolgens de doel locatie en de doel-kluizen voor schijf-en sleutel versleuteling.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Pop-up van invoer script waarden." :::


6. Wanneer het script is voltooid, geeft de scherm uitvoer aan dat CopyKeys is geslaagd.

## <a name="prepare-vms"></a>VM's voorbereiden

1. Nadat u hebt [gecontroleerd of de vm's voldoen aan de vereisten](#prerequisites), moet u ervoor zorgen dat de vm's die u wilt verplaatsen, zijn ingeschakeld. Alle virtuele machines die u in de doel regio wilt beschik bare schijven, moeten zijn gekoppeld en geïnitialiseerd in de virtuele machine.
3. Controleer of Vm's de meest recente vertrouwde basis certificaten en een bijgewerkte certificaatintrekkingslijst (CRL) hebben. Om dit te doen:
    - Installeert u de meest recente Windows-updates op virtuele Windows-machines.
    - Volgt u de richtlijnen van de distributeurs op virtuele Linux-machines zodat computers over de nieuwste certificaten en certificaatintrekkingslijst beschikken. 
4. Sta uitgaande verbindingen van virtuele machines als volgt toe:
    - Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze [URL's](support-matrix-move-region-azure-vm.md#url-access) toe
    - Als u regels voor netwerk beveiligingsgroepen (NSG) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze [servicetagregels](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Te verplaatsen resources selecteren


- U kunt elk ondersteund resource type selecteren in een van de resource groepen in de bron regio die u selecteert.  
- U kunt resources verplaatsen naar een doel regio die zich in hetzelfde abonnement bevinden als de bron regio. Als u het abonnement wilt wijzigen, kunt u dat doen nadat de resources zijn verplaatst.

Selecteer resources als volgt:

1. Zoek in de Azure-portal naar *resource mover*. Selecteer vervolgens **Azure Resource Mover** onder **Services**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Zoek resultaten voor resource-overdrijfing in de Azure Portal." :::

2. Klik in **overzicht** op **verplaatsen tussen regio's**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Om resources toe te voegen om naar een andere regio te verplaatsen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. Selecteer in **Resources verplaatsen** > **Bron en doel**, het bronabonnement en de regio.
4. Selecteer in **Doel** de regio waarnaar u de virtuele machines wilt verplaatsen. Klik op **Volgende**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Pagina om de bron-en doel regio te selecteren.." :::

5. Klik in **Resources die moeten worden verplaatst** op **Resources selecteren**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Om de resource te selecteren die u wilt verplaatsen.]." :::

6. Selecteer de virtuele machines in **resources selecteren**. U kunt alleen resources toevoegen die worden [ondersteund voor verplaatsen](#prepare-vms). Klik vervolgens op **Gereed**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Pagina om de Vm's te selecteren die u wilt verplaatsen." :::

    > [!NOTE]
    >  In deze zelf studie selecteert u een virtuele machine die gebruikmaakt van versleuteling aan de server zijde (Rayne-VM) met een door de klant beheerde sleutel en een virtuele machine met schijf versleuteling ingeschakeld (Rayne-VM-ADE).

7.  Klik in **Resources die moeten worden verplaatst** op **Volgende**.
8. Controleer bij **controle** de bron-en doel instellingen. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Pagina om de instellingen te controleren en door te gaan met verplaatsen." :::

9. Klik op **Doorgaan** om te beginnen met toevoegen van de resources.
10. Selecteer het pictogram meldingen om de voortgang bij te houden. Wanneer het toevoegen is voltooid, selecteert u **toegevoegde resources voor verplaatsen** in de meldingen.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="De melding voor het bevestigen van resources is toegevoegd." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Controleer nadat u op de melding hebt geklikt de resources op de pagina **Tussen regio's**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Pagina's met toegevoegde resources die in behandeling zijn voor bereid." :::

> [!NOTE]
> - Resources die u toevoegt, worden in *afwachting van voor bereide status in behandeling* genomen.
> - De resource groep voor de virtuele machines wordt automatisch toegevoegd.
> - Als u de **doel configuratie** vermeldingen wijzigt om een resource te gebruiken die al in de doel regio bestaat, wordt de resource status ingesteld op *door voeren in behandeling*, omdat u geen verplaatsing hoeft te initiëren.
> - Als u een resource wilt verwijderen die is toegevoegd, is dit afhankelijk van waar u zich in het Verplaats proces bevindt. [Meer informatie](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

1. Als resources een bericht voor het *valideren van afhankelijkheden* in de kolom **problemen** weer geven, selecteert u de knop **afhankelijkheden valideren** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="NButton om afhankelijkheden te controleren." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Het validatieproces wordt gestart.
2. Als er afhankelijkheden worden gevonden, klikt u op **afhankelijkheden toevoegen**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Knop om afhankelijkheden toe te voegen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. Laat bij **afhankelijkheden toevoegen** de optie standaard **alle afhankelijkheden weer geven** staan.

    - **Alle afhankelijkheden weer geven** , worden alle directe en indirecte afhankelijkheden voor een resource herhaald. Voor een VM worden bijvoorbeeld de NIC, het virtuele netwerk, netwerk beveiligings groepen (Nsg's), enzovoort weer gegeven.
    - **Afhankelijkheden van het eerste niveau weer geven alleen** directe afhankelijkheden worden weer gegeven. Bijvoorbeeld: voor een virtuele machine wordt de NIC weer gegeven, maar niet het virtuele netwerk.
 
4. Selecteer de afhankelijke resources die u wilt toevoegen > **afhankelijkheden toevoegen**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Selecteer afhankelijkheden uit de lijst met afhankelijkheden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Valideer afhankelijkheden opnieuw. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="De pagina die u opnieuw wilt valideren." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Doel resources toewijzen

Doel resources die zijn gekoppeld aan versleuteling, moeten hand matig worden toegewezen.

- Als u een virtuele machine verplaatst die gebruikmaakt van Azure Disk Encryption (ADE), wordt de sleutel kluis in de doel regio weer gegeven als een afhankelijkheid.
- Als u een virtuele machine verplaatst die versleuteling aan de server zijde heeft die gebruikmaakt van aangepast beheerde sleutels (CMKs), wordt de schijf versleuteling die in de doel regio is ingesteld, weer gegeven als een afhankelijkheid. 
- Omdat in deze zelf studie een virtuele machine wordt verplaatst met ADE ingeschakeld en een virtuele machine met een CMK, worden zowel de doel sleutel kluis als de schijf versleuteling ingesteld als afhankelijkheden.

Wijs hand matig als volgt toe:

1. Selecteer in de vermelding schijf versleuteling instellen de optie **resource is niet toegewezen** in de **doel configuratie** kolom.
2. Selecteer in **configuratie-instellingen** de versleutelings reeks voor de doel schijf. Selecteer vervolgens **wijzigingen opslaan**.
3. U kunt selecteren om afhankelijkheden op te slaan en te valideren voor de resource die u wilt wijzigen, of u kunt de wijzigingen gewoon opslaan en alles valideren dat u in één slag wijzigt.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Pagina voor het selecteren van schijf versleuteling die is ingesteld in de doel regio." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Na het toevoegen van de doel resource, wordt de status van de schijf versleuteling ingesteld op *door voeren verplaatsen in behandeling*.
3. Selecteer in de sleutel kluis vermelding **resource niet toegewezen** in de kolom **doel configuratie** . **Configuratie-instellingen**, selecteer de doel sleutel kluis. Sla de wijzigingen op. 

In deze fase wordt zowel de schijf versleutelings als de sleutel kluis status ingesteld op *door voeren verplaatsen in behandeling*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Om het voorbereiden van andere resources te selecteren." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Voor het door voeren en volt ooien van het verplaatsings proces voor versleutelings resources.

1. In **meerdere regio's** selecteert u de resource (schijf encryptie set of sleutel kluis) > **door voeren verplaatsen**.
2. Klik in **Resources verplaatsen** op **Doorvoeren**.

> [!NOTE]
> Nadat de verplaatsing is doorgevoerd, bevindt de resource zich in de status *verwijderings bron in behandeling* .


## <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen 

Voordat u virtuele machines kunt voorbereiden en verplaatsen, moet de resourcegroep van de virtuele machine aanwezig zijn in de doelregio. 

### <a name="prepare-to-move-the-source-resource-group"></a>De bronresourcegroep voorbereiden voor verplaatsing

Tijdens het voorbereidingsproces worden met Resource Mover ARM-sjablonen (Azure Resource Manager) gegenereerd met behulp van de instellingen voor de resourcegroep. Resources in de resourcegroep worden niet beïnvloed.

Bereid als volgt voor:

1. Selecteer in **Tussen regio's** de bronresourcegroep > **Voorbereiden**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Resource groep voorbereiden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. Klik in **Resources voorbereiden** op **Voorbereiden**.

> [!NOTE]
> Nadat de resourcegroep is voorbereid, heeft deze niet de status *Initiëren verplaatsing in behandeling*. 

 
### <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen

Initieer de verplaatsing als volgt:

1. Selecteer in **Tussen regio's** de resourcegroep > **Verplaatsing initiëren**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Om verplaatsen te initiëren." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**. De resourcegroep wordt verplaatst naar een status *Initiëren verplaatsing wordt uitgevoerd*.   
3. Nadat de verplaatsing is geïnitieerd, wordt de doelresourcegroep gemaakt op basis van de gegenereerde ARM-sjabloon. De bronresourcegroep wordt verplaatst naar een status *Verplaatsing doorvoeren in behandeling*.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Controleer de status door voeren verplaatsen in behandeling." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Ga als volgt te werk om het verplaatsingsproces door te voeren en te voltooien:

1. Selecteer in **Tussen regio's** de resourcegroep > **Verplaatsing doorvoeren**.
2. Klik in **Resources verplaatsen** op **Doorvoeren**.

> [!NOTE]
> Nadat de verplaatsing is doorgevoerd, bevindt de bronresourcegroep zich in de status *Verwijderen bron in behandeling*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Controleer de status verwijderen verplaatsen in behandeling." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Te verplaatsen resources voorbereiden

Nu de versleutelings resources en de bron resource groep zijn verplaatst, kunt u voor bereidingen treffen om andere resources te verplaatsen die in *behandeling* zijn.


1. Valideer in **meerdere regio's** opnieuw en los eventuele problemen op.
2. Als u de doelinstellingen wilt bewerken voordat u begint met verplaatsen, selecteert u de koppeling in de kolom **Doelconfiguratie** voor de resource en bewerkt u de instellingen. Als u de instellingen van de doel-VM bewerkt, mag de grootte van de doel-VM niet kleiner zijn dan de grootte van de bron-VM.
3. Selecteer **voorbereiden** voor resources in de status *in behandeling voorbereiden* die u wilt verplaatsen.
3. Selecteer **voorbereiden** in **resources voorbereiden**

    - Tijdens het voorbereidingsproces wordt de Azure Site Recovery Mobility-agent op VM's geïnstalleerd om ze te repliceren.
    - VM-gegevens worden periodiek naar de doelregio gerepliceerd. Dit heeft geen invloed op de bron-VM.
    - Met Resource Mover worden ARM-sjablonen voor de andere bronresources gegenereerd.

Nadat de resources zijn voorbereid, bevinden ze zich in de status *Initiëren verplaatsing in behandeling*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Er wordt een pagina weer gegeven met bronnen in de status initiëren in wachtrij plaatsen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>De verplaatsing initiëren

Nu de resources zijn voorbereid, kunt u de verplaatsing initiëren. 

1. Selecteer in **Tussen regio's** resources met de status *Initiëren verplaatsing in behandeling*. Klik op **Verplaatsing initiëren**.
2. Klik in **Resources verplaatsen** op **Verplaatsing initiëren**.
3. Controleer de voortgang van de verplaatsing in de meldingenbalk.

    - Voor VM's worden replica-VM's gemaakt in de doelregio. De bron-VM wordt uitgeschakeld en er treedt enige downtime op (meestal een aantal minuten).
    - Met Resource Mover worden andere resources opnieuw gemaakt met behulp van de ARM-sjablonen die zijn voorbereid. Meestal treedt er geen downtime op.
    - Nadat de resources zijn verplaatst, bevinden ze zich in de status *Verplaatsing doorvoeren in behandeling*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Pagina met resources in de status door voeren verplaatsen in behandeling." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


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

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Pagina om resources door te voeren om de verplaatsing te volt ooien." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Controleer de voortgang van het doorvoeren in de meldingenbalk.

> [!NOTE]
> - Na het doorvoeren van de verplaatsing, worden VM's niet meer gerepliceerd. De bron-VM wordt door de doorvoer niet beïnvloed.
> - Doorvoeren heeft geen invloed op bronnetwerkresources.
> - Nadat de verplaatsing is doorgevoerd, bevinden de resources zich in de status *Verwijderen bron in behandeling*.



## <a name="configure-settings-after-the-move"></a>Instellingen configureren na de verplaatsing

- De Mobility-service wordt niet automatisch van VM's verwijderd. Verwijder de service handmatig of laat deze staan als u van plan bent de server opnieuw te verplaatsen.
- Wijzig de regels voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) na de verplaatsing.

## <a name="delete-source-resources-after-commit"></a>Bronresources verwijderen na doorvoeren

Na de verplaatsing kunt u desgewenst de resources in de bronregio verwijderen. 

1. Selecteer in **meerdere regio's** elke bron resource die u wilt verwijderen. Selecteer vervolgens **bron verwijderen**.
2. Controleer in **bron verwijderen** wat u wilt verwijderen en typ **Ja** in het **vak verwijderen bevestigen**. De actie is onomkeerbaar, dus Controleer zorgvuldig.
3. Nadat u **Ja** hebt getypt, selecteert u **bron verwijderen**.

> [!NOTE]
>  In de resource Move-Portal kunt u geen resource groepen, sleutel kluizen of SQL Server servers verwijderen. U moet deze afzonderlijk verwijderen van de pagina eigenschappen voor elke resource.


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
> * Versleutelde virtuele Azure-machines en hun afhankelijke resources verplaatst naar een andere Azure-regio.


We gaan nu proberen de Azure SQL-databases en elastische pools naar een andere regio te verplaatsen.

> [!div class="nextstepaction"]
> [Azure SQL-resources verplaatsen](./tutorial-move-region-sql.md)
