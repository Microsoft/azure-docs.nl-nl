---
title: Veelvoorkomende waarschuwingen en oplossingen in Azure AD Domain Services | Microsoft Docs
description: Informatie over het oplossen van veelvoorkomende waarschuwingen die zijn gegenereerd als onderdeel van de integriteits status voor Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 80011b2ae2a70ce4c540ac31897cc8be4a6580c3
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618617"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Bekende problemen: gemeen schappelijke waarschuwingen en oplossingen in Azure Active Directory Domain Services

Als centraal onderdeel van identiteits-en verificatie voor toepassingen, Azure Active Directory Domain Services (Azure AD DS) soms problemen ondervindt. Als u problemen ondervindt, zijn er enkele veelvoorkomende waarschuwingen en de bijbehorende stappen voor probleem oplossing om u te helpen met het uitvoeren van taken. U kunt op elk gewenst moment ook [een ondersteunings aanvraag voor Azure openen][azure-support] voor aanvullende hulp bij het oplossen van problemen.

Dit artikel bevat informatie over het oplossen van problemen met algemene waarschuwingen in azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: ontbrekende map

### <a name="alert-message"></a>Waarschuwings bericht

*De Azure AD-Directory die aan uw beheerde domein is gekoppeld, is mogelijk verwijderd. Het beheerde domein bevindt zich niet meer in een ondersteunde configuratie. Micro soft kan uw beheerde domein niet bewaken, beheren, patchen en synchroniseren.*

### <a name="resolution"></a>Oplossing

Deze fout treedt meestal op wanneer een Azure-abonnement wordt verplaatst naar een nieuwe Azure AD-adres lijst en de oude Azure AD-adres lijst die is gekoppeld aan Azure AD DS wordt verwijderd.

Deze fout kan niet worden hersteld. U kunt de waarschuwing oplossen door [uw bestaande beheerde domein te verwijderen](delete-aadds.md) en opnieuw te maken in de nieuwe map. Als u problemen ondervindt bij het verwijderen van het beheerde domein, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij probleem oplossing.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C wordt uitgevoerd in deze map

### <a name="alert-message"></a>Waarschuwings bericht

*Azure AD Domain Services kan niet worden ingeschakeld in een Azure AD B2C map.*

### <a name="resolution"></a>Oplossing

Azure AD DS wordt automatisch gesynchroniseerd met een Azure AD-adres lijst. Als de Azure AD-Directory is geconfigureerd voor B2C, kan Azure AD DS niet worden geïmplementeerd en gesynchroniseerd.

Als u Azure AD DS wilt gebruiken, moet u uw beheerde domein opnieuw maken in een niet-Azure AD B2C Directory door de volgende stappen uit te voeren:

1. [Verwijder het beheerde domein](delete-aadds.md) uit uw bestaande Azure AD-adres lijst.
1. Maak een nieuwe Azure AD-Directory die geen Azure AD B2C Directory is.
1. [Maak een vervangend beheerd domein](tutorial-create-instance.md).

De status van het beheerde domein wordt automatisch bijgewerkt binnen twee uur en de waarschuwing wordt verwijderd.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: het adres bevindt zich in een openbaar IP-bereik

### <a name="alert-message"></a>Waarschuwings bericht

*Het IP-adres bereik voor het virtuele netwerk waarin u Azure AD Domain Services hebt ingeschakeld, bevindt zich in een openbaar IP-bereik. Azure AD Domain Services moet zijn ingeschakeld in een virtueel netwerk met een privé-IP-adres bereik. Deze configuratie is van invloed op de mogelijkheid van micro soft om uw beheerde domein te bewaken, te beheren, te patchen en te synchroniseren.*

### <a name="resolution"></a>Oplossing

Zorg ervoor dat u bekend bent met de [persoonlijke IP v4-adres ruimten](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)voordat u begint.

In een virtueel netwerk kunnen Vm's aanvragen indienen voor Azure-resources in hetzelfde IP-adres bereik als geconfigureerd voor het subnet. Als u een openbaar IP-adres bereik voor een subnet configureert, kunnen aanvragen die in een virtueel netwerk worden gerouteerd, de beoogde webbronnen niet bereiken. Deze configuratie kan leiden tot onvoorspelbare fouten met Azure AD DS.

