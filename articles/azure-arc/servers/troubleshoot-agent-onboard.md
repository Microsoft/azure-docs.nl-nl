---
title: Verbindingsproblemen Azure Arc servers met agent oplossen
description: In dit artikel wordt beschreven hoe u problemen met de Connected Machine-agent kunt oplossen die zich voordoen met Azure Arc servers die verbinding willen maken met de service.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497996"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Verbindingsproblemen Azure Arc servers met agent oplossen

Dit artikel bevat informatie over het oplossen van problemen die zich kunnen voordoen tijdens het configureren van de Azure Arc servers connected machine agent voor Windows of Linux. Zowel de interactieve installatiemethoden als de installatiemethoden op schaal bij het configureren van de verbinding met de service zijn opgenomen. Zie overzicht van [Arc-servers](./overview.md)voor algemene informatie.

## <a name="agent-error-codes"></a>Agentfoutcodes

Als er een foutbericht wordt weergegeven bij het configureren van de Azure Arc serveragent, kan de volgende tabel u helpen bij het identificeren van de waarschijnlijke oorzaak en voorgestelde stappen om uw probleem op te lossen. U hebt de foutcode ('0000' kan elk 4-cijferig getal zijn) nodig die wordt afgedrukt naar de console of `AZCM0000` scriptuitvoer om door te gaan.

