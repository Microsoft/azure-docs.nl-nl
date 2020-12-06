---
title: Release geschiedenis van de wachtwoord beveiligings agent-Azure Active Directory
description: Geschiedenis van versie en wijziging van het gedrag van de release van documenten
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd9b07f1f7aed479e94e77a5641130cb784dd69e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741963"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Versie geschiedenis van de Azure AD-wachtwoord beveiligings agent

## <a name="121250"></a>1.2.125.0

Release datum: 3/22/2019

* Kleine fout type fouten in gebeurtenis logboek berichten oplossen
* GEBRUIKSRECHT overeenkomst bijwerken naar definitieve versie van algemene Beschik baarheid

> [!NOTE]
> Build 1.2.125.0 is de algemene Beschik baarheid. Hartelijk dank voor iedereen heeft feedback gegeven over het product.

## <a name="121160"></a>1.2.116.0

Release datum: 3/13/2019

* Met de cmdlets Get-AzureADPasswordProtectionProxy en Get-AzureADPasswordProtectionDCAgent wordt nu software versie en de huidige Azure-Tenant gerapporteerd met de volgende beperkingen:
  * Software versie en Azure-Tenant gegevens zijn alleen beschikbaar voor DC-agents en-proxy's waarop versie 1.2.116.0 of hoger wordt uitgevoerd.
  * Azure-Tenant gegevens worden mogelijk pas gerapporteerd als er een nieuwe registratie (of verlenging) van de proxy of het forest is opgetreden.
* Voor de proxy service moet .NET 4,7 nu zijn geïnstalleerd.
  * .NET 4,7 moet al zijn geïnstalleerd op een volledig bijgewerkte Windows-Server. Als dat niet het geval is, downloadt en voert u het installatie programma uit dat is gevonden op [het .NET Framework 4,7 offline-installatie programma voor Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
  * Op Server Core-systemen kan het nodig zijn om de/q-vlag door te geven aan het .NET 4,7-installatie programma om deze te laten slagen.
* De proxy service ondersteunt nu automatische upgrades. Automatische upgrade maakt gebruik van de Microsoft Azure AD connect agent Updater-service die naast de proxy service is geïnstalleerd. Automatische upgrade is standaard ingeschakeld.
* Automatische upgrades kunnen worden ingeschakeld of uitgeschakeld met behulp van de cmdlet Set-AzureADPasswordProtectionProxyConfiguration. De huidige instelling kan worden opgevraagd met behulp van de cmdlet Get-AzureADPasswordProtectionProxyConfiguration.
* De naam van de service-binary voor de DC-Agent service is gewijzigd in AzureADPasswordProtectionDCAgent.exe.
* De naam van de binaire service voor de proxy service is gewijzigd in AzureADPasswordProtectionProxy.exe. Firewall regels moeten mogelijk dienovereenkomstig worden gewijzigd als een firewall van een derde partij in gebruik is.
  * Opmerking: als er een http-proxy configuratie bestand werd gebruikt in een eerdere proxy installatie, moet u na deze upgrade een andere naam (van *proxyservice.exe.config* tot *AzureADPasswordProtectionProxy.exe.config*) krijgen.
* Alle tijdgebonden functionaliteits controles zijn verwijderd uit de DC-agent.
* Kleine oplossingen voor fouten en logboek registratie van verbeteringen.

## <a name="12650"></a>1.2.65.0

Release datum: 2/1/2019

Gewijzigde

* DC-agent en proxy service worden nu ondersteund op Server Core. Mininimum OS-vereisten zijn niet gewijzigd ten opzichte van vóór: Windows Server 2012 voor DC-agents en Windows Server 2012 R2 voor proxy's.
* De cmdlets Register-AzureADPasswordProtectionProxy en Register-AzureADPasswordProtectionForest bieden nu ondersteuning voor Azure-verificatie modi op basis van een apparaatcode.
* Met de cmdlet Get-AzureADPasswordProtectionDCAgent worden vervormde en/of ongeldige service verbindings punten genegeerd. Hiermee wordt de bug opgelost waarbij domein controllers soms meerdere keren in de uitvoer worden weer gegeven.
* Met de cmdlet Get-AzureADPasswordProtectionSummaryReport worden vervormde en/of ongeldige service verbindings punten genegeerd. Hiermee wordt de bug opgelost waarbij domein controllers soms meerdere keren in de uitvoer worden weer gegeven.
* De module proxy Power shell is nu geregistreerd bij%ProgramFiles%\WindowsPowerShell\Modules. De omgevings variabele PSModulePath van de machine wordt niet meer gewijzigd.
* Er is een nieuwe Get-AzureADPasswordProtectionProxy cmdlet toegevoegd ter ondersteuning bij het detecteren van geregistreerde proxy's in een forest of domein.
* De DC-agent gebruikt een nieuwe map in de SYSVOL-share voor het repliceren van wachtwoord beleid en andere bestanden.

   Oude maplocatie:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Nieuwe maplocatie:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Deze wijziging is doorgevoerd om waarschuwingen over onjuiste positieve ' zwevende GPO ' te voor komen.)

   > [!NOTE]
   > Er wordt geen migratie of delen van gegevens uitgevoerd tussen de oude map en de nieuwe map. Oudere versies van de DC-agent blijven de oude locatie gebruiken totdat deze is bijgewerkt naar deze versie of hoger. Zodra op alle DC-agents versie 1.2.65.0 of hoger wordt uitgevoerd, wordt de oude map SYSVOL mogelijk hand matig verwijderd.

