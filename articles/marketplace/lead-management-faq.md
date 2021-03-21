---
title: Vragen en probleem oplossing voor het beheer van leads-micro soft Partner Center
description: Meer informatie over veelvoorkomende fouten en vragen bij het configureren van leads voor commerciële Marketplace in het partner centrum
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330917"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Veelgestelde vragen en probleem oplossing voor lead configuratie

In dit artikel vindt u antwoorden op enkele veelgestelde vragen over Lead beheer voor uw commerciële Marketplace-aanbiedingen. Er worden ook problemen opgelost die zich kunnen voordoen bij het configureren van leads aan uw CRM-systeem (Customer Relationship Management) in Partner Center.

## <a name="common-questions-about-lead-management"></a>Veelgestelde vragen over Lead beheer

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Waar kan ik hulp krijgen bij het instellen van mijn lead bestemming?

Bekijk [klanten leads van uw commerciële Marketplace-aanbieding](partner-center-portal/commercial-marketplace-get-customer-leads.md) voor een overzicht van hoe u uw CRM-systeem verbindt met uw commerciële Marketplace-aanbiedingen. Als er een fout optreedt, raadpleegt u de onderstaande richt lijnen voor probleem oplossing. Voor meer ondersteuning kunt u een ondersteunings ticket indienen via de [Help en ondersteuning van het partner centrum](https://aka.ms/marketplacepublishersupport). Selecteer vervolgens **aanbieding maken**  >  **uw type**  >  **configuratie van lead beheer**.

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Moet ik een lead bestemming configureren om een aanbieding in de commerciële Marketplace te publiceren?

Het antwoord is afhankelijk van het type aanbieding dat u publiceert. Software as a Service (SaaS) en Dynamics 365 Customer engagement gebruiken **contact met mij** om een lijst te maken met alle Dynamics 365 voor Financiën en operationele aanbiedingen, alle Dynamics 365 Business Central-aanbiedingen en alle advies service aanbiedingen. Als gevolg hiervan is een verbinding met een lead bestemming vereist. Als uw aanbiedings type niet wordt vermeld, is er geen verbinding met een doel voor de lead vereist. We raden u aan om een doel voor de lead te configureren zodat u geen zakelijke kansen meer hebt.

#### <a name="how-can-i-find-the-test-lead"></a>Hoe kan ik de test lead vinden?

Zoek naar `"MSFT_TEST"` de doel locatie van de lead. Hieronder vindt u een voor beeld van een test lead van micro soft. Houd er rekening mee dat de indeling van de leider van de test afhankelijk is van de doel locatie van de lead.

```
{
    "userDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Ik heb een live aanbieding, maar waarom krijg ik geen leads te zien?

Zorg ervoor dat de verbinding met de doel locatie van de lead geldig is. U ontvangt een test lead nadat u **publiceren** op uw aanbieding hebt geselecteerd in partner centrum. Als u de test leider ziet, is de verbinding geldig. U kunt ook uw lead verbinding testen door te proberen de preview-versie van de aanbieding te verkrijgen tijdens de preview-stap. Selecteer **nu downloaden**, **Neem contact met mij** op of **Maak een gratis proef versie** van de aanbieding in de commerciële Marketplace.

Zorg er ook voor dat u de juiste gegevens zoekt. Zie [informatie over leads](partner-center-portal/commercial-marketplace-get-customer-leads.md) voor een uitleg van de lead gegevens die we verzenden naar uw lead bestemming.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Ik heb Azure Blob-opslag geconfigureerd als mijn lead-doel, maar waarom zie ik de lead niet?

Azure Blob-opslag wordt niet langer ondersteund als een lead bestemming, dus er ontbreken potentiële klant leads die zijn gegenereerd door uw aanbieding. Schakel over naar een van de andere opties voor de [doel bestemming](partner-center-portal/commercial-marketplace-get-customer-leads.md). 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Ik heb een e-mail bericht ontvangen van de commerciële Marketplace, maar waarom kan ik de lead niet vinden in mijn CRM?

Het is mogelijk dat het e-mail domein van de eind gebruiker afkomstig is van. edu. Uit het oogpunt van privacy geven we geen persoonlijke gegevens uit het domein. edu door. Verzend een ondersteunings ticket via het [partner centrum hulp en ondersteuning](https://aka.ms/marketplacepublishersupport).

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Ik heb een Azure-tabel geconfigureerd als mijn lead bestemming. Hoe kan ik de leads weer geven?

U kunt toegang krijgen tot de lead gegevens die zijn opgeslagen in de Azure-tabel vanuit het Azure Portal. U kunt [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ook gratis downloaden en installeren om de tabel gegevens van uw Azure Storage-account weer te geven.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Ik heb een Azure-tabel geconfigureerd als mijn lead bestemming. Kan ik een melding ontvangen wanneer een nieuwe lead voor een commerciële Marketplace wordt verzonden?

Ja. Volg de instructies in [beheer van leads configureren met behulp van een Azure-tabel](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) voor het instellen van een micro soft flow waarmee een e-mail wordt verzonden als een lead wordt toegevoegd aan de Azure-tabel.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Ik heb Sales Force geconfigureerd als mijn lead-doel, maar waarom kan ik de leads niet vinden?

Controleer of het formulier web-naar-lead een verplicht veld is op basis van een keuze lijst. Als dat het geval is, schakelt u het veld in op een niet-verplicht tekst veld.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Er is een probleem met mijn lead doel en er zijn enkele leads gemist. Kan ik ze in een e-mail bericht naar mij verzenden?

Vanwege het beleid voor persoonlijke gegevens kunnen we geen lead gegevens delen via onbeveiligde e-mail berichten.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Ik heb een Azure-tabel geconfigureerd als mijn lead bestemming. Wat kost het?

De gegevens voor het genereren van leads zijn laag. Het is minder dan 1 GB voor bijna alle uitgevers. De kosten zijn afhankelijk van het aantal ontvangen leads. Als bijvoorbeeld 1.000-leads per maand worden ontvangen, zijn de kosten ongeveer 50 cent. Zie [Azure Storage-overzicht](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen voor opslag.

Als uw vraag niet wordt beantwoord, neemt u contact op met Microsoft Ondersteuning via de [Help en ondersteuning van het partner centrum](https://aka.ms/marketplacepublishersupport). Selecteer vervolgens **aanbieding maken**  >  **uw type**  >  **configuratie van lead beheer**.

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Ik ontvang e-mail meldingen wanneer er nieuwe leads van klanten worden ontvangen. Hoe kan ik iemand anders configureren voor het ontvangen van deze e-mail berichten?

Krijg toegang tot uw aanbieding in Partner Center en ga naar de pagina voor het instellen van de **aanbieding** > **lead beheer**  >  **bewerken**. Werk de e-mail adressen bij in het veld **e-mail adres van contact persoon** .

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Problemen met configuratie fouten van de lead oplossen

**Kan de lead niet opslaan in Dynamics CRM. Controleer de instellingen van het Dynamics CRM-account. LastCRMError: kan niet aanmelden bij Dynamics CRM, LastCRMException:** 

> Als Microsoft 365 verificatie is geselecteerd, controleert u of het gebruikers account en het wacht woord geldig zijn. Als Azure Active Directory is geselecteerd, controleert u of de Tenant-ID, toepassings-ID en sleutel toepassing geheim overeenkomt met wat is ingesteld op Azure Active Directory. Volg de instructies [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Als de gebruikers naam of het wacht woord van het account geldig is, controleert u of het toegang heeft tot Dynamics 365 en of er een licentie is toegewezen (stap 11-15 als u Azure Active Directory of beveiligings instellingen gebruikt als u een Office-gebruiker gebruikt). 

**Kan de lead niet opslaan in Dynamics CRM. De gebruiker heeft geen machtigingen voor het maken van het kenmerk leadsourcecode in de entiteit lead** 

> De toepassing/gebruiker mist een of meer beveiligings rollen om de lead schrijver te Microsoft Marketplace. Volg de stappen 11-15 als u Azure Active Directory of beveiligings instellingen gebruikt als u [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)een Office-gebruiker gebruikt.

**Kan de potentiële klant niet opslaan in Dynamics CRM met AAD. Uitzonde ring:: de Tenant is niet gevonden. Dit exemplaar kan zich voordoen als er geen actieve abonnementen voor de Tenant zijn.**  

> De map-id die is opgenomen in de sectie Lead beheer is geen geldige map. U kunt de Directory-id ophalen op basis van de instructies bij stap 2 (onder [Azure Active Directory).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)

**Kan de lead niet opslaan in Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser is mislukt-er zijn geen rollen aan de gebruiker toegewezen.**  

> Oplossing: wijs de beveiligingsrol toe aan Microsoft Marketplace lead Writer. Volg de [instructies in](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) de beveiligings instellingen.

**Kan de potentiële klant niet opslaan in Dynamics CRM met AAD. Uitzonde ring:: de toepassing met de id is niet gevonden in de map** 

> De toepassings-id die is opgenomen in de sectie Lead beheer is geen geldige map. Haal de Directory-id op op basis van de instructies in stap 8 (onder Azure Active Directory van [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Kan de potentiële klant niet opslaan in Dynamics CRM met AAD. Uitzonde ring:: de aangevraagde Tenant-id is ongeldig en heeft geen geldige indeling voor een extern domein** 

> De map-id die is opgenomen in de sectie Lead beheer is geen geldige map. Haal de Directory-id op op basis van de instructies bij stap 2 (onder Azure Active Directory van [hier](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)). 

**Kan de potentiële klant niet opslaan in Dynamics CRM met AAD. Uitzonde ring:: fout bij het valideren van referenties.: er is een ongeldig client geheim gegeven.** 

> Oplossing: Meld u aan bij de Azure Portal, Controleer of de toepassings sleutel overeenkomt met wat is in het partner centrum. Genereer een wacht woord op [basis van de](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md)instructie bij stap 10 (onder Azure Active Directory). 

**Kan de lead niet opslaan in Dynamics CRM. LastCRMError: er is een time-out opgetreden voor het aanvraag kanaal tijdens het wachten op een antwoord na 00:02:00. Verhoog de time-outwaarde die is door gegeven aan de aanroep naar request of verhoog de SendTimeout-waarde voor de binding. De tijd die aan deze bewerking wordt toegewezen, heeft mogelijk een deel van een langere time-out.**  

> Oplossing: Meld u aan bij Partner Center, Controleer de installatie van de aanbieding >> klant leads >> URL en controleer of het een geldig dynamisch CRM-exemplaar is.

