---
title: Azure AD beveiligde hybride toegang met F5-implementatiehandleiding | Microsoft Docs
description: Zelfstudie voor het implementeren van F5 BIG-IP Virtual Edition (VE) VM in Azure IaaS voor beveiligde hybride toegang
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 139009c55573e1e115a22069671f66e93695a635
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783319"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Zelfstudie voor het implementeren van een virtuele F5 BIG-IP Virtual Edition-machine in Azure IaaS voor beveiligde hybride toegang

In deze zelfstudie wordt u door het end-to-end proces voor het implementeren van BIG-IP Vitial Edition (VE) in Azure IaaS doorloopt. Aan het einde van deze zelfstudie hebt u het volgende nodig:

- Een volledig voorbereide BIG-IP Virtual Machine (VM) voor het modelleren van een sha-concept (Secure Hybrid Access)

- Een faserings exemplaar dat moet worden gebruikt voor het testen van nieuwe BIG-IP-systeemupdates en hotfixes

## <a name="prerequisites"></a>Vereisten

Eerdere ervaring of kennis van F5 BIG-IP is niet nodig, maar we raden u aan vertrouwd te raken met [F5 BIG-IP-terminologie.](https://www.f5.com/services/resources/glossary) Voor het implementeren van een BIG-IP in Azure voor SHA is het volgende vereist:

- Een betaald Azure-abonnement of een gratis proefabonnement van 12 [maanden.](https://azure.microsoft.com/free/)

- Een van de volgende F5 BIG-IP-licentie-SKU's

  - F5 BIG-IP® Beste bundel

  - Zelfstandige licentie voor F5 BIG-IP Access Policy Manager™ (APM)

  - Invoeglicentie voor F5 BIG-IP Access Policy Manager™ (APM) op een bestaande BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM)
  
  - 90-daagse big-IP-proeflicentie voor [volledige functie](https://www.f5.com/trial/big-ip-trial.php).

- Een jokerteken of SAN-certificaat (Subject Alternative Name) voor het publiceren van webtoepassingen via Secure Socket Layer (SSL). [Laten we versleutelen biedt](https://letsencrypt.org/) een gratis certificaat van 90 dagen voor testen.

- Een SSL-certificaat voor het beveiligen BIG-IPs beheerinterface. Een certificaat dat wordt gebruikt om web-apps te publiceren, kan worden gebruikt als het onderwerp overeenkomt met de Fully Qualified Domain Name (FQDN) van het BIG-IP. Een jokertekencertificaat dat is gedefinieerd met een onderwerp *.contoso.com is bijvoorbeeld geschikt voor `https://big-ip-vm.contoso.com:8443`

VM-implementatie en basissysteem-configuraties duren ongeveer 30 minuten, waarna uw BIG-IP-platform gereed is voor de implementatie van een van de SHA-scenario's die hier [worden vermeld.](f5-aad-integration.md)

Voor het testen van de scenario's wordt in deze zelfstudie ervan uitgenomen dat het BIG-IP wordt geïmplementeerd in een Azure-resourcegroep met een Active Directory-omgeving (AD). De omgeving moet bestaan uit een domeincontroller (DC) en webhost -VM's (IIS). Het is ook geen probleem om deze servers op andere locaties naar de BIG-IP-VM te plaatsen, mits het BIG-IP-adres zicht heeft op elk van de rollen die nodig zijn voor de ondersteuning van een bepaald scenario. Scenario's waarbij de BIG-IP-VM is verbonden met een andere omgeving via een VPN-verbinding, worden ook ondersteund.

Als u de items die hier worden vermeld niet hebt om te testen, kunt u een volledige AD-domeinomgeving implementeren in Azure met behulp van [dit script](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Een verzameling voorbeeldtesttoepassingen kan ook programmatisch worden geïmplementeerd op een IIS-webhost met behulp van deze [automatisering met script.](https://github.com/jeevanbisht/DemoSuite)

>[!NOTE]
>De [Azure Portal](https://portal.azure.com/#home) is voortdurend in ontwikkeling, dus sommige van de stappen in deze zelfstudie kunnen afwijken van de werkelijke indeling die in de zelfstudie Azure Portal.

## <a name="azure-deployment"></a>Azure-implementatie

Een BIG-IP kan worden geïmplementeerd in verschillende topologies. Deze handleiding richt zich op één NIC-implementatie (netwerkinterface). Als uw BIG-IP-implementatie echter meerdere netwerkinterfaces vereist voor hoge beschikbaarheid, netwerkscheiding of meer dan 1 GB doorvoer, kunt u overwegen om de vooraf gecompileerde Azure Resource Manager-sjablonen [(ARM)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)van F5 te gebruiken.

Voltooi de volgende taken om BIG-IP VE te implementeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Meld u aan [Azure Portal](https://portal.azure.com/#home) account met machtigingen voor het maken van VM's. Bijvoorbeeld Inzender

2. Typ marketplace in het bovenste zoekvak op **het** lint, gevolgd door **Enter**

3. Typ **F5** in het Marketplace-filter, gevolgd door **Enter**

4. Selecteer **+ Toevoegen in** het bovenste lint en typ **F5** in het marketplace-filter, gevolgd door **Enter**

5. Selecteer **F5 BIG-IP Virtual Edition (BYOL)** Selecteer een  >  **softwareplan**  >  **F5 BIG-IP VE - ALL (BYOL, 2 opstartlocaties)**

6. Selecteer **Maken**.

![In de afbeelding ziet u de stappen voor het selecteren van een softwareplan](./media/f5ve-deployment-plan/software-plan.png)

7. Doorseen het menu **Basis** en gebruik de volgende instellingen

 |  Projectgegevens     |  Waarde     |
 |:-------|:--------|
 |Abonnement|Doelabonnement voor de implementatie van de BIG-IP-VM|
 |Resourcegroep | Bestaande Azure-resourcegroep de BIG-IP-VM wordt geïmplementeerd in of er een gemaakt. Moet dezelfde resourcegroep van uw DC- en IIS-VM's zijn|
 | **Exemplaardetails**|  |
 |VM-naam| Voorbeeld van BIG-IP-VM |
 |Region | Doel-Azure-geo voor BIG-IP-VM |
 |Beschikbaarheidsopties| Alleen inschakelen als U VM in productie gebruikt|
 |Installatiekopie| F5 BIG-IP VE - ALL (BYOL, 2 opstartlocaties)|
 |Azure Spot-exemplaar| Nee, maar u kunt dit indien nodig inschakelen |
 |Grootte| De minimale specificatie moet 2 vCCPUs en 8 GB geheugen zijn|
 |**Beheerdersaccount**|  |
 |Verificatietype|Selecteer het wachtwoord voor nu. U kunt later overschakelen naar een sleutelpaar |
 |Gebruikersnaam|De identiteit die wordt gemaakt als een lokaal BIG-IP-account voor toegang tot de beheerinterfaces. De gebruikersnaam is casegevoelig.|
 |Wachtwoord|Beheerderstoegang beveiligen met een sterk wachtwoord|
 |**Regels voor binnenkomende poort**|  |
 |Openbare poorten voor inkomend verkeer|Geen|

8. Selecteer **Volgende: Schijven laten** alle standaardwaarden staan en selecteer **Volgende: Netwerken.**

9. Voltooi deze **instellingen** in het menu Netwerken.

 |Netwerkinterface|      Waarde |
 |:--------------|:----------------|
 |Virtueel netwerk|Hetzelfde Azure-VNet dat wordt gebruikt door uw DC- en IIS-VM's, of maak er een|
 |Subnet| Hetzelfde interne Azure-subnet als uw DC- en IIS-VM's, of maak er een|
 |Openbare IP |  Geen|
 |NIC-netwerkbeveiligingsgroep| Selecteer Geen als het Azure-subnet dat u in de vorige stappen hebt geselecteerd, al is gekoppeld aan een netwerkbeveiligingsgroep (NSG); selecteer anders Basic|
 |Netwerken versnellen| Aan |
 |**Taakverdeling**|     |
 |Load balancer VM| No|

10. Selecteer **Volgende: Beheer** en voltooi deze instellingen.

 |Bewaking|    Waarde |
 |:---------|:-----|
 |Gedetailleerde bewaking| Aan|
 |Diagnostische gegevens over opstarten|Inschakelen met aangepast opslagaccount. Hiermee kunt u verbinding maken met de SSH-interface (BIG-IP Secure Shell) via de optie Seriële console in de Azure Portal. Selecteer een beschikbaar Azure-opslagaccount|
 |**Identiteit**|  |
 |Door het systeem toegewezen beheerde identiteit|Aan|
 |Azure Active Directory|BIG-IP biedt momenteel geen ondersteuning voor deze optie|
 |**Autossluitingdown**|    |
 |Automatisch afsluiten inschakelen| Als u test, kunt u overwegen om de BIG-IP-VM in te stellen op dagelijks afsluiten|

11. Selecteer **Volgende: Geavanceerd,** laat alle standaardwaarden staan en selecteer **Volgende: Tags.**

12. Selecteer **Volgende: Beoordelen en maken om** uw BIG-IP-VM-configuraties te controleren voordat u **Maken** selecteert om de implementatie te laten worden uitgevoerd.

13. Het volledig implementeren van een BIG-IP-VM is doorgaans 5 minuten. Wanneer u klaar bent, selecteert u Ga naar **resource,** vouwt u het menu aan de linkerkant van Azure Portal uit en selecteert u **Resourcegroepen** om naar uw nieuwe BIG-IP-VM te navigeren. Als het maken van de VM mislukt, selecteert **u Terug** en **Volgende.**

## <a name="network-configuration"></a>Netwerkconfiguratie

Wanneer de BIG-IP-VM voor het eerst wordt  uitgevoerd, wordt de NIC ingericht met een primair privé-IP-adres dat is uitgegeven door de Dynamic Host Configuration Protocol-service (DHCP) van het Azure-subnet dat is verbonden. Dit IP-adres wordt gebruikt door het Traffic Management Operating System (TMOS) van het BIG-IP om te communiceren met:

- Communiceren met andere hosts en services

- Uitgaande toegang tot het openbare internet

- Inkomende toegang tot de BIG-IPs web-configuratie en SSH-beheerinterfaces

Als u een van deze beheerinterfaces beschikbaar maakt voor internet, wordt het BIG-IPs-aanvalsoppervlak vergroot. Daarom is het primaire IP-adres van BIG-IPs tijdens de implementatie niet ingericht met een openbaar IP-adres. In plaats daarvan worden een secundair intern IP-adres en het bijbehorende openbare IP-adres ingericht voor publicatieservices.
Met deze 1-op-1-toewijzing tussen een openbaar IP-adres van een VM en een privé-IP-adres kan extern verkeer een VM bereiken. Er is echter ook een Azure NSG-regel vereist om het verkeer toe te staan, op vrijwel dezelfde manier als een firewall.

In het diagram ziet u één NIC-implementatie van een BIG-IP VE in Azure, geconfigureerd met een primair IP-adres voor algemene bewerkingen en beheer, en een afzonderlijke ip-adressen voor virtuele servers voor het publiceren van services.
In deze rangschikking staat een NSG-regel extern verkeer toe dat is bestemd voor route naar het openbare IP-adres voor de gepubliceerde service, voordat het wordt doorgestuurd naar de virtuele `intranet.contoso.com` BIG-IP-server.

![In de afbeelding ziet u dat de implementatie met één nic standaard privé- en openbare IP's die zijn uitgegeven aan virtuele Azure-VM's altijd dynamisch zijn, dus dit verandert waarschijnlijk bij elke herstart ](./media/f5ve-deployment-plan/single-nic-deployment.png) van een virtueleM. Voorkom onvoorziene connectiviteitsproblemen door het beheer-IPBIG-IPs te wijzigen in statisch en doe hetzelfde met secundaire IP-adressen die worden gebruikt voor publicatieservices.

1. Ga in het menu van uw BIG-IP-VM naar **Instellingen**  >  **Netwerken**

2. Selecteer in de netwerkweergave de koppeling rechts van **Netwerkinterface**

![In de afbeelding ziet u de netwerk config](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM-namen worden willekeurig gegenereerd tijdens de implementatie.

3. Selecteer in het linkerdeelvenster **IP-configuraties** en selecteer vervolgens **de rij ipconfig1**

4. Stel de **optie IP-toewijzing** in op statisch en wijzig zo nodig het primaire IP-adres van de BIG-IP-VM's. Selecteer vervolgens **Opslaan** en sluit het menu ipconfig1

>[!NOTE]
>U gebruikt dit primaire IP-adres om verbinding te maken met de BIG-IP-VM en deze te beheren.

5. Selecteer **+ Toevoegen** op het bovenste lint en geef een naam op voor een secundair privé-IP-adres, bijvoorbeeld ipconfig2

6. Stel de **optie Toewijzing** van de privé-IP-adresinstellingen in op **Statisch.** Door het volgende opeenvolgende IP-adres (hoger of lager) op te geven, zorgt u ervoor dat alles ordeloos blijft.

7. Stel het openbare IP-adres in op **Koppelen en** selecteer **Maken**

8. Geef een naam op voor het nieuwe openbare IP-adres, bijvoorbeeld BIG-IP-VM_ipconfig2_Public

9. Stel deSKU in op Standard als u hier **om** wordt gevraagd

10. Als u hier om wordt gevraagd, stelt u **de laag in** **op Globaal** en

11. Stel de **optie Toewijzing** in op **Statisch** en selecteer vervolgens twee **keer OK**

Uw BIG-IP-VM is nu klaar om in te stellen met:

- **Primair privé-IP-adres:** toegewezen aan het beheren van de BIG-IP-VM via het hulpprogramma Web config en SSH. Het wordt door het BIG-IP-systeem gebruikt als **self-IP om** verbinding te maken met gepubliceerde back-endservices. Er wordt ook verbinding met externe services zoals NTP, AD en LDAP.

- **Secundair privé-IP-adres:** wordt gebruikt bij het maken van een virtuele BIG-IP APM-server om te luisteren naar binnenkomende aanvragen naar een gepubliceerde service(s).

- **Openbaar IP:,** gekoppeld aan het secundaire privé-IP-adres, zorgt ervoor dat clientverkeer van het openbare internet de virtuele BIG-IP-server voor de gepubliceerde service(s) kan bereiken

In het voorbeeld ziet u de 1-op-1-relatie tussen openbare en privé-IP's van een VM. Een Azure VM-NIC kan slechts één primair IP-adres hebben en eventuele extra gemaakte IP-adressen worden altijd secundair genoemd.

>[!NOTE]
>U hebt de secundaire IP-toewijzingen nodig voor het publiceren van BIG-IP-services.

![In deze afbeelding ziet u alle secundaire IP's](./media/f5ve-deployment-plan/secondary-ips.png)

Als u SHA wilt implementeren met behulp van de begeleide configuratie voor BIG-IP-toegang, herhaalt u stap 5-11 om extra privé- en openbare IP-paren te maken voor elke extra service die u wilt publiceren via de BIG-IP APM. Dezelfde methode kan ook worden gebruikt als u services publiceert met behulp BIG-IPs **Geavanceerde configuratie**. In dat scenario hebt u echter de mogelijkheid om openbare IP-overhead te vermijden met behulp van een [SNI-configuratie (Server Name Indicator).](https://support.f5.com/csp/#/article/K13452) In deze configuratie accepteert een virtuele BIG-IP-server al het clientverkeer dat deze ontvangt en routeert deze naar de bestemming.

## <a name="dns-configuration"></a>DNS-configuratie

Het is essentieel dat DNS is geconfigureerd voor clients om uw gepubliceerde SHA-services om te passen naar de openbare IP('s) van uw BIG-IP-VM's.

In de volgende stappen wordt ervan uitgenomen dat de DNS-zone van het openbare domein dat wordt gebruikt voor uw SHA-services, wordt beheerd in Azure. Dezelfde DNS-principes voor het maken van locatorrecords zijn echter nog steeds van toepassing, ongeacht waar uw DNS-zone wordt beheerd.

1. Als dit nog niet is geopend, vouwt u het linkermenu van de portal uit en navigeert u naar uw BIG-IP-VM via de **optie Resourcegroepen**

2. Ga in het menu van uw BIG-IP-VM naar **Instellingen**  >  **Netwerken**

3. Selecteer in de netwerkweergave BIG-IP-VM's het eerste secundaire IP-adres in de vervolgkeuzelijst IP-configuratie en selecteer de koppeling voor het openbare **IP-adres** van de NIC

![Schermopname van het openbare IP-adres van de NIC](./media/f5ve-deployment-plan/networking.png)

4. Selecteer onder **de sectie** Instellingen in  het linkerdeelvenster de optie Configuratie om het menu met openbare IP- en DNS-eigenschappen voor het geselecteerde secundaire IP-adres weer te geven

5. Selecteer **aliasrecord maken** en selecteer uw **DNS-zone** in de vervolgkeuzelijst. Als er nog geen DNS-zone bestaat, kan deze buiten Azure worden beheerd of kunt u er een maken voor het domeinachtervoegsel dat u in Azure AD zou hebben geverifieerd.

6. Gebruik de volgende gegevens om de eerste DNS-aliasrecord te maken:

 | Veld | Waarde |
 |:-------|:-----------|
 |Abonnement| Hetzelfde abonnement als de BIG-IP-VM|
 |DNS-zone| DNS-zone die gezaghebbend is voor het geverifieerde domeinachtervoegsel dat door uw gepubliceerde websites wordt gebruikt, bijvoorbeeld www.contoso.com |
 |Name | De hostnaam die u opgeeft, wordt omgezet naar het openbare IP-adres dat is gekoppeld aan het geselecteerde secundaire IP-adres. Zorg ervoor dat u de juiste DNS-naar-IP-toewijzingen definieert. Zie de laatste afbeelding in de sectie Netwerk configs, bijvoorbeeld intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |TTL-eenheden | Tijden |

7. Selecteer **Maken** voor Azure om deze instellingen op te slaan in openbare DNS.

8. Laat het **dns-naamlabel staan (optioneel)** en selecteer **Opslaan voordat** u het menu Openbaar IP sluit.

9. Herhaal stap 1 tot en met 6 om extra DNS-records te maken voor elke service die u wilt publiceren met behulp van de begeleide configuratie van BIG-IP.

  Als dns-records zijn gemaakt, kunt u een van de online hulpprogramma's zoals [DNS-controle](https://dnschecker.org/) gebruiken om te controleren of een gemaakte record is doorgegeven aan alle algemene openbare DNS-servers.

  Als u uw DNS-domeinnaamruimte beheert met behulp van een externe provider zoals [GoDaddy,](https://www.godaddy.com/)moet u records maken met behulp van hun eigen DNS-beheerfaciliteit.

>[!NOTE]
>U kunt ook het lokale hostbestand van een pc gebruiken als u test en vaak van DNS-records wisselt. Het localhosts-bestand op een Windows-pc is toegankelijk door op Win + R op het toetsenbord te drukken en het woord stuurprogramma's **in** het vak Run in te dienen. Denk er wel aan dat een localhost-record alleen DNS-resolutie biedt voor de lokale pc, niet voor andere clients.

## <a name="client-traffic"></a>Clientverkeer

Azure VNets en bijbehorende subnetten zijn standaard particuliere netwerken die geen internetverkeer kunnen ontvangen. De NIC van uw BIG-IP-VM moet worden gekoppeld aan de NSG die u tijdens de implementatie hebt opgegeven. Als u wilt dat extern webverkeer de BIG-IP-VM bereikt, moet u een NSG-regel voor binnenkomende verbindingen definiëren om de poorten 443 (HTTPS) en 80 (HTTP) via het openbare internet toe te staan.

1. Selecteer netwerken in het hoofdmenu  Overzicht van  de BIG-IP-VM

2. Selecteer **Binnenkomende** regel toevoegen om de volgende eigenschappen van de NSG-regel in te voeren:

 |     Veld   |   Waarde        |
 |:------------|:------------|
 |Bron| Elk|
 |Poortbereiken van bron| *|
 |Doel-IP-adressen|Door komma's gescheiden lijst met alle secundaire IP-adressen van big-IP-VM's|
 |Doelpoorten| 80.443|
 |Protocol| TCP |
 |Bewerking| Toestaan|
 |Prioriteit|Laagste beschikbare waarde tussen 100 - 4096|
 |Name | Een beschrijvende naam, bijvoorbeeld: `BIG-IP-VM_Web_Services_80_443`|

3. Selecteer **Toevoegen om** de wijzigingen door te voeren en sluit het menu Netwerken. 

HTTP- en HTTPS-verkeer vanaf elke locatie kan nu al uw secundaire big-IP-VM's bereiken. Het toestaan van poort 80 is handig bij het toestaan van de BIG-IP APM om gebruikers automatisch om te leiden van HTTP naar HTTPS. Deze regel kan zo nodig worden bewerkt om doel-IP's toe te voegen of te verwijderen.

## <a name="manage-big-ip"></a>BIG-IP beheren

Een BIG-IP-systeem wordt beheerd via de gebruikersinterface van de web-config, die toegankelijk is via een van de volgende aanbevolen methoden:

- Vanaf een computer binnen het interne netwerk van het BIG-IP-adres

- Van een VPN-client die is verbonden met het interne netwerk van de BIG-IP-VM

- Gepubliceerd via [Azure AD toepassingsproxy](./application-proxy-add-on-premises-application.md)

U moet een beslissing nemen over de meest geschikte methode voordat u kunt doorgaan met de resterende configuraties. Indien nodig kunt u rechtstreeks verbinding maken met de web-configuratie via internet door het primaire IP-adres van het BIG-IP te configureren met een openbaar IP-adres. Voeg vervolgens een NSG-regel toe om het 8443-verkeer naar dat primaire IP-adres toe te staan. Zorg ervoor dat u de bron beperkt tot uw eigen vertrouwde IP, anders kan iedereen verbinding maken.

Bevestig dat u verbinding kunt maken met de web-configuratie van de BIG-IP-VM en meld u aan met de referenties die zijn opgegeven tijdens de implementatie van de VM:

- Als u verbinding maakt vanaf een VM in het interne netwerk of via VPN, maakt u rechtstreeks verbinding met de BIG-IPs primaire IP- en web-configuratiepoort. Bijvoorbeeld `https://<BIG-IP-VM_Primary_IP:8443`. In uw browser wordt gevraagd of de verbinding onveilig is, maar u kunt de prompt negeren totdat het BIG-IP-adres is geconfigureerd. Als de browser de toegang blokkeert, moet u de cache wissen en het opnieuw proberen.

- Als u de web-configuratie hebt gepubliceerd via toepassingsproxy, gebruikt u de URL die is gedefinieerd om extern toegang te krijgen tot de web-configuratie, zonder de poort toe te toepassingsproxy, bijvoorbeeld `https://big-ip-vm.contoso.com` . De interne URL moet worden gedefinieerd met behulp van de web-configuratiepoort, bijvoorbeeld `https://big-ip-vm.contoso.com:8443` 

Een BIG-IP-systeem kan ook worden beheerd via de onderliggende SSH-omgeving, die doorgaans wordt gebruikt voor opdrachtregeltaken (CLI) en toegang op hoofdniveau. Er zijn verschillende opties voor het maken van verbinding met de CLI, waaronder:

- [Azure Bastion service:](../../bastion/bastion-overview.md)hiermee kunt u vanaf elke locatie snelle en veilige verbindingen maken met elke VM binnen een vNET

- Rechtstreeks verbinding maken via een SSH-client zoals PuTTY via de JIT-benadering

- Seriële console: wordt onder aan de sectie Ondersteuning en probleemoplossing van het menu VM's in de portal aangeboden. Het biedt geen ondersteuning voor bestandsoverdrachten.

- Net als bij de web-configuratie kunt u ook rechtstreeks verbinding maken met de CLI via internet door het primaire IP-adres van het BIG-IP te configureren met een openbaar IP-adres en een NSG-regel toe te voegen om SSH-verkeer toe te staan. Zorg er opnieuw voor dat u de bron beperkt tot uw eigen vertrouwde IP als u deze methode gebruikt.  

## <a name="big-ip-license"></a>BIG-IP-licentie

Een BIG-IP-systeem moet worden geactiveerd en ingericht met de APM-module voordat het kan worden geconfigureerd voor publicatieservices en SHA.

1. Meld u weer aan bij de web-configuratie en selecteer activeren op **de** pagina Algemene **eigenschappen**

2. Voer in **het veld Basisregistratiesleutel** de hoofdwoordgevoelige sleutel in die is opgegeven door F5

3. Laat de **activeringsmethode** ingesteld **op Automatisch** en selecteer **Volgende,** de BIG-IP valideert de licentie en geeft de gebruikersinterfaceovereenkomst (EULA) weer.

4. Selecteer **Accepteren** en wacht tot de activering is voltooid voordat u **Doorgaan selecteert.**

5. Meld u weer aan en selecteer volgende onder aan de pagina **Licentieoverzicht.** Het BIG-IP geeft nu een lijst weer met modules die de verschillende functies bieden die vereist zijn voor SHA. Als u dit niet ziet, gaat u op het hoofdtabblad naar  >  **Systeemresource inrichten.**

6. Controleer de inrichtingskolom op Toegangsbeleid (APM)

![In de afbeelding ziet u het inrichten van toegang](./media/f5ve-deployment-plan/access-provisioning.png)

7. Selecteer **Verzenden** en accepteer de waarschuwing

8. Wacht tot het BIG-IP de nieuwe functies heeft belicht. Deze kan meerdere keren worden gefaseeerd voordat deze volledig wordt initialiseerd. 

9. Wanneer u klaar bent, **selecteert** u Doorgaan **en** selecteert u op het tabblad Over **de optie Het installatieprogramma uitvoeren**

>[!IMPORTANT]
>F5-licenties worden op elk moment beperkt tot verbruik door één BIG-IP VE-instantie. Er kunnen redenen zijn waarom u een licentie van het ene exemplaar [](https://support.f5.com/csp/article/K41458656) naar het andere wilt migreren. Als u dit doet, moet u ervoor zorgen dat u uw proeflicentie voor het actieve exemplaar ingetrokken voordat u deze uit bedrijf neemt, anders gaat de licentie definitief verloren.

## <a name="provision-big-ip"></a>BIG-IP inrichten

Het beveiligen van beheerverkeer van en BIG-IPs web-configuratie is cruciaal. Configureer een certificaat voor apparaatbeheer om het web config-kanaal te beschermen tegen risico's.

1. Ga in de linkernavigatiebalk naar **System**  >  **Certificate Management** Traffic Certificate  >  **Management**  >  **SSL Certificate List**  >  **Import**

2. Selecteer in **de vervolgkeuzelijst Importtype** **PKCS 12 (IIS)** en **Kies Bestand**. Zoek een SSL-webcertificaat met een onderwerpnaam of SAN die betrekking heeft op de FQDN die u in de volgende stappen aan de BIG-IP-VM toewijst

3. Geef het wachtwoord voor het certificaat op en selecteer vervolgens **Importeren**

4. Ga in de linkernavigatiebalk naar  >  **Systeemplatform**

5. Configureer de BIG-IP-VM met een volledig gekwalificeerde hostnaam en de tijdzone voor de omgeving, gevolgd door **Update**

![In de afbeelding ziet u algemene eigenschappen](./media/f5ve-deployment-plan/general-properties.png)

6. Ga in de linkernavigatiebalk naar   >  **Systeemconfiguratieapparaat**  >    >  **NTP**

7. Geef een betrouwbare NTP-bron op en selecteer **Toevoegen,** gevolgd door **Bijwerken.** Bijvoorbeeld: `time.windows.com`

U hebt nu een DNS-record nodig om de BIG-IPs FQDN die in de vorige stappen is opgegeven, om te zetten in het primaire privé-IP-adres. Er moet een record worden toegevoegd aan de interne DNS van uw omgeving of aan het localhost-bestand van een pc die wordt gebruikt om verbinding te maken met de web-configuratie van het BIG-IP-adres. Hoe dan ook, de browserwaarschuwing wordt niet meer weergegeven wanneer u rechtstreeks verbinding maakt met de web-configuratie. Dat wil zeggen, niet via toepassingsproxy of een andere omgekeerde proxy.

## <a name="ssl-profile"></a>SSL-profiel

Als omgekeerde proxy kan een BIG-IP-systeem fungeren als een eenvoudige doorsturende service, ook wel transparante proxy genoemd, of een volledige proxy die actief deelneemt aan uitwisselingen tussen clients en servers.
Een volledige proxy maakt twee afzonderlijke verbindingen: een front-end TCP-clientverbinding samen met een afzonderlijke back-end-TCP-serververbinding, gekoppeld aan een zachte hiaat in het midden. Clients maken aan de ene kant verbinding met de proxylistener, ook wel een **virtuele server** genoemd, en de proxy brengt een afzonderlijke, onafhankelijke verbinding met de back-endserver tot stand. Dit is bi-directioneel aan beide zijden.
In deze volledige proxymodus is het F5 BIG-IP-systeem geschikt voor het controleren van verkeer. Daarom is interactie met aanvragen en antwoorden ook mogelijk. Bepaalde functies, zoals taakverdeling en optimalisatie van webprestaties, evenals geavanceerdere services voor verkeerbeheer, zoals beveiliging op de toepassingslaag, webversnelling, paginaroutering en beveiligde externe toegang, zijn afhankelijk van deze functionaliteit.
Bij het publiceren van op SSL gebaseerde services wordt het proces van het ontsleutelen en versleutelen van verkeer tussen clients en back-endservices afgehandeld door BIG-IP SSL profielen.

Er zijn twee typen profielen:

- **Client-SSL:** De meest voorkomende manier om een BIG-IP-systeem in te stellen voor het publiceren van interne services met SSL is het maken van een Client SSL-profiel. Met een Client SSL-profiel kan een BIG-IP-systeem inkomende clientaanvragen ontsleutelen voordat deze worden verzonden naar een downstreamservice. Vervolgens worden uitgaande back-endreacties versleuteld voordat ze naar clients worden verzonden.

- **Server-SSL:** voor back-endservices die zijn geconfigureerd voor HTTPS, kunt u BIG-IP configureren voor het gebruik van een Server SSL-profiel. Met een Server SSL-profiel versleutelt het BIG-IP de clientaanvraag opnieuw voordat deze wordt verzonden naar de doelback-endservice. Wanneer de server een versleuteld antwoord retourneert, ontsleutelt het BIG-IP-systeem het antwoord en versleutelt het opnieuw, voordat het naar de client wordt verzonden, via het geconfigureerde Client SSL-profiel.

Bij het inrichten van zowel client- als server-SSL-profielen is BIG-IP vooraf geconfigureerd en gereed voor alle SHA-scenario's.

1. Ga in de linkernavigatiebalk naar **System**  >  **Certificate Management** Traffic Certificate  >  **Management**  >  **SSL Certificate List**  >  **Import**

2. Selecteer  **PKCS 12(IIS)** in de vervolgkeuzelijst Importtype

3. Geef een naam op voor het geïmporteerde certificaat, bijvoorbeeld  `ContosoWilcardCert`

4. Selecteer **Bestand kiezen om** naar het SSL-webcertificaat te bladeren met de onderwerpnaam die overeenkomt met het domeinachtervoegsel dat u wilt gebruiken voor gepubliceerde services

5. Geef het **wachtwoord voor** het geïmporteerde certificaat op en selecteer vervolgens **Importeren**

6. Ga in de linkernavigatiebalk naar **Lokale verkeersprofielen**  >    >  **SSL-client**  >   en selecteer **maken**

7. Geef op **de pagina Nieuw client-SSL-profiel** een unieke, gebruiksvriendelijke naam op voor het nieuwe client-SSL-profiel en zorg ervoor dat het bovenliggende profiel is ingesteld op **clientssl**

![In de afbeelding ziet u big-ip bijwerken](./media/f5ve-deployment-plan/client-ssl.png)

8. Schakel het selectievakje aan de rechterkant in de rij **Certificaatsleutelketen** in en selecteer **Toevoegen**

9. Selecteer in de drie vervolgkeuzelijsten het jokertekencertificaat dat u hebt geïmporteerd zonder wachtwoordzin en selecteer **vervolgens**  >  **Voltooid toevoegen.**

![In de afbeelding ziet u het jokerteken big-ip contoso bijwerken](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Herhaal stap 6-9 om een **SSL-servercertificaatprofiel te maken.** Selecteer op het bovenste lint **SSL**  >  **Server**  >  **maken.**

11. Geef op **de pagina Nieuw server-SSL-profiel** een unieke, gebruiksvriendelijke naam op voor het nieuwe SSL-profiel van de server en zorg ervoor dat het bovenliggende profiel is ingesteld **op serverssl**

12. Schakel het selectievakje helemaal rechts in voor de rijen Certificaat en Sleutel en selecteer in de vervolgkeuzelijst uw geïmporteerde certificaat, gevolgd door **Voltooid.**

![In de afbeelding ziet u hoe u big-ip server all profile bijwerkt](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Als u geen SSL-certificaat kunt aanschaffen, kunt u de geïntegreerde zelf-ondertekende BIG-IP-server en client-SSL-certificaten gebruiken. U ziet gewoon een certificaatfout in de browser.

Een laatste stap bij het voorbereiden van een BIG-IP voor SHA is ervoor te zorgen dat de resources die worden gepubliceerd, en ook de directoryservice waar deze voor eenmalige aanmelding van afhankelijk is, kunnen worden gevonden. Een BIG-IP-adres heeft twee bronnen voor naamresolutie, beginnend met het lokale/.../hosts-bestand, of als er geen record wordt gevonden, gebruikt het BIG-IP-systeem elke DNS-service waarbij het is geconfigureerd. De bestandsmethode hosts is niet van toepassing op APM-knooppunten en -pools die gebruikmaken van een FQDN.

1. Ga in de webconfiguratie naar   >    >  **Systeemconfiguratieapparaat-DNS**  >  

2. Voer **in de lijst DNS Lookup Server** het IP-adres van de DNS-server van uw omgevingen in

3. Selecteer **Update**  >  **toevoegen**

Als afzonderlijke en optionele stap kunt u een [LDAP-configuratie](https://somoit.net/f5-big-ip/authentication-using-active-directory) overwegen om BIG-IP-sysadmins te verifiëren bij AD in plaats van lokale BIG-IP-accounts te beheren.

## <a name="update-big-ip"></a>BIG-IP bijwerken

Uw BIG-IP-VM moet worden bijgewerkt om alle nieuwe functies te ontgrendelen die worden behandeld in de [op scenario's gebaseerde richtlijnen.](f5-aad-integration.md) U kunt de TMOS-versie van systemen controleren die met de muisaanwijzer over de BIG-IPs hostnaam in de linkerbovenboven op de hoofdpagina beweegt. Het is raadzaam om versie 15.x en hoger uit te gebruiken, te verkrijgen via [F5 download](https://downloads.f5.com/esd/productlines.jsp). Gebruik de [richtlijnen om](https://support.f5.com/csp/article/K34745165) het hoofdsysteem os (TMOS) bij te werken.

Als het niet mogelijk is om de belangrijkste TMOS bij te werken, kunt u de begeleide configuratie alleen upgraden met behulp van deze stappen.

1. Ga op het hoofdtabblad in de BIG-IP-webconfiguratie naar  >  **Begeleide configuratie voor toegang**

2. Selecteer op **de pagina Begeleide** configuratie de optie **Begeleide configuratie upgraden**

![In de afbeelding ziet u hoe u big-ip bij werkt](./media/f5ve-deployment-plan/update-big-ip.png)

3. Kies in **het dialoogvenster Upgrade Guided Configuration** de optie **File** om het gedownloade use case pack te uploaden en selecteer Uploaden **en installeren**

4. Wanneer de upgrade is voltooid, selecteert u **Doorgaan.**

## <a name="backup-big-ip"></a>Back-up maken van BIG-IP

Nu het BIG-IP-systeem volledig is ingericht, raden we u aan een volledige back-up van de configuratie te maken:

1. Ga naar  >  **Systeemarchieven**  >  **maken**

2. Geef een unieke **bestandsnaam op** en schakel **Versleuteling** in met een wachtwoordzin

3. Stel de **optie Persoonlijke sleutels in** op Opnemen om **ook** een back-up te maken van apparaat- en SSL-certificaten

4. Selecteer **Voltooid** en wacht tot het proces is voltooid

5. Er wordt een bewerkingsstatus weergegeven met een status van het back-upresultaat. Selecteer **OK**

6. Sla het archief gebruikersconfiguratieset (UCS) lokaal op door de koppeling van de back-up te kiezen en **Downloaden te selecteren.**

Als optionele stap kunt u ook een back-up van de hele systeemschijf maken met behulp van Azure-momentopnamen. In tegenstelling tot de back-up van de web-configuratie kan dit tot onvoorziene gebeurtenissen voor het testen tussen TMOS-versies of het terugrollen naar een nieuw systeem. [](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>BIG-IP herstellen

Het herstellen van een BIG-IP volgt een vergelijkbare procedure als de back-up en kan ook worden gebruikt voor het migreren van configuraties tussen BIG-IP-VM's. Details over ondersteunde upgradepaden moeten worden geobserveerd voordat u een back-up importeert.

1. Ga naar  >  **Systeemarchieven**

2. Kies de koppeling van een back-up die u wilt herstellen of selecteer de knop **Uploaden** om te bladeren naar een eerder opgeslagen UCS-archief dat niet in de lijst staat

3. Geef de wachtwoordzin voor de back-up op en selecteer **Herstellen**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>Op het moment van schrijven is de Cmdlet AzVmSnapshot beperkt tot het herstellen van de meest recente momentopname, op basis van datum. Momentopnamen worden opgeslagen in de hoofdmap van de resourcegroep van de VM. Let erop dat met het herstellen van momentopnamen een Azure-VM opnieuw wordt opgestart, dus tijd dit zorgvuldig.

## <a name="additional-resources"></a>Aanvullende bronnen

-   [BIG-IP VE-wachtwoord opnieuw instellen in Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Het wachtwoord opnieuw instellen zonder de portal te gebruiken](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [De NIC wijzigen die wordt gebruikt voor BIG-IP VE-beheer](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Routes in één NIC-configuratie](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Volgende stappen

Selecteer een [implementatiescenario en](f5-aad-integration.md) start uw implementatie.