* De DC-agent en de proxy service detecteren de vervormde kopieën van hun respectieve service verbindings punten nu en worden verwijderd.
* Elke DC-agent zal regel matig vervormde en verlopen service verbindings punten in het domein verwijderen voor zowel DC-agent-als proxy service-verbindings punten. De verbindings punten van de DC-agent en de proxy service worden als verouderd beschouwd als de heartbeat-tijds tempel ouder is dan zeven dagen.
* De DC-agent zal het forest-certificaat nu vernieuwen als dat nodig is.
* De proxy-service zal het proxy certificaat nu vernieuwen als dat nodig is.
* Updates voor het algoritme voor wachtwoord validatie: de algemene lijst met geblokkeerde wacht woorden en de klantspecifieke lijst met geblokkeerde wacht woorden (indien geconfigureerd) worden gecombineerd voor validatie van wacht woorden. Een gegeven wacht woord kan nu worden geweigerd (alleen mislukt of alleen controleren) als het tokens uit de algemene en klantspecifieke lijst bevat. De documentatie van het gebeurtenis logboek is bijgewerkt, zodat deze kan worden weer gegeven. Zie [Azure AD-wachtwoord beveiliging controleren](howto-password-ban-bad-on-premises-monitor.md).
* Oplossingen voor prestaties en robuustheid
* Verbeterde logboek registratie

> [!WARNING]
> Beperkte functionaliteit: de DC-Agent service in deze release (1.2.65.0) stopt met het verwerken van aanvragen voor wachtwoord validatie vanaf 1 september 2019.  DC-agent services in eerdere versies (Zie de lijst hieronder) stopt met de verwerking vanaf 1 juli 2019. De DC Agent-service in alle versies registreert 10021-gebeurtenissen in het gebeurtenis logboek van de beheerder in de twee maanden waarin deze deadlines van belang zijn. Alle tijds limiet beperkingen worden verwijderd in de aanstaande GA-release. De proxy-Agent service is niet in een wille keurige versie beperkt, maar moet nog steeds worden bijgewerkt naar de nieuwste versie om te kunnen profiteren van alle volgende oplossingen voor fouten en andere verbeteringen.

## <a name="12250"></a>1.2.25.0

Release datum: 11/01/2018

Dit

* DC-agent en proxy service mogen niet meer mislukken als gevolg van fouten in de certificaat vertrouwen.
* DC-agent en proxy service hebben extra oplossingen voor FIPS-compatibele computers.
* Proxy service werkt nu goed in een TLS 1,2-only-netwerk omgeving.
* Kleine prestaties en robuustheid van oplossingen
* Verbeterde logboek registratie

Gewijzigde

* Het mini maal vereiste besturingssysteem niveau voor de proxy service is nu Windows Server 2012 R2. Het mini maal vereiste besturingssysteem niveau voor de DC-Agent service blijft Windows Server 2012.
* Voor de proxy service is nu .NET versie 4.6.2 vereist.
* De wachtwoord validatie algoritme maakt gebruik van een verbreede teken normalisatie tabel. Dit kan ertoe leiden dat wacht woorden worden afgewezen die in eerdere versies zijn geaccepteerd.

## <a name="12100"></a>1.2.10.0

Release datum: 8/17/2018

Dit

* Register-AzureADPasswordProtectionProxy en Register-AzureADPasswordProtectionForest bieden nu ondersteuning voor multi-factor Authentication
* Voor Register-AzureADPasswordProtectionProxy is een WS2012 of nieuwe domein controller in het domein vereist om versleutelings fouten te voor komen.
* De DC-Agent service is betrouwbaarder voor het aanvragen van een nieuw wachtwoord beleid van Azure bij het opstarten.
* DC-Agent service vraagt elk uur, indien nodig, een nieuw wachtwoord beleid aan van Azure, maar zal dit nu doen op een wille keurige geselecteerde begin tijd.
* DC-Agent service veroorzaakt niet langer een onbeperkte vertraging in een nieuwe DC-aankondiging wanneer deze wordt geïnstalleerd op een server voorafgaand aan de promotie als een replica.
* De DC-Agent service voldoet nu aan de configuratie-instelling wachtwoord beveiliging inschakelen voor Windows Server Active Directory
* Zowel DC-agent-als proxy installatie Programma's ondersteunen nu in-place upgrade bij het upgraden naar toekomstige versies.

> [!WARNING]
> Een in-place upgrade van versie 1.1.10.3 wordt niet ondersteund en leidt tot een installatie fout. Als u een upgrade wilt uitvoeren naar versie 1.2.10 of hoger, moet u eerst de DC-agent en de proxy service-software verwijderen en vervolgens de nieuwe versie volledig installeren. U moet de Azure AD-proxy service voor wachtwoord beveiliging opnieuw registreren.  Het is niet nodig om het forest opnieuw te registreren.

> [!NOTE]
> In-place Upgrades van de DC-agent software moet opnieuw worden opgestart.

* DC-agent en proxy service bieden nu ondersteuning voor uitvoering op een server die is geconfigureerd om alleen FIPS-compatibele algoritmen te gebruiken.
* Kleine prestaties en robuustheid van oplossingen
* Verbeterde logboek registratie

## <a name="11103"></a>1.1.10.3

Release datum: 6/15/2018

Eerste open bare preview-versie

## <a name="next-steps"></a>Volgende stappen

[Azure AD-wachtwoord beveiliging implementeren](howto-password-ban-bad-on-premises-deploy.md)
