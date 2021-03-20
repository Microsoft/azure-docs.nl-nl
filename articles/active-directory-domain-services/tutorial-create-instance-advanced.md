---
title: 'Zelfstudie: een aangepast domein maken dat wordt beheerd met Active Directory Domain Services | Microsoft Docs'
description: In deze zelfstudie leert u hoe u een aangepast domein maakt en configureert dat wordt beheerd met Azure Active Directory Domain Services en hoe u geavanceerde configuratieopties opgeeft met Azure Portal.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4d03edac98329d7c47b8bfafbf4d30cfca18863c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618226"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain-with-advanced-configuration-options"></a>Zelfstudie: Een door Azure Active Directory Domain Services beheerd domein maken en configureren met geavanceerde configuratieopties

Azure AD DS (Azure Active Directory Domain Services) biedt beheerde domeinservices, zoals lid worden van een domein, groepsbeleid, LDAP, Kerberos/NTLM-verificatie, die volledig compatibel zijn met Windows Server Active Directory. U gebruikt deze domeinservices zonder zelf domeincontrollers te implementeren, te beheren en er patches op toe te passen. Azure AD DS kan met uw bestaande Azure AD-tenant worden geïntegreerd. Met deze integratie kunnen gebruikers zich aanmelden met behulp van hun bedrijfsreferenties, en u kunt bestaande groepen en gebruikersaccounts gebruiken om de toegang tot resources te beveiligen.

U kunt [een beheerd domein maken met behulp van de standaardconfiguratieopties][tutorial-create-instance] voor netwerken en synchronisatie, of deze instellingen handmatig definiëren. In deze zelfstudie ziet u hoe u die geavanceerde configuratieopties definieert om een door Azure AD DS beheerd domein te maken en te configureren met behulp van Azure Portal.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * DNS-instellingen en instellingen voor een virtueel netwerk configureren voor een beheerd domein
> * Een beheerd domein maken
> * Gebruikers met beheerdersrechten toevoegen aan domeinbeheer
> * Wachtwoord-hashsynchronisatie inschakelen

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een actief Azure-abonnement.
    * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
    * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* U hebt *bevoegdheden van een globale beheerder* voor de Azure AD-tenant nodig om Azure AD DS in te schakelen.
* U hebt bevoegdheden van een *Inzender* voor uw Azure-abonnement nodig om de vereiste Azure AD DS-resources te maken.

Hoewel het voor Azure AD DS niet is vereist, wordt het wel aangeraden om [Self-Service Password Reset (SSPR) te configureren][configure-sspr] voor de Azure AD-tenant. Gebruikers kunnen hun wachtwoord wijzigen zonder SSPR, maar met SSPR kunnen ze wel hun wachtwoord opnieuw instellen als ze dit zijn vergeten.

> [!IMPORTANT]
> Nadat u een beheerd domein hebt gemaakt, kunt u het beheerde domein vervolgens niet verplaatsen naar een andere resourcegroep, een ander virtueel netwerk, abonnement, enzovoort. Selecteer de meest geschikte abonnement, resourcegroep, regio en virtuele netwerk wanneer u het beheerde domein implementeert.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

