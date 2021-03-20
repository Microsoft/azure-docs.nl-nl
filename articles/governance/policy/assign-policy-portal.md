---
title: Nieuwe beleidstoewijzing met portal
description: In deze quickstart gebruikt u Azure Portal om een Azure Policy-toewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: e5cbf31e897b5be404327efa254eb90ead990f5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99220884"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Een beleidstoewijzing maken om niet-conforme resources te identificeren.

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een beleidstoewijzing maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken.

Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze snelstart maakt u een beleidstoewijzing en wijst u de beleidsdefinitie _Virtuele machines zonder beheerde schijven controleren_ toe.

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Schermopname van het zoeken naar Beleid in Alle services." border="false":::

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina. Een toewijzing is een beleid dat is toegewezen om te worden toegepast binnen een bepaald bereik.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Schermopname van het selecteren van de pagina Toewijzingen op de pagina Overzicht van het beleid." border="false":::

1. Selecteer **Beleid toewijzen** boven in de pagina **Beleidstoewijzingen**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Schermopname van het selecteren van Beleid toewijzen op de pagina Toewijzingen." border="false":::

1. Stel op de pagina **Beleid toewijzen** het **bereik** in door het beletselteken te selecteren en een beheergroep of abonnement te selecteren. U kunt ook een resourcegroep selecteren. Het bereik bepaalt op welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Gebruik vervolgens de knop **Selecteren** onderaan de pagina **Bereik**.

   In dit voorbeeld wordt het abonnement **Contoso** gebruikt. U hebt waarschijnlijk een ander abonnement.

1. Resources kunnen worden uitgesloten op basis van het **bereik**. **Uitsluitingen** starten op één niveau lager dan het niveau van het **bereik**. **Uitsluitingen** zijn optioneel, dus laat dit veld nu nog leeg.

1. Selecteer het weglatingsteken **Beleidsdefinitie** om de lijst van beschikbare definities te openen. Azure Policy wordt geleverd met ingebouwde beleidsdefinities die u kunt gebruiken. Er zijn allerlei definities beschikbaar, zoals:

   - Tag en waarde afdwingen
   - Tag en waarde toepassen
   - Een tag overnemen van de resourcegroep indien deze ontbreekt

   Zie [Azure Policy-voorbeelden](./samples/index.md) voor een gedeeltelijke lijst met beschikbare ingebouwde beleidsregels.

1. Doorzoek uw beleidsdefinities en zoek de definitie _Controleren van virtuele machines die geen beheerde schijven gebruiken_. Selecteer dat beleid en gebruik vervolgens de knop **Selecteren**.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="Schermopname van het filteren van de beschikbare definities." border="false":::

1. De **Toewijzingsnaam** wordt automatisch ingevuld met de naam van het beleid dat u hebt geselecteerd, maar u kunt dit wijzigen. In dit geval gebruiken we _Controleren van virtuele machines die geen beheerde schijven gebruiken_. U kunt ook een optionele **Beschrijving** opgeven. De beschrijving bevat details over deze beleidstoewijzing.
   **Toegewezen door** wordt automatisch gevuld op basis van de persoon die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Zorg ervoor dat beleids afdwinging is _ingeschakeld_. Zie [beleids toewijzing-afdwingings modus](./concepts/assignment-structure.md#enforcement-mode)voor meer informatie.

1. Selecteer **volgende** onder aan de pagina of het tabblad **para meters** boven aan de pagina om naar het volgende segment van de toewijzings wizard te gaan.

1. Als de beleids definitie is geselecteerd op het tabblad **basis beginselen** , zijn de para meters geconfigureerd op dit tabblad. Omdat de _virtuele machines voor controle die geen gebruik maken van beheerde schijven_ geen para meters hebben, selecteert u **volgende** onder aan de pagina of het tabblad **herstel** bovenaan de pagina om naar het volgende segment van de toewijzings wizard te gaan.

1. Laat **Beheerde identiteit maken** uitgeschakeld. Dit vak _moet_ worden ingeschakeld wanneer het beleid of initiatief beleid bevat met het effect [deployIfNotExists](./concepts/effects.md#deployifnotexists) of [modify](./concepts/effects.md#modify). Omdat het beleid dat voor deze zelfstudie wordt gebruikt deze regel niet bevat, laat u deze optie uitgeschakeld. Zie [Beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) en [Hoe herstelbeveiliging werkt](./how-to/remediate-resources.md#how-remediation-security-works) voor meer informatie.

1. Selecteer **volgende** onder aan de pagina of het tabblad **niet-nalevings berichten** boven aan de pagina om naar het volgende segment van de toewijzings wizard te gaan.

1. Het **niet-nalevings bericht** instellen op _virtuele machines moet een beheerde schijf gebruiken_. Dit aangepaste bericht wordt weer gegeven wanneer een resource wordt geweigerd of voor niet-compatibele resources tijdens een reguliere evaluatie.

1. Selecteer **volgende** onder aan de pagina of het tabblad **controleren + maken** bovenaan de pagina om naar het volgende segment van de toewijzings wizard te gaan.

1. Controleer de geselecteerde opties en selecteer vervolgens **maken** onder aan de pagina.

U kunt nu niet-compatibele resources identificeren om inzicht te krijgen in de nalevingsstatus van uw omgeving.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Selecteer **Naleving** links op de pagina. Zoek dan de beleidstoewijzing _Virtuele machines zonder beheerde schijven controleren_ die u hebt gemaakt.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Schermopname van de compatibiliteitsdetails op de pagina Naleving van het beleid." border="false":::

Als er bestaande resources zijn die niet conform deze nieuwe toewijzing zijn, worden deze weergegeven bij **Niet-conforme resources**.

Als een voorwaarde wordt geëvalueerd ten opzichte van uw bestaande resources en deze waar blijkt te zijn, worden deze resources gemarkeerd als niet-compatibel met het beleid. In de volgende tabel ziet u hoe verschillende beleidsacties werken met de evaluatie van voorwaarden voor de resulterende nalevingsstatus. U kunt de evaluatielogica niet zien in Azure Portal, maar de resultaten voor de nalevingsstatus worden wel weergegeven. Het resultaat voor de nalevingsstatus is compatibel of niet-compatibel.

| Resourcestatus | Effect | Beleidsevaluatie | Nalevingsstatus |
| --- | --- | --- | --- |
| Nieuw of bijgewerkt | Controleren, wijzigen, AuditIfNotExist | True | Niet-compatibel |
| Nieuw of bijgewerkt | Controleren, wijzigen, AuditIfNotExist | False | Compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen, Wijzigen, DeployIfNotExist, AuditIfNotExist | True | Niet-compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen, Wijzigen, DeployIfNotExist, AuditIfNotExist | False | Compatibel |

> [!NOTE]
> Voor de effecten DeployIfNotExist en AuditIfNotExist moet de IF-instructie TRUE en de bestaansvoorwaarde FALSE zijn om niet-compatibel te zijn. Indien TRUE, activeert de IF-voorwaarde de evaluatie van de bestaansvoorwaarde voor de gerelateerde resources.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, volgt u deze stappen:

1. Selecteer **Naleving** (of **Toewijzingen**) aan de linkerkant van de pagina Azure Policy en zoek de beleidstoewijzing _Controleren van virtuele machines die geen beheerde schijven gebruiken_ die u hebt gemaakt.

1. Klik met de rechtermuisknop op de beleidstoewijzing _Controleer virtuele machines die niet gebruikmaken van beheerde schijven_ en selecteer **Toewijzing verwijderen**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Schermopname van het gebruik van het contextmenu om een toewijzing te verwijderen van de pagina Naleving." border="false":::

## <a name="next-steps"></a>Volgende stappen

In deze snelstart wijst u een beleidsdefinitie toe aan een bereik en evalueert u het rapport voor naleving.
De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)