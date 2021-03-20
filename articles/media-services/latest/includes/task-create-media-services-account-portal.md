---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: 72857564a6b195353e7bf2a27c8369004d52d3f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95971341"
---
<!-- Use the portal to create a media services account. -->

## <a name="create-a-media-services-account"></a>Een Media Services-account kunt maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Klik op **+ Een resource maken** > **Media** > **Media Services**.
1. Voer bij **Een Media Services-account maken** de vereiste waarden in.

    | Naam | Beschrijving |
    | ---|---|
    |**Accountnaam**|Voer de naam in van het nieuwe Media Services-account. Voor de naam van een Media Services-account mogen alleen cijfers en kleine letters worden gebruikt. Spaties zijn niet toegestaan. De naam mag 3 tot 24 tekens lang zijn.|
    |**Abonnement**|Als u meer dan één abonnement hebt, selecteert u een uit de lijst Azure-abonnementen waartoe u toegang hebt.|
    |**Resourcegroep**|Selecteer de nieuwe of bestaande resource. Een resourcegroep is een verzameling resources met dezelfde levenscyclus, dezelfde machtigingen en hetzelfde beleid. Klik [hier](../../../azure-resource-manager/management/overview.md#resource-groups) voor meer informatie.|
    |**Locatie**|Selecteer de geografische regio die wordt gebruikt om de media en metagegevensrecords voor uw Media Services-account op te slaan. Deze regio wordt gebruikt om uw media te verwerken en te streamen. Alleen de beschikbare Media Services-regio's worden in de vervolgkeuzelijst weergegeven. |
    |**Opslagaccount**|Selecteer een opslagaccount om Blob Storage van de media-inhoud vanaf uw Media Services-account te leveren. U kunt een bestaand opslagaccount in dezelfde geografische regio als uw Media Services-account selecteren of u kunt een nieuw opslagaccount maken. Een nieuw opslagaccount wordt in dezelfde regio gemaakt. De regels voor opslagaccountnamen zijn hetzelfde als voor Media Services-accounts.<br/><br/>U kunt maar één **primaire** opslagaccount koppelen aan uw Media Services-account, maar een onbeperkt aantal **secundaire** opslagaccounts. U kunt de Azure Portal gebruiken om secundaire opslagaccounts toe te voegen. Raadpleeg [Azure Storage-accounts met Azure Media Services-accounts](../storage-account-concept.md) voor meer informatie.<br/><br/>Het Media Services-account en alle gekoppelde opslagaccounts moeten zich in hetzelfde Azure-abonnement bevinden. Het wordt sterk aangeraden opslagaccounts te gebruiken op dezelfde locatie als het Media Services-account om aanvullende kosten voor latentie en uitgaande data te vermijden.|
    |**Geavanceerde instellingen**| U kunt een account maken met een door het systeem beheerde identiteit door de radioknop **Door het systeem beheerd** te selecteren.  Als u voor deze optie kiest, kunt u door de klant beheerde sleutels gebruiken, of uw eigen sleutels (BYOK) en Media Services meenemen om vertrouwde opslag in te schakelen.  Raadpleeg [Uw eigen sleutel (door klant beheerde sleutels) meenemen met Media Services](../concept-use-customer-managed-keys-byok.md) voor meer informatie over door de klant beheerde sleutels. Daarnaast kunnen [beheerde identiteiten](../concept-managed-identities.md) ook worden ingeschakeld.

1. Selecteer **Vastmaken aan dashboard** om de voortgang van de implementatie van het account te bekijken.
1. Klik op **Maken** onder in het formulier.
