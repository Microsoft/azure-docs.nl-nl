---
title: Azure Key Vault voor het verwijderen van | Microsoft Docs
description: Met de functie voor Azure Key Vault kunt u verwijderde sleutelkluizen en sleutelkluisobjecten, zoals sleutels, geheimen en certificaten, herstellen.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.openlocfilehash: 52cd7742f3c6961350f907cde8ffe19235cff9b8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753250"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Azure Key Vault: overzicht van voorlopig verwijderen

> [!IMPORTANT]
> U moet de functie voor het verwijderen van gegevens direct inschakelen voor uw sleutelkluizen. De mogelijkheid om u af te zien van de mogelijkheid om te verwijderen, wordt binnenkort afgeschaft. Bekijk hier de volledige [details](soft-delete-change.md)

> [!IMPORTANT]
> Met de functie voor het verwijderen van een kluis worden instellingen voor het verwijderen van Key Vault-services, dat wil zeggen Azure RBAC-roltoewijzingen, Event Grid uitgevoerd. Na het herstellen van de Key Vault instellingen voor geïntegreerde services handmatig opnieuw worden gemaakt. 

met Key Vault functie voor het verwijderen van de sleutelkluis kunnen de verwijderde kluizen en verwijderde sleutelkluisobjecten (bijvoorbeeld sleutels, geheimen, certificaten) worden hersteld, ook wel 'soft-delete' genoemd. In het bijzonder hebben we het over de volgende scenario's: Deze beveiliging biedt de volgende beveiligingen:

- Zodra een geheim, sleutel, certificaat of sleutelkluis is verwijderd, blijft deze herstelbaar voor een configureerbare periode van 7 tot 90 kalenderdagen. Als er geen configuratie is opgegeven, wordt de standaard herstelperiode ingesteld op 90 dagen. Dit biedt gebruikers voldoende tijd om een onbedoeld geheim te verwijderen en te reageren.
- Er moeten twee bewerkingen worden uitgevoerd om een geheim permanent te verwijderen. Eerst moet een gebruiker het object verwijderen, waardoor het in de status Van de soft verwijderd wordt. Ten tweede moet een gebruiker het object met de status 'soft-leted' leeg maken. Voor de opstingsbewerking zijn aanvullende machtigingen voor toegangsbeleid vereist. Deze extra beveiligingen verminderen het risico dat een gebruiker per ongeluk of met kwaadwillende bedoelingen een geheim of een sleutelkluis kan verwijderen.  
- Als u een geheim met de status 'soft-leted' wilt ops leeg maken, moet aan een service-principal een extra machtiging voor toegangsbeleid voor opspen worden verleend. De machtiging voor toegangsbeleid voor opsluizen wordt niet standaard verleend aan een service-principal, inclusief sleutelkluis- en abonnementseigenaren, en moet bewust worden ingesteld. Door een machtiging met verhoogde toegang te vereisen voor het leeg maken van een soft-leted geheim, wordt de kans verkleind dat een geheim per ongeluk wordt verwijderd.

## <a name="supporting-interfaces"></a>Ondersteunende interfaces