| Foutcode | Waarschijnlijke oorzaak | Voorgesteld herstel |
|------------|----------------|-----------------------|
| AZCM0000 | De actie is geslaagd | N.v.t. |
| AZCM0001 | Er is een onbekende fout opgetreden | Contact opnemen Microsoft-ondersteuning voor meer hulp |
| AZCM0011 | De gebruiker heeft de actie geannuleerd (CTRL +C) | Voer de vorige opdracht opnieuw uit |
| AZCM0012 | Het opgegeven toegangs token is ongeldig | Een nieuw toegang token verkrijgen en het opnieuw proberen |
| AZCM0013 | De opgegeven tags zijn ongeldig | Controleer of de tags tussen dubbele aanhalingstekens staan, gescheiden door komma's, en of namen of waarden met spaties tussen enkele aanhalingstekens staan: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | De cloud is ongeldig | Geef een ondersteunde cloud `AzureCloud` op: of `AzureUSGovernment` |
| AZCM0015 | De opgegeven correlatie-id is geen geldige GUID | Geef een geldige GUID op voor `--correlation-id` |
| AZCM0016 | Er ontbreekt een verplichte parameter | Controleer de uitvoer om te bepalen welke parameters ontbreken |
| AZCM0017 | De resourcenaam is ongeldig | Geef een naam op die alleen alfanumerieke tekens, afbreekstreepingstekens en/of onderstrepingstekens gebruikt. De naam mag niet eindigen met een afbreekstreepingsteken of onderstrepingsteken. |
| AZCM0018 | De opdracht is uitgevoerd zonder beheerdersbevoegdheden | Voer de opdracht opnieuw uit met beheerders- of hoofdbevoegdheden in een opdrachtprompt met verhoogde bevoegdheden of een consolesessie. |
| AZCM0041 | De opgegeven referenties zijn ongeldig | Voor apparaat-aanmeldingen controleert u of het opgegeven gebruikersaccount toegang heeft tot de tenant en het abonnement waarin de serverresource wordt gemaakt. Voor aanmeldingen van service-principals controleert u de client-id en het geheim op juistheid, de vervaldatum van het geheim en of de service-principal afkomstig is van dezelfde tenant waar de serverresource wordt gemaakt. |
| AZCM0042 | Het maken van de arc-serverresource is mislukt | Controleer of de opgegeven gebruiker/service-principal toegang heeft tot het maken van serverresources met Arc in de opgegeven resourcegroep. |
| AZCM0043 | Verwijderen van de Arc-serverresource is mislukt | Controleer of de opgegeven gebruiker/service-principal toegang heeft tot het verwijderen van serverresources met Arc in de opgegeven resourcegroep. Als de resource niet meer bestaat in Azure, gebruikt u de `--force-local-only` vlag om door te gaan. |
| AZCM0044 | Er bestaat al een resource met dezelfde naam | Geef een andere naam op voor `--resource-name` de parameter of verwijder de bestaande Arc-server in Azure en probeer het opnieuw. |
| AZCM0061 | Kan de agentservice niet bereiken | Controleer of u de opdracht in een verhoogde gebruikerscontext (administrator/root) hebt uitgevoerd en of de HIMDS-service op uw server wordt uitgevoerd. |
| AZCM0062 | Er is een fout opgetreden tijdens het verbinden van de server | Bekijk andere foutcodes in de uitvoer voor meer specifieke informatie. Als de fout is opgetreden nadat de Azure-resource is gemaakt, moet u de Arc-server uit uw resourcegroep verwijderen voordat u het opnieuw kunt proberen. |
| AZCM0063 | Er is een fout opgetreden tijdens het verbreken van de verbinding met de server | Bekijk andere foutcodes in de uitvoer voor meer specifieke informatie. Als u deze fout blijft tegenkomen, kunt u de resource in Azure verwijderen en vervolgens uitvoeren op de server om de verbinding met `azcmagent disconnect --force-local-only` de agent te verbreken. |
| AZCM0064 | De agentservice reageert niet | Controleer de status van de service om te controleren of `himds` deze wordt uitgevoerd. Start de service als deze niet wordt uitgevoerd. Als deze wordt uitgevoerd, wacht u een minuut en probeert u het opnieuw. |
| AZCM0065 | Er is een communicatiefout opgetreden in de interne agent | Contact opnemen Microsoft-ondersteuning voor hulp |
| AZCM0066 | De webservice van de agent reageert niet of is niet beschikbaar | Contact opnemen Microsoft-ondersteuning voor hulp |
| AZCM0067 | De agent is al verbonden met Azure | Volg eerst de stappen in [de verbinding met de agent](manage-agent.md#unregister-machine) verbreken en probeer het vervolgens opnieuw. |
| AZCM0068 | Er is een interne fout opgetreden tijdens het loskoppelen van de server van Azure | Contact opnemen Microsoft-ondersteuning voor hulp |
| AZCM0081 | Er is een fout opgetreden tijdens het downloaden van Azure Active Directory beheerde identiteitscertificaat | Als dit bericht wordt aangetroffen tijdens een poging om de server te verbinden met Azure, kan de agent niet communiceren met de Azure Arc service. Verwijder de resource in Azure en probeer opnieuw verbinding te maken. |
| AZCM0101 | De opdracht is niet geparseerd | Voer uit `azcmagent <command> --help` om de juiste opdrachtsyntaxis te controleren |
| AZCM0102 | Kan de hostnaam van de computer niet ophalen | Voer `hostname` uit om te controleren op foutberichten op systeemniveau en neem vervolgens contact op met Microsoft-ondersteuning. |
| AZCM0103 | Er is een fout opgetreden tijdens het genereren van RSA-sleutels | Contact opnemen Microsoft-ondersteuning voor hulp |
| AZCM0104 | Kan systeemgegevens niet lezen | Controleer of de identiteit die wordt gebruikt om uit te voeren `azcmagent` beheerders-/hoofdbevoegdheden heeft op het systeem en probeer het opnieuw. |

## <a name="agent-verbose-log"></a>Uitgebreid logboek van agent

Voordat u de stappen voor probleemoplossing volgt die verderop in dit artikel worden beschreven, is het uitgebreide logboek de minimale informatie die u nodig hebt. Het bevat de uitvoer van de **opdrachten van het hulpprogramma azcmagent,** wanneer het uitgebreide argument (-v) wordt gebruikt. De logboekbestanden worden geschreven naar `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` voor Windows en Linux naar `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Hier volgt een voorbeeld van de opdracht voor het inschakelen van uitgebreide logboekregistratie met de Connected Machine-agent voor Windows bij het uitvoeren van een interactieve installatie.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Hier volgt een voorbeeld van de opdracht voor het inschakelen van uitgebreide logboekregistratie met de Connected Machine-agent voor Windows bij het uitvoeren van een installatie op schaal met behulp van een service-principal.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Hier volgt een voorbeeld van de opdracht voor het inschakelen van uitgebreide logboekregistratie met de Connected Machine-agent voor Linux bij het uitvoeren van een interactieve installatie.

>[!NOTE]
>U moet *machtigingen voor toegang* tot de hoofdmap hebben op Linux-machines om **azcmagent uit te voeren.**

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Hier volgt een voorbeeld van de opdracht voor het inschakelen van uitgebreide logboekregistratie met de Connected Machine-agent voor Linux bij het uitvoeren van een installatie op schaal met behulp van een service-principal.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problemen met de verbinding van de agent met de service

De volgende tabel bevat enkele bekende fouten en suggesties voor het oplossen van problemen.

|Bericht |Fout |Waarschijnlijke oorzaak |Oplossing |
|--------|------|---------------|---------|
|Kan geen autorisatie-tokenapparaatstroom verkrijgen |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Kan eindpunt `login.windows.net` niet bereiken | Controleer de verbinding met het eindpunt. |
|Kan geen autorisatie-tokenapparaatstroom verkrijgen |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Proxy of firewall blokkeert de toegang tot `login.windows.net` het eindpunt. | Controleer de verbinding met het eindpunt en deze wordt niet geblokkeerd door een firewall of proxyserver. |
|Kan geen autorisatie-tokenapparaatstroom verkrijgen  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | groepsbeleid Object *Computer Configuration\ Beheersjablonen\ System\ User Profiles\ Delete* user profiles older than a specified number of days on system restart is ingeschakeld. | Controleer of het GPO is ingeschakeld en gericht is op de betreffende computer. Zie voetnoot <sup>[1](#footnote1)</sup> voor meer informatie. |
|Kan geen autorisatie-token verkrijgen van SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Proxy of firewall blokkeert de toegang tot `login.windows.net` het eindpunt. |Controleer de verbinding met het eindpunt en deze wordt niet geblokkeerd door een firewall of proxyserver. |
|Kan geen autorisatie-token verkrijgen van SPN |`Invalid client secret is provided` |Onjuist of ongeldig geheim van service-principal. |Controleer het geheim van de service-principal. |
| Kan geen autorisatie-token verkrijgen van SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Onjuiste service-principal en/of tenant-id. |Controleer de service-principal en/of de tenant-id.|
|Antwoord van ARM-resource krijgen |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Verkeerde referenties en/of machtigingen |Controleer of u of de service-principal lid is van Azure Connected Machine **onboarding-rol.** |
|Kan arm-resource AzcmagentConnect niet gebruiken |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure-resourceproviders worden niet geregistreerd. |Registreer de [resourceproviders](./agent-overview.md#register-azure-resource-providers). |
|Kan arm-resource AzcmagentConnect niet gebruiken |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Proxyserver of firewall blokkeert de toegang `management.azure.com` tot het eindpunt. |Controleer de verbinding met het eindpunt en deze wordt niet geblokkeerd door een firewall of proxyserver. |

<a name="footnote1"></a><sup>1</sup> Als dit GPO is ingeschakeld en van toepassing is op machines met de Connected Machine-agent, wordt het gebruikersprofiel verwijderd dat is gekoppeld aan het ingebouwde account dat is opgegeven voor de *himds-service.* Als gevolg hiervan wordt ook het verificatiecertificaat verwijderd dat wordt gebruikt om te communiceren met de service die 30 dagen in de cache van het lokale certificaatopslag is opgeslagen. Vóór de limiet van 30 dagen wordt geprobeerd het certificaat te vernieuwen. U kunt dit probleem oplossen door de registratie van de [machine](manage-agent.md#unregister-machine) ongedaan te maken en deze vervolgens opnieuw te registreren bij de service met `azcmagent connect` .

## <a name="next-steps"></a>Volgende stappen

Als u het probleem hier niet ziet of als u het probleem niet kunt oplossen, probeert u een van de volgende kanalen voor aanvullende ondersteuning:

* Krijg antwoorden van Azure-experts via [Microsoft Q&A](/answers/topics/azure-arc.html).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de klantervaring. Ondersteuning voor Azure maakt de Azure-community verbinding met antwoorden, ondersteuning en experts.

* Een incident ondersteuning voor Azure indienen. Ga naar de [ondersteuning voor Azure site](https://azure.microsoft.com/support/options/)en selecteer **Ondersteuning krijgen.**
