---
title: Veelvoorkomende problemen oplossen bij het toevoegen of verwijderen van een toepassing aan Azure Active Directory
description: Los het probleem op met veelvoorkomende problemen bij het toevoegen of verwijderen van een app aan Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.openlocfilehash: f873608d07785e6b99bd8e0fcdadf64a8899d490
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108412"
---
# <a name="troubleshoot-common-problem-adding-or-removing-an-application-to-azure-active-directory"></a>Veelvoorkomende problemen oplossen bij het toevoegen of verwijderen van een toepassing aan Azure Active Directory
Dit artikel helpt u bij het begrijpen van de veelvoorkomende problemen bij het toevoegen of verwijderen van een app aan Azure Active Directory.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Ik heb op de knop toevoegen geklikt en het duurt lang voordat mijn toepassing wordt weer gegeven
In sommige gevallen kan het 1-2 minuten (en soms langer) duren voordat een toepassing wordt weer gegeven nadat deze is toegevoegd aan uw Directory. Dit is niet de normale verwachte prestaties, maar u kunt ook zien dat de toepassing wordt toegevoegd door te klikken op het pictogram **meldingen** (de Bell) in de rechter bovenhoek van de [Azure Portal](https://portal.azure.com/) en te zoeken naar een **lopende** of **voltooide** melding die de **toepassing toevoegt.**

Als uw toepassing nooit wordt toegevoegd of als er een fout optreedt wanneer u op de knop **toevoegen** klikt, ziet u een **melding** in een **fout** status. Als u meer informatie wilt over de fout voor meer informatie over of als u wilt delen met een ondersteunings technicus, kunt u de volgende stappen volgen in de sectie [Details van een portal melding weer geven](#how-to-see-the-details-of-a-portal-notification) .

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Ik heb geklikt op de knop toevoegen en mijn toepassing wordt niet weer gegeven
Soms mislukt het toevoegen van een toepassing als gevolg van tijdelijke problemen, netwerk problemen of een bug. U kunt dit zien wanneer u op het pictogram **meldingen** (de Bell) in de rechter bovenhoek van de Azure Portal klikt en er een rood pictogram (!) wordt weer geven naast de melding voor het toevoegen van de **toepassing** . Dit geeft aan dat er een fout is opgetreden bij het maken van de toepassing.

Als er een fout optreedt wanneer u op de knop **toevoegen** klikt, wordt er een **melding** weer geven met een **fout** status. Als u meer informatie wilt over de fout voor meer informatie over of als u wilt delen met een ondersteunings technicus, kunt u de volgende stappen volgen in de sectie [Details van een portal melding weer geven](#how-to-see-the-details-of-a-portal-notification) .

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Ik weet niet hoe ik mijn toepassing moet instellen nadat ik deze heb toegevoegd
Als u hulp nodig hebt bij het leren van toepassingen, is de [lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](../saas-apps/tutorial-list.md) -artikel een goede plaats om te starten.

Daarnaast kunt u met de [document bibliotheek van Azure AD-toepassingen](./what-is-application-management.md) meer te weten komen over eenmalige aanmelding met Azure AD en hoe deze werkt.

## <a name="i-want-to-delete-an-application-but-the-delete-button-is-disabled"></a>Ik wil een toepassing verwijderen, maar de knop verwijderen is uitgeschakeld

De knop verwijderen wordt uitgeschakeld in de volgende scenario's:

- Als u geen van de volgende rollen hebt: globale beheerder, Cloud toepassings beheerder, toepassings beheerder of eigenaar van de Service-Principal, voor toepassingen onder een bedrijfs toepassing.

- Voor micro soft-toepassingen kunt u ze niet verwijderen uit de gebruikers interface, ongeacht uw rol.

- Voor servicePrincipals die overeenkomen met een beheerde identiteit. Service-principals voor beheerde identiteiten kunnen niet worden verwijderd op de Blade Enter prise-apps. U moet naar de Azure-resource gaan om deze te beheren. Meer informatie over [beheerde identiteit](../managed-identities-azure-resources/overview.md)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>De details van een portal melding weer geven
U kunt de details van een portal melding bekijken door de volgende stappen uit te voeren:
1.  Selecteer het pictogram **meldingen** (de Bell) in de rechter bovenhoek van de Azure Portal
2.  Selecteer een melding in een **fout** status (die met een rood (!) ernaast).
    >[!NOTE]
    >U kunt niet klikken op meldingen met de status **geslaagd** of **in uitvoering** .
4.  Gebruik de informatie onder **meldings Details** voor meer informatie over het probleem.
5.  Als u nog steeds hulp nodig hebt, kunt u deze informatie ook delen met een ondersteunings technicus of de product groep om hulp te krijgen bij het probleem.
6.  Selecteer het **Kopieer pictogram** rechts van het tekstvak **Kopieer fout** om alle meldings details te kopiëren die u wilt delen met de engineer ondersteuning of productgroep.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Hulp vragen door het verzenden van meldings gegevens naar een ondersteunings technicus
Het is belang rijk dat u **alle details die hieronder worden weer gegeven** , deelt met een ondersteunings technicus als u hulp nodig hebt, zodat u snel aan de slag kunt. **Maak een scherm opname** of selecteer het **pictogram copy error**, rechts van het tekstvak **copy error** .

## <a name="notification-details-explained"></a>Details van de melding
Raadpleeg de volgende beschrijvingen voor meer informatie over de meldingen.

### <a name="essential-notification-items"></a>Essentiële meldings items
- **Titel** : de beschrijvende titel van de melding
  * Voor beeld: **proxy-instellingen** van de toepassing
- **Beschrijving** : de beschrijving van wat er is gebeurd als gevolg van de bewerking
  -   Voor beeld: de **ingevoerde interne URL wordt al gebruikt door een andere toepassing**
- **Meldings-id** : de unieke id van de melding
  -   Voor beeld – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**
- **Client aanvraag-id** : de specifieke aanvraag-id die is gemaakt door de browser
  -   Voor beeld – **302fd775-3329-4670-a9f3-bea37004f0bc**
- **Tijds tempel UTC** : de tijds tempel waarin de melding is opgetreden, in UTC
  -   Voor beeld – **2017-03-23T19:50:43.7583681 z**
- **Interne trans actie-id** : de interne id die we kunnen gebruiken om de fout op te sporen in onze systemen
  -   Voor beeld – **71a2f329-ca29-402f-aa72-bc00a7aca603**
- **UPN** : de gebruiker die de bewerking heeft uitgevoerd
  -   Voor beeld – **tperkins \@ f128.info**
- **Tenant-id** : de unieke id van de Tenant waarvan de gebruiker die de bewerking heeft uitgevoerd lid is van
  -   Voor beeld – **7918d4b5-0442-4a97-be2d-36f9f9962ece**
- **Gebruikers object-id** : de unieke id van de gebruiker die de bewerking heeft uitgevoerd
  -   Voor beeld – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Gedetailleerde meldings items
-   **Weergave naam** – **(kan leeg zijn)** een meer gedetailleerde weergave naam voor de fout
    -   Voor beeld: **proxy-instellingen** van de toepassing
-   **Status** : de specifieke status van de melding
    -   Voor beeld: **mislukt**
-   **Object-id** – **(kan leeg zijn)** de object-id waartegen de bewerking is uitgevoerd
    -   Voor beeld – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**
-   **Details** : de gedetailleerde beschrijving van wat er is gebeurd als gevolg van de bewerking
    -   Voor beeld: **interne URL `https://bing.com/` is ongeldig omdat deze al wordt gebruikt**
-   **Kopieer fout** : Selecteer het **Kopieer pictogram** rechts van het tekstvak **Kopieer fout** om alle meldings gegevens te kopiëren die moeten worden gedeeld met een ondersteuning of product groep 
-   technicus
    -   Hierbij ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```
