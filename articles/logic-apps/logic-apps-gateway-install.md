---
title: On-premises gegevensgateway installeren
description: U kunt de on-premises gegevens gateway downloaden en installeren voordat u toegang hebt tot gegevens on-Azure Logic Apps premises.
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 4b2559ad20036870c6df5c0662bb973f35155bfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104576795"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>On-premises gegevensgateway installeren voor Azure Logic Apps

Voordat u [verbinding kunt maken met on-premises gegevens bronnen vanuit Azure Logic apps](../logic-apps/logic-apps-gateway-connection.md), downloadt en installeert u de [on-premises gegevens gateway](https://aka.ms/on-premises-data-gateway-installer) op een lokale computer. De gateway werkt als een brug die snelle gegevensoverdracht en versleuteling mogelijk maakt tussen on-premises gegevensbronnen en uw logische apps. U kunt dezelfde Gateway-installatie gebruiken met andere Cloud Services, zoals automatische energie automatisering, Power BI, Power apps en Azure Analysis Services. Zie de volgende artikelen voor meer informatie over het gebruik van de gateway met deze services:

* [Micro soft power on-premises gegevens gateway automatiseren](/power-automate/gateway-reference)
* [On-premises gegevens gateway van micro soft Power BI](/power-bi/service-gateway-onprem)
* [On-premises gegevens gateway van micro soft power apps](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services on-premises gegevens gateway](../analysis-services/analysis-services-gateway.md)

In dit artikel wordt beschreven hoe u uw on-premises gegevens gateway kunt downloaden, installeren en instellen, zodat u toegang hebt tot on-premises gegevens bronnen van Azure Logic Apps. U kunt ook meer te weten komen over [de manier waarop de gegevens gateway](#gateway-cloud-service) verderop in dit onderwerp werkt. Zie [Wat is een on-premises gateway](/data-integration/gateway/service-gateway-onprem)? voor meer informatie over de gateway. Ga naar de Power shell-galerie voor de [DataGateway Power shell-cmdlets](https://www.powershellgallery.com/packages/DataGateway/3000.15.15)om de installatie-en beheer taken van de gateway te automatiseren.

<a name="requirements"></a>

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u geen Azure-account hebt met een abonnement, [meldt u zich aan voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  * Uw Azure-account moet een werk account of school account zijn dat er als volgt uitziet `username@contoso.com` . U kunt geen Azure B2B-accounts of persoonlijke micro soft-accounts gebruiken, zoals @hotmail.com of @outlook.com .

    > [!NOTE]
    > Als u zich hebt geregistreerd voor een Microsoft 365 aanbieding en uw werk-e-mail adres niet hebt verstrekt, kan uw adres er als volgt uitzien `username@domain.onmicrosoft.com` . Uw account wordt opgeslagen in een Azure AD-Tenant. In de meeste gevallen is de UPN (User Principal Name) voor uw Azure-account hetzelfde als uw e-mail adres.

    Als u een [Visual Studio Standard-abonnement](https://visualstudio.microsoft.com/vs/pricing/) wilt gebruiken dat is gekoppeld aan een Microsoft-account, maakt u eerst [een Azure AD-Tenant](../active-directory/develop/quickstart-create-new-tenant.md) of gebruikt u de standaard directory. Voeg een gebruiker met een wacht woord toe aan de map en geef die gebruiker vervolgens toegang tot uw Azure-abonnement. U kunt zich vervolgens aanmelden tijdens de installatie van de gateway met deze gebruikers naam en dit wacht woord.

  * Uw Azure-account mag alleen deel uitmaken van een enkele [Azure Active Directory (Azure AD)-Tenant of-map](../active-directory/fundamentals/active-directory-whatis.md#terminology). U moet hetzelfde Azure-account gebruiken voor het installeren en beheren van de gateway op uw lokale computer.

  * Wanneer u de gateway installeert, meldt u zich aan met uw Azure-account, waarmee u uw gateway-installatie kunt koppelen aan uw Azure-account en alleen dat account. U kunt dezelfde Gateway-installatie niet koppelen aan meerdere Azure-accounts of Azure AD-tenants.

  * Later in de Azure Portal, moet u hetzelfde Azure-account gebruiken om een Azure gateway-resource te maken die is gekoppeld aan de installatie van de gateway. U kunt slechts één gateway-installatie en één Azure gateway-resource aan elkaar koppelen. Uw Azure-account kan echter worden gekoppeld aan verschillende gateway-installaties die elk zijn gekoppeld aan een Azure gateway-resource. Uw Logic apps kunnen deze gateway bron vervolgens gebruiken in triggers en acties die toegang hebben tot on-premises gegevens bronnen.

* Hier vindt u de vereisten voor uw lokale computer:

  **Minimale vereisten**

  * .NET Framework 4.7.2
  * 64-bits versie van Windows 7 of Windows Server 2008 R2 (of hoger)

  **Aanbevolen vereisten**

  * 8-core CPU
  * 8 GB geheugen
  * 64-bits versie van Windows Server 2012 R2 of hoger
  * SSD-opslag (Solid-State Drive) voor spooling

  > [!NOTE]
  > De gateway biedt geen ondersteuning voor Windows Server Core.

* **Verwante overwegingen**

  * De on-premises gegevens gateway alleen installeren op een lokale computer, niet op een domein controller. U hoeft de gateway niet te installeren op dezelfde computer als uw gegevens bron. U hebt slechts één gateway nodig voor al uw gegevens bronnen, dus u hoeft de gateway niet voor elke gegevens bron te installeren.

    > [!TIP]
    > Als u de latentie wilt minimaliseren, kunt u de gateway zo dicht mogelijk bij de gegevens bron of op dezelfde computer installeren, ervan uitgaande dat u over de juiste machtigingen beschikt.

  * Installeer de gateway op een lokale computer in een bekabeld netwerk, verbonden met internet, altijd ingeschakeld en gaat niet verder met de slaap stand. Anders kan de gateway niet worden uitgevoerd en is het mogelijk dat de prestaties van een draadloos netwerk afnemen.

  * Als u van plan bent om Windows-verificatie te gebruiken, moet u ervoor zorgen dat u de gateway installeert op een computer die lid is van dezelfde Active Directory omgeving als uw gegevens bronnen.

  * De regio die u voor de gateway-installatie selecteert, is dezelfde locatie die u moet selecteren wanneer u later de Azure-gateway resource voor uw logische app maakt. Deze regio is standaard dezelfde locatie als uw Azure AD-Tenant voor het beheren van uw Azure-gebruikers account. U kunt de locatie echter wijzigen tijdens de installatie van de gateway of later.

    > [!IMPORTANT]
    > Tijdens het installeren van de gateway is de opdracht **regio wijzigen** niet beschikbaar als u bent aangemeld met uw Azure Government-account, dat is gekoppeld aan een Azure Active Directory-Tenant (Azure AD) in de [Azure Government Cloud](../azure-government/compare-azure-government-global-azure.md). De gateway gebruikt automatisch dezelfde regio als de Azure AD-Tenant van uw gebruikers account.
    > 
    > Als u uw Azure Government-account wilt blijven gebruiken, maar de gateway wilt instellen voor gebruik in de globale multi-tenant Azure commerciële Cloud, moet u zich eerst aanmelden tijdens de installatie van de gateway met de `prod@microsoft.com` gebruikers naam. Met deze oplossing wordt de gateway gedwongen de wereld wijde Azure-Cloud voor meerdere tenants te gebruiken, maar kunt u nog steeds uw Azure Government-account blijven gebruiken.

  * Als u de installatie van de gateway bijwerkt, moet u eerst uw huidige gateway verwijderen voor een overzichtelijke ervaring.

    Zorg er als best practice voor dat u een ondersteunde versie gebruikt. Micro soft brengt elke maand een nieuwe update naar de on-premises gegevens gateway uit en biedt momenteel alleen ondersteuning voor de laatste zes releases van de on-premises gegevens gateway. Als u problemen ondervindt met de versie die u gebruikt, voert u [een upgrade uit naar de nieuwste versie](https://aka.ms/on-premises-data-gateway-installer) , omdat uw probleem mogelijk wordt opgelost in de meest recente versie.

  * De gateway heeft twee modi: standaard modus en persoonlijke modus, die alleen van toepassing is op Power BI. U kunt niet meer dan één gateway in dezelfde modus op dezelfde computer uitvoeren.

  * Azure Logic Apps ondersteunt Lees-en schrijf bewerkingen via de gateway. Deze bewerkingen hebben echter [limieten voor de grootte van de nettolading](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Een gegevensgateway installeren

1. [Down load en voer het installatie programma van de gateway op een lokale computer uit](https://aka.ms/on-premises-data-gateway-installer).

1. Bekijk de minimale vereisten, behoud de standaard installatie, accepteer de gebruiksrecht overeenkomst en selecteer vervolgens **installeren**.

   ![Bekijk de vereisten en accepteer de gebruiks voorwaarden](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Nadat de gateway is geïnstalleerd, geeft u het e-mail adres voor uw Azure-account op en selecteert u **Aanmelden**, bijvoorbeeld:

   ![Aanmelden met een werk-of school account](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   De gateway-installatie kan slechts aan één Azure-account worden gekoppeld.

1. Selecteer **een nieuwe gateway registreren op deze computer**  >  **volgende**. Met deze stap wordt de gateway-installatie geregistreerd bij de [Gateway-Cloud service](#gateway-cloud-service).

   ![Gateway op lokale computer registreren](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Geef deze informatie op voor de gateway-installatie:

   * Een gateway naam die uniek is binnen uw Azure AD-Tenant
   * De herstel sleutel, die ten minste acht tekens moet bevatten die u wilt gebruiken
   * Bevestiging van uw herstel sleutel

   ![Informatie over de installatie van de gateway opgeven](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Bewaar uw herstel sleutel op een veilige plaats. U hebt deze sleutel nodig als u ooit de locatie wilt wijzigen, verplaatsen, herstellen of overnemen van een gateway-installatie.

   Let op de optie om toe **te voegen aan een bestaand gateway cluster**, dat u selecteert wanneer u extra gateways voor [scenario's met hoge Beschik baarheid](#high-availability)installeert.

1. Controleer de regio voor de gateway-Cloud service en het [Azure service bus Messa ging-exemplaar](../service-bus-messaging/service-bus-messaging-overview.md) dat wordt gebruikt door de gateway-installatie. Standaard is deze regio dezelfde locatie als de Azure AD-Tenant voor uw Azure-account.

   ![Regio bevestigen voor de Gateway Service en service bus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Selecteer **configureren** om de standaard regio te accepteren. Als de standaard regio echter niet het meest overeenkomt met u, kunt u de regio wijzigen.

   *Waarom wijzigt u de regio voor de installatie van de gateway?*

   Als u bijvoorbeeld de latentie wilt beperken, kunt u de regio van de gateway wijzigen in dezelfde regio als uw logische app. U kunt ook de regio selecteren die het dichtst bij uw on-premises gegevens bron ligt. Uw *Gateway bron in azure* en uw logische app kunnen verschillende locaties hebben.

   1. Selecteer **regio wijzigen** naast de huidige regio.

      ![De huidige gateway regio wijzigen](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Open de lijst **regio selecteren** op de volgende pagina, selecteer de gewenste regio en selecteer **gereed**.

      ![Selecteer een andere regio voor de Gateway Service](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Bekijk de informatie in het laatste bevestigings venster. In dit voor beeld wordt hetzelfde account gebruikt voor Logic Apps, Power BI, Power apps en Power Automatiseer, zodat de gateway beschikbaar is voor al deze services. Wanneer u klaar bent, selecteert u **sluiten**.

   ![Gegevens gateway gegevens bevestigen](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. [Maak nu de Azure-resource voor de gateway-installatie](../logic-apps/logic-apps-gateway-connection.md).

<a name="communication-settings"></a>

## <a name="check-or-adjust-communication-settings"></a>Communicatie-instellingen controleren of aanpassen

De on-premises gegevens gateway is afhankelijk van [Azure Service Bus berichten](../service-bus-messaging/service-bus-messaging-overview.md) voor Cloud connectiviteit en de bijbehorende uitgaande verbindingen naar de Azure-regio die aan de gateway is gekoppeld. Als uw werk omgeving vereist dat verkeer via een proxy of firewall wordt uitgevoerd om toegang te krijgen tot internet, kan deze beperking ertoe leiden dat de on-premises gegevens gateway geen verbinding kan maken met de gateway-Cloud service en Azure Service Bus berichten. De gateway heeft verschillende communicatie-instellingen, die u kunt aanpassen.

In een voorbeeld scenario kunt u aangepaste connectors gebruiken die toegang hebben tot on-premises resources met behulp van de on-premises gegevens gateway resource in Azure. Als u ook een firewall hebt die het verkeer beperkt tot specifieke IP-adressen, moet u de installatie van de gateway instellen om toegang toe te staan voor de bijbehorende *[uitgaande IP-adressen](logic-apps-limits-and-config.md#outbound)van beheerde connectors*. *Alle* Logic apps in dezelfde regio gebruiken dezelfde IP-adresbereiken.

Raadpleeg de volgende onderwerpen voor meer informatie:

* [Communicatie-instellingen voor de on-premises gegevensgateway aanpassen](/data-integration/gateway/service-gateway-communication)
* [Proxyinstellingen configureren voor de on-premises gegevensgateway](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Ondersteuning voor hoge Beschik baarheid

Als u storingen wilt voor komen op individuele punten van uitval voor on-premises gegevens toegang, kunt u meerdere gateway-installaties (alleen de standaard modus) hebben met elk op een andere computer en deze instellen als een cluster of groep. Op die manier, als de primaire gateway niet beschikbaar is, worden gegevens aanvragen doorgestuurd naar de tweede gateway, enzovoort. Omdat u slechts één standaard gateway op een computer kunt installeren, moet u elke extra gateway in het cluster op een andere computer installeren. Alle connectors die samen werken met de on-premises gegevens gateway ondersteunen hoge Beschik baarheid.

* U moet al ten minste één gateway installeren met hetzelfde Azure-account als de primaire gateway en de herstel sleutel voor die installatie.

* Op uw primaire gateway moet de gateway-update van november 2017 of hoger worden uitgevoerd.

Wanneer u na het instellen van de primaire gateway een andere gateway installeert, selecteert u **toevoegen aan een bestaand gateway cluster**, selecteert u de primaire gateway, de eerste gateway die u hebt geïnstalleerd, en geeft u de herstel sleutel voor die gateway op. Zie [clusters met hoge Beschik baarheid voor on-premises gegevens gateway](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)voor meer informatie.

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Een bestaande gateway wijzigen, migreren, herstellen of overnemen

Als u de locatie van de gateway moet wijzigen, de installatie van de gateway naar een nieuwe computer verplaatst, een beschadigde gateway herstelt of eigenaar van een bestaande gateway bent, hebt u de herstel sleutel nodig die tijdens de installatie van de gateway werd verschaft.

> [!NOTE]
> Voordat u de gateway op de computer met de oorspronkelijke gateway installatie herstelt, moet u eerst de gateway op die computer verwijderen. Met deze actie wordt de verbinding van de oorspronkelijke gateway verbroken.
> Als u een gateway cluster verwijdert of verwijdert voor een Cloud service, kunt u dat cluster niet herstellen.

1. Voer het installatie programma van de gateway uit op de computer met de bestaande gateway.

1. Nadat het installatie programma is geopend, meldt u zich aan met hetzelfde Azure-account dat is gebruikt om de gateway te installeren.

1. Selecteer **een bestaande gateway migreren, herstellen of overnemen**  >  , bijvoorbeeld:

   ![Selecteer een bestaande gateway migreren, herstellen of overnemen](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selecteer een van de beschik bare clusters en gateways en voer de herstel sleutel voor de geselecteerde gateway in, bijvoorbeeld:

   ![Gateway selecteren en herstel sleutel opgeven](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Als u de regio wilt wijzigen, selecteert u **regio wijzigen** en selecteert u de nieuwe regio.

1. Wanneer u klaar bent, selecteert u **configureren** zodat u de taak kunt volt ooien.

## <a name="tenant-level-administration"></a>Beheer op Tenant niveau

Om inzicht te krijgen in alle on-premises gegevens gateways in een Azure AD-Tenant, kunnen globale beheerders in die Tenant zich aanmelden bij het [Power platform-beheer centrum](https://powerplatform.microsoft.com) als Tenant beheerder en de optie **gegevens gateways** selecteren. Zie [beheer op Tenant niveau voor de on-premises gegevens gateway](/data-integration/gateway/service-gateway-tenant-level-admin)voor meer informatie.

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Gateway opnieuw opstarten

Standaard wordt de installatie van de gateway op uw lokale computer uitgevoerd als een Windows-Service account met de naam ' on-premises gegevens Gateway-Service '. De gateway-installatie gebruikt echter de `NT SERVICE\PBIEgwService` naam voor de account referenties aanmelden als en heeft de machtigingen aanmelden als service.

> [!NOTE]
> Uw Windows-Service account wijkt af van het account dat wordt gebruikt om verbinding te maken met on-premises gegevens bronnen en van het Azure-account dat u gebruikt wanneer u zich aanmeldt bij Cloud Services.

Net als elke andere Windows-service kunt u de gateway op verschillende manieren starten en stoppen. Zie [een on-premises gegevens Gateway opnieuw starten](/data-integration/gateway/service-gateway-restart)voor meer informatie.

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Hoe de gateway werkt

Gebruikers in uw organisatie hebben toegang tot on-premises gegevens waarvoor ze al geautoriseerde toegang hebben. Voordat deze gebruikers verbinding kunnen maken met uw on-premises gegevens bron, moet u echter een on-premises gegevens gateway installeren en instellen. Normaal gesp roken is een beheerder de persoon die een gateway installeert en instelt. Voor deze acties zijn mogelijk beheerders machtigingen voor de server of speciale kennis van uw on-premises servers vereist.

De gateway helpt sneller en veiliger achter de schermen communicatie. Deze communicatie loopt tussen een gebruiker in de Cloud, de gateway-Cloud service en uw on-premises gegevens bron. De gateway-Cloud service versleutelt en slaat uw referenties voor de gegevens bron en de gateway gegevens op. De service routeert ook query's en de bijbehorende resultaten tussen de gebruiker, de gateway en uw on-premises gegevens bron.

De gateway werkt met firewalls en maakt gebruik van alleen uitgaande verbindingen. Al het verkeer is afkomstig van het beveiligde uitgaande verkeer van de gateway-agent. De Gateway verzendt de gegevens van on-premises bronnen op versleutelde kanalen via [Azure Service Bus berichten](../service-bus-messaging/service-bus-messaging-overview.md). Met deze service bus wordt een kanaal gemaakt tussen de gateway en de aanroepende service, maar worden er geen gegevens opgeslagen. Alle gegevens die via de gateway worden uitgewisseld, worden versleuteld.

![Architectuur voor on-premises gegevens gateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Afhankelijk van de Cloud service moet u mogelijk een gegevens bron instellen voor de gateway.

In deze stappen wordt beschreven wat er gebeurt wanneer u communiceert met een-element dat is verbonden met een on-premises gegevens Bron:

1. De Cloud service maakt een query, samen met de versleutelde referenties voor de gegevens bron. De service verzendt vervolgens de query en referenties naar de gateway wachtrij voor verwerking.

1. De gateway-Cloud service analyseert de query en duwt de aanvraag naar Azure Service Bus berichten.

1. Azure Service Bus berichten verzendt de in behandeling zijnde aanvragen naar de gateway.

1. De gateway haalt de query op, ontsleutelt de aanmeldingsgegevens en maakt met deze aanmeldingsgegevens verbinding met een of meer gegevensbronnen.

1. De gateway stuurt de query naar de gegevens bron om uit te voeren.

1. De resultaten worden vanuit de gegevens bron teruggezonden naar de gateway en vervolgens naar de gateway-Cloud service. De gateway-Cloud service gebruikt vervolgens de resultaten.

### <a name="authentication-to-on-premises-data-sources"></a>Verificatie voor on-premises gegevensbronnen

Een opgeslagen referentie wordt gebruikt om verbinding te maken tussen de gateway en de on-premises gegevens bronnen. Ongeacht de gebruiker, gebruikt de gateway de opgeslagen referentie om verbinding te maken. Er zijn mogelijk verificatie-uitzonde ringen voor specifieke services, zoals DirectQuery en LiveConnect voor Analysis Services in Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Micro soft Cloud Services gebruiken [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) om gebruikers te verifiëren. Een Azure AD-Tenant bevat gebruikers namen en beveiligings groepen. Normaal gesp roken is het e-mail adres dat u gebruikt voor aanmelden hetzelfde als de UPN (User Principal Name) voor uw account.

### <a name="what-is-my-upn"></a>Wat is mijn UPN?

Als u geen domein beheerder bent, bent u mogelijk niet op de hoogte van uw UPN. Als u de UPN voor uw account wilt zoeken, voert u de `whoami /upn` opdracht uit vanaf uw werk station. Hoewel het resultaat eruitziet als een e-mail adres, is het resultaat de UPN voor uw lokale domein account.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Een on-premises Active Directory Domain Services synchroniseren met Azure AD

De UPN voor uw on-premises Active Directory accounts en Azure AD-accounts moet hetzelfde zijn. Zorg er dus voor dat elke on-premises Active Directory-account overeenkomt met uw Azure AD-account. De Cloud Services weten alleen over accounts in azure AD. U hoeft dus geen account aan uw on-premises Active Directory toe te voegen. Als het account niet bestaat in azure AD, kunt u dat account niet gebruiken.

Hier vindt u een aantal manieren waarop u uw on-premises Active Directory accounts kunt vergelijken met Azure AD.

* Voeg accounts hand matig toe aan Azure AD.

  Maak een account in de Azure Portal of in het Microsoft 365 beheer centrum. Zorg ervoor dat de account naam overeenkomt met de UPN voor het on-premises Active Directory-account.

* Synchroniseer lokale accounts met uw Azure AD-Tenant met behulp van het Azure Active Directory Connect-hulp programma.

  Het hulp programma Azure AD Connect biedt opties voor Directory synchronisatie en verificatie-instellingen. Deze opties omvatten wachtwoord-hash-synchronisatie, Pass Through-verificatie en Federatie. Als u geen Tenant beheerder of een lokale domein beheerder bent, neemt u contact op met uw IT-beheerder om Azure AD Connect in te stellen. Azure AD Connect zorgt ervoor dat uw Azure AD-UPN overeenkomt met uw lokale Active Directory UPN. Deze overeenkomst helpt u bij het gebruik van Analysis Services live-verbindingen met Power BI of SSO-mogelijkheden (single sign-on).

  > [!NOTE]
  > Als u accounts synchroniseert met het hulp programma Azure AD Connect, worden er nieuwe accounts gemaakt in uw Azure AD-Tenant.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Veelgestelde vragen en probleemoplossing

* [Veelgestelde vragen over on-premises gegevensgateways](/data-integration/gateway/service-gateway-onprem-faq)
* [Problemen met de on-premises gegevensgateway oplossen](/data-integration/gateway/service-gateway-tshoot)
* [Gatewayprestaties bewaken en optimaliseren](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met on-premises gegevens vanuit Logic apps](../logic-apps/logic-apps-gateway-connection.md)
* [Functies voor ondernemings integratie](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connectors voor Azure Logic Apps](../connectors/apis-list.md)
