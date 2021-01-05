---
title: Beheerde identiteiten gebruiken voor toegang tot Event hub vanuit een Azure Stream Analytics-taak (preview-versie)
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren bij Azure Event Hubs invoer en uitvoer.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: ca27df7188c5edd1da94fc41707f6c25eb4034bf
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895135"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Beheerde identiteiten gebruiken voor toegang tot Event hub vanuit een Azure Stream Analytics-taak (preview-versie)

Azure Stream Analytics ondersteunt beheerde identiteits verificatie voor zowel invoer als uitvoer van Azure Event Hubs. Beheerde identiteiten elimineren de beperkingen van verificatie methoden op basis van gebruikers, zoals de nood zaak om opnieuw te verifiëren vanwege wachtwoord wijzigingen of gebruikers token verloop die elke 90 dagen worden uitgevoerd. Wanneer u de nood zaak om hand matig te verifiëren verwijdert, kunnen uw Stream Analytics-implementaties volledig worden geautomatiseerd.  

Een beheerde identiteit is een beheerde toepassing die is geregistreerd in Azure Active Directory die een bepaalde Stream Analytics taak vertegenwoordigt. De beheerde toepassing wordt gebruikt om te verifiëren bij een doel bron, met inbegrip van Event Hubs die zich achter een firewall of een virtueel netwerk (VNet) bevinden. Voor meer informatie over het overs laan van firewalls, Zie [toegang tot Azure Event hubs-naam ruimten toestaan via persoonlijke eind punten](../event-hubs/private-link-service.md#trusted-microsoft-services).

Dit artikel laat u zien hoe u beheerde identiteit kunt inschakelen voor een Event Hubs invoer of uitvoer van een Stream Analytics taak via de Azure Portal.Voordat u beheerde identiteit hebt ingeschakeld, moet u eerst een Stream Analytics-taak en Event hub-Resource hebben.

### <a name="limitation"></a>Beperking
Tijdens de preview-fase werkt de bemonsterings invoer van Event Hubs op Azure Portal niet wanneer beheerde identiteits verificatie wordt gebruikt.

## <a name="create-a-managedidentity"></a>Een beheerde identiteit maken  

Eerst maakt u een beheerde identiteit voor uw Azure Stream Analytics-taak.  

1. Open de Azure Stream Analytics-taak in de Azure Portal.  

1. Selecteer in het navigatie menu links **beheerde identiteit**   onder *configureren*. Schakel vervolgens het selectie vakje naast door **systeem toegewezen beheerde identiteit gebruiken** in   en selecteer **Opslaan**.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Door het systeem toegewezen beheerde identiteit":::  

1. Een service-principal voor de identiteit van de Stream Analytics-taak wordt gemaakt in Azure Active Directory. De levens cyclus van de zojuist gemaakte identiteit wordt beheerd door Azure. Wanneer de Stream Analytics taak wordt verwijderd, wordt de bijbehorende identiteit (de service-principal) automatisch verwijderd door Azure.  

   Wanneer u de configuratie opslaat, wordt de object-ID (OID) van de Service-Principal vermeld als de principal-ID zoals hieronder wordt weer gegeven:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="Principal-ID":::

   De service-principal heeft dezelfde naam als de Stream Analytics taak. Als de naam van uw taak bijvoorbeeld is  `MyASAJob` , is de naam van de Service-Principal ook  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>De Stream Analytics taak machtigingen verlenen voor toegang tot de Event hub

De service-principal die u hebt gemaakt, moet speciale machtigingen hebben voor de Event hub om de Stream Analytics-taak toegang te geven tot uw event hub met behulp van beheerde identiteit.

1. Ga naar **Access Control (IAM)** in uw event hub.

1. Selecteer **+ roltoewijzing toevoegen** en **toevoegen**.

1. Voer op de pagina **roltoewijzing toevoegen** de volgende opties in:

   |Parameter|Waarde|
   |---------|-----|
   |Rol|Eigenaar van Azure Event Hubs-gegevens|
   |Toegang toewijzen aan|Gebruiker, groep of Service-Principal|
   |Selecteer|Voer de naam van uw Stream Analytics-taak in|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Roltoewijzing toevoegen":::

1. Selecteer **Opslaan** en wacht even of Ja om de wijzigingen door te geven.

U kunt deze rol ook verlenen op het niveau van de Event hub-naam ruimte, waardoor de machtigingen op natuurlijke wijze worden door gegeven aan alle Event Hubs die hieronder worden gemaakt. Dat wil zeggen dat alle Event Hubs onder een naam ruimte kunnen worden gebruikt als een beheerde bron voor het verifiëren van identiteiten in uw Stream Analytics-taak.

## <a name="create-anevent-hub-input-or-output"></a>Een event hub-invoer of-uitvoer maken  

Nu u de beheerde identiteit hebt geconfigureerd, kunt u de Event hub-resource toevoegen als een invoer-of uitvoer taak voor uw Stream Analytics job.  

### <a name="add-the-event-hub-as-an-input"></a>De Event hub als invoer toevoegen 

1. Ga naar uw Stream Analytics-taak en navigeer naar de pagina **invoer** onder **taak topologie**.

1. Selecteer **Stream-invoer > Event hub toevoegen**. In het venster invoer eigenschappen zoekt u naar de Event hub en selecteert u **beheerde identiteit** in de vervolg keuzelijst *verificatie modus* .

1. Vul de overige eigenschappen in en selecteer **Opslaan**.

### <a name="add-the-event-hub-as-an-output"></a>Event hub toevoegen als uitvoer

1. Ga naar uw Stream Analytics-taak en navigeer naar de pagina **uitvoer** onder **taak topologie**.

1. Selecteer **> Event hub toevoegen**. In het venster uitvoer eigenschappen zoekt u naar de Event hub en selecteert u **beheerde identiteit** in de vervolg keuzelijst *verificatie modus* .

1. Vul de overige eigenschappen in en selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

* [Event Hubs uitvoer van Azure Stream Analytics](event-hubs-output.md)
* [Gegevens streamen vanuit Event Hubs](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