De functie voor het verwijderen van soft-REST API is beschikbaar via de [interfaces REST API](/rest/api/keyvault/), De [Azure CLI,](./key-vault-recovery.md) [Azure PowerShell en](./key-vault-recovery.md) [.NET/C#,](/dotnet/api/microsoft.azure.keyvault) evenals [ARM-sjablonen.](/azure/templates/microsoft.keyvault/2019-09-01/vaults)

## <a name="scenarios"></a>Scenario's

Azure Key Vaults zijn bij te houden resources die worden beheerd door Azure Resource Manager. Azure Resource Manager geeft ook een goed gedefinieerd gedrag voor verwijdering op, wat vereist dat een geslaagde DELETE-bewerking ertoe moet leiden dat die resource niet meer toegankelijk is. Met de functie voor het verwijderen van een zachte verwijdering wordt het herstel van het verwijderde object vereend, ongeacht of de verwijdering per ongeluk of opzettelijk is gemaakt.

1. In het gebruikelijke scenario heeft een gebruiker mogelijk per ongeluk een sleutelkluis of een sleutelkluisobject verwijderd; Als dat sleutelkluis- of sleutelkluisobject voor een vooraf bepaalde periode kan worden hersteld, kan de gebruiker de verwijdering ongedaan maken en de gegevens herstellen.

2. In een ander scenario kan een rogue gebruiker proberen een sleutelkluis of een sleutelkluisobject, zoals een sleutel in een kluis, te verwijderen om een bedrijfsonderbreking te veroorzaken. Het verwijderen van de sleutelkluis of het sleutelkluisobject van de daadwerkelijke verwijdering van de onderliggende gegevens kan als veiligheidsmaatregel worden gebruikt door bijvoorbeeld machtigingen voor het verwijderen van gegevens te beperken tot een andere, vertrouwde rol. Voor deze benadering is quorum vereist voor een bewerking die anders kan leiden tot een onmiddellijk gegevensverlies.

### <a name="soft-delete-behavior"></a>Gedrag bij voorlopig verwijderen

Wanneer de functie voor het verwijderen van de functie voor verwijderen is ingeschakeld, worden resources die zijn gemarkeerd als verwijderde resources bewaard voor een opgegeven periode (standaard 90 dagen). De service biedt verder een mechanisme voor het herstellen van het verwijderde object, waarmee de verwijdering in feite ongedaan wordt gemaakt.

Bij het maken van een nieuwe sleutelkluis is soft-delete standaard ingeschakeld. U kunt een sleutelkluis maken zonder deze te verwijderen via [de Azure CLI](./key-vault-recovery.md) of [Azure PowerShell.](./key-vault-recovery.md) Zodra de functie voor het verwijderen van een sleutelkluis is ingeschakeld, kan deze niet meer worden uitgeschakeld

De standaardretentieperiode is 90 dagen, maar tijdens het maken van de sleutelkluis is het mogelijk om het retentiebeleidsinterval in te stellen op een waarde van 7 tot 90 dagen tot de Azure Portal. Het bewaarbeleid voor de beveiliging tegen opschonen gebruikt hetzelfde interval. Zodra het is ingesteld, kan het interval voor het bewaarbeleid niet worden gewijzigd.

U kunt de naam van een sleutelkluis die is verwijderd, pas opnieuw gebruiken als de retentieperiode is verstreken.

### <a name="purge-protection"></a>Beveiliging tegen opskeren

Beveiliging tegen ops en Key Vault is **optioneel en is niet standaard ingeschakeld.** Beveiliging tegen opsisten kan alleen worden ingeschakeld zodra de functie voor het verwijderen van de functie voor het verwijderen van gegevens is ingeschakeld.  Deze kan worden ingeschakeld via [CLI](./key-vault-recovery.md?tabs=azure-cli) of [PowerShell.](./key-vault-recovery.md?tabs=azure-powershell)

Wanneer beveiliging tegen opsluizen is aangegaan, kan een kluis of een object met de verwijderde status pas worden gewist als de bewaarperiode is verstreken. Soft-leted kluizen en objecten kunnen nog steeds worden hersteld, zodat het bewaarbeleid wordt gevolgd.

De standaardretentieperiode is 90 dagen, maar het is mogelijk om het retentiebeleidsinterval in te stellen op een waarde van 7 tot 90 dagen tot de Azure Portal. Zodra het retentiebeleidsinterval is ingesteld en opgeslagen, kan het niet meer worden gewijzigd voor die kluis.

### <a name="permitted-purge"></a>Toegestane opsting

Een sleutelkluis kan permanent worden verwijderd en verwijderd via een POST-bewerking op de proxyresource en vereist speciale bevoegdheden. Over het algemeen kan alleen de eigenaar van het abonnement een sleutelkluis opsluizen. De POST-bewerking activeert de onmiddellijke en onherstelbare verwijdering van die kluis. 

Uitzonderingen zijn:
- Wanneer het Azure-abonnement is gemarkeerd als *niet-verdeerbaar.* In dit geval kan alleen de service de daadwerkelijke verwijdering uitvoeren en doet dit als een gepland proces. 
- Wanneer de `--enable-purge-protection flag` is ingeschakeld voor de kluis zelf. In dit geval wacht Key Vault 90 dagen vanaf het moment dat het oorspronkelijke geheime object is gemarkeerd voor verwijdering om het object permanent te verwijderen.

Zie How [to use Key Vault soft-delete with CLI: Purging a key vault](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) (Een sleutelkluis opsluizen) of How [to use Key Vault soft-delete with PowerShell: Purging a key vault](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)(Een sleutelkluis opsluizen) voor stappen.

### <a name="key-vault-recovery"></a>Key Vault-herstel

Bij het verwijderen van een sleutelkluis maakt de service een proxyresource onder het abonnement, waardoor er voldoende metagegevens voor herstel worden toegevoegd. De proxyresource is een opgeslagen object dat beschikbaar is op dezelfde locatie als de verwijderde sleutelkluis. 

### <a name="key-vault-object-recovery"></a>Herstel van Key Vault-objecten

Bij het verwijderen van een sleutelkluisobject, zoals een sleutel, zal de service het object in een verwijderde status plaatsen, waardoor het niet toegankelijk is voor ophaalbewerkingen. In deze status kan het sleutelkluisobject alleen worden weergegeven, hersteld of geforceerd/permanent worden verwijderd. Als u de objecten wilt weergeven, gebruikt u de Azure CLI-opdracht (zoals beschreven in How to use Key Vault soft-delete with CLI (Het gebruik van `az keyvault key list-deleted` [Key Vault soft-delete](./key-vault-recovery.md)met CLI) of de parameter Azure PowerShell (zoals beschreven in `-InRemovedState` How to use Key Vault [soft-delete with PowerShell (Key Vault soft-delete](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)gebruiken met PowerShell).  

Tegelijkertijd plannen Key Vault het verwijderen van de onderliggende gegevens die overeenkomen met de verwijderde sleutelkluis of het sleutelkluisobject voor uitvoering na een vooraf bepaald retentie-interval. De DNS-record die overeenkomt met de kluis wordt ook bewaard voor de duur van de retentieperiode.

### <a name="soft-delete-retention-period"></a>Retentieperiode voor het verwijderen van soft-delete

Soft-deleted resources worden bewaard voor een bepaalde periode, 90 dagen. Tijdens de retentieperiode voor het verwijderen van de soft-delete is het volgende van toepassing:

- U kunt alle sleutelkluizen en sleutelkluisobjecten in de status voor het verwijderen van de sleutel voor uw abonnement, evenals informatie over het verwijderen van toegang en het herstel ervan, in de lijst plaatsen.
  - Alleen gebruikers met speciale machtigingen kunnen verwijderde kluizen op een lijst zetten. Het is raadzaam dat onze gebruikers een aangepaste rol maken met deze speciale machtigingen voor het verwerken van verwijderde kluizen.
- Een sleutelkluis met dezelfde naam kan niet op dezelfde locatie worden gemaakt; Een sleutelkluisobject kan niet worden gemaakt in een bepaalde kluis als die sleutelkluis een object met dezelfde naam bevat en dat de status Verwijderd heeft.
- Alleen een specifiek bevoegde gebruiker kan een sleutelkluis of sleutelkluisobject herstellen door een herstelopdracht uit te geven op de bijbehorende proxyresource.
  - De gebruiker, lid van de aangepaste rol, die de bevoegdheid heeft om een sleutelkluis onder de resourcegroep te maken, kan de kluis herstellen.
- Alleen een specifiek bevoegde gebruiker kan een sleutelkluis of sleutelkluisobject gecibeerd verwijderen door een verwijderopdracht uit te geven op de bijbehorende proxyresource.

Tenzij een sleutelkluis- of sleutelkluisobject wordt hersteld, voert de service aan het einde van het retentie-interval een opslofing uit van het sleutelkluis- of sleutelkluisobject en de inhoud ervan. Het verwijderen van resources kan niet opnieuw worden achterstallig.

### <a name="billing-implications"></a>Gevolgen voor facturering

Als een object (een sleutelkluis, sleutel of geheim) de status Verwijderd heeft, zijn er over het algemeen slechts twee bewerkingen mogelijk: 'leeg maken' en 'herstellen'. Alle andere bewerkingen mislukken. Dus zelfs als het object bestaat, kunnen er geen bewerkingen worden uitgevoerd en wordt er dus geen gebruik uitgevoerd, dus geen factuur. Er zijn echter de volgende uitzonderingen:

- De acties 'opsluizen' en 'herstellen' tellen mee voor normale sleutelkluisbewerkingen en worden gefactureerd.
- Als het object een HSM-sleutel is, worden de kosten per sleutelversie per maand in rekening brengen als er in de afgelopen 30 dagen een sleutelversie is gebruikt. Daarna kunnen er geen bewerkingen meer worden uitgevoerd, omdat het object de status Verwijderd heeft. Er worden dus geen kosten in rekening brengen.

## <a name="next-steps"></a>Volgende stappen

De volgende twee handleidingen bieden de primaire gebruiksscenario's voor het gebruik van soft-delete.

- [Een Key Vault portal gebruiken](./key-vault-recovery.md?tabs=azure-portal)
- [De Key Vault-functie voor voorlopig verwijderen gebruiken met PowerShell](./key-vault-recovery.md) 
- [De Key Vault-functie voor voorlopig verwijderen gebruiken met CLI](./key-vault-recovery.md)
