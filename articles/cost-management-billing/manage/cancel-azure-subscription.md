---
title: Uw Azure-abonnement opzeggen
description: Hier wordt beschreven hoe u een Azure-abonnement, zoals het gratis proefabonnement, kunt opzeggen
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: banders
ms.openlocfilehash: 0d0dd5342cfc57da776e38ddef3df3b9c2199ac2
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735772"
---
# <a name="cancel-your-azure-subscription"></a>Uw Azure-abonnement opzeggen

Als u uw Azure-abonnement niet meer nodig hebt, kunt u het opzeggen in de Azure-portal.

Hoewel dit niet vereist is, geeft Microsoft de *aanbeveling* de volgende acties te ondernemen voordat u uw abonnement annuleert:

* Maak een back-up van uw gegevens. Als u gegevens opslaat in Azure Storage of SQL, moet u bijvoorbeeld een kopie downloaden. Als u een virtuele machine hebt, slaat u lokaal een kopie van die VM op.
* Sluit uw services af. Ga naar de [pagina met resources in de beheerportal](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) en **Stop** alle actieve virtuele machines, toepassingen of andere services.
* Overweeg uw gegevens te migreren. Zie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Verwijder alle resources en alle resourcegroepen.
* Als u aangepaste rollen hebt die verwijzen naar dit abonnement in `AssignableScopes`, moet u die aangepaste rollen bijwerken om het abonnement te kunnen verwijderen. Als u een aangepaste rol probeert bij te werken nadat u een abonnement hebt opgezegd, wordt er mogelijk een foutmelding weergegeven. Zie [Problemen met aangepaste rollen oplossen](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) en [Aangepaste rollen voor Azure](../../role-based-access-control/custom-roles.md) voor meer informatie.

