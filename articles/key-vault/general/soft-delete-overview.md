---
title: Zacht verwijderen Azure Key Vault | Microsoft Docs
description: Met zacht-verwijderen in Azure Key Vault kunt u verwijderde sleutel kluizen en sleutel kluis objecten herstellen, zoals sleutels, geheimen en certificaten.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 12/15/2020
ms.openlocfilehash: 68c690b9cbd2028f73492550adbe86111f9ec3a7
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257943"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault: overzicht van voorlopig verwijderen

> [!IMPORTANT]
> U moet voorlopig verwijderen direct inschakelen voor uw sleutel kluizen. De mogelijkheid om te voor komen dat de Soft-software wordt verwijderd, zal binnenkort worden afgeschaft. Bekijk [hier](soft-delete-change.md) de volledige Details

Met de functie voor het tijdelijk verwijderen van Key Vault kunt u de verwijderde kluizen en verwijderde sleutel kluis objecten (bijvoorbeeld sleutels, geheimen, certificaten), ook wel zacht verwijderen genoemd. We behandelen de volgende scenario's: deze beveiliging biedt de volgende beveiligings problemen:

- Zodra een geheim, sleutel, certificaat of sleutel kluis wordt verwijderd, blijft de configuratie onherstelbaar voor een Configureer bare periode van 7 tot 90 kalender dagen. Als er geen configuratie is opgegeven, wordt de standaard herstelperiode ingesteld op 90 dagen. Dit biedt gebruikers voldoende tijd om een onbedoeld geheim te verwijderen en te reageren.
- Er moeten twee bewerkingen worden uitgevoerd om een geheim permanent te verwijderen. Eerst moet een gebruiker het object verwijderen, waardoor het het voorlopig verwijderde staat. Ten tweede moet een gebruiker het object leegmaken met de status zacht verwijderd. Voor de opschoon bewerking zijn aanvullende machtigingen voor toegangs beleid vereist. Deze extra beveiligingen verminderen het risico dat een gebruiker per ongeluk een geheim of een sleutel kluis verwijdert.  
- Als u een geheim wilt verwijderen in de tijdelijke status, moet aan een service-principal een extra machtiging voor het leegmaken van het toegangs beleid worden verleend. De machtiging toegangs beleid opschonen wordt niet standaard verleend aan een service-principal met inbegrip van sleutel kluis-en abonnements eigenaren en moet opzettelijk worden ingesteld. Door een machtiging voor een verhoogd toegangs beleid te vereisen voor het leegmaken van een voorlopig verwijderd geheim, vermindert dit de kans dat een geheim per ongeluk wordt verwijderd.

## <a name="supporting-interfaces"></a>Ondersteunende interfaces

