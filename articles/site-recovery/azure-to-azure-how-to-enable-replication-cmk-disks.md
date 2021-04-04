---
title: Replicatie van versleutelde virtuele machines in azure inschakelen in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u replicatie configureert voor virtuele machines met door de klant beheerde sleutel (CMK) ingeschakelde schijven van een Azure-regio naar een andere met behulp van Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 9f9052f51c5bab0ea738e9fd15d8f62f45ff0c9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146532"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Machines repliceren met schijven met door de klant beheerde sleutels

In dit artikel wordt beschreven hoe u virtuele Azure-machines met Customer-Managed-CMK (Managed Keys) repliceert van de ene Azure-regio naar de andere.

## <a name="prerequisite"></a>Vereiste
U moet de schijf versleutelings (en) in de doel regio voor het doel abonnement maken voordat u replicatie inschakelt voor uw virtuele machines met CMK Managed disks.

## <a name="enable-replication"></a>Replicatie inschakelen

Voor dit voor beeld is de primaire Azure-regio Azië-oost en is de secundaire regio Zuid Azië-oost.

1. Selecteer **+ repliceren** in de kluis.
2. Let op de volgende velden.
    - **Bron**: het herkomst punt van de virtuele machines, in dit geval **Azure**.
    - **Bron locatie**: de Azure-regio waar u uw virtuele machines wilt beveiligen. Voor dit voor beeld is de bron locatie Azië-oost.
    - **Implementatie model**: het Azure-implementatie model van de bron machines.
    - **Bron abonnement**: het abonnement waartoe de virtuele bron machines behoren. Dit kan elk abonnement zijn dat zich in dezelfde Azure Active Directory Tenant bevindt als de Recovery Services-kluis.
    - **Resource groep**: de resource groep waartoe de virtuele bron machine behoort. Alle virtuele machines in de geselecteerde resource groep worden in de volgende stap weer gegeven voor beveiliging.

3. Selecteer in **virtual machines**  >  **virtuele machines selecteren** de VM die u wilt repliceren. U kunt alleen machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**.

4. In **instellingen** kunt u de volgende instellingen voor de doel site configureren.

    - **Doel locatie**: de locatie waar de gegevens van de virtuele bron machine worden gerepliceerd. Site Recovery biedt een lijst met geschikte doel regio's op basis van de locatie van de geselecteerde machine. U wordt aangeraden dezelfde locatie te gebruiken als de locatie van de Recovery Services kluis.
    - **Doel abonnement**: het doel abonnement dat wordt gebruikt voor herstel na nood gevallen. Standaard is het doelabonnement hetzelfde als het bronabonnement.
    - **Doel resource groep**: de resource groep waarvan al uw gerepliceerde virtuele machines deel uitmaken. Site Recovery maakt standaard een nieuwe resource groep in de doel regio. De naam haalt het `asr` achtervoegsel op. Als er al een resource groep bestaat die is gemaakt door Azure Site Recovery, wordt deze opnieuw gebruikt. U kunt er ook voor kiezen om deze aan te passen, zoals wordt weer gegeven in de volgende sectie. De locatie van de doel resource groep kan een wille keurige Azure-regio zijn, behalve de regio waarin de virtuele bron machines worden gehost.
    - **Virtueel netwerk van doel**: standaard maakt site Recovery een nieuw virtueel netwerk in de doel regio. De naam haalt het `asr` achtervoegsel op. Het wordt toegewezen aan uw bron netwerk en wordt gebruikt voor toekomstige beveiliging. [Meer informatie](./azure-to-azure-network-mapping.md) over netwerk toewijzing.
    - **Doel opslag accounts (als uw bron-VM geen Managed disks gebruikt)**: standaard maakt site Recovery een nieuw doel opslag account door de mimicking van de bron-VM te configureren. Als er al een opslag account bestaat, wordt dit opnieuw gebruikt.
    - **Beheerde replica schijven (als uw bron-VM gebruikmaakt van beheerde schijven)**: site Recovery maakt nieuwe replica beheerde schijven in de doel regio om de beheerde schijven van de bron-VM van hetzelfde opslag type (Standard of Premium) te spie gelen als de beheerde schijven van de bron-VM.
    - **Cache opslag accounts**: voor site Recovery is een extra opslag account met de naam *cache opslag* in de bron regio vereist. Alle wijzigingen op de bron-Vm's worden bijgehouden en verzonden naar het cache-opslag account. Ze worden vervolgens gerepliceerd naar de doel locatie.
    - **Beschikbaarheidsset**: standaard maakt site Recovery een nieuwe beschikbaarheidsset in de doel regio. De naam heeft het `asr` achtervoegsel. Als een beschikbaarheidsset die is gemaakt door Site Recovery al bestaat, wordt deze opnieuw gebruikt.
    - **Schijf versleutelings sets (des)**: site Recovery moet de schijf versleutelingset (en) die moeten worden gebruikt voor replica-en doel-beheerde schijven. U moet vooraf een DES maken in het doel abonnement en de doel regio voordat u de replicatie inschakelt. Standaard is een DES niet geselecteerd. U moet op aanpassen klikken om een DES per bron schijf te kiezen.
    - **Replicatie beleid**: definieert de instellingen voor de Bewaar geschiedenis van het herstel punt en de frequentie van de app-consistente moment opname. Site Recovery maakt standaard een nieuw replicatie beleid met standaard instellingen van *24 uur* voor de Bewaar periode van het herstel punt en *60 minuten* voor de frequentie van de app-consistente moment opname.

    ![Replicatie inschakelen voor de computer met CMK ingeschakelde schijven](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Doel resources aanpassen

Volg deze stappen om de Site Recovery standaard doel instellingen te wijzigen.

1. Selecteer **aanpassen** naast doel abonnement om het standaard doel abonnement te wijzigen. Selecteer het abonnement in de lijst met abonnementen die beschikbaar zijn in de Azure AD-Tenant.

2. Selecteer **aanpassen** naast resource groep, netwerk, opslag en beschikbaarheids sets om de volgende standaard instellingen te wijzigen:
    - Voor **doel resource groep** selecteert u de resource groep in de lijst met resource groepen op de doel locatie van het abonnement.
    - Voor het **virtuele netwerk** van het doel selecteert u het netwerk in een lijst met virtuele netwerken op de doel locatie.
    - Voor **beschikbaarheidsset** kunt u instellingen voor beschikbaarheids sets toevoegen aan de VM als deze deel uitmaken van een beschikbaarheidsset in de bron regio.
    - Selecteer voor **doel opslag accounts** het account dat u wilt gebruiken.

3. Selecteer **aanpassen** naast instellingen voor opslag versleuteling om de doel-des te selecteren voor elke door de klant beheerde sleutel (CMK) die beheerde bron beheert. Op het moment van de selectie kunt u ook zien aan welke doel sleutel kluis de DES is gekoppeld.

4. Selecteer **doel bron maken**  >  **replicatie inschakelen**.
5. Nadat de Vm's zijn ingeschakeld voor replicatie, kunt u de status van de virtuele machines controleren onder **gerepliceerde items**.

![Scherm afbeelding die laat zien waar de status van de virtuele machines moet worden gecontroleerd.](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Tijdens de eerste replicatie kan het enige tijd duren voordat de status is vernieuwd, zonder dat de voortgang wordt verduidelijkt. Klik op **vernieuwen**  om de meest recente status op te halen.

## <a name="faqs"></a>Veelgestelde vragen

* Ik heb CMK ingeschakeld voor een bestaand gerepliceerd item, hoe kan ik ervoor zorgen dat CMK ook worden toegepast op de doel regio?

    U kunt de naam van de door de replica beheerde schijf (gemaakt door Azure Site Recovery in de doel regio) opzoeken en DES koppelen aan deze replica schijf. Het is echter niet mogelijk om de DES-gegevens op de Blade schijven weer te geven nadat u deze hebt gekoppeld. U kunt er ook voor kiezen om de replicatie van de virtuele machine uit te scha kelen en deze opnieuw in te scha kelen. Het zorgt ervoor dat de gegevens van DES en sleutel kluis worden weer geven op de Blade schijven voor het gerepliceerde item.

* Ik heb een nieuwe CMK-ingeschakelde schijf toegevoegd aan het gerepliceerde item. Hoe kan ik deze schijf repliceren met Azure Site Recovery?

    Het is niet mogelijk een nieuwe CMK-ingeschakelde schijf toe te voegen aan een bestaand gerepliceerd item. Schakel de replicatie uit en schakel de replicatie voor de virtuele machine opnieuw in.

* Ik heb de door het platform en de klant beheerde sleutels ingeschakeld, hoe kan ik mijn schijven beveiligen?

    Het inschakelen van dubbele versleuteling met zowel het platform als de door de klant beheerde sleutels is suppprted door Site Recovery. Volg de instructies in dit artikel om uw computer te beveiligen. U moet vooraf een met dubbele versleuteling ingeschakelde DES in de doel regio maken. Op het moment dat de replicatie voor een dergelijke virtuele machine wordt ingeschakeld, kunt u deze DES Site Recovery.

