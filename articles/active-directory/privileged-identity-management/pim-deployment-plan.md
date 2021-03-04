---
title: Privileged Identity Management implementeren (PIM)-Azure AD | Microsoft Docs
description: Hierin wordt beschreven hoe u de implementatie van Azure AD Privileged Identity Management (PIM) plant.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036823"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management implementeren (PIM)

Dit artikel bevat stapsgewijze instructies voor het plannen van de implementatie van Privileged Identity Management (PIM) in uw Azure Active Directory-organisatie (Azure AD). U wijst gebruikers met verhoogde bevoegdheden toe aan minder krachtige ingebouwde of aangepaste rollen waar mogelijk, en plan just-in-time-roltoewijzingen voor uw meest privilegede rollen. In dit artikel voeren we aanbevelingen voor de implementatie planning en-implementatie.

> [!TIP]
> In dit artikel ziet u items die zijn gemarkeerd als:
>
> : heavy_check_mark: **micro soft raadt** aan
>
> Dit zijn algemene aanbevelingen en u moet ze alleen implementeren als ze van toepassing zijn op uw specifieke bedrijfs behoeften.

## <a name="licensing-requirements"></a>Licentievereisten

Als u Privileged Identity Management wilt gebruiken, moet uw Directory een van de volgende betaalde of proef licenties hebben. Zie [licentie vereisten voor het gebruik van privileged Identity Management](subscription-requirements.md)voor meer informatie.

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

## <a name="how-pim-works"></a>Hoe PIM werkt

Deze sectie bevat een overzicht van de plannings doeleinden van de relevante delen van het Privileged Identity Management proces. Zie [Wat is Azure AD privileged Identity Management?](pim-configure.md) voor meer informatie.

1. Begin met het gebruik van Privileged Identity Management zodat gebruikers in aanmerking komen voor geprivilegieerde rollen.
1. Wanneer een in aanmerking komende gebruiker hun bevoorrechte rol moet gebruiken, activeren ze de rol met behulp van Privileged Identity Management.
1. De gebruiker kan in instellingen het volgende doen:

    - Multi-factor Authentication gebruiken
    - Goed keuring aanvragen voor activering
    - Geef een zakelijke reden voor de activering op

1. Nadat de gebruiker zijn rol heeft geactiveerd, hebben ze de rol machtigingen voor een ingestelde duur.
1. Beheerders kunnen een geschiedenis van alle Privileged Identity Management activiteiten in het audit logboek weer geven. Ze kunnen hun Azure AD-organisaties ook verder beveiligen en voldoen aan de naleving met behulp van Privileged Identity Management functies, zoals beoordelingen en waarschuwingen.

## <a name="roles-that-can-be-managed-by-pim"></a>Rollen die kunnen worden beheerd door PIM

**Azure AD-functies** bevinden zich allemaal in azure Active Directory (zoals globale beheerder, Exchange-beheerder en beveiligings beheerder). Meer informatie over de rollen en hun functionaliteit vindt u in de [beheerders bevoegdheden in azure Active Directory](../roles/permissions-reference.md). Zie [minst geprivilegieerde rollen per taak](../roles/delegate-by-task.md)voor hulp bij het bepalen van de rollen die aan uw beheerders worden toegewezen.

**Azure-rollen** zijn rollen die zijn gekoppeld aan een Azure-resource, resource groep,-abonnement of-beheer groep. U kunt PIM gebruiken om just-in-time toegang te bieden tot ingebouwde Azure-rollen zoals owner, gebruikers toegangs beheer en Inzender, en ook om [aangepaste rollen](../../role-based-access-control/custom-roles.md). Zie voor meer informatie over Azure-rollen [toegangs beheer op basis van rollen](../../role-based-access-control/overview.md).

Zie [rollen die u niet kunt beheren in privileged Identity Management](pim-roles.md)voor meer informatie.

## <a name="deployment-plan"></a>Implementatieplan

Voordat u Privileged Identity Management in uw organisatie implementeert, volgt u de instructies en begrijpt u de concepten in deze sectie om u te helpen een plan te maken dat is afgestemd op de privileged Identity-vereisten van uw organisatie.

### <a name="identify-your-stakeholders"></a>Identificeer uw belanghebbenden

De volgende sectie helpt u bij het identificeren van alle belanghebbenden die betrokken zijn bij het project en moeten zich afmelden, beoordelen of op de hoogte blijven. Het bevat afzonderlijke tabellen voor het implementeren van PIM voor Azure AD-rollen en PIM voor Azure-rollen. Voeg belanghebbenden toe aan de volgende tabel die geschikt is voor uw organisatie.

- SO = afmelden voor dit project
- R = dit project controleren en een invoer opgeven
- Ik = op de hoogte van dit project

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Belanghebbenden: Privileged Identity Management voor Azure AD-rollen

