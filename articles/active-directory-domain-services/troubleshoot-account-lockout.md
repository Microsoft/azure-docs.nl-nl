---
title: Problemen met account vergrendeling in Azure AD Domain Services oplossen | Microsoft Docs
description: Meer informatie over het oplossen van veelvoorkomende problemen die ertoe leiden dat gebruikers accounts worden vergrendeld in Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 3341f290a5a5bb169b6e70ea22459a2afafedbbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103198963"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Problemen met account vergrendeling oplossen met een door Azure Active Directory Domain Services beheerd domein

Om te voor komen dat er herhaalde aanmeldings pogingen worden gedaan, vergrendelt een door Azure Active Directory Domain Services (Azure AD DS) beheerde domein accounts na een ingestelde drempel waarde. Deze account vergrendeling kan ook plaatsvinden per ongeluk zonder dat er zich een aanval heeft voordoen. Als een gebruiker bijvoorbeeld herhaaldelijk het verkeerde wacht woord invoert of een service probeert een oud wacht woord te gebruiken, wordt het account vergrendeld.

Dit artikel bevat informatie over het oplossen van problemen met account vergrendelingen en over hoe u het gedrag kunt configureren en hoe beveiligings controles moeten worden gecontroleerd om vergrendelings gebeurtenissen op te lossen.

## <a name="what-is-an-account-lockout"></a>Wat is een account vergrendeling?

Een gebruikers account in een Azure AD DS beheerd domein wordt vergrendeld wanneer aan een gedefinieerde drempel voor mislukte aanmeldings pogingen is voldaan. Dit account vergrendelings gedrag is ontworpen om u te beschermen tegen herhaalde pogingen om te voor komen dat u zich kunt aanmelden met een geautomatiseerde digitale aanval.

**Als er gedurende twee minuten vijf mislukte wachtwoord pogingen zijn, wordt het account 30 minuten vergrendeld.**

De standaard drempel waarden voor account vergrendeling worden geconfigureerd met een nauw keurig wachtwoord beleid. Als u een specifieke set vereisten hebt, kunt u deze standaard drempel waarden voor account vergrendeling onderdrukken. Het is echter niet raadzaam de drempel limieten te verhogen om de vergren deling van het aantal accounts te verminderen. Los de bron van het account vergrendelings gedrag eerst op.

### <a name="fine-grained-password-policy"></a>Nauw keurig wachtwoord beleid

Met een verfijnd wachtwoord beleid (FGPPs) kunt u specifieke beperkingen voor wacht woord-en account vergrendelings beleid Toep assen op verschillende gebruikers in een domein. FGPP is alleen van invloed op gebruikers binnen een beheerd domein. Cloud gebruikers en domein gebruikers die zijn gesynchroniseerd met het beheerde domein vanuit Azure AD worden alleen beïnvloed door het wachtwoord beleid binnen het beheerde domein. Hun accounts in azure AD of een on-premises Directory worden niet beïnvloed.

Beleids regels worden gedistribueerd via een groeps koppeling in het beheerde domein en alle wijzigingen die u aanbrengt, worden toegepast bij de volgende aanmelding van de gebruiker. Als u het beleid wijzigt, wordt een gebruikers account dat al is vergrendeld, niet ontgrendeld.

Zie [wacht woord-en account vergrendelings beleid configureren][configure-fgpp]voor meer informatie over verfijnd wachtwoord beleid en de verschillen tussen gebruikers die rechtstreeks in azure AD DS zijn gemaakt en die zijn gesynchroniseerd vanuit Azure AD.

## <a name="common-account-lockout-reasons"></a>Veelvoorkomende redenen voor account vergrendeling

De meest voorkomende redenen voor het vergren delen van een account, zonder schadelijke intentie of factoren, zijn de volgende scenario's:

* **De gebruiker heeft zichzelf vergrendeld.**
    * Heeft de gebruiker na een recente wachtwoord wijziging een vorig wacht woord blijven gebruiken? Het standaard account vergrendelings beleid van vijf mislukte pogingen binnen twee minuten kan worden veroorzaakt door de gebruiker per ongeluk opnieuw proberen een oud wacht woord.
