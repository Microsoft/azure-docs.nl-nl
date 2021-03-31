---
title: Problemen met Azure Active Directory Domain Services oplossen | Microsoft Docs '
description: Meer informatie over het oplossen van veelvoorkomende fouten bij het maken of beheren van Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 89b04f86d41f8e4828580f70a9aec8acea3e0053
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618447"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Veelvoorkomende fouten en stappen voor probleem oplossing voor Azure Active Directory Domain Services

Als centraal onderdeel van identiteits-en verificatie voor toepassingen, Azure Active Directory Domain Services (Azure AD DS) soms problemen ondervindt. Als u problemen ondervindt, zijn er enkele veelvoorkomende fout berichten en bijbehorende stappen voor probleem oplossing om u te helpen met het uitvoeren van taken. U kunt op elk gewenst moment ook [een ondersteunings aanvraag voor Azure openen][azure-support] voor aanvullende hulp bij het oplossen van problemen.

Dit artikel bevat probleemoplossings stappen voor veelvoorkomende problemen in azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>U kunt Azure AD Domain Services niet inschakelen voor uw Azure AD-adres lijst

Als u problemen ondervindt met het inschakelen van Azure AD DS, raadpleegt u de volgende veelvoorkomende fouten en stappen om deze op te lossen:

| **Voorbeeld fout bericht** | **Oplossing** |
| --- |:--- |
| *De naam aaddscontoso.com wordt al gebruikt in dit netwerk. Geef een naam op die niet wordt gebruikt.* |[Domein naam conflict in het virtuele netwerk](troubleshoot.md#domain-name-conflict) |
| *Domain Services kan niet worden ingeschakeld in deze Azure AD-Tenant. De service heeft niet de juiste machtigingen voor de toepassing met de naam Azure AD Domain Services Sync. Verwijder de toepassing met de naam Azure AD Domain Services Sync en probeer vervolgens Domain Services in te scha kelen voor uw Azure AD-Tenant.* |[Domain Services beschikt niet over voldoende machtigingen voor de Azure AD Domain Services Sync-toepassing](troubleshoot.md#inadequate-permissions) |
| *Domain Services kan niet worden ingeschakeld in deze Azure AD-Tenant. De domein Services-toepassing in uw Azure AD-Tenant beschikt niet over de vereiste machtigingen om domein Services in te scha kelen. Verwijder de toepassing met de toepassings-id d87dcbc6-a371-462e-88e3-28ad15ec4e64 en probeer vervolgens Domain Services in te scha kelen voor uw Azure AD-Tenant.* |[De Domain Services-toepassing is niet juist geconfigureerd in uw Azure AD-Tenant](troubleshoot.md#invalid-configuration) |
| *Domain Services kan niet worden ingeschakeld in deze Azure AD-Tenant. De Microsoft Azure AD toepassing is uitgeschakeld in uw Azure AD-Tenant. Schakel de toepassing met de toepassings-id 00000002-0000-0000-C000-000000000000 te gebruiken in en probeer vervolgens Domain Services in te scha kelen voor uw Azure AD-Tenant.* |[De Microsoft Graph toepassing is uitgeschakeld in uw Azure AD-Tenant](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Conflict met domein naam

**Fout bericht**

*De naam aaddscontoso.com wordt al gebruikt in dit netwerk. Geef een naam op die niet wordt gebruikt.*

**Oplossing**

Controleer of u geen bestaande AD DS omgeving hebt met dezelfde domein naam op hetzelfde of een gekoppeld virtueel netwerk. U hebt bijvoorbeeld een AD DS domein met de naam *aaddscontoso.com* dat wordt uitgevoerd op virtuele Azure-machines. Wanneer u probeert een met Azure AD DS beheerd domein met dezelfde domein naam *aaddscontoso.com* in het virtuele netwerk in te scha kelen, mislukt de aangevraagde bewerking.

Deze fout wordt veroorzaakt door naam conflicten voor de domein naam in het virtuele netwerk. Met een DNS-zoek opdracht wordt gecontroleerd of een bestaande AD DS omgeving reageert op de aangevraagde domein naam. U kunt dit probleem oplossen door een andere naam te gebruiken om uw beheerde domein in te stellen of de inrichting van het bestaande AD DS domein te deactiveren en vervolgens opnieuw te proberen om Azure AD DS in te scha kelen.

### <a name="inadequate-permissions"></a>Ontoereikende machtigingen

**Fout bericht**

*Domain Services kan niet worden ingeschakeld in deze Azure AD-Tenant. De service heeft niet de juiste machtigingen voor de toepassing met de naam Azure AD Domain Services Sync. Verwijder de toepassing met de naam Azure AD Domain Services Sync en probeer vervolgens Domain Services in te scha kelen voor uw Azure AD-Tenant.*

**Oplossing**

Controleer of er een toepassing met de naam *Azure AD Domain Services synchronisatie* in uw Azure AD-adres lijst is. Als deze toepassing bestaat, verwijdert u deze en probeert u Azure AD DS in te scha kelen. Voer de volgende stappen uit om een bestaande toepassing te controleren en deze indien nodig te verwijderen:

1. Selecteer in de Azure Portal **Azure Active Directory** in het navigatie menu aan de linkerkant.
1. Selecteer **Enterprise-toepassingen**. Kies *alle toepassingen* in de vervolg keuzelijst **toepassings type** en selecteer vervolgens **Toep assen**.
1. Voer *Azure AD Domain Services Sync* in het zoekvak in. Als de toepassing bestaat, selecteert u deze en kiest u **verwijderen**.
1. Nadat u de toepassing hebt verwijderd, probeert u Azure AD DS opnieuw in te scha kelen.

### <a name="invalid-configuration"></a>Ongeldige configuratie

**Fout bericht**

*Domain Services kan niet worden ingeschakeld in deze Azure AD-Tenant. De domein Services-toepassing in uw Azure AD-Tenant beschikt niet over de vereiste machtigingen om domein Services in te scha kelen. Verwijder de toepassing met de toepassings-id d87dcbc6-a371-462e-88e3-28ad15ec4e64 en probeer vervolgens Domain Services in te scha kelen voor uw Azure AD-Tenant.*

**Oplossing**

Controleer of u een bestaande toepassing met de naam *AzureActiveDirectoryDomainControllerServices* hebt met een toepassings-id van *D87dcbc6-a371-462e-88e3-28ad15ec4e64* in uw Azure AD-adres lijst. Als deze toepassing bestaat, verwijdert u deze en probeert u Azure AD DS in te scha kelen.

Gebruik het volgende Power shell-script om te zoeken naar een bestaande toepassings instantie en verwijder deze indien nodig:

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph uitgeschakeld

**Fout bericht**

*Domain Services kan niet worden ingeschakeld in deze Azure AD-Tenant. De Microsoft Azure AD toepassing is uitgeschakeld in uw Azure AD-Tenant. Schakel de toepassing met de toepassings-id 00000002-0000-0000-C000-000000000000 te gebruiken in en probeer vervolgens Domain Services in te scha kelen voor uw Azure AD-Tenant.*

**Oplossing**

Controleer of u een toepassing hebt uitgeschakeld met de id *00000002-0000-0000-C000-000000000000 te gebruiken*. Deze toepassing is de Microsoft Azure AD toepassing en biedt Graph API toegang tot uw Azure AD-Tenant. Als u uw Azure AD-Tenant wilt synchroniseren, moet u deze toepassing inschakelen.

Voer de volgende stappen uit om de status van deze toepassing te controleren en zo nodig in te scha kelen:

1. Selecteer in de Azure Portal **Azure Active Directory** in het navigatie menu aan de linkerkant.
1. Selecteer **Enterprise-toepassingen**. Kies *alle toepassingen* in de vervolg keuzelijst **toepassings type** en selecteer vervolgens **Toep assen**.
1. Voer *00000002-0000-0000-C000-00000000000* in het zoekvak in. Selecteer de toepassing en kies vervolgens **Eigenschappen**.
1. Als u deze optie **inschakelt voor gebruikers die zich willen aanmelden** *, stelt u de* waarde in op *Ja* en selecteert u **Opslaan**.
1. Nadat u de toepassing hebt ingeschakeld, probeert u Azure AD DS opnieuw in te scha kelen.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Gebruikers kunnen zich niet aanmelden bij het in Azure AD Domain Services beheerde domein

Als een of meer gebruikers in uw Azure AD-Tenant zich niet kunnen aanmelden bij het beheerde domein, voert u de volgende stappen uit om het probleem te verhelpen:

* **Indeling van referenties** : Probeer de UPN-indeling om referenties op te geven, zoals `dee@aaddscontoso.onmicrosoft.com` . De UPN-indeling is de aanbevolen manier om referenties op te geven in azure AD DS. Zorg ervoor dat deze UPN juist is geconfigureerd in azure AD.

    De *SAMAccountName* voor uw account, zoals *AADDSCONTOSO\driley* , kan automatisch worden gegenereerd als er meerdere gebruikers zijn met hetzelfde UPN-voor voegsel in uw TENANT of als uw UPN-voor voegsel langer is dan lang. Daarom kan de *SAMAccountName* -indeling voor uw account afwijken van wat u verwacht of gebruikt in uw on-premises domein.

* **Wachtwoord synchronisatie** : Zorg ervoor dat u wachtwoord synchronisatie hebt ingeschakeld voor [Cloud gebruikers][cloud-only-passwords] of voor [hybride omgevingen met behulp van Azure AD CONNECT][hybrid-phs].
    * **Hybrid Synchronized accounts:** Als de betrokken gebruikers accounts worden gesynchroniseerd vanuit een on-premises map, controleert u de volgende gebieden:
    
      * U hebt de [meest recente aanbevolen versie van Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)geïmplementeerd of bijgewerkt naar.
      * U hebt Azure AD Connect geconfigureerd om [een volledige synchronisatie uit te voeren][hybrid-phs].
      * Afhankelijk van de grootte van uw map kan het enige tijd duren voordat gebruikers accounts en referentie-hashes beschikbaar zijn in het beheerde domein. Zorg ervoor dat u lang genoeg wacht voordat u probeert te verifiëren op basis van het beheerde domein.
      * Als het probleem zich blijft voordoen nadat u de vorige stappen hebt gecontroleerd, start u de *Microsoft Azure AD Sync-Service* opnieuw op. Open vanaf uw Azure AD Connect-server een opdracht prompt en voer de volgende opdrachten uit:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Alleen Cloud accounts**: als het betrokken gebruikers account een alleen-Cloud gebruikers account is, moet u ervoor zorgen dat de [gebruiker zijn of haar wacht woord heeft gewijzigd nadat u Azure AD DS hebt ingeschakeld][cloud-only-passwords]. Bij het opnieuw instellen van het wacht woord worden de vereiste referentie-hashes voor het beheerde domein gegenereerd.

* **Controleer of het gebruikers account actief is**: standaard hebben vijf ongeldige wachtwoord pogingen binnen twee minuten op het beheerde domein tot gevolg dat een gebruikers account 30 minuten wordt vergrendeld. De gebruiker kan zich niet aanmelden als het account is vergrendeld. Na 30 minuten wordt het gebruikers account automatisch ontgrendeld.
  * Bij ongeldige wachtwoord pogingen op het beheerde domein wordt het gebruikers account in azure AD niet vergrendeld. Het gebruikers account is alleen vergrendeld binnen het beheerde domein. Controleer de status van het gebruikers account in de *Active Directory-beheer console (ADAC)* met behulp van de [beheer-VM][management-vm], niet in azure AD.
  * U kunt ook een [verfijnd wachtwoord beleid configureren][password-policy] om de standaard drempelwaarde en duur van de vergren deling te wijzigen.

* **Externe accounts** : Controleer of het betrokken gebruikers account geen extern account is in de Azure AD-Tenant. Voor beelden van externe accounts zijn micro soft-accounts zoals `dee@live.com` of gebruikers accounts uit een externe Azure AD-adres lijst. Azure AD DS slaat geen referenties op voor externe gebruikers accounts zodat ze zich niet kunnen aanmelden bij het beheerde domein.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Er zijn een of meer waarschuwingen voor uw beheerde domein

Als er actieve waarschuwingen op het beheerde domein aanwezig zijn, kan het zijn dat het verificatie proces niet goed werkt.

[Controleer de status van een beheerd domein][check-health]om te zien of er actieve waarschuwingen zijn. Als er waarschuwingen worden weer gegeven, moet u [problemen oplossen en oplossen][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Gebruikers die uit uw Azure AD-tenant zijn verwijderd, worden niet verwijderd uit uw beheerde domein

Azure AD beveiligt tegen onbedoeld verwijderen van gebruikers objecten. Wanneer u een gebruikers account uit een Azure AD-Tenant verwijdert, wordt het bijbehorende gebruikers object verplaatst naar de Prullenbak. Wanneer deze Verwijder bewerking is gesynchroniseerd met uw beheerde domein, wordt het bijbehorende gebruikers account gemarkeerd als uitgeschakeld. Met deze functie kunt u het gebruikers account herstellen of de verwijdering ongedaan maken.

Het gebruikers account blijft uitgeschakeld in het beheerde domein, zelfs als u opnieuw een gebruikers account met dezelfde UPN maakt in de Azure AD-adres lijst. Als u het gebruikers account uit het beheerde domein wilt verwijderen, moet u het verwijderen uit de Azure AD-Tenant.

Als u een gebruikers account volledig uit een beheerd domein wilt verwijderen, verwijdert u de gebruiker permanent uit uw Azure AD-Tenant met behulp van de Power shell [-cmdlet Remove-MsolUser][Remove-MsolUser] met de `-RemoveFromRecycleBin` para meter.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
