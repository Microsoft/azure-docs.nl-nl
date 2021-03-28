---
title: Architectuur van OPC Vault in Azure | Microsoft Docs
description: Architectuur van OPC Vault-certificaatbeheerservice
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: eb558d967ad657d14158684fba92b13979ea5fe2
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646710"
---
# <a name="opc-vault-architecture"></a>OPC Vault-architectuur

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

Dit artikel bevat een overzicht van de OPC Vault-microservice en de OPC Vault IoT Edge-module.

OPC UA-toepassingen gebruiken toepassingsexemplaarcertificaten om beveiliging op toepassingsniveau te bieden. Er wordt een beveiligde verbinding tot stand gebracht met behulp van asymmetrische cryptografie waarvoor de toepassingscertificaten het openbare en persoonlijke sleutelpaar bieden. De certificaten kunnen zelfondertekend zijn, of ondertekend door een certificeringsinstantie.

Een OPC UA-toepassing heeft een lijst met vertrouwde certificaten voor vertrouwde toepassingen. Deze certificaten kunnen zelfondertekend zijn, of ondertekend door een certificeringsinstantie, of kunnen een basis-CA of een sub-CA zijn. Als een vertrouwd certificaat deel uitmaakt van een grotere certificaatketen, vertrouwt de toepassing alle certificaten die zijn gekoppeld aan het certificaat in de lijst met vertrouwde toepassingen. Dit geldt zolang de volledige certificaatketen kan worden gevalideerd.

Het belangrijkste verschil tussen het vertrouwen van zelfondertekende certificaten en het vertrouwen van een CA-certificaat is de extra inspanning die het kost om een lijst met vertrouwde toepassingen te implementeren en onderhouden. Het kost ook extra inspanning om een bedrijfsspecifieke CA te hosten. 

Als u het vertrouwen voor zelfondertekende certificaten voor meerdere servers met één clienttoepassing wilt distribueren, moet u alle servertoepassingscertificaten installeren in de lijst met vertrouwde clienttoepassingen. Daarnaast moet u het clienttoepassingscertificaat installeren voor alle lijsten met vertrouwde servertoepassingen. Deze administratieve inspanning kan erg tijdrovend zijn en helemaal wanneer u de levensduur van het certificaat moet overwegen en certificaten moet vernieuwen.

Het gebruik van een bedrijfsspecifieke CA kan het beheer van vertrouwde toepassingen met meerdere servers en clients aanzienlijk vereenvoudigen. In dit geval genereert de beheerder voor elke gebruikte client en server een CA-ondertekend certificaat voor het toepassingsexemplaar. Daarnaast wordt het CA-certificaat geïnstalleerd in elke lijst met vertrouwde toepassingen, op alle servers en clients. Met deze aanpak hoeven alleen verlopen certificaten te worden vernieuwd en vervangen voor de betrokken toepassingen.

