---
title: Azure Enterprise-overdrachten
description: Hierin worden Azure EA-overdrachten beschreven
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: f5555662c6cb5f44be3959ec3d22d1cf1953674d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030090"
---
# <a name="azure-enterprise-transfers"></a>Azure Enterprise-overdrachten

Dit artikel biedt een overzicht van Enterprise-overdrachten.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Een ondernemingsaccount overdragen naar een nieuwe inschrijving

Bij een accountoverdracht wordt een accounteigenaar verplaatst van de ene inschrijving naar een andere. Alle verwante abonnementen onder de accounteigenaar worden verplaatst naar de doelinschrijving. Gebruik een accountoverdracht wanneer u meerdere actieve inschrijvingen hebt en alleen de geselecteerde accounteigenaren wilt verplaatsen.

Deze sectie is alleen bedoeld voor informatieve doeleinden, omdat de actie niet kan worden uitgevoerd door een ondernemingsbeheerder. Er is een ondersteuningsaanvraag nodig om een ondernemingsaccount over te brengen naar een nieuwe inschrijving.

Houd rekening met de volgende punten wanneer u een Enterprise-account overdraagt naar een nieuwe inschrijving:

- Alleen de accounts in de aanvraag worden overgedragen. Als alle accounts worden gekozen, worden ze allemaal overgedragen.
- De status van de broninschrijving blijft actief of uitgebreid. U kunt de inschrijving blijven gebruiken totdat deze verloopt.

### <a name="prerequisites"></a>Vereisten

Wanneer u een accountoverdracht aanvraagt, verstrekt u de volgende gegevens:

- Het nummer van de doelinschrijving, de accountnaam, en het e-mailadres van de accounteigenaar voor het account dat moet worden overgedragen
- Voor de broninschrijving het inschrijvingsnummer en het account dat moet worden overgedragen

Andere punten waarmee u rekening moet houden voordat u een account overdraagt:

- Goedkeuring van een EA-beheerder is vereist voor de doel- en broninschrijving
- Als een accountoverdracht niet aan uw vereisten voldoet, kunt u een inschrijvingsoverdracht overwegen.
- De accountoverdracht draagt alle services en abonnementen over die betrekking hebben op de specifieke accounts.
- Nadat de overdracht is voltooid, verschijnt het overgedragen account als inactief onder de broninschrijving en als actief onder de doelinschrijving.
- In het account wordt de einddatum getoond die correspondeert met de effectieve overdrachtsdatum op de broninschrijving en als een begindatum op de doelinschrijving.
- Elk gebruik dat met het account is uitgevoerd vóór de effectieve overgangsdatum, blijft onder de broninschrijving bestaan.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Enterprise-inschrijving overdragen naar een nieuwe inschrijving

Een inschrijvingsoverdracht wordt overwogen wanneer:

- De vooruitbetalingstermijn van een huidige inschrijving tot een einde is gekomen.
- Een inschrijving de status verlopen/uitgebreid heeft en er wordt onderhandeld over een nieuwe overeenkomst.
- U meerdere inschrijvingen hebt en alle accounts en facturering wilt samenvoegen onder één inschrijving.

Deze sectie is alleen bedoeld voor informatieve doeleinden, omdat de actie niet kan worden uitgevoerd door een ondernemingsbeheerder. Er is een ondersteuningsaanvraag nodig om een ondernemingsinschrijving over te brengen naar een nieuwe inschrijving.

Wanneer u de overdracht van een volledige Enterprise-inschrijving naar een inschrijving aanvraagt, worden de volgende acties uitgevoerd:

- Alle services, abonnementen, accounts en afdelingen van Azure, en de volledige inschrijvingsstructuur, inclusief alle EA-afdelingsbeheerders, worden overgedragen naar een nieuwe doelinschrijving.
- De inschrijvingsstatus wordt ingesteld op _Overgedragen_. De overgedragen inschrijving is alleen beschikbaar voor rapportage van het historische gebruik.
- U kunt geen rollen of abonnementen toevoegen aan een overgedragen inschrijving. De status Overgedragen voorkomt aanvullend gebruik van de inschrijving.
- Het resterende saldo van de Azure-vooruitbetaling in de overeenkomst gaat verloren, met inbegrip van toekomstige termijnen.
-    Als de inschrijving van waaruit u de overdracht uitvoert over RI-aankopen beschikt, blijven de kosten voor de aanschaf van RI in de broninschrijving. Alle RI-voordelen worden echter overgedragen zodat u deze in de nieuwe inschrijving kunt gebruiken.
-    De eenmalige aanschafkosten via de marketplace en eventuele vaste maandelijkse kosten die al bij de oude inschrijving in rekening zijn gebracht, worden niet naar de nieuwe inschrijving overgedragen. Marketplace-kosten op basis van gebruik worden wel overgedragen.

### <a name="effective-transfer-date"></a>Werkelijke overdrachtsdatum

De werkelijke overdrachtsdatum kan zijn op of na de startdatum van de doelinschrijving.

