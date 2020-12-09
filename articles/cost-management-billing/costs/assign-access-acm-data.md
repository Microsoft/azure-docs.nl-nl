---
title: Toegang tot gegevens van Azure Cost Management toewijzen
description: In dit artikel vindt u informatie over het toewijzen van machtigingen voor gegevens van Azure Cost Management voor verschillende toegangsbereiken.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: 660b5751e3b3cbc632331e99d797af3392a8aea4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "91371963"
---
# <a name="assign-access-to-cost-management-data"></a>Toegang tot gegevens van Cost Management toewijzen

Voor gebruikers met Azure Enterprise-overeenkomsten wordt het toegangsniveau van gebruikers tot gegevens van Azure Cost Management bepaald door een combinatie van machtigingen die in de Azure-portal en de Enterprise-portal (EA) worden verleend. Voor gebruikers met andere typen Azure-accounts kunt u het toegangsniveau van gebruikers tot gegevens van Cost Management eenvoudiger definiëren met behulp van op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). In dit artikel wordt uitgelegd hoe u toegang tot gegevens van Cost Management toewijst. Nadat de combinatie van machtigingen is toegewezen, kunnen de gebruikers gegevens in Cost Management weergeven op basis van hun toegangsbereik en het bereik dat ze selecteren in de Azure-portal.

Het bereik dat een gebruiker selecteert wordt door Cost Management gebruikt om gegevens te bundelen en toegang tot de kostengegevens te beheren. Wanneer u bereiken gebruikt, kunnen gebruikers er niet meerdere selecteren. In plaats daarvan selecteren gebruikers een groter bereik waarin onderliggende bereiken worden getotaliseerd en filteren ze het bereik op wat ze willen weergeven. Het is belangrijk om te begrijpen hoe gegevens worden gebundeld, omdat bepaalde personen geen toegang mogen hebben tot een bovenliggend bereik waartoe een onderliggend bereik behoort.

Bekijk de video [Toegangsbeheer met Cost Management ](https://www.youtube.com/watch?v=_uQzQ9puPyM) voor meer informatie over het toewijzen van toegang voor het weergeven van kosten met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Bereiken in Cost Management

Cost Management biedt ondersteuning voor verschillende typen Azure-accounts. Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](understand-cost-mgt-data.md). Het type account bepaalt de beschikbare bereiken.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-abonnementsbereiken

U moet minimaal leestoegang hebben tot een of meer van de volgende bereiken om kostengegevens van Azure EA-abonnementen te kunnen bekijken.