| Name | Rol | Bewerking |
| --- | --- | --- |
| Naam en e-mail adres | **Identiteits architect of Azure Global-beheerder**<br/>Een vertegenwoordiger van het team voor identiteits beheer die verantwoordelijk is voor het bepalen hoe deze wijziging moet worden uitgelijnd met de infra structuur voor identiteits beheer in uw organisatie. | SO/R/I |
| Naam en e-mail adres | **Eigenaar van service/regel beheer**<br/>Een vertegenwoordiger van de IT-eigen aars van een service of een groep services. Ze zijn belang rijk voor het nemen van beslissingen en het helpen bij het inrollen van Privileged Identity Management voor hun team. | SO/R/I |
| Naam en e-mail adres | **Eigenaar van beveiliging**<br/>Een vertegenwoordiger van het beveiligings team dat zich kan afmelden dat het plan voldoet aan de beveiligings vereisten van uw organisatie. | SO/R |
| Naam en e-mail adres | **IT-ondersteunings Manager/Help Desk**<br/>Een vertegenwoordiger van de IT-ondersteunings organisatie die feedback kan geven over de ondersteuning van deze wijziging vanuit een oogpunt van de Help Desk. | R/I |
| Naam en e-mail adres voor pilot gebruikers | **Gebruikers met geprivilegieerde rollen**<br/>De groep gebruikers waarvoor privileged Identity Management is geïmplementeerd. Ze moeten weten hoe ze hun rollen kunnen activeren zodra Privileged Identity Management is geïmplementeerd. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Belanghebbenden: Privileged Identity Management voor Azure-rollen

| Name | Rol | Bewerking |
| --- | --- | --- |
| Naam en e-mail adres | **Eigenaar van abonnement/resource**<br/>Een vertegenwoordiger van de IT-eigen aars van elk abonnement of resource dat u wilt implementeren Privileged Identity Management voor | SO/R/I |
| Naam en e-mail adres | **Eigenaar van beveiliging**<br/>Een vertegenwoordiger van het beveiligings team dat zich kan afmelden dat het plan voldoet aan de beveiligings vereisten van uw organisatie. | SO/R |
| Naam en e-mail adres | **IT-ondersteunings Manager/Help Desk**<br/>Een vertegenwoordiger van de IT-ondersteunings organisatie die feedback kan geven over de ondersteuning van deze wijziging vanuit een oogpunt van de Help Desk. | R/I |
| Naam en e-mail adres voor pilot gebruikers | **Azure Role-gebruikers**<br/>De groep gebruikers waarvoor privileged Identity Management is geïmplementeerd. Ze moeten weten hoe ze hun rollen kunnen activeren zodra Privileged Identity Management is geïmplementeerd. | I |

### <a name="start-using-privileged-identity-management"></a>Aan de slag met Privileged Identity Management

Als onderdeel van het plannings proces moet u Privileged Identity Management voorbereiden door het [privileged Identity Management](pim-getting-started.md) -artikel te volgen. Privileged Identity Management biedt u toegang tot enkele functies die zijn ontworpen om u te helpen bij de implementatie.

Als uw doel is om Privileged Identity Management voor Azure-resources te implementeren, moet u onze [Azure-resources ontdekken om te beheren in privileged Identity Management](pim-resource-roles-discover-resources.md) artikel. Alleen eigen aren van abonnementen en beheer groepen kunnen deze resources onder beheer brengen door Privileged Identity Management. Nadat deze is beheerd, is de PIM-functionaliteit beschikbaar voor eigen aars op alle niveaus, waaronder beheer groep, abonnement, resource groep en resource. Als u een globale beheerder bent die probeert Privileged Identity Management te implementeren voor uw Azure-resources, kunt u toegang tot het [beheer van alle Azure-abonnementen verhogen](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) om uzelf toegang te geven tot alle Azure-resources in de Directory voor detectie. Er wordt echter wel geadviseerd om goed keuring te krijgen van elk van uw abonnements eigenaren voordat u hun resources beheert met Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Principe van minimale bevoegdheid afdwingen

Het is belang rijk om ervoor te zorgen dat u het principe van minimale bevoegdheden in uw organisatie hebt afgedwongen voor zowel uw Azure AD-als uw Azure-rollen.

#### <a name="plan-least-privilege-delegation"></a>Overdracht van minste bevoegdheden plannen

Voor Azure AD-rollen is het gebruikelijk voor organisaties om de rol van globale beheerder toe te wijzen aan een aantal beheerders als de meeste beheerders slechts een of twee specifieke en minder krachtige beheerders rollen nodig hebben. Met een groot aantal globale beheerders of andere rollen met een hoge bevoegdheid is het moeilijk om uw geprivilegieerde roltoewijzingen nauw keurig te volgen.

Volg deze stappen voor het implementeren van het principe van minimale bevoegdheden voor uw Azure AD-rollen.

1. Inzicht in de granulatie van de rollen door de beschik bare [ingebouwde Azure AD-rollen](../roles/permissions-reference.md)te lezen en begrijpen. U en uw team moeten ook verwijzen naar [beheerders rollen per identiteits taak in azure AD](../roles/delegate-by-task.md), waarin de minst privileged Role voor specifieke taken wordt uitgelegd.

1. Lijst met geprivilegieerde rollen in uw organisatie. U kunt de Privileged Identity Management [detectie en inzichten (preview)](pim-security-wizard.md) gebruiken om de bloot stelling te verminderen.

    ![De pagina detectie en inzichten (preview) om de bloot stelling via geprivilegieerde rollen te verminderen](./media/pim-deployment-plan/new-preview-page.png)

