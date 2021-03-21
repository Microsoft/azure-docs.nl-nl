---
title: Configuratieproblemen met Private Links diagnosticeren in Azure Key Vault
description: Veelvoorkomende problemen met persoonlijke koppelingen oplossen met Key Vault en dieper in de configuratie
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 03abe4e4e098d46060e33ba114872905e54a443f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96317058"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Configuratieproblemen met Private Links diagnosticeren in Azure Key Vault

## <a name="introduction"></a>Inleiding

Dit artikel helpt gebruikers bij het vaststellen en oplossen van problemen met betrekking tot Key Vault en de functie persoonlijke koppelingen. Deze hand leiding helpt u bij het configureren van configuratie aspecten, zoals het ophalen van persoonlijke koppelingen die voor de eerste keer werken, of voor het oplossen van een situatie waarin privé koppelingen zijn gestopt vanwege een wijziging.

Als u geen ervaring hebt met deze functie, raadpleegt u [Key Vault integreren met Azure private link](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>Problemen die in dit artikel worden behandeld

- Uw DNS-query's retour neren nog steeds een openbaar IP-adres voor de sleutel kluis in plaats van een privé-IP-adres dat u zou verwachten van het gebruik van de functie persoonlijke koppelingen.
- Alle aanvragen van een bepaalde client die een privé koppeling gebruiken, mislukken met time-outs of netwerk fouten en het probleem treedt niet op.
- De sleutel kluis heeft een privé-IP-adres, maar aanvragen ontvangen nog steeds `403` antwoord met de `ForbiddenByFirewall` interne fout code.
- U gebruikt persoonlijke koppelingen, maar uw sleutel kluis accepteert nog steeds aanvragen van het open bare Internet.
- Uw sleutel kluis heeft twee privé-eind punten. Aanvragen met één werken prima, maar aanvragen die gebruikmaken van de andere, mislukken.
- U hebt een ander abonnement, een sleutel kluis of een virtueel netwerk dat persoonlijke koppelingen gebruikt. U wilt een nieuwe, vergelijk bare implementatie maken, maar u kunt geen persoonlijke koppelingen verkrijgen om daar te werken.

### <a name="problems-not-covered-by-this-article"></a>Problemen die niet onder dit artikel vallen

- Er is een probleem met de verbinding. In een bepaalde client ziet u dat sommige aanvragen werken en sommige niet werken. *Tijdelijke problemen worden meestal niet veroorzaakt door een probleem in de configuratie van particuliere koppelingen; ze zijn een teken van netwerk-of client overbelasting.*
- U gebruikt een Azure-product dat ondersteuning biedt voor BYOK (Bring Your Own Key), CMK (door de klant beheerde sleutels) of toegang tot geheimen die zijn opgeslagen in de sleutel kluis. Wanneer u de firewall in de sleutel kluis instellingen inschakelt, heeft dat product geen toegang tot uw sleutel kluis. *Bekijk productspecifieke documentatie. Zorg ervoor dat het expliciet ondersteuning biedt voor sleutel kluizen waarvoor de firewall is ingeschakeld. Neem zo nodig contact op met de ondersteuning voor dat specifieke product.*

### <a name="how-to-read-this-article"></a>Lees dit artikel

Als u geen ervaring hebt met persoonlijke koppelingen of als u een complexe implementatie evalueert, is het raadzaam het hele artikel te lezen. Als dat niet het geval is, kunt u de sectie kiezen die voor het probleem duidelijker is.

Laten we beginnen.

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. Controleer of u eigenaar bent van de client verbinding

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Controleren of de client wordt uitgevoerd in het virtuele netwerk

Deze hand leiding is bedoeld om u te helpen bij het oplossen van verbindingen met de sleutel kluis die afkomstig zijn uit toepassings code. Voor beelden zijn toepassingen en scripts die worden uitgevoerd in azure Virtual Machines, Azure Service Fabric-clusters, Azure App Service, Azure Kubernetes service (AKS) en vergelijk bare andere. Deze hand leiding is ook van toepassing op toegang die wordt uitgevoerd in de Azure Portal web-base-gebruikers interface, waarbij de browser rechtstreeks toegang heeft tot uw sleutel kluis.

Op basis van de definitie van persoonlijke koppelingen moet de toepassing, het script of de portal worden uitgevoerd op de computer, het cluster of de omgeving die is verbonden met de Virtual Network waar de [privé-eindpunt resource](../../private-link/private-endpoint-overview.md) is geïmplementeerd.

Als de toepassing, het script of de portal wordt uitgevoerd op een wille keurig netwerk met Internet verbinding, is deze hand leiding niet van toepassing en kunnen er mogelijk privé koppelingen niet worden gebruikt. Deze beperking is ook van toepassing op opdrachten die worden uitgevoerd in de Azure Cloud Shell, omdat deze worden uitgevoerd op een externe Azure-machine, in plaats van via de browser van de gebruiker.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Raadpleeg specifieke documentatie voor meer informatie over het gebruik van een beheerde oplossing.

Deze hand leiding is niet van toepassing op oplossingen die door micro soft worden beheerd, waarbij de sleutel kluis wordt gebruikt door een Azure-product dat onafhankelijk van de klant Virtual Network bestaat. Voor beelden van dergelijke scenario's zijn Azure Storage of Azure SQL geconfigureerd voor versleuteling in rust, Azure Event hub versleutelt gegevens met door de klant opgegeven sleutels, Azure Data Factory toegang tot service referenties die zijn opgeslagen in sleutel kluis, Azure-pijp lijnen die geheimen ophalen uit de sleutel kluis en andere vergelijk bare scenario's. In deze gevallen *moet u controleren of het product sleutel kluizen ondersteunt waarvoor de firewall is ingeschakeld*. Deze ondersteuning wordt doorgaans uitgevoerd met de functie voor [vertrouwde services](overview-vnet-service-endpoints.md#trusted-services) van Key Vault firewall. Veel producten zijn echter om verschillende redenen niet opgenomen in de lijst met vertrouwde services. In dat geval bereikt u de productspecifieke ondersteuning.

Een klein aantal Azure-producten ondersteunt het concept van *vnet-injectie*. In eenvoudige termen voegt het product een netwerk apparaat toe aan de klant Virtual Network, zodat het aanvragen kan verzenden alsof het is geïmplementeerd voor de Virtual Network. Een voor beeld hiervan is [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Met producten zoals dit kunt u aanvragen indienen bij de sleutel kluis met behulp van de persoonlijke koppelingen. deze hand leiding voor probleem oplossing kan helpen.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Controleer of de verbinding is goedgekeurd en is voltooid

In de volgende stappen wordt gecontroleerd of de verbinding van het particuliere eind punt is goedgekeurd en is geslaagd:

1. Open de Azure Portal en open uw sleutel kluis resource.
2. Selecteer in het menu links de optie **netwerken**.
3. Klik op het tabblad **verbindingen met privé-eind punten** . Hiermee worden alle privé-eindpunt verbindingen en hun respectieve statussen weer gegeven. Als er geen verbindingen zijn of als de verbinding voor uw Virtual Network ontbreekt, moet u een nieuw persoonlijk eind punt maken. Dit wordt later besproken.
4. Ga nog steeds in verbindingen met een **privé-eind punt** naar het account dat u wilt onderzoeken en controleer of ' verbindings status ' is **goedgekeurd** en de inrichtings status is **geslaagd**.
    - Als de verbinding de status in behandeling heeft, kunt u deze mogelijk gewoon goed keuren.
    - Als de verbinding ' geweigerd ', ' failed ', ' error ', ' disconnected ' of een andere status is, is het helemaal niet effectief. u moet een nieuwe persoonlijke eindpunt resource maken.

Het is een goed idee om inwerkende verbindingen te verwijderen, zodat u alles schoon kunt maken.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Controleer of de sleutel kluis firewall juist is geconfigureerd

>[!IMPORTANT]
> Als u de firewall instellingen wijzigt, kan de toegang tot legitieme clients die nog steeds geen persoonlijke koppelingen gebruiken, worden verwijderd. Zorg ervoor dat u op de hoogte bent van de implicaties van elke wijziging in de firewall configuratie.

Een belang rijk voor stel is dat de functie persoonlijke koppelingen alleen toegang *geeft* tot uw sleutel kluis in een Virtual Network dat gesloten is om gegevens exfiltration te voor komen. Er wordt geen bestaande toegang *verwijderd* . Als u de toegang tot het open bare Internet effectief wilt blok keren, moet u de firewall voor de sleutel kluis expliciet inschakelen:

1. Open de Azure Portal en open uw sleutel kluis resource.
2. Selecteer in het menu links de optie **netwerken**.
3. Zorg ervoor dat het tabblad **firewalls en virtuele netwerken** op de voor grond wordt geselecteerd.
4. Zorg ervoor dat de optie **persoonlijk eind punt en geselecteerde netwerken** is geselecteerd. Als u **alle netwerken** selecteert, wordt uitgelegd waarom externe clients nog steeds toegang hebben tot de sleutel kluis.

De volgende instructies zijn ook van toepassing op Firewall instellingen:

- De functie voor persoonlijke koppelingen vereist geen ' virtueel netwerk ' dat moet worden opgegeven in de firewall instellingen van de sleutel kluis. Alle aanvragen die gebruikmaken van het privé-IP-adres van de sleutel kluis (zie volgende sectie) moeten werken, zelfs als er geen virtueel netwerk is opgegeven in de firewall instellingen van de sleutel kluis.
- Voor de functie persoonlijke koppelingen is geen IP-adres opgegeven in de firewall instellingen van de sleutel kluis. Daarnaast moeten alle aanvragen die gebruikmaken van het privé-IP-adres van de sleutel kluis werken, zelfs als er geen IP-adres is opgegeven in de firewall instellingen.

Als u persoonlijke koppelingen gebruikt, zijn de enige motivaties voor het opgeven van een virtueel netwerk of IP-adres in een sleutel kluis firewall:

- U hebt een hybride systeem waarbij sommige clients persoonlijke koppelingen gebruiken, sommige service-eind punten gebruiken, een openbaar IP-adres gebruiken.
- U bent overstappen op persoonlijke koppelingen. Als u in dit geval hebt gecontroleerd of alle clients persoonlijke koppelingen gebruiken, moet u virtuele netwerken en IP-adressen verwijderen uit de firewall instellingen van de sleutel kluis.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Controleer of de sleutel kluis een privé-IP-adres heeft

### <a name="difference-between-private-and-public-ip-addresses"></a>Verschil tussen persoonlijke en open bare IP-adressen

Een privé-IP-adres heeft altijd een van de volgende indelingen:

- 10. x. x. x: voor beelden: `10.1.2.3` , `10.56.34.12` .
- 172.16. x. x naar 172.32. x. x: voor beelden: `172.20.1.1` , `172.31.67.89` .
- 192.168. x. x: voor beelden: `192.168.0.1` , `192.168.100.7`

Sommige IP-adressen en-bereiken zijn gereserveerd:

- 224. x. x. x: multi cast
- 255.255.255.255: Broadcast
- 127. x. x. x: loop back
- 169.254. x. x: link-local
- 168.63.129.16: interne DNS

Alle andere IP-adressen zijn openbaar.

Wanneer u door de portal bladert of een opdracht uitvoert waarin het IP-adres wordt weer gegeven, moet u controleren of dat IP-adres privé, openbaar of gereserveerd is. Voor een goede werking van persoonlijke koppelingen moet de hostnaam van de sleutel kluis worden omgezet in een privé-IP-adres dat deel uitmaakt van de Virtual Network-adres ruimte.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Zoek het privé-IP-adres van de sleutel kluis in het virtuele netwerk

U moet de omzetting van hostnamen diagnosticeren en voor dat u het precieze persoonlijke IP-adres van uw sleutel kluis moet kennen met persoonlijke koppelingen ingeschakeld. Voer de volgende procedure uit om het adres te vinden:

1. Open de Azure Portal en open uw sleutel kluis resource.
2. Selecteer in het menu links de optie **netwerken**.
3. Klik op het tabblad **verbindingen met privé-eind punten** . Hiermee worden alle privé-eindpunt verbindingen en hun respectieve statussen weer gegeven.
4. Zoek het account dat u wilt onderzoeken en controleer of ' verbindings status ' is **goedgekeurd** en de inrichtings status is **geslaagd**. Als u dit niet ziet, gaat u terug naar de vorige secties van dit document.
5. Wanneer u het juiste item vindt, klikt u op de koppeling in de kolom **persoonlijk eind punt** . Hiermee wordt de persoonlijke eindpunt resource geopend.
6. Op de pagina overzicht wordt een sectie met de naam **aangepaste DNS-instellingen** weer gegeven. Controleer of er slechts één vermelding is die overeenkomt met de sleutel kluis hostnaam. In deze vermelding wordt het privé IP-adres van de sleutel kluis weer gegeven.
7. U kunt ook op de koppeling op de **netwerk interface** klikken en controleren of het privé-IP-adres hetzelfde is als in de vorige stap. De netwerk interface is een virtueel apparaat dat sleutel kluis vertegenwoordigt.

Het IP-adres is de naam die door Vm's en andere apparaten die worden *uitgevoerd in dezelfde Virtual Network* wordt gebruikt om verbinding te maken met de sleutel kluis. Noteer het IP-adres of blijf het browser tabblad geopend en blijf het niet aanraken terwijl u verder onderzoek doet.

>[!NOTE]
> Als uw sleutel kluis meerdere persoonlijke eind punten heeft, heeft deze meerdere privé-IP-adressen. Dit is alleen nuttig als u meerdere virtuele netwerken hebt die dezelfde sleutel kluis gebruiken, elk via een eigen privé-eind punt (het persoonlijke eind punt behoort tot één Virtual Network). Zorg ervoor dat u het probleem voor de juiste Virtual Network diagnosticeert en selecteer de juiste verbinding voor een persoonlijk eind punt in de bovenstaande procedure. Maak bovendien **geen** meerdere persoonlijke eind punten voor hetzelfde Key Vault in dezelfde Virtual Network. Dit is niet nodig en is een bron van Verwar ring.

## <a name="5-validate-the-dns-resolution"></a>5. de DNS-omzetting valideren

DNS-omzetting is het proces van het vertalen van de sleutel kluis hostnaam (voor beeld: `fabrikam.vault.azure.net` ) naar een IP-adres (bijvoorbeeld: `10.1.2.3` ). In de volgende subsecties worden de verwachte resultaten van de DNS-omzetting in elk scenario weer gegeven.

### <a name="key-vaults-without-private-link"></a>Sleutel kluizen zonder persoonlijke koppeling

Deze sectie is bedoeld voor Learning doeleinden. Wanneer de sleutel kluis geen privé-eindpunt verbinding heeft met de status goedgekeurd, resulteert het omzetten van de hostnaam in het volgende resultaat:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

U kunt zien dat de naam wordt omgezet in een openbaar IP-adres en dat er geen `privatelink` alias is. De alias wordt later uitgelegd. u hoeft dit nu niet te doen.

Het bovenstaande resultaat wordt verwacht, ongeacht of de computer met de Virtual Network is verbonden of een wille keurige computer met een Internet verbinding is. Dit gebeurt omdat de sleutel kluis geen privé-eindpunt verbinding heeft met de status goedgekeurd en daarom niet nodig is voor de sleutel kluis om persoonlijke koppelingen te ondersteunen.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Sleutel kluis met het oplossen van een particuliere koppeling van een wille keurige internet computer

Wanneer de sleutel kluis een of meer verbindingen met een privé-eind punt in de goedgekeurde status heeft en u de hostnaam oplost van een wille keurige computer die is verbonden met internet (een computer die *niet* is verbonden met de Virtual Network waar het persoonlijke eind punt zich bevindt), vindt u dit:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

Het belangrijkste verschil van het vorige scenario is dat er een nieuwe alias met de waarde is `{vaultname}.privatelink.vaultcore.azure.net` . Dit betekent dat het sleutel kluis-gegevens vlak gereed is om aanvragen van persoonlijke koppelingen te accepteren.

Dit betekent niet dat aanvragen die worden uitgevoerd vanaf computers *buiten* de Virtual Network (zoals de versie die u zojuist hebt gebruikt) persoonlijke koppelingen gebruiken. U kunt zien dat de hostnaam nog steeds wordt omgezet in een openbaar IP-adres. Alleen computers *die zijn verbonden met de Virtual Network* kunnen persoonlijke koppelingen gebruiken. Meer informatie vindt u hier.

Als u de alias niet ziet `privatelink` , betekent dit dat de sleutel kluis nul verbindingen met een privé-eind punt heeft `Approved` . Ga terug naar [deze sectie](#2-confirm-that-the-connection-is-approved-and-succeeded) voordat u het opnieuw probeert.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Sleutel kluis met het oplossen van een persoonlijke koppeling van Virtual Network

Wanneer de sleutel kluis een of meer verbindingen met een privé-eind punt in de goedgekeurde status heeft en u de hostnaam oplost van een computer die is verbonden met de Virtual Network waar het persoonlijke eind punt is gemaakt, is dit de verwachte reactie:

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

Er zijn twee belang rijke verschillen. Eerst wordt de naam omgezet in een privé-IP-adres. Dit moet het IP-adres zijn dat we hebben gevonden in de [bijbehorende sectie](#find-the-key-vault-private-ip-address-in-the-virtual-network) van dit artikel. Ten tweede zijn er geen andere aliassen aanwezig `privatelink` . Dit gebeurt omdat de DNS-servers van Virtual Network de keten van aliassen *onderschept* en het privé-IP-adres rechtstreeks vanuit de naam retour neren `fabrikam.privatelink.vaultcore.azure.net` . Deze vermelding is eigenlijk een `A` record in een privé-DNS zone. Meer informatie vindt u hier.

>[!NOTE]
> Het bovenstaande resultaat wordt alleen uitgevoerd op een virtuele machine die is verbonden met de Virtual Network waar het persoonlijke eind punt is gemaakt. Als u geen VM hebt geïmplementeerd in de Virtual Network die het persoonlijke eind punt bevat, implementeert u er een en maakt u er extern verbinding mee en voert u de `nslookup` opdracht (Windows) of de `host` opdracht (Linux) uit.

Als u deze opdrachten uitvoert op een virtuele machine die is verbonden met de Virtual Network waarop het persoonlijke eind punt is gemaakt, en de sleutel kluis privé IP-adres **niet** wordt weer gegeven, kan de volgende sectie het probleem mogelijk oplossen.

## <a name="6-validate-the-private-dns-zone"></a>6. de Privé-DNS zone valideren

Als de DNS-omzetting niet werkt zoals beschreven in de vorige sectie, is er mogelijk een probleem met uw Privé-DNS zone en kan deze sectie helpen. Als de DNS-omzetting het juiste privé-IP-adres van de sleutel kluis weergeeft, is uw Privé-DNS-zone waarschijnlijk juist. U kunt deze volledige sectie overs Laan.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Controleer of de vereiste Privé-DNS-zone resource bestaat

Uw Azure-abonnement moet een [privé-DNS zone](../../dns/private-dns-privatednszone.md) resource met deze exacte naam hebben:

`privatelink.vaultcore.azure.net`

U kunt controleren of deze resource aanwezig is door naar de pagina abonnement in de portal te gaan en ' resources ' te selecteren in het menu aan de linkerkant. De resource naam moet zijn `privatelink.vaultcore.azure.net` en het resource type moet **privé-DNS zone** zijn.

Normaal gesp roken wordt deze resource automatisch gemaakt wanneer u een persoonlijk eind punt maakt met behulp van een gemeen schappelijke procedure. Er zijn echter gevallen waarin deze resource niet automatisch wordt gemaakt en u deze hand matig moet doen. Mogelijk is deze resource ook per ongeluk verwijderd.

Als u deze resource niet hebt, maakt u een nieuwe Privé-DNS zone resource in uw abonnement. Houd er rekening mee dat de naam exact moet zijn `privatelink.vaultcore.azure.net` , zonder spaties of extra punten. Als u de verkeerde naam opgeeft, werkt de naam omzetting die in dit artikel wordt uitgelegd, niet. Zie [een Azure private DNS-zone maken met behulp van de Azure Portal](../../dns/private-dns-getstarted-portal.md)voor meer informatie over het maken van deze bron. Als u deze pagina volgt, kunt u het maken van Virtual Network overs Laan omdat u op dit moment al een hebt. U kunt ook de validatie procedures over Virtual Machines overs Laan.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Controleer of de Privé-DNS zone is gekoppeld aan de Virtual Network

Het is niet voldoende om een Privé-DNS zone te hebben. Het moet ook worden gekoppeld aan de Virtual Network die het persoonlijke eind punt bevat. Als de Privé-DNS zone niet is gekoppeld aan de juiste Virtual Network, wordt de zone Privé-DNS door de DNS-omzetting van die Virtual Network genegeerd.

Open de resource Privé-DNS zone en klik op de optie Koppelingen naar het **virtuele netwerk** in het menu links. Hiermee wordt een lijst met koppelingen weer gegeven, elk met de naam van een Virtual Network in uw abonnement. De Virtual Network die de persoonlijke eindpunt resource bevat, moet hier worden vermeld. Als dat niet het geval is, voegt u het toe. Zie voor gedetailleerde stappen [koppelen van het virtuele netwerk](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). U kunt ' automatische registratie inschakelen ' verlaten. deze functie heeft geen betrekking op persoonlijke koppelingen.

Zodra de Privé-DNS zone is gekoppeld aan de Virtual Network, zoeken DNS-aanvragen die afkomstig zijn van de Virtual Network, naar namen in de zone Privé-DNS. Dit is vereist voor een juiste adres omzetting die is uitgevoerd op Virtual Machines verbonden met de Virtual Network waar het persoonlijke eind punt is gemaakt.

>[!NOTE]
> Als u de koppeling zojuist hebt opgeslagen, kan het enige tijd duren voordat deze wijziging doorloopt, zelfs nadat de portal heeft aangegeven dat de bewerking is voltooid. Mogelijk moet u ook de virtuele machine die u gebruikt om de DNS-omzetting te testen opnieuw opstarten.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Controleer of de Privé-DNS zone het recht een record bevat

Open met behulp van de Portal de Privé-DNS zone met de naam `privatelink.vaultcore.azure.net` . Op de overzichts pagina worden alle records weer gegeven. Standaard is er één record met de naam en het `@` type `SOA` , wat begin van de instantie is. Raak dat niet.

De naam omzetting van de sleutel kluis werkt alleen als er een `A` record is met de naam van de eenvoudige kluis zonder achtervoegsel of punten. Als de hostnaam bijvoorbeeld is `fabrikam.vault.azure.net` , moet er een `A` record zijn met de naam `fabrikam` , zonder achtervoegsel of punten.

De waarde van de `A` record (het IP-adres) moet ook [het privé-IP-adres van de sleutel kluis](#find-the-key-vault-private-ip-address-in-the-virtual-network)zijn. Als u de `A` record vindt, maar deze het verkeerde IP-adres bevat, moet u het verkeerde IP-adres verwijderen en een nieuwe toevoegen. Het is raadzaam om de gehele `A` record te verwijderen en een nieuwe te voegen.

>[!NOTE]
> Wanneer u een record verwijdert of wijzigt `A` , kan de computer nog steeds worden omgezet naar het oude IP-adres, omdat de TTL-waarde (time to Live) mogelijk nog niet is verlopen. Het is raadzaam om altijd een TTL-waarde op te geven die niet kleiner is dan 60 seconden (één minuut) en niet groter is dan 600 seconden (10 minuten). Als u een waarde opgeeft die te groot is, kunnen uw clients te lang duren om te herstellen na storingen.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>DNS-omzetting voor meer dan één Virtual Network

Als er meerdere virtuele netwerken zijn en elk een eigen privé-eindpunt resource heeft die verwijst naar dezelfde sleutel kluis, moet de sleutel kluis hostnaam worden omgezet in een ander privé-IP-adres, afhankelijk van het netwerk. Dit betekent dat er meerdere Privé-DNS zones nodig zijn, elk gekoppeld aan een andere Virtual Network en een ander IP-adres in de `A` record gebruiken.

In meer geavanceerde scenario's is het mogelijk dat peering is ingeschakeld voor de virtuele netwerken. In dit geval heeft slechts één Virtual Network de persoonlijke eindpunt resource nodig, hoewel beide mogelijk moeten worden gekoppeld aan de Privé-DNS zone resource. Dit is een scenario dat niet direct onder dit document valt.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>Meer informatie over de controle over de DNS-omzetting

Zoals beschreven in de [vorige sectie](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine), heeft een sleutel kluis met persoonlijke koppelingen de alias `{vaultname}.privatelink.vaultcore.azure.net` in de *open bare* registratie. Op de DNS-server die door de Virtual Network wordt gebruikt, wordt de open bare registratie gebruikt, maar wordt elke alias voor een *privé* registratie gecontroleerd en als er een wordt gevonden, worden de volgende aliassen voor de registratie gestopt.

Deze logica betekent dat als de Virtual Network is gekoppeld aan een Privé-DNS zone met de naam `privatelink.vaultcore.azure.net` , en de open bare DNS-registratie voor de sleutel kluis de alias heeft `fabrikam.privatelink.vaultcore.azure.net` (Houd er rekening mee dat het achtervoegsel van de sleutel kluis hostnaam precies overeenkomt met de naam van de privé-DNS zone). vervolgens zoekt de DNS-query naar een `A` record met de naam `fabrikam` *in de zone privé-DNS*. Als de `A` record wordt gevonden, wordt het bijbehorende IP-adres geretourneerd in de DNS-query en wordt er geen verdere zoek opdracht uitgevoerd bij de open bare DNS-registratie.

Zoals u kunt zien, is de naam omzetting onder uw beheer. De motivering van dit ontwerp zijn:

- Mogelijk hebt u een complex scenario waarbij aangepaste DNS-servers en integratie met on-premises netwerken betrokken zijn. In dat geval moet u bepalen hoe namen worden omgezet naar IP-adressen.
- Mogelijk moet u toegang hebben tot een sleutel kluis zonder persoonlijke koppelingen. In dat geval moet het omzetten van de hostnaam uit het Virtual Network het open bare IP-adres retour neren. dit gebeurt omdat sleutel kluizen zonder persoonlijke koppelingen geen `privatelink` alias hebben in de naam registratie.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. controleren of aanvragen voor de sleutel kluis een persoonlijke koppeling gebruiken

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Een query uitvoeren `/healthstatus` op het eind punt van de sleutel kluis

Uw sleutel kluis biedt het `/healthstatus` eind punt dat kan worden gebruikt voor diagnostische gegevens. De antwoord headers bevatten het IP-adres van de oorsprong, zoals wordt gezien door de sleutel kluis service. U kunt dat eind punt aanroepen met de volgende opdracht (**Vergeet niet uw sleutel kluis hostnaam te gebruiken**):

Windows (Power shell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux of een recente versie van Windows 10, waaronder `curl` :

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

Als u geen uitvoer ziet die vergelijkbaar is met dat, of als er een netwerk fout optreedt, betekent dit dat uw sleutel kluis niet toegankelijk is via de hostnaam die u hebt opgegeven ( `fabrikam.vault.azure.net` in het voor beeld). De hostnaam wordt niet omgezet naar het juiste IP-adres of u hebt een connectiviteits probleem op de transportlaag. Dit kan worden veroorzaakt door routerings problemen, pakket drup pels en andere redenen. U moet verder onderzoeken.

Het antwoord moet header bevatten `x-ms-keyvault-network-info` :

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

`addr`In het veld in de `x-ms-keyvault-network-info` koptekst wordt het IP-adres van de oorsprong van de aanvraag weer gegeven. Dit IP-adres kan een van de volgende zijn:

- Het privé-IP-adres van de computer die de aanvraag uitvoert. Dit geeft aan dat de aanvraag gebruikmaakt van persoonlijke koppelingen of service-eind punten. Dit is het verwachte resultaat voor persoonlijke koppelingen.
- Een ander privé-IP-adres, *niet* van de computer die de aanvraag uitvoert. Dit geeft aan dat een bepaalde aangepaste route ring effectief is. Er wordt nog steeds aangegeven dat de aanvraag persoonlijke koppelingen of service-eind punten gebruikt.
- Een openbaar IP-adres. Dit geeft aan dat de aanvraag wordt doorgestuurd naar Internet via een gateway-apparaat (NAT). Dit geeft aan dat de aanvraag geen persoonlijke koppelingen gebruikt en dat er een probleem moet worden opgelost. De meest voorkomende oorzaken hiervoor zijn 1) het persoonlijke eind punt is niet goedgekeurd en geslaagd. en 2) de hostnaam wordt niet omgezet naar het privé-IP-adres van de sleutel kluis. Dit artikel bevat probleemoplossings acties voor beide gevallen.

>[!NOTE]
> Als de aanvraag `/healthstatus` werkt, maar de `x-ms-keyvault-network-info` header ontbreekt, wordt het eind punt waarschijnlijk niet geleverd door de sleutel kluis. Aangezien de bovenstaande opdrachten ook HTTPS-certificaat valideren, kan de ontbrekende header een aantekening van knoeien zijn.

### <a name="query-the-key-vault-ip-address-directly"></a>Het IP-adres van de sleutel kluis rechtstreeks opvragen

>[!IMPORTANT]
> Toegang tot de sleutel kluis zonder HTTPS-certificaat validatie is gevaarlijk en kan alleen worden gebruikt voor leer doeleinden. Productie code mag nooit toegang hebben tot de sleutel kluis zonder deze validatie aan de client zijde. Zelfs als u gewoon problemen wilt vaststellen, is het mogelijk dat u pogingen ondervindt die niet worden onthuld als u regel matig HTTPS-certificaat validatie in uw aanvragen voor sleutel kluis uitschakelt.

Als u een recente versie van Power shell hebt geïnstalleerd, kunt u gebruiken `-SkipCertificateCheck` om HTTPS-certificaat controles over te slaan. vervolgens kunt u het [IP-adres van de sleutel kluis](#find-the-key-vault-private-ip-address-in-the-virtual-network) rechtstreeks richten:

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

Als u gebruikt `curl` , kunt u hetzelfde doen met het `-k` argument:

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

De antwoorden moeten hetzelfde zijn als de vorige sectie, wat betekent dat deze de `x-ms-keyvault-network-info` koptekst met dezelfde waarde moet bevatten. Het `/healthstatus` eind punt is niet van belang als u de sleutel kluis hostnaam of het IP-adres gebruikt.

Als u `x-ms-keyvault-network-info` een waarde voor de aanvraag retourneert met behulp van de sleutel kluis hostnaam en een andere waarde voor de aanvraag met behulp van het IP-adres, is elke aanvraag gericht op een ander eind punt. Raadpleeg de uitleg van het `addr` veld `x-ms-keyvault-network-info` in het vorige gedeelte om te bepalen welk geval onjuist is en moet worden opgelost.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. andere wijzigingen en aanpassingen die invloed hebben

De volgende items zijn niet-limitatieve onderzoek acties. U krijgt informatie over de locatie waar u naar aanvullende problemen kunt zoeken, maar u moet geavanceerde netwerk kennis hebben om problemen in deze scenario's op te lossen.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Aangepaste DNS-servers diagnosticeren op Virtual Network

Open in de Portal de Virtual Network resource. Open in het menu links de optie **DNS-servers**. Als u ' aangepast ' gebruikt, wordt de DNS-omzetting mogelijk niet zoals beschreven in dit document. U moet vaststellen hoe uw DNS-servers de hostnaam van de sleutel kluis omzetten.

Als u gebruikmaakt van de standaard-DNS-servers die door Azure worden gebruikt, is dit hele document van toepassing.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Detecteren van hosts die worden overschreven of aangepaste DNS-servers op de virtuele machine

Veel besturings systemen maken het instellen van een expliciet vast IP-adres per hostnaam mogelijk, meestal door het bestand te wijzigen `hosts` . Ze kunnen ook toestaan dat de DNS-servers worden overschreven. Als u een van deze scenario's gebruikt, gaat u door met systeemspecifieke diagnostische opties.

### <a name="promiscuous-proxies-fiddler-etc"></a>Ongeordende proxy's (Fiddler, enzovoort)

Behalve als dit expliciet wordt vermeld, werken de diagnostische opties in dit artikel alleen als er geen ongeordende proxy in de omgeving aanwezig is. Hoewel deze proxy's vaak alleen worden geïnstalleerd op de computer die wordt gecontroleerd (Fiddler is het meest voorkomende voor beeld), kunnen ervaren beheerders basis certificerings instanties (Ca's) overschrijven en een ongeordende proxy installeren in gateway apparaten die van meerdere computers in het netwerk worden gebruikt. Deze proxy's kunnen de beveiliging en betrouw baarheid aanzienlijk beïnvloeden. Micro soft biedt geen ondersteuning voor configuraties die gebruikmaken van dergelijke producten.

### <a name="other-things-that-may-affect-connectivity"></a>Andere zaken die van invloed kunnen zijn op de connectiviteit

Dit is een niet-limitatieve lijst met items die u kunt vinden in geavanceerde of complexe scenario's:

- Firewall instellingen, de Azure Firewall verbonden met de Virtual Network of een aangepaste firewall oplossing die wordt geïmplementeerd in de Virtual Network of op de machine.
- Peering op het netwerk, die kan beïnvloeden welke DNS-servers worden gebruikt en hoe verkeer wordt gerouteerd.
- Aangepaste gateway-oplossingen, die van invloed kunnen zijn op het routeren van verkeer, inclusief verkeer van DNS-query's.