Als u een betaald Azure-ondersteuningsplan annuleert, wordt u gefactureerd voor de rest van de abonnementsperiode. Zie [Ondersteuningsabonnementen voor Azure](https://azure.microsoft.com/support/plans/) voor meer informatie.

## <a name="who-can-cancel-a-subscription"></a>Wie kan een abonnement annuleren?

In de onderstaande tabel worden de machtigingen beschreven die zijn vereist voor het opzeggen van een abonnement.

|Abonnementstype     |Wie het abonnement kan opzeggen  |
|---------|---------|
|Abonnementen die zijn gemaakt toen u zich aanmeldde voor Azure via de Azure-website. Bijvoorbeeld als u zich aanmeldt voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), een [account met tarieven op gebruiksbasis](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Beheerder van het account en eigenaars van het abonnement  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) en [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Eigenaar van het account en eigenaars van het abonnement       |
|[Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/) en [Azure-plan voor DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Eigenaars van het abonnement      |


## <a name="cancel-subscription-in-the-azure-portal"></a>Abonnement annuleren in de Azure-portal

1. Selecteer uw abonnement op de [pagina Abonnementen in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer het abonnement dat u wilt opzeggen.
1. Selecteer **Overzicht** en selecteer vervolgens **Abonnement annuleren**.
    ![Schermopname waarin de knop ‘Abonnement annuleren’ wordt weergegeven](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Volg de aanwijzingen om de opzegging te voltooien.

> [!NOTE]
> Partners kunnen een abonnement opschorten of annuleren indien dit wordt aangevraagd door een klant of in geval van niet-betaling of fraude. Zie [Een abonnement onderbreken of annuleren](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription) voor meer informatie.

## <a name="cancel-a-support-plan"></a>Een ondersteuningsplan annuleren

Als u uw ondersteuningsplan hebt aangeschaft via de Azure-website of de Azure-portal, of als u er een hebt onder een Microsoft-klantovereenkomst, kunt u het ondersteuningsplan annuleren. Als u uw ondersteuningsplan hebt aangeschaft via een Microsoft-vertegenwoordiger of -partner, neemt u contact met hen op voor hulp. 

1. Ga in de Azure-portal naar **Cost Management + Billing**.
1. Selecteer onder **Facturering** de optie **Terugkerende kosten**.
1. Selecteer aan de rechterkant van het regelitem voor het ondersteuningsplan het weglatingsteken ( **...** ), en selecteer **Automatisch verlengen uitschakelen**.

## <a name="what-happens-after-subscription-cancellation"></a>Wat gebeurt er nadat het abonnement is opgezegd?

Nadat u uw abonnement hebt opgezegd, wordt de facturering onmiddellijk stopgezet. Het kan echter wel maximaal 10 minuten duren voordat de opzegging in de portal wordt weergegeven. Als u uw abonnement halverwege een factureringsperiode opzegt, verzenden we de laatste factuur op de gebruikelijke factuurdatum, na afloop van de periode.

Nadat u het abonnement hebt opgezegd, worden uw services uitgeschakeld. Dit betekent dat de toewijzing uw virtuele machines ongedaan wordt gemaakt, dat uw tijdelijke IP-adressen worden vrijgemaakt en dat uw opslag het kenmerk Alleen-lezen heeft.

Nadat uw abonnement is geannuleerd, wacht Microsoft 30 tot 90 dagen voordat uw gegevens permanent worden verwijderd, voor het geval u er toegang toe moet hebben of u van gedachten verandert. Wij brengen u niets in rekening voor het bewaren van de gegevens. Zie [Vertrouwenscentrum van Microsoft - Hoe we uw gegevens beheren](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409) voor meer informatie.

## <a name="delete-free-trial-subscription"></a>Gratis proefabonnement verwijderen

Als u een gratis proefabonnement hebt, hoeft u niet 30 dagen te wachten tot het abonnement automatisch wordt verwijderd. U kunt uw abonnement *drie dagen* nadat u het hebt geannuleerd verwijderen. De optie **Abonnement verwijderen** is pas drie dagen nadat u uw abonnement hebt opgezegd beschikbaar.

1. Wacht tot drie dagen na de datum waarop u het abonnement hebt geannuleerd.
1. Selecteer uw abonnement op de pagina [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in Azure Portal.
1. Selecteer het abonnement dat u wilt verwijderen.
1. Selecteer **Overzicht** en **Abonnement verwijderen**.

## <a name="reactivate-a-subscription"></a>Een abonnement opnieuw activeren

Als u uw abonnement per ongeluk betaalt met betalen naar gebruik-tarieven, kunt u [deze opnieuw activeren in de Azure Portal](subscription-disabled.md).

Als uw abonnement geen abonnement is met tarieven voor betalen per gebruik, neemt u binnen 90 dagen na opzegging contact op met het ondersteuningsteam om uw abonnement opnieuw te activeren.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Waarom zie ik de optie Abonnement opzeggen niet in de Azure-portal? 

U hebt mogelijk niet de vereiste machtigingen om een abonnement op te zeggen. Zie [Wie kan een abonnement opzeggen?](#who-can-cancel-a-subscription) voor een beschrijving van wie verschillende soorten abonnementen kan opzeggen.

## <a name="how-do-i-delete-my-azure-account"></a>Hoe verwijder ik mijn Azure-account?

*Ik moet mijn account verwijderen, inclusief al mijn persoonlijke gegevens. Ik heb mijn actieve abonnementen (gratis proefabonnementen) al opgezegd. Ik heb geen actieve abonnementen en wil mijn account graag helemaal verwijderen*.

* Als u via uw organisatie een Azure Active Directory-account hebt, kan de Azure AD-beheerder het account verwijderen. Daarna worden uw services uitgeschakeld. Dit betekent dat de toewijzing uw virtuele machines ongedaan wordt gemaakt, dat uw tijdelijke IP-adressen worden vrijgemaakt en dat uw opslag het kenmerk Alleen-lezen heeft. Ter samenvatting: nadat u uw abonnement hebt opgezegd, wordt de facturering onmiddellijk stopgezet.

* Als u niet via uw organisatie een Azure AD-account hebt, kunt u uw Azure-abonnementen opzeggen en verwijderen en vervolgens uw creditcard uit het account verwijderen. Hoewel het account niet wordt verwijderd met deze actie, wordt het wel onbruikbaar. U kunt nog een stap verder gaan en ook het bijbehorende Microsoft-account verwijderen als dat niet voor andere doeleinden wordt gebruikt.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Hoe annuleer ik een Visual Studio Professional-account?

Zie het artikel [Verlenging en opzegging](/visualstudio/subscriptions/faq/admin/renewal-cancellation). Als u Visual Studio Azure-abonnementen hebt, moeten ze worden opgezegd en ook worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

- Als dat nodig is, kunt u een abonnement op basis van betalen naar gebruik opnieuw activeren in de [Azure Portal](subscription-disabled.md).