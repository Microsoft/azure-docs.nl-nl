---
title: Beheer van Azure EA Portal
description: In dit artikel worden de algemene taken beschreven die een beheerder in Azure EA Portal uitvoert.
author: bandersmsft
ms.author: banders
ms.date: 11/13/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperfq1
ms.openlocfilehash: f18f1184f921aca4f2c1e33ac00dcf14c63b24eb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95893355"
---
# <a name="azure-ea-portal-administration"></a>Beheer van Azure EA Portal

In dit artikel worden de algemene taken beschreven die een beheerder in Azure EA Portal uitvoert (https://ea.azure.com). Azure EA Portal is een online beheerportal waarmee klanten de kosten van hun Azure EA-services kunnen beheren. Zie het artikel [Aan de slag met Azure EA Portal](ea-portal-get-started.md) voor inleidende informatie over Azure EA Portal.

## <a name="activate-your-enrollment"></a>Uw inschrijving activeren

Als u uw service wilt activeren, moet de oorspronkelijke ondernemingsbeheerder de [Azure Enterprise-portal](https://ea.azure.com) openen en zich aanmelden met het e-mailadres in de uitnodigingsmail.

Als u zeker weet dat u bent ingesteld als ondernemingsbeheerder, hoeft u niet te wachten tot u de activeringsmail hebt ontvangen. Ga naar de [Azure Enterprise-portal](https://ea.azure.com) en meld u aan met het e-mailadres en wachtwoord van uw werk-, school- of Microsoft-account.

Als u meerdere inschrijvingen heeft, kiest u de inschrijving die u wilt activeren. Standaard worden alleen actieve inschrijvingen weergegeven. Als u de inschrijvingsgeschiedenis wilt weergeven, wist u de optie **Actief** rechtsboven in de Azure Enterprise-portal.

De status onder **Inschrijving** is **Actief**.

![Voorbeeld van een actieve inschrijving](./media/ea-portal-administration/ea-enrollment-status.png)

Alleen bestaande Azure-ondernemingsbeheerders kunnen andere ondernemingsbeheerders maken.

### <a name="create-another-enterprise-administrator"></a>Een andere ondernemingsbeheerder maken

Ga als volgt te werk om een extra ondernemingsbeheerder toe te voegen:

1. Meld u aan bij de [Azure Enterprise-portal](https://ea.azure.com).
1. Ga naar **Beheren** > **Details van inschrijving**.
1. Selecteer rechtsboven **+ Beheerder toevoegen**.

Zorg ervoor dat u het e-mailadres en de gewenste verificatiemethode van de gebruiker weet, zoals het werk-, school- of Microsoft-account.

Als u geen ondernemingsbeheerder bent, neemt u contact op met een ondernemingsbeheerder om te vragen of u kunt worden toegevoegd aan een inschrijving. Zodra u bent toegevoegd aan een inschrijving, ontvangt u een activerings-e-mail.

Als de ondernemingsbeheerder u niet kan helpen, maakt u een [ondersteuningsaanvraag voor de Azure Enterprise-portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Geef de volgende informatie op:

- Inschrijvingsnummer
- E-mailadres dat moet worden toegevoegd en verificatietype (werk-, school- of Microsoft-account)
- E-mailgoedkeuring van een bestaande ondernemingsbeheerder
  - Als de bestaande ondernemingsbeheerder niet beschikbaar is, neemt u contact op met uw partner of softwareadviseur om te vragen of de contactgegevens kunnen worden gewijzigd via het hulpprogramma Servicecentrum Volumelicenties.

## <a name="create-an-azure-enterprise-department"></a>Een Azure Enterprise-afdeling maken

Ondernemingsbeheerders en afdelingsbeheerders gebruiken afdelingen om de zakelijke Azure-services en het gebruik per afdeling en kostenplaats te organiseren en hierover te rapporteren. De ondernemingsbeheerder kan het volgende:

- Afdelingen toevoegen of verwijderen.
- Een account aan een afdeling koppelen.
- Afdelingsbeheerders maken.
- Afdelingsbeheerders toestaan om de prijs en kosten weer te geven.

Afdelingsbeheerders kunnen nieuwe accounts toevoegen aan hun afdelingen. Ze kunnen accounts uit hun afdelingen verwijderen, maar niet uit de inschrijving.

Een afdeling toevoegen:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer in het linkerdeelvenster de optie **Beheren**.
1. Selecteer het tabblad **Afdeling** en vervolgens **+ Afdeling toevoegen**.
1. Voer de gegevens in.
   Het veld Naam van afdeling is het enige verplichte veld. De naam moet minstens drie tekens bevatten.
1. Als u klaar bent, selecteert u **Toevoegen**.

## <a name="add-a-department-administrator"></a>Een afdelingsbeheerder toevoegen

Nadat een afdeling is gemaakt, kan de ondernemingsbeheerder afdelingsbeheerders toevoegen en deze koppelen aan een afdeling. Afdelingsbeheerders kunnen de volgende acties uitvoeren voor hun afdeling:

- Andere afdelingsbeheerders maken
- Afdelingseigenschappen zoals naam en kostenplaats bekijken en bewerken
- Accounts toevoegen
- Accounts verwijderen
- Gebruiksgegevens downloaden
- Het maandelijkse gebruik en de kosten bekijken<sup>1</sup>

> <sup>1</sup> Een ondernemingsbeheerder moet deze machtigingen verlenen. Als u toestemming hebt om het maandelijkse gebruik en de maandelijkse kosten weer te geven maar u deze niet kunt zien, neemt u contact op met uw partner.

### <a name="to-add-a-department-administrator"></a>Een afdelingsbeheerder toevoegen

Als een ondernemingsbeheerder:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer in het linkerdeelvenster de optie **Beheren**.
1. Selecteer het tabblad **Afdeling** en selecteer vervolgens de betreffende afdeling.
1. Selecteer **+ Beheerder toevoegen** en voeg de vereiste informatie toe.
1. Voor alleen-lezen toegang stelt u de optie **Alleen-lezen** in op **Ja** en selecteert u vervolgens **Toevoegen**.

![Voorbeeld met het dialoogvenster Afdelingsbeheerder toevoegen](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Alleen-lezentoegang instellen

U kunt afdelingsbeheerders alleen-lezentoegang verlenen.

- Als u een nieuwe afdelingsbeheerder maakt, stelt u de optie Alleen-lezen in op **Ja**.

- Een bestaande afdelingsbeheerder bewerken:
   1. Selecteer een afdeling en selecteer vervolgens het potloodpictogram naast de **afdelingsbeheerder** die u wilt bewerken.
   1. Stel de optie Alleen-lezen in op **Ja** en selecteer **Opslaan**.

Ondernemingsbeheerders krijgen automatisch machtigingen van afdelingsbeheerders.

## <a name="add-an-account"></a>Een account toevoegen

De indeling van accounts en abonnementen bepaalt hoe ze worden beheerd en hoe ze worden weergegeven op facturen en in rapporten. Organisaties worden vaak gestructureerd op basis van bedrijfsdivisies, functionele teams en geografische locaties.

Een account toevoegen:

1. Selecteer in de Azure Enterprise-portal de optie **Beheren** in het linkernavigatievenster.
1. Selecteer het tabblad **Account**. Selecteer op de pagina **Account** de optie **+ Account toevoegen**.
1. Selecteer een afdeling of zorg ervoor dat deze niet toegewezen is en selecteer vervolgens het gewenste verificatietype.
1. Geef een beschrijvende naam op waaraan u het account in de rapportage kunt herkennen.
1. Geef het **e-mailadres van de accounteigenaar** op dat u aan het nieuwe account wilt koppelen.
1. Bevestig het e-mailadres en selecteer **Toevoegen**.

![Voorbeeld met een lijst accounts en de optie +Account toevoegen](./media/ea-portal-administration/create-ea-add-an-account.png)

Als u nog een account wilt toevoegen, selecteert u **Nog een account toevoegen** of selecteert u rechtsonder in de linkerwerkbalk de optie **Toevoegen**.

Eigendom van het account controleren:

1. Meld u aan bij de Azure Enterprise-portal.
1. Bekijk de status.

   De status wordt gewijzigd van **In behandeling** in **Begin-/einddatum**. De Begin-/einddatum is de datum waarop de gebruiker zich voor het eerst heeft aangemeld en de einddatum van de overeenkomst.
1. Zodra het pop-upbericht **Waarschuwing** wordt weergegeven, moet de accounteigenaar **Doorgaan** selecteren om het account te activeren wanneer deze zich voor de eerste keer bij de Azure Enterprise-portal aanmeldt.

## <a name="change-azure-subscription-or-account-ownership"></a>Het Azure-abonnement of de accounteigenaar wijzigen

Ondernemingsbeheerders kunnen de Azure Enterprise-portal gebruiken om het eigendom over te dragen van geselecteerde of alle abonnementen in een inschrijving.

Zodra u een eigenaarsoverdracht van het abonnement of een account hebt voltooid, wordt de eigenaar van het account door Microsoft bijgewerkt.

Voordat u een eigenaarsoverdracht uitvoert, dient u deze beleidsmaatregelen op Azure-rollen gebaseerd toegangsbeheer (Azure RBAC) te begrijpen:

- Wanneer u een eigenaarsoverdracht van het abonnement of een account uitvoert tussen twee organisatie-id's in dezelfde tenant, blijven het Azure RBAC-beleid, de bestaande servicebeheerders en co-beheerdersrollen bewaard.
- Cross-tenant-eigenaarsoverdrachten van het abonnement of een account leiden ertoe dat uw Azure RBAC-beleid en de roltoewijzingen verloren gaan.
- Beleidsregels en beheerdersrollen kunnen niet worden overgedragen naar andere mappen. Servicebeheerders worden bijgewerkt naar eigenaar van het doelaccount.
- Als u wilt voorkomen dat het Azure RBAC-beleid en de roltoewijzingen verloren gaan bij het overdragen van het abonnement tussen tenants, moet u ervoor zorgen dat het selectievakje **Verplaats abonnementen naar de Azure AD-tenant van de ontvanger** van de ontvanger **niet wordt aangevinkt**. Hiermee blijven de services, Azure-rollen en het beleid van de huidige Azure AD-tenant behouden en wordt alleen het eigendom van de facturering voor het account overgedragen.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Afbeelding waarin het selectievakje voor het verplaatsen van abonnementen naar de Azure AD-tenant niet is aangevinkt" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


Voordat u de accounteigenaar wijzigt:

1. Ga in de Azure Enterprise-portal naar het tabblad **Account** en identificeer het bronaccount. Het bronaccount moet actief zijn.
1. Identificeer het doelaccount en controleer of het actief is.

Accounteigendom overdragen voor alle abonnementen:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer in het navigatiegedeelte aan de linkerkant de optie **Beheren**.
1. Selecteer het tabblad **Account** en plaats de muisaanwijzer op een account.
1. Selecteer aan de rechterkant het pictogram voor het wijzigen van de accounteigenaar. Het pictogram lijkt op een persoon.  
    ![Afbeelding van het symbool Accounteigenaar wijzigen](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Kies het doelaccount waarnaar u wilt overdragen en selecteer dan **Volgende**.
1. Als u het eigendom van het account wilt overdragen tussen Azure AD-tenants, zet u een vinkje in het selectievakje **Verplaats abonnementen naar de Azure AD-tenant van de ontvanger**.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Afbeelding waarin het selectievakje voor het verplaatsen van abonnementen naar de Azure AD-tenant is aangevinkt" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Bevestig de overdracht en selecteer **Verzenden**.

Accounteigendom overdragen voor één abonnementen:

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer in het navigatiegedeelte aan de linkerkant de optie **Beheren**.
1. Selecteer het tabblad **Account** en plaats de muisaanwijzer op een account.
1. Selecteer aan de rechterkant het pictogram voor abonnementsoverdracht. Het pictogram lijkt op een pagina.  
    ![Afbeelding met het symbool Abonnementen overdragen](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Kies het doelaccount waarnaar u het abonnement wilt overdragen en selecteer dan **Volgende**.
1. Als u het eigendom van het abonnement wilt overdragen tussen Azure AD-tenants, zet u een vinkje in het selectievakje **Verplaats abonnementen naar de Azure AD-tenant van de ontvanger**.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Afbeelding waarin het selectievakje voor het verplaatsen van abonnementen naar de Azure AD-tenant is aangevinkt" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Bevestig de overdracht en selecteer **Verzenden**.


## <a name="associate-an-account-to-a-department"></a>Een account aan een afdeling koppelen

Ondernemingsbeheerders kunnen bestaande accounts aan afdelingen onder de inschrijving koppelen.

### <a name="to-associate-an-account-to-a-department"></a>Een account aan een afdeling koppelen

1. Meld u als ondernemingsbeheerder aan bij Azure EA Portal.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer **Afdeling**.
1. Beweeg de muisaanwijzer over de rij met het account en selecteer het potloodpictogram aan de rechterkant.
1. Selecteer de afdeling in het vervolgkeuzemenu.
1. Selecteer **Opslaan**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Een bestaand account koppelen aan uw abonnement dat wordt betaald per gebruik

Als u al over een bestaand Microsoft Azure-account in de Azure-portal beschikt, geeft u het bijbehorende school-, werk- of Microsoft-account op om dit aan uw Enterprise Agreement-inschrijving te koppelen.

### <a name="associate-an-existing-account"></a>Een bestaand account koppelen

1. Selecteer in de Azure Enterprise-portal de optie **Beheren**.
1. Selecteer het tabblad **Account**.
1. Selecteer **+Een account toevoegen**.
1. Geef het werk-, school- of Microsoft-account op dat aan het bestaande Azure-account is gekoppeld.
1. Bevestig dat het account is gekoppeld aan het bestaande Azure-account.
1. Geef een naam op die u wilt gebruiken om dit account te identificeren in de rapportfunctie.
1. Selecteer **Toevoegen**.
1. Als u nog een account wilt toevoegen, selecteert u nogmaals de optie **+Een account toevoegen** of gaat u terug naar de startpagina door de knop **Beheerder** te selecteren.
1. Als u de pagina **Account** bekijkt, wordt het zojuist toegevoegde account weergegeven met de status **In behandeling**.

### <a name="confirm-account-ownership"></a>Eigendom van het account bevestigen

1. Meld u aan met het e-mailaccount dat is gekoppeld aan het werk-, school- of Microsoft-account dat u hebt opgegeven.
1. Open de e-mailmelding met de titel _Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing_ (Uitnodiging voor het activeren van uw account in de Microsoft Azure-service van Microsoft Volume Licensing).
1. Selecteer de link **Log into the Microsoft Azure Enterprise Portal** (Aanmelden bij de Microsoft Azure Enterprise-portal) in de uitnodiging.
1. Selecteer **Aanmelden**.
1. Geef uw werk-, school- of Microsoft-account en wachtwoord op om u aan te melden en te bevestigen dat u de accounteigenaar bent.

### <a name="azure-marketplace"></a>Azure Marketplace

Hoewel de meeste abonnementen vanuit een omgeving waarin wordt betaald per gebruik, kunnen worden omgezet naar een Azure Enterprise Agreement, geldt dit niet voor Azure Marketplace-services. Als u in één weergave alle abonnementen en kosten wilt kunnen zien, is het raadzaam de Azure Marketplace-services aan de Azure Enterprise-portal toe te voegen.

1. Meld u aan bij de Azure Enterprise-portal.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Inschrijving**.
1. Bekijk het gedeelte **Details van inschrijving**.
1. Selecteer rechts naast het veld Azure Marketplace het potloodpictogram om de functie in te schakelen. Selecteer **Opslaan**.

De accounteigenaar kan nu Azure Marketplace-services aanschaffen die hij of zij voorheen als abonnementen met betalen per gebruik in eigendom had.

Nadat de nieuwe Azure Marketplace-abonnementen onder uw Azure EA-inschrijving zijn geactiveerd, moet u de Marketplace-services opzeggen die in de omgeving voor betalen per gebruik zijn gemaakt. Dit is een essentiële stap om ervoor te zorgen dat uw Azure Marketplace-abonnementen geen slechte status krijgen wanneer uw betaalmiddel voor betalen per gebruik verloopt.

### <a name="msdn"></a>MSDN

MSDN-abonnementen worden automatisch omgezet naar MSDN Dev/Test. Eventueel bestaand geldtegoed van de Azure EA-aanbieding gaat verloren.

### <a name="azure-in-open"></a>Azure in Open

Als u een Azure in Open-abonnement koppelt aan een Enterprise Agreement, raakt u ongebruikt Azure in Open-tegoed kwijt. We raden daarom aan al het tegoed van het Azure in Open-abonnement te gebruiken voordat u het account toevoegt aan uw Enterprise Agreement.  

### <a name="accounts-with-support-subscriptions"></a>Accounts met ondersteuningsabonnementen

Als uw Enterprise Agreement geen ondersteuningsabonnement heeft en u een bestaand account met ondersteuningsabonnement toevoegt aan de Azure Enterprise-portal, wordt uw MOSA-ondersteuningsabonnement niet automatisch overgezet. U moet tijdens de respijtperiode (voor het einde van de volgende maand) een nieuw ondersteuningsabonnement in Azure EA aanschaffen.

## <a name="department-spending-quotas"></a>Bestedingsquota van afdeling

EA-klanten kunnen bestedingsquota instellen of wijzigen voor elke afdeling onder een inschrijving. De hoogte van het bestedingsquotum wordt voor de huidige vooruitbetalingstermijn ingesteld. Aan het einde van de huidige vooruitbetalingstermijn wordt het bestaande bestedingsquotum naar de volgende vooruitbetalingstermijn uitgebreid, tenzij de waarden worden bijgewerkt.

De afdelingsbeheerder kan het bestedingsquotum zien, maar de hoogte ervan kan uitsluitend door de ondernemingsbeheerder worden bijgewerkt. De ondernemingsbeheerder en de afdelingsbeheerder krijgen meldingen wanneer 50%, 75%, 90% en 100% van een quotum is bereikt.

### <a name="enterprise-administrator-to-set-the-quota"></a>Een ondernemingsbeheerder kan het quotum als volgt instellen:

 1. Open Azure EA Portal.
 1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
 1. Selecteer het tabblad **Afdeling**.
 1. Selecteer de afdeling.
 1. Selecteer in de sectie Afdelingsgegevens het potloodpictogram of selecteer het pictogram **+ Afdeling toevoegen** om een bestedingsquotum aan een nieuwe afdeling toe te voegen.
 1. Voer onder Afdelingsgegevens in het vak Bestedingsquotum $ de hoogte van het bestedingsquotum in (in de valuta van de inschrijving). Deze waarde moet hoger zijn dan 0.
    - Op dit punt kunt u ook de Afdelingsnaam en de Kostenplaats bewerken.
 1. Selecteer **Opslaan**.

Het bestedingsquotum voor de afdeling is nu zichtbaar in de weergave Afdelingslijst op het tabblad Afdeling. Aan het einde van de huidige vooruitbetaling blijven de bestedingsquota voor de volgende vooruitbetalingstermijn door Azure EA Portal gehandhaafd.

De hoogte van het bestedingsquotum is onafhankelijk van de huidige Azure-vooruitbetaling, en de hoogte van het quotum en meldingen zijn alleen van toepassing op gebruik van interne producten. Het bestedingsquotum voor de afdeling is alleen bedoeld ter informatie en kan niet worden gebruikt om bestedingslimieten af te dwingen.

### <a name="department-administrator-to-view-the-quota"></a>De afdelingsbeheerder kan het quotum als volgt bekijken:

1. Open Azure EA Portal.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Afdeling** en bekijk de weergave Afdelingslijst met daarin de bestedingsquota.

Als u een indirecte klant bent, moeten kostenfuncties door uw kanaalpartner zijn ingesteld.

## <a name="enterprise-user-roles"></a>Enterprise-gebruikersrollen

Met Azure EA Portal kunt u de kosten en het gebruik van Azure EA beheren. Er zijn drie hoofdrollen in Azure EA Portal:

- EA-beheerder
- Afdelingsbeheerder
- Accounteigenaar

Elke rol heeft een ander toegangs- en autoriteitsniveau.

Zie [Enterprise-gebruikersrollen](/azure/cost-management-billing/manage/understand-ea-roles#enterprise-user-roles) voor meer informatie over rollen.

## <a name="add-an-azure-ea-account"></a>Een Azure EA-account toevoegen

Het Azure EA-account is een organisatie-eenheid in de Azure EA-portal. Dit account wordt gebruikt om abonnementen te beheren en rapporten te maken. Voor toegang tot en het gebruik van Azure-services moet u een account maken of laten maken.

Zie [Een account toevoegen](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-administration#add-an-account) voor meer informatie over Azure-accounts.

## <a name="enterprise-devtest-offer"></a>Aanbieding voor Enterprise Dev/Test

Als Azure-ondernemingsbeheerder kunt u accounteigenaren in uw organisatie de mogelijkheid geven om abonnementen te maken op basis van de EA Dev/Test-aanbieding. Als u dit wilt doen, selecteert u het vak Dev/Test voor de accounteigenaar in de Azure EA-portal.

Zodra u het selectievakje Dev/Test hebt ingeschakeld, brengt u de accounteigenaar hiervan op de hoogte zodat hij of zij de EA Dev/Test-abonnementen kan instellen die voor zijn of haar teams van Dev/Test-abonnees benodigd zijn.

Hierdoor kunnen actieve Visual Studio-abonnees de ontwikkeling uitvoeren en de werklast op Azure met speciale Dev/Test-snelheden testen. Het biedt toegang tot de volledige galerie Dev/Test-installatiekopieën, waaronder Windows 8.1 en Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>U kunt de Enterprise Dev/Test-aanbieding als volgt instellen:

1. Meld u aan als de ondernemingsbeheerder.
1. Selecteer **Beheren** in het navigatievenster aan de linkerkant.
1. Selecteer het tabblad **Account**.
1. Selecteer de rij voor het account waarvoor u toegang tot Dev/Test wilt inschakelen.
1. Selecteer rechts naast de rij het potloodpictogram.
1. Schakel het selectievakje Dev/Test in.
1. Selecteer **Opslaan**.

Wanneer een gebruiker via Azure EA Portal als accounteigenaar wordt toegevoegd, worden alle Azure-abonnementen die aan de accounteigenaar zijn gekoppeld en zijn gebaseerd op ofwel de Dev/Test-aanbieding met betalen per gebruik of de aanbiedingen voor maandelijks tegoed voor Visual Studio-abonnees omgezet naar de EA Dev/Test-aanbieding. Abonnementen op basis van andere typen, zoals betalen per gebruik, die aan de accounteigenaar zijn gekoppeld, worden omgezet naar Microsoft Azure Enterprise-aanbiedingen.

De Dev/Test-aanbieding is op dit moment niet van toepassing op Azure Gov-klanten.

## <a name="create-a-subscription"></a>Een abonnement maken

Accounteigenaren kunnen abonnementen weergeven en beheren. U kunt abonnementen gebruiken om teams in uw organisatie toegang te geven tot ontwikkelomgevingen en projecten. Bijvoorbeeld: test, productie, ontwikkeling en fasering.

Wanneer u verschillende abonnementen voor elke toepassingsomgeving maakt, draagt u bij aan de beveiliging van elke omgeving.

- U kunt ook een ander servicebeheerdersaccount toewijzen voor elk abonnement.
- U kunt abonnementen aan elk gewenst aantal services koppelen.
- De accounteigenaar maakt abonnementen en wijst een servicebeheerdersaccount toe aan elk abonnement in zijn account.

### <a name="add-a-subscription"></a>Een abonnement toevoegen

Gebruik de volgende informatie om een abonnement toe te voegen.

De eerste keer dat u een abonnement aan uw account toevoegt, wordt u gevraagd de Microsoft Online Subscription Overeenkomst (MOSA) en een tariefplan te accepteren. Hoewel ze niet van toepassing zijn op Enterprise Agreement-klanten, zijn de MOSA en het tariefplan vereist om uw abonnement te maken. Uw aangepaste Microsoft Azure Enterprise Agreement-inschrijving vervangt de bovenstaande items en uw contractuele relatie blijft ongewijzigd. Schakel desgevraagd het selectievakje in om de voorwaarden te accepteren.

Als u een abonnement maakt, krijgt dit standaard de naam _Microsoft Azure Enterprise_. U kunt de naam wijzigen om het abonnement te onderscheiden van andere abonnementen in de inschrijving en zodat het kan worden herkend in rapporten op ondernemingsniveau.

Een abonnement toevoegen:

1. Meld u in de Azure Enterprise-portal aan bij het account.
1. Selecteer het tabblad **Beheerder** en selecteer vervolgens bovenaan de pagina **Abonnement**.
1. Controleer of u bent aangemeld als de eigenaar van het account.
1. Selecteer **+Abonnement toevoegen** en vervolgens **Aanschaffen**.

   De eerste keer dat u een abonnement aan een account toevoegt, moet u uw contactgegevens opgeven. Als u extra abonnementen toevoegt, worden uw contactgegevens voor u toegevoegd.

1. Selecteer **Abonnementen** en selecteer vervolgens het abonnement dat u hebt gemaakt.
1. Selecteer **Abonnementsgegevens bewerken**.
1. Bewerk de velden **Abonnementsnaam** en **Servicebeheerder** en selecteer vervolgens het vinkje.

   De abonnementsnaam wordt weergegeven op de rapporten. De naam komt overeen met het project dat is gekoppeld aan het abonnement in de ontwikkelingsportal.

Het kan tot 24 uur duren voordat nieuwe abonnementen worden weergegeven in de lijst abonnementen. Zodra u een abonnement hebt gemaakt, kunt u het volgende:

- [Abonnementsgegevens bewerken](https://account.azure.com/Subscriptions)
- [Abonnementsservices beheren](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>Abonnement verwijderen

Als u een abonnement waarvoor u de accounteigenaar bent, wilt verwijderen, doet u het volgende:

1. Meld u aan bij Azure Portal met de referenties die bij uw account horen.
1. Selecteer **Abonnementen** op het Hub-menu.
1. Ga naar het tabblad Abonnementen in de linkerbovenhoek van de pagina, selecteer het abonnement dat u wilt opzeggen en selecteer **Abonnement opzeggen** om het tabblad Opzeggen te openen.
1. Voer de abonnementsnaam in en kies een reden voor het opzeggen. Selecteer daarna de knop **Abonnement opzeggen**.

Alleen accountbeheerders kunnen abonnementen opzeggen.

Zie voor meer informatie [Wat gebeurt er nadat ik mijn abonnement heb opgezegd?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)

## <a name="delete-an-account"></a>Een account verwijderen

De verwijdering van een account kan alleen worden voltooid voor actieve accounts zonder actieve abonnementen.

1. Selecteer in Enterprise-portal in het linkernavigatievenster de optie **Beheren**.
1. Selecteer het tabblad **Account**.
1. Ga naar de tabel Accounts en selecteer het account dat u wilt verwijderen.
1. Selecteer het X-pictogram rechts naast de rij Account.
1. Zodra het account geen actieve abonnementen meer heeft, selecteert u **Ja** onder de rij Account om te bevestigen dat u het account wilt verwijderen.

## <a name="update-notification-settings"></a>Instellingen voor meldingen bijwerken

Ondernemingsbeheerders worden automatisch ingeschreven voor het ontvangen van gebruiksmeldingen die aan hun inschrijving zijn gekoppeld. Iedere ondernemingsbeheerder kan het interval voor de afzonderlijke meldingen wijzigen of deze volledig uitschakelen.

Contactpersonen voor meldingen worden in Azure EA Portal weergegeven in het gedeelte **Contactpersoon voor meldingen**. Door uw contactpersonen voor meldingen te beheren, zorgt u ervoor dat de juiste personen in uw organisatie Azure EA-meldingen ontvangen.

De huidige instellingen voor meldingen weergeven:

1. Ga in Azure EA Portal naar **Beheren** > **Contactpersoon voor meldingen**.
2. E-mailadres: het e-mailadres dat is gekoppeld aan het Microsoft-, werk- of schoolaccount van de ondernemingsbeheerder waarop de meldingen worden ontvangen.
3. Meldingsfrequentie niet-gefactureerd saldo: de ingestelde frequentie waarmee de afzonderlijke ondernemingsbeheerders meldingen ontvangen.

Een contactpersoon toevoegen:

1. Selecteer **+Contactpersoon toevoegen**.
2. Voer het e-mailadres in en bevestig dit.
3. Selecteer **Opslaan**.

De nieuwe contactpersoon voor meldingen wordt weergegeven in het gedeelte **Contactpersoon voor meldingen**. Als u de meldingsfrequentie wilt wijzigen, selecteert u de contactpersoon voor meldingen en selecteert u rechts van de geselecteerde rij het potloodpictogram. Stel de frequentie in **dagelijks**, **wekelijks**, **maandelijks** of **geen**.

U kunt de _naderende einddatum van de dekkingsperiode_ onderdrukken en de meldingen over de levenscyclus _uitschakelen en de inrichting van de datum ongedaan maken_. Als u de meldingen over de levenscyclus uitschakelt, worden meldingen over de dekkingsperiode en de einddatum van de overeenkomst onderdrukt.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-aanbieding

De Azure Sponsorship-aanbieding is een beperkt gesponsord Microsoft Azure-account. De aanbieding is beschikbaar via een e-mailuitnodiging en alleen voor een beperkt aantal, door Microsoft geselecteerde klanten. Als u recht hebt op de aanbieding voor Microsoft Azure Sponsorship, ontvangt u een e-mailuitnodiging voor uw account-id.

Maak een [ondersteuningsaanvraag voor activering van sponsoring](https://aka.ms/azrsponsorship) voor meer informatie.

## <a name="conversion-to-work-or-school-account-authentication"></a>Verificatie van de omzetting naar een werk- of schoolaccount

Azure Enterprise-gebruikers kunnen een Microsoft-account (MSA of Live ID) omzetten naar het verificatietype van een werk- of schoolaccount (waarvoor Azure Active Directory wordt gebruikt).

U gaat als volgt aan de slag:

1. Voeg het werk- of schoolaccount toe aan Azure EA Portal bij de benodigde rol(len).
1. Indien er foutmeldingen worden weergegeven, is het account mogelijk niet geldig in de Active Directory.  Azure maakt gebruik van User Principal Name (UPN). Dit is niet altijd hetzelfde als het e-mailadres.
1. Voer een verificatie bij Azure EA Portal uit met het werk- of schoolaccount.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>U kunt abonnementen van Microsoft-accounts als volgt omzetten naar werk- of schoolaccounts:

1. Meld u aan bij de beheerportal met het Microsoft-account dat eigenaar is van de abonnementen.
1. Voer een overdracht van het accounteigendom uit om naar het nieuwe account over te stappen.
1. Als het goed is, bevat het Microsoft-account nu geen actieve abonnementen meer en kan het account dus worden verwijderd.
1. Verwijderde accounts blijven voor historische factureringsredenen in de portal zichtbaar met de status Inactief.  U kunt deze accounts met behulp van een filter verbergen door een selectievakje in te schakelen zodat alleen actieve accounts worden weergegeven.

## <a name="account-subscription-ownership-faq"></a>Veelgestelde vragen over het eigendom van accountabonnementen

In dit document worden veelgestelde vragen beantwoord met betrekking tot het eigendom van accountabonnementen.

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Kan ik mijn bestaande Azure-account aan een Azure EA-inschrijving koppelen?

Ja. Alle Azure-abonnementen waarvan u de accounteigenaar bent, worden naar uw Enterprise Agreement omgezet. Hieronder vallen alle abonnementen die maandelijks tegoed gebruiken, zoals Visual Studio, AzurePass, MPN, BizSpark en meer. Het maandelijkse tegoed gaat verloren als u deze abonnementen overzet.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Hoeveel Azure-accounteigenaren mogen er per abonnement worden gebruikt?

Er is per abonnement maar één accounteigenaar toegestaan.  U kunt aanvullende rollen toevoegen met de opties Op rollen gebaseerde toegang of (Toegangsbeheer (IAM)) op het tabblad Abonnement in de linkerbovenhoek van de pagina op de [Azure-portal](https://portal.azure.com).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Is het mogelijk om het eigendom van een abonnement over te dragen aan een ander account?

Ja, u kunt het eigendom van een abonnement overdragen aan een ander account. Als aan account A bijvoorbeeld drie abonnementen zijn gekoppeld, kan de ondernemingsbeheerder één abonnement naar account B, één naar account C en één naar account D overdragen, of alle abonnementen naar account E overdragen.

Ga als volgt te werk om abonnementen over te dragen:

1. Selecteer in de Azure Enterprise-portal **Beheren** > **Account**.
1. Plaats de muisaanwijzer helemaal rechts op **Account** om de opties voor **eigendomsoverdracht** (pictogram van persoon) en **abonnementsoverdracht** (pictogram van lijst) weer te geven. Deze opties zijn alleen zichtbaar voor actieve accounts.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Kan een Azure-accounteigenaar worden vermeld onder meer dan één afdeling?

Nee, een accounteigenaar kan maar aan één afdeling worden gekoppeld. We hanteren dit beleid om ervoor te zorgen dat de kosten en uitgaven die aan de afdeling waarmee ze onder de EA-inschrijving in de Azure EA-portal zijn afgestemd, nauwkeurig worden bijgehouden en verdeeld.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Kan een Azure-accounteigenaar als een beveiligingsgroep worden vermeld?

Nee, de eigenaar van een abonnement moet een uniek Microsoft-account (MSA) of een unieke Azure Active Directory-verificatie (AAD) zijn. Als u opvolging binnen uw organisatie wilt inzetten, kunt u generieke accounts maken en Azure AD gebruiken om toegang tot abonnementen te beheren.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Kan een individuele gebruiker de eigenaar van meerdere abonnementen zijn?

Een Azure-accounteigenaar kan een onbeperkt aantal abonnementen maken en beheren.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Hoe krijg ik toegang tot alle abonnementen van mijn organisatie en hoe kan ik ze weergeven?

Tegenwoordig wordt dit vanuit een beleid geregeld. Dit houdt in dat u voor elk abonnement dat wordt gemaakt, moet vereisen dat uw account aan een abonnementsrol wordt toegevoegd met behulp van op rollen gebaseerde toegang.

### <a name="where-do-i-go-to-create-a-subscription"></a>Waar kan ik een abonnement maken?

Voordat u een abonnement voor een Enterprise Azure-aanbieding (EA) kunt maken, moet uw account door de beheerder van uw EA-inschrijving in de Azure EA-portal aan de rol van accounteigenaar worden toegevoegd. Vervolgens moet u zich bij Azure de EA-portal aanmelden om recht te krijgen op het maken van abonnementen van het EA-aanbiedingstype. U wordt aangeraden om uw eerste EA-abonnement te maken via de koppeling '+ Abonnement toevoegen', op het tabblad Abonnement in de EA-portal.  Zodra uw account echter over de juiste machtigingen beschikt, is het wellicht eenvoudiger om abonnementen te maken op het tabblad Abonnement in de linkerbovenhoek van de pagina op portal.azure.com. Hier kunt u in één stap zowel uw abonnement maken als de naam daarvan wijzigen.

### <a name="who-can-create-a-subscription"></a>Wie kan een abonnement maken?

Als u een abonnement van het Enterprise Azure-aanbiedingstype wilt maken, moet u zijn gemachtigd in de rol van accounteigenaar op [EA Portal](https://ea.azure.com).

## <a name="azure-ea-term-glossary"></a>Woordenlijst met Azure EA-terminologie

- **Account**: Een organisatie-eenheid in de Azure Enterprise-portal. Accounts worden gebruikt om abonnementen te beheren en rapporten te maken.
- **Accounteigenaar**: De persoon die abonnementen en servicebeheerders in Azure beheert. Ze kunnen gebruiksgegevens van dit account en de bijbehorende abonnementen bekijken.
- **Aanpassingsabonnement**: Een abonnement voor één jaar of een uniform abonnement onder de aangepaste inschrijving.
- **Vooruitbetaling**: Vooruitbetaling van een jaarlijks bedrag voor Azure-services, tegen een gereduceerd vooruitbetalingstarief voor het gebruik ten opzichte van deze vooruitbetaling.
- **Afdelingsbeheerder**: De persoon die afdelingen beheert, nieuwe accounts en accounteigenaren maakt, gebruiksgegevens voor de beheerde afdelingen bekijkt en kosten kan bekijken indien deze machtiging is verleend.
- **Inschrijvingsnummer**: Een unieke id die door Microsoft wordt verleend om de specifieke inschrijving te identificeren die aan een Enterprise Agreement is gekoppeld.
- **Zakelijke beheerder**: De persoon die afdelingen, afdelingseigenaren, accounts en accounteigenaren in Azure beheert. Ze beschikken over de mogelijkheid om zakelijk beheerders te beheren en gebruiksgegevens, gefactureerde hoeveelheden en niet-gefactureerde kosten te bekijken voor alle accounts en abonnementen die aan de Enterprise-inschrijving zijn gekoppeld.
- **Enterprise Agreement**: Een Microsoft-licentieovereenkomst voor klanten met gecentraliseerde aankoopmogelijkheden die hun gehele organisatie willen standaardiseren naar Microsoft-technologie en een informatietechnologie-infrastructuur handhaven op een standaard van Microsoft-software.
- **Enterprise Agreement-inschrijving**: Een inschrijving in het Enterprise Agreement-programma, zodat er gebruik kan worden gemaakt van grote volumes Microsoft-producten tegen een gereduceerd tarief.
- **Microsoft-account**: Een op het web gebaseerde service waarmee gebruikers op deelnemende sites kunnen worden geverifieerd met één set aanmeldingsgegevens.
- **Aanpassing van de Microsoft Azure Enterprise-inschrijving (aanpassing van inschrijving)** : Een aanpassing die is ondertekend door een onderneming, die deze toegang geeft tot Azure als onderdeel van de Enterprise-inschrijving.
- **Azure Enterprise-portal**: De portal die door onze Enterprise-klanten wordt gebruikt voor het beheren van hun Azure-accounts en hun bijbehorende abonnementen.
- **Hoeveelheid verbruikte resources**: De verbruikte hoeveelheid van een afzonderlijke Azure-service in één maand.
- **Servicebeheerder**: De persoon die toegang heeft tot abonnementen en ontwikkelingsprojecten in de Azure Enterprise-portal.
- **Abonnement**: Staat voor een abonnement op de Azure Enterprise-portal en fungeert als container voor Azure-services die door dezelfde servicebeheerder worden beheerd.
- **Werk- of schoolaccount**: Voor organisaties die Azure Active Directory met federatie hebben ingesteld voor de cloud en waarbij alle accounts zich in één tenant bevinden.

### <a name="enrollment-statuses"></a>Statussen van inschrijving

- **Nieuw**: Deze status wordt toegewezen aan een enrollment die binnen 24 uur is gemaakt en wordt binnen 24 uur worden bijgewerkt naar de status In behandeling.
- **In behandeling**: De inschrijvingsbeheerder moet zich aanmelden bij de Azure Enterprise-portal. Zodra de beheerder zich heeft aangemeld, krijgt de inschrijving de status Actief.
- **Actief**: De inschrijving is actief en de gebruiker kan accounts en abonnementen maken in de Azure Enterprise-portal. De inschrijving blijft actief tot de einddatum van de Enterprise Agreement.
- **Onbeperkte verlengde termijn**: Een onbeperkte verlengde termijn wordt toegekend nadat de einddatum van de Enterprise Agreement is verstreken. Hiermee kunnen Azure EA-klanten die zich voor de verlengde termijn hebben aangemeld aan het einde van hun Enterprise Agreement onbeperkt gebruik blijven maken van Azure-services.

   Voordat de einddatum van de Enterprise Agreement is bereikt voor de Azure EA-inschrijving, moet de inschrijvingsbeheerder een van de volgende acties ondernemen:

  - De inschrijving vernieuwen door een extra Azure-vooruitbetaling toe te voegen.
  - Alles overdragen naar een nieuwe inschrijving.
  - Migreren naar het Microsoft Online Subscription Program (MOSP).
  - Het uitschakelen van alle services die aan de inschrijving zijn gekoppeld, bevestigen.
- **Verlopen**: De Azure EA-klant heeft zich uitgeschreven voor de verlengde termijn en de Azure EA-inschrijving heeft de einddatum voor de Enterprise Agreement bereikt. De inschrijving verloopt en alle bijbehorende services worden uitgeschakeld.
- **Overgedragen**: Inschrijvingen waarvan de bijbehorende accounts en services zijn overgedragen naar een nieuwe inschrijving, worden weergegeven met de status Overgedragen.
  >[!NOTE]
  > Inschrijvingen worden niet automatisch overgedragen als er bij het verlengen een nieuw inschrijvingsnummer wordt gegenereerd. U moet het voorgaande inschrijvingsnummer opnemen in de verlengingsaanvraag om een automatische overdracht mogelijk te maken.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u geld kunt besparen met [VM-reserveringen](ea-portal-vm-reservations.md).
- Zie [Problemen met toegang tot Azure EA Portal](ea-portal-troubleshoot.md) als u hulp nodig hebt met het oplossen van problemen Azure EA Portal.
