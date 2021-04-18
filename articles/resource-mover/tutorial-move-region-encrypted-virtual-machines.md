---
title: Versleutelde Azure-VM's verplaatsen tussen regio's met behulp van Azure Resource Mover
description: Meer informatie over het verplaatsen van versleutelde Azure-VM's naar een andere regio met behulp van Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600689"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Zelfstudie: Versleutelde Azure-VM's verplaatsen tussen regio's

In dit artikel wordt beschreven hoe u versleutelde virtuele Azure-machines (VM's) naar een andere Azure-regio verplaatst met behulp van [Azure Resource Mover](overview.md). 

Versleutelde VM's kunnen als een van de volgende worden beschreven:

- VM's met schijven Azure Disk Encryption ingeschakeld. Zie Een virtuele [Windows-machine maken en versleutelen](../virtual-machines/windows/disk-encryption-portal-quickstart.md)met behulp van de Azure Portal.
- VM's die gebruikmaken van door de klant beheerde sleutels (CMK's) voor versleuteling in rust of versleuteling aan de serverzijde. Zie Use the [Azure Portal to enable server-side encryption with customer-managed keys for managed disks (De](../virtual-machines/disks-enable-customer-managed-keys-portal.md)Azure Portal gebruiken voor het inschakelen van versleuteling aan de serverzijde met door de klant beheerde sleutels voor beheerde schijven) voor meer informatie.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleer de vereisten. 
> * Voor VM'Azure Disk Encryption ingeschakeld, kopieert u sleutels en geheimen van de sleutelkluis in de bronregio naar de sleutelkluis in de doelregio.
> * Bereid het verplaatsen van VM's voor en selecteer resources in de bronregio waar u ze wilt verplaatsen.
> * Resourceafhankelijkheden oplossen.
> * Voor VM's Azure Disk Encryption ingeschakeld, wijst u handmatig de doelsleutelkluis toe. Voor VM's die gebruikmaken van versleuteling aan de serverzijde met door de klant beheerde sleutels, wijst u handmatig een schijfversleutelingsset toe in de doelregio.
> * Verplaats de sleutelkluis of schijfversleutelingsset.
> * De bronresourcegroep voorbereiden en verplaatsen. 
> * De overige resources voorbereiden en verplaatsen.
> * Bepaal of u de move wilt verwijderen of door te zetten. 
> * Verwijder eventueel resources in de bronregio na de overstap.

> [!NOTE]
> In deze zelfstudie ziet u het snelste pad voor het proberen van een scenario. Er worden alleen de standaardopties gebruikt. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint. Meld u vervolgens aan bij de [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

Vereiste |Details
--- | ---
**Abonnementmachtigingen** | Controleer of u eigenaarstoegang *hebt voor het* abonnement dat de resources bevat die u wilt verplaatsen.<br/><br/> *Waarom heb ik eigenaarstoegang nodig?* De eerste keer dat u een resource toevoegt voor een specifiek bron- en doelpaar in een [Azure-abonnement,](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)maakt Resource Mover een door het systeem toegewezen beheerde identiteit, voorheen bekend als managed service identity (MSI). Deze identiteit wordt vertrouwd door het abonnement. Voordat u de identiteit kunt maken en de vereiste rollen *kunt* toewijzen (Inzender en Gebruikerstoegangbeheerder *in* het bronabonnement), moet het account dat u gebruikt om resources toe te voegen eigenaarsmachtigingen hebben in het abonnement.  Zie Klassieke [abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen voor meer informatie.](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)
**VM-ondersteuning** | Controleer of de VM's die u wilt verplaatsen, als volgt worden ondersteund:<li>[Controleer](support-matrix-move-region-azure-vm.md#windows-vm-support) ondersteunde virtuele Windows-machines.<li>[Controleer](support-matrix-move-region-azure-vm.md#linux-vm-support) ondersteunde virtuele Linux-machines en versies van de kernel.<li>Controleer de ondersteunde [compute-](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [opslag-](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) en [netwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)instellingen.
**Vereisten voor Sleutelkluis (Azure Disk Encryption)** | Als u een Azure Disk Encryption hebt ingeschakeld voor VM's, hebt u een sleutelkluis nodig in zowel de bron- als de doelregio. Zie Een sleutelkluis [maken voor meer informatie.](../key-vault/general/quick-create-portal.md)<br/><br/> Voor de sleutelkluizen in de bron- en doelregio's hebt u de volgende machtigingen nodig:<li>Sleutelmachtigingen: Sleutelbeheerbewerkingen (Get, List) en cryptografische bewerkingen (ontsleutelen en versleutelen)<li>Geheime machtigingen: Secret Management Operations (Get, List en Set)<li>Certificaat (List en Get)
**Schijfversleutelingsset (versleuteling aan serverzijde met CMK)** | Als u VM's gebruikt met versleuteling aan de serverzijde die gebruikmaakt van een CMK, hebt u een schijfversleutelingsset nodig in zowel de bron- als de doelregio. Zie Een [schijfversleutelingsset maken voor meer informatie.](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)<br/><br/> Het verplaatsen tussen regio's wordt niet ondersteund als u HSM-sleutels (Hardware Security Module) gebruikt voor door de klant beheerde sleutels.
**Quotum voor doelregio** | Het abonnement moet voldoende quota hebben om de resources die u verplaatst in de doelregio te maken. Als de quota onvoldoende zijn, moet u [hogere limieten aanvragen](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Kosten voor doelregio** | Controleer de prijzen en kosten die zijn gekoppeld aan de doelregio waar u de VM's naar verplaatst. Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/).


## <a name="verify-permissions-in-the-key-vault"></a>Machtigingen verifiëren in de sleutelkluis

Als u VM's verplaatst Azure Disk Encryption ingeschakeld, moet u een script uitvoeren zoals vermeld in de sectie De sleutels naar de doelsleutelkluis [kopiëren.](#copy-the-keys-to-the-destination-key-vault) De gebruikers die het script uitvoeren, moeten de juiste machtigingen hebben om dit te doen. Raadpleeg de volgende tabel om te begrijpen welke machtigingen nodig zijn. U vindt de opties voor het wijzigen van de machtigingen door naar de sleutelkluis in de Azure Portal. Selecteer **onder Instellingen** de optie **Toegangsbeleid.**

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Schermopname van de koppeling Toegangsbeleid in het deelvenster Instellingen van de sleutelkluis." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Als de gebruikersmachtigingen niet zijn opgegeven, selecteert u **Toegangsbeleid toevoegen** en geeft u de machtigingen op. Als het gebruikersaccount al een beleid heeft, stelt u onder **Gebruiker** de machtigingen in volgens de instructies in de volgende tabel.

Virtuele Azure-Azure Disk Encryption kunnen de volgende variaties hebben en u moet de machtigingen instellen op basis van hun relevante onderdelen. De VM's kunnen het volgende hebben:
- Een standaardoptie waarbij de schijf alleen is versleuteld met geheimen.
- Beveiliging toegevoegd die gebruikmaakt van [een Sleutelversleutelingssleutel (KEK).](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-key-vault"></a>Sleutelkluis voor bronregio

Voor gebruikers die het script uitvoeren, stelt u machtigingen in voor de volgende onderdelen: 

Onderdeel | Benodigde machtigingen
--- | ---
Geheimen |  *Ophalen* <br></br> Selecteer **Geheime machtigingen**  >  **Geheime beheerbewerkingen** en selecteer vervolgens **Get.** 
Sleutels <br></br> Als u een KEK gebruikt, hebt u deze machtigingen nodig naast de machtigingen voor geheimen. | *Get* and *Decrypt* <br></br> Selecteer **Sleutelmachtigingen**  >  **Sleutelbeheerbewerkingen** en selecteer vervolgens **Get.** Selecteer **in Cryptografische bewerkingen** de optie **Ontsleutelen.**

### <a name="destination-region-key-vault"></a>Sleutelkluis van doelregio

Zorg **ervoor dat in** Toegangsbeleid de Azure Disk Encryption voor **volumeversleuteling** is ingeschakeld. 

Voor gebruikers die het script uitvoeren, stelt u machtigingen in voor de volgende onderdelen: 

Onderdeel | Benodigde machtigingen
--- | ---
Geheimen |  *Instellen* <br></br> Selecteer **Geheime machtigingen**  >  **Geheime beheerbewerkingen** en selecteer vervolgens **Instellen.** 
Sleutels <br></br> Als u een KEK gebruikt, hebt u deze machtigingen nodig naast de machtigingen voor geheimen. | *Get,* *Create* en *Encrypt* <br></br> Selecteer **Sleutelmachtigingen**  >  **Sleutelbeheerbewerkingen** en selecteer **vervolgens Get** en **Create.** Selecteer **versleutelen in Cryptografische** **bewerkingen.**

<br>

Naast de voorgaande machtigingen moet u in de doelsleutelkluis machtigingen toevoegen voor de [Managed System Identity](./common-questions.md#how-is-managed-identity-used-in-resource-mover) die Resource Mover gebruikt om namens u toegang te krijgen tot de Azure-resources. 

1. Selecteer **onder Instellingen** de optie **Toegangsbeleid toevoegen.** 
1. Zoek **in Principal selecteren** naar de MSI. De MSI-naam is ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
1. Voeg voor de MSI de volgende machtigingen toe:

    Onderdeel | Benodigde machtigingen
    --- | ---
    Geheimen|  *Get* and *List* <br></br> Selecteer **Geheime machtigingen**  >  **Geheime beheerbewerkingen** en selecteer vervolgens **Get** en **List.** 
    Sleutels <br></br> Als u een KEK gebruikt, hebt u deze machtigingen nodig naast de machtigingen voor geheimen. | *Get* and *List* <br></br> Selecteer **Sleutelmachtigingen**  >  **Sleutelbeheerbewerkingen** en selecteer vervolgens **Get** and **List.**

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>De sleutels kopiëren naar de doelsleutelkluis

Kopieer de versleutelingsgeheimen en sleutels van de bronsleutelkluis naar de doelsleutelkluis met behulp van een [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) dat we leveren.

- Voer het script uit in PowerShell. U wordt aangeraden de nieuwste Versie van PowerShell te gebruiken.
- Voor het script zijn de volgende modules vereist:
    - Az.Compute
    - Az.KeyVault (versie 3.0.0)
    - Az.Accounts (versie 2.2.3)

Ga als volgt te werk om het script uit te voeren:

1. Open het [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) in GitHub.
1. Kopieer de inhoud van het script naar een lokaal bestand en noem *hetCopy-keys.ps1*.
1. Voer het script uit.
1. Meld u aan bij Azure Portal.
1. Selecteer in de vervolgkeuzelijsten in het venster Gebruikersinvoer het bronabonnement, de resourcegroep en de bron-VM, en selecteer vervolgens de doellocatie en de doelkluizen voor schijf- en sleutelversleuteling. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Schermopname van het venster Gebruikersinvoer voor het invoeren van de scriptwaarden." :::

1. Selecteer de knop **Selecteren**. 
   
   Wanneer het script is uitgevoerd, wordt u via een bericht op de melding gezonden dat CopyKeys is voltooid.

## <a name="prepare-vms"></a>VM's voorbereiden

1. Nadat u hebt gecontroleerd of de VM's aan de vereisten [voldoen,](#prerequisites)moet u ervoor zorgen dat de VM's die u wilt verplaatsen, zijn ingeschakeld. Alle VM-schijven die u beschikbaar wilt maken in de doelregio, moeten worden gekoppeld en initialiseerd in de VM.
1. Ga als volgt te werk om ervoor te zorgen dat de VM's de meest recente vertrouwde basiscertificaten en een bijgewerkte certificaatintrekkende lijst (CRL) hebben:
    - Installeert u de meest recente Windows-updates op virtuele Windows-machines.
    - Volg op Linux-VM's de richtlijnen voor de distributeur, zodat de machines over de nieuwste certificaten en CRL's kunnen zorgen. 
1. Ga als volgt te werk om uitgaande connectiviteit vanaf de VM's toe te staan:
    - Als u een op URL gebaseerde firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat [u toegang tot de URL's toe.](support-matrix-move-region-azure-vm.md#url-access)
    - Als u regels voor netwerk beveiligingsgroepen (NSG) gebruikt om de uitgaande connectiviteit te beheren, maakt u deze [servicetagregels](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-the-resources-to-move"></a>De resources selecteren die u wilt verplaatsen

- U kunt elk ondersteund resourcetype selecteren in een van de resourcegroepen in de bronregio die u selecteert.  
- U kunt resources verplaatsen naar een doelregio die zich in hetzelfde abonnement als de bronregio heeft. Als u het abonnement wilt wijzigen, kunt u dit doen nadat de resources zijn verplaatst.

Ga als volgt te werk om de resources te selecteren:

1. Zoek in de Azure-portal naar **resource mover**. Selecteer vervolgens **Azure Resource Mover** onder **Services**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Schermopname van zoekresultaten voor Azure Resource Mover in de Azure Portal." :::

1. Selecteer in Azure Resource Mover **deelvenster Overzicht** de optie **Verplaatsen tussen regio's.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Schermopname van de knop Verplaatsen tussen regio's voor het toevoegen van resources om naar een andere regio te verplaatsen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. Selecteer in **het deelvenster Resources** verplaatsen het tabblad Bron **en** doel. Selecteer vervolgens in de vervolgkeuzelijsten het bronabonnement en de bronregio.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Pagina om de bron- en doelregio te selecteren." :::

1. Selecteer **onder** Doel de regio waar u de VM's wilt verplaatsen en selecteer **vervolgens Volgende.**

1. Selecteer het **tabblad Resources dat u wilt** verplaatsen en selecteer vervolgens Resources **selecteren.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Schermopname van het deelvenster Resources verplaatsen en de knop Resources selecteren." :::

1. Selecteer in **het deelvenster Resources** selecteren de VM's die u wilt verplaatsen. Zoals vermeld in de [sectie De resources selecteren](#select-the-resources-to-move) die u wilt verplaatsen, kunt u alleen resources toevoegen die worden ondersteund voor een verplaatsen.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Schermopname van het deelvenster Resources selecteren voor het selecteren van VM's die moeten worden verplaatst." :::

    > [!NOTE]
    >  In deze zelfstudie selecteert u een VM die gebruikmaakt van versleuteling aan de serverzijde (rayne-vm) met een door de klant beheerde sleutel en een VM met schijfversleuteling ingeschakeld (rayne-vm-ade).

1. Selecteer **Gereed**.
1. Selecteer het **tabblad Resources dat u wilt** verplaatsen en selecteer vervolgens **Volgende.**
1. Selecteer het **tabblad Controleren** en controleer vervolgens de bron- en doelinstellingen. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Schermopname van het deelvenster voor het controleren van de bron- en doelinstellingen." :::

1. Selecteer **Doorgaan om** te beginnen met het toevoegen van de resources.
1. Selecteer het meldingenpictogram om de voortgang bij te houden. Nadat het proces is voltooid, selecteert u in **het deelvenster Meldingen** de optie Resources toegevoegd voor **verplaatsen.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Schermopname van het deelvenster Meldingen om te bevestigen dat de resources zijn toegevoegd." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. Nadat u de melding hebt geselecteerd, controleert u de resources op de **pagina Tussen regio's.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Schermopname van toegevoegde resources met de status Voorbereiden in behandeling." :::

> [!NOTE]
> - De resources die u toevoegt, krijgen de *status Voorbereiding in behandeling.*
> - De resourcegroep voor de VM's wordt automatisch toegevoegd.
> - Als u  de vermeldingen van de doelconfiguratie wijzigt om een resource te gebruiken die al bestaat in de doelregio, wordt de resourcetoestand ingesteld op Commit *pending*, omdat u er geen move voor hoeft te initiëren.
> - Als u een resource wilt verwijderen die is toegevoegd, is de methode die u gaat gebruiken, afhankelijk van waar u zich in het proces voor verplaatsen in de procedure voor verplaatsen. Zie Verzamelingen en resourcegroepen verplaatsen [beheren voor meer informatie.](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Afhankelijkheden oplossen

1. Als voor resources het bericht *Afhankelijkheden valideren* wordt weergegeven in de **kolom Problemen,** selecteert u **de knop Afhankelijkheden valideren.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Schermopname met de knop Afhankelijkheden valideren." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Het validatieproces wordt gestart.
1. Als er afhankelijkheden worden gevonden, **selecteert u Afhankelijkheden toevoegen.**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Schermopname van de knop Afhankelijkheden toevoegen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. Laat in **het deelvenster Afhankelijkheden** toevoegen de **standaardoptie Alle afhankelijkheden** weergeven staan.

    - **Alle afhankelijkheden worden** door alle directe en indirecte afhankelijkheden voor een resource itereert. Voor een virtuele machine worden bijvoorbeeld de NIC, het virtuele netwerk, netwerkbeveiligingsgroepen (NSG's) en meer gebruikt.
    - **Afhankelijkheden op het eerste niveau tonen alleen** directe afhankelijkheden. Voor een virtuele machine ziet u bijvoorbeeld de NIC, maar niet het virtuele netwerk.
 
1. Selecteer de afhankelijke resources die u wilt toevoegen en selecteer **vervolgens Afhankelijkheden toevoegen.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Schermopname van de lijst met afhankelijkheden en de knop Afhankelijkheden toevoegen." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Valideer de afhankelijkheden opnieuw. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Schermopname van het deelvenster voor het opnieuwvalideren van de afhankelijkheden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Doelbronnen toewijzen

U moet handmatig doelbronnen toewijzen die zijn gekoppeld aan versleuteling.

- Als u een VM verplaatst die is Azure Disk Encryption ingeschakeld, wordt de sleutelkluis in uw doelregio weergegeven als een afhankelijkheid.
- Als u een VM verplaatst met versleuteling aan de serverzijde die gebruikmaakt van CMK's, wordt de schijfversleutelingsset in de doelregio weergegeven als een afhankelijkheid. 
- Omdat in deze zelfstudie wordt gedemonstreerd hoe u een VM verplaatst die Azure Disk Encryption ingeschakeld en die gebruikmaakt van een CMK, worden zowel de doelsleutelkluis als de schijfversleutelingsset als afhankelijkheden getoond.

Ga als volgt te werk om de doelbronnen handmatig toe te wijzen:

1. Selecteer in de vermelding voor de schijfversleutelingsset **Resource niet toegewezen** in de kolom **Doelconfiguratie.**
1. Selecteer **in Configuratie-instellingen** de doelschijfversleutelingsset en selecteer **vervolgens Wijzigingen opslaan.**
1. U kunt afhankelijkheden opslaan en valideren voor de resource die u wilt wijzigen, of u kunt alleen de wijzigingen opslaan en vervolgens alles valideren dat u op hetzelfde moment wijzigt.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Schermopname van het deelvenster Doelconfiguratie voor het opslaan van wijzigingen in de doelregio." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Nadat u de doelresource hebt toegevoegd, wordt de status van de schijfversleutelingsset gewijzigd in *Commit move pending*.

1. Selecteer in de sleutelkluisinvoer **Resource niet toegewezen** in de kolom **Doelconfiguratie.** Selecteer **onder Configuratie-instellingen** de doelsleutelkluis en sla uw wijzigingen op. 

In deze fase worden de statussen van de schijfversleutelingsset en sleutelkluis gewijzigd in *Commit move pending*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Schermopname van het deelvenster voor het voorbereiden van andere resources." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Ga als volgt te werk om het versleutelingsproces voor versleutelingsbronnen door te zetten en te voltooien:

1. Selecteer **in Tussen regio's** de resource (schijfversleutelingsset of sleutelkluis) en selecteer **vervolgens Move commit**.
1. Selecteer **in Resources verplaatsen** de optie **Commit**.

> [!NOTE]
> Nadat u de wijziging hebt doorgevoerd, verandert de resourcestatus in *Bron verwijderen in behandeling.*


## <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen 

Voordat u virtuele machines kunt voorbereiden en verplaatsen, moet de resourcegroep van de virtuele machine aanwezig zijn in de doelregio. 

### <a name="prepare-to-move-the-source-resource-group"></a>De bronresourcegroep voorbereiden voor verplaatsing

Tijdens het voorbereidingsproces genereert Resource Mover Azure Resource Manager ARM-sjablonen (resourcegroepinstellingen). De resources in de resourcegroep worden niet beïnvloed.

Ga als volgt te werk om het verplaatsen van de bronresourcegroep voor te bereiden:

1. Selecteer **in Tussen regio's** de bronresourcegroep en selecteer vervolgens **Voorbereiden.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Schermopname van de knop 'Voorbereiden' in het deelvenster Resources voorbereiden." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. Selecteer **in Resources voorbereiden** de optie **Voorbereiden.**

> [!NOTE]
> Nadat u de verplaatsen hebt voorbereid, verandert de status van de resourcegroep in *Initiëren verplaatsen in behandeling.* 

 
### <a name="move-the-source-resource-group"></a>De bronresourcegroep verplaatsen

Begin met het verplaatsen van de bronresourcegroep door het volgende te doen:

1. Selecteer in **het deelvenster** Tussen regio's de resourcegroep en selecteer vervolgens **Verplaatsen initiëren.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Schermopname van de knop 'Verplaatsen initiëren' in het deelvenster Tussen regio's." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. Selecteer in **het deelvenster Resources** verplaatsen de optie Verplaatsen **initiëren.** De status van de resourcegroep wordt gewijzigd *in Initiëren -beweging wordt uitgevoerd.*   
1. Nadat u de verplaatsen hebt gestart, wordt de doelresourcegroep gemaakt op basis van de gegenereerde ARM-sjabloon. De status van de bronresourcegroep wordt gewijzigd in *Commit move pending*.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Schermopname van het deelvenster Resources verplaatsen waarin de status van de resourcegroep is gewijzigd in 'Commit move pending'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Ga als volgt te werk om de move door te zetten en het proces te voltooien:

1. Selecteer in **het deelvenster** Tussen regio's de resourcegroep en selecteer vervolgens **Verplaatsen doormaken.**
1. Selecteer in **het deelvenster Resources** verplaatsen de optie **Commit**.

> [!NOTE]
> Nadat u de overstap hebt doorgevoerd, verandert de status van de bronresourcegroep in *Bron verwijderen in behandeling.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Schermopname van de bronresourcegroep waarin de status is gewijzigd in 'Bron verwijderen in behandeling'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Te verplaatsen resources voorbereiden

Nu de versleutelingsresources en de bronresourcegroep zijn verplaatst, kunt u voorbereidingen treffen om andere resources te verplaatsen waarvan de huidige status Voorbereiden in *behandeling is.*


1. Valideer **in het deelvenster** Tussen regio's de overstap opnieuw en los eventuele problemen op.
1. Als u de doelinstellingen wilt bewerken voordat u begint  met verplaatsen, selecteert u de koppeling in de kolom Doelconfiguratie voor de resource en bewerkt u vervolgens de instellingen. Als u de instellingen van de doel-VM bewerkt, mag de grootte van de doel-VM niet kleiner zijn dan de grootte van de bron-VM.
1. Selecteer Voorbereiden voor resources *met de* status Voorbereiden in behandeling die u wilt **verplaatsen.**
1. Selecteer in **het deelvenster Resources** voorbereiden de optie **Voorbereiden.**

    - Tijdens de voorbereiding wordt de Azure Site Recovery mobility-agent geïnstalleerd op de VM's om ze te repliceren.
    - De VM-gegevens worden periodiek gerepliceerd naar de doelregio. Dit heeft geen invloed op de bron-VM.
    - Met Resource Mover worden ARM-sjablonen voor de andere bronresources gegenereerd.

> [!NOTE]
> Nadat u de resources hebt voorbereid, wordt de status gewijzigd in *Initiëren van verplaatsen in behandeling.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Schermopname van het deelvenster Resources voorbereiden, met de resources in de status 'Initiëren verplaatsen in behandeling'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>De verplaatsing initiëren

Nu u de voorbereide resources hebt voorbereid, kunt u de overstap initiëren. 

1. Selecteer in **het deelvenster Tussen** regio's de resources waarvan de status *Initiëren verplaatsen in* behandeling is en selecteer vervolgens Verplaatsen **initiëren.**
1. Selecteer in **het deelvenster Resources** verplaatsen de optie Verplaatsen **initiëren.**
1. Volg de voortgang van de overstap in de meldingenbalk.

    - Voor VM's worden replica-VM's gemaakt in de doelregio. De bron-VM wordt uitgeschakeld en er treedt enige downtime op (meestal een aantal minuten).
    - Resource Mover maakt andere resources opnieuw met behulp van de voorbereide ARM-sjablonen. Meestal treedt er geen downtime op.
    - Nadat u de resources hebt verplaatst, wordt de status gewijzigd in *Commit move pending*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Schermopname van een lijst met resources met de status 'Commit move pending'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Verwijderen of doorvoeren?

Na de eerste stap kunt u beslissen of u de verplaatsen wilt door commiten of verwijderen. 

- **Verwijderen:** u kunt een move verwijderen als u deze test en de bronresource niet daadwerkelijk wilt verplaatsen. Als u de verplaatsen genegeerd, wordt de resource teruggeleid *naar initiëren status Verplaatsen in* behandeling.
- **Doorvoeren**: Met doorvoeren wordt de verplaatsing naar de doelregio voltooid. Nadat u een bronresource hebt doorgevoerd, verandert de status ervan in Bron verwijderen *in* behandeling en kunt u beslissen of u deze wilt verwijderen.


## <a name="discard-the-move"></a>De verplaatsing verwijderen 

Ga als volgt te werk om de verplaatsen te negeren:

1. Selecteer in **het deelvenster** Tussen regio's resources waarvan de status *Verplaatsen* in behandeling is, en selecteer vervolgens **Verplaatsen negeren.**
1. Selecteer in **het deelvenster Verplaatsen** verwijderen de optie **Verwijderen.**
1. Houd de voortgang van de overstap bij in de meldingenbalk.


> [!NOTE]
> Nadat u de resources hebt verwijderd, worden de statussen van de VM gewijzigd in *Initiëren verplaatsen in behandeling.*

## <a name="commit-the-move"></a>De verplaatsing doorvoeren

Om het verplaatsen te voltooien, moet u de verplaatsen als volgt voltooien: 

1. Selecteer in **het deelvenster Tussen** regio's resources waarvan de status Commit move in *behandeling* is en selecteer vervolgens **Commit move**.
1. Selecteer in **het deelvenster Resources** commit de optie **Commit.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Schermopname van een lijst met resources voor het maken van resources om de overstap te maken." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Controleer de voortgang van het doorvoeren in de meldingenbalk.

> [!NOTE]
> - Nadat u de overstap hebt gemaakt, worden de VM's niet meer repliceren. De bron-VM wordt niet beïnvloed door de door commit.
> - Het door te maken proces heeft geen invloed op de bronnetwerkbronnen.
> - Nadat u de verplaatsen hebt vastgelegd, worden de resourcestatussen gewijzigd in *Bron verwijderen in behandeling.*



## <a name="configure-settings-after-the-move"></a>Instellingen configureren na de verplaatsing

- De Mobility-service wordt niet automatisch verwijderd van VM's. Verwijder de service handmatig of laat deze staan als u van plan bent de server opnieuw te verplaatsen.
- Wijzig regels voor op rollen gebaseerd toegangsbeheer (RBAC) van Azure na de overstap.

## <a name="delete-source-resources-after-commit"></a>Bronresources verwijderen na doorvoeren

Na de verplaatsing kunt u desgewenst de resources in de bronregio verwijderen. 

1. Selecteer in **het deelvenster** Tussen regio's elke bronresource die u wilt verwijderen en selecteer vervolgens **Bron verwijderen.**
1. Controleer **in Bron** verwijderen wat u wilt verwijderen en typ ja in Verwijderen **bevestigen.**  De actie kan niet ongedaan worden maken, dus controleer zorgvuldig!
1. Nadat u ja hebt **getypt,** **selecteert u Bron verwijderen.**

> [!NOTE]
>  In de portal voor het verplaatsen van resources kunt u geen resourcegroepen, sleutelkluizen of SQL Server verwijderen. U moet elke resource afzonderlijk verwijderen van de eigenschappenpagina voor elke resource.


## <a name="delete-resources-that-you-created-for-the-move"></a>Resources verwijderen die u voor de verplaatsen hebt gemaakt

Na de overstap kunt u de verzameling voor verplaatsen handmatig verwijderen en Site Recovery resources die u tijdens dit proces hebt gemaakt.

- De verzameling voor verplaatsen wordt standaard verborgen. Als u deze wilt zien, moet u verborgen resources inschakelen.
- De cacheopslag heeft een vergrendeling die moet worden verwijderd voordat de cacheopslag kan worden verwijderd.

Ga als volgt te werk om uw resources te verwijderen: 
1. Zoek de resources in resourcegroep ```RegionMoveRG-<sourceregion>-<target-region>```.
1. Controleer of alle VM's en andere bronbronnen in de bronregio zijn verplaatst of verwijderd. Deze stap zorgt ervoor dat er geen resources in behandeling zijn die deze gebruiken.
1. De resources verwijderen:

    - Naam van verzameling verplaatsen: ```movecollection-<sourceregion>-<target-region>```
    - Naam van cacheopslagaccount: ```resmovecache<guid>```
    - Kluisnaam: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Versleutelde Azure-VM's en hun afhankelijke resources verplaatst naar een andere Azure-regio.


Als volgende stap kunt u proberen Azure SQL databases en elastische pools naar een andere regio te verplaatsen.

> [!div class="nextstepaction"]
> [Azure SQL-resources verplaatsen](./tutorial-move-region-sql.md)