| **Bereik** | **Gedefinieerd op** | **Vereiste toegang voor het weergeven van gegevens** | **Vereiste EA-instelling** | **Bundelt gegevens tot** |
| --- | --- | --- | --- | --- |
| Factureringsaccount<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Enterprise-beheerder | Geen | Alle abonnementen uit de Enterprise Overeenkomst |
| Afdeling | [https://ea.azure.com](https://ea.azure.com/) | Afdelingsbeheerder | **DA-kosten weergeven** ingeschakeld | Alle abonnementen die horen bij een inschrijvingsaccount dat is gekoppeld aan de afdeling |
| Inschrijvingsaccount<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Accounteigenaar | **AO-kosten weergeven** ingeschakeld | Alle abonnementen van het inschrijvingsaccount |
| Beheergroep | [https://portal.azure.com](https://portal.azure.com/) | Cost Management Reader (of Reader) | **AO-kosten weergeven** ingeschakeld | Alle abonnementen onder de beheergroep |
| Abonnement | [https://portal.azure.com](https://portal.azure.com/) | Cost Management Reader (of Reader) | **AO-kosten weergeven** ingeschakeld | Alle resources/resourcegroepen in het abonnement |
| Resourcegroep | [https://portal.azure.com](https://portal.azure.com/) | Cost Management Reader (of Reader) | **AO-kosten weergeven** ingeschakeld | Alle resources in de resourcegroep |

<sup>1</sup>De factureringsrekening wordt ook wel aangeduid als de Enterprise Overeenkomst of Inschrijving.

<sup>2</sup> Het inschrijvingsaccount wordt ook wel aangeduid als de accounteigenaar.


## <a name="other-azure-account-scopes"></a>Andere Azure-accountbereiken

U moet minimaal leestoegang hebben tot een of meer van de volgende bereiken om kostengegevens van andere Azure-abonnementen te kunnen bekijken:

- Azure-account
- Beheergroep
- Resourcegroep

Er zijn verschillende bereiken beschikbaar nadat partners onboarding van klanten voor een Microsoft-klantovereenkomst hebben uitgevoerd. CSP-klanten kunnen vervolgens Cost Management-functies gebruiken wanneer deze zijn ingeschakeld door hun CSP-partner. Zie [Aan de slag met Azure Cost Management voor partners](get-started-partners.md) voor meer informatie.

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Toegang tot kosten in de Azure-portal inschakelen

Voor het afdelingsbereik moet de optie **Afdelingsbeheerders kunnen kosten weergeven** (DA-kosten weergeven) zijn ingesteld op **Aan**. Configureer de optie in de Azure-portal of in de EA-portal. Voor alle andere bereiken moet de optie **Accounteigenaars kunnen kosten weergeven** (AO-kosten weergeven) zijn ingesteld op **Aan**.

Een optie inschakelen in de Azure-portal:

1. Meld u aan bij de Azure-portal op https://portal.azure.com met een ondernemingsbeheerdersaccount.
1. Selecteer het menu-item **Kostenbeheer en facturering**.
1. Selecteer **Factureringsbereiken** om een lijst met beschikbare factureringsbereiken en factureringsaccounts weer te geven.
1. Selecteer uw **Factureringsaccount** in de lijst met beschikbare factureringsaccounts.
1. Selecteer onder **Instellingen** het menu-item **Beleid**, en configureer vervolgens de instelling.  
    ![Beleid voor factureringsbereik met opties voor weergavekosten](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

Nadat de opties voor het weergeven van kosten zijn ingeschakeld, moet voor de meeste bereiken ook de machtiging voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) worden geconfigureerd in Azure Portal.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Toegang tot kosten in de EA-portal inschakelen

Voor het afdelingsbereik moet de optie **DA-kosten weergeven** zijn **ingeschakeld** in de EA-portal. Configureer de optie in de Azure-portal of in de EA-portal. Voor alle andere bereiken moet de optie **OA-kosten weergeven** zijn **ingeschakeld** in de EA-portal.

Een optie inschakelen in de EA-portal:

1. Meld u aan bij de EA-portal op [https://ea.azure.com](https://ea.azure.com) met een ondernemingsbeheerdersaccount.
2. Selecteer **Beheren** in het linkerdeelvenster.
3. Stel de kostenoptie in op **DA-kosten weergeven** en/of **AO-kosten weergeven** voor de Cost Management-bereiken waartoe u toegang wilt verlenen.  
    ![tabblad Inschrijving met de opties DA-kosten weergeven en AO-kosten weergeven](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Nadat de opties voor het weergeven van kosten zijn ingeschakeld, moet voor de meeste bereiken ook de machtiging voor op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) worden geconfigureerd in Azure Portal.

## <a name="enterprise-administrator-role"></a>Rol van ondernemingsbeheerder

Een ondernemingsbeheerder heeft standaard toegang tot het factureringsaccount (Enterprise Overeenkomst/inschrijving) en alle andere bereiken, die onderliggende bereiken zijn. De ondernemingsbeheerder wijst toegangsrechten voor bereiken toe aan andere gebruikers. Als best practice voor bedrijfscontinuïteit moet u altijd twee gebruikers met toegangsrechten als ondernemingsbeheerder hebben. In de volgende secties worden voorbeelden gegeven van een ondernemingsbeheerder die toegangsrechten voor bereiken aan andere gebruikers toewijst.

## <a name="assign-billing-account-scope-access"></a>Toegang tot factureringsrekeningbereik toewijzen

Voor toegang tot het factureringsrekeningbereik is de machtiging Ondernemingsbeheerder in de EA-portal vereist. De ondernemingsbeheerder kan kosten weergeven voor de volledige EA-inschrijving of meerdere inschrijvingen. Er is geen actie vereist in de Azure-portal voor het bereik van de factureringsrekening.

1. Meld u aan bij de EA-portal op [https://ea.azure.com](https://ea.azure.com) met een ondernemingsbeheerdersaccount.
2. Selecteer **Beheren** in het linkerdeelvenster.
3. Selecteer op het tabblad **Inschrijving** de inschrijving die u wilt beheren.  
    ![uw inschrijving selecteren in de EA-portal](./media/assign-access-acm-data/ea-portal.png)
4. Select **+ Beheerder toevoegen**.
5. Selecteer in het vak Beheerder toevoegen het verificatietype en typ het e-mailadres van de gebruiker.
6. Als de gebruiker alleen-lezentoegang moet hebben tot kosten- en gebruiksgegevens, selecteert u **Ja** onder **Alleen-lezen**.  Anders selecteert u **Nee**.
7. Selecteer **Toevoegen** om het account te maken.  
    ![voorbeeld van informatie die wordt weergegeven in het vak Beheerder toevoegen](./media/assign-access-acm-data/add-admin.png)

Het kan tot 30 minuten duren voordat de nieuwe gebruiker toegang heeft tot gegevens in Cost Management.

### <a name="assign-department-scope-access"></a>Toegang tot het afdelingsbereik toewijzen

Voor toegang tot het afdelingsbereik is toegang als afdelingsbeheerder (DA-kosten weergeven) in de EA-Portal vereist. De afdelingsbeheerder kan kosten- en gebruiksgegevens weergeven die zijn gekoppeld aan een of meer afdelingen. De afdelingsgegevens omvatten ook alle abonnementen die horen bij een inschrijvingsaccount dat is gekoppeld aan de afdeling. Er is geen actie vereist in de Azure-portal.

1. Meld u aan bij de EA-portal op [https://ea.azure.com](https://ea.azure.com) met een ondernemingsbeheerdersaccount.
2. Selecteer **Beheren** in het linkerdeelvenster.
3. Selecteer op het tabblad **Inschrijving** de inschrijving die u wilt beheren.
4. Selecteer het tabblad **Afdeling** en selecteer vervolgens **Beheerder toevoegen**.
5. Selecteer in het vak Afdelingsbeheerder toevoegen het verificatietype en typ vervolgens het e-mailadres van de gebruiker.
6. Als de gebruiker alleen-lezentoegang moet hebben tot kosten- en gebruiksgegevens, selecteert u **Ja** onder **Alleen-lezen**.  Anders selecteert u **Nee**.
7. Selecteer de afdelingen waaraan u afdelingsbeheerdersmachtigingen wilt verlenen.
8. Selecteer **Toevoegen** om het account te maken.  
    ![vereiste gegevens invoeren in het vak Afdelingsbeheerder toevoegen](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Toegang tot inschrijvingsaccountbereik toewijzen

Voor toegang tot het inschrijvingsaccountbereik is toegang als accounteigenaar (AO-kosten weergeven) in de EA-portal vereist. De accounteigenaar kan de kosten- en gebruiksgegevens weergeven die zijn gekoppeld aan de abonnementen die zijn gemaakt op basis van dat inschrijvingsaccount. Er is geen actie vereist in de Azure-portal.

1. Meld u aan bij de EA-portal op [https://ea.azure.com](https://ea.azure.com) met een ondernemingsbeheerdersaccount.
2. Selecteer **Beheren** in het linkerdeelvenster.
3. Selecteer op het tabblad **Inschrijving** de inschrijving die u wilt beheren.
4. Selecteer het tabblad **Account** en selecteer vervolgens **Account toevoegen**.
5. Selecteer in het vak Account toevoegen de **afdeling** waaraan u het account wilt koppelen, of laat het niet-toegewezen.
6. Selecteer het verificatietype en typ de accountnaam.
7. Typ het e-mailadres van de gebruiker en typ desgewenst de kostenplaats.
8. Selecteer **Toevoegen** om het account te maken.  
    ![de vereiste gegevens invoeren in het vak Account toevoegen voor een inschrijvingsaccount](./media/assign-access-acm-data/add-account.png)

Nadat u de bovenstaande stappen hebt voltooid, wordt het gebruikersaccount een inschrijvingsaccount in de Enterprise-portal en kunnen abonnementen worden gemaakt. De gebruiker heeft toegang tot kosten- en gebruiksgegevens van de abonnementen die hij of zij maakt.

## <a name="assign-management-group-scope-access"></a>Toegang tot beheergroepsbereik toewijzen

Voor toegangsrechten voor het weergeven van het beheergroepsbereik is ten minste de machtiging Lezer Cost Management (of Lezer) vereist. U kunt machtigingen voor een beheergroep configureren in de Azure-portal. U moet ten minste de machtiging Administrator voor gebruikerstoegang (of Eigenaar) voor de beheergroep hebben om toegang voor anderen te kunnen inschakelen. Voor Azure EA-accounts moet u ook de instelling **AO-kosten weergeven** in de EA-portal hebben ingeschakeld.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **Alle services** in de zijbalk, zoek naar _beheergroepen_ en selecteer vervolgens **beheergroepen**.
3. Selecteer de beheergroep in de hiërarchie.
4. Selecteer **Details** naast de naam van de beheergroep.
5. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster.
6. Selecteer **Toevoegen**.
7. Selecteer **Lezer Cost Management** onder **Rol**.
8. Selecteer **Azure AD-gebruiker, -groep of -toepassing** onder **Toegang toewijzen aan**.
9. Om toegang toe te wijzen, zoekt u de gebruiker en selecteert u deze.
10. Selecteer **Opslaan**.  
    ![voorbeeld van informatie in het vak Machtigingen toevoegen voor een beheergroep](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Toegang tot een abonnementsbereik toewijzen

Voor toegang tot een abonnement is ten minste de machtiging Lezer Cost Management (of Lezer) vereist. U kunt machtigingen voor een abonnement configureren in de Azure-portal. U moet ten minste de machtiging Administrator voor gebruikerstoegang (of Eigenaar) voor het abonnement hebben om toegang voor anderen te kunnen inschakelen. Voor Azure EA-accounts moet u ook de instelling **AO-kosten weergeven** in de EA-portal hebben ingeschakeld.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **Alle services** in de zijbalk, zoek naar _abonnementen_ en selecteer vervolgens **Abonnementen**.
3. Selecteer uw abonnement.
4. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster.
5. Selecteer **Toevoegen**.
6. Selecteer **Lezer Cost Management** onder **Rol**.
7. Selecteer **Azure AD-gebruiker, -groep of -toepassing** onder **Toegang toewijzen aan**.
8. Om toegang toe te wijzen, zoekt u de gebruiker en selecteert u deze.
9. Selecteer **Opslaan**.

## <a name="assign-resource-group-scope-access"></a>Toegang tot een resourcegroepsbereik toewijzen

Voor toegang tot een resourcegroep is ten minste de machtiging Lezer Cost Management (of Lezer) vereist. U kunt machtigingen voor een resourcegroep configureren in de Azure-portal. U moet ten minste de machtiging Administrator voor gebruikerstoegang (of Eigenaar) voor de resourcegroep hebben om toegang voor anderen te kunnen inschakelen. Voor Azure EA-accounts moet u ook de instelling **AO-kosten weergeven** in de EA-portal hebben ingeschakeld.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer **Alle services** in de zijbalk, zoek naar _resourcegroepen_ en selecteer vervolgens **Resourcegroepen**.
3. Selecteer uw resourcegroep.
4. Selecteer **Toegangsbeheer (IAM)** in het linkerdeelvenster.
5. Selecteer **Toevoegen**.
6. Selecteer **Lezer Cost Management** onder **Rol**.
7. Selecteer **Azure AD-gebruiker, -groep of -toepassing** onder **Toegang toewijzen aan**.
8. Om toegang toe te wijzen, zoekt u de gebruiker en selecteert u deze.
9. Selecteer **Opslaan**.

## <a name="cross-tenant-authentication-issues"></a>Problemen met verificatie tussen tenants

Momenteel biedt Azure Cost Management beperkte ondersteuning voor verificatie tussen tenants. In sommige gevallen kan het voorkomen dat de fout **Toegang geweigerd** in de kostenanalyse wordt weergegeven. Dit probleem kan optreden als u op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) configureert voor het abonnement van een andere tenant en vervolgens probeert kostengegevens weer te geven.

*U kunt dit probleem als volgt omzeilen*: Wacht een uur nadat u Azure RBAC tussen tenants hebt geconfigureerd. Probeer vervolgens kosten in kostenanalyse weer te geven of toegang tot Cost Management te verlenen aan gebruikers in beide tenants.  


## <a name="next-steps"></a>Volgende stappen

- Als u de eerste quickstart voor Cost Management nog niet hebt voltooid, leest u die op [Kosten analyseren](quick-acm-cost-analysis.md).
