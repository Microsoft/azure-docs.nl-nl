---
title: 'Zelfstudie: nieuwe beleidstoewijzing met Azure-portal'
description: In deze zelfstudie gebruikt u de Azure-portal om een Azure Policy-toewijzing te maken om niet-compatibele resources te identificeren.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: 20cb8125a157a3ce97c88278c99f90936b449b65
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831650"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Zelfstudie: Een beleidstoewijzing maken om niet-conforme resources te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources. Azure Policy ondersteunt het controleren van de status van uw voor Arc ingeschakelde server met gastconfiguratiebeleid. Gastconfiguratiebeleid past geen configuraties toe, maar controleert alleen instellingen van de machine. In deze zelfstudie wordt u stapsgewijs begeleid bij het maken en toewijzen van een beleid, waarmee wordt aangegeven op welke van uw Arc-servers de Log Analytics-agent niet is geïnstalleerd.

Aan het einde van dit proces kunt u computers identificeren waarop de Log Analytics-agent voor Windows of Linux niet is geïnstalleerd. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze zelfstudie maakt u een beleidstoewijzing en wijst u de _\[Preview] toe: Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-computers_.

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Beleid zoeken in alle services" border="false":::

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina. Een toewijzing is een beleid dat is toegewezen om te worden toegepast binnen een bepaald bereik.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Toewijzingspagina’s selecteren op de pagina Overzicht van beleid" border="false":::

1. Selecteer **Beleid toewijzen** boven in de pagina **Beleidstoewijzingen**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Een beleidsdefinitie toewijzen op de pagina Toewijzingen" border="false":::

1. Selecteer op de pagina **Beleid toewijzen** het **bereik** door te klikken op het beletselteken en een beheergroep of abonnement te selecteren. U kunt ook een resourcegroep selecteren. Het bereik bepaalt op welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Klik vervolgens op **Selecteren** aan de onderkant van de pagina **Bereik**.

   In dit voorbeeld wordt het abonnement **Parnell Aerospace** gebruikt. U hebt waarschijnlijk een ander abonnement.

1. Resources kunnen worden uitgesloten op basis van het **bereik**. **Uitsluitingen** starten op één niveau lager dan het niveau van het **bereik**. **Uitsluitingen** zijn optioneel, dus laat dit veld nu nog leeg.

1. Selecteer het weglatingsteken **Beleidsdefinitie** om de lijst van beschikbare definities te openen. Azure Policy wordt geleverd met ingebouwde beleidsdefinities die u kunt gebruiken. Er zijn allerlei definities beschikbaar, zoals:

   - Tag en waarde afdwingen
   - Tag en waarde toepassen
   - Een tag overnemen van de resourcegroep indien deze ontbreekt

   Zie [Azure Policy-voorbeelden](../../../governance/policy/samples/index.md) voor een gedeeltelijke lijst met beschikbare ingebouwde beleidsregels.

1. Zoek in de lijst met beleidsdefinities naar de definitie _\[Voorbeeld]: De Log Analytics-agent moet zijn geïnstalleerd op uw Windows Azure Arc-computers_ als u de agent voor Arc-servers hebt ingeschakeld op een Windows-computer. Voor een Linux-computer zoekt u de bijbehorende beleidsdefinitie _\[Voorbeeld]: Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-computers_. Klik op dit beleid en vervolgens op **Selecteren**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="De juiste beleidsdefinitie vinden" border="false":::

