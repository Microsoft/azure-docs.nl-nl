---
ms.openlocfilehash: 46c27fa2073ea1178fdf01ea3cbf96fe86954a6a
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255121"
---
Dit artikel bevat een overzicht van de versies en functies van Azure Active Directory Connect Provisioning agent die is uitgebracht. Het Azure AD-team werkt de inrichtings agent regel matig bij met nieuwe functies en functionaliteit. De inrichtings agent wordt automatisch bijgewerkt wanneer er een nieuwe versie wordt uitgebracht. 

Micro soft biedt direct ondersteuning voor de nieuwste versie van de agent en één versie voor.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Status van de release

Van november XX, 2020: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Ondersteuning voor [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)
* Ondersteuning voor groepen tot een grootte van 1500 leden (tijdens Delta synchronisatie)
* Ondersteuning voor grote groepen met een grootte van Maxi maal 15.000
* Verbeteringen van de initiële synchronisatie
* Geavanceerde uitgebreide logboek registratie
* Introductie van de [Power shell-module AADCloudSyncTools](../articles/active-directory/cloud-provisioning/reference-powershell.md)
* Vaste beperkingen zodat agent kan worden geïnstalleerd in een niet-Engelse server
* Ondersteuning voor PHS-filtering alleen voor objecten in het bereik (oorspronkelijk hebben we wacht woord-hashes voor alle objecten gesynchroniseerd)
* Het geheugenlek probleem in de agent is opgelost
* Verbeterde inrichtings logboeken


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Status van de release

4 december 2019: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Biedt ondersteuning voor [Azure AD Connect Cloud inrichting](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) voor het synchroniseren van gebruikers, het contact opnemen en groeperen van gegevens van on-premises Active Directory naar Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Status van de release

9 september 2019: uitgebracht voor automatische updates

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Mogelijkheid om extra tracering en logboek registratie te configureren voor problemen met het inrichten van inrichtings agent
* De mogelijkheid om alleen de Azure AD-kenmerken op te halen die zijn geconfigureerd in de toewijzing om de prestaties van synchronisatie te verbeteren

### <a name="fixed-issues"></a>Opgeloste problemen

* Er is een fout opgelost waarbij de agent niet meer reageert als er problemen zijn met Azure AD-verbindings fouten
* Er is een fout opgelost die problemen heeft veroorzaakt tijdens het lezen van binaire gegevens uit Azure Active Directory
* Er is een fout opgelost waarbij de agent geen vertrouwens relatie kan vernieuwen met de Cloud-Hybrid Identity-service

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Status van de release

23 januari 2019: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* De inrichtings agent en connector architectuur zijn vernieuwd voor betere prestaties, stabiliteit en betrouw baarheid 
* Vereenvoudigde configuratie van de inrichtings agent met behulp van de wizard voor de installatie van de gebruikers interface 
* Er is ondersteuning toegevoegd voor automatische agent updates


