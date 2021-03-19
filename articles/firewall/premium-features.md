---
title: Preview-functies Azure Firewall Premium
description: Azure Firewall Premium is een beheerde, Cloud service voor netwerk beveiliging die uw Azure Virtual Network-Resources beveiligt.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 4a8efff7ef53753e15a47e87a2bb82d0124ae997
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590446"
---
# <a name="azure-firewall-premium-preview-features"></a>Preview-functies Azure Firewall Premium

Logo voor :::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="ICSA-certificering logo" border="false":::voor:::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI" border="false":::


> [!IMPORTANT]
> Azure Firewall Premium is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

 Azure Firewall Premium preview is een firewall van de volgende generatie met mogelijkheden die vereist zijn voor zeer gevoelige en gereglementeerde omgevingen.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Overzichts diagram van Azure Firewall Premium":::

Azure Firewall Premium preview maakt gebruik van firewall beleid, een wereld wijde resource die kan worden gebruikt om uw firewalls centraal te beheren met behulp van Azure Firewall Manager. Als u deze release start, kunnen alle nieuwe functies alleen worden geconfigureerd via firewall-beleid. Firewall regels (klassiek) blijven worden ondersteund en kunnen worden gebruikt voor het configureren van bestaande standaard Firewall functies.  Het firewall beleid kan onafhankelijk worden beheerd of met Azure Firewall Manager. Een firewall beleid dat is gekoppeld aan één firewall, wordt niet in rekening gebracht.

> [!IMPORTANT]
> De Premium-SKU van firewalls wordt momenteel niet ondersteund in Secure hub-implementaties en geforceerde tunnel configuraties. 

Azure Firewall Premium Preview bevat de volgende functies:

- **TLS-inspectie** -Hiermee wordt het uitgaande verkeer gedecodeerd, worden de gegevens verwerkt, worden de gegevens versleuteld en verzonden naar de bestemming.
- **Id** : een netwerk aanval en preventie systeem (id) biedt u de mogelijkheid om netwerk activiteiten te bewaken voor schadelijke activiteiten, informatie over deze activiteit te registreren, deze te rapporteren en optioneel te proberen om deze te blok keren.
- **URL-filtering** : Hiermee wordt de FQDN-filter functie van Azure firewall uitgebreid om een volledige URL te bekijken. Bijvoorbeeld `www.contoso.com/a/c` in plaats van `www.contoso.com` .
- **Webcategorieën** : beheerders kunnen gebruikers toegang tot website categorieën toestaan of weigeren, zoals gokken websites, websites voor sociale media en anderen.


## <a name="tls-inspection"></a>TLS-inspectie

Azure Firewall Premium beëindigt uitgaande en Oost-West TLS-verbindingen. Binnenkomende TLS-inspectie wordt ondersteund met [Azure-toepassing gateway](../web-application-firewall/ag/ag-overview.md) waarbij End-to-end-versleuteling is toegestaan. Azure Firewall voldoet aan de vereiste waarde toegevoegde beveiligings functies en versleutelt het verkeer dat wordt verzonden naar de oorspronkelijke bestemming opnieuw.

> [!TIP]
> TLS 1,0 en 1,1 worden afgeschaft en worden niet ondersteund. TLS 1,0 en 1,1 versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar voor aanvallen en terwijl ze momenteel nog steeds werken om achterwaartse compatibiliteit mogelijk te maken, worden ze niet aanbevolen. Migreer zo snel mogelijk naar TLS 1,2.

Zie [Azure Firewall Premium preview-certificaten](premium-certificates.md)voor meer informatie over de vereisten voor een voor beeld van een tussenliggend CA-certificaat voor Azure Firewall Premium.

## <a name="idps"></a>ID

Met een netwerk aanval en preventie systeem (id) kunt u uw netwerk controleren op schadelijke activiteiten, informatie over deze activiteit vastleggen, het rapport rapporteren en eventueel proberen om het te blok keren. 

Azure Firewall Premium preview biedt op hand tekeningen gebaseerde id om snelle detectie van aanvallen mogelijk te maken door te zoeken naar specifieke patronen, zoals byte reeksen in netwerk verkeer, of bekende schadelijke instructie reeksen die worden gebruikt door malware. De id-hand tekeningen worden volledig beheerd en doorlopend bijgewerkt.

De Azure Firewall hand tekeningen/rules zijn:
- Een nadruk op het nagaan van de daad werkelijke malware, opdracht en controle, cracks en in de wilde schadelijke activiteit gemist door traditionele preventie methoden.
- Meer dan 35.000 regels in meer dan 50 categorieën.
    - De categorieën bevatten malware-opdracht en-beheer, DoS-aanvallen, botnets, informatieve gebeurtenissen, misbruik, beveiligings problemen, SCADA-netwerk protocollen, activiteit voor exploit Kit, en meer.