1. Voor alle globale beheerders in uw organisatie, Lees waarom ze de rol nodig hebben. Verwijder ze vervolgens uit de rol globale beheerder en wijs ingebouwde rollen of aangepaste rollen toe met een lagere bevoegdheid binnen Azure Active Directory. Ter informatie heeft micro soft momenteel slechts 10 beheerders met de rol van globale beheerder. Meer informatie over de [manier waarop micro soft gebruikmaakt van privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. Voor alle andere Azure AD-rollen bekijkt u de lijst met toewijzingen, identificeert u beheerders die de rol niet meer nodig hebben en verwijdert u deze uit hun toewijzingen.

Als u de laatste twee stappen wilt automatiseren, kunt u toegangs beoordelingen gebruiken in Privileged Identity Management. Volg de stappen in [een toegangs beoordeling starten voor Azure AD-rollen in privileged Identity Management](pim-how-to-start-security-review.md), kunt u een toegangs beoordeling instellen voor elke Azure AD-rol die een of meer leden heeft.

![Een toegangs beoordelings venster maken voor Azure AD-rollen](./media/pim-deployment-plan/create-access-review.png)

De revisoren instellen op **leden (zelf)**. Alle gebruikers in de rol ontvangen een e-mail bericht waarin wordt gevraagd om te bevestigen dat ze toegang nodig hebben. Schakel ook de optie **reden voor goed keuring vereisen** in de geavanceerde instellingen in, zodat gebruikers moeten aangeven waarom ze de rol nodig hebben. Op basis van deze informatie kunt u gebruikers uit overbodige rollen verwijderen of ze delegeren aan meer gedetailleerde beheerders rollen.

Toegangs beoordelingen zijn gebaseerd op e-mail berichten om personen te melden hun toegang tot de rollen te controleren. Als u privileged accounts hebt waaraan geen e-mail berichten zijn gekoppeld, vult u het tweede veld voor e-mail op deze accounts in. Zie [proxyAddresses-kenmerk in azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)voor meer informatie.

#### <a name="plan-azure-resource-role-delegation"></a>Taak overdracht van Azure-resource plannen

Voor Azure-abonnementen en-resources kunt u een soortgelijk toegangs beoordelings proces instellen om de rollen in elk abonnement of elke resource te controleren. Het doel van dit proces is het minimaliseren van de toewijzingen van eigenaar-en gebruikers toegang aan elk abonnement of resource en voor het verwijderen van overbodige toewijzingen. Organisaties delegeren echter vaak dergelijke taken aan de eigenaar van elk abonnement of resource, omdat ze een beter inzicht hebben in de specifieke rollen (met name aangepaste rollen).

Als u de rol globale beheerder gebruikt voor het implementeren van PIM voor Azure-rollen in uw organisatie, kunt u toegang tot het [beheer van alle Azure-abonnementen verhogen](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) om toegang te krijgen tot elk abonnement. U kunt vervolgens elke eigenaar van het abonnement zoeken en ermee werken om overbodige toewijzingen te verwijderen en de roltoewijzing van de eigenaar te minimaliseren.

Gebruikers met de rol eigenaar voor een Azure-abonnement kunnen ook [toegangs Beoordelingen voor Azure-resources](pim-resource-roles-start-access-review.md) gebruiken om onnodige roltoewijzingen te controleren en te verwijderen die vergelijkbaar zijn met het proces dat eerder is beschreven voor Azure AD-rollen.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Bepalen welke roltoewijzingen moeten worden beveiligd door Privileged Identity Management

Na het opschonen van geprivilegieerde roltoewijzingen in uw organisatie, moet u beslissen welke rollen u met Privileged Identity Management wilt beveiligen.

Als een rol wordt beveiligd door Privileged Identity Management, moeten in aanmerking komende gebruikers die hieraan zijn toegewezen, de bevoegdheden verhogen die worden verleend door de rol. Het verhogings proces kan ook het verkrijgen van goed keuring mogelijk maken, met behulp van multi-factor Authentication en een reden opgeven waarom ze worden geactiveerd. Privileged Identity Management kunt ook verhogingen volgen via meldingen en de controle gebeurtenis logboeken van Privileged Identity Management en Azure AD.

U kunt kiezen welke rollen moeten worden beveiligd met Privileged Identity Management lastig zijn en voor elke organisatie verschillend zijn. Deze sectie bevat onze best practice-advies voor Azure AD en Azure-rollen.

#### <a name="azure-ad-roles"></a>Azure AD-rollen

Het is belang rijk om de beveiliging van Azure AD-rollen met de meeste machtigingen te bepalen. Op basis van gebruiks patronen voor alle Privileged Identity Management klanten, zijn de belangrijkste 10 Azure AD-rollen die worden beheerd door Privileged Identity Management:

1. Globale beheerder
1. Beveiligingsbeheerder
1. Gebruikersbeheerder
1. Exchange-beheerder
1. SharePoint-beheerder
1. Intune-beheerder
1. Beveiligingslezer
1. Servicebeheerder
1. Factureringsbeheerder
1. Skype voor Bedrijven-beheerder

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om al uw globale beheerders en beveiligings beheerders te beheren met behulp van privileged Identity Management als een eerste stap, omdat dit de gebruikers zijn die het meest schadelijk kunnen zijn wanneer ze worden aangetast.

Het is belang rijk om te bepalen welke gegevens en machtigingen het meest gevoelig zijn voor uw organisatie. Een voor beeld hiervan is het mogelijk dat sommige organisaties hun Power BI beheerdersrol of hun rol team beheerder kunnen beveiligen met behulp van Privileged Identity Management, omdat ze toegang hebben tot gegevens en kern werk stromen kunnen wijzigen.

Als er rollen zijn met gast gebruikers, zijn ze kwetsbaar voor aanvallen.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om alle rollen met gast gebruikers te beheren met privileged Identity Management om het risico te verminderen dat is gekoppeld aan gast gebruikers accounts.

Lees functies zoals de Directory Reader, Message Center Reader en Security Reader worden soms als minder belang rijk beschouwd dan andere functies omdat ze geen schrijf machtiging hebben. Er zijn echter enkele klanten die deze rollen ook beveiligen, omdat aanvallers die toegang hebben tot deze accounts gevoelige gegevens kunnen lezen, zoals persoons gegevens. Houd dit risico in overweging wanneer u wilt bepalen of u wilt dat lezers rollen in uw organisatie worden beheerd met Privileged Identity Management.

#### <a name="azure-roles"></a>Azure-rollen

Wanneer u besluit welke roltoewijzingen moeten worden beheerd met Privileged Identity Management voor Azure-resource, moet u eerst de abonnementen/resources identificeren die het belangrijkst zijn voor uw organisatie. Voor beelden van dergelijke abonnementen/resources zijn:

- Resources die de meest gevoelige gegevens hosten
- Bronnen waarvan de kern, klant gerichte toepassingen afhankelijk zijn

Als u een globale beheerder bent die problemen heeft met het bepalen welke abonnementen en resources het belangrijkst zijn, moet u contact opnemen met abonnements eigenaren in uw organisatie voor het verzamelen van een lijst met resources die worden beheerd door elk abonnement. Werk vervolgens met de eigen aars van het abonnement om de resources te groeperen op basis van het Ernst niveau, in het geval dat ze zijn aangetast (laag, gemiddeld, hoog). Volg prioriteiten voor het beheren van resources met Privileged Identity Management op basis van dit Ernst niveau.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om te werken met abonnements-en resource-eigen aren van essentiële services om privileged Identity Management werk stroom in te stellen voor alle rollen binnen gevoelige abonnementen/resources.

Privileged Identity Management voor Azure-resources ondersteunt tijdgebonden service accounts. U dient service accounts precies hetzelfde te behandelen als hoe u een normaal gebruikers account zou behandelen.

Voor abonnementen/resources die niet zo belang rijk zijn, hoeft u Privileged Identity Management voor alle rollen niet in te stellen. U moet echter wel de beheerders rollen eigenaar en gebruikers toegang beveiligen met Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om de rol van eigenaar en de beheerders rollen van alle abonnementen/resources met behulp van privileged Identity Management te beheren.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Bepalen of u een groep wilt gebruiken om rollen toe te wijzen

Of een rol moet worden toegewezen aan een groep in plaats van aan afzonderlijke gebruikers is een strategische beslissing. Bij het plannen kunt u overwegen om een rol toe te wijzen aan een groep om roltoewijzingen te beheren wanneer:

- Er zijn veel gebruikers toegewezen aan een rol
- U de toewijzing van de rol wilt delegeren

#### <a name="many-users-are-assigned-to-a-role"></a>Er zijn veel gebruikers toegewezen aan een rol

Bijhouden wie aan een rol is toegewezen en hun toewijzingen beheren op basis van het moment dat deze hand matig worden uitgevoerd. Als u een groep aan een rol wilt toewijzen, maakt u eerst [een rol](../roles/groups-create-eligible.md) die kan worden toegewezen en wijst u de groep vervolgens toe aan een functie die in aanmerking komt voor een rol. Deze actie is van kracht voor iedereen in de groep tot hetzelfde activerings proces als afzonderlijke gebruikers die in aanmerking komen voor verhoging van de rol. Groeps leden activeren hun toewijzingen voor de groep afzonderlijk met behulp van het Privileged Identity Management activerings aanvraag en goedkeurings proces. De groep is niet geactiveerd, alleen het groepslid maatschap van de gebruiker.

#### <a name="you-want-to-delegate-assigning-the-role"></a>U de toewijzing van de rol wilt delegeren

Een groeps eigenaar kan lidmaatschap voor een groep beheren. Voor functie-toewijs bare groepen van Azure AD kunnen alleen de beheerder van de bevoegde rol, de globale beheerder en de groeps eigenaren groepslid maatschap beheren. Door nieuwe leden toe te voegen aan de groep, krijgt het lid toegang tot de rollen waaraan de groep wordt toegewezen, ongeacht of de toewijzing in aanmerking komt of actief is. Gebruik groeps eigenaren om het beheer van groepslid maatschappen voor een toegewezen rol te delegeren om het vereiste aantal bevoegdheden te verminderen. Zie [een door een functie toewijs bare groep maken in azure AD](../roles/groups-create-eligible.md)voor meer informatie over het toewijzen van een eigenaar aan een groep bij het maken van de groep.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om Azure AD Role-toewijs bare groepen onder beheer te brengen door privileged Identity Management. Nadat een door een functie toewijs bare groep onder beheer van PIM wordt geplaatst, wordt dit een groep met uitgebreide toegang genoemd. Gebruik PIM om groeps eigenaren verplicht te stellen hun roltoewijzing te activeren voordat ze groepslid maatschap kunnen beheren. Zie voor meer informatie over het aanbrengen van groepen onder PIM-beheer, [privileged Access groups (preview) in privileged Identity Management](groups-discover-groups.md).

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Beslissen welke roltoewijzingen permanent of in aanmerking komen

Wanneer u de lijst met rollen die door Privileged Identity Management moeten worden beheerd, hebt vastgesteld, moet u bepalen welke gebruikers de in aanmerking komende rol en de permanent actieve rol moeten krijgen. Permanente actieve rollen zijn de normale rollen die worden toegewezen via Azure Active Directory en Azure-resources, terwijl in aanmerking komende rollen alleen kunnen worden toegewezen in Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om permanent actieve toewijzingen te hebben voor zowel Azure AD-rollen als Azure-rollen, met uitzonde ring van de aanbevolen [toegangs accounts voor afbreek glazen](../roles/security-emergency-access.md), die de permanente globale beheerdersrol moeten hebben.

Hoewel we de permanente beheerder aanraden, is het soms moeilijk voor organisaties om dit meteen te bereiken. Hier volgen enkele dingen die u moet overwegen bij het nemen van deze beslissing:

- De frequentie van uitbrei ding van bevoegdheden: als de gebruiker de privileged slechts eenmaal nodig heeft, hoeft deze niet permanent te worden toegewezen. Aan de andere kant, als de gebruiker de rol voor de dagelijkse taak nodig heeft en Privileged Identity Management de productiviteit aanzienlijk zou verlagen, kunnen ze worden overwogen voor de permanente rol.
- Cases die specifiek zijn voor uw organisatie: als de persoon die de in aanmerking komende rol heeft gekregen, afkomstig is van een ongelijk team of een leidinggevende Executive naar het punt dat het communiceren en afdwingen van het verhogings proces moeilijk maakt, kunnen ze worden overwogen voor de permanente rol.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om terugkerende toegangs beoordelingen in te stellen voor gebruikers met permanente roltoewijzingen (indien van toepassing). Meer informatie over terugkerende toegangs beoordeling vindt u in de laatste sectie van dit implementatie plan

### <a name="draft-your-privileged-identity-management-settings"></a>Uw Privileged Identity Management-instellingen ontwerpen

Voordat u uw Privileged Identity Management-oplossing implementeert, is het verstandig om uw Privileged Identity Management-instellingen te ontwerpen voor elke geprivilegieerde rol die uw organisatie gebruikt. In deze sectie vindt u enkele voor beelden van Privileged Identity Management instellingen voor bepaalde rollen (ze zijn alleen ter informatie en zijn mogelijk anders voor uw organisatie). Elk van deze instellingen wordt gedetailleerd uitgelegd met de aanbevelingen van micro soft na de tabellen.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Privileged Identity Management instellingen voor Azure AD-rollen

| Rol | MFA vereisen | Melding | Incident ticket | Goed keuring vereisen | Fiatteur | Activerings duur | Permanente beheerder |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Hoofdbeheerder | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andere globale beheerders | 1 uur | Accounts voor toegang in nood gevallen |
| Exchange-beheerder | :heavy_check_mark: | :heavy_check_mark: | BxDxH | BxDxH | Geen | 2 uur | Geen |
| Helpdeskbeheerder | BxDxH | BxDxH | :heavy_check_mark: | BxDxH | Geen | 8 uur | Geen |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Privileged Identity Management instellingen voor Azure-rollen

| Rol | MFA vereisen | Melding | Goed keuring vereisen | Fiatteur | Activerings duur | Actieve beheerder | Actieve verval datum | Verval datum in aanmerking komend |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Eigenaar van kritieke abonnementen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andere eigen aars van het abonnement | 1 uur | Geen | n.v.t. | 3 maand |
| Gebruikers toegangs beheerder van minder kritieke abonnementen | :heavy_check_mark: | :heavy_check_mark: | BxDxH | Geen | 1 uur | Geen | n.v.t. | 3 maand |
| Inzender voor virtuele machines | BxDxH | :heavy_check_mark: | BxDxH | Geen | 3 uur | Geen | n.v.t. | 6 maanden |

In de volgende tabel worden de instellingen beschreven.

| Instelling | Beschrijving |
| --- | --- |
| Rol | De naam van de rol waarvoor u de instellingen wilt definiëren. |
| MFA vereisen | Of de in aanmerking komende gebruiker MFA moet uitvoeren voordat de rol wordt geactiveerd.<br/><br/> : heavy_check_mark: **micro soft raadt** u aan MFA af te dwingen voor alle beheerders rollen, met name als de rollen gast gebruikers hebben. |
| Melding | Als deze eigenschap is ingesteld op True, wordt een e-mail melding door de beheerder van de bevoegde rol en de beveiligings beheerder in de organisatie ontvangen wanneer een in aanmerking komende gebruiker de rol activeert.<br/><br/>**Opmerking:** Sommige organisaties hebben geen e-mail adres dat is gekoppeld aan hun beheerders accounts. Als u deze e-mail meldingen wilt ontvangen, moet u een alternatief e-mail adres instellen, zodat beheerders deze e-mail berichten ontvangen. |
| Incident ticket | Hiermee wordt aangegeven of de in aanmerking komende gebruiker een incident ticket nummer moet vastleggen wanneer hun rol wordt geactiveerd. Deze instelling helpt een organisatie bij het identificeren van elke activering met een intern incident nummer om ongewenste activeringen te beperken.<br/><br/> : heavy_check_mark: **micro soft raadt** u aan om te profiteren van de ticket nummers van een incident om privileged Identity Management te koppelen aan uw interne systeem. Deze methode kan nuttig zijn voor goed keurders die context nodig hebben voor de activering. |
| Goed keuring vereisen | Hiermee wordt aangegeven of de in aanmerking komende gebruiker goed keuring moet krijgen om de rol te activeren.<br/><br/> : heavy_check_mark: **micro soft raadt** u aan om goed keuring in te stellen voor rollen met de meeste machtigingen. Op basis van gebruiks patronen van alle Privileged Identity Management klanten, globale beheerder, gebruikers beheerder, Exchange-beheerder, beveiligings beheerder en wachtwoord beheerder zijn de meest voorkomende rollen met goedkeurings instellingen. |
| Fiatteur | Als goed keuring is vereist voor het activeren van de in aanmerking komende rol, vermeldt u de personen die de aanvraag moeten goed keuren. Privileged Identity Management stelt de fiatteur standaard in op alle gebruikers die een bevoegde rol beheerder zijn, ongeacht of ze permanent of in aanmerking komen.<br/><br/>**Opmerking:** Als een gebruiker in aanmerking komt voor een Azure AD-rol en een goed keurder van de rol, kunnen ze zichzelf niet goed keuren.<br/><br/> : heavy_check_mark: **micro soft adviseert** dat u goed keurders kiest voor gebruikers die het meeste weten over de rol en de frequente gebruikers in plaats van een globale beheerder. |
| Activerings duur | De tijds duur dat een gebruiker wordt geactiveerd in de rol voordat deze verloopt. |
| Permanente beheerder | Lijst met gebruikers die een permanente beheerder voor de rol worden (nooit hoeven te activeren).<br/><br/> : heavy_check_mark: **micro soft raadt** aan dat u geen permanente beheerder hebt voor alle rollen, met uitzonde ring van globale beheerders. Meer informatie hierover vindt u in de sectie wie moet worden gemaakt en die permanent actief moeten zijn in dit plan. |
| Actieve beheerder | Voor Azure-resources is de actieve beheerder de lijst met gebruikers die nooit moeten worden geactiveerd om de rol te gebruiken. Deze lijst wordt niet aangeduid als een permanente beheerder zoals in azure AD-rollen omdat u een verloop tijd kunt instellen voor wanneer de gebruiker deze rol gaat verliezen. |
| Actieve verval datum | Actieve roltoewijzingen voor Azure-rollen verlopen na de geconfigureerde duur. U kunt kiezen uit 15 dagen, 1 maand, 3 maanden, 6 maanden, 1 jaar of permanent actief. |
| Verval datum in aanmerking komend | In aanmerking komende roltoewijzingen voor Azure-rollen verlopen na deze duur. U kunt kiezen uit 15 dagen, 1 maand, 3 maanden, 6 maanden, 1 jaar of permanent in aanmerking komen. |

## <a name="implementation-plan"></a>Implementatie plan

De kern van de juiste planning is de basis waarop u een toepassing kunt implementeren met Azure Active Directory.  Het biedt intelligente beveiliging en integratie die het onboarding vereenvoudigt tijdens het verkorten van de tijd voor geslaagde implementaties.  Met deze combi natie zorgt u ervoor dat uw toepassing is geïntegreerd met gemak en de tijd voor de eind gebruikers verkleint.

### <a name="identify-test-users"></a>Test gebruikers identificeren

Gebruik deze sectie om een set gebruikers en of groepen gebruikers te identificeren om de implementatie te valideren. Op basis van de instellingen die u hebt geselecteerd in het gedeelte planning, identificeert u de gebruikers die u wilt testen voor elke rol.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om service-eigen aren van elke Azure AD-rol de test gebruikers te maken, zodat ze vertrouwd kunnen raken met het proces en een interne advocator worden voor de implementatie.

In deze tabel identificeert u de test gebruikers die zullen controleren of de instellingen voor de functies werken.

| Rolnaam | Gebruikers testen |
| --- | --- |
| &lt;Rolnaam&gt; | &lt;Gebruikers om de rol te testen&gt; |
| &lt;Rolnaam&gt; | &lt;Gebruikers om de rol te testen&gt; |

### <a name="test-implementation"></a>Implementatie testen

Nu u de test gebruikers hebt geïdentificeerd, gebruikt u deze stap om Privileged Identity Management te configureren voor uw test gebruikers. Als uw organisatie Privileged Identity Management werk stroom wil opnemen in uw eigen interne toepassing in plaats van Privileged Identity Management in de Azure Portal te gebruiken, worden alle bewerkingen in Privileged Identity Management ook ondersteund via onze [Graph API](/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Privileged Identity Management configureren voor Azure AD-rollen

1. [Configureer de functie-instellingen van Azure AD](pim-how-to-change-default-settings.md) op basis van wat u hebt gepland.

1. Navigeer naar **Azure AD-rollen**, selecteer **rollen** en selecteer vervolgens de functie die u hebt geconfigureerd.

1. Als ze voor de groep test gebruikers al een permanente beheerder zijn, kunt u ze in aanmerking komen door ernaar te zoeken en ze te converteren van permanent naar in aanmerking komend door de drie puntjes op hun rij te selecteren. Als ze nog geen roltoewijzingen hebben, kunt u [een nieuwe in aanmerking komende toewijzing maken](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Herhaal stap 1-3 voor alle rollen die u wilt testen.

1. Zodra u de test gebruikers hebt ingesteld, moet u ze de koppeling sturen voor het activeren van [hun Azure AD-rol](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Privileged Identity Management voor Azure-rollen configureren

1. [Configureer de instellingen van de Azure-resource functie](pim-resource-roles-configure-role-settings.md) voor een rol binnen een abonnement of resource die u wilt testen.

1. Navigeer naar **Azure-resources** voor dat abonnement en selecteer **rollen**, selecteer de rol die u hebt geconfigureerd.

1. Als de groep test gebruikers al een actieve beheerder is, kunt u deze in aanmerking komen door ernaar te zoeken en [hun roltoewijzing](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)bij te werken. Als ze nog geen rol hebben, kunt u [een nieuwe rol toewijzen](pim-resource-roles-assign-roles.md#assign-a-role).

1. Herhaal stap 1-3 voor alle rollen die u wilt testen.

1. Zodra u de test gebruikers hebt ingesteld, moet u ze de koppeling sturen om [hun Azure-resource functie te activeren](pim-resource-roles-activate-your-roles.md).

Gebruik deze fase om te controleren of alle configuratie die u voor de rollen hebt ingesteld, correct werkt. Gebruik de volgende tabel om uw tests te documenteren. U moet deze fase ook gebruiken om de communicatie met de betrokken gebruikers te optimaliseren.

| Rol | Verwacht gedrag tijdens de activering | Werkelijke resultaten |
| --- | --- | --- |
| Hoofdbeheerder | (1) MFA vereisen<br/>(2) goed keuring vereisen<br/>(3) fiatteur ontvangt een melding en kan goed keuren<br/>(4) rol verloopt na vooraf ingestelde tijd |  |
| Eigenaar van abonnement *X* | (1) MFA vereisen<br/>(2) de in aanmerking komende toewijzing verloopt na de geconfigureerde tijds periode |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Privileged Identity Management met betrokken belanghebbenden communiceren

Wanneer u Privileged Identity Management implementeert, worden er extra stappen geïntroduceerd voor gebruikers van geprivilegieerde rollen. Hoewel Privileged Identity Management beveiligings problemen die zijn gekoppeld aan bevoorrechte identiteiten aanzienlijk reduceert, moet de wijziging effectief worden gecommuniceerd vóór de implementatie van de hele organisatie. Afhankelijk van het aantal betrokken beheerders, selecteren organisaties vaak een intern document, een video of een e-mail bericht over de wijziging. Deze communicatie bevat vaak de volgende informatie:

- Wat is PIM?
- Wat is het voor deel voor de organisatie?
- Die worden beïnvloed
- Wanneer moet PIM worden getotaliseerd
- Welke extra stappen zijn vereist voor gebruikers om hun rol te activeren
    - U moet koppelingen naar onze documentatie verzenden:
    - [Azure AD-rollen activeren](pim-how-to-activate-role.md)
    - [Azure-rollen activeren](pim-resource-roles-activate-your-roles.md)
- Contact gegevens of koppeling naar Help Desk voor problemen met PIM

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om de tijd in te stellen met uw Help Desk/ondersteunings team om deze via de privileged Identity Management werk stroom te door lopen (als uw organisatie een intern it-ondersteunings team heeft). Geef ze de juiste documenten en uw contact gegevens.

### <a name="move-to-production"></a>Verplaatsen naar productie

Wanneer het testen is voltooid en geslaagd, gaat u Privileged Identity Management naar productie door alle stappen in de test fasen te herhalen voor alle gebruikers van elke functie die u in uw Privileged Identity Management configuratie hebt gedefinieerd. Voor Privileged Identity Management voor Azure AD-rollen, testen en implementeren organisaties vaak Privileged Identity Management voor globale beheerders voordat ze Privileged Identity Management testen en implementeren voor andere rollen. In de tussen tijd voor Azure resource testen en implementeren organisaties doorgaans Privileged Identity Management één Azure-abonnement tegelijk.

### <a name="if-a-rollback-is-needed"></a>Als er een terugdraai actie nodig is

Als Privileged Identity Management niet naar wens werkt in de productie omgeving, kunnen de volgende terugdraai stappen u helpen om terug te keren naar een bekende goede staat voordat u Privileged Identity Management instelt:

#### <a name="azure-ad-roles"></a>Azure AD-rollen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Open **Azure AD privileged Identity Management**.
1. Selecteer **Azure AD-rollen** en selecteer vervolgens **rollen**.
1. Voor elke rol die u hebt geconfigureerd, selecteert u het weglatings teken (**...**) voor alle gebruikers met een in aanmerking komende toewijzing.
1. Selecteer de optie **permanent maken** om de roltoewijzing permanent te maken.

#### <a name="azure-roles"></a>Azure-rollen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Open **Azure AD privileged Identity Management**.
1. Selecteer **Azure-resources** en selecteer vervolgens een abonnement of resource die u wilt terugzetten.
1. Selecteer **Rollen**.
1. Voor elke rol die u hebt geconfigureerd, selecteert u het weglatings teken (**...**) voor alle gebruikers met een in aanmerking komende toewijzing.
1. Selecteer de optie **permanent maken** om de roltoewijzing permanent te maken.

## <a name="next-steps-after-deploying"></a>Volgende stappen na de implementatie

Het implementeren van Privileged Identity Management in productie is een belang rijke stap voor het beveiligen van de bevoegde identiteiten van uw organisatie. Met de implementatie van Privileged Identity Management worden extra Privileged Identity Management functies geleverd die u moet gebruiken voor beveiliging en naleving.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Privileged Identity Management waarschuwingen gebruiken om uw bevoegde toegang te beveiligen

Zie [beveiligings waarschuwingen](pim-how-to-configure-security-alerts.md#security-alerts)voor meer informatie over het gebruik van de ingebouwde waarschuwings functionaliteit van privileged Identity Management om uw organisatie te beschermen. Deze waarschuwingen omvatten: beheerders die geen geprivilegieerde rollen gebruiken, de rollen worden toegewezen buiten Privileged Identity Management, worden de rollen te vaak geactiveerd. Als u uw organisatie volledig wilt beveiligen, moet u de lijst met waarschuwingen regel matig door lopen en de problemen oplossen. U kunt uw waarschuwingen op de volgende manier weer geven en herstellen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Open **Azure AD privileged Identity Management**.
1. Selecteer **Azure AD-rollen** en selecteer vervolgens **waarschuwingen**.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** aan dat u alle waarschuwingen die zijn gemarkeerd met hoge urgentie onmiddellijk wilt afhandelen. Voor gemiddeld en lage Ernst waarschuwingen moet u op de hoogte blijven en wijzigingen aanbrengen als u denkt dat er sprake is van een beveiligings risico.

Als een van de specifieke waarschuwingen niet nuttig is of niet van toepassing is op uw organisatie, kunt u de waarschuwing altijd negeren op de pagina waarschuwingen. U kunt deze instelling altijd later op de pagina Azure AD-instellingen herstellen.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Terugkerende toegangs beoordelingen instellen om de bevoegde identiteiten van uw organisatie regel matig te controleren

Toegangs beoordelingen zijn de beste manier om gebruikers te vragen die zijn toegewezen aan geprivilegieerde rollen of specifieke revisoren, ongeacht of elke gebruiker de bevoorrechte identiteit nodig heeft. Toegangs beoordelingen zijn handig als u de kwets baarheid wilt beperken en wilt blijven voldoen aan het beleid. Zie toegangs beoordelingen van [Azure AD-rollen](pim-how-to-start-security-review.md) en [toegangs Beoordelingen voor Azure-rollen](pim-resource-roles-start-access-review.md)voor meer informatie over het starten van een toegangs beoordeling. Voor sommige organisaties is het uitvoeren van periodieke toegangs beoordeling vereist om aan de wetten en voor Schriften te blijven voldoen en voor anderen, is toegangs beoordeling de beste manier om de principal van minimale bevoegdheden in uw organisatie af te dwingen.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om per kwar taal toegangs beoordelingen in te stellen voor al uw Azure AD-en Azure-rollen.

In de meeste gevallen is de revisor voor Azure AD-rollen de gebruikers zelf, terwijl de revisor voor Azure-rollen de eigenaar van het abonnement is, waarbij de rol zich bevindt. Het is echter vaak het geval wanneer bedrijven geprivilegieerde accounts hebben die niet zijn gekoppeld aan het e-mail adres van een bepaalde persoon. In deze gevallen wordt er geen lees-en-beoordeling van de toegang gegeven.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om een secundair e-mail adres toe te voegen voor alle accounts met geprivilegieerde roltoewijzingen die niet zijn gekoppeld aan een regel matig gecontroleerd e-mail adres

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Het maximale uit uw audit logboek halen om de beveiliging en naleving te verbeteren

Het audit logboek is de plek waar u up-to-date kunt blijven en voldoen aan de voor Schriften. Privileged Identity Management wordt momenteel een geschiedenis van 30 dagen opgeslagen in het controle logboek van uw organisatie, met inbegrip van:

- Activering/deactivering van in aanmerking komende rollen
- Roltoewijzings activiteiten binnen en buiten Privileged Identity Management
- Wijzigingen in rolinstellingen
- Activiteiten voor activering van rollen met goedkeurings instellingen aanvragen/goed keuren/weigeren
- Bijwerken naar waarschuwingen

U kunt de audit Logboeken openen als u een globale beheerder of een beheerder van een bevoegde rol bent. Zie de [controle geschiedenis voor Azure AD-rollen](pim-how-to-use-audit-log.md) en de [controle geschiedenis voor Azure-rollen](azure-pim-resource-rbac.md)voor meer informatie.

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan ten minste één beheerder te laten door alle controle gebeurtenissen wekelijks te lezen en uw controle gebeurtenissen maandelijks te exporteren.

Als u uw audit gebeurtenissen automatisch wilt opslaan gedurende een langere periode, wordt het controle logboek van Privileged Identity Management automatisch gesynchroniseerd naar de [audit logboeken van Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **micro soft raadt** u aan om [Azure-logboek bewaking](../reports-monitoring/concept-activity-logs-azure-monitor.md) in te stellen voor het archiveren van controle gebeurtenissen in een Azure-opslag account voor betere beveiliging en naleving.