De functie voor voorlopig verwijderen is beschikbaar via de [rest API](/rest/api/keyvault/), de [Azure cli](./key-vault-recovery.md), [Azure PowerShell](./key-vault-recovery.md)en [.net/C#](/dotnet/api/microsoft.azure.keyvault?view=azure-dotnet) -interfaces, evenals arm- [sjablonen](/azure/templates/microsoft.keyvault/2019-09-01/vaults).

## <a name="scenarios"></a>Scenario's

Azure-sleutel kluizen zijn bijgehouden resources die worden beheerd door Azure Resource Manager. Azure Resource Manager geeft ook een goed gedefinieerd gedrag op voor verwijdering, waardoor een geslaagde Verwijder bewerking ertoe moet leiden dat de bron niet meer toegankelijk is. De functie voor voorlopig verwijderen behandelt het herstel van het verwijderde object, of het verwijderen per ongeluk of opzettelijk is geslaagd.

1. In het typische scenario kan een gebruiker per ongeluk een sleutel kluis of een sleutel kluis-object hebben verwijderd. Als de sleutel kluis of het sleutel kluis object voor een vooraf bepaalde periode kan worden hersteld, kan de gebruiker de verwijdering ongedaan maken en de gegevens herstellen.

2. In een ander scenario kan een Rogue-gebruiker proberen een sleutel kluis of een sleutel kluis object te verwijderen, zoals een sleutel binnen een kluis, om een bedrijfs onderbreking te veroorzaken. Het verwijderen van de sleutel kluis of het sleutel kluis object van de daad werkelijke verwijdering van de onderliggende gegevens kan worden gebruikt als een veiligheids maatregel, bijvoorbeeld door de machtigingen voor het verwijderen van gegevens te beperken tot een andere vertrouwde rol. Deze benadering vereist een quorum voor een bewerking die anders kan leiden tot een onmiddellijk verlies van gegevens.

### <a name="soft-delete-behavior"></a>Gedrag bij voorlopig verwijderen

Als de functie voor voorlopig verwijderen is ingeschakeld, worden de resources die zijn gemarkeerd als verwijderde resources, gedurende een opgegeven periode (standaard 90 dagen) bewaard. De service biedt verder een mechanisme voor het herstellen van het verwijderde object, waardoor het verwijderen in feite ongedaan wordt.

Bij het maken van een nieuwe sleutel kluis is voorlopig verwijderen standaard ingeschakeld. U kunt een sleutel kluis maken zonder zacht te verwijderen via de [Azure cli](./key-vault-recovery.md) of [Azure PowerShell](./key-vault-recovery.md). Wanneer de functie voor het voorlopig verwijderen van een sleutel kluis is ingeschakeld, kan deze niet worden uitgeschakeld

De standaard retentie periode is 90 dagen, maar tijdens het maken van de sleutel kluis is het mogelijk om het interval voor het Bewaar beleid in te stellen op een waarde tussen 7 en 90 dagen via de Azure Portal. Het bewaarbeleid voor de beveiliging tegen opschonen gebruikt hetzelfde interval. Zodra het is ingesteld, kan het interval voor het bewaarbeleid niet worden gewijzigd.

U kunt de naam van een sleutel kluis die zacht is verwijderd, niet opnieuw gebruiken totdat de Bewaar periode is verstreken.

### <a name="purge-protection"></a>Beveiliging opschonen

Het opschonen van de beveiliging is een optioneel Key Vault gedrag en is **standaard niet ingeschakeld**. Het opschonen van de beveiliging kan alleen worden ingeschakeld wanneer het voorlopig verwijderen is ingeschakeld.  Deze kan worden ingeschakeld via [cli](./key-vault-recovery.md?tabs=azure-cli) of [Power shell](./key-vault-recovery.md?tabs=azure-powershell).

Wanneer het leegmaken van de beveiliging is ingeschakeld, kan een kluis of een object in de verwijderde status pas worden verwijderd nadat de Bewaar periode is verstreken. Voorlopig verwijderde kluizen en objecten kunnen nog steeds worden hersteld, zodat het Bewaar beleid wordt gevolgd.

De standaard retentie periode is 90 dagen, maar het is mogelijk om het interval voor het Bewaar beleid in te stellen op een waarde van 7 tot 90 dagen via de Azure Portal. Zodra het interval voor het Bewaar beleid is ingesteld en opgeslagen, kan het niet meer worden gewijzigd voor die kluis.

### <a name="permitted-purge"></a>Toegestaan opschonen

Permanent verwijderen, opschonen, een sleutel kluis is mogelijk via een POST-bewerking op de proxy bron en vereist speciale bevoegdheden. Over het algemeen kan alleen de eigenaar van het abonnement een sleutel kluis opschonen. Met de POST-bewerking wordt de onmiddellijke en onherstelbare verwijdering van die kluis geactiveerd. 

Uitzonde ringen zijn:
- Wanneer het Azure-abonnement is gemarkeerd als niet- *verwijderd*. In dit geval kan de service de daad werkelijke verwijdering vervolgens uitvoeren, en dit als een gepland proces. 
- Wanneer de `--enable-purge-protection flag` is ingeschakeld op de kluis zelf. In dit geval wacht Key Vault 90 dagen vanaf het moment dat het oorspronkelijke geheime object werd gemarkeerd voor verwijdering, zodat het object definitief kan worden verwijderd.

Zie [How to use key Vault soft-delete with cli](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) (Engelstalig) voor meer informatie over het gebruik van een sleutel kluis of [het gebruiken van Key Vault soft-delete met Power shell: een sleutel kluis opschonen](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell).

### <a name="key-vault-recovery"></a>Herstel van sleutel kluis

Wanneer u een sleutel kluis verwijdert, maakt de service een proxy resource onder het abonnement en voegt hij voldoende meta gegevens toe voor herstel. De proxy resource is een opgeslagen object dat beschikbaar is op dezelfde locatie als de verwijderde sleutel kluis. 

### <a name="key-vault-object-recovery"></a>Sleutel kluis-object herstel

Wanneer u een sleutel kluis-object verwijdert, zoals een sleutel, plaatst de service het object in een verwijderde staat, waardoor het ontoegankelijk wordt voor alle ophaal bewerkingen. In deze status kan het sleutel kluis-object alleen worden weer gegeven, hersteld of geforceerd/permanent worden verwijderd. Als u de objecten wilt weer geven, gebruikt u de Azure CLI- `az keyvault key list-deleted` opdracht (zoals beschreven in het [gebruik van Key Vault Soft-verwijderingen met CLI](./key-vault-recovery.md)) of de `-InRemovedState` para meter Azure PowerShell (zoals beschreven in [het gebruik van Key Vault zacht verwijderen met Power shell](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)).  

Tegelijkertijd plant Key Vault de verwijdering van de onderliggende gegevens die overeenkomen met de verwijderde sleutel kluis of het sleutel kluis-object voor uitvoering na een vooraf vastgesteld Bewaar interval. De DNS-record die overeenkomt met de kluis, wordt ook bewaard voor de duur van de Bewaar periode.

### <a name="soft-delete-retention-period"></a>Tijdelijke Bewaar periode voor verwijderen

Zacht verwijderde resources worden gedurende een bepaalde periode (90 dagen) bewaard. Het volgende is van toepassing tijdens het Bewaar interval voor voorlopig verwijderen:

- U kunt alle sleutel kluizen en sleutel kluis objecten in de verzachtte verwijderings status van uw abonnement vermelden, evenals de toegang voor het verwijderen en herstellen van de gegevens.
  - Alleen gebruikers met speciale machtigingen kunnen verwijderde kluizen weer geven. We raden aan dat onze gebruikers een aangepaste rol maken met deze speciale machtigingen voor het afhandelen van verwijderde kluizen.
- Een sleutel kluis met dezelfde naam kan niet op dezelfde locatie worden gemaakt. een sleutel kluis object kan niet worden gemaakt in een bepaalde kluis als deze sleutel kluis een object met dezelfde naam en een verwijderde status bevat.
- Alleen een specifiek bevoegde gebruiker kan een sleutel kluis of een sleutel kluis object herstellen door een herstel opdracht uit te geven voor de bijbehorende proxy resource.
  - De gebruiker, het lid van de aangepaste rol, met de bevoegdheid om een sleutel kluis te maken onder de resource groep kan de kluis herstellen.
- Alleen een specifiek bevoegde gebruiker kan een sleutel kluis of een sleutel kluis object verwijderen door een verwijder opdracht uit te geven voor de bijbehorende proxy resource.

Tenzij een sleutel kluis of sleutel kluis object wordt hersteld, wordt aan het einde van de Bewaar termijn de service een schone verwijdering van de tijdelijke, verwijderde sleutel kluis of het sleutel kluis-object en de bijbehorende inhoud uitgevoerd. Het verwijderen van resources kan niet opnieuw worden gepland.

### <a name="billing-implications"></a>Facturerings implicaties

In het algemeen zijn er slechts twee bewerkingen mogelijk: ' opschonen ' en ' herstellen ' wanneer een object (een sleutel kluis of een sleutel of een geheim) de status verwijderd heeft. Alle andere bewerkingen zullen mislukken. Daarom kunnen er geen bewerkingen worden uitgevoerd, zelfs als het object bestaat en er geen gebruik wordt gemaakt, dus geen factuur. Er zijn echter de volgende uitzonde ringen:

- de acties opschonen en herstellen tellen mee bij normale sleutel kluis bewerkingen en worden gefactureerd.
- Als het object een HSM-sleutel is, is de kosten voor de sleutel van de HSM Protected Key-versie per maand van toepassing als er in de afgelopen 30 dagen een sleutel versie is gebruikt. Daarna kunnen er geen bewerkingen worden uitgevoerd, omdat het object zich in de verwijderde status bevindt, zodat er geen kosten in rekening worden gebracht.

## <a name="next-steps"></a>Volgende stappen

De volgende twee hand leidingen bieden de primaire gebruiks scenario's voor het gebruik van voorlopig verwijderen.

- [Key Vault Soft-verwijdering gebruiken met de portal](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal)
- [De Key Vault-functie voor voorlopig verwijderen gebruiken met PowerShell](./key-vault-recovery.md) 
- [De Key Vault-functie voor voorlopig verwijderen gebruiken met CLI](./key-vault-recovery.md)