> [!NOTE]
> Als u het IP-adres bereik in het Internet hebt dat is geconfigureerd in het virtuele netwerk, kan deze waarschuwing worden genegeerd. Azure AD Domain Services kan echter niet door voeren in de [Sla](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) met deze configuratie, omdat deze kan leiden tot onvoorspelbare fouten.

U kunt deze waarschuwing oplossen door uw bestaande beheerde domein te verwijderen en opnieuw te maken in een virtueel netwerk met een privé-IP-adres bereik. Dit proces is verstoord omdat het beheerde domein niet beschikbaar is en alle aangepaste resources die u hebt gemaakt, zoals Ou's of service accounts, verloren gaan.

1. [Verwijder het beheerde domein](delete-aadds.md) uit uw Directory.
1. Als u het IP-adres bereik van het virtuele netwerk wilt bijwerken, zoekt en selecteert u *virtueel netwerk* in de Azure Portal. Selecteer het virtuele netwerk voor Azure AD DS waarvoor ten onrechte een openbaar IP-adres bereik is ingesteld.
1. Selecteer bij **instellingen** de optie *adres ruimte*.
1. Werk het adres bereik bij door het bestaande adres bereik te kiezen en te bewerken, of een extra adres bereik toe te voegen. Zorg ervoor dat het nieuwe IP-adres bereik zich in een privé-IP-bereik bevindt. Als u klaar bent, **slaat** u de wijzigingen op.
1. Selecteer **subnetten** in de linkernavigatiebalk.
1. Kies het subnet dat u wilt bewerken of maak een extra subnet.
1. Een privé-IP-adres bereik bijwerken of opgeven en vervolgens uw wijzigingen **Opslaan** .
1. [Maak een vervangend beheerd domein](tutorial-create-instance.md). Zorg ervoor dat u het bijgewerkte subnet van het virtuele netwerk met een privé-IP-adres bereik selecteert.

De status van het beheerde domein wordt automatisch bijgewerkt binnen twee uur en de waarschuwing wordt verwijderd.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: uw Azure-abonnement is niet gevonden

### <a name="alert-message"></a>Waarschuwings bericht

*Uw Azure-abonnement dat is gekoppeld aan uw beheerde domein, is verwijderd.  Azure AD Domain Services moet een actief abonnement zijn om goed te kunnen werken.*

### <a name="resolution"></a>Oplossing

Azure AD DS vereist een actief abonnement en kan niet worden verplaatst naar een ander abonnement. Als het Azure-abonnement waaraan het beheerde domein is gekoppeld, is verwijderd, moet u een Azure-abonnement en een beheerd domein opnieuw maken.

