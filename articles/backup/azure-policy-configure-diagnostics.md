---
title: Diagnostische instellingen voor de kluis op schaal configureren
description: Log Analytics Diagnostische instellingen configureren voor alle kluizen in een bepaald bereik met behulp van Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 55461937381f7551c42714c835d4755ab65f175b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92171529"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Diagnostische instellingen voor de kluis op schaal configureren

De rapportage oplossing die wordt verschaft door Azure Backup maakt gebruik van Log Analytics (LA). Voor de gegevens van een bepaalde kluis die naar LA moeten worden verzonden, moet een [Diagnostische instelling](./backup-azure-diagnostic-events.md) worden gemaakt voor die kluis.

Vaak is het toevoegen van een diagnostische instelling hand matig per kluis een lastigere taak. Daarnaast moet voor elke nieuwe kluis die wordt gemaakt, diagnostische instellingen zijn ingeschakeld om rapporten voor deze kluis te kunnen weer geven.

Azure Backup biedt een ingebouwde [Azure Policy](../governance/policy/index.yml)om het maken van diagnostische instellingen op schaal te vereenvoudigen (met La als doel). Dit beleid voegt een instelling voor diagnostische gegevens van LA toe aan alle kluizen in een bepaald abonnement of resource groep. De volgende secties bevatten instructies over het gebruik van dit beleid.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Het beleid kan in één keer worden toegepast op alle Recovery Services kluizen in een bepaald abonnement (of op een resource groep in het abonnement). De gebruiker die het beleid toewijst, moet **eigenaar** toegang hebben tot het abonnement waaraan het beleid is toegewezen.

* De werk ruimte LA zoals opgegeven door de gebruiker (waarnaar diagnostische gegevens worden verzonden) kan zich in een ander abonnement bevinden dan de kluizen waaraan het beleid is toegewezen. De gebruiker moet toegang hebben tot de **lezer**, **Inzender** of **eigenaar** van het abonnement waarin de opgegeven La-werk ruimte zich bevindt.

* Het bereik van de beheer groep wordt momenteel niet ondersteund.

* Het ingebouwde beleid is momenteel niet beschikbaar in nationale Clouds.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Het ingebouwde beleid toewijzen aan een bereik

Volg de onderstaande stappen om het beleid voor kluizen toe te wijzen in het vereiste bereik:

1. Meld u aan bij de Azure Portal en navigeer naar het **beleids** dashboard.
2. Selecteer **definities** in het linkermenu om een lijst op te halen met alle ingebouwde beleids regels voor Azure-resources.
3. Filter de lijst voor **Category = backup**. Zoek het beleid met de naam **[Preview]: Diagnostische instellingen voor Recovery Services kluis implementeren in log Analytics werk ruimte voor resource-specifieke categorieën**.

    ![Deel venster beleids definitie](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Selecteer de naam van het beleid. U wordt omgeleid naar de gedetailleerde definitie van dit beleid.

    ![Gedetailleerde beleids definitie](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Selecteer de knop **toewijzen** boven aan het deel venster. Hiermee wordt u omgeleid naar het deel venster **beleid toewijzen** .

6. Selecteer onder **basis beginselen** de drie punten naast het veld **bereik** . Hiermee opent u een context paneel met de rechter muisknop waarin u het abonnement kunt selecteren waarop het beleid moet worden toegepast. U kunt desgewenst ook een resource groep selecteren, zodat het beleid alleen wordt toegepast op kluizen in een bepaalde resource groep.

    ![Basis principes van beleids toewijzing](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Onder **para meters** voert u de volgende gegevens in:

    * **Profiel naam** : de naam die wordt toegewezen aan de diagnostische instellingen die door het beleid zijn gemaakt.
    * **Log Analytics-werk ruimte** : de log Analytics werk ruimte waaraan de diagnostische instelling moet worden gekoppeld. Diagnostische gegevens van alle kluizen in het bereik van de beleids toewijzing worden gepusht naar de opgegeven LA-werk ruimte.

    * **Naam van het uitsluitings label (optioneel) en de uitzonderings label waarde (optioneel)** : u kunt ervoor kiezen om de kluizen uit te sluiten van een bepaalde label naam en-waarde uit de beleids toewijzing. Als u bijvoorbeeld **niet** wilt dat een diagnostische instelling wordt toegevoegd aan de kluizen waarvan de tag ' isTest ' is ingesteld op de waarde ' ja ', moet u ' isTest ' invoeren in het veld met de naam van het **uitsluitings label** en ' ja ' in het veld met de uitzonderings **label waarde** . Als een (of beide) van deze twee velden leeg blijven, wordt het beleid toegepast op alle relevante kluizen, ongeacht de tags die ze bevatten.

    ![Beleids toewijzings parameters](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Een herstel taak maken** : zodra het beleid is toegewezen aan een bereik, krijgen nieuwe kluizen die in dat bereik zijn gemaakt automatisch de instellingen voor de diagnostische gegevens van de la die zijn geconfigureerd (binnen 30 minuten vanaf het moment waarop de kluis is gemaakt). Als u een diagnostische instelling wilt toevoegen aan bestaande kluizen in het bereik, kunt u een herstel taak activeren op het moment van de beleids toewijzing. Schakel het selectie vakje **een herstel taak maken** in om een herstel taak te activeren.

    ![Herstel van beleids toewijzing](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Ga naar het tabblad **controleren + maken** en selecteer **maken**.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Onder welke voor waarden is de herstel taak van toepassing op een kluis?

De herstel taak wordt toegepast op kluizen die niet-compatibel zijn volgens de definitie van het beleid. Een kluis is niet-compatibel als deze voldoet aan een van de volgende voor waarden:

* Voor de kluis is geen diagnostische instelling aanwezig.
* Er zijn Diagnostische instellingen voor de kluis aanwezig, maar voor geen van de instellingen zijn **alle** resource-specifieke gebeurtenissen ingeschakeld met La as Destination en de **resource-specifiek** geselecteerd in de wissel knop.

Zelfs als een gebruiker een kluis heeft waarvoor de gebeurtenis AzureBackupReport is ingeschakeld in de AzureDiagnostics-modus (die wordt ondersteund door de back-uprapporten), is de herstel taak nog steeds van toepassing op deze [kluis, omdat](./backup-azure-diagnostic-events.md#legacy-event)de resource-specifieke modus de aanbevolen manier is om Diagnostische instellingen te maken.

Als een gebruiker echter een kluis heeft met slechts een subset van de zes resource-specifieke gebeurtenissen ingeschakeld, is de herstel taak van toepassing op deze kluis, omdat back-uprapporten alleen zoals verwacht werken als alle zes specifieke gebeurtenissen voor de resource zijn ingeschakeld.

> [!NOTE]
>
> Als een kluis een bestaande diagnostische instelling heeft met een **subset van resource-specifieke** categorieën die zijn ingeschakeld, is geconfigureerd om gegevens te verzenden naar een bepaalde La-werk ruimte, zeg ' werk ruimte X ', dan kan de herstel taak niet worden uitgevoerd (alleen voor deze kluis) als de doel-La-werk ruimte die is opgegeven in de beleids toewijzing **dezelfde** ' werk ruimte X ' is.
>
>Dit komt doordat als de gebeurtenissen die door twee verschillende diagnostische instellingen op dezelfde bron zijn ingeschakeld, in sommige vorm **elkaar overlappen** , de instellingen niet dezelfde La-werk ruimte als de bestemming kunnen hebben. U moet deze fout hand matig oplossen door te navigeren naar de relevante kluis en een diagnostische instelling te configureren met een andere LA-werk ruimte als bestemming.
>
> Houd er rekening mee dat de herstel taak **niet** kan mislukken als de bestaande diagnostische instelling als alleen AzureBackupReport is ingeschakeld met de werk ruimte X als de bestemming, omdat in dit geval er geen overlap is tussen de gebeurtenissen die worden ingeschakeld door de bestaande instelling en de gebeurtenissen die zijn ingeschakeld door de instelling die is gemaakt door de herstel taak.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het gebruik van back-uprapporten](./configure-reports.md)
* [Meer informatie over Azure Policy](../governance/policy/index.yml)
* [Azure Policy gebruiken om back-ups automatisch in te scha kelen voor alle Vm's in een bereik opgeven](./backup-azure-auto-enable-backup.md)
