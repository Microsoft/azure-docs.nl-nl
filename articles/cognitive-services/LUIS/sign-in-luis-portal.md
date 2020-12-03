---
title: Aanmelden bij de LUIS-portal
description: Als u een nieuwe gebruiker bent die zich aanmeldt bij de LUIS-Portal, wijkt de aanmeldings ervaring enigszins af op basis van uw huidige gebruikers account.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: ae51dca466a9aaf489ba4628e13a5e13de25b9bc
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546859"
---
# <a name="sign-in-to-luis-portal"></a>Aanmelden bij de LUIS-portal

Als u een nieuwe gebruiker bent die zich aanmeldt bij de LUIS-Portal, verschilt de aanmeldings ervaring enigszins op basis van uw huidige gebruikers account:
  * Gekoppeld aan een Azure-abonnement
  * Niet gekoppeld aan een Azure-abonnement

## <a name="determine-account-type"></a>Account type bepalen

Wanneer u zich voor het eerst aanmeldt bij de LUIS-Portal, gebruikt u de volgende visuele indica toren om het account type te bepalen.

### <a name="account-without-azure-subscription"></a>Account zonder Azure-abonnement

Een account, dat niet aan een Azure-abonnement is gekoppeld, heeft het Azure-pictogram in de bovenste navigatie balk. Wanneer u de migratie naar het gekoppelde account type hebt uitgevoerd, wordt het pictogram niet meer weer gegeven.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="Gedeeltelijk scherm: opname van de LUIS-navigatie balk met het pictogram van Azure.":::

### <a name="account-with-azure-subscription"></a>Account met Azure-abonnement

Met een account dat is gekoppeld aan een Azure-abonnement kunt u het te gebruiken abonnement en de resource selecteren.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="Gedeeltelijke scherm opname van de LUIS-Portal met de vervolg keuzelijsten voor het maken van abonnementen en ontwerpen van resources.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Aanmelden met een account dat is gekoppeld aan een Azure-abonnement

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai) en ga akkoord met de gebruiks voorwaarden.

