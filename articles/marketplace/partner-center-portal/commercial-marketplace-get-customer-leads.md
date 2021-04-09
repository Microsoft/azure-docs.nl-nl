---
title: Lead beheer van micro soft Commercial Marketplace
description: Meer informatie over het genereren en ontvangen van leads van klanten van uw Microsoft AppSource en Azure Marketplace-aanbiedingen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 1d5f2248d94796d5e3ee76301642a95abddebfe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489332"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Leads van uw aanbieding in de commerciële marketplace

Leads zijn klanten die geïnteresseerd zijn in of implementeren van uw aanbiedingen van [Microsoft AppSource](https://appsource.microsoft.com) en [Azure Marketplace](https://azuremarketplace.microsoft.com). U kunt leads van klanten ontvangen nadat uw aanbieding naar de commerciële Marketplace is gepubliceerd. In dit artikel worden de volgende concepten voor lead management beschreven:

* Hoe uw commerciële Marketplace-aanbieding klanten leads genereert om ervoor te zorgen dat u geen zakelijke kansen meer mist. 
* Hoe koppelt u uw Customer Relationship Management-systeem (CRM) aan uw aanbieding zodat u uw leads kunt beheren op één centrale locatie.
* De lead gegevens die we u verzenden zodat u klanten kunt opvolgen die aan u zijn terechtgekomen.

## <a name="generate-customer-leads"></a>Leads van klanten genereren

Hier vindt u plaatsen waar een lead wordt gegenereerd:

- Een klant heeft toestemming gegeven om hun gegevens te delen nadat ze **contact met mij** hebben geselecteerd vanuit de commerciële Marketplace. Deze potentiële klant is een *eerste geïnteresseerde* lead. We delen informatie met u over de klant die geïnteresseerd is in het ophalen van uw product. De lead is de bovenkant van de verwervings trechter.

    ![Dynamics 365 contact met mij opnemen](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Een klant selecteert de optie **nu downloaden** (of **selecteren in** het [Azure Portal](https://portal.azure.com/)) om uw aanbieding te ontvangen. Deze lead is een *actieve* lead. We delen informatie met u over de klant die is begonnen met het implementeren van uw product.

    ![SQL-knop nu ophalen](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Knop Windows Server maken](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Een klant selecteert **test drive** of **gratis proef versie** om uw aanbieding uit te proberen. Test stations of gratis proef versies zijn versnelde kansen om uw bedrijf direct te delen met potentiële klanten zonder enige obstakels in te brengen.

    ![Knop Dynamics 365-test station](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Knop Dynamics 365 gratis proef versie](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Verbinding maken met uw CRM-systeem

[!INCLUDE [Links to lead configuration for different CRM systems](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Informatie over leads

Elke lead die u ontvangt tijdens het klant verwervings proces heeft gegevens in specifieke velden. Het eerste veld dat moet worden uitgecheckt `LeadSource` , is het veld, dat de volgende indeling heeft: de aanbieding voor de **bron actie**  |  .

**Bronnen**: de waarde voor dit veld wordt ingevuld op basis van de Marketplace die de lead heeft gegenereerd. Mogelijke waarden zijn `"AzureMarketplace"` , `"AzurePortal"` en `"AppSource (SPZA)"` .

**Acties**: de waarde voor dit veld wordt ingevuld op basis van de actie die de klant heeft ondernomen op de Marketplace die de lead heeft gegenereerd.

Mogelijke waarden zijn:

- **' Ins '**: staat voor *installatie*. Deze actie bevindt zich in azure Marketplace of AppSource wanneer een klant uw product aanschaft.
- **"PLT"**: staat voor een door de *partner geleide proef versie*. Deze actie bevindt zich in AppSource wanneer een klant de optie **contact opnemen** selecteert.
- **"DNC"**: staat voor *geen contact persoon*. Deze actie bevindt zich in AppSource wanneer een partner die op uw app-pagina is genoteerd, wordt gevraagd om contact met u op te nemen. Er wordt een melding weer gegeven dat deze klant door de gebruiker is opgenomen in uw app, maar waarmee geen contact moet worden opgenomen.
- **' Maken '**: deze actie bevindt zich alleen in de Azure Portal en wordt gegenereerd wanneer een klant uw aanbieding aan hun account koopt.
- **"StartTestDrive"**: deze actie is alleen voor de optie **test drive** en wordt gegenereerd wanneer een klant hun test drive start.

**Aanbiedingen**: mogelijk hebt u meerdere aanbiedingen in de commerciële Marketplace. De waarde voor dit veld wordt ingevuld op basis van de aanbieding die de lead heeft gegenereerd. De uitgevers-ID en aanbiedings-ID worden beide in dit veld verzonden en zijn de waarden die u hebt ingevoerd tijdens het publiceren van de aanbieding naar de Marketplace.

In de volgende voor beelden worden waarden in de verwachte indeling weer gegeven `publisherid.offerid` : 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Klant gegevens

De gegevens van de klant worden verzonden via meerdere velden. In het volgende voor beeld ziet u de klant gegevens die zijn opgenomen in een lead:

- Voor naam: John
- LastName: Smit
- E-mail: jsmith \@ Microsoft.com
- Telefoon: 1234567890
- Land: VS
- Bedrijf: micro soft
- Titel: CTO

>[!NOTE]
>Niet alle gegevens in het vorige voor beeld zijn altijd beschikbaar voor elke lead. Omdat u leads van meerdere stappen krijgt, zoals vermeld in de sectie ' leads van klanten genereren ', kunt u de leads het beste afhandelen door de records te verdubbelen en de vervolg-ups aan te passen. Op deze manier krijgt elke klant een passend bericht en maakt u een unieke relatie.

## <a name="best-practices-for-lead-management"></a>Aanbevolen procedures voor het beheer van leads

Hier volgen enkele aanbevelingen voor het aansturen van leads via uw verkoop cyclus:

- **Proces**: Definieer een duidelijk verkoop proces met mijl palen, analyses en duidelijke team eigendom.
- **Kwalificatie**: Definieer vereisten, die aangeven of een lead volledig is gekwalificeerd. Zorg ervoor dat verkoop-of marketing vertegenwoordigers leads goed kwalificeren voordat ze via het volledige verkoop proces door lopen.
- **Follow-up**: Vergeet niet binnen 24 uur om te volgen. U krijgt de lead in uw CRM-keuze onmiddellijk nadat de klant een test drive heeft geïmplementeerd. u kunt ze in een e-mail verzenden terwijl ze nog steeds warm zijn. Vraag een telefoon gesprek aan te vragen om beter te kunnen begrijpen of uw product een goede oplossing voor het probleem is. Verwacht wordt dat de gebruikelijke trans actie talloze opvolgings aanroepen vereist.
- **Nurture**: nurture uw leads om u op weg te helpen met een hogere winst marge. Inchecken, maar niet BOMBARD. We raden u aan om ten minste een paar keer e-mail berichten te verzenden voordat u deze sluit. Geef na de eerste poging niets op. Houd er rekening mee dat deze klanten rechtstreeks samen werken met uw product en tijd hebben besteed aan een gratis proef versie. Dit zijn fantastische kandidaten.

Nadat de technische installatie is uitgevoerd, moet u deze leads opnemen in uw huidige verkoop-en marketing strategie en operationele processen. We zijn geïnteresseerd in een beter inzicht in uw algehele verkoop proces en willen nauw keurig samen werken om leads van hoge kwaliteit en voldoende gegevens te bieden om u te laten slagen. We stellen uw feedback op de manier waarop we u kunnen helpen bij het optimaliseren en verbeteren van de leads die wij u met aanvullende gegevens sturen om deze klanten succesvol te maken. Laat het ons weten als u geïnteresseerd bent in [feedback](mailto:AzureMarketOnboard@microsoft.com) en suggesties zodat uw verkoop team succes voller kan maken met leads voor commerciële Marketplace.

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over Lead beheer en probleem oplossing](../lead-management-faq.md)