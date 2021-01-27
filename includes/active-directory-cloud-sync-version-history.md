---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900868"
---
Dit artikel bevat een overzicht van de versies en functies van Azure Active Directory Connect Provisioning agent die is uitgebracht. Het Azure AD-team werkt de inrichtings agent regel matig bij met nieuwe functies en functionaliteit. De inrichtings agent wordt automatisch bijgewerkt wanneer er een nieuwe versie wordt uitgebracht. 

Micro soft biedt direct ondersteuning voor de nieuwste versie van de agent en één versie voor.

## <a name="113540"></a>1.1.354.0

20 januari 2021: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen
- Verbetering van de GMSA-ervaring, inclusief ondersteuning voor vooraf gemaakte GMSA-accounts
- Ondersteuning van [Power shell-cmdlets](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) voor GMSA-installatie
- [CLI-ondersteuning](../articles/active-directory/cloud-sync/how-to-install-pshell.md) voor Agent installatie (installatie op de achtergrond)
- Aanvullende diagnostische gegevens voor problemen met agent bron quarantaine
- Fout oplossingen waarbij minder geheugen wordt gebruikt voor het bereik van OE-filters, waarbij PHS alleen worden uitgevoerd voor gebruikers in de scope, het verwerken van geneste objecten in OE wanneer OE-scopes worden gebruikt. 


### <a name="fixed-issues"></a>Opgeloste problemen
-    Quarantaine voor komen wanneer de bereik groep buiten het bereik valt
-   Wanneer bereik filters zijn geconfigureerd-PHS-taak nu alleen werkt voor gebruikers die in de scope zijn
-   De agent loopt tijdens de upgrade ergens bij
-   Initiële synchronisatie voor objecten in geneste organisatie-eenheden bij gebruik van OE-Scope
-   De Repair-AADCloudSyncToolsAccount robuuster maken
-   Verminder het grote geheugen gebruik van het bereik van de OE-filters
-   Controle van beheerdersrol mislukt als de leden van de rol een beveiligings groep bevatten
-   Het machtigings probleem van de GMSA oplossen waardoor het vernieuwen van het agent certificaat wordt voor komen







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Status van de release

23 november 2020: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Ondersteuning voor [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Ondersteuning voor groepen tot een grootte van minder dan 1500 leden tijdens incrementele of Delta synchronisatie cyclus. Dit is van toepassing wanneer u het filter groeps bereik gebruikt
* Ondersteuning voor grote groepen met een grootte van Maxi maal 15.000
* Verbeteringen van de initiële synchronisatie
* Geavanceerde uitgebreide logboek registratie
* Introductie van de [Power shell-module AADCloudSyncTools](../articles/active-directory/cloud-sync/reference-powershell.md)
* Vaste beperkingen zodat agent kan worden geïnstalleerd in een niet-Engelse server
* Ondersteuning voor PHS-filtering alleen voor objecten in het bereik (oorspronkelijk hebben we wacht woord-hashes voor alle objecten gesynchroniseerd)
* Het geheugenlek probleem in de agent is opgelost
* Verbeterde inrichtings logboeken
* Ondersteuning voor het configureren van [time-out voor LDAP-verbindingen](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Ondersteuning voor het configureren van [referentie Chasing](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Status van de release

4 december 2019: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

* Biedt ondersteuning voor [Azure AD Connect Cloud synchronisatie](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) om gebruikers te synchroniseren, gegevens van on-premises Active Directory te maken voor Azure AD


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