- 20 tot 40 + nieuwe regels worden elke dag vrijgegeven.
- Lage valse positieve classificatie door gebruik te maken van de geavanceerde malware sandbox en een wereld wijde sensor netwerk feedback-lus.

Met id kunt u aanvallen in alle poorten en protocollen voor niet-versleuteld verkeer detecteren. Als HTTPS-verkeer echter moet worden geïnspecteerd, kan Azure Firewall de TLS-inspectie mogelijkheid gebruiken om het verkeer te ontsleutelen en om schadelijke activiteiten beter te detecteren.  

Met de id bypass-lijst kunt u geen verkeer filteren op een van de IP-adressen, bereiken en subnetten die zijn opgegeven in de bypass-lijst. 

## <a name="url-filtering"></a>URL-filtering

URL-filtering breidt de FQDN-filter functie van Azure Firewall uit om een volledige URL te bekijken. Bijvoorbeeld `www.contoso.com/a/c` in plaats van `www.contoso.com` .  

URL-filtering kan worden toegepast op HTTP-en HTTPS-verkeer. Wanneer HTTPS-verkeer wordt geïnspecteerd, kan Azure Firewall Premium Preview de TLS-inspectie mogelijkheid gebruiken om het verkeer te ontsleutelen en de doel-URL uit te pakken om te controleren of toegang is toegestaan. Voor TLS-inspectie is opt-in op toepassings regel niveau vereist. Wanneer deze functie is ingeschakeld, kunt u Url's gebruiken voor filteren met HTTPS. 

## <a name="web-categories"></a>Webcategorieën

Met webcategorieën kunnen beheerders gebruikers toegang tot website categorieën toestaan of weigeren, zoals gokken websites, sociale media websites en anderen. Webcategorieën worden ook opgenomen in Azure Firewall standaard, maar het is beter te verfijnen in Azure Firewall Premium preview. In tegens telling tot de mogelijkheden van webcategorieën in de standaard-SKU die overeenkomt met de categorie op basis van een FQDN, komt de Premium-SKU overeen met de categorie volgens de volledige URL voor HTTP-en HTTPS-verkeer. 

Als Azure Firewall bijvoorbeeld een HTTPS-aanvraag onderschept voor `www.google.com/news` , wordt de volgende categorisatie verwacht: 

- Firewall standaard: alleen het FQDN-deel wordt onderzocht, dus `www.google.com` gecategoriseerd als *Zoek machine*. 

- Firewall Premium: de volledige URL wordt onderzocht en wordt dus `www.google.com/news` gecategoriseerd als *Nieuws*.

De categorieën zijn ingedeeld op basis van Ernst onder **aansprakelijkheid**, **hoge band breedte**, **bedrijfs gebruik**, **productiviteits verlies**, **Algemeen surfen** en niet- **gecategoriseerd**.

### <a name="category-exceptions"></a>Categorie uitzonderingen

U kunt uitzonde ringen maken voor de Web Category-regels. Maak een afzonderlijke regel verzameling voor toestaan of weigeren met een hogere prioriteit in de regel verzamelings groep. U kunt bijvoorbeeld een regel verzameling configureren die `www.linkedin.com` met prioriteit 100 is toegestaan, met een regel verzameling die **sociale netwerken** met prioriteit 200 weigert. Hiermee maakt u de uitzonde ring voor de website van de vooraf gedefinieerde categorie voor **sociale netwerken** .

### <a name="categorization-change"></a>Wijziging categorisatie

U kunt de wijziging van een categorisatie aanvragen als u:

 - denk eraan dat een FQDN of URL zich onder een andere categorie moet bevindt 
 
of 

- een voorgestelde categorie hebben voor een niet-gecategoriseerde FQDN of URL

