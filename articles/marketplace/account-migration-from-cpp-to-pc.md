---
title: Accounts migreren van Cloud Partner-portal naar de commerciële marketplace van Microsoft
description: Meer informatie over het migreren van uw account van Cloud Partner-portal naar Partner Center in de commerciële Marketplace van Microsoft voor Azure
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 09/23/2019
ms.openlocfilehash: 189966c468fe5a39fbd44f7961e9512b7b054882
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811782"
---
# <a name="how-to-migrate-your-account-from-cloud-partner-portal-to-partner-center"></a>Uw account migreren van Cloud Partner-portal naar Partner Center

Als u een bestaand CPP Cloud Partner-portal account hebt, moeten uw accountinstellingen worden gemigreerd naar Partner Center.

## <a name="account-migration-process"></a>Accountmigratieproces

Als u een gebruiker bent met de rol Eigenaar in CPP voor een bepaald account, wordt er een gele banner weergegeven op de pagina Uitgeversprofiel. U kunt deel uitmaken van een van de volgende twee gevallen:

- Uw account is al gemigreerd en u bent klaar om uw accountinstellingen te beheren in Partner Center.
- Uw account is niet gemigreerd naar Partner Center en u moet verdere actie ondernemen.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Uw account is gemigreerd naar Partner Center

U beheert uw account nu in Partner Center. Wijzigingen, zoals het optellingen/verwijderen van gebruikers, worden opnieuw gesynchroniseerd naar CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>U hebt uw account nog niet gemigreerd naar Partner Center

Klik op de banner om het migratieproces van uw account te starten. U wordt verwacht de volgende items in te voeren:

1. Werk-e-mailadres: <br> <br> In de meeste gevallen moet u zich aanmelden met het e-mailadres dat u gebruikt om u aan te melden bij CPP. In bepaalde gevallen moet een ander e-mailadres worden gebruikt:

    * Microsoft-account: als het CPP-account een Microsoft-account is, voert u een geldig werk-e-mailadres in dat is gekoppeld aan de tenant voor wie de MPN-id (Microsoft Partner Network) is geregistreerd. Zie Registreren voor Microsoft Partner Network [Program voor meer informatie.](#sign-up-for-microsoft-partner-network-program)

    * Tenant komt niet overeen: als uw werk-e-mailadres niet behoort tot de tenant die is gekoppeld aan de Microsoft Partner Network-id die aanwezig is in uw CPP-account, wordt er een foutbericht weergegeven. Als u verder wilt gaan dan deze fout, voert u een e-mailadres in dat is gekoppeld aan de tenant. Er wordt een foutbericht weergegeven met de naam van de tenant.

2. Registreren voor Microsoft Partner Network programma

    Als uw CPP-account geen Microsoft Partner Network-id heeft of een ongeldige id heeft, moet u zich registreren voor het Microsoft Partner Network-programma als onderdeel van het activeringsproces.

## <a name="publishers-moving-from-cpp"></a>Uitgevers over van CPP

Als uw account is gemigreerd van de CPP, hoeft u geen nieuw Partner Center maken. U moet een aangepaste koppeling naar uw nieuwe Partner Center-account hebben ontvangen via e-mail en in een bannermelding nadat u zich hebt aanmelden bij uw bestaande CPP-account.

Zodra u uw nieuwe Partner Center-account hebt ingeschakeld door deze aangepaste koppeling te bezoeken, kunt u terugkeren naar uw account door het dashboard commerciële [marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) in Partner Center.

De publicatieovereenkomst en bedrijfsprofielgegevens worden gemigreerd naar uw nieuwe Partner Center-account, samen met eventuele eerder ingestelde profielgegevens voor uitbetaling van accounts, gebruikersaccounts en machtigingen en actieve aanbiedingen die zijn gekoppeld aan uw CPP-account.

Nadat uw accountgegevens zijn verplaatst van CPP naar Partner Center, gebruikt u CPP niet meer om accountupdates uit te voeren of gebruikers, machtigingen en facturering te beheren. Gedurende een beperkte periode worden alle accountupdates die u in Partner Center maakt, automatisch bijgewerkt in uw alleen-lezen CPP-account totdat de CPP-portal uiteindelijk wordt afgeschaft.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Registreren voor Microsoft Partner Network programma

Bedrijven die willen samenwerken met Microsoft, moeten lid worden van de Microsoft Partner Network (MPN) en een MPN-id krijgen. Als u al lid bent van de Microsoft Partner Network en een MPN-id hebt, moet u de informatie bij de hand houden omdat u deze nodig hebt tijdens het activeringsproces van het account.  

Als u geen lid bent van de Microsoft Partner Network, kunt u hier deelnemen [om](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) een MPN-id op te halen. Noteer uw MPN-id, aangezien u deze moet invoeren tijdens het activeringsproces van het account.

Zie Join [the Microsoft Partner Network](https://partner.microsoft.com/membership) on the partner website (Deelnemen aan de Microsoft Partner Network op de partnerwebsite) voor meer informatie over de Microsoft Partner Network. Zie de ISV Resource Hub voor Microsoft Partner Network meer informatie over [ISV-voordelen](https://partner.microsoft.com/isv-resource-hub)in de Microsoft Partner Network.  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Dynamics 365- en PowerApps-aanbiedingen verplaatsen naar Partner Center

Om het account- en aanbiedingsbeheer voor Dynamics 365 Customer Engagement, PowerApps en Dynamics 365 Operations te stroomlijnen, zijn aanbiedingen verplaatst [naar Partner Center](https://partner.microsoft.com/). De overstap zorgt ervoor dat dezelfde inhoud beschikbaar is voor zowel openbare catalogussen als verkopercatalogi.

Volg de onderstaande instructies voor specifieke informatie over wat er moet worden gedaan op **15 oktober 2019** voor uw Dynamics 365 Customer Engagement-, PowerApps- en Dynamics 365 Operations-aanbiedingen.

> [!NOTE]
> Dit geldt niet voor aanbiedingen van Dynamics 365 Business Central.  

1. Als uw MPN-lidmaatschapsaccount oorspronkelijk is gemaakt in Partner Membership Center (PMC), moet u zich aanmelden bij [Partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) om te bevestigen dat uw account is gemigreerd. Als u een profielscherm met uw MPN-id ziet, kunt u doorgaan. Als dat niet het beste is, moet u de migratie van uw account starten door de aanwijzingen in de [Partner Membership Center.](https://partners.microsoft.com/partnerprogram/Welcome.aspx) Als u hulp nodig hebt, gaat u naar [ondersteuning](https://partner.microsoft.com/support?issueid=100-0077).
2. Ga naar de [overzichtspagina Commerciële marketplace in Partner Center.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Als u Commerciële marketplace ziet in het navigatiedeelvenster aan de linkerkant, bent u ingeschreven en gaat u verder met de volgende stap. Zo niet, [schrijf u dan nu in op de commerciële marketplace.](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)
3. Controleer of uw aanbiedingen zich in AppSource zijn door [te zoeken naar uw aanbiedingen.](https://appsource.microsoft.com/) Als uw aanbiedingen al in AppSource staan, gaat u verder met de volgende stap. Zie Een [Dynamics 365 Customer Engagement-aanbieding](dynamics-365-customer-engage-offer-setup.md) plannen of Een [Dynamics 365 Operations-aanbieding](.\partner-center-portal\create-new-operations-offer.md)maken voor een aanbieding die niet beschikbaar is in AppSource.
4. Zorg Partner Center u op [de](https://partner.microsoft.com/dashboard/account/agreements)pagina Overeenkomsten van de ISV hebt gecontroleerd en geaccepteerd **dat Business Applications ISV-addendum hebt geaccepteerd.**
5. Zorg Partner Center uw [factureringsgegevens zijn](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)voltooid in de accountinstellingen van de Partner Center.
6. Verzend elke nieuwe en bestaande aanbieding voor certificering en publicatie, zelfs als uw aanbiedingen eerder zijn gecertificeerd.
    * Vul de informatieschermen in, inclusief het verstrekken van uw app voor certificering en marketinginformatie. Selecteer **Indienen** (rechterbovenhoek van het scherm) voor **15 oktober 2019.** Deze stappen moeten worden uitgevoerd om te voorkomen dat de beschikbaarheid van de aanbieding wordt beïnvloed.
    * Als u in aanmerking komt, kunt u tijdens dit proces een aanvraag indienen om deel te nemen aan de Premium-laag.
    * Voor certificering of hercertificering moet uw app ondersteuning bieden voor de nieuwste versie van ons Business Applications Platform.
    * Zodra uw app is goedgekeurd, ontvangt u een e-mailbericht om terug te keren naar de aanbieding en selecteert u Live gaan om de aanbieding live te laten Microsoft AppSource.

## <a name="additional-resources"></a>Aanvullende bronnen

Krijg hulp van experts en collega's in forums en ontdek blogs, webinars, video's, gebeurtenissen en meer op [Microsoft Dynamics CRM.](https://community.dynamics.com/crm?wa=wsignin1.0)

Als u hulp nodig hebt bij het publiceren, certificeren of beheren van uw Marketplace-aanbiedingen, dient [u een ondersteuningsticket in.](https://aka.ms/MarketplacePublisherSupport)

## <a name="next-step"></a>Volgende stap

- [Uw commerciële marketplace-account beheren in Partner Center](./manage-account.md)
