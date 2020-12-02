---
title: Klanten-lockbox voor Microsoft Azure
description: Technisch overzicht van Klanten-lockbox voor Microsoft Azure, waarmee u de toegang van cloud providers kunt controleren wanneer micro soft toegang moet hebben tot klant gegevens.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 09/15/2020
ms.openlocfilehash: 01232aa101e2964354acfbeb6cea341a0da33ca6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489885"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Klanten-lockbox voor Microsoft Azure

> [!NOTE]
> Als u deze functie wilt gebruiken, moet uw organisatie een [ondersteunings abonnement voor Azure](https://azure.microsoft.com/support/plans/) hebben met een mini maal **ontwikkel** niveau.

Klanten-lockbox voor Microsoft Azure biedt klanten een interface om aanvragen voor toegang tot klantgegevens te controleren en goed te keuren of af te wijzen. Dit wordt gebruikt in gevallen waarin een Microsoft-engineer toegang heeft tot klant gegevens tijdens een ondersteuningsaanvraag.

In dit artikel wordt beschreven hoe Klanten-lockbox aanvragen worden gestart, bijgehouden en opgeslagen voor latere beoordelingen en controles.

Klanten-lockbox is nu algemeen beschikbaar en is momenteel ingeschakeld voor toegang tot virtuele machines met extern bureau blad.

## <a name="supported-services-and-scenarios-in-preview"></a>Ondersteunde services en scenario's in Preview

De volgende services zijn nu beschikbaar als preview-versie voor Klanten-lockbox:

- API Management
- Azure App Service
- Cognitive Services
- Container Registry
- Azure Database for MySQL
- Azure Databricks
- Azure Data Box
- Azure Data Explorer
- Azure Data Factory
- Azure Database for PostgreSQL
- Azure Functions
- HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Azure Storage
- Azure SQL Database
- Transfers voor Azure-abonnementen
- Azure Synapse Analytics
- Virtuele machines (bestrijken nu ook de toegang tot geheugendumps en beheerde schijven)

Als u Klanten-lockbox wilt inschakelen voor deze preview-aanbiedingen voor uw organisatie, kunt u zich registreren voor [klanten-lockbox voor de open bare preview van Azure](https://aka.ms/customerlockbox/insiderprogram).

## <a name="supported-services-and-scenarios-in-general-availability"></a>Ondersteunde services en scenario's in algemene Beschik baarheid

De volgende services en scenario's zijn momenteel in algemene Beschik baarheid voor Klanten-lockbox.

### <a name="remote-desktop-access-to-virtual-machines"></a>Externe bureaubladtoegang naar virtuele machines

Klanten-lockbox is nu algemeen beschikbaar en is momenteel ingeschakeld voor toegang tot virtuele machines met extern bureaublad. De volgende workloads worden ondersteund:
- Platform as a Service (PaaS) - Azure Cloud Services (webrol en werkrollen)
- Infrastructure as a Service (IaaS) - Windows en Linux (alleen Azure Resource Manager)
- Schaalset voor virtuele machines - Windows en Linux

> [!NOTE]
> Klassieke IaaS-instanties worden niet ondersteund door Klanten-lockbox. Als u werk belastingen hebt die worden uitgevoerd op klassieke IaaS-instanties, raden we u aan om ze te migreren van het klassieke naar het Resource Manager-implementatie model. Zie [Door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../../virtual-machines/migration-classic-resource-manager-overview.md)voor instructies.

#### <a name="detailed-audit-logs"></a>Gedetailleerde audit-logboeken

Voor scenario's waarbij toegang tot extern bureaublad is vereist, kunt u Windows-gebeurtenislogboeken gebruiken om de acties te bekijken die door de Microsoft-engineer worden uitgevoerd. Overweeg het gebruik van Azure Security Center om uw gebeurtenislogboeken te verzamelen en de gegevens naar uw werkruimte te kopiëren voor analyse. Ga voor meer informatie naar [Gegevensverzameling in het Azure Security Center](../../security-center/security-center-enable-data-collection.md).

## <a name="workflow"></a>Werkstroom

De volgende stappen beschrijven een typische werk stroom voor een Klanten-lockbox aanvraag.

1. Iemand bij een organisatie heeft een probleem met de Azure-workload.

2. Nadat deze persoon het probleem heeft opgelost, maar niet kan oplossen, wordt er een ondersteunings ticket van de [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)geopend. Het ticket wordt toegewezen aan een mede werker van de klant ondersteuning van Azure.

3. Een ondersteunings technicus van Azure controleert de service aanvraag en bepaalt de volgende stappen om het probleem op te lossen.

4. Als de ondersteunings technicus het probleem niet kan oplossen met behulp van standaard hulpprogramma's en telemetrie, is de volgende stap het aanvragen van verhoogde machtigingen met behulp van een just-in-time-toegangs service (JIT). Deze aanvraag kan afkomstig zijn van de oorspronkelijke ondersteunings technicus. Het kan ook van een andere Engineer zijn, omdat het probleem wordt geëscaleerd naar het Azure DevOps-team.

5. Nadat de toegangs aanvraag is ingediend door de Azure-Engineer, evalueert just-in-Time-service de aanvraag, waarbij rekening wordt gehouden met de volgende factoren:
    - Het bereik van de resource
    - Of de aanvrager een geïsoleerde identiteit is of gebruikmaakt van multi-factor Authentication
    - Machtigings niveaus

    Op basis van de JIT-regel kan deze aanvraag ook een goed keuring bevatten van interne micro soft-goed keurders. De goed keurder kan bijvoorbeeld de lead voor klant ondersteuning of de DevOps Manager zijn.

6. Wanneer de aanvraag directe toegang tot klant gegevens vereist, wordt een Klanten-lockbox aanvraag gestart. Bijvoorbeeld: extern bureau blad heeft toegang tot de virtuele machine van een klant.

    De aanvraag bevindt zich nu in de status van de **klant** en wacht op goed keuring van de klant alvorens toegang te verlenen.

7. De gebruiker die de [rol van eigenaar](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) van het Azure-abonnement heeft, ontvangt een E-mail van micro soft, om hen op de hoogte te stellen van de toegangs aanvraag in behandeling. Voor Klanten-lockbox aanvragen is deze persoon de aangewezen fiatteur.

    Voor beeld-e-mail:

    ![Azure Klanten-lockbox-e-mail melding](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. De e-mail melding bevat een koppeling naar de Blade **klanten-lockbox** in de Azure Portal. Met deze koppeling meldt de aangewezen goed keurder zich aan bij de Azure Portal om alle in behandeling zijnde aanvragen weer te geven die hun organisatie voor Klanten-lockbox heeft:

    ![Azure Klanten-lockbox-landings pagina](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   De aanvraag blijft vier dagen in de wachtrij van de klant. Na deze periode verloopt de toegangs aanvraag automatisch en wordt er geen toegang verleend aan micro soft-technici.

9. Om de details van de aanvraag in behandeling te krijgen, kan de aangewezen fiatteur de lockbox-aanvraag selecteren in **in behandeling zijnde aanvragen**:

    ![Azure Klanten-lockbox: de in behandeling zijnde aanvraag weer geven](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. De aangewezen fiatteur kan ook de **service aanvraag-id** selecteren om de ondersteunings ticket aanvraag weer te geven die door de oorspronkelijke gebruiker is gemaakt. Deze informatie geeft een context voor waarom Microsoft Ondersteuning wordt ingeschakeld, en de geschiedenis van het gemelde probleem. Bijvoorbeeld:

    ![Azure Klanten-lockbox: de aanvraag voor het ondersteunings ticket weer geven](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Nadat de aanvraag is gelezen, selecteert de aangewezen goed keurder **goed keuren** of **weigeren**:

    ![Azure Klanten-lockbox-goed keuren of weigeren selecteren](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Als gevolg van de selectie:
    - **Goed keuren**: de toegang tot de micro soft-Engineer wordt verleend. De toegang wordt voor een standaard periode van acht uur verleend.
    - **Weigeren**: de aanvraag voor verhoogde toegang van de micro soft-Engineer wordt geweigerd en er wordt geen verdere actie ondernomen.

Voor controle doeleinden worden de acties die in deze werk stroom worden uitgevoerd, geregistreerd in [klanten-lockbox aanvraag logboeken](#auditing-logs).

## <a name="auditing-logs"></a>Controlelogboeken

Logboeken van de Klanten-lockbox worden opgeslagen in activiteitenlogboeken. Selecteer in de Azure Portal **activiteiten logboeken** om controle-informatie te bekijken die betrekking heeft op klanten-lockbox aanvragen. U kunt filteren op specifieke acties, zoals:
- **Lockbox-aanvraag weigeren**
- **Lockbox-aanvraag maken**
- **Lockbox-aanvraag goedkeuren**
- **Verval datum lockbox-aanvraag**

Bijvoorbeeld:

![Azure Klanten-lockbox-activiteiten logboeken](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Klanten-lockbox-integratie met Azure Security Benchmark

We hebben een nieuw basislijn besturings element ([3,13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) geïntroduceerd in azure Security Bench mark dat klanten-lockbox toepas baarheid dekt. Klanten kunnen nu benchmarks gebruiken om Klanten-lockbox toepasselijkheid van een service te controleren.

## <a name="exclusions"></a>Uitsluitingen

Klanten-lockbox aanvragen worden niet geactiveerd in de volgende technische ondersteuningsscenario's:

- Een Microsoft-engineer moet een activiteit uitvoeren die buiten de standaard werkprocedures valt. U kunt bijvoorbeeld services herstellen of terugzetten in onverwachte of onvoorspelbare scenario's.

- Een Microsoft-engineer heeft toegang tot het Azure-platform als onderdeel van het oplossen van problemen en daardoor per ongeluk toegang tot klantgegevens. Als voorbeeld, het team van het Azure-netwerk voert probleemoplossing uit, wat resulteert in het vastleggen van pakketten op een netwerkapparaat. Als de klant echter de gegevens tijdens de overdracht versleutelde, kan de engineer de gegevens niet lezen.

## <a name="next-steps"></a>Volgende stappen

Klanten-lockbox is automatisch beschikbaar voor alle klanten die een [ondersteunings abonnement voor Azure](https://azure.microsoft.com/support/plans/) hebben met een mini maal **ontwikkel** niveau.

Wanneer u een in aanmerking komend ondersteunings plan hebt, hoeft u geen actie te ondernemen om Klanten-lockbox in te scha kelen. Klanten-lockbox aanvragen worden geïnitieerd door een micro soft-Engineer als deze actie nodig is om een ondersteunings ticket te maken dat van iemand in uw organisatie is ingediend.