Met de Azure Industrial IoT OPC UA-certificaatbeheerservice kan u een bedrijfsspecifieke CA voor OPC UA-toepassingen beheren. Deze service is gebaseerd op de OPC Vault-microservice. OPC Vault biedt een microservice om een bedrijfsspecifieke CA in een beveiligde cloud te hosten. Deze oplossing wordt ondersteund door services die zijn beveiligd door Azure Active Directory (Azure AD), Azure Key Vault met Hardware Security-modules (Hsm's), Azure Cosmos DB en optioneel IoT Hub als een toepassingsarchief.

De OPC Vault-microservice is ontworpen ter ondersteuning van op rollen gebaseerde werkstromen, waarbij beveiligingsbeheerders en goedkeurders met ondertekeningsrechten in Azure Key Vault aanvragen goedkeuren of afwijzen.

Voor compatibiliteit met bestaande OPC UA-oplossingen zijn de services voorzien van ondersteuning voor een Edge-module met OPC Vault-microservice. Hiermee implementeert u de **OPC UA Global Discovery-server en de Certificaatbeheer**-interface voor het distribueren van certificaten en lijsten met vertrouwde toepassingen in overeenstemming met deel 12 van de specificatie. 


## <a name="architecture"></a>Architectuur

De architectuur is gebaseerd op de OPC Vault-microservice, met een OPC Vault IoT Edge-module voor het fabrieksnetwerk en een webgebaseerde voorbeeldinterface om de werkstroom te beheren:

![Diagram van OPC Vault-architectuur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC Vault-microservice

De OPC Vault-microservice bestaat uit de volgende interfaces voor het implementeren van de werkstroom voor het distribueren en beheren van een bedrijfsspecifieke CA voor OPC UA-toepassingen.

### <a name="application"></a>Toepassing 
- Een OPC UA-toepassing kan een server of een client zijn, of beide. OPC Vault fungeert in dit geval als een toepassingsregistratie-instantie. 
- Naast de basisbewerkingen voor het registreren, bijwerken en ongedaan maken van de registratie van toepassingen, zijn er ook interfaces om met behulp van zoekexpressies toepassingen te vinden of er query’s op uit te voeren. 
- De certificaataanvragen moeten verwijzen naar een geldige toepassing om een aanvraag te verwerken en een ondertekend certificaat uit te geven met alle OPC UA-specifieke extensies. 
- De toepassingsservice wordt ondersteund door een database in Azure Cosmos DB.

### <a name="certificate-group"></a>Certificaatgroep
- Een certificaatgroep is een entiteit waarin een basis-CA of een sub-CA certificaat wordt opgeslagen, met inbegrip van de persoonlijke sleutel voor het ondertekenen van certificaten. 
- De lengte van de RSA-sleutel, de SHA-2-hash-lengte en de levensduur kunnen worden geconfigureerd voor zowel de verlenende CA als de ondertekende toepassingscertificaten. 
- De CA-certificaten worden opgeslagen in Azure Key Vault, ondersteund met FIPS 140-2 level 2 HSM. De persoonlijke sleutel verlaat nooit de beveiligde opslag, omdat ondertekening wordt uitgevoerd door een Key Vault-bewerking die wordt beveiligd door Azure AD. 
- U kunt de CA-certificaten na verloop van tijd vernieuwen en ze veilig bewaren met Key Vault-geschiedenis. 
- De intrekkingslijst voor elk CA-certificaat wordt beveiligd opgeslagen in Key Vault. Wanneer een toepassing niet is geregistreerd, wordt het toepassingscertificaat ook ingetrokken in de certificaatintrekkingslijst door een beheerder.
- U kunt afzonderlijke certificaten intrekken, of meerdere certificaten tegelijk.

### <a name="certificate-request"></a>Certificaataanvraag
Een certificaataanvraag implementeert de werkstroom voor het genereren van een nieuw sleutelpaar of een ondertekend certificaat met behulp van een aanvraag voor certificaatondertekening (CSR) voor een OPC UA-toepassing. 
- De aanvraag wordt opgeslagen in een database met bijbehorende informatie, zoals het onderwerp of een CSR, en een verwijzing naar de OPC UA-toepassing. 
- De bedrijfslogica in de service valideert de aanvraag met de informatie die is opgeslagen in de toepassingsdatabase. De URI van de toepassing in de database moet bijvoorbeeld overeenkomen met de toepassings-URI in de CSR.
- Een beveiligingsbeheerder met ondertekeningsrechten (de rol Goedkeurder) keurt de aanvraag goed of weigert deze. Als de aanvraag is goedgekeurd, wordt een nieuw sleutelpaar of een ondertekend certificaat (of beide) gegenereerd. De nieuwe persoonlijke sleutel wordt beveiligd opgeslagen in Key Vault en het nieuwe ondertekende openbare certificaat wordt opgeslagen in de database van de certificaataanvraag.
- De aanvrager kan de aanvraagstatus controleren totdat deze is goedgekeurd of ingetrokken. Als de aanvraag is goedgekeurd, kunnen de persoonlijke sleutel en het certificaat worden gedownload en geïnstalleerd in het certificaatarchief van de OPC UA-toepassing.
- De aanvrager kan nu de aanvraag accepteren om overbodige gegevens uit de aanvraagdatabase te verwijderen. 

Gedurende de levensduur van een ondertekend certificaat kan het voorkomen dat een toepassing wordt verwijderd of dat een sleutel wordt gecompromitteerd. In een dergelijk geval kan een CA-beheerder:
- Een toepassing verwijderen, waarmee ook alle in behandeling zijnde en goedgekeurde certificaataanvragen van de app worden verwijderd. 
- Eén certificaataanvraag verwijderen, indien maar één sleutel moet worden vernieuwd of is gecompromitteerd.

Goedgekeurde en geaccepteerde certificaat die zijn gecompromitteerd worden gemarkeerd als verwijderd.

Een manager kan de CA-CRL van de certificaatverlener regelmatig vernieuwen. Op het moment van vernieuwing worden alle verwijderde certificaataanvragen ingetrokken en worden de serienummers van het certificaat toegevoegd aan de CRL-intrekkingslijst. Ingetrokken certificaataanvragen worden gemarkeerd als ingetrokken. In dringende gevallen kunnen ook afzonderlijke certificaataanvragen worden ingetrokken.

Ten slotte zijn de bijgewerkte CRL's beschikbaar voor distributie naar de deelnemende OPC UA-clients en -servers.

## <a name="opc-vault-iot-edge-module"></a>OPC Vault IoT Edge-module
Ter ondersteuning van de algemene detectieserver van het netwerk, kunt u de OPC Vault-module op de Edge-server implementeren. Voer deze uit als een lokale .NET core-toepassing of start deze in een docker-container. Houd er rekening mee dat vanwege een gebrek aan ondersteuning voor Auth2-verificatie in de huidige OPC UA .NET Standard-stack, de functionaliteit van de OPC Vault Edge-module beperkt is tot de rol van Lezer. Een gebruiker kan niet van de Edge-module naar de microservice worden geïmiteerd met behulp van de standaardinterface OPC UA GDS.

## <a name="next-steps"></a>Volgende stappen

Nu u meer weet over de architectuur van OPC Vault, kunt u het volgende doen:

> [!div class="nextstepaction"]
> [OPC Vault bouwen en implementeren](howto-opc-vault-deploy.md)