1. [Maak een Azure-abonnement](../cost-management-billing/manage/create-subscription.md).
1. [Verwijder het beheerde domein](delete-aadds.md) uit uw bestaande Azure AD-adres lijst.
1. [Maak een vervangend beheerd domein](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: uw Azure-abonnement is uitgeschakeld

### <a name="alert-message"></a>Waarschuwings bericht

*Uw Azure-abonnement dat is gekoppeld aan uw beheerde domein, is niet actief.  Azure AD Domain Services moet een actief abonnement zijn om goed te kunnen werken.*

### <a name="resolution"></a>Oplossing

Voor Azure AD DS is een actief abonnement vereist. Als het Azure-abonnement waaraan het beheerde domein is gekoppeld, niet actief is, moet u het vernieuwen om het abonnement opnieuw te activeren.

1. [Verleng uw Azure-abonnement](../cost-management-billing/manage/subscription-disabled.md).
2. Zodra het abonnement is vernieuwd, kunt u met een melding van Azure AD DS het beheerde domein opnieuw inschakelen.

Wanneer het beheerde domein opnieuw wordt ingeschakeld, wordt de status van het beheerde domein automatisch bijgewerkt binnen twee uur en wordt de waarschuwing verwijderd.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: door abonnement verplaatste directory's

### <a name="alert-message"></a>Waarschuwings bericht

*Het abonnement dat door Azure AD Domain Services wordt gebruikt, is verplaatst naar een andere map. Azure AD Domain Services moet een actief abonnement hebben in dezelfde map om goed te kunnen functioneren.*

### <a name="resolution"></a>Oplossing

Azure AD DS vereist een actief abonnement en kan niet worden verplaatst naar een ander abonnement. Als het Azure-abonnement waaraan het beheerde domein is gekoppeld, is verplaatst, verplaatst u het abonnement terug naar de vorige map of [verwijdert u het beheerde domein](delete-aadds.md) uit de bestaande map en [maakt u een vervangend beheerd domein in het gekozen abonnement](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: kan geen resources vinden voor uw beheerde domein

### <a name="alert-message"></a>Waarschuwings bericht

*Een resource die wordt gebruikt voor uw beheerde domein, is verwijderd. Deze resource is nodig om Azure AD Domain Services goed te laten functioneren.*

### <a name="resolution"></a>Oplossing

Azure AD DS maakt extra resources om goed te werken, zoals open bare IP-adressen, virtuele netwerk interfaces en een load balancer. Als een van deze resources wordt verwijderd, heeft het beheerde domein de status niet-ondersteund en voor komt u dat het domein wordt beheerd. Zie [netwerk bronnen die door Azure AD DS worden gebruikt](network-considerations.md#network-resources-used-by-azure-ad-ds)voor meer informatie over deze resources.

Deze waarschuwing wordt gegenereerd wanneer een van deze vereiste bronnen wordt verwijderd. Als de resource minder dan vier uur geleden is verwijderd, is er een kans dat het Azure-platform de verwijderde resource automatisch opnieuw kan maken. In de volgende stappen wordt uitgelegd hoe u de status en tijds tempel controleert voor het verwijderen van resources:

1. Zoek en selecteer in het Azure Portal **Domain Services**. Kies uw beheerde domein, bijvoorbeeld *aaddscontoso.com*.
1. Selecteer in de navigatie balk aan de linkerkant de optie **status**.
1. Selecteer op de pagina status de waarschuwing met de ID *AADDS109*.
1. De waarschuwing heeft een tijds tempel voor de eerste keer dat deze is gevonden. Als de tijds tempel minder dan vier uur geleden is, kan het Azure-platform de resource mogelijk automatisch opnieuw maken en de waarschuwing zelf oplossen.

    Als de waarschuwing meer dan vier uur oud is, heeft het beheerde domein een onherstelbare status. [Verwijder het beheerde domein](delete-aadds.md) en [Maak vervolgens een vervangend beheerd domein](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: het subnet dat is gekoppeld aan uw beheerde domein is vol

### <a name="alert-message"></a>Waarschuwings bericht

*Het subnet dat is geselecteerd voor de implementatie van Azure AD Domain Services, is vol en heeft geen ruimte voor de extra domein controller die moet worden gemaakt.*

### <a name="resolution"></a>Oplossing

Het subnet van het virtuele netwerk voor Azure AD DS vereist voldoende IP-adressen voor de automatisch gemaakte resources. Deze IP-adres ruimte bevat de nood zaak om vervangings resources te maken als er een onderhouds gebeurtenis is. Als u het risico van het uitvoeren van beschik bare IP-adressen wilt minimaliseren, kunt u geen aanvullende resources, zoals uw eigen Vm's, implementeren in hetzelfde subnet van het virtuele netwerk als het beheerde domein.

Deze fout kan niet worden hersteld. U kunt de waarschuwing oplossen door [uw bestaande beheerde domein te verwijderen](delete-aadds.md) en opnieuw te maken. Als u problemen ondervindt bij het verwijderen van het beheerde domein, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij probleem oplossing.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Service-Principal niet toegestaan

### <a name="alert-message"></a>Waarschuwings bericht

*Een service-principal die Azure AD Domain Services gebruikt voor het onderhouden van uw domein is niet gemachtigd om resources te beheren in het Azure-abonnement. De Service-Principal moet machtigingen krijgen om uw beheerde domein te onderhouden.*

### <a name="resolution"></a>Oplossing

Sommige automatisch gegenereerde service-principals worden gebruikt voor het beheren en maken van resources voor een beheerd domein. Als de toegangs machtigingen voor een van deze service-principals zijn gewijzigd, kan het domein de resources niet op de juiste manier beheren. In de volgende stappen wordt uitgelegd hoe u toegangs machtigingen voor een Service-Principal begrijpt en vervolgens verkent:

1. Meer informatie over [op rollen gebaseerd toegangs beheer en het verlenen van toegang tot toepassingen in de Azure Portal](../role-based-access-control/role-assignments-portal.md).
2. Controleer de toegang die de service-principal met de ID *abba844e-bc0e-44b0-947a-dc74e5d09022* heeft en verleen de toegang die op een eerdere datum is geweigerd.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: er is onvoldoende IP-adres in het beheerde domein

### <a name="alert-message"></a>Waarschuwings bericht

*Er is vastgesteld dat het subnet van het virtuele netwerk in dit domein mogelijk niet voldoende IP-adressen heeft. Azure AD Domain Services moet mini maal twee beschik bare IP-adressen hebben in het subnet waarin deze is ingeschakeld. U kunt het beste ten minste 3-5 reserved IP-adressen binnen het subnet hebben. Dit kan zijn gebeurd als andere virtuele machines worden geïmplementeerd in het subnet, waardoor het aantal beschik bare IP-adressen wordt uitgeput of als er een beperking geldt voor het aantal beschik bare IP-adressen in het subnet.*

### <a name="resolution"></a>Oplossing

Het subnet van het virtuele netwerk voor Azure AD DS vereist voldoende IP-adressen voor de automatisch gemaakte resources. Deze IP-adres ruimte bevat de nood zaak om vervangings resources te maken als er een onderhouds gebeurtenis is. Als u het risico van het uitvoeren van beschik bare IP-adressen wilt minimaliseren, kunt u geen aanvullende resources, zoals uw eigen Vm's, implementeren in hetzelfde subnet van het virtuele netwerk als het beheerde domein.

U kunt deze waarschuwing oplossen door uw bestaande beheerde domein te verwijderen en opnieuw te maken in een virtueel netwerk met een groot genoeg IP-adres bereik. Dit proces is verstoord omdat het beheerde domein niet beschikbaar is en alle aangepaste resources die u hebt gemaakt, zoals Ou's of service accounts, verloren gaan.

1. [Verwijder het beheerde domein](delete-aadds.md) uit uw Directory.
1. Als u het IP-adres bereik van het virtuele netwerk wilt bijwerken, zoekt en selecteert u *virtueel netwerk* in de Azure Portal. Selecteer het virtuele netwerk voor het beheerde domein met het kleine IP-adres bereik.
1. Selecteer bij **instellingen** de optie *adres ruimte*.
1. Werk het adres bereik bij door het bestaande adres bereik te kiezen en te bewerken, of een extra adres bereik toe te voegen. Zorg ervoor dat het nieuwe IP-adres bereik groot genoeg is voor het subnet van het beheerde domein. Als u klaar bent, **slaat** u de wijzigingen op.
1. Selecteer **subnetten** in de linkernavigatiebalk.
1. Kies het subnet dat u wilt bewerken of maak een extra subnet.
1. Update of geef een groot voldoende IP-adres bereik op en **Sla** uw wijzigingen op.
1. [Maak een vervangend beheerd domein](tutorial-create-instance.md). Zorg ervoor dat u het bijgewerkte subnet van het virtuele netwerk met een groot voldoende IP-adres bereik selecteert.

De status van het beheerde domein wordt automatisch bijgewerkt binnen twee uur en de waarschuwing wordt verwijderd.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: bronnen kunnen niet worden hersteld

### <a name="alert-message"></a>Waarschuwings bericht

*De resources die door Azure AD Domain Services worden gebruikt, zijn in een onverwachte status gedetecteerd en kunnen niet worden hersteld.*

### <a name="resolution"></a>Oplossing

Deze fout kan niet worden hersteld. U kunt de waarschuwing oplossen door [uw bestaande beheerde domein te verwijderen](delete-aadds.md) en opnieuw te maken. Als u problemen ondervindt bij het verwijderen van het beheerde domein, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij probleem oplossing.

## <a name="aadds114-subnet-invalid"></a>AADDS114: subnet ongeldig

### <a name="alert-message"></a>Waarschuwings bericht

*Het subnet dat is geselecteerd voor de implementatie van Azure AD Domain Services, is ongeldig en kan niet worden gebruikt.*

### <a name="resolution"></a>Oplossing

Deze fout kan niet worden hersteld. U kunt de waarschuwing oplossen door [uw bestaande beheerde domein te verwijderen](delete-aadds.md) en opnieuw te maken. Als u problemen ondervindt bij het verwijderen van het beheerde domein, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij probleem oplossing.

## <a name="aadds115-resources-are-locked"></a>AADDS115: resources zijn vergrendeld

### <a name="alert-message"></a>Waarschuwings bericht

*Een of meer netwerk bronnen die door het beheerde domein worden gebruikt, kunnen niet worden uitgevoerd omdat het doel bereik is vergrendeld.*

### <a name="resolution"></a>Oplossing

Resource vergrendelingen kunnen worden toegepast op Azure-resources om te voor komen dat ze worden gewijzigd of verwijderd. Omdat Azure AD DS een beheerde service is, heeft het Azure-platform de mogelijkheid om configuratie wijzigingen aan te brengen. Als een resource vergrendeling wordt toegepast op een aantal van de onderdelen van Azure AD DS, kunnen de beheer taken van het Azure-platform niet worden uitgevoerd.

Voer de volgende stappen uit om te controleren op resource vergrendelingen op de onderdelen van Azure AD DS en ze te verwijderen:

1. Controleer de bewerkings Logboeken in de Azure Portal voor elk van de netwerk onderdelen van het beheerde domein in uw resource groep, zoals een virtueel netwerk, een netwerk interface of een openbaar IP-adres. In deze bewerkings logboeken moet worden aangegeven waarom een bewerking mislukt en waar een resource vergrendeling wordt toegepast.
1. Selecteer de resource waar een vergren deling wordt toegepast en selecteer **vervolgens vergrendelde** vergren deling (en).

## <a name="aadds116-resources-are-unusable"></a>AADDS116: bronnen zijn onbruikbaar

### <a name="alert-message"></a>Waarschuwings bericht

*Een of meer netwerk bronnen die door het beheerde domein worden gebruikt, kunnen niet worden uitgevoerd vanwege beleids beperking (en).*

### <a name="resolution"></a>Oplossing

Beleids regels worden toegepast op Azure-resources en-resource groepen die bepalen welke configuratie acties zijn toegestaan. Omdat Azure AD DS een beheerde service is, heeft het Azure-platform de mogelijkheid om configuratie wijzigingen aan te brengen. Als er een beleid wordt toegepast op een aantal van de onderdelen van Azure AD DS, kunnen de beheer taken van het Azure-platform mogelijk niet worden uitgevoerd.

Voer de volgende stappen uit om te controleren of er beleid is toegepast op de onderdelen van Azure AD DS en deze bij te werken:

1. Controleer de bewerkings Logboeken in de Azure Portal voor elk van de netwerk onderdelen van het beheerde domein in uw resource groep, zoals een virtueel netwerk, een NIC of een openbaar IP-adres. In deze bewerkings logboeken moet worden aangegeven waarom een bewerking mislukt en waar een beperkend beleid wordt toegepast.
1. Selecteer de resource waar een beleid wordt toegepast en selecteer vervolgens onder **beleids regels** het beleid en bewerk dit zodat het minder beperkend is.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: de synchronisatie is niet in een tijdje voltooid

### <a name="alert-message"></a>Waarschuwings bericht

*Het beheerde domein is voor het laatst gesynchroniseerd met Azure AD op [date]. Gebruikers kunnen zich mogelijk niet aanmelden bij het beheerde domein of groepslid maatschappen zijn mogelijk niet gesynchroniseerd met Azure AD.*

### <a name="resolution"></a>Oplossing

[Controleer de status van Azure AD DS](check-health.md) voor waarschuwingen die duiden op problemen in de configuratie van het beheerde domein. Problemen met de netwerk configuratie kunnen de synchronisatie van Azure AD blok keren. Als u waarschuwingen kunt oplossen die duiden op een configuratie probleem, wacht u twee uur en controleert u of de synchronisatie is voltooid.

De volgende veelvoorkomende oorzaken hebben tot gevolg dat synchronisatie in een beheerd domein stopt:

* De vereiste netwerk verbinding is geblokkeerd. Zie [problemen met netwerk beveiligings groepen](alert-nsg.md) en de [netwerk vereisten voor Azure AD DS](network-considerations.md)oplossen voor meer informatie over het controleren van het virtuele Azure-netwerk voor problemen en wat er is vereist.
*  Wachtwoord synchronisatie is niet ingesteld of is voltooid bij het implementeren van het beheerde domein. U kunt wachtwoord synchronisatie instellen voor [Cloud gebruikers](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) of [hybride gebruikers van on-premises](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: er is geen back-up gemaakt tijdens een tijdje

### <a name="alert-message"></a>Waarschuwings bericht

*Het beheerde domein is voor het laatst een back-up gemaakt op [date].*

### <a name="resolution"></a>Oplossing

[Controleer de status van Azure AD DS](check-health.md) voor waarschuwingen die duiden op problemen in de configuratie van het beheerde domein. Door problemen met de netwerk configuratie kan het Azure-platform verhinderen dat back-ups worden gemaakt. Als u waarschuwingen kunt oplossen die duiden op een configuratie probleem, wacht u twee uur en controleert u of de synchronisatie is voltooid.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: opschorting vanwege een uitgeschakeld abonnement

### <a name="alert-message"></a>Waarschuwings bericht

*Het beheerde domein is onderbroken omdat het Azure-abonnement dat is gekoppeld aan het domein, niet actief is.*

### <a name="resolution"></a>Oplossing

> [!WARNING]
> Als een beheerd domein gedurende lange tijd wordt onderbroken, is er een risico dat het wordt verwijderd. Los zo snel mogelijk de reden voor de onderbreking op. Zie voor meer informatie [inzicht in de onderbroken statussen voor Azure AD DS](suspension.md).

Voor Azure AD DS is een actief abonnement vereist. Als het Azure-abonnement waaraan het beheerde domein is gekoppeld, niet actief is, moet u het vernieuwen om het abonnement opnieuw te activeren.

1. [Verleng uw Azure-abonnement](../cost-management-billing/manage/subscription-disabled.md).
2. Zodra het abonnement is vernieuwd, kunt u met een melding van Azure AD DS het beheerde domein opnieuw inschakelen.

Wanneer het beheerde domein opnieuw wordt ingeschakeld, wordt de status van het beheerde domein automatisch bijgewerkt binnen twee uur en wordt de waarschuwing verwijderd.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: suspensie vanwege een ongeldige configuratie

### <a name="alert-message"></a>Waarschuwings bericht

*Het beheerde domein is onderbroken vanwege een ongeldige configuratie. De service kan de domein controllers voor uw beheerde domein lange tijd niet beheren, patchen of bijwerken.*

### <a name="resolution"></a>Oplossing

> [!WARNING]
> Als een beheerd domein gedurende lange tijd wordt onderbroken, is er een risico dat het wordt verwijderd. Los zo snel mogelijk de reden voor de onderbreking op. Zie voor meer informatie [inzicht in de onderbroken statussen voor Azure AD DS](suspension.md).

[Controleer de status van Azure AD DS](check-health.md) voor waarschuwingen die duiden op problemen in de configuratie van het beheerde domein. Als u waarschuwingen kunt oplossen die duiden op een configuratie probleem, wacht u twee uur en controleert u of de synchronisatie is voltooid. Als u klaar bent, [opent u een ondersteunings aanvraag voor Azure][azure-support] om het beheerde domein opnieuw in te scha kelen.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt, [opent u een ondersteunings aanvraag voor Azure][azure-support] voor aanvullende hulp bij het oplossen van problemen.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md