Het gebruik van de broninschrijving wordt verrekend met de Azure-vooruitbetaling of in rekening gebracht als overschrijding. Gebruik dat plaatsvindt na de werkelijke overdrachtsdatum, wordt overgedragen naar de nieuwe inschrijving en dienovereenkomstig in rekening gebracht.

### <a name="prerequisites"></a>Vereisten

Wanneer u een inschrijvingsoverdracht aanvraagt, verstrekt u de volgende gegevens:

- Voor de broninschrijving is dit het inschrijvingsnummer.
- Voor de doelinschrijving het inschrijvingsnummer waarnaar moet worden overgedragen.
- De werkelijke datum van de inschrijvingsoverdracht kan een datum op of na de begindatum van de doelinschrijving zijn. De gekozen datum is niet van invloed op het gebruik voor een overschrijdingsfactuur die al is uitgegeven.

Andere punten waarmee u rekening moet houden voordat u een inschrijving overdraagt:

- Goedkeuring van een EA-beheerder is vereist voor zowel de doel- als broninschrijving.
- Als een inschrijvingsoverdracht niet aan uw vereisten voldoet, kunt u een accountoverdracht overwegen.
- De status van de broninschrijving wordt bijgewerkt naar overgedragen, en is alleen beschikbaar voor rapportagedoeleinden over historisch gebruik.

### <a name="azure-prepayment"></a>Azure-vooruitbetaling

De Azure-vooruitbetaling is niet overdraagbaar tussen inschrijvingen. De Azure-vooruitbetalingssaldi zijn contractueel gebonden aan de inschrijving waar ze zijn besteld. De Azure-vooruitbetaling is niet overdraagbaar als onderdeel van het overdrachtsproces voor het account of de inschrijving.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Er worden geen services beïnvloed voor de overdracht van accounts en inschrijvingen

Er is geen downtime tijdens de overdracht van accounts en inschrijvingen. Indien alle vereiste gegevens worden verstrekt, kan de overdracht nog op de dag van uw aanvraag worden voltooid.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Een ondernemingsabonnement overzetten naar een abonnement met betalen per gebruik

Als u een ondernemingsabonnement wilt overzetten naar een afzonderlijk abonnement dat wordt betaald naar gebruik, moet u een nieuwe ondersteuningsaanvraag maken in de Azure Enterprise-portal. Ga naar het gedeelte **Help en ondersteuning** en selecteer **+ Nieuwe ondersteuningsaanvraag** om een ondersteuningsaanvraag te maken.

## <a name="change-azure-subscription-or-account-ownership"></a>Het Azure-abonnement of de accounteigenaar wijzigen

In Azure EA Portal kunnen abonnementen worden overdragen van de ene accounteigenaar naar de andere. Raadpleeg [Het Azure-abonnement of de accounteigenaar wijzigen](ea-portal-administration.md#change-azure-subscription-or-account-ownership) voor meer informatie.

## <a name="subscription-transfer-effects"></a>Gevolgen van een abonnementsoverdracht

Wanneer een Azure-abonnement wordt overgedragen naar een account in dezelfde Azure Active Directory-tenant, blijft de toegang behouden voor alle gebruikers, groepen en service-principals die [op Azure-rollen gebaseerde toegang (Azure RBAC)](../../role-based-access-control/overview.md) hadden om de resources te beheren.

Gebruikers met RBAC-toegang tot het abonnement weergeven:

1. Open in Azure Portal **Abonnementen**.
2. Selecteer het abonnement dat u wilt weergeven en selecteer vervolgens **Toegangsbeheer (IAM)** .
3. Selecteer **Roltoewijzingen**. De pagina met roltoewijzingen bevat een overzicht van alle gebruikers met RBAC-toegang tot het abonnement.

Als het abonnement wordt overgedragen naar een account in een andere Azure AD-tenant, verliezen alle gebruiker, groepen en service-principals met [RBAC](../../role-based-access-control/overview.md) om resources te beheren, _hun toegang_. Hoewel er geen RBAC-toegang aanwezig is, is het abonnement mogelijk toegankelijk via een beveiligingsmechanisme, inclusief:

- Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../../cloud-services/cloud-services-certs-create.md) voor meer informatie.
- Toegangssleutels voor services zoals Storage. Zie [Overzicht van Azure-opslagaccount](../../storage/common/storage-account-overview.md) voor meer informatie.
- Referenties voor externe toegang voor services zoals Azure Virtual Machines.

Als ontvangers de toegang tot hun Azure-resources willen beperken, moeten ze overwegen om de geheimen bij te werken die zijn gekoppeld aan de service. De meeste resources kunnen worden bijgewerkt door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer in het Hub-menu **Alle resources**.
3. Selecteer de resource.
4. Selecteer op de resourcepagina **Instellingen** om bestaande geheimen weer te geven en bij te werken.

## <a name="next-steps"></a>Volgende stappen

- Zie [Problemen met toegang tot Azure EA Portal oplossen](ea-portal-troubleshoot.md) als u hulp nodig hebt met het oplossen van problemen met Azure EA Portal.