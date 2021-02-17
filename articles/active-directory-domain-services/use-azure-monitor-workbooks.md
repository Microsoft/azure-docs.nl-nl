---
title: Azure Monitor werkmappen gebruiken met Azure AD Domain Services | Microsoft Docs
description: Informatie over het gebruik van Azure Monitor werkmappen om beveiligings controles te controleren en problemen in een Azure Active Directory Domain Services beheerd domein te begrijpen.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 9a126fe4676f340fb45677382a3aeeed79291f0b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574211"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Beveiligings controle gebeurtenissen in Azure Active Directory Domain Services controleren met behulp van Azure Monitor werkmappen

Als u de status van uw Azure Active Directory Domain Services (Azure AD DS) beheerde domein wilt begrijpen, kunt u beveiligings controle gebeurtenissen inschakelen. Deze beveiligings controle gebeurtenissen kunnen vervolgens worden gecontroleerd met behulp van Azure Monitor werkmappen die tekst, analyse query's en para meters in uitgebreide interactieve rapporten combi neren. Azure AD DS bevat werkmap sjablonen voor beveiligings overzicht en account activiteit waarmee u controle gebeurtenissen kunt bewaken en uw omgeving beheert.

In dit artikel leest u hoe u Azure Monitor werkmappen kunt gebruiken om beveiligings controle gebeurtenissen in azure AD DS te controleren.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
    * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
