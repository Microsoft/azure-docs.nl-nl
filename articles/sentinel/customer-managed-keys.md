---
title: Door de klant beheerde sleutels instellen in azure Sentinel | Microsoft Docs
description: Meer informatie over het instellen van door de klant beheerde sleutels (CMK) in azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: bd85936c86656a8ae59a04ccdf53a62bd852368f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655269"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Door de klant beheerde sleutel met Azure-Sentinel instellen

Dit artikel bevat achtergrond informatie en stappen voor het configureren van een door de klant beheerde sleutel (CMK) voor Azure Sentinel. CMK maakt het mogelijk dat alle gegevens die worden opgeslagen of verzonden naar Azure Sentinel, worden versleuteld in alle relevante opslag resources met een Azure Key Vault sleutel die u hebt gemaakt of waarvan u eigenaar bent.

> [!NOTE]
> - De Azure Sentinel CMK-mogelijkheid wordt alleen door gegeven aan **nieuwe klanten**.
>
> - Toegang tot deze mogelijkheid wordt bepaald door de Azure-functie registratie. U kunt toegang aanvragen door contact op te nemen azuresentinelCMK@microsoft.com . Aanvragen in behandeling worden goedgekeurd volgens de beschik bare capaciteit.
>
> - De CMK-mogelijkheid is alleen beschikbaar voor klanten die 1 TB per dag of langer verzenden. U ontvangt informatie over aanvullende prijzen wanneer u van toepassing bent op micro soft om CMK in te richten op uw Azure-abonnement. Meer informatie over [log Analytics prijzen](../azure-monitor/platform/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="how-cmk-works"></a>Hoe CMK werkt 

De Azure Sentinel-oplossing maakt gebruik van verschillende opslag bronnen voor logboek verzameling en-functies, waaronder Log Analytics en anderen. Als onderdeel van de configuratie van de Azure Sentinel CMK moet u ook de CMK-instellingen voor de gerelateerde opslag resources configureren. Gegevens die zijn opgeslagen in andere opslag bronnen dan Log Analytics, worden ook versleuteld.

Meer informatie over [CMK](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-overview).

> [!NOTE]
> Als u CMK inschakelt op de Sentinel van Azure, wordt een open bare preview-functie die geen ondersteuning biedt voor CMK, niet ingeschakeld.

## <a name="enable-cmk"></a>CMK inschakelen 

Voer de volgende stappen uit om CMK in te richten: 

1.  Maak een Azure Key Vault en sla de sleutel op.

2.  Schakel CMK in op uw Log Analytics-werk ruimte.

3.  Registreer u voor Cosmos DB.

4.  Een toegangs beleid toevoegen aan uw Azure Key Vault-exemplaar.

5.  Schakel CMK in bij Azure Sentinel.

6.  Azure Sentinel inschakelen.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>STAP 1: een Azure Key Vault maken en een sleutel opslaan

1.  [Maak Azure Key Vault resource](/azure-stack/user/azure-stack-key-vault-manage-portal)en Genereer of importeer een sleutel die moet worden gebruikt voor gegevens versleuteling.
    > [!NOTE]
    >  Azure Key Vault moet worden geconfigureerd als herstel bare voor het beveiligen van uw sleutel en de toegang.

1.  [Herstel opties inschakelen:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Zorg ervoor dat [zacht verwijderen](../key-vault/general/soft-delete-overview.md) is ingeschakeld.

    -   Schakel [beveiliging opschonen](../key-vault/general/soft-delete-overview.md#purge-protection) in voor beveiliging tegen geforceerde verwijdering van het geheim of de kluis, zelfs na het zacht verwijderen.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>STAP 2: CMK inschakelen in uw Log Analytics-werk ruimte

Volg de instructies in [Azure monitor door de klant beheerde sleutel configuratie](../azure-monitor/platform/customer-managed-keys.md) om een CMK-werk ruimte te maken die wordt gebruikt als de Azure Sentinel-werk ruimte in de volgende stappen.

### <a name="step-3-register-for-cosmos-db"></a>STAP 3: registreren voor Cosmos DB

Azure Sentinel werkt met Cosmos DB als een extra opslag resource. Zorg ervoor dat u zich registreert bij Cosmos DB.

Volg de Cosmos DB instructie voor [het registreren van de Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) resource provider voor uw Azure-abonnement.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>STAP 4: een toegangs beleid toevoegen aan uw Azure Key Vault-exemplaar

Zorg ervoor dat u toegang toevoegt van Cosmos DB aan uw Azure Key Vault-exemplaar. Volg de Cosmos DB instructie om [een toegangs beleid toe te voegen aan uw Azure Key Vault-exemplaar](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) met Azure Cosmos DB principal.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>STAP 5: CMK inschakelen in azure Sentinel

De Azure Sentinel CMK-mogelijkheid wordt alleen aan nieuwe klanten gegeven nadat u rechtstreeks toegang hebt gekregen vanuit de Azure-product groep. Gebruik uw contact personen bij micro soft om goed keuring te ontvangen van het Azure Sentinel-team om CMK in uw oplossing in te scha kelen.

Nadat u goed keuring krijgt, wordt u gevraagd om de volgende informatie op te geven om de CMK-functie in te scha kelen.

-  Werk ruimte-ID waarop u CMK wilt inschakelen

-  Key Vault URL: de sleutel-id van de sleutel kopiëren naar de laatste slash:  
    

    ![sleutel-id](./media/customer-managed-keys/key-identifier.png)

    Het Azure Sentinel-team schakelt de Azure Sentinel CMK-functie in voor uw beschik bare werk ruimte.

-  Verificatie van het Azure Sentinel-product team dat u hebt goedgekeurd om deze functie te gebruiken. U moet dit hebben voordat u kunt door gaan.

### <a name="step-6-enable-azure-sentinel"></a>STAP 6: Azure Sentinel inschakelen


Ga naar de Azure Portal en schakel Azure Sentinel in op de werk ruimte waarop u CMK hebt ingesteld. Zie [Azure Sentinel-onboarding](quickstart-onboard.md)voor meer informatie.

## <a name="key-encryption-key-revocation-or-deletion"></a>Sleutel voor het intrekken of verwijderen van sleutels versleutelen


In het geval dat de sleutel versleutelings sleutel door een gebruiker wordt ingetrokken door deze te verwijderen of door de toegang tot Azure-Sentinel te verwijderen binnen één uur, zal Azure Sentinel de wijziging naleven en zich gedragen alsof de gegevens niet meer beschikbaar zijn. Op dit moment wordt een bewerking uitgevoerd die gebruikmaakt van permanente opslag resources, zoals gegevens opname, blijvende configuratie wijzigingen en het maken van incidenten, voor komen. Eerder opgeslagen gegevens worden niet verwijderd, maar blijven ontoegankelijk. Ontoegankelijke gegevens vallen onder het beleid voor gegevens behoud en worden verwijderd overeenkomstig dat beleid.

De enige bewerking die mogelijk is nadat de versleutelings sleutel is ingetrokken of verwijderd, is account verwijdering.

Als de toegang na het intrekken wordt hersteld, wordt de toegang tot de gegevens binnen een uur hersteld door Azure Sentinel.

Zie [Azure monitor CMK intrekken](../azure-monitor/platform/customer-managed-keys.md#key-revocation)voor meer informatie over hoe dit werkt in azure monitor.

## <a name="key-encryption-key-rotation"></a>Sleutel versleutelings sleutel draaien


Azure Sentinel en Log Analytics bieden ondersteuning voor sleutel rotatie. Wanneer een gebruiker een sleutel rotatie uitvoert in Key Vault, ondersteunt Azure Sentinel de nieuwe sleutel binnen een uur.

In Key Vault kunt u een nieuwe versie van de sleutel draaiing maken.

![sleutel rotatie](./media/customer-managed-keys/key-rotation.png)

U kunt de vorige versie van de sleutel na 24 uur uitschakelen, of nadat de Azure Key Vault controle logboeken geen activiteit meer weer geven die gebruikmaakt van de vorige versie.

Als u dezelfde sleutel gebruikt in azure Sentinel en in Log Analytics, is het nood zakelijk om een sleutel rotatie uit te voeren. u moet de cluster bron in Log Analytics expliciet bijwerken met de nieuwe Azure Key Vault sleutel versie. Zie [Azure monitor CMK Rotation](../azure-monitor/platform/customer-managed-keys.md#key-rotation)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u een door de klant beheerde sleutel instelt in azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.