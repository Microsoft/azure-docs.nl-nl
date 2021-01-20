---
title: Azure AD Connect fout codes en-beschrijvingen voor Cloud synchronisatie
description: Referentie artikel voor fout codes voor Cloud synchronisatie
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22e677e5a86f60627552161f7b2115c08695dbf0
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613235"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect fout codes en-beschrijvingen voor Cloud synchronisatie
Hier volgt een lijst met fout codes en de bijbehorende beschrijvingen


## <a name="error-codes"></a>Foutcodes

|Foutcode|Details|Scenario|Oplossing|
|-----|-----|-----|-----|
|Out|Fout bericht: er is een time-out opgetreden voor de aanvraag bij het maken van contact met de on-premises agent en het synchroniseren van uw configuratie. Raadpleeg de richt lijnen voor probleem oplossing voor meer problemen met betrekking tot uw Cloud synchronisatie agent.|Er is een time-out opgetreden voor de aanvraag. De huidige time-outwaarde is 10 minuten.|Raadpleeg de [richt lijnen voor probleem oplossing](how-to-troubleshoot.md)|
|HybridSynchronizationActiveDirectoryInternalServerError|Fout bericht: deze aanvraag kan op dit moment niet worden verwerkt. Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning en geeft u de volgende taak-id op: AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Aanvullende details: verwerking van de HTTP-aanvraag resulteerde in een uitzonde ring. |Kan de in SCIM-aanvraag ontvangen para meters niet verwerken naar een zoek opdracht.|Raadpleeg het HTTP-antwoord dat is geretourneerd door de eigenschap Response van deze uitzonde ring voor meer informatie.|
|HybridIdentityServiceNoAgentsAssigned|Fout bericht: er is geen actieve agent gevonden voor het domein dat u wilt synchroniseren. Controleer of de agents zijn verwijderd. Als dit het geval is, installeert u de agent opnieuw.|Er worden geen agents uitgevoerd. Er zijn waarschijnlijk agents verwijderd. Registreer een nieuwe agent.|"In dit geval wordt er geen agent weer geven die is toegewezen aan het domein in de portal.|
|HybridIdentityServiceNoActiveAgents|Fout bericht: er is geen actieve agent gevonden voor het domein dat u wilt synchroniseren. Controleer of de agent wordt uitgevoerd door naar de server te gaan, waar de agent is geïnstalleerd en controleer of ' Microsoft Azure AD Cloud Sync agent ' onder Services wordt uitgevoerd.|"Agents Luis teren niet naar het ServiceBus-eind punt. [De agent bevindt zich achter een firewall die geen verbinding met Service Bus toestaat](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|Fout bericht: deze aanvraag kan op dit moment niet worden verwerkt. Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning en geeft u de volgende taak-id op: AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9. Aanvullende details: er is een probleem met de configuratie van de Cloud synchronisatie. Registreer uw Cloud synchronisatie agent opnieuw in uw on-premises AD-domein en start de configuratie opnieuw vanuit Azure Portal.|De resource naam moet zo worden ingesteld dat deze de contact persoon van de agent weet.|Registreer uw Cloud synchronisatie agent opnieuw in uw on-premises AD-domein en start de configuratie opnieuw vanuit Azure Portal.|
|HybridIdentityServiceAgentSignalingError|Fout bericht: deze aanvraag kan op dit moment niet worden verwerkt. Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning en geeft u de volgende taak-id op: AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62e8-42e3-B495-18d5272eb3f9. Aanvullende details: deze aanvraag kan op dit moment niet worden verwerkt. Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning met de taak-ID (uit het status venster van uw configuratie).|Service Bus kan geen bericht verzenden naar de agent. Dit kan een storing in service bus zijn of de agent reageert niet.|Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning met de taak-ID (uit het status venster van uw configuratie).|
|AzureDirectoryServiceServerBusy|Fout bericht: er is een fout opgetreden. Fout code: 81. Fout beschrijving: de Azure Active Directory is momenteel bezet. Deze bewerking wordt automatisch opnieuw uitgevoerd. Neem contact op met de technische ondersteuning als het probleem zich langer dan 24 uur voordoet. Tracerings-ID: 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f-server naam:|Azure Active Directory is momenteel bezet.|Neem contact op met de technische ondersteuning als het probleem zich langer dan 24 uur voordoet.|
|AzureActiveDirectoryInvalidCredential|Fout bericht: er is een probleem gevonden met het service account dat wordt gebruikt om Azure AD Connect Cloud synchronisatie uit te voeren. U kunt het Cloud service-account herstellen door de instructies [hier](https://go.microsoft.com/fwlink/?linkid=2150988)te volgen. Als de fout zich blijft voordoen, neemt u contact op met de ondersteuning met de taak-ID (uit het status venster van uw configuratie). Aanvullende fout Details: CredentialsInvalid AADSTS50034: het gebruikers account {EmailHidden} bestaat niet in de skydrive365.onmicrosoft.com-map. Als u zich wilt aanmelden bij deze toepassing, moet het account worden toegevoegd aan de Directory. Tracerings-ID: 14b63033-3bc9-4bd4-b871-5eb4b3500200 correlatie-ID: 57d93ed1-be4d-483c-997c-a3b6f03deb00-tijds tempel: 2021-01-12 21:08:29Z |Deze fout treedt op wanneer het synchronisatie service account ADToAADSyncServiceAccount niet voor komt in de Tenant. Dit kan worden veroorzaakt door onbedoeld verwijderen van het account.|Gebruik [Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) om het service account te herstellen.|
|AzureActiveDirectoryExpiredCredentials|Fout bericht: deze aanvraag kan op dit moment niet worden verwerkt. Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning met de taak-ID (uit het status venster van uw configuratie). Aanvullende fout Details: CredentialsExpired AADSTS50055: het wacht woord is verlopen. Tracerings-ID: 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 correlatie-ID: 1c69b196-1c3a-4381-9187-c84747807155-tijds tempel: 2021-01-12 20:59:31Z | De antwoord status code geeft niet aan dat geslaagd is: 401 (niet toegestaan).|De referenties van het AAD Sync-Service account zijn verlopen.|U kunt het Cloud service-account herstellen door de instructies op te volgen op https://go.microsoft.com/fwlink/?linkid=2150988 . Als de fout zich blijft voordoen, neemt u contact op met de ondersteuning met de taak-ID (uit het status venster van uw configuratie).  Aanvullende fout Details: de referenties van uw beheerders Azure Active Directory tenants zijn uitgewisseld voor een OAuth-token dat sindsdien is verlopen.|
|AzureActiveDirectoryAuthenticationFailed|Fout bericht: deze aanvraag kan op dit moment niet worden verwerkt. Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning en geeft u de volgende taak-id op: AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405d-a3b9-de87761dc3ac. Aanvullende details: deze aanvraag kan op dit moment niet worden verwerkt. Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning met de taak-ID (uit het status venster van uw configuratie). Aanvullende fout Details: UnexpectedError.|Onbekende fout.|Als dit probleem zich blijft voordoen, neemt u contact op met de ondersteuning met de taak-ID (uit het status venster van uw configuratie).|

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud synchronisatie?](what-is-cloud-sync.md)