* **Er is een toepassing of service met een oud wacht woord.**
    * Als een account wordt gebruikt door toepassingen of services, proberen deze resources zich herhaaldelijk aan te melden met een oud wacht woord. Dit gedrag zorgt ervoor dat het account wordt vergrendeld.
    * Probeer het account gebruik voor meerdere toepassingen of services te minimaliseren en leg vast waar referenties worden gebruikt. Als een account wachtwoord is gewijzigd, werkt u de bijbehorende toepassingen of services dienovereenkomstig bij.
* **Het wacht woord is gewijzigd in een andere omgeving en het nieuwe wacht woord is nog niet gesynchroniseerd.**
    * Als een account wachtwoord wordt gewijzigd buiten het beheerde domein, zoals in een on-premises AD DS omgeving, kan het enkele minuten duren voordat het wacht woord wordt gewijzigd in azure AD en in het beheerde domein.
    * Een gebruiker die zich probeert aan te melden bij een bron in het beheerde domein voordat het wachtwoord synchronisatie proces is voltooid, zorgt ervoor dat het account wordt vergrendeld.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Problemen met account vergrendelingen oplossen met beveiligings controles

Als u problemen wilt oplossen wanneer account vergrendelings gebeurtenissen optreden en waar ze vandaan komen, [schakelt u beveiligings controles in voor Azure AD DS][security-audit-events]. Controle gebeurtenissen worden alleen vastgelegd vanaf het moment dat u de functie inschakelt. In het ideale geval moet u beveiligings controles inschakelen *voordat* er een probleem is met account vergrendeling. Als een gebruikers account herhaaldelijk problemen vergrendelt, kunt u beveiligings controles voor de volgende keer dat de situatie zich voordoet, inschakelen.

Wanneer u beveiligings controles hebt ingeschakeld, laten de volgende voorbeeld query's zien hoe u *account vergrendelings gebeurtenissen* kunt controleren, code *4740*.

Bekijk alle account vergrendelings gebeurtenissen voor de afgelopen zeven dagen:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Bekijk alle account vergrendelings gebeurtenissen voor de afgelopen zeven dagen voor het account met de naam *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Bekijk alle account vergrendelings gebeurtenissen tussen 26 juni 2020 om 9:00 uur en 1 juli 2020 middernacht, oplopend gesorteerd op de datum en tijd:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

**Opmerking**

Mogelijk vindt u de gebeurtenis Details van het bron werkstation: leeg op 4776 en 4740. Dit komt doordat het wacht woord van de netwerk aanmelding via een aantal andere apparaten is overschreden.
Bijvoorbeeld: als u een RADIUS-server hebt, waarmee u de verificatie kunt door sturen naar AAD Active Directory. Om te controleren of het inschakelen van RDP voor DC-back-end Netlogon-logboeken configureren.

03/04 19:07:29 [Aanmelden] [10752] contoso: SamLogon: transitieve netwerk aanmelding van contoso\Nagappan.Veerappan van (via LOB11-RADIUS) ingevoerd 

03/04 19:07:29 [Aanmelden] [10752] contoso: SamLogon: transitieve netwerk aanmelding van contoso\Nagappan.Veerappan van (via LOB11-RADIUS) retourneert 0xC000006A

03/04 19:07:35 [Aanmelden] [10753] contoso: SamLogon: transitieve netwerk aanmelding van contoso\Nagappan.Veerappan van (via LOB11-RADIUS) ingevoerd 

03/04 19:07:35 [Aanmelden] [10753] contoso: SamLogon: transitieve netwerk aanmelding van contoso\Nagappan.Veerappan van (via LOB11-RADIUS) retourneert 0xC000006A

Schakel RDP in voor uw Dc's in NSG naar de back-end om diagnostische gegevens te configureren (dat wil zeggen Netlogon) https://docs.microsoft.com/azure/active-directory-domain-services/alert-nsg#inbound-security-rules Als u de standaard NSG al hebt gewijzigd, kunt u de PSlet-methode inschakelen https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#port-3389---management-using-remote-desktop

Netlogon-logboek inschakelen op elke server https://docs.microsoft.com/troubleshoot/windows-client/windows-security/enable-debug-logging-netlogon-service

## <a name="next-steps"></a>Volgende stappen

Zie [beleid voor wacht woord-en account vergrendeling configureren][configure-fgpp]voor meer informatie over verfijnd wachtwoord beleid voor het aanpassen van de drempel waarden voor account vergrendeling.

Als u nog steeds problemen ondervindt met het toevoegen van uw VM aan het beheerde domein, [zoekt u hulp en opent u een ondersteunings ticket voor Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