1. U hebt twee opties bij het aanmelden:

    * Ga door met het gebruik van een Azure-resource. Dit is het aanbevolen pad en het is binnenkort het enige beschik bare pad. Met dit pad kunt u uw LUIS-account koppelen aan een Azure-ontwerp bron terwijl u zich aanmeldt door een bestaande resource in uw abonnement te kiezen of een nieuwe resource te maken. Dit komt overeen met het registreren van gemigreerd zonder dat het [migratie proces](luis-migration-authoring.md#what-is-migration) later hoeft te worden uitgevoerd. Alle gebruikers moeten worden gemigreerd op 2 november 2020.

    * Ga door met het gebruik van de eerste of proef versie. Met dit pad kunt u zich aanmelden bij LUIS met de Starter of de proef versie van de resource die wordt aangeboden zonder dat u resources hoeft te maken. Als u dit pad kiest, moet u uiteindelijk [uw account migreren](luis-migration-authoring.md#migration-steps) en uw toepassingen koppelen aan een ontwerp bron. Daarom wordt u aangeraden het pad te kiezen waar u doorgaat met uw Azure-resource.

    [Meer informatie over ontwerpen en starter Keys](luis-how-to-azure-subscription.md#luis-resources). Beide resources bieden u 1.000.000 gratis ontwerp transacties en 1000 gratis Voorspellings eindpunt transacties.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="Gedeeltelijk scherm: afbeelding om een type Language Understanding-ontwerp bron te kiezen.":::

1. Een bestaande ontwerp bron gebruiken

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="Resource voor ontwerpen kiezen":::

    Als u al LUIS hebt in uw abonnement en u deze hebt gekoppeld aan uw LUIS-account tijdens het aanmelden, kiest u de optie **bestaande ontwerp bron gebruiken** en geeft u de volgende informatie op:

    * **Tenant**: de tenant waaraan uw Azure-abonnement is gekoppeld. U kunt geen tenants van het bestaande venster wijzigen. U kunt tenants overschakelen door de meest rechtse avatar te selecteren, die uw initialen bevat in de bovenste balk.
    * **Abonnements naam** : het abonnement dat wordt gekoppeld aan de resource. Als u meer dan één abonnement bij uw Tenant hebt, selecteert u de gewenste versie in de vervolg keuzelijst.
    * **Resource naam** : de ontwerp resource waaraan u wilt koppelen.

    > [!Note]
    > Als de resource die u zoekt in de vervolg keuzelijst grijs wordt weer gegeven, betekent dit dat u zich hebt aangemeld bij een andere regionale Portal. [Het concept van regionale portals begrijpen](luis-reference-regions.md#luis-authoring-regions).

1. Een nieuwe ontwerp bron maken

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="Ontwerpresource maken":::

    Geef bij het **maken van een nieuwe ontwerpresource** de volgende informatie op:

    * **Tenant**: de tenant waaraan uw Azure-abonnement is gekoppeld. U kunt geen tenants van het bestaande venster wijzigen. U kunt tenants overschakelen door de meest rechtse avatar te selecteren, die uw initialen bevat in de bovenste balk.
    * **Resource naam** : een aangepaste naam die u kiest, die wordt gebruikt als onderdeel van de URL voor uw ontwerp transacties. De resource naam mag alleen alfanumerieke tekens bevatten, '-', en mag niet beginnen of eindigen met '-'. Als er andere symbolen in de naam zijn opgenomen, mislukt het maken van een resource.
    * **Abonnements naam** : het abonnement dat wordt gekoppeld aan de resource. Als u meer dan één abonnement bij uw Tenant hebt, selecteert u de gewenste versie in de vervolg keuzelijst.
    * **Resource groep** : een aangepaste naam voor de resource groep die u in uw abonnement hebt gekozen. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer. Als u momenteel geen resource groep in uw abonnement hebt, mag u er geen in de LUIS-portal maken. Ga naar [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) om er nog een te maken, gaat u naar Luis om door te gaan met het aanmeldings proces.

1. Nadat u het pad hebt gekozen, kan het enkele seconden duren voordat een teken met de tekst ' uw account is gemigreerd ' wordt weer gegeven. Voltooi door **door gaan** te selecteren.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="Ontwerp bron bevestigen":::

    > [!Note]
    > Als u een abonnement hebt en ten minste één bewerkings resource in de regio hetzelfde is als degene waarbij u zich aanmeldt in de portal, kunt u zich automatisch aanmelden bij LUIS gemigreerd en gekoppeld aan een resource zonder dat u moet kiezen welk pad moet worden gekozen.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Meld u aan met een gebruikers account dat niet is gekoppeld aan een Azure-abonnement

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai) en controleer of u akkoord gaat met de gebruiks voorwaarden.

1. Voltooi door **door gaan** te selecteren. U wordt automatisch aangemeld met een proef-of-start sleutel. Dit betekent dat u uiteindelijk [uw account moet migreren](luis-migration-authoring.md#migration-steps) en uw toepassingen kunt koppelen aan een ontwerp bron. Als u het migratie proces wilt uitvoeren, moet u zich aanmelden voor een [gratis proef versie van Azure](https://azure.microsoft.com/free/).

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="Geen abonnements scenario":::

## <a name="troubleshooting"></a>Problemen oplossen

* Als u een ontwerp bron maakt op basis van de Azure Portal in een andere regio dan de portal waarbij u zich aanmeldt, wordt de ontwerp bron grijs weer gegeven.
* Wanneer u een nieuwe resource maakt, moet u ervoor zorgen dat de resource naam alleen alfanumerieke tekens,-bevat en niet kan beginnen of eindigen met '-'. Als dat niet het geval is, mislukt de fout.
* Zorg ervoor dat u over de [juiste machtigingen voor uw abonnement beschikt om een Azure-resource te maken](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Als u niet over de juiste machtigingen beschikt, neemt u contact op met de beheerder van uw abonnement om u voldoende machtigingen te geven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [starten van een nieuwe app](luis-how-to-start-new-app.md)
