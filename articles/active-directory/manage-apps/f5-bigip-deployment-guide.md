---
title: Azure AD beveiligde hybride toegang met F5 implementatie handleiding | Microsoft Docs
description: Zelf studie voor het implementeren van een virtuele machine met F5 BIG-IP (VE) in azure IaaS voor beveiligde hybride toegang
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
ms.openlocfilehash: f962bf131b87f17712186145b8c8b8e6090f7002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730653"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Zelf studie voor het implementeren van een VM met een virtuele editie van F5 in azure IaaS voor beveiligde hybride toegang

In deze zelf studie wordt u begeleid bij het einde van het proces voor het implementeren van BIG-IP Vitural Edition (VE) in azure IaaS. Aan het einde van deze zelf studie hebt u het volgende nodig:

- Een volledig voor bereide, BIG-IP-virtuele machine (VM) voor het model leren van een SHA-concept (Secure Hybrid Access) voor het maken van concepten

- Een staging-instantie die moet worden gebruikt voor het testen van nieuwe BIG-IP-systeem updates en hotfixes

## <a name="prerequisites"></a>Vereisten

Eerdere F5 BIG-IP-ervaring of kennis is niet nodig, maar we raden u echter aan de [terminologie van F5 BIG-IP](https://www.f5.com/services/resources/glossary)te verkennen. Voor het implementeren van een BIG-IP-adres in azure voor SHA is het volgende vereist:

- Een betaald Azure-abonnement of een gratis [proef abonnement](https://azure.microsoft.com/free/)van 12 maanden.

- Een van de volgende F5 BIG-IP-licentie-Sku's

  - F5 BIG-IP-® beste bundel

  - Zelfstandige licentie voor F5 BIG-IP Access Policy Manager™ (APM)

  - F5 BIG-IP Access Policy Manager™ (APM)-invoeg toepassing voor een bestaande BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM)
  
  - 90-daagse [proef licentie](https://www.f5.com/trial/big-ip-trial.php)voor volledige-IP-functies.

- Een certificaat voor joker tekens of alternatieve naam voor onderwerp (SAN) om webtoepassingen te publiceren via Secure Socket Layer (SSL). [Laten we](https://letsencrypt.org/) een gratis 90 dagen certificaat voor testen aanbieden.

- Een SSL-certificaat voor het beveiligen van de BIG-IPs beheer interface. Een certificaat dat wordt gebruikt voor het publiceren van web-apps kan worden gebruikt als het onderwerp overeenkomt met de FQDN (Fully Qualified Domain Name) van het BIG-IP-adres. Een Joker teken dat is gedefinieerd met een onderwerp *. contoso.com is bijvoorbeeld geschikt voor `https://big-ip-vm.contoso.com:8443`

Implementaties van VM'S en basis systeem configuraties duren ongeveer 30 minuten, op welk moment uw BIG-IP-platform gereed is voor het implementeren van een van de hieronder [vermelde SHA](f5-aad-integration.md)-scenario's.

Voor het testen van de scenario's gaat deze zelf studie ervan uit dat het BIG-IP-adres wordt geïmplementeerd in een Azure-resource groep met een Active Directory (AD)-omgeving. De omgeving moet bestaan uit een domein controller (DC) en web host (IIS)-Vm's. Het is ook mogelijk om deze servers in andere locaties naar de BIG-IP-VM te brengen, waardoor de BIG-IP een regel van het gezichts vermogen heeft voor elk van de rollen die nodig zijn om een bepaald scenario te ondersteunen. Scenario's waarin de BIG-IP-VM is verbonden met een andere omgeving via een VPN-verbinding, worden ook ondersteund.

Als u niet over de items beschikt die hier worden beschreven, kunt u een volledige AD-domein omgeving in azure implementeren met behulp van dit [script](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). Een verzameling voor beelden van test toepassingen kan ook programmatisch worden geïmplementeerd op een IIS-webhost met behulp van deze [geautomatiseerde automatisering](https://github.com/jeevanbisht/DemoSuite).

>[!NOTE]
>De [Azure Portal](https://portal.azure.com/#home) is voortdurend in ontwikkeling, waardoor sommige stappen in deze zelf studie afwijken van de werkelijke indeling die in de Azure Portal is waargenomen.

## <a name="azure-deployment"></a>Azure-implementatie

Een BIG-IP-adres kan in verschillende topologieën worden geïmplementeerd. Deze hand leiding is gericht op een implementatie van één netwerk interface (NIC). Als uw BIG-IP-implementatie echter meerdere netwerk interfaces vereist voor hoge Beschik baarheid, netwerk scheiding of meer dan 1 GB door Voer, overweeg dan om F5's vooraf gecompileerde [Azure Resource Manager (arm)-Sjablonen](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)te gebruiken.

Voer de volgende taken uit om BIG-IP VE te implementeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/#home) met een account met machtigingen voor het maken van vm's. Bijvoorbeeld Inzender

2. Typ in het zoekvak bovenste lint **Marketplace**, gevolgd door **Enter**

3. Typ **F5** in het Marketplace-filter, gevolgd door **Enter**

4. Selecteer **+ toevoegen** in het bovenste lint en typ **F5** in het Marketplace-filter, gevolgd door **Enter**

5. Selecteer **F5 BIG-IP Virtual Edition (BYOL)**  >  **Selecteer een software plan**  >  **F5 BIG-IP ve-all (BYOL, 2 opstart locaties)**

6. Selecteer **Maken**.

![De afbeelding toont de stappen voor het selecteren van een software abonnement](./media/f5ve-deployment-plan/software-plan.png)

7. Door loop het menu **basis informatie** en gebruikt u de volgende instellingen:

 |  Projectgegevens     |  Waarde     |
 |:-------|:--------|
 |Abonnement|Doel abonnement voor de implementatie van een BIG-IP-VM|
 |Resourcegroep | Bestaande Azure-resource groep de grootschalige IP-VM wordt geïmplementeerd in of maakt er een. Moet dezelfde resource groep zijn als uw DC-en IIS-Vm's|
 | **Exemplaardetails**|  |
 |VM-naam| Voor beeld BIG-IP-VM |
 |Region | Doel-Azure geo voor BIG-IP-VM |
 |Beschikbaarheidsopties| Alleen inschakelen als VM in productie wordt gebruikt|
 |Installatiekopie| F5 BIG-IP VE-ALL (BYOL, 2 opstart locaties)|
 |Azure Spot-exemplaar| Nee, maar u kunt indien nodig |
 |Grootte| De minimum specificatie moet 2 Vcpu's en 8 GB geheugen zijn|
 |**Beheerdersaccount**|  |
 |Verificatietype|Selecteer nu een wacht woord. U kunt later naar een sleutel paar overschakelen |
 |Gebruikersnaam|De identiteit die wordt gemaakt als een lokaal IP-account voor toegang tot de beheer interfaces. Gebruikers naam is hoofdletter gevoelig.|
 |Wachtwoord|Beheerders toegang beveiligen met een sterk wacht woord|
 |**Regels voor binnenkomende poort**|  |
 |Openbare poorten voor inkomend verkeer|Geen|

8. Selecteer **volgende: schijven** waarbij alle standaard waarden worden gebruikt en selecteer **volgende: netwerken**.

9. Voer deze instellingen uit in het menu **netwerk** .

 |Netwerkinterface|      Waarde |
 |:--------------|:----------------|
 |Virtueel netwerk|Dezelfde Azure-VNet die wordt gebruikt door uw DC-en IIS-Vm's, of er een maken|
 |Subnet| Hetzelfde interne Azure-subnet als uw DC-en IIS-Vm's, of er een maken|
 |Openbare IP |  Geen|
 |NIC-netwerk beveiligings groep| Selecteer geen als het Azure-subnet dat u in de vorige stappen hebt geselecteerd, al is gekoppeld aan een netwerk beveiligings groep (NSG); anders selecteert u basis|
 |Netwerken versnellen| Aan |
 |**Taakverdeling**|     |
 |Taak verdeling van virtuele machine| Nee|

10. Selecteer **volgende: beheer** en Voltooi deze instellingen.

 |Bewaking|    Waarde |
 |:---------|:-----|
 |Gedetailleerde bewaking| Aan|
 |Diagnostische gegevens over opstarten|Inschakelen met aangepast opslag account. Maakt het mogelijk om verbinding te maken met de Secure Shell (SSH)-interface via de seriële console in de Azure Portal. Selecteer een beschikbaar Azure-opslag account|
 |**Identiteit**|  |
 |Door het systeem toegewezen beheerde identiteit|Aan|
 |Azure Active Directory|BIG-IP biedt momenteel geen ondersteuning voor deze optie|
 |**Automatisch afsluiten**|    |
 |Automatisch afsluiten inschakelen| Als u wilt testen, kunt u overwegen om de BIG-IP-VM in te stellen op dagelijks afsluiten|

11. Selecteer **volgende: Geavanceerd** alle standaard waarden laten staan en **volgende selecteren: Tags**.

12. Selecteer **volgende: controleren + maken** om de configuraties van uw Big-IP-VM te controleren voordat u **maken** voor de implementatie selecteert.

13. Tijd voor het volledig implementeren van een BIG-IP-VM is doorgaans 5 minuten. Als u volt ooien niet selecteert, **gaat u naar resource** en klikt u in het menu links op het Azure Portal en selecteert u **resource groepen** om naar uw nieuwe Big-IP-VM te gaan. Als het maken van de virtuele machine mislukt, selecteert u **vorige** en **volgende**.

## <a name="network-configuration"></a>Netwerkconfiguratie

Wanneer de BIG-IP-VM voor het eerst wordt opgestart, wordt de NIC ingericht met een **primair** particulier IP-adres dat is uitgegeven door de Dynamic Host Configuration Protocol (DHCP)-service van het Azure-subnet waarmee deze is verbonden. Dit IP-adres wordt gebruikt door het TMOS (Traffic Management Operating System) van het BIG-IP-verkeer om te communiceren met:

- Communiceren met andere hosts en services

- Uitgaande toegang tot het open bare Internet

- Inkomende toegang tot de BIG-IPs Web config-en SSH-beheer interfaces

Door een van deze beheer interfaces beschikbaar te maken voor het Internet, wordt de BIG-IPs kwets baarheid verhoogd, waardoor het BIG-IPs primaire IP-adres niet is ingericht met een openbaar IP-adres tijdens de implementatie. In plaats daarvan wordt een secundair intern IP-adres en een bijbehorend openbaar IP-adres ingericht voor het publiceren van services.
Met deze 1 tot 1 toewijzing tussen een open bare IP-adres van een virtuele machine en een particulier IP-adres kan extern verkeer een virtuele machine bereiken. Een Azure NSG-regel is echter ook vereist voor het toestaan van het verkeer, op ongeveer dezelfde manier als een firewall.

Het diagram toont een implementatie met één NIC van een BIG-IP-adres in azure, geconfigureerd met een primair IP-adres voor algemene bewerkingen en beheer, en een afzonderlijke virtuele-server Ip's voor het publiceren van services.
In deze regeling staat een NSG-regel extern verkeer toe dat is bestemd voor `intranet.contoso.com` om naar het open bare IP-adres voor de gepubliceerde service te sturen voordat ze worden doorgestuurd naar de VPN-server van het Big-IP-adres.

![De installatie kopie toont de implementatie van één NIC ](./media/f5ve-deployment-plan/single-nic-deployment.png) standaard, persoonlijke en open bare ip's die worden uitgegeven aan Azure-vm's zijn altijd dynamisch, en worden daarom waarschijnlijk gewijzigd bij elke herstart van een virtuele machine. Vermijd onvoorziene verbindings problemen door het IP-adres van BIG-IPs beheer te wijzigen in statisch en hetzelfde te doen voor secundaire IP-adressen die worden gebruikt voor het publiceren van services.

1. Ga in het menu van uw Big IP-adres naar **instellingen**  >  **netwerken**

2. Selecteer in de weer gave netwerken de koppeling naar de rechter kant van de **netwerk interface**

![De afbeelding toont de netwerk configuratie](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM-namen worden wille keurig gegenereerd tijdens de implementatie.

3. Selecteer in het linkerdeel venster **IP-configuraties** en selecteer vervolgens **ipconfig1** rij

4. Stel de optie voor het **toewijzen van IP-** adressen in op statisch en wijzig indien nodig het primaire IP-adres van de Big-IP-vm's. Selecteer vervolgens **Opslaan** en sluit het menu ipconfig1

>[!NOTE]
>U gebruikt dit primaire IP-adres om verbinding te maken en de BIG-IP-VM te beheren.

5. Selecteer **+ toevoegen** op het bovenste lint en geef een naam op voor een secundair particulier IP-adres, bijvoorbeeld ipconfig2

6. Stel de **toewijzings** optie van de instellingen voor het privé-IP-adres in op **statisch**. Door de volgende hogere of lagere opeenvolgende IP-adressen te gebruiken, kunt u dingen best Ellen.

7. Stel het open bare IP-adres in op **koppelen** en selecteer **maken** .

8. Geef een naam op voor het nieuwe open bare IP-adres, bijvoorbeeld BIG-IP-VM_ipconfig2_Public

9. Als u hierom wordt gevraagd, stelt u de **SKU** in op Standard

10. Als u hierom wordt gevraagd, stelt u de **laag** in op **Global** en

11. Stel de **toewijzings** optie in op **statisch** en selecteer vervolgens twee keer **OK**

Uw BIG-IP-VM is nu klaar voor het instellen van:

- **Primair particulier IP**-adres: toegewezen voor het beheren van de Big-IP-VM via het hulp programma web config en SSH. Het wordt door het BIG-IP-systeem als eigen **IP-adres** gebruikt om verbinding te maken met de gepubliceerde back-end-services. Daarnaast maakt het verbinding met externe services, zoals NTP, AD en LDAP.

- **Secundair particulier IP-adres**: wordt gebruikt bij het maken van een virtuele apm-server met een Big IP-adres om te Luis teren naar binnenkomende aanvragen naar een of meer gepubliceerde service (s).

- **Openbaar IP-adres** dat is gekoppeld aan het secundaire particuliere IP-adres, client verkeer van het open bare Internet om de Big-IP-server te bereiken voor de gepubliceerde service (s).

In het voor beeld wordt de relatie 1 tot 1 tussen een open bare en privé-IP-adressen van een virtuele machine geïllustreerd. Een Azure VM NIC kan slechts één primair IP-adres hebben, en eventuele andere Ip's worden altijd als secundair aangeduid.

>[!NOTE]
>U hebt de secundaire IP-toewijzingen nodig voor het publiceren van BIG-IP-services.

![In deze afbeelding worden alle secundaire Ip's weer gegeven](./media/f5ve-deployment-plan/secondary-ips.png)

Als u SHA wilt implementeren met behulp van de **begeleide configuratie** van Big-IP-toegang, herhaalt u stap 5-11 om extra persoonlijke en open bare IP-paren te maken voor elke extra service die u wilt publiceren via de Big-IP apm. Dezelfde benadering kan ook worden gebruikt als u Services publiceert met BIG-IPs **Geavanceerde configuratie**. In dat scenario hebt u echter de mogelijkheid om open bare IP-overhead te vermijden door gebruik te maken van een [SNI-configuratie (server name indicator)](https://support.f5.com/csp/#/article/K13452) . In deze configuratie accepteert een virtueel-IP-server alle client verkeer dat wordt ontvangen en stuurt het de route naar de bestemming.

## <a name="dns-configuration"></a>DNS-configuratie

Het is essentieel dat DNS is geconfigureerd voor clients om uw gepubliceerde SHA-Services om te zetten naar de open bare IP (s) van uw BIG-IP-VM.

Bij de volgende stappen wordt ervan uitgegaan dat de DNS-zone van het open bare domein dat wordt gebruikt voor uw SHA-Services, wordt beheerd in Azure. Dezelfde DNS-principes voor het maken van een locator-record zijn echter nog steeds van toepassing, ongeacht waar uw DNS-zone wordt beheerd.

1. Als nog niet is geopend, vouwt u het menu links van de portal uit en navigeert u naar uw BIG-IP-VM via de optie **resource groepen** .

2. Ga in het menu van uw Big IP-adres naar **instellingen**  >  **netwerken**

3. Selecteer in de weer gave BIG-IP-Vm's netwerk het eerste secundaire IP-adres in de vervolg keuzelijst IP-configuratie en selecteer de koppeling voor het **open bare IP-adres** van de NIC

![Scherm opname van het open bare IP-adres van de NIC](./media/f5ve-deployment-plan/networking.png)

4. Klik onder de sectie **instellingen** in het linkerdeel venster op **configuratie** om het menu met open bare IP-en DNS-eigenschappen voor het geselecteerde secundaire IP-adres te openen.

5. Selecteer en **Maak** een alias record en selecteer uw **DNS-zone** in de vervolg keuzelijst. Als een DNS-zone nog niet bestaat, wordt deze mogelijk buiten Azure beheerd of u kunt er een maken voor het domein achtervoegsel dat u in azure AD zou hebben gecontroleerd.

6. Gebruik de volgende informatie om de eerste DNS-alias record te maken:

 | Veld | Waarde |
 |:-------|:-----------|
 |Abonnement| Hetzelfde abonnement als de BIG-IP-VM|
 |DNS-zone| DNS-zone die gemachtigd is voor het geverifieerde domein achtervoegsel dat door uw gepubliceerde websites wordt gebruikt, bijvoorbeeld www.contoso.com |
 |Name | De hostnaam die u opgeeft, wordt omgezet in het open bare IP-adres dat is gekoppeld aan het geselecteerde secundaire IP-adres. Zorg ervoor dat u de juiste DNS-IP-toewijzingen definieert. Zie laatste installatie kopie in de sectie configuratie van netwerken, bijvoorbeeld intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |TTL-eenheden | Tijden |

7. Selecteer **maken** voor Azure om die instellingen op open bare DNS op te slaan.

8. Behoud het **DNS-naam label (optioneel)** en selecteer **Opslaan** voordat u het open bare IP-menu sluit.

9. Herhaal stap 1 tot en met 6 om extra DNS-records te maken voor elke service die u wilt publiceren met behulp van de begeleide configuratie van BIG-IP.

  Als u DNS-records hebt geïmplementeerd, kunt u een van de online-hulpprogram ma's, zoals [DNS-controle](https://dnschecker.org/) , gebruiken om te controleren of een gemaakte record is door gegeven op alle globale open bare DNS-servers.

  Als u de naam ruimte van uw DNS-domein beheert met behulp van een externe provider zoals [GoDaddy](https://www.godaddy.com/), moet u records maken met behulp van hun eigen DNS-beheer faciliteit.

>[!NOTE]
>U kunt ook het lokale hosts-bestand van een PC gebruiken als u DNS-records wilt testen en regel matig wilt overschakelen. U kunt het localhost-bestand op een Windows-computer openen door op het toetsen bord op Win + R te drukken en de **Stuur Programma's** voor het woord in het vak uitvoeren te verzenden. Mindful dat een localhost-record alleen een DNS-omzetting biedt voor de lokale PC, niet voor andere clients.

## <a name="client-traffic"></a>Client verkeer

Standaard zijn Azure VNets en gekoppelde subnetten particuliere netwerken die geen Internet verkeer kunnen ontvangen. De NIC van uw BIG-IP-VM moet worden gekoppeld aan de NSG die u tijdens de implementatie hebt opgegeven. Voor extern webverkeer om de BIG-IP-VM te bereiken, moet u een regel voor binnenkomende NSG definiëren waarmee poorten 443 (HTTPS) en 80 (HTTP) via het open bare Internet kunnen worden toegestaan.

1. Selecteer in het hoofd **overzichts** menu van de Big-IP-VM de optie **netwerken**

2. Selecteer binnenkomende regel **toevoegen** om de volgende NSG-regel eigenschappen in te voeren:

 |     Veld   |   Waarde        |
 |:------------|:------------|
 |Bron| Elk|
 |Poortbereiken van bron| *|
 |Doel-IP-adressen|Door komma's gescheiden lijst met alle secundaire privé Ip's van BIG-IP-VM|
 |Doelpoorten| 80.443|
 |Protocol| TCP |
 |Bewerking| Toestaan|
 |Prioriteit|De laagste beschik bare waarde tussen 100 en 4096|
 |Name | Een beschrijvende naam, bijvoorbeeld: `BIG-IP-VM_Web_Services_80_443`|

3. Selecteer **toevoegen** om de wijzigingen door te voeren en sluit het **netwerk** menu.

HTTP-en HTTPS-verkeer vanaf elke locatie is nu toegestaan om alle secundaire interfaces van uw BIG-IP-Vm's te bereiken. Het toestaan van poort 80 is handig voor het automatisch omleiden van gebruikers van HTTP naar HTTPS door de BIG-IP APM. Deze regel kan zo nodig worden bewerkt om IP-adressen toe te voegen of te verwijderen.

## <a name="manage-big-ip"></a>BIG-IP beheren

Een BIG-IP-systeem wordt beheerd via de web-gebruikers interface van de Webconfiguratie, die kan worden geopend via een van de volgende aanbevolen methoden:

- Van een computer binnen het interne netwerk van het BIG-IP-adres

- Van een VPN-client die is verbonden met het interne netwerk van een BIG-IP-VM

- Gepubliceerd via [Azure AD-toepassingsproxy](./application-proxy-add-on-premises-application.md)

U moet de meest geschikte methode kiezen voordat u kunt door gaan met de resterende configuraties. Als dat nodig is, kunt u rechtstreeks verbinding maken met de Web-config door het primaire IP-adres van het BIG IP-adres te configureren met een openbaar IP. Voeg vervolgens een NSG-regel toe om het 8443-verkeer naar dat primaire IP toe te staan. Zorg ervoor dat u de bron beperkt tot uw eigen vertrouwde IP, anders kan iedereen verbinding maken.

Nadat u klaar bent, bevestigt u dat u verbinding kunt maken met de Web-config van de BIG-IP-VM en aanmelden met de referenties die zijn opgegeven tijdens de implementatie van de virtuele machine:

- Als u verbinding maakt vanaf een virtuele machine in het interne netwerk of via VPN, maakt u rechtstreeks verbinding met de BIG-IPs primaire IP-en Web-configuratie poort. Bijvoorbeeld `https://<BIG-IP-VM_Primary_IP:8443`. In uw browser wordt gevraagd of de verbinding onveilig is, maar u kunt de prompt negeren totdat het BIG-IP-adres is geconfigureerd. Als de browser de toegang blokkeert, wist u de cache en probeert u het opnieuw.

- Als u de Web-config via toepassings proxy hebt gepubliceerd, gebruikt u de URL die is gedefinieerd voor toegang tot de Webconfiguratie extern, zonder de poort toe te voegen, bijvoorbeeld `https://big-ip-vm.contoso.com` . De interne URL moet worden gedefinieerd met behulp van de Web-configuratie poort, bijvoorbeeld `https://big-ip-vm.contoso.com:8443` 

Een BIG-IP-systeem kan ook worden beheerd via de onderliggende SSH-omgeving, die meestal wordt gebruikt voor opdracht regel taken en toegang op toegangs punt niveau. Er zijn verschillende opties voor het maken van verbinding met de CLI, waaronder:

- [Azure Bastion-service](../../bastion/bastion-overview.md): maakt snelle en veilige verbindingen mogelijk met elke virtuele machine binnen een vNET, vanaf elke locatie

- Rechtstreeks verbinding maken via een SSH-client zoals PuTTy via de JIT-benadering

- Seriële console: wordt aan de onderkant van de sectie ondersteuning en probleem oplossing van het menu Vm's in de portal geboden. Bestands overdrachten worden niet ondersteund.

- Net als bij de Web-config kunt u ook rechtstreeks verbinding maken met de CLI via internet door het primaire IP-adres van het BIG IP-netwerk te configureren met een openbaar IP-adres en een NSG-regel toe te voegen om SSH-verkeer toe te staan. Als u deze methode gebruikt, moet u de bron ook beperken tot uw eigen vertrouwde IP-adres.  

## <a name="big-ip-license"></a>BIG-IP-licentie

Een BIG-IP-systeem moet worden geactiveerd en ingericht met de APM-module voordat het kan worden geconfigureerd voor het publiceren van services en SHA.

1. Meld u weer aan bij Web config en selecteer op de pagina **algemene eigenschappen** **activeren**

2. Voer in het veld **basis registratie sleutel** de hoofdletter gevoelige sleutel van F5 in

3. Zorg ervoor dat de **activerings methode** is ingesteld op **automatisch** en selecteer **volgende**. het Big-IP-adres valideert de licentie en de gebruiksrecht overeenkomst (EULA) wordt weer gegeven.

4. Selecteer **accepteren** en wacht totdat de activering is voltooid, voordat u **door gaan** selecteert.

5. Meld u weer aan en klik onder aan de pagina licentie overzicht op **volgende**. In het BIG-IP-adres wordt nu een lijst weer gegeven met modules die de verschillende functies bieden die vereist zijn voor SHA. Als u dit niet ziet, gaat u op het tabblad Main naar **systeem**  >  **resource inrichten**.

6. Controleer de inrichtings kolom voor het toegangs beleid (APM)

![De afbeelding toont het inrichten van toegang](./media/f5ve-deployment-plan/access-provisioning.png)

7. Selecteer **verzenden** en accepteer de waarschuwing

8. Wacht even tot het BIG-IP-adres is voltooid om de nieuwe functies te verlichten. Het kan enige tijd duren voordat het proces volledig is geïnitialiseerd. 

9. Als u klaar bent, selecteert u **door gaan** en selecteert u **het installatie programma uitvoeren** op het tabblad **info** .

>[!IMPORTANT]
>F5-licenties zijn beperkt om te worden gebruikt door één BIG-IP VE-exemplaar tegelijk. Er kunnen redenen zijn waarom u een licentie van de ene instantie naar de andere wilt migreren. als dat zo is, moet u ervoor zorgen dat u de licentie voor de proef versie [intrekt](https://support.f5.com/csp/article/K41458656) op het actieve exemplaar voordat u deze uit bedrijf neemt, anders gaat de licentie permanent verloren.

## <a name="provision-big-ip"></a>Een BIG-IP-adres inrichten

Het beveiligen van beheer verkeer van en naar BIG-IPs Webconfiguratie is een cruciaal onderdeel. Configureer een certificaat voor Apparaatbeheer om het webconfiguratiesysteem te beschermen tegen inbreuk.

1. Ga in de linkernavigatiebalk naar **systeem**  >  **certificaat beheer**  >  **Traffic certificaat beheer**  >  **SSL-certificaat lijst**  >  **importeren**

2. Selecteer in de vervolg keuzelijst **type import** **PKCS 12 (IIS)** en **Kies bestand**. Zoek naar een SSL-webcertificaat met een onderwerpnaam of SAN dat de FQDN-naam bevat die u in de volgende stappen een BIG-IP-VM wilt toewijzen

3. Geef het wacht woord op voor het certificaat en selecteer vervolgens **importeren**

4. Ga in de linker navigatie balk naar het **systeem**  >  **platform**

5. Configureer de BIG-IP-VM met een volledig gekwalificeerde hostnaam en de tijd zone voor de omgeving, gevolgd door **Update**

![De afbeelding bevat algemene eigenschappen](./media/f5ve-deployment-plan/general-properties.png)

6. Ga in de linker navigatie balk naar **systeem**  >  **configuratie**  >  **apparaat**  >  **NTP**

7. Geef een betrouw bare NTP-bron op en selecteer **toevoegen**, gevolgd door **Update**. Bijvoorbeeld: `time.windows.com`

U hebt nu een DNS-record nodig om de BIG-IPs FQDN die in de vorige stappen is opgegeven, op te lossen naar het primaire privé-IP-adres. Er moet een record worden toegevoegd aan de interne DNS van uw omgeving of aan het localhost-bestand van een PC die wordt gebruikt om verbinding te maken met de Webconfiguratie van het BIG-IP-adres. In beide gevallen moet de browser waarschuwing niet meer worden weer gegeven wanneer u rechtstreeks verbinding maakt met de Webconfiguratie. Dat wil zeggen, niet via een toepassings proxy of een andere omgekeerde proxy.

## <a name="ssl-profile"></a>SSL-profiel

Als omgekeerde proxy kan een BIG-IP-systeem fungeren als een eenvoudige Doorstuur Service, ook wel een transparante proxy genoemd, of een volledige proxy die actief deelneemt aan uitwisselingen tussen clients en servers.
Een volledige proxy maakt twee afzonderlijke verbindingen: een front-end-TCP-client verbinding samen met een afzonderlijke back-end TCP-server verbinding, gekoppeld aan een tijdelijke onderbreking in het midden. Clients maken verbinding met de proxy-listener op een end, anders aangeduid als een **virtuele server**, en de proxy brengt een afzonderlijke, onafhankelijke verbinding tot stand met de back-endserver. Dit is twee richtingen aan beide zijden.
In deze volledige proxy modus is het F5 BIG-IP-systeem in staat om verkeer te inspecteren en daarom is het ook mogelijk om te communiceren met aanvragen en antwoorden. Bepaalde functies, zoals taak verdeling en optimalisatie van webprestaties, en meer geavanceerde Traffic Management-Services zoals Application Layer-beveiliging, webversnelling, pagina Routering en beveiligde externe toegang, zijn afhankelijk van deze functionaliteit.
Bij het publiceren van op SSL gebaseerde services wordt het proces van het ontsleutelen en versleutelen van verkeer tussen clients en back-end-services afgehandeld door BIG-IP SSL profielen.

Er bestaan twee typen profielen:

- **Client-SSL**: de meest voorkomende manier om een Big-IP-systeem in te stellen voor het publiceren van interne services met SSL is het maken van een client-SSL-profiel. Met een client-SSL-profiel kan een BIG-IP-systeem inkomende client aanvragen ontsleutelen voordat deze naar een downstream-service worden verzonden. Vervolgens worden uitgaande back-end-antwoorden versleuteld voordat ze naar clients worden verzonden.

- **Server-SSL**: voor back-end-services die zijn geconfigureerd voor HTTPS, kunt u Big-IP configureren voor het gebruik van een server-SSL-profiel. Met een SSL-Profiel van de server versleutelt de BIG-IP de client aanvraag opnieuw voordat deze wordt verzonden naar de back-end-doel service. Wanneer de server een versleuteld antwoord retourneert, ontsleutelt het BIG-IP-systeem de reactie en versleutelt deze opnieuw, voordat deze wordt verzonden naar de client via het geconfigureerde client-SSL-profiel.

Voor het inrichten van zowel client-als server-SSL-profielen is het BIG-IP vooraf geconfigureerd en gereed voor alle SHA-scenario's.

1. Ga in de linkernavigatiebalk naar **systeem**  >  **certificaat beheer**  >  **Traffic certificaat beheer**  >  **SSL-certificaat lijst**  >  **importeren**

2. Selecteer in de vervolg keuzelijst **type import** **PKCS 12 (IIS)**

3. Geef een naam op voor het geïmporteerde certificaat, bijvoorbeeld  `ContosoWilcardCert`

4. Selecteer **bestand kiezen** om te bladeren naar het SSL-webcertificaat waarvan de onderwerpnaam overeenkomt met het domein achtervoegsel dat u wilt gebruiken voor gepubliceerde Services

5. Geef het **wacht woord** op voor het geïmporteerde certificaat en selecteer vervolgens **importeren**

6. Ga in de linker navigatie balk naar **lokale verkeers**  >  **profielen**  >  **SSL**-  >  **client** en selecteer vervolgens **maken**

7. Geef op de pagina **nieuw SSL-profiel voor client** een unieke beschrijvende naam op voor het nieuwe client SSL-profiel en zorg ervoor dat het bovenliggende profiel is ingesteld op **clientssl**

![De afbeelding toont update Big-IP](./media/f5ve-deployment-plan/client-ssl.png)

8. Selecteer het selectie vakje uiterst rechts in de rij **certificaat sleutel keten** en selecteer **toevoegen**

9. Selecteer in de drie vervolg keuzelijsten het Joker teken dat u hebt geïmporteerd zonder wachtwoordzin en selecteer vervolgens **toevoegen**  >  **voltooid**.

![De afbeelding toont update Big-IP contoso Joker teken](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Herhaal de stappen 6-9 om een **SSL-server certificaat profiel** te maken. Selecteer in het bovenste lint de optie **SSL**-  >  **Server**  >  **maken**.

11. Geef op de pagina **nieuw SSL-profiel voor Server** een unieke beschrijvende naam op voor het nieuwe SSL-Profiel van de server en zorg ervoor dat het bovenliggende profiel is ingesteld op **serverssl**

12. Selecteer het selectie vakje uiterst rechts voor het certificaat en de sleutel rijen en selecteer in de vervolg keuzelijst het geïmporteerde certificaat, gevolgd door **voltooid**.

![De afbeelding toont een update van een Big-IP-server voor alle profielen](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Despair niet als u een SSL-certificaat niet meer kunt aanschaffen, gebruikt u de geïntegreerde zelf-ondertekende BIG-IP-server en SSL-client certificaten. Er wordt alleen een certificaat fout weer geven in de browser.

Een van de laatste stappen voor het voorbereiden van een BIG-IP voor SHA is om ervoor te zorgen dat de bronnen kunnen worden gevonden en ook de Directory service afhankelijk is van SSO. Een BIG-IP heeft twee bronnen voor naam omzetting, te beginnen met het lokale/.../hosts-bestand, of als een record niet is gevonden, gebruikt het BIG-IP-systeem welke DNS-service is geconfigureerd met. De methode hosts-bestand is niet van toepassing op APM-knoop punten en-Pools die gebruikmaken van een FQDN.

1. Ga in de Web-config naar het **systeem**  >  **configuratie**  >  **apparaat**  >  **DNS**

2. Voer in de **lijst DNS-Zoek server** het IP-adres in van de DNS-server van uw omgevingen

3. Selecteer   >  **Update** toevoegen

Als afzonderlijke en optionele stap kunt u overwegen een LDAP- [configuratie](https://somoit.net/f5-big-ip/authentication-using-active-directory) te gebruiken voor het verifiëren van de systeem eigen-IP-sysadmins tegen ad in plaats van lokale Big-IP-accounts te beheren.

## <a name="update-big-ip"></a>BIG-IP bijwerken

Uw BIG-IP-VM moet worden bijgewerkt om alle nieuwe functies te ontgrendelen die worden behandeld in de [richt lijnen op basis van scenario's](f5-aad-integration.md). U kunt de TMOS-versie van het systeem controleren met de muis aanwijzer over de BIG-IPs hostnaam in de linkerbovenhoek van de hoofd pagina. Het is raadzaam om V15. x en hoger te kunnen uitvoeren, die u kunt [downloaden van F5](https://downloads.f5.com/esd/productlines.jsp). Gebruik de [richt lijnen](https://support.f5.com/csp/article/K34745165) voor het bijwerken van het hoofd systeem besturingssysteem (TMOS).

Als het bijwerken van de hoofd-TMOS niet mogelijk is, kunt u overwegen ten minste de begeleide configuratie alleen bij te werken met de volgende stappen.

1. Ga **naar de**  >  **begeleidende configuratie** op het tabblad Main in de Big-IP Web config

2. Selecteer op de pagina **begeleide configuratie** de optie **upgrade begeleide configuratie** .

![De afbeelding laat zien hoe u een Big-IP-adres kunt bijwerken](./media/f5ve-deployment-plan/update-big-ip.png)

3. Kies in het dialoog venster **begeleid configuratie bijwerken** de **optie bestand** om het gedownloade use-case Pack te uploaden en selecteer **uploaden en installeren**

4. Wanneer de upgrade is voltooid, selecteert u **door gaan**.

## <a name="backup-big-ip"></a>Back-up maken van BIG-IP

Wanneer het BIG-IP-systeem nu volledig is ingericht, wordt u aangeraden een volledige back-up te maken van de configuratie:

1. Ga naar **systeem**  >  **Archief**  >  **maken**

2. Geef een unieke **Bestands naam** op en Schakel **versleuteling** in met een wachtwoordzin

3. Stel de optie **persoonlijke sleutels** in op **include** om ook back-ups te maken van apparaat-en SSL-certificaten

4. Selecteer **voltooid** en wacht totdat het proces is voltooid

5. Er wordt een bewerkings status weer gegeven die de status van het back-upresultaat verschaft. Selecteer **OK**

6. Sla het Archief voor gebruikers configuratie (UCS) lokaal op door de koppeling van de back-up te kiezen en **down load** te selecteren.

Als optionele stap kunt u ook een back-up van de volledige systeem schijf maken met behulp van [Azure snap shots](../../virtual-machines/windows/snapshot-copy-managed-disk.md), wat in tegens telling tot de back-up van de Webconfiguratie kan enige nood geval zijn voor het testen tussen TMOS-versies of het terugdraaien van een nieuw systeem.

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

Het herstellen van een BIG-IP volgt een vergelijk bare procedure met de back-up en kan ook worden gebruikt voor het migreren van configuraties tussen grote IP-Vm's. Details over ondersteunde upgrade paden moeten worden waargenomen voordat u een back-up importeert.

1. Naar **systeem**  >  **archieven**

2. Kies de koppeling van een back-up die u wilt herstellen of selecteer de knop **uploaden** om naar een eerder opgeslagen ucs-archief te bladeren dat zich niet in de lijst bevindt

3. Geef de wachtwoordzin voor de back-up op en selecteer **herstellen**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>Op het moment van schrijven is de cmdlet AzVmSnapshot beperkt tot het herstellen van de meest recente moment opname, op basis van datum. Moment opnamen worden opgeslagen in de hoofdmap van de resource groep van de virtuele machine. Houd er rekening mee dat bij het herstellen van moment opnamen een Azure VM opnieuw wordt gestart. Dit is dus een goed moment.

## <a name="additional-resources"></a>Aanvullende bronnen

-   [BIG-IP VE-wacht woord opnieuw instellen in azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Het wacht woord opnieuw instellen zonder de portal te gebruiken](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [De NIC wijzigen die wordt gebruikt voor het beheer van BIG-IP](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Routes in een configuratie met één NIC](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Volgende stappen

Selecteer een [implementatie scenario](f5-aad-integration.md) en start uw implementatie.