U kunt een aanvraag indienen bij [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 
## <a name="supported-regions"></a>Ondersteunde regio’s

Azure Firewall Premium Preview wordt ondersteund in de volgende regio's:

- Europa-west (openbaar/Europa)
- VS-Oost (openbaar/Verenigde Staten)
- Australië-oost (openbaar/Australië)
- Zuidoost-Azië (openbaar/Azië en Stille Oceaan)
- UK-zuid (openbaar/Verenigd Konink rijk)
- Europa-noord (openbaar/Europa)
- VS-Oost 2 (openbaar/Verenigde Staten)
- VS Zuid-Centraal (openbaar/Verenigde Staten)
- VS-West 2 (openbaar/Verenigde Staten)
- VS-West (openbaar/Verenigde Staten)
- VS-centraal (openbaar/Verenigde Staten)
- Noord-Centraal VS (openbaar/Verenigde Staten)
- Japan-Oost (openbaar/Japan)
- Azië-oost (openbaar/Azië en Stille Oceaan)
- Canada-centraal (openbaar/Canada)
- Frankrijk-centraal (openbaar/Frank rijk)
- Zuid-Afrika-noord (openbaar/Zuid-Afrika)
- UAE-noord (openbaar/VAE)
- Zwitserland-noord (openbaar/Zwitser land)
- Brazilië-zuid (openbaar/Brazilië)
- Noor wegen-Oost (openbaar/Noor wegen)
- Australië-centraal (openbaar/Australië)
- Australië-centraal 2 (openbaar/Australië)
- Australië-zuidoost (openbaar/Australië)
- Canada-oost (openbaar/Canada)
- Centrale VS-EUAP (openbaar/Canarische (VS))
- Frankrijk-zuid (openbaar/Frank rijk)
- Japan-West (openbaar/Japan)
- Korea-zuid (openbaar/Korea)
- UAE-centraal (openbaar/VAE)
- UK-west (openbaar/Verenigd Konink rijk)
- VS-West-Centraal (openbaar/Verenigde Staten)
- India-West (openbaar/India)


## <a name="known-issues"></a>Bekende problemen

Azure Firewall Premium preview heeft de volgende bekende problemen:

|Probleem  |Beschrijving  |Oplossing  |
|---------|---------|---------|
|TLS-inspectie wordt alleen ondersteund op de HTTPS-standaard poort|TLS-inspectie ondersteunt alleen HTTPS/443. |Geen. Andere poorten worden in GA ondersteund.|
|ESNI-ondersteuning voor FQDN-omzetting in HTTPS|Versleutelde SNI wordt niet ondersteund in HTTPS-handshake.|Alleen de huidige Firefox ondersteunt ESNI via aangepaste configuratie. De voorgestelde tijdelijke oplossing is om deze functie uit te scha kelen.|
|Client certificaten (TLS)|Client certificaten worden gebruikt voor het bouwen van een wederzijdse identiteits vertrouwensrelatie tussen de client en de server. Client certificaten worden gebruikt tijdens een TLS-onderhandeling. Azure firewall onderhandelt een verbinding met de server en heeft geen toegang tot de persoonlijke sleutel van de client certificaten.|Geen|
|QUIC/HTTP3|QUIC is de nieuwe primaire versie van HTTP. Het is een UDP-protocol op basis van 80 (abonnement) en 443 (SSL). De controle van de FQDN/URL/TLS wordt niet ondersteund.|Het door geven van UDP 80/443 als netwerk regels configureren.|
|Beveiligde hub en geforceerde tunneling worden niet ondersteund in Premium|De Premium-SKU van firewalls wordt momenteel niet ondersteund in Secure hub-implementaties en geforceerde tunnel configuraties.|Reparatie gepland voor GA.|
Niet-vertrouwde door de klant ondertekende certificaten|Door de klant ondertekende certificaten worden niet vertrouwd door de firewall zodra ze zijn ontvangen van een webserver op intranet.|Reparatie gepland voor GA.
|Verkeerde bron-en doel-IP-adressen in waarschuwingen voor id met TLS-inspectie.|Wanneer u TLS-inspectie inschakelt en id een nieuwe waarschuwing geeft, is het weer gegeven IP-adres van de bron/bestemming onjuist (het interne IP-adres wordt weer gegeven in plaats van het oorspronkelijke IP-adres).|Reparatie gepland voor GA.|
|Onjuist IP-adres van de bron in waarschuwingen met id voor HTTP (zonder TLS-inspectie).|Wanneer het HTTP-verkeer zonder opmaak wordt gebruikt en id een nieuwe waarschuwing geeft en de bestemming een openbaar IP-adres is, is het IP-adres van de weer gegeven bron onjuist (het interne IP-adres wordt weer gegeven in plaats van het oorspronkelijke IP-adres).|Reparatie gepland voor GA.|
|Doorgifte van certificaten|Nadat een CA-certificaat is toegepast op de firewall, kan het tussen 5-10 minuten duren voordat het certificaat van kracht wordt.|Reparatie gepland voor GA.|
|ID bypass|ID bypass werkt niet voor beëindigde TLS-verkeer, en het IP-adres van de bron en IP-bron groepen worden niet ondersteund.|Reparatie gepland voor GA.|
|TLS 1,3-ondersteuning|TLS 1,3 wordt gedeeltelijk ondersteund. De TLS-tunnel van de client naar de firewall is gebaseerd op TLS 1,2 en van de firewall op de externe webserver is gebaseerd op TLS 1,3.|Updates worden onderzocht.|
|Persoonlijk eind punt voor de sleutel kluis|De sleutel kluis ondersteunt toegang tot het privé-eind punt om de bloot stelling aan het netwerk te beperken. Vertrouwde Azure-Services kunnen deze beperking overs Laan als er een uitzonde ring is geconfigureerd zoals beschreven in de [documentatie over](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)de sleutel kluis. Azure Firewall wordt momenteel niet vermeld als een vertrouwde service en heeft geen toegang tot de Key Vault.|Reparatie gepland voor GA.|


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Firewall Premium-certificaten](premium-certificates.md)
- [Azure Firewall Premium-preview implementeren en configureren](premium-deploy.md)
- [Migreren naar Azure Firewall Premium preview](premium-migrate.md)