1. De **Toewijzingsnaam** wordt automatisch ingevuld met de naam van het beleid dat u hebt geselecteerd, maar u kunt dit wijzigen. Laat voor dit voorbeeld _\[Voorbeeld]: Log Analytics-agent moet zijn geïnstalleerd op uw Windows Azure Arc-computers_ of _\[Voorbeeld]: Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-computers_ ingeschakeld. U kunt ook een optionele **Beschrijving** opgeven. De beschrijving bevat details over deze beleidstoewijzing.
   **Toegewezen door** wordt automatisch gevuld op basis van de persoon die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Laat **Beheerde identiteit maken** uitgeschakeld. Deze optie _moet_ worden ingeschakeld wanneer het beleid of initiatief een beleidsregel bevat met het effect [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists). Omdat het beleid dat voor deze zelfstudie wordt gebruikt deze regel niet bevat, laat u deze optie uitgeschakeld. Zie [Beheerde identiteiten](../../../active-directory/managed-identities-azure-resources/overview.md) en [Hoe herstelbeveiliging werkt](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works) voor meer informatie.

1. Klik op **Toewijzen**.

U kunt nu niet-compatibele resources identificeren om inzicht te krijgen in de nalevingsstatus van uw omgeving.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Selecteer **Naleving** links op de pagina. Zoek vervolgens de beleidstoewijzing **\[Voorbeeld]: Log Analytics-agent moet zijn geïnstalleerd op uw Windows Azure Arc-computers** of **\[Voorbeeld]: Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-computers** die u hebt gemaakt.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Compatibiliteitsdetails op de pagina Naleving van het beleid" border="false":::

Als er bestaande resources zijn die niet conform deze nieuwe toewijzing zijn, worden deze weergegeven bij **Niet-conforme resources**.

Als een voorwaarde wordt geëvalueerd ten opzichte van uw bestaande resources en deze waar blijkt te zijn, worden deze resources gemarkeerd als niet-compatibel met het beleid. In de volgende tabel ziet u hoe verschillende beleidsacties werken met de evaluatie van voorwaarden voor de resulterende nalevingsstatus. U kunt de evaluatielogica niet zien in Azure Portal, maar de resultaten voor de nalevingsstatus worden wel weergegeven. Het resultaat voor de nalevingsstatus is compatibel of niet-compatibel.

| **Resourcestatus** | **Effect** | **Beleidsevaluatie** | **Nalevingsstatus** |
| --- | --- | --- | --- |
| Bestaat | Weigeren, Controleren, Toevoegen\*, DeployIfNotExist\*, AuditIfNotExist\* | Waar | Niet-compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen\*, DeployIfNotExist\*, AuditIfNotExist\* | Niet waar | Compliant |
| Nieuw | Controleren, AuditIfNotExist\* | Waar | Niet-compatibel |
| Nieuw | Controleren, AuditIfNotExist\* | Niet waar | Compliant |

\*Voor de acties Toevoegen, DeployIfNotExist en AuditIfNotExist moet de IF-instructie TRUE zijn.
De acties vereisen ook dat de bestaansvoorwaarde FALSE is om niet-compatibel te zijn. Indien TRUE, activeert de IF-voorwaarde de evaluatie van de bestaansvoorwaarde voor de gerelateerde resources.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, volgt u deze stappen:

1. Selecteer **Naleving** (of **Toewijzingen**) aan de linkerkant van de pagina van Azure Policy en zoek de beleidstoewijzing **\[Voorbeeld]: Log Analytics-agent moet zijn geïnstalleerd op uw Windows Azure Arc-computers** of **\[Voorbeeld]: Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-computers** die u hebt gemaakt.

1. Klik met de rechtermuisknop op de beleidstoewijzing en selecteer **Toewijzing verwijderen**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Een toewijzing verwijderen van de pagina Naleving" border="false":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een beleidsdefinitie toegewezen aan een bereik en het bijbehorende nalevingsrapport geëvalueerd. De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn. U bent nu klaar om uw computer met Azure Arc servers te bewaken door [VM-inzichten in te stellen.](../../../azure-monitor/vm/vminsights-overview.md)

Ga verder met de volgende zelfstudie voor meer informatie over het bewaken en weergeven van de prestaties, het uitgevoerde proces en hun afhankelijkheden vanaf uw computer:

> [!div class="nextstepaction"]
> [VM-inzichten inschakelen](tutorial-enable-vm-insights.md)