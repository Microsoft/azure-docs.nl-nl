---
title: Problemen met aanmelden oplossen in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het oplossen van veelvoorkomende problemen met aanmelding bij gebruikers en fouten in Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618566"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Problemen met het aanmelden van accounts oplossen met een door Azure Active Directory Domain Services beheerd domein

De meest voorkomende redenen voor een gebruikers account waarmee u zich niet kunt aanmelden bij een beheerd domein van een Azure Active Directory Domain Services (Azure AD DS), zijn de volgende scenario's:

* [Het account is nog niet gesynchroniseerd met Azure AD DS.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Azure AD DS heeft geen wacht woord-hashes waarmee het account kan worden aangemeld.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [Het account is vergrendeld.](#the-account-is-locked-out)

> [!TIP]
> Azure AD DS kan niet synchroniseren in referenties voor accounts die extern zijn voor de Azure AD-Tenant. Externe gebruikers kunnen zich niet aanmelden bij het beheerde domein van Azure AD DS.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>Het account is nog niet gesynchroniseerd in azure AD DS

Afhankelijk van de grootte van uw map kan het enige tijd duren voordat gebruikers accounts en referentie-hashes beschikbaar zijn in een beheerd domein. Voor grote mappen kan deze eerste eenrichtings synchronisatie vanuit Azure AD enkele uren duren en Maxi maal één dag of twee. Zorg ervoor dat u lang genoeg wacht voordat u opnieuw probeert te authenticeren.

Voor hybride omgevingen die gebruikers Azure AD Connect om on-premises Directory gegevens te synchroniseren met Azure AD, moet u ervoor zorgen dat u de meest recente versie van Azure AD Connect uitvoert en [Azure AD Connect hebt geconfigureerd om een volledige synchronisatie uit te voeren nadat u Azure AD DS hebt ingeschakeld][azure-ad-connect-phs]. Als u Azure AD DS uitschakelt en vervolgens weer inschakelt, moet u deze stappen opnieuw uitvoeren.

Als er problemen blijven optreden met accounts die niet zijn gesynchroniseerd via Azure AD Connect, start u de Azure AD Sync-service opnieuw. Open op de computer waarop Azure AD Connect is geïnstalleerd een opdracht prompt venster en voer de volgende opdrachten uit:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Azure AD DS heeft geen wacht woord-hashes

Totdat u Azure AD DS voor uw tenant inschakelt, maakt of bewaart Azure AD geen wachtwoordhashes in de vereiste indeling voor NTLM- of Kerberos-verificatie. Om veiligheidsredenen slaat Azure AD ook geen wachtwoorden op in niet-gecodeerde vorm. Azure AD kan deze wachtwoordhashes voor NTLM of Kerberos niet automatisch genereren op basis van bestaande referenties van gebruikers.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hybride omgevingen met on-premises synchronisatie

Voor hybride omgevingen met Azure AD Connect om te synchroniseren vanuit een on-premises AD DS omgeving, kunt u de vereiste NTLM-of Kerberos-wachtwoord hashes lokaal genereren en synchroniseren in azure AD. Nadat u uw beheerde domein hebt gemaakt, schakelt u de [synchronisatie van wacht woord-hashen in azure Active Directory Domain Services][azure-ad-connect-phs]. Als u de synchronisatie stap voor wachtwoord hash niet hebt voltooid, kunt u zich niet aanmelden bij een account met behulp van het beheerde domein. Als u Azure AD DS uitschakelt en vervolgens weer inschakelt, moet u deze stappen opnieuw volgen.

Zie [hoe wacht woord-hash synchronisatie werkt voor Azure AD DS][phs-process]voor meer informatie.

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Alleen Cloud omgevingen zonder on-premises synchronisatie

Beheerde domeinen zonder on-premises synchronisatie, alleen accounts in azure AD, moeten ook de vereiste NTLM-of Kerberos-wachtwoord hashes genereren. Als een Cloud account niet kan worden aangemeld, heeft een wachtwoord wijzigings proces voltooid voor het account nadat Azure AD DS is ingeschakeld?

* **Nee, het wacht woord is niet gewijzigd.**
    * [Wijzig het wacht woord voor het account][enable-user-accounts] om de vereiste wacht woord-hashes te genereren en wacht vijf tien minuten voordat u zich opnieuw probeert aan te melden.
    * Als u Azure AD DS uitschakelt en vervolgens weer inschakelt, moet elk account de stappen opnieuw uitvoeren om het wacht woord te wijzigen en de vereiste wachtwoord hashes te genereren.
* **Ja, het wacht woord is gewijzigd.**
    * Probeer u aan te melden met de *UPN* -indeling, zoals `driley@aaddscontoso.com` in plaats van de *SAMAccountName* -indeling zoals `AADDSCONTOSO\deeriley` .
    * De *SAMAccountName* kan automatisch worden gegenereerd voor gebruikers waarvan het UPN-voor voegsel langer is dan of gelijk is aan die van een andere gebruiker in het beheerde domein. De *UPN* -indeling is gegarandeerd uniek binnen een Azure AD-Tenant.

## <a name="the-account-is-locked-out"></a>Het account is vergrendeld

Een gebruikers account in een beheerd domein wordt vergrendeld wanneer is voldaan aan een ingestelde drempel voor mislukte aanmeldings pogingen. Dit account vergrendelings gedrag is ontworpen om u te beschermen tegen herhaalde pogingen om te voor komen dat u zich kunt aanmelden met een geautomatiseerde digitale aanval.

Als er gedurende twee minuten vijf mislukte wachtwoord pogingen zijn, wordt het account 30 minuten vergrendeld.

Zie problemen [met account vergrendeling oplossen in Azure AD DS][troubleshoot-account-lockout]voor meer informatie en hoe u problemen met account vergrendeling kunt oplossen.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt met het toevoegen van uw VM aan het beheerde domein, [zoekt u hulp en opent u een ondersteunings ticket voor Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md