In deze zelfstudie gaat u het beheerde domein maken en configureren met behulp van Azure Portal. Meld u eerst aan bij [Azure Portal](https://portal.azure.com) om aan de slag te gaan.

## <a name="create-a-managed-domain-and-configure-basic-settings"></a>Een beheerd domein maken en basisinstellingen configureren

Voer de volgende stappen uit om de wizard **Azure AD Domain Services inschakelen** te starten:

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
1. Voer *Domain Services* in op de zoekbalk en kies vervolgens *Azure AD Domain Services* bij de zoeksuggesties.
1. Selecteer **Maken** op de Azure AD Domain Services-pagina. De wizard **Azure AD Domain Services inschakelen** wordt gestart.
1. Selecteer het **Abonnement** voor Azure waarin u het beheerde domein wilt maken.
1. Selecteer de **Resourcegroep** waarvan het beheerde domein deel moet uitmaken. Kies voor **Nieuwe maken** of selecteer een bestaande resourcegroep.

Wanneer u een beheerd domein wilt maken, geeft u een DNS-naam op. Er is een aantal factoren waar u rekening mee moet houden wanneer u deze DNS-naam kiest:

* **Ingebouwde domeinnaam:** Standaard wordt de ingebouwde domeinnaam van de directory gebruikt (met het achtervoegsel *.onmicrosoft.com*). Als u beveiligde LDAP-toegang tot het beheerde domein wilt krijgen via een internetverbinding, kunt u geen digitaal certificaat maken om de verbinding met dit standaarddomein te beveiligen. Microsoft is de eigenaar van het domein *.onmicrosoft.com*, dus een Certificate Authority (CA) zal geen certificaat uitgeven.
* **Aangepaste domeinnamen:** De meestgebruikte methode is via het opgeven van een aangepaste domeinnaam, vaak een domein waarvan u al de eigenaar bent en waarnaar kan worden gerouteerd. Wanneer u een routeerbaar, aangepast domein gebruikt, kan verkeer op de juiste manier stromen om uw toepassingen te ondersteunen.
* **Niet-routeerbare domeinachtervoegsels:** Over het algemeen wordt aangeraden een niet-routeerbaar domeinnaamachtervoegsel, zoals *contoso.local*, te vermijden. Het achtervoegsel *.local* is niet routeerbaar en kan problemen veroorzaken met de DNS-resolutie.

> [!TIP]
> Als u een aangepaste domeinnaam maakt, wees dan voorzichtig met bestaande DNS-naamruimten. U wordt aangeraden een domeinnaam te gebruiken die losstaat van een bestaande Azure- of on-premises DNS-naamruimte.
>
> Als u bijvoorbeeld *contoso.com* als bestaande DNS-naamruimte hebt, maakt u een beheerd domein met de aangepaste domeinnaam *aaddscontoso.com*. Als u beveiligde LDAP moet gebruiken, moet u dit aangepaste domeinnaam registreren en hier eigenaar van zijn om de vereiste certificaten te genereren.
>
> Mogelijk moet u een aantal aanvullende DNS-records maken voor andere services in uw omgeving, of voorwaardelijke DNS-doorstuurservers maken tussen bestaande DNS-naamruimten in uw omgeving. Als u bijvoorbeeld een webserver hebt waar een site wordt gehost met de root-DNS-naam, kunnen naamconflicten ontstaan waardoor extra DNS-vermeldingen vereist zijn.
>
> In deze zelfstudies en artikelen met instructies wordt het aangepaste domein *aaddscontoso.com* gebruikt als een kort voorbeeld. In alle opdrachten geeft u uw eigen domeinnaam op.

De volgende DNS-naambeperkingen zijn ook van toepassing:

* **Beperkingen voor voorvoegsels voor domeinen:** U kunt geen beheerd domein maken met een voorvoegsel van meer dan 15 tekens. Het voorvoegsel van uw opgegeven domeinnaam (zoals *aaddscontoso* in de domeinnaam *aaddscontoso.com*) mag maximaal 15 tekens bevatten.
* **Conflicten met de netwerknaam:** De DNS-domeinnaam voor uw beheerde domein mag nog niet bestaan in het virtuele netwerk. Controleer met name op de volgende scenario's die kunnen leiden tot een naamconflict:
    * Of u al een Active Directory-domein met deze DNS-domeinnaam in het virtuele Azure-netwerk hebt.
    * Of het virtuele netwerk waarin u het beheerde domein wilt inschakelen, over een VPN-verbinding met uw on-premises netwerk beschikt. In dit scenario moet u ervoor zorgen dat u geen domein met dezelfde DNS-domeinnaam in uw on-premises netwerk hebt.
    * Als u een bestaande Azure-cloudservice hebt met die naam op het virtuele Azure-netwerk.

Vul de velden in het venster *Basisinstellingen* van het Azure-portal in om een beheerd domein te maken:

1. Voer een **DNS-domeinnaam** in voor uw beheerde domein, waarbij u rekening houdt met de vorige punten.
1. Kies de Azure-**locatie** waarin het beheerde domein moet worden gemaakt. Als u een regio kiest die Beschikbaarheidszones ondersteunt, worden de Azure AD DS-resources gedistribueerd over zones voor aanvullende redundantie.

    > [!TIP]
    > Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's.
    >
    > U hoeft niets te configureren voor Azure AD DS om te worden gedistribueerd over zones. De distributie van resources over zones wordt automatisch afgehandeld op het Azure-platform. Zie [Wat zijn beschikbaarheidszones in Azure?][availability-zones] voor meer informatie en om de beschikbaarheid van regio's te zien.

1. De **SKU** bepaalt de prestaties, back-upfrequentie en het maximum aantal forestvertrouwensrelaties dat u kunt maken. U kunt de SKU wijzigen zodra het beheerde domein is gemaakt wanneer uw zakelijke behoeften of vereisten veranderen. Zie [Azure AD DS SKU-concepten][concepts-sku] voor meer informatie.

    Voor deze zelfstudie selecteert u de *Standaard*-SKU.
1. Een *forest* is een logische constructie die door Active Directory Domain Services wordt gebruikt om een of meer domeinen te groeperen. Een beheerd domein wordt standaard gemaakt als een *Gebruikersforest*. Met dit type forest worden alle objecten van Azure AD gesynchroniseerd, waaronder alle gebruikersaccounts die zijn gemaakt in een on-premises AD DS-omgeving.

    Met een *resourceforest* worden alleen gebruikers en groepen gesynchroniseerd die rechtstreeks in Azure AD zijn gemaakt. Wachtwoord-hashes voor on-premises gebruikers worden nooit gesynchroniseerd naar een beheerd domein wanneer u een resourceforest maakt. Raadpleeg [Overzicht van Azure AD DS-resourceforests][resource-forests] voor meer informatie over *resourceforests*, waaronder redenen om deze te gebruiken en hoe u forestvertrouwensrelaties maakt met on-premises AD DS-domeinen.

    Voor deze zelfstudie kiest u voor het maken van een *gebruikersforest*.

    ![Basisinstellingen configureren voor een door Azure AD Domain Services beheerd domein](./media/tutorial-create-instance-advanced/basics-window.png)

1. Als u handmatig aanvullende opties wilt configureren, kiest u **Volgende - Netwerken**. Selecteer anders **Controleren en maken** om de standaardconfiguratieopties te accepteren. Ga vervolgens door naar de sectie [Uw beheerde domein implementeren](#deploy-the-managed-domain). De volgende standaarden worden geconfigureerd wanneer u deze maakoptie kiest:

    * Hiermee maakt u een virtueel netwerk met de naam *aadds-vnet* dat het IP-adresbereik van *10.0.1.0/24* gebruikt.
    * Hiermee maakt u een subnet met de naam *aadds-subnet* met behulp van het IP-adresbereik van *10.0.1.0/24*.
    * Hiermee worden *alle* gebruikers van Azure AD gesynchroniseerd met het beheerde domein.

## <a name="create-and-configure-the-virtual-network"></a>Het virtuele netwerk maken en configureren

Voor het opgeven van connectiviteit zijn een virtueel Azure-netwerk en een toegewezen subnet nodig. Azure AD DS is ingeschakeld in dit virtuele-netwerksubnet. In deze zelfstudie gaat u een virtueel netwerk maken, maar in plaats daarvan kunt u er ook voor kiezen om een bestaand virtueel netwerk te gebruiken. Bij beide methoden moet u een toegewezen subnet maken dat door Azure AD DS wordt gebruikt.

Voor dit toegewezen virtuele-netwerksubnet moet rekening gehouden worden met de volgende factoren:

* Het subnet moet ten minste 3-5 beschikbare IP-adressen in het adresbereik hebben om de Azure AD DS-resources te ondersteunen.
* Selecteer het *Gateway*-subnet niet voor het implementeren van Azure AD DS. Het implementeren van Azure AD DS in een *Gateway*-subnet wordt niet ondersteund.
* Implementeer geen andere virtuele machines in het subnet. Toepassingen en virtuele machines gebruiken vaak netwerkbeveiligingsgroepen om de connectiviteit te beveiligen. Door deze workloads in een afzonderlijk subnet uit te voeren, kunt u die netwerkbeveiligingsgroepen toepassen zonder de connectiviteit met uw beheerde domein te onderbreken.
* U kunt uw beheerde domein niet verplaatsen naar een ander virtueel netwerk nadat u Azure AD DS hebt ingeschakeld.

Zie [Netwerkoverwegingen voor Azure Active Directory Domain Services][network-considerations] voor meer informatie over het plannen en configureren van het virtuele netwerk.

Vul als volgt de velden in het venster *Netwerk* in:

1. Kies in het vervolgkeuzemenu op de pagina **Netwerk** een virtueel netwerk waarin u Azure AD DS wilt implementeren, of selecteer **Nieuwe maken**.
    1. Als u ervoor kiest om een virtueel netwerk te maken, voert u een naam in voor het virtuele netwerk, zoals *myVnet*. Geef vervolgens een adresbereik, zoals *10.0.1.0/24*, op.
    1. Maak een toegewezen subnet met een duidelijke naam, zoals *Domeinservices*. Geef een adresbereik op, zoals *10.0.1.0/24*.

    [ ![Een virtueel netwerk en subnet maken om te gebruiken voor Azure AD Domain Services](./media/tutorial-create-instance-advanced/create-vnet.png)](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Kies een adresbereik dat binnen uw persoonlijke IP-adresbereik valt. IP-adresbereiken waarvan u geen eigenaar bent en die zich in de openbare adresruimte bevinden, veroorzaken fouten in Azure AD DS.

1. Selecteer een virtuele-netwerksubnet, zoals *Domeinservices*.
1. Wanneer u klaar bent, kiest u **Volgende - Beheer**.

## <a name="configure-an-administrative-group"></a>Een beheergroep configureren

Een speciale beheergroep met de naam *AAD DC-beheerders* wordt gebruikt voor het beheer van het Azure AD DS-domein. Aan leden van deze groep worden beheermachtigingen toegewezen op virtuele machines die aan het beheerde domein zijn toegevoegd. Op aan het domein toegevoegde virtuele machines wordt deze groep toegevoegd aan de lokale beheerdersgroep. Leden van deze groep kunnen ook extern bureaublad gebruiken om op afstand verbinding te maken met aan het domein toegevoegde virtuele machines.

> [!IMPORTANT]
> Met Azure AD DS beschikt u niet over de machtigingen voor *Domeinbeheerder* of *Enterprise-beheerder* op een beheerd domein. Deze machtigingen zijn gereserveerd door de service en worden niet beschikbaar gesteld voor gebruikers in de tenant.
>
> In plaats daarvan kunt u in de groep *AAD DC-beheerders* een aantal bevoorrechte bewerkingen uitvoeren. Deze bewerkingen zijn onder andere het toevoegen aan de beheergroep op aan het domein toegevoegde virtuele machines en het configureren van groepsbeleid.

De wizard maakt automatisch de groep *AAD DC-beheerders* in uw Azure AD-directory. Als u een bestaande groep met deze naam in uw Azure AD-directory hebt, wordt met de wizard deze groep geselecteerd. Indien gewenst, kunt u ervoor kiezen om aanvullende gebruikers aan deze groep *AAD DC-beheerders* toe te voegen tijdens het implementatieproces. Deze stappen kunnen later worden voltooid.

1. Als u aanvullende gebruikers aan deze groep *AAD DC-beheerders* wilt toevoegen, selecteert u **Groepslidmaatschap beheren**.

    ![Groepslidmaatschap van de groep AAD DC-beheerders configureren](./media/tutorial-create-instance-advanced/admin-group.png)

1. Selecteer de knop **Leden toevoegen** en zoek vervolgens naar en selecteer gebruikers uit uw Azure AD-directory. Zoek bijvoorbeeld naar uw eigen account en voeg deze toe aan de groep *AAD DC-beheerders*.
1. Indien gewenst kunt u aanvullende ontvangers voor meldingen wijzigen of toevoegen wanneer er meldingen in het beheerde domein zijn die uw aandacht eisen.
1. Wanneer u klaar bent, kiest u **Volgende - Synchronisatie**.

## <a name="configure-synchronization"></a>Synchronisatie configureren

In Azure AD DS kunt u *alle* gebruikers en groepen synchroniseren die beschikbaar zijn in Azure AD, of een synchronisatie *met een bereik* van alleen specifieke groepen. U kunt het synchronisatiebereik nu wijzigen of wanneer het beheerde domein is geïmplementeerd. Zie [Synchronisatie met een bereik in Azure AD Domain Services][scoped-sync] voor meer informatie.

1. Voor deze zelfstudie kiest u ervoor om **alle** gebruikers en groepen te synchroniseren. Deze synchronisatiekeuze is de standaardoptie.

    ![Een volledige synchronisatie van gebruikers en groepen uitvoeren vanuit Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Selecteer **Controleren + maken**.

## <a name="deploy-the-managed-domain"></a>Het beheerde domein implementeren

Ga naar de pagina **Samenvatting** van de wizard om de configuratie-instellingen voor uw beheerde domein te controleren. U kunt teruggaan naar elke stap van de wizard om wijzigingen door te voeren. Als u een beheerd domein opnieuw en consistent wilt implementeren in een andere Azure AD-tenant met behulp van deze configuratieopties, dan kunt u ook **Een sjabloon downloaden voor automatisering**.

1. Voor het maken van het beheerde domein selecteert u **Maken**. Er wordt een opmerking weergegeven dat bepaalde configuratieopties, zoals de DNS-naam of een virtueel netwerk, niet kunnen worden gewijzigd zodra het beheerde Azure AD DS-domein is gemaakt. Selecteer **OK** om door te gaan.
1. Het inrichtingsproces van uw beheerde domein duurt ongeveer een uur. Er wordt een melding weergegeven in het portal met de voortgang van uw Azure AD DS-implementatie. Selecteer de melding om gedetailleerde voortgangsinformatie voor de implementatie weer te geven.

    ![Melding in Azure Portal van de voortgang van de implementatie](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Selecteer uw resourcegroep, zoals *myResourceGroup* en kies vervolgens uw beheerde domein uit de lijst met Azure-resources, zoals *aaddscontoso.com*. Op het tabblad **Overzicht** ziet u dat het beheerde domein momenteel *Wordt geïmplementeerd*. U kunt het beheerde domein pas configureren wanneer dit volledig is ingericht.

    ![De status van Domain Services tijdens de inrichtingsfase](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Wanneer het beheerde domein volledig is ingericht, wordt de domeinstatus op het tabblad **Overzicht** weergegeven als *Wordt uitgevoerd*.

    ![Status van Domain Services zodra het domein is ingericht](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

> [!IMPORTANT]
> Het beheerde domein is gekoppeld aan uw Azure AD-tenant. Tijdens het inrichtingsproces worden in Azure AD DS twee Enterprise-toepassingen met de naam *Domain Controller Services* en *AzureActiveDirectoryDomainControllerServices* gemaakt in de Azure AD-tenant. Deze Enterprise-toepassingen zijn nodig voor het onderhoud van uw beheerde domein. Verwijder deze toepassingen niet.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>DNS-instellingen bijwerken voor het virtuele Azure-netwerk

Nu Azure AD DS is geïmplementeerd, gaat u het virtuele netwerk configureren zodat andere verbonden virtuele machines en toepassingen van het beheerde domein gebruik kunnen maken. Om deze connectiviteit te leveren moet u de DNS-serverinstellingen voor uw virtuele netwerk bijwerken zodat wordt verwezen naar de twee IP-adressen waar het beheerde domein is geïmplementeerd.

1. Op het tabblad **Overzicht** voor uw beheerde domein wordt een aantal **Vereiste configuratiestappen** weergegeven. De eerste configuratiestap is het bijwerken van de DNS-serverinstellingen voor het virtuele netwerk. Zodra de DNS-instellingen goed zijn geconfigureerd, wordt deze stap niet meer weergegeven.

    De vermelde adressen zijn de domeincontrollers die in het virtuele netwerk moeten worden gebruikt. In dit voorbeeld zijn die adressen *10.0.1.4* en *10.0.1.5*. U kunt deze IP-adressen terugvinden op het tabblad **Eigenschappen**.

    ![DNS-instellingen voor uw virtuele netwerk configureren met de IP-adressen van Azure AD Domain Services](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Selecteer de knop **Configureren** om de DNS-serverinstellingen bij te werken voor het virtuele netwerk. De DNS-instellingen worden automatisch geconfigureerd voor uw virtuele netwerk.

> [!TIP]
> Als u een bestaand virtueel netwerk in de vorige stappen hebt geselecteerd, worden de nieuwe DNS-instellingen pas ingesteld op virtuele machines die met het netwerk zijn verbonden zodra u de machine opnieuw hebt opgestart. U kunt virtuele machines opnieuw opstarten met behulp van Azure Portal, Azure PowerShell of de Azure CLI.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Gebruikersaccounts voor Azure AD DS inschakelen

Voor de verificatie van gebruikers in het beheerde domein heeft Azure AD DS wachtwoordhashes nodig in een indeling die geschikt is voor NTLM- (NT LAN Manager) en Kerberos-verificatie. Totdat u Azure AD DS voor uw tenant inschakelt, maakt of bewaart Azure AD geen wachtwoordhashes in de vereiste indeling voor NTLM- of Kerberos-verificatie. Om veiligheidsredenen slaat Azure AD ook geen wachtwoorden op in niet-gecodeerde vorm. Azure AD kan deze wachtwoordhashes voor NTLM of Kerberos niet automatisch genereren op basis van bestaande referenties van gebruikers.

> [!NOTE]
> Zodra de configuratie is geslaagd, worden de bruikbare wachtwoordhashes opgeslagen in het beheerde domein. Als u het beheerde domein verwijdert, worden alle wachtwoordhashes die op dat punt zijn opgeslagen ook verwijderd.
>
> Gesynchroniseerde referentiegegevens in Azure AD kunnen niet opnieuw worden gebruikt als u later een beheerd domein maakt; u moet de synchronisatie van wachtwoordhashes opnieuw configureren om de wachtwoordhashes opnieuw op te slaan. Eerder aan het domein toegevoegde virtuele machines of gebruikers kunnen niet direct een verificatie uitvoeren; Azure AD moet eerst de wachtwoordhashes in het nieuwe beheerd domein genereren en opslaan.
>
> Zie [Synchronisatieproces voor wachtwoordhashes voor Azure AD DS en Azure AD Connect][password-hash-sync-process] voor meer informatie.

De stappen om deze wachtwoordhashes te genereren en op te slaan zijn voor cloudgebruikersaccounts die in Azure AD zijn gemaakt anders dan de stappen voor gebruikersaccounts die zijn gesynchroniseerd vanuit uw on-premises directory met behulp van Azure AD Connect.

Een cloudgebruikersaccount is een account dat is gemaakt in uw Azure AD-directory via de Azure portal of Azure AD PowerShell-cmdlets. Deze gebruikersaccounts zijn niet gesynchroniseerd vanuit een on-premises map.

In deze zelfstudie gaat u aan de slag met een basisaccount voor cloudgebruikers. Zie [Wachtwoordhashes synchroniseren voor gebruikersaccounts die vanaf uw on-premises AD bij uw beheerde domein zijn gesynchroniseerd][on-prem-sync] voor meer informatie over de aanvullende stappen die vereist zijn om Azure AD Connect te gebruiken.

> [!TIP]
> Als de Azure AD-tenant een combinatie van cloudgebruikers en gebruikers van on-premises AD heeft, moet u beide sets met stappen uitvoeren.

Voor accounts voor cloudgebruikers moeten gebruikers hun wachtwoorden wijzigen voordat ze Azure AD DS kunnen gebruiken. Door deze wachtwoordwijziging worden de wachtwoordhashes voor Kerberos- en NTLM-verificatie gegenereerd en opgeslagen in Azure AD. Het account wordt niet gesynchroniseerd vanuit Azure AD naar Azure AD DS totdat het wachtwoord wordt gewijzigd. Laat de wachtwoorden voor alle cloudgebruikers in de tenant die Azure AD DS moeten gebruiken verlopen (hierdoor moeten gebruikers hun wachtwoord wijzigen wanneer ze zich weer aanmelden) of instrueer cloudgebruikers om hun wachtwoorden handmatig te wijzigen. Voor deze zelfstudie gaan we handmatig een gebruikerswachtwoord wijzigen.

Voordat een gebruiker zijn of haar wachtwoord opnieuw kan instellen, moet de Azure AD-tenant worden [geconfigureerd voor het opnieuw instellen van wachtwoorden via de selfservice][configure-sspr].

Om het wachtwoord voor een cloudgebruiker te wijzigen, moet de gebruiker de volgende stappen uitvoeren:

1. Ga naar de pagina Toegangsvenster van Azure AD op [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Selecteer in de rechterbovenhoek uw naam en kies **Profiel** in het vervolgkeuzemenu.

    ![Selecteer het profiel](./media/tutorial-create-instance-advanced/select-profile.png)

1. Selecteer op de pagina **Profiel** de optie **Wachtwoord wijzigen**.
1. Voer op de pagina **Wachtwoord wijzigen** uw bestaande (oude) wachtwoord in, voer een nieuw wachtwoord in en bevestig dit nieuwe wachtwoord.
1. Selecteer **Indienen**.

Nadat u uw wachtwoord hebt gewijzigd, duurt het enkele minuten voordat het nieuwe wachtwoord kan worden gebruikt in Azure AD DS en u zich kunt aanmelden bij computers die aan het beheerde domein zijn gekoppeld.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * DNS-instellingen en instellingen voor een virtueel netwerk configureren voor een beheerd domein
> * Een beheerd domein maken
> * Gebruikers met beheerdersrechten toevoegen aan domeinbeheer
> * Gebruikersaccounts inschakelen voor Azure AD DS en wachtwoordhashes genereren

Als u dit beheerde domein in actie wilt zien, maakt en koppelt u een virtuele machine aan het domein.

> [!div class="nextstepaction"]
> [Een virtuele Windows Server-machine toevoegen aan uw beheerde domein](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