* Beveiligings controle gebeurtenissen ingeschakeld voor uw beheerde domein waarmee gegevens worden gestreamd naar een Log Analytics-werk ruimte.
    * Schakel, indien nodig, [beveiligings controles in voor Azure AD DS][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Overzicht van Azure Monitor werkmappen

Wanneer beveiligings controle gebeurtenissen zijn ingeschakeld in azure AD DS, kan het lastig zijn om problemen in het beheerde domein te analyseren en te identificeren. Met Azure Monitor kunt u deze gebeurtenissen voor beveiligings controle samen stellen en query's uitvoeren op de gegevens. Met Azure Monitor werkmappen kunt u deze gegevens visualiseren om sneller en gemakkelijker problemen te identificeren.

Werkmap sjablonen zijn bedrijfsspecifieke rapporten die zijn ontworpen voor flexibel hergebruik door meerdere gebruikers en teams. Wanneer u een werkmap sjabloon opent, worden de gegevens uit uw Azure Monitor omgeving geladen. U kunt sjablonen gebruiken zonder dat dit invloed heeft op andere gebruikers in uw organisatie en kunnen uw eigen werkmappen opslaan op basis van de sjabloon.

Azure AD DS bevat de volgende twee werkmap sjablonen:

* Rapport beveiligings overzicht
* Rapport account activiteit

Zie [Azure monitor Workbooks Overview](../azure-monitor/visualize/workbooks-overview.md)(Engelstalig) voor meer informatie over het bewerken en beheren van werkmappen.

## <a name="use-the-security-overview-report-workbook"></a>De werkmap voor het beveiligings overzicht rapport gebruiken

Om u te helpen beter inzicht te krijgen in het gebruik en mogelijke beveiligings Risico's te identificeren, wordt in het overzichts rapport van de beveiliging een overzicht gegeven van de aanmeldings gegevens en worden accounts geïdentificeerd die u mogelijk wilt controleren. U kunt gebeurtenissen in een bepaald datum bereik weer geven en inzoomen op specifieke aanmeldings gebeurtenissen, zoals een onjuist wacht woord of de locatie waar het account is uitgeschakeld.

Voer de volgende stappen uit om toegang te krijgen tot de werkmap sjabloon voor het rapport beveiligings overzicht:

1. Zoek en selecteer **Azure Active Directory Domain Services** in het Azure Portal.
1. Selecteer uw beheerde domein, zoals *aaddscontoso.com*
1. Kies in het menu aan de linkerkant de optie **bewaking > werkmappen**

    ![Scherm opname van Hightlights waar u het rapport beveiligings overzicht en account activiteit selecteert.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Kies het **rapport beveiligings overzicht**.
1. Selecteer uw Azure-abonnement in de vervolg keuzelijst boven aan de werkmap en vervolgens een Azure Monitor-werk ruimte.

    Kies een **tijds bereik**, zoals de *laatste 7 dagen*, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

    ![Selecteer de menu optie werkmappen in de Azure Portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    De **tegel weergave** en **grafiek weergave** opties kunnen ook worden gewijzigd om de gegevens naar wens te analyseren en te visualiseren.

1. Als u wilt inzoomen op een specifiek gebeurtenis type, selecteert u de kaarten voor het **aanmeldings resultaat** zoals *account vergrendeld*, zoals wordt weer gegeven in het volgende voor beeld:

    ![Voor beeld van een rapport met overzichts gegevens over beveiliging in Azure Monitor werkmappen](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Het onderste gedeelte van het beveiligings overzicht rapport onder de grafiek wordt vervolgens het geselecteerde type activiteit gesplitst. U kunt filteren op gebruikers namen die aan de rechter kant zijn betrokken, zoals wordt weer gegeven in het volgende voorbeeld rapport:

    [![Details van account vergrendelingen in Azure Monitor werkmappen.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>De werkmap van het rapport account activiteit gebruiken

Om u te helpen problemen voor een specifiek gebruikers account op te lossen, onderbreekt het rapport account activiteit gedetailleerde informatie over het controle gebeurtenis logboek. U kunt controleren wanneer een onjuiste gebruikers naam of wacht woord is opgegeven tijdens het aanmelden en de bron van de aanmeldings poging.

Voer de volgende stappen uit om toegang te krijgen tot de werkmap sjabloon voor het rapport account activiteit:

1. Zoek en selecteer **Azure Active Directory Domain Services** in het Azure Portal.
1. Selecteer uw beheerde domein, zoals *aaddscontoso.com*
1. Kies in het menu aan de linkerkant de optie **bewaking > werkmappen**
1. Kies het **rapport account activiteit**.
1. Selecteer uw Azure-abonnement in de vervolg keuzelijst boven aan de werkmap en vervolgens een Azure Monitor-werk ruimte.

    Kies een **tijds bereik**, bijvoorbeeld de *laatste 30 dagen*, en hoe de **tegel weergave** de gegevens moet vertegenwoordigen.

    U kunt filteren op de **gebruikers naam** van het account, zoals *Felix*, zoals wordt weer gegeven in het volgende voorbeeld rapport:

    [![Account activiteit rapport in Azure Monitor-werkmappen.](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    In het gebied onder de grafiek worden afzonderlijke aanmeldings gebeurtenissen weer gegeven, samen met informatie zoals het resultaat van de activiteit en het bron werkstation. Deze informatie kan u helpen bij het bepalen van herhaalde bronnen van aanmeldings gebeurtenissen die kunnen leiden tot het vergren delen van accounts of het aanduiden van een mogelijke aanval.

Net als bij het rapport beveiligings overzicht kunt u inzoomen op de verschillende tegels boven aan het rapport om de gegevens naar behoefte te visualiseren en te analyseren.

## <a name="save-and-edit-workbooks"></a>Werkmappen opslaan en bewerken

De twee sjabloon-werkmappen die door Azure AD DS worden verschaft, zijn een goede plaats om te beginnen met uw eigen gegevens analyse. Als u meer gedetailleerde informatie over de gegevens query's en onderzoeken nodig hebt, kunt u uw eigen werkmappen opslaan en de query's bewerken.

1. Als u een kopie van een van de werkmap sjablonen wilt opslaan, selecteert u **bewerken > opslaan als > gedeelde rapporten**. vervolgens geeft u een naam op en slaat u deze op.
1. Selecteer in uw eigen kopie van de sjabloon **bewerken** om de bewerkings modus in te voeren. U kunt de knop voor blauw **bewerken** kiezen naast een deel van het rapport en deze wijzigen.

Alle grafieken en tabellen in Azure Monitor werkmappen worden gegenereerd met behulp van Kusto-query's. Zie [Azure monitor-logboek query's][azure-monitor-queries] en [Kusto query's][kusto-queries]voor meer informatie over het maken van uw eigen query's.

## <a name="next-steps"></a>Volgende stappen

Als u het wacht woord en het vergrendelings beleid wilt aanpassen, raadpleegt u [wacht woord-en account vergrendelings beleid in beheerde domeinen][password-policy].

Voor problemen met gebruikers leert u hoe u problemen met [account aanmelding][troubleshoot-sign-in] of [account vergrendelings][troubleshoot-account-lockout]problemen oplost.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer