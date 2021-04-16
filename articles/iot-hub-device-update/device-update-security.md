---
title: Beveiliging voor apparaatupdates voor Azure IoT Hub | Microsoft Docs
description: Begrijpen hoe Apparaatupdate voor IoT Hub ervoor zorgt dat apparaten veilig worden bijgewerkt.
author: lichris
ms.author: lichris
ms.date: 4/15/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: b10049e03e26cfe8da2bd57cc9f69dd933af706b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567295"
---
# <a name="device-update-security-model"></a>Beveiligingsmodel voor apparaatupdates

Apparaatupdate voor IoT Hub biedt een veilige methode voor het implementeren van updates voor apparaatfirmware, afbeeldingen en toepassingen op uw IoT-apparaten. De werkstroom biedt een end-to-end beveiligd kanaal met een volledig controleketenmodel dat een apparaat kan gebruiken om te bewijzen dat een update vertrouwd, ongewijzigd en opzettelijk is.

Elke stap in de werkstroom apparaatupdate wordt beveiligd via verschillende beveiligingsfuncties en -processen om ervoor te zorgen dat elke stap in de pijplijn een beveiligde hand-off naar de volgende uitvoert. De Apparaatupdate-client identificeert en beheert eventuele onleesbaar updateaanvragen op de juiste wijze. De client controleert ook elke download om ervoor te zorgen dat de inhoud vertrouwd, ongewijzigd en opzettelijk is.

## <a name="for-solution-operators"></a>Voor oplossingsoperators

Als oplossingsoperators updates importeren in hun exemplaar van Apparaatupdate, uploadt en controleert de service de binaire bestanden van de update om ervoor te zorgen dat ze niet zijn gewijzigd of verwisseld door een kwaadwillende gebruiker. Zodra dit is geverifieerd, genereert [](./update-manifest.md) de Device Update-service een intern updatemanifest met bestandshashes van het importmanifest en andere metagegevens. Dit updatemanifest wordt vervolgens ondertekend door de Device Update-service.

Zodra de binaire bestanden en de bijbehorende metagegevens van klanten in rust zijn opgenomen in de service en zijn opgeslagen in Azure, worden ze automatisch versleuteld door de Azure Storage-service. De Device Update-service biedt niet automatisch extra versleuteling, maar biedt ontwikkelaars wel de mogelijkheid om inhoud zelf te versleutelen voordat de inhoud de Device Update-service bereikt.

Wanneer de oplossingsoperator een aanvraag indient om een apparaat bij te werken, wordt een ondertekend bericht via het beveiligde IoT Hub naar het apparaat verzonden. De handtekening van de aanvraag wordt door de Device Update-agent van het apparaat gevalideerd als authentic. 

Alle resulterende binaire download wordt beveiligd door de handtekening voor het updatemanifest te valideren. Het updatemanifest bevat de binaire bestandshashes, dus zodra het manifest wordt vertrouwd, vertrouwt de Apparaatupdate-agent de hashes en komt deze overeen met de binaire bestanden. Zodra het binaire bestand van de update is gedownload en geverifieerd, wordt het veilig overgedragen aan het installatieprogramma op het apparaat.

## <a name="for-device-builders"></a>Voor apparaatbouwers

Het beveiligingsmodel maakt gebruik van onbewerkte asymmetrische sleutels en onbewerkte handtekeningen om ervoor te zorgen dat de Device Update-service omlaag wordt geschaald naar eenvoudige apparaten met lage prestaties. Ze gebruiken JSON-indelingen zoals JSON-webtokens & JSON-websleutels.

### <a name="securing-update-content-via-the-update-manifest"></a>Update-inhoud beveiligen via het updatemanifest

Het updatemanifest wordt gevalideerd met behulp van twee handtekeningen. De handtekeningen worden gemaakt met behulp van een structuur die bestaat uit *ondertekeningssleutels* en *hoofdsleutels.*

De Agent voor apparaatupdates heeft ingesloten openbare sleutels die worden gebruikt voor alle apparaten die compatibel zijn met Apparaatupdates. Dit zijn de *hoofdsleutels.* De bijbehorende persoonlijke sleutels worden beheerd door Microsoft.

Microsoft genereert ook een openbaar/persoonlijk sleutelpaar dat niet is opgenomen in de Agent voor apparaatupdates of is opgeslagen op het apparaat. Dit is de *ondertekeningssleutel.*

Wanneer een update wordt geïmporteerd in Apparaatupdate voor IoT Hub en het updatemanifest wordt gegenereerd door de service, ondertekent de service het manifest met behulp van de ondertekeningssleutel en bevat de ondertekeningssleutel zelf, die is ondertekend door een basissleutel. Wanneer het updatemanifest naar het apparaat wordt verzonden, ontvangt de Agent voor apparaatupdates de volgende handtekeninggegevens:

1. De handtekeningwaarde zelf.
2. Het algoritme dat wordt gebruikt voor het genereren #1.
3. De informatie over de openbare sleutel van de ondertekeningssleutel die wordt gebruikt voor het genereren van #1.
4. De handtekening van de openbare ondertekeningssleutel in #3.
5. De id van de openbare sleutel van de hoofdsleutel die wordt gebruikt voor het genereren van #3.
6. Het algoritme dat wordt gebruikt voor het genereren #4.

De Agent voor apparaatupdates gebruikt de hierboven gedefinieerde informatie om te controleren of de handtekening van de openbare ondertekeningssleutel is ondertekend door de hoofdsleutel. De Agent voor apparaatupdates controleert vervolgens of de handtekening van het updatemanifest is ondertekend door de ondertekeningssleutel. Als alle handtekeningen juist zijn, wordt het updatemanifest vertrouwd door de Agent voor apparaatupdates. Omdat het updatemanifest de bestandshashes bevat die overeenkomen met de updatebestanden zelf, kunnen de updatebestanden ook worden vertrouwd als de hashes overeenkomen.

Met root- en ondertekeningssleutels kan Microsoft de ondertekeningssleutel, een beveiligingssleutel, periodiek best practice.

### <a name="json-web-signature-jws"></a>JSON Web Signature (JWS)

De wordt gebruikt om ervoor te zorgen dat er niet met de informatie `updateManifestSignature` in `updateManifest` de is geknoeid. De `updateManifestSignature` wordt geproduceerd met behulp van een JSON Web Signature met JSON-websleutels, waardoor bronverificatie mogelijk is. De handtekening is een met Base64Url gecodeerde tekenreeks met drie secties die zijn gescheiden door ''.  Raadpleeg de [helpermethoden jws_util.h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) voor het parseren en verifiëren van JSON-sleutels en -tokens.

JSON Web Signature is een veelgebruikte [voorgestelde IETF-standaard](https://tools.ietf.org/html/rfc7515) voor het ondertekenen van inhoud met behulp van gegevensstructuren op basis van JSON. Het is een manier om de integriteit van gegevens te waarborgen door de handtekening van de gegevens te controleren. Meer informatie vindt u in JSON Web Signature (JWS) [RFC 7515.](https://www.rfc-editor.org/info/rfc7515)

### <a name="json-web-token"></a>JSON Web Token

JSON-webtokens zijn een open, [industriestandaardmethode](https://tools.ietf.org/html/rfc7519) voor het veilig vertegenwoordigen van claims tussen twee partijen.

### <a name="root-keys"></a>Hoofdsleutels

Elk apparaat met apparaatupdates bevat een set hoofdsleutels. Deze sleutels zijn de basis van vertrouwen voor alle handtekeningen van Device Update. Elke handtekening moet worden geketend via een van deze hoofdsleutels om als legitiem te worden beschouwd.

De set basissleutels wordt na een periode gewijzigd, omdat het goed is om de ondertekeningssleutels periodiek te roteren voor beveiligingsdoeleinden. Als gevolg hiervan moet de software van de Agent voor apparaatupdates zichzelf bijwerken met de meest recente set hoofdsleutels. 

### <a name="signatures"></a>Handtekeningen

Alle handtekeningen worden ondergebracht door een (openbare) ondertekeningssleutel die is ondertekend door een van de hoofdsleutels. De handtekening identificeert welke hoofdsleutel is gebruikt om de ondertekeningssleutel te ondertekenen. 

Een Agent voor apparaatupdates moet handtekeningen valideren door eerst te controleren of de handtekening van de (openbare) sleutel juist, geldig en ondertekend is door een van de goedgekeurde basissleutels. Zodra de ondertekeningssleutel is gevalideerd, kan de handtekening zelf worden gevalideerd met behulp van de nu vertrouwde openbare ondertekeningssleutel.

Ondertekeningssleutels worden veel sneller geroteerd dan basissleutels, dus verwacht berichten die zijn ondertekend door verschillende ondertekeningssleutels. 

Het intrekken van een ondertekeningssleutel wordt beheerd door de Device Update-service, zodat gebruikers niet mogen proberen ondertekeningssleutels in de cache op te halen. Gebruik altijd de ondertekeningssleutel bij een handtekening.

### <a name="receiving-updates"></a>Updates ontvangen

Updateaanvragen die worden ontvangen door een Agent voor apparaatupdates, bevatten een ondertekend document voor updatemanifest (UM). De agent moet controleren of de handtekening van de UM juist en intact is. Dit wordt gedaan door te valideren dat de ondertekeningssleutel van de UM-handtekening is ondertekend door een juiste basissleutel. Zodra dit is gebeurd, valideert de agent de UM-handtekening op basis van de ondertekeningssleutel.

Zodra de UM-handtekening is gevalideerd, vertrouwt de Apparaatupdate-agent deze mogelijk als 'bron van waarheid'. Alle verdere beveiligingsvertrouwensrelatie is afkomstig van deze bron. 

De UM bevat URL's en bestandshashes met inhoud die moeten worden gedownload en geïnstalleerd. Zodra de agent een binaire update heeft gedownload, moet deze de update valideren op de bestandshash die in de UM is gevonden. Dit biedt een transitief vertrouwensmodel voor downloadvalidatie. Het zorgt er niet alleen voor dat de inhoud intact is (niet gewijzigd), maar bevestigt ook dat wat is gedownload inderdaad de bedoeling was om te worden gedownload. 

### <a name="securing-the-device"></a>Het apparaat beveiligen

Het is belangrijk om ervoor te zorgen dat apparaatupdate-gerelateerde beveiligingsactiva goed zijn beveiligd en beveiligd op uw apparaat. Assets zoals hoofdsleutels moeten worden beveiligd tegen wijzigingen. Er zijn verschillende manieren om dit te doen, zoals het gebruik van beveiligingsapparaten (TPM, SGX, HSM, andere beveiligingsapparaten) of zelfs hard-coding in de Agent voor apparaatupdates. Voor de laatste is vereist dat de code van de Device Update-agent digitaal is ondertekend en dat de ondersteuning voor code-integriteit van het systeem is ingeschakeld om te beschermen tegen schadelijke wijzigingen van de agentcode.

Aanvullende beveiligingsmaatregelen kunnen worden gerechtvaardigd, zoals ervoor zorgen dat de hand-off van onderdeel naar onderdeel op een veilige manier wordt uitgevoerd. Bijvoorbeeld het registreren van een specifiek geïsoleerd account om de verschillende onderdelen uit te voeren. En het beperken van netwerkgebaseerde communicatie (bijvoorbeeld REST API aanroepen) tot alleen localhost.

**[Volgende stap: meer informatie over hoe Apparaatupdate gebruikmaakt van Azure RBAC](.\device-update-control-access.md)**