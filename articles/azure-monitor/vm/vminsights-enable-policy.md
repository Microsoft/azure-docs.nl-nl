---
title: VM Insights inschakelen met behulp van Azure Policy
description: Hierin wordt beschreven hoe u VM Insights inschakelt voor meerdere virtuele machines van Azure of virtuele-machine schaal sets met behulp van Azure Policy.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 51baf009543208fbbfe091238d0215a24761641d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031953"
---
# <a name="enable-vm-insights-by-using-azure-policy"></a>VM Insights inschakelen met behulp van Azure Policy
In dit artikel wordt uitgelegd hoe u met Azure Policy VM-inzichten kunt inschakelen voor virtuele Azure-machines of een hybride virtuele machine die is verbonden met Azure Arc (preview). Met Azure Policy kunt u beleids definities toewijzen waarmee de vereiste agents voor VM-inzichten in uw Azure-omgeving worden geïnstalleerd en de bewaking voor Vm's automatisch wordt ingeschakeld wanneer elke virtuele machine wordt gemaakt. VM Insights biedt een functie waarmee niet-compatibele Vm's in uw omgeving kunnen worden gedetecteerd en hersteld. Gebruik deze functie in plaats van rechtstreeks te werken met Azure Policy.

Als u niet bekend bent met Azure Policy, kunt u een korte inleiding vinden op de [implementatie Azure monitor op schaal met behulp van Azure Policy](../deploy-scale.md).

> [!NOTE]
> Als u Azure Policy wilt gebruiken met virtuele-machine schaal sets van Azure, of als u rechtstreeks met Azure Policy wilt werken om virtuele Azure-machines in te scha kelen, raadpleegt u [Azure monitor op schaal implementeren met behulp van Azure Policy](../deploy-scale.md#vm-insights).

## <a name="prerequisites"></a>Vereisten
- [Een log Analytics-werk ruimte maken en configureren](./vminsights-configure-workspace.md).
- Zie [ondersteunde besturings systemen](./vminsights-enable-overview.md#supported-operating-systems) om ervoor te zorgen dat het besturings systeem van de virtuele machine of virtuele-machine schaalset die u inschakelt, wordt ondersteund. 


## <a name="vm-insights-initiative"></a>VM Insights-initiatief
VM Insights biedt ingebouwde beleids definities voor het installeren van de Log Analytics agent en de afhankelijkheids agent op virtuele machines van Azure. Het initiatief **schakelt VM Insights in** met elk van deze beleids definities. Wijs dit initiatief toe aan een beheer groep, abonnement of resource groep om de agents automatisch te installeren op virtuele Windows-of Linux Azure-machines in dat bereik.

## <a name="open-policy-coverage-feature"></a>Functie beleids dekking openen
Als u toegang wilt krijgen tot de **beleids dekking van de VM Insights**, gaat u naar de **virtuele machines** in het menu **Azure monitor** van de Azure Portal. Selecteer **andere opties voor onboarding** en **Schakel vervolgens inschakelen** **via beleid inschakelen** in.

[![Azure Monitor van het tabblad aan de slag met Vm's](./media/vminsights-enable-policy/get-started-page.png)](./media/vminsights-enable-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>Nieuwe toewijzing maken
Als u nog geen toewijzing hebt, maakt u een nieuwe en klikt u op **beleid toewijzen**.

[![Toewijzing maken](media/vminsights-enable-policy/create-assignment.png)](media/vminsights-enable-policy/create-assignment.png#lightbox)

Dit is dezelfde pagina om een initiatief in Azure Policy toe te wijzen, behalve dat het is vastgelegd in het bereik dat u hebt geselecteerd en de definitie van de **VM Insights** -initiatief inschakelen. U kunt desgewenst de naam van de **toewijzing** wijzigen en een **Beschrijving** toevoegen. Selecteer **uitsluitingen** als u een uitsluiting wilt maken voor het bereik. Uw bereik kan bijvoorbeeld een beheer groep zijn, en u kunt een abonnement opgeven in die beheer groep die moet worden uitgesloten van de toewijzing.

[![Initiatief toewijzen](media/vminsights-enable-policy/assign-initiative.png)](media/vminsights-enable-policy/assign-initiative.png#lightbox)

Selecteer op de pagina **para meters** een **log Analytics werkruimte** die door alle virtuele machines in de toewijzing moet worden gebruikt. Als u verschillende werk ruimten voor verschillende virtuele machines wilt opgeven, moet u meerdere toewijzingen maken, elk met een eigen bereik. 

   > [!NOTE]
   > Als de werk ruimte zich buiten het bereik van de toewijzing bevindt, moet u *log Analytics Inzender* machtigingen verlenen aan de principal-id van de beleids toewijzing. Als u dit niet doet, ziet u mogelijk een implementatie fout zoals `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![Werk ruimte](media/vminsights-enable-policy/assignment-workspace.png)](media/vminsights-enable-policy/assignment-workspace.png#lightbox)

Klik op **controleren + maken** om de details van de toewijzing te controleren voordat u op **maken** klikt om deze te maken. Maak op dit moment geen herstel taak, omdat u waarschijnlijk meerdere herstel taken nodig hebt om bestaande virtuele machines in te scha kelen. Zie [nalevings resultaten herstellen](#remediate-compliance-results) hieronder.

## <a name="review-compliance"></a>Naleving controleren
Zodra een toewijzing is gemaakt, kunt u de dekking voor het inschakelen van **VM Insights** -initiatief in uw beheer groepen en abonnementen controleren en beheren. Hiermee wordt weer gegeven hoeveel virtuele machines er bestaan in elk van de beheer groepen of-abonnementen en de nalevings status ervan.

[![Pagina beleid voor beheer van virtuele machines](media/vminsights-enable-policy/manage-policy-page-01.png)](media/vminsights-enable-policy/manage-policy-page-01.png#lightbox)


De volgende tabel bevat een beschrijving van de informatie in deze weer gave.

| Functie | Beschrijving | 
|----------|-------------| 
| **Bereik** | Beheer groep en abonnementen waarvoor u toegang hebt of die u hebt overgenomen, met de mogelijkheid om in te zoomen op de hiërarchie van de beheer groep.|
| **Role** | Uw rol in het bereik, die lezer, eigenaar of Inzender kan zijn. Dit is leeg als u toegang tot het abonnement hebt, maar niet tot de beheer groep waarvan het deel uitmaakt. Deze rol bepaalt welke gegevens u kunt zien en welke acties u kunt uitvoeren met betrekking tot het toewijzen van beleid of initiatieven (eigenaar), het bewerken ervan of het weer geven van naleving. |
| **Totaal aantal Vm's** | Het totale aantal virtuele machines in dat bereik, ongeacht hun status. Voor een beheer groep is dit een totaal van de Vm's die zijn genest onder de abonnementen of onderliggende beheer groepen. |
| **Toewijzings dekking** | Percentage Vm's die worden gedekt door het initiatief. |
| **Toewijzings status** | **Geslaagd** : voor alle vm's in het bereik zijn de log Analytics-en Dependency agents geïmplementeerd.<br>**Waarschuwing** : het abonnement bevindt zich niet in een beheer groep.<br>**Niet gestart** : er is een nieuwe toewijzing toegevoegd.<br>**Vergren deling** : u beschikt niet over voldoende bevoegdheden voor de beheer groep.<br>**Leeg** : er zijn geen vm's of er is geen beleid toegewezen. |
| **Compatibele Vm's** | Het aantal Vm's dat compatibel is. Dit is het aantal Vm's waarop zowel Log Analytics agent als de afhankelijkheids agent zijn geïnstalleerd. Dit is leeg als er geen toewijzingen zijn, geen Vm's in het bereik of niet de juiste machtigingen hebben. |
| **Naleving** | Het algemene compatibiliteits nummer is de som van afzonderlijke resources die compatibel zijn gedeeld door de som van alle afzonderlijke resources. |
| **Nalevingsstatus** | **Compatibel** : voor alle vm's in de virtuele machines van het bereik zijn de log Analytics-en afhankelijkheids agenten geïmplementeerd, of eventuele nieuwe vm's in het bereik waarop de toewijzing van toepassing is, zijn nog niet geëvalueerd.<br>**Niet-compatibel** : er zijn vm's die zijn geëvalueerd, maar niet zijn ingeschakeld en mogelijk moeten worden hersteld.<br>**Niet gestart** : er is een nieuwe toewijzing toegevoegd.<br>**Vergren deling** : u beschikt niet over voldoende bevoegdheden voor de beheer groep.<br>**Leeg** : er is geen beleid toegewezen.  |


Wanneer u het initiatief toewijst, kan het bereik dat u hebt geselecteerd in de toewijzing het bereik of een subset hiervan zijn. U hebt bijvoorbeeld een toewijzing voor een abonnement (beleids bereik) gemaakt en geen beheer groep (dekkings bereik). In dit geval geeft de waarde van **toewijzings dekking** de vm's aan in het initiatief bereik gedeeld door de vm's in het dekkings bereik. In andere gevallen hebt u mogelijk enkele Vm's, resource groepen of een abonnement uitgesloten van het beleids bereik. Als de waarde leeg is, geeft dit aan dat het beleid of initiatief niet bestaat of dat u geen toestemming hebt. Informatie wordt weer gegeven onder de **toewijzings status**.


## <a name="remediate-compliance-results"></a>Nalevings resultaten herstellen
Het initiatief wordt toegepast op virtuele machines die worden gemaakt of gewijzigd, maar worden niet toegepast op bestaande Vm's. Als uw toewijzing geen naleving van 100% weergeeft, maakt u herstel taken om bestaande Vm's te evalueren en in te scha kelen, selecteert u **naleving weer geven** door het weglatings teken (...) te selecteren.

[![Naleving weer geven](media/vminsights-enable-policy/view-compliance.png)](media/vminsights-enable-policy/view-compliance.png#lightbox)

Op de pagina **naleving** wordt een lijst weer gegeven met de toewijzingen die overeenkomen met het opgegeven filter en of ze compatibel zijn. Klik op een toewijzing om de details ervan weer te geven.

[![Naleving van beleid voor Azure-Vm's](./media/vminsights-enable-policy/policy-view-compliance.png)](./media/vminsights-enable-policy/policy-view-compliance.png#lightbox)

De **compliance** -pagina van het initiatief geeft een lijst van de beleids definities in het initiatief en of elk compatibel is.

[![Compatibiliteits Details](media/vminsights-enable-policy/compliance-details.png)](media/vminsights-enable-policy/compliance-details.png#lightbox)

Klik op een beleids definitie om de details ervan weer te geven. Scenario's waarin beleids definities worden weer gegeven als niet-naleving, zijn onder meer het volgende:

* Log Analytics agent of afhankelijkheids agent is niet geïmplementeerd. Maak een herstel taak om te verminderen.
* VM-installatie kopie (OS) wordt niet geïdentificeerd in de beleids definitie. De criteria van het implementatie beleid bevatten alleen de Vm's die zijn geïmplementeerd op basis van bekende VM-installatie kopieën van Azure. Raadpleeg de documentatie om te controleren of het VM-besturings systeem wordt ondersteund.
* Vm's worden niet geregistreerd in de opgegeven Log Analytics-werk ruimte. Sommige Vm's in het initiatief bereik zijn verbonden met een andere Log Analytics werk ruimte dan de virtuele machines die zijn opgegeven in de beleids toewijzing.

[![Details van beleids naleving](media/vminsights-enable-policy/policy-compliance-details.png)](media/vminsights-enable-policy/policy-compliance-details.png#lightbox)

Als u een herstel taak wilt maken om nalevings problemen te verhelpen, klikt u op **herstel taak maken**. 

[![Nieuwe herstel taak](media/vminsights-enable-policy/new-remediation-task.png)](media/vminsights-enable-policy/new-remediation-task.png#lightbox)

Klik op **herstellen** om de herstel taak te maken en vervolgens te **herstellen** om deze te starten. Waarschijnlijk moet u meerdere herstel taken maken, één voor elke beleids definitie. U kunt geen herstel taak maken voor een initiatief.

[![Scherm opname toont het deel venster voor het herstellen van beleid voor monitor | Virtual Machines.](media/vminsights-enable-policy/remediation.png)](media/vminsights-enable-policy/remediation.png#lightbox)


Zodra de herstel taken zijn voltooid, moeten de virtuele machines voldoen aan de agents die zijn geïnstalleerd en ingeschakeld voor VM Insights. 

## <a name="next-steps"></a>Volgende stappen

Nu de bewaking voor uw virtuele machines is ingeschakeld, is deze informatie beschikbaar voor analyse met VM Insights. 

- Als u gedetecteerde toepassings afhankelijkheden wilt weer geven, raadpleegt u [VM Insights-toewijzing weer geven](vminsights-maps.md). 
- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten en het algehele gebruik van de VM-prestaties te